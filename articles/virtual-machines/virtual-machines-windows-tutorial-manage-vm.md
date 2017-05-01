---
title: "Gérer des machines virtuelles Windows à l’aide d’Azure PowerShell | Microsoft Docs"
description: "Didacticiel - Gérer des machines virtuelles Windows avec Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 8dde92d3bee451ee701508cec98b3581384fd16c
ms.lasthandoff: 04/06/2017

---

# <a name="manage-windows-virtual-machines-with-azure-powershell"></a>Gérer des machines virtuelles Windows avec Azure PowerShell

Dans ce didacticiel, vous créez une machine virtuelle et effectuez des tâches de gestion courantes telles que l’ajout d’un disque, l’automatisation de l’installation de logiciels, la gestion des règles de pare-feu et la création d’une capture instantanée de machine virtuelle.

Pour suivre ce didacticiel, assurez-vous que vous avez installé le dernier module [d’Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

## <a name="step-1--log-in-to-azure"></a>Étape 1 : Connexion à Azure

Pour commencer, connectez-vous à votre abonnement Azure avec la commande Login-AzureRmAccount et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

## <a name="step-2---create-resource-group"></a>Étape 2 : Création d’un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Un groupe de ressources doit être créé avant les machines virtuelles.

Créez un groupe de ressources avec [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v2.0.3/new-azurermresourcegroup).  Dans cet exemple, un groupe de ressources nommé `myResourceGroup` est créé dans la région `westeurope` :

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-3---create-virtual-machine"></a>Étape 3 : Création d’une machine virtuelle

Une machine virtuelle doit être connectée à un réseau virtuel. Vous communiquez avec la machine virtuelle à l’aide d’une adresse IP publique via une carte réseau (NIC).

### <a name="create-virtual-network"></a>Création d’un réseau virtuel

Créez un sous-réseau avec [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermvirtualnetworksubnetconfig) :

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24
```

Créez un réseau virtuel avec [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermvirtualnetwork) :

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>Création d’une adresse IP publique

Créez une adresse IP publique avec [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermpublicipaddress) :

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>Création de la carte réseau

Créez une carte réseau avec [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermnetworkinterface) :

```powershell
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>Création d’un groupe de sécurité réseau

Un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) (NSG) Azure contrôle le trafic entrant et sortant pour une ou plusieurs machines virtuelles. Les règles de groupe de sécurité réseau autorisent ou refusent le trafic réseau sur un port ou une plage de ports spécifique. Ces règles peuvent également inclure un préfixe d’adresse source afin que seul le trafic provenant d’une source prédéfinie puisse communiquer avec une machine virtuelle. Pour accéder au serveur web IIS que vous installez, vous devez ajouter une règle de groupe de sécurité réseau entrante.

Pour créer une règle de groupe de sécurité réseau entrante, utilisez [Add-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/add-azurermnetworksecurityruleconfig). L’exemple suivant crée une règle de groupe de sécurité réseau nommée `myNSGRule` qui ouvre le port `80` pour la machine virtuelle :

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Créez le groupe de sécurité réseau `myNSGRule` avec [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermnetworksecuritygroup) :

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

Ajoutez le groupe de sécurité réseau au sous-groupe dans le réseau virtuel avec [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/set-azurermvirtualnetworksubnetconfig) :

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -Name mySubnet `
  -VirtualNetwork $vnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

Mettez à jour le réseau virtuel avec [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/set-azurermvirtualnetwork) :

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Create virtual machine

Lorsque vous créez une machine virtuelle, plusieurs options sont disponibles, comme l’image de système d’exploitation, les informations d’identification d’administration ou le dimensionnement des disques. Dans cet exemple, une machine virtuelle est créée avec le nom `myVM` exécutant la dernière version de Windows Server 2016 Datacenter.

Définissez le nom d’utilisateur et le mot de passe pour le compte d’administrateur sur la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```powershell
$cred = Get-Credential
```

Créez la configuration initiale de la machine virtuelle avec [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig) :

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Ajoutez les informations relatives au système d’exploitation à la configuration de la machine virtuelle avec [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem) :

```powershell
$vm = Set-AzureRmVMOperatingSystem -VM $vm `
  -Windows `
  -ComputerName myVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
```

Ajoutez les informations relatives à l’image à la configuration de la machine virtuelle avec [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage) :

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest
```

