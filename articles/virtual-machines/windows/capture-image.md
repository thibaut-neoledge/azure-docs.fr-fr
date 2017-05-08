---
title: "Capture d’une image de machine virtuelle à partir d’une machine virtuelle Azure généralisée | Microsoft Docs"
description: "Apprenez à capturer une image de machine virtuelle à partir d’une machine virtuelle Azure généralisée créée dans le modèle de déploiement Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7316782a1884f4affe5041bf767aa0e32946fbe0
ms.lasthandoff: 04/27/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Comment capturer une image de machine virtuelle à partir d’une machine virtuelle Azure généralisée
Cet article vous montre comment utiliser Azure PowerShell pour créer une image à partir d’une machine virtuelle Azure généralisée. Vous pouvez ensuite utiliser l’image pour créer une autre machine virtuelle. L’image comprend le disque du système d’exploitation, ainsi que les disques de données attachés à la machine virtuelle. L’image n’inclut pas les ressources du réseau virtuel. Vous devez donc configurer les ressources lorsque vous créez la nouvelle machine virtuelle. 

## <a name="prerequisites"></a>Composants requis
* Vous devez déjà avoir [généralisé la machine virtuelle](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). La généralisation d’une machine virtuelle supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Vous pouvez également généraliser une machine virtuelle Linux à l’aide de `sudo waagent -deprovision+user`, puis utiliser PowerShell pour capturer la machine virtuelle. Pour plus d’informations sur l’utilisation de l’interface de ligne de commande pour capturer une machine virtuelle, consultez [Généraliser et capturer une machine virtuelle Linux à l’aide de l’interface de ligne de commande Azure](../linux/capture-image.md)
* Vous devez disposer d’une installation d’Azure PowerShell version 1.0.x ou ultérieure. Si vous n’avez pas déjà installé PowerShell, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) .

## <a name="log-in-to-azure-powershell"></a>Connexion à Azure PowerShell
1. Ouvrez Azure PowerShell et connectez-vous à votre compte Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Une fenêtre contextuelle s’ouvre pour vous permettre d’entrer les informations d’identification de votre compte Azure.
2. Obtenez les ID d’abonnement de vos abonnements disponibles.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Définissez l’abonnement approprié à l’aide de l’ID d’abonnement.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Libération de la machine virtuelle et définition de l’état sur généralisé
1. Libérez les ressources de la machine virtuelle.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *L’état* de la machine virtuelle dans le Portail Azure passe de **Arrêté** à **Arrêté (libéré)**.
2. Définissez l’état de la machine virtuelle sur **Généralisé**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Vérifiez l’état de la machine virtuelle. La section **OSState/généralisé** pour la machine virtuelle doit avoir la valeur de **DisplayStatus** définie sur **Machine virtuelle généralisée**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Création de l’image
1. Copiez l’image de la machine virtuelle dans le conteneur de stockage de destination à l’aide de cette commande. L’image est créée dans le même compte de stockage que la machine virtuelle d’origine. Le paramètre `-Path` enregistre une copie du modèle JSON localement. Le paramètre `-DestinationContainerName` est le nom du conteneur dans lequel vous souhaitez stocker vos images. Si le conteneur n’existe pas, il est créé pour vous.
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    Vous pouvez obtenir l’URL de l’image à partir du modèle de fichier JSON. Accédez à la section **resources** > **storageProfile** > **osDisk** > **image** > **uri** pour obtenir le chemin d’accès complet de votre image. L’URL de l’image ressemble a le format suivant : `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
    Vous pouvez également vérifier l’URI dans le portail. L’image est copiée dans un conteneur nommé **system** dans votre compte de stockage. 

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez désormais [créer une machine virtuelle à partir de l’image](create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


