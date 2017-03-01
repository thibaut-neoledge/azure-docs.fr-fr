---
title: "Créer une copie d’un disque géré Azure pour la sauvegarde | Microsoft Docs"
description: "Découvrez comment créer une copie de disque géré Azure à utiliser pour la sauvegarde ou la résolution des problèmes de disque."
documentationcenter: 
author: cwatsonMSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 2/9/2017
ms.author: cwatson
translationtype: Human Translation
ms.sourcegitcommit: b82833c916900def7da0058bab4d419a9dee742b
ms.openlocfilehash: 7be6751cd90ad4dab0dbeb1b49b0838e60a4c5b8


---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Créer une copie d’un disque dur virtuel stocké en tant que disque Azure géré à l’aide de captures instantanées gérées
Prenez une capture instantanée d’un disque géré pour la sauvegarde ou créez un disque géré à partir de la capture instantanée et attachez-le à une machine virtuelle de test pour résoudre les problèmes. Une capture instantanée gérée est une copie complète d’un disque géré de machine virtuelle à un moment donné. Elle crée une copie en lecture seule de votre disque dur virtuel et, par défaut, le stocke sous la forme d’un disque géré Standard. Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble d’Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Pour plus d’informations sur la tarification, consultez la page [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/managed-disks/). 

## <a name="before-you-begin"></a>Avant de commencer
Si vous utilisez PowerShell, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).

## <a name="copy-the-vhd-with-a-snapshot"></a>Copier le disque dur virtuel avec une capture instantanée
Utilisez le portail Azure ou PowerShell pour prendre une capture instantanée du disque géré.

### <a name="use-azure-portal-to-take-a-snapshot"></a>Utiliser le portail Azure pour prendre une capture instantanée 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans l’angle supérieur gauche, cliquez sur **Nouveau** et recherchez **Capture instantanée**.
3. Dans le panneau Capture instantanée, cliquez sur **Créer**.
4. Entrez un **nom** pour la capture instantanée.
5. Sélectionnez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups) existant ou tapez le nom d’un nouveau groupe. 
6. Sélectionnez un emplacement de centre de données Azure.  
7. Dans **Disque source**, sélectionnez le disque géré dont vous souhaitez obtenir une capture instantanée.
8. Sélectionnez le **type de compte** à utiliser pour stocker la capture instantanée. Nous vous recommandons d’utiliser le type **Standard_LRS**, sauf si vous avez besoin de la stocker sur un disque hautes performances.
9. Cliquez sur **Create**.

### <a name="use-powershell-to-take-a-snapshot"></a>Utiliser PowerShell pour prendre une capture instantanée
Les étapes suivantes vous expliquent comment obtenir une copie du disque dur virtuel, créer les configurations de capture instantanée et prendre une capture instantanée du disque à l’aide de l’applet de commande New-AzureRmSnapshot<!--Add link to cmdlet when available-->. 

1. Définissez certains paramètres. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'southeastasia' 
$dataDiskName = 'ContosoMD_datadisk1' 
$snapshotName = 'ContosoMD_datadisk1_snapshot1'  
```
  Remplacez les valeurs de paramètre :
  -  « myResourceGroup » par le groupe de ressources de la machine virtuelle.
  -  « southeastasia » par l’emplacement géographique où vous souhaitez stocker votre capture instantanée gérée. <!---How do you look these up? -->
  -  « ContosoMD_datadisk1 » par le nom du disque dur virtuel que vous souhaitez copier.
  -  « ContosoMD_datadisk1_snapshot1 » par le nom que vous souhaitez utiliser pour la nouvelle capture instantanée.

2. Obtenez le disque dur virtuel à copier.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Créez les configurations de capture instantanée. 

 ```powershell
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Prenez la capture instantanée.

 ```powershell
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Si vous envisagez d’utiliser la capture instantanée pour créer un disque géré et l’attacher à une machine virtuelle qui doit être hautement performante, utilisez le paramètre `-AccountType Premium_LRS` avec la commande New-AzureRmSnapshot. Le paramètre crée la capture instantanée et la stocke en tant que disque géré Premium. Les disques gérés Premium sont plus chers que les disques gérés Standard. Vérifiez qu’il vous faut vraiment le niveau Premium avant d’utiliser ce paramètre.





<!--HONumber=Feb17_HO2-->