Ajoutez les paramètres du disque du système d’exploitation à la configuration de la machine virtuelle avec [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk) :

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm `
  -Name myOsDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
```

Ajoutez la carte réseau que vous avez créée précédemment à la configuration de la machine virtuelle avec [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) :

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Créez la machine virtuelle avec [New-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvm) :

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
```

## <a name="step-4--add-data-disk"></a>Étape 4 : Ajout du disque de données

Par défaut, les machines virtuelles Azure sont créés avec un seul disque de système d’exploitation. Il est possible d’ajouter des disques supplémentaires pour la configuration de stockage à plusieurs disques, l’installation de l’application et les données.

### <a name="create-disk"></a>Créer le disque

Créez la configuration initiale du disque de données avec [New-AzureRmDiskConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdiskconfig). L’exemple suivant crée un disque nommé `myDataDisk` avec une taille de 50 Go :

```powershell
$diskConfig = New-AzureRmDiskConfig `
  -Location westeurope `
  -CreateOption Empty `
  -DiskSizeGB 50
```

Créez le disque de données avec [New-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdisk) :

```powershell
$dataDisk = New-AzureRmDisk `
  -ResourceGroupName myResourceGroup `
  -DiskName myDataDisk `
  -Disk $diskConfig
```

Obtenez la machine virtuelle que vous souhaitez ajouter au disque de données avec [Get-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermvm) :

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Ajoutez le disque de données à la configuration de la machine virtuelle avec [Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmdatadisk) :

```powershell
$vm = Add-AzureRmVMDataDisk `
  -VM $vm `
  -Name myDataDisk `
  -CreateOption Attach `
  -ManagedDiskId $dataDisk.Id `
  -Lun 1
```

Mettez à jour la machine virtuelle avec [Update-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/update-azurermvm) :

```powershell
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

## <a name="step-5--automate-configuration"></a>Étape 5 : Automatisation de la configuration

Les extensions de machine virtuelle Azure sont utilisées pour automatiser les tâches de configuration de machine virtuelle telles que l’installation d’applications et la configuration du système d’exploitation. [L’extension de script personnalisé pour Windows](./virtual-machines-windows-extensions-customscript.md) permet d’exécuter n’importe quel script PowerShell sur la machine virtuelle. Le script peut être stocké dans le stockage Azure, dans tout point de terminaison HTTP accessible ou il peut être intégré dans la configuration d’extension de script personnalisé. Dans ce didacticiel, deux actions sont automatisées :

- Installation d’IIS
- Formatage d’un disque de données sur la machine virtuelle

Étant donné que l’extension s’exécute au moment du déploiement de la machine virtuelle, le fichier **install-iis-format-disk.ps1** doit être défini avant de créer la machine virtuelle. Pour ce didacticiel, le fichier se trouve dans le référentiel de scripts Azure PowerShell. Le fichier contient la commande `Add-WindowsFeature Web-Server` et la commande `Get-Disk | Where partitionstyle -eq 'raw' | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false**`.

Ajoutez l’extension avec [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmextension) :

```powershell
Set-AzureRmVMExtension -Name myScript `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -VMName myVM `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.4 `
  -SettingString '{"fileUris":["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/virtual-machine/install-iis-format-disk/install-iis-format-disk.ps1?token=ABlGkLjzVTdZGoCRbu1pCXjvSDRMHnUlks5Y5nbZwA%3D%3D"], "commandToExecute":"powershell.exe -ExecutionPolicy Unrestricted -File install-iis-format-disk.ps1" }'
```

