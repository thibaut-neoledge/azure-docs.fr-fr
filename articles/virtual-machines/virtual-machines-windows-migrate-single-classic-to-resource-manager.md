---
title: "Migrer une machine virtuelle unique à partir d’un modèle de déploiement Classic vers Azure Managed Disks | Microsoft Docs"
description: "Migrez une machine virtuelle Azure unique à partir du modèle de déploiement Classic vers Managed Disks dans le modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: a46db1815b84f0ecf93c805f3ea36e4e3d4282ac
ms.lasthandoff: 02/11/2017


---

# <a name="migrate-a-single-azure-vm-from-classic-to-managed-disks"></a>Migrer une machine virtuelle Azure unique du modèle Classic vers Managed Disks 


Cette section vous aide à migrer vos machines virtuelles Azure existantes à partir du modèle de déploiement Classic vers [Managed Disks](../storage/storage-managed-disks-overview.md) dans le modèle de déploiement Resource Manager.

## <a name="before-you-begin"></a>Avant de commencer
Si vous utilisez PowerShell, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).


## <a name="plan-for-the-migration-to-managed-disks"></a>Planification de la migration vers Managed Disks

Cette section vous aide à prendre la meilleure décision concernant les types de machines virtuelles et de disques.


### <a name="location"></a>Emplacement

