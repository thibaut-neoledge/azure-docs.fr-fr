<properties
	pageTitle="Création de jeux de mise à l’échelle de machine virtuelle | Microsoft Azure"
	description="Créer un jeu de mise à l’échelle de machine virtuelle à l’aide de PowerShell"
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Créer un jeu de mise à l’échelle de machine virtuelle Windows à l’aide d’Azure PowerShell

Ces étapes utilisent une méthode de cases à remplir pour créer un jeu de mise à l’échelle de machine virtuelle Azure. Vous trouverez tout au long de cet article les variables pour lesquelles vous devez fournir des valeurs. Remplacez tous les éléments entre guillemets par les valeurs appropriées à votre abonnement et à votre application.

## Étape 1 : installer Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Étape 2 : Définir votre abonnement

1. Démarrez une invite de commandes PowerShell.

2. Connectez-vous à votre compte :

        Login-AzureRmAccount

3. Récupérez votre abonnement :

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

4. Définissez comme actuel l'abonnement que vous souhaitez utiliser :

        $subscr = "subscription name"
        Select-AzureSubscription -SubscriptionName $subscr –Current


## Étape 2 : Créer des ressources

Créez les ressources nécessaires à votre nouveau jeu de mise à l'échelle de machine virtuelle.

### Groupe de ressources

Un jeu de mise à l'échelle de machine virtuelle doit figurer dans un groupe de ressources.

1.  Exécutez cette commande pour obtenir la liste des emplacements disponibles et des services pris en charge :

        Get-AzureLocation | Sort Name | Select Name, AvailableServices

    Le résultat suivant devrait s'afficher :

        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}

    Choisissez l’emplacement qui vous convient le mieux, puis remplacez le texte entre guillemets par le nom de cet emplacement :

        $locName = "location name from the list, such as Central US"

4. Remplacez le texte entre guillemets par le nom que vous souhaitez utiliser pour le nouveau groupe de ressources puis créez ce groupe à l'emplacement que vous avez précédemment défini :

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Le résultat suivant devrait s'afficher :

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### Compte de stockage

Les machines virtuelles créées dans un jeu de mise à l'échelle nécessitent un compte de stockage pour stocker les disques associés.

1. Remplacez le texte entre guillemets par le nom que vous souhaitez utiliser pour le compte de stockage, puis vérifiez si ce nom est unique :

        $saName = "storage account name"
        Test-AzureName -Storage $saName

    Si la réponse est **False**, le nom proposé est unique.

2. Remplacez le texte entre guillemets par le type du compte de stockage, puis créez le compte avec le nom et l'emplacement que vous avez précédemment définis. Les valeurs possibles sont : Standard\_LRS, Standard\_GRS, Standard\_RAGRS ou Premium\_LRS :

        $saType = "storage account type"
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Le résultat suivant devrait s'afficher :

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
	                    	.Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

### Réseau virtuel

Un réseau virtuel est requis pour les machines virtuelles dans le jeu de mise à l'échelle.

1. Remplacez le texte entre guillemets par le nom que vous souhaitez utiliser pour le sous-réseau du réseau virtuel, puis créez la configuration :

        $subName = "subnet name"
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24

2. Remplacez le texte entre guillemets par le nom que vous souhaitez utiliser pour le réseau virtuel, puis créez ce réseau virtuel à l'aide des informations et des ressources que vous avez définies précédemment :

        $netName="virtual network name"
        $vnet=New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Adresse IP publique

Avant de pouvoir créer une interface réseau, vous devez créer une adresse IP publique.

1. Remplacez le texte entre guillemets par l'étiquette de nom de domaine que vous souhaitez utiliser avec votre adresse IP publique, puis vérifiez si le nom est unique. L’étiquette ne peut contenir que des lettres, des chiffres et des tirets, et le dernier caractère doit être une lettre ou un chiffre :

        $domName = "domain name label"
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    Si la réponse est **False**, le nom proposé est unique.

2. Remplacez le texte entre guillemets par le nom que vous souhaitez utiliser pour l'adresse IP publique, puis créez cette adresse :

        $pipName = "public ip address name"
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### Interface réseau

Maintenant que vous disposez de l'adresse IP publique, vous pouvez créer l'interface réseau.

1. Remplacez le texte entre guillemets par le nom que vous souhaitez utiliser pour l’interface réseau, puis créez cette interface réseau à l'aide des ressources que vous avez créées précédemment :

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


### Créer le jeu de mise à l’échelle de machine virtuelle

Maintenant que vous disposez de toutes les ressources nécessaires, il est temps de créer le jeu de mise à l'échelle.