Obtenez l’adresse IP publique de la machine virtuelle avec [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/get-azurermpublicipaddress) :

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIPAddress | select IpAddress
```

Maintenant, accédez à l’adresse IP publique de la machine virtuelle. Avec la règle de groupe de sécurité réseau en place, le site web IIS par défaut s’affiche.


## <a name="step-6--snapshot-virtual-machine"></a>Étape 6 – Capture d’un instantané de la machine virtuelle

La prise d’une capture instantanée d’une machine virtuelle crée une copie ponctuelle, en lecture seule, du disque du système d’exploitation des machines virtuelles. Avec une capture instantanée, la machine virtuelle peut être restaurée à un état spécifique ou la capture instantanée peut être utilisée pour créer une nouvelle machine virtuelle avec un état identique.

### <a name="create-snapshot"></a>Création d’un instantané

Avant de créer une capture instantanée de disque de machine virtuelle, l’ID du disque du système d’exploitation de la machine virtuelle est nécessaire.

Obtenez le disque du système d’exploitation avec [Get-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermdisk) :

```powershell
$vmOSDisk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name myOSDisk
```

Créez la configuration de la capture instantanée avec New-AzureRmSnapshotConfig :

```powershell
$snapshotConfig = New-AzureRmSnapshotConfig -Location westeurope -CreateOption Copy -SourceResourceId $vmOSDisk.id
```

Créez la capture instantanée avec New-AzureRmSnapshot :

```powershell
$snapshot = New-AzureRmSnapshot -ResourceGroupName myResourceGroup -SnapshotName mySnapshot -Snapshot $snapshotConfig
```

### <a name="create-disk-from-snapshot"></a>Création d’un disque à partir d’une capture instantanée

Cet instantané peut ensuite être converti en disque qui peut être utilisé pour recréer la machine virtuelle.

Créez la configuration du disque avec [New-AzureRmDiskConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdiskconfig) :

```powershell
$diskConfig = New-AzureRmDiskConfig -Location westeurope -CreateOption Copy -SourceResourceId $snapshot.id
```

Créez le disque avec [New-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdisk) :

```powershell
$disk = New-AzureRmDisk -ResourceGroupName myResourceGroup -DiskName myOSDiskFromSnapshot -Disk $diskConfig
```

### <a name="restore-virtual-machine-from-snapshot"></a>Restauration de la machine virtuelle à partir de l’instantané

Pour illustrer la récupération de machine virtuelle, supprimez la machine virtuelle existante.

```powershell
Remove-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Créez une nouvelle machine virtuelle à l’aide du disque d’instantané. Dans cet exemple, l’interface de réseau existante est spécifiée. Cette configuration applique toutes les règles de groupe de sécurité réseau précédemment créées à la nouvelle machine virtuelle.

Créez la configuration initiale de la machine virtuelle avec [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig) :

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Ajoutez le disque du système d’exploitation avec [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk) :

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -CreateOption Attach -ManagedDiskId $disk.Id -Windows
```

Ajoutez la carte réseau avec [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) :

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Créez la machine virtuelle avec [New-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvm) :

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -Location westeurope
```

Entrez l’adresse IP publique de la machine virtuelle dans la barre d’adresses du navigateur Internet. IIS doit s’afficher comme en cours d’exécution sur la machine virtuelle restaurée.

## <a name="step-7--management-tasks"></a>Étape 7 : Tâches de gestion

Pendant le cycle de vie d’une machine virtuelle, vous souhaiterez exécuter des tâches de gestion telles que le démarrage, l’arrêt ou la suppression d’une machine virtuelle. En outre, vous souhaiterez peut-être créer des scripts pour automatiser les tâches répétitives ou complexes. À l’aide d’Azure PowerShell, de nombreuses tâches courantes de gestion peuvent être exécutées à partir de la ligne de commande ou dans des scripts.

### <a name="stop-virtual-machine"></a>Arrêt d’une machine virtuelle

Arrêtez et libérez une machine virtuelle avec [Stop-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/stop-azurermvm) :

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Si vous souhaitez conserver la machine virtuelle dans un état approvisionné, utilisez le paramètre - StayProvisioned.

### <a name="resize-virtual-machine"></a>Redimensionner la machine virtuelle

Pour redimensionner une machine virtuelle arrêtée et libérée, vous devez connaître le nom des tailles disponibles dans la région Azure choisie. Vous trouverez ces tailles avec [Get-AzureRmVMSize](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermvmsize) :

```powershell
Get-AzureRmVMSize -Location westeurope
```

Obtenez la machine virtuelle que vous souhaitez redimensionner avec [Get-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermdisk) :

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Définissez la nouvelle taille de la machine virtuelle en définissant la propriété vmSize sur la taille que vous avez sélectionnée.

```powershell
$vm.HardwareProfile.vmSize = "Standard_F4s"
```

Mettez à jour la machine virtuelle avec [Update-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/update-azurermvm) :

```powershell
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

### <a name="start-virtual-machine"></a>Démarrage d’une machine virtuelle

```powershell
Start-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

### <a name="delete-resource-group"></a>Supprimer un groupe de ressources

La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Exemples – [Exemples de scripts PowerShell pour machine virtuelle Azure](./virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