Choisissez un emplacement où Azure Managed Disks est disponible. Si vous effectuez une migration vers des disques gérés Premium, assurez-vous également que le stockage Premium est disponible dans la région où vous prévoyez la migration. Pour obtenir des informations à jour sur les emplacements disponibles, consultez [Services Azure par région](https://azure.microsoft.com/regions/#services).

### <a name="vm-sizes"></a>Tailles de machine virtuelle

Si vous effectuez une migration vers des disques gérés Premium, vous devez mettre à jour la taille de la machine virtuelle par rapport à la capacité de stockage Premium disponible dans la région où se trouve la machine virtuelle. Consultez les tailles de machine virtuelle compatibles avec le stockage Premium. Les spécifications des tailles des machines virtuelles Azure sont répertoriées dans la section [Tailles des machines virtuelles](virtual-machines-windows-sizes.md).
Passez en revue les caractéristiques de performances des machines virtuelles fonctionnant avec Premium Storage et choisissez la taille de machine virtuelle la mieux adaptée à votre charge de travail. Assurez-vous que la bande passante disponible est suffisante sur votre machine virtuelle pour gérer le trafic du disque.

### <a name="disk-sizes"></a>Tailles du disque

**Disques gérés Premium**

Il existe trois types de disques gérés Premium qui peuvent être utilisés avec votre machine virtuelle, chacun présentant des limites d’E/S par seconde et de débit spécifiques. Prenez en compte ces limites lors de la sélection du type de disque Premium pour votre machine virtuelle en fonction des besoins en capacité, en performances, en extensibilité et en charges maximales de votre application.

| Type de disque Premium  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Taille du disque           | 128 Go            | 512 Go            | 1024 Go (1 To)    |
| IOPS par disque       | 500               | 2 300              | 5 000              |
| Débit par disque | 100 Mo par seconde | 150 Mo par seconde | 200 Mo par seconde |

**Disques gérés Standard**

Il existe cinq types de disques gérés Standard qui peuvent être utilisés avec votre machine virtuelle. Chacun d’eux dispose d’une capacité différente, mais ils partagent les mêmes limites d’IOPS et de débit. Choisissez le type de disque géré Standard en fonction des besoins de votre application.

| Type de disque Standard  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Taille du disque           | 30 Go            | 64 Go            | 128 Go           | 512 Go           | 1024 Go (1 To)   |
| IOPS par disque       | 500              | 500              | 500              | 500              | 500              |
| Débit par disque | 60 Mo par seconde | 60 Mo par seconde | 60 Mo par seconde | 60 Mo par seconde | 60 Mo par seconde |

### <a name="disk-caching-policy"></a>Stratégie de mise en cache du disque 

**Disques gérés Premium**

Par défaut, la stratégie de mise en cache est *Lecture seule* pour tous les disques de données Premium et *Lecture-écriture* pour le disque du système d’exploitation Premium attaché à la machine virtuelle. Ce paramètre de configuration est recommandé pour optimiser les performances des E/S de votre application. Pour les disques de données en écriture seule ou avec d'importantes opérations d'écriture (par ex., les fichiers journaux de SQL Server), désactivez la mise en cache du disque pour de meilleures performances de l'application.

### <a name="pricing"></a>Tarification

Consultez la [tarification des disques gérés](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). La tarification des disques gérés Premium est identique à celle des disques Premium non gérés. En revanche, la tarification des disques gérés Standard diffère de celle des disques Standard non gérés.


## <a name="checklist"></a>Liste de contrôle

1.  Si vous effectuez une migration vers des disques gérés Premium, assurez-vous qu’ils sont disponibles dans la région vers laquelle vous effectuez la migration.

2.  Choisissez la nouvelle série de machines virtuelles que vous allez utiliser. Elle doit être compatible avec le stockage Premium si vous migrez vers des disques gérés Premium.

3.  Définissez la taille de machine virtuelle exacte que vous allez utiliser, disponible dans la région vers laquelle vous effectuez la migration. La taille de machine virtuelle doit être suffisante pour prendre en charge le nombre de disques de données dont vous disposez. Par exemple, si vous avez quatre disques de données, la machine virtuelle doit avoir au moins deux cœurs. Prenez également en considération les besoins en puissance, mémoire et bande passante réseau.

4.  Gardez à portée de main les informations détaillées sur les machines virtuelles, notamment la liste des disques et des blobs de disques durs virtuels correspondants.

Préparez votre application pour les interruptions de service. Pour effectuer une migration sans erreur, vous devez arrêter tous les processus en cours d’exécution dans le système actuel. Ce n’est qu’à cette condition que le système présentera un état cohérent, permettant sa migration vers la nouvelle plateforme. La durée de l’interruption de service dépend de la quantité de données dans les disques à migrer.


## <a name="migrate-the-vm"></a>Migrer la machine virtuelle

Préparez votre application pour les interruptions de service. Pour effectuer une migration sans erreur, vous devez arrêter tous les processus en cours d’exécution dans le système actuel. Ce n’est qu’à cette condition que le système présentera un état cohérent, permettant sa migration vers la nouvelle plateforme. La durée de l’interruption de service dépend de la quantité de données dans les disques à migrer.


1.  Tout d’abord, définissez les paramètres communs :

    ```powershell
    $resourceGroupName = 'yourResourceGroupName'
    
    $location = 'your location' 
    
    $virtualNetworkName = 'yourExistingVirtualNetworkName'
    
    $virtualMachineName = 'yourVMName'
    
    $virtualMachineSize = 'Standard_DS3'
    
    $adminUserName = "youradminusername"
    
    $adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force
    
    $imageName = 'yourImageName'
    
    $osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
    
    $dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'
    
    $dataDiskName = 'dataDisk1'
    ```

2.  Créez un disque de système d’exploitation géré à l’aide du disque dur virtuel de la machine virtuelle classique.

    Assurez-vous que vous avez indiqué l’URI complet du disque dur virtuel du système d’exploitation dans le paramètre $osVhdUri. De plus, dans **Type de compte**, indiquez **PremiumLRS** ou **StandardLRS** selon le type de disque (Premium ou Standard) vers lequel vous effectuez la migration.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  Attachez le disque du système d’exploitation à la nouvelle machine virtuelle.

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  Créez un disque de données géré à partir du fichier de disque dur virtuel de données et ajoutez-le à la nouvelle machine virtuelle.

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  Créez la machine virtuelle en définissant l’adresse IP publique, le réseau virtuel et la carte réseau.

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>Des étapes supplémentaires susceptibles de ne pas être exposées dans ce guide peuvent être nécessaires pour prendre en charge vos applications.
>
>

## <a name="next-steps"></a>Étapes suivantes

- Connectez-vous à la machine virtuelle. Pour plus d’informations, voir [Connexion à une machine virtuelle Azure exécutant Windows](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