1. Remplacez le texte entre guillemets par le nom que vous souhaitez utiliser pour la configuration IP, puis créez cette configuration :

        $ipName = "IP configuration name"
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Remplacez le texte entre guillemets par le nom que vous souhaitez utiliser pour la configuration du jeu de mise à l’échelle, puis créez cette configuration. Cette étape inclut la définition de la taille (appelée SkuName) des machines virtuelles dans le jeu. Consultez la rubrique [Tailles de machines virtuelles](..\virtual-machines\virtual-machines-windows-sizes.md) pour trouver une taille adaptée à vos besoins. Pour cet exemple, il est recommandé d'utiliser Standard\_A0. :

        $vmssName = "Scale set configuration name"
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0"
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssName -Primary $true -IPConfiguration $ipConfig

    Le résultat suivant devrait s'afficher :

        Sku                   : {
                                  "name": "Standard_A0",
                                  "tier": null,
                                  "capacity": 3
        						}
        UpgradePolicy         : {
                                  "mode": "automatic"
                                }
        VirtualMachineProfile : {
                                  "osProfile": null,
                                  "storageProfile": null,
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myniccfg1",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "myipconfig1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [],
                                            "properties.loadBalancerInboundNatPools": [],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": null
                                  }
                                }
        ProvisioningState     :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags.Count            : 0
        Tags                  :

3. Remplacez le texte entre guillemets pour le préfixe de nom d'ordinateur que vous souhaitez utiliser, pour le nom du compte d'administrateur sur les machines virtuelles, pour le mot de passe du compte, puis créez le profil de système d'exploitation :

        $computerName = "computer name prefix"
        $adminName = "administrator account name"
        $adminPassword = "password for administrator accounts"
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

    La section osProfile devrait ressembler à ceci :

        "osProfile": {
          "computerNamePrefix": "myvmss1",
          "adminUsername": "########",
          "adminPassword": "########",
          "customData": null,
          "windowsConfiguration": null,
          "linuxConfiguration": null,
          "secrets": null
        },

4. Remplacez le texte entre guillemets par le nom que vous souhaitez utiliser pour le profil de stockage, les informations de l'image et le chemin d'accès au stockage des disques des machines virtuelles, puis créez le profil. Consultez la rubrique [Rechercher et sélectionner des images de machines virtuelles Azure avec Windows PowerShell et l’interface de ligne de commande Azure](..\virtual-machines\virtual-machines-windows-cli-ps-findimage.md) pour identifier les informations dont vous avez besoin.

        $storeProfile = "storage profile name"
        $imagePublisher = "image publisher name, such as MicrosoftWindowsServer"
        $imageOffer = "offer from publisher, such as WindowsServer"
        $imageSku = "sku of image, such as 2012-R2-Datacenter"
        $vhdContainer = "URI of storage container"
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainer -OsDiskCreateOption "FromImage" -OsDiskCaching "None"

    La section storageProfile devrait ressembler à ceci :

        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "mystore1",
            "caching": "None",
            "createOption": "FromImage",
            "osType": null,
            "image": null,
            "vhdContainers": {
              "http://myst1.blob.core.windows.net/vhds"
            }
          }
        },

5. Remplacez le texte entre guillemets par le nom du jeu de mise à l'échelle de machine virtuelle, puis créez ce jeu :

        $vmssName = "scale set name"
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Vous devriez voir quelque chose comme ce qui suit, indiquant que le déploiement a réussi :

        ProvisioningState     : Succeeded
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags.Count            : 0
        Tags                  :

## Étape 3 : Explorer les ressources

Utilisez ces ressources pour explorer le jeu de mise à l'échelle de machine virtuelle que vous venez de créer :

- Portail Azure : une quantité limitée d’informations est disponible via le portail.
- [Azure Resource Explorer](https://resources.azure.com/) : il s’agit du meilleur outil pour déterminer l’état actuel de votre jeu de mise à l’échelle.
- Azure PowerShell : utilisez cette commande pour obtenir des informations :

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## Étapes suivantes

1. Consultez la rubrique [Vue d’ensemble des jeux de mise à l’échelle de machines virtuelles](virtual-machine-scale-sets-overview.md) pour en savoir plus.

2. Vous pouvez configurer la mise à l'échelle automatique de votre jeu de mise à l’échelle à l'aide des informations fournies dans la rubrique [Mise à l’échelle automatique et jeux de mise à l’échelle de machines virtuelles](virtual-machine-scale-sets-autoscale-overview.md)

<!---HONumber=AcomDC_0427_2016-->