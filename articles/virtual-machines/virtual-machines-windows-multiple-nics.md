<properties
   pageTitle="Configurer plusieurs cartes réseau sur une machine virtuelle Windows | azure.microsoft.com/ Azure"
   description="Découvrez comment créer une machine virtuelle dotée de plusieurs cartes réseau à l’aide d’Azure PowerShell ou de modèles Resource Manager."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/04/2016"
   ms.author="iainfou"/>

# Création d’une machine virtuelle avec plusieurs cartes d’interface réseau (NIC)
Vous pouvez créer une machine virtuelle dans Azure, à laquelle sont attachées plusieurs interfaces réseau virtuelles (NIC). Un scénario courant consisterait à avoir des sous-réseaux différents pour les connectivités frontale et principale, ou un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article fournit des commandes rapides pour créer une machine virtuelle avec plusieurs cartes d’interface réseau. Pour plus d’informations, notamment sur la création de plusieurs cartes réseau dans vos propres scripts PowerShell, consultez la page consacrée au [déploiement de machines virtuelles avec plusieurs cartes d’interface réseau](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Comme le nombre de cartes réseau prises en charge varie suivant la [taille des machines virtuelles](virtual-machines-windows-sizes.md), pensez à dimensionner la vôtre en conséquence.

>[AZURE.WARNING] Vous devez attacher plusieurs cartes réseau quand vous créez une machine virtuelle ; vous ne pouvez pas ajouter de cartes réseau à une machine virtuelle existante. Vous pouvez [créer une machine virtuelle basée sur les disques virtuels d’origine](virtual-machines-windows-specialized-image.md) et créer plusieurs cartes réseau quand vous déployez la machine virtuelle.

## Créer les ressources de base
Vérifiez que la [dernière version d’Azure PowerShell est installée et configurée](../powershell-install-configure.md).

Créez d’abord un groupe de ressources :

```powershell
New-AzureRmResourceGroup -Name TestRG -Location WestUS
```

Créez un compte de stockage qui contiendra vos machines virtuelles :

```powershell
$storageAcc = New-AzureRmStorageAccount -Name teststorage `
    -ResourceGroupName TestRG -Kind Storage -SkuName Premium_LRS -Location WestUS
```

## Créer un réseau virtuel et des sous-réseaux
Définissez deux sous-réseaux de réseau virtuel : l’un pour le trafic frontal, l’autre pour le trafic principal. Créez votre réseau virtuel avec ces sous-réseaux :

```powershell
$frontEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" `
    -AddressPrefix 192.168.1.0/24
$backEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "BackEnd" `
    -AddressPrefix 192.168.2.0/24


$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location WestUS `
    -Subnet $frontEndSubnet,$backEndSubnet
```


## Créer plusieurs cartes réseau
Créez deux cartes réseau en attachant l’une au sous-réseau frontal et l’autre au sous-réseau principal :

```powershell
$frontEnd = $vnet.Subnets|?{$_.Name -eq 'FrontEnd'}
$NIC1 = New-AzureRmNetworkInterface -Name NIC1 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $FrontEnd.Id

$backEnd = $vnet.Subnets|?{$_.Name -eq 'BackEnd'}
$NIC2 = New-AzureRmNetworkInterface -Name NIC2 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $BackEnd.Id
```

En général, vous devez également créer un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) ou un [équilibreur de charge](../load-balancer/load-balancer-overview.md) pour faciliter la gestion et la répartition du trafic entre vos machines virtuelles. L’article [plus détaillé consacré aux machines virtuelles dotées de plusieurs cartes réseau](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) explique comment créer un groupe de sécurité réseau et affecter des cartes réseau.


## Créer la machine virtuelle
Maintenant, commencez à élaborer la configuration de votre machine virtuelle. La taille d’une machine virtuelle détermine le nombre maximal de cartes réseau qu’elle peut accueillir. En savoir plus sur les [tailles des machines virtuelles Windows](virtual-machines-windows-sizes.md). L’exemple suivant utilise une taille de machine virtuelle qui prend en charge jusqu’à deux cartes réseau (`Standard_DS2_v2`) :

```powershell
$cred = Get-Credential

$vmConfig = New-AzureRmVMConfig -VMName TestVM -VMSize "Standard_DS2_v2"

$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName TestVM `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName azure.microsoft.com/WindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
```

Attachez les deux cartes réseau que vous avez créées :

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC2.Id
```

Configurez le stockage et le disque virtuel de votre nouvelle machine virtuelle :

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption fromImage
```

Enfin, créez une machine virtuelle :

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName TestRG -Location WestUS
```

## Création de plusieurs cartes réseau à l’aide de modèles Resource Manager
Les modèles Azure Resource Manager utilisent des fichiers JSON déclaratifs pour définir votre environnement. Vous pouvez consulter une [vue d’ensemble d’Azure Resource Manager](../resource-group-overview.md). Grâce aux modèles Resource Manager, vous pouvez créer plusieurs instances d’une ressource pendant le déploiement, à l’image de la création de plusieurs cartes réseau. Utilisez *copy* pour spécifier le nombre d’instances à créer :

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

En savoir plus sur la [création de plusieurs instances à l’aide de *copy*](../resource-group-create-multiple.md).

Vous pouvez également utiliser `copyIndex()` pour ajouter ensuite un numéro à un nom de ressource permettant de créer `NIC1`, `NIC2`, etc. Voici un exemple d’ajout de la valeur d’index :

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

Vous pouvez consulter un exemple complet de la [création de plusieurs cartes réseau à l’aide de modèles Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Étapes suivantes
Veillez à consulter les [tailles des machines virtuelles Windows](virtual-machines-windows-sizes.md) si vous créez une machine virtuelle avec plusieurs cartes réseau. Faites attention au nombre maximal de cartes réseau pris en charge par chaque taille de machine virtuelle.

N’oubliez pas que vous ne pouvez pas ajouter de cartes réseau à une machine virtuelle existante. Vous devez créer toutes les cartes réseau quand vous déployez la machine virtuelle. Quand vous planifiez vos déploiements, vérifiez que vous disposez de toute la connectivité réseau nécessaire dès le départ.

<!---HONumber=AcomDC_0817_2016-->