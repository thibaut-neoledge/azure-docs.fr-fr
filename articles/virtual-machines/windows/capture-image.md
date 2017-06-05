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
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 8f1d488fd8f71bf90c8bf7b7c1544445ffbd7686
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Comment capturer une image de machine virtuelle à partir d’une machine virtuelle Azure généralisée
Cet article vous montre comment utiliser Azure PowerShell pour créer une image à partir d’une machine virtuelle Azure généralisée. Vous pouvez ensuite utiliser l’image pour créer une autre machine virtuelle. L’image comprend le disque du système d’exploitation, ainsi que les disques de données attachés à la machine virtuelle. L’image n’inclut pas les ressources du réseau virtuel. Vous devez donc configurer les ressources lorsque vous créez la nouvelle machine virtuelle. 

## <a name="prerequisites"></a>Composants requis
Vous devez disposer d’une installation d’Azure PowerShell version 1.0.x ou ultérieure. Si vous n’avez pas déjà installé PowerShell, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) .

## <a name="generalize-the-windows-vm-using-sysprep"></a>Généraliser la machine virtuelle Windows à l’aide de Sysprep

Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Si vous exécutez Sysprep avant de charger votre disque dur virtuel vers Azure pour la première fois, vérifiez que vous avez [préparé votre machine virtuelle](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) avant d’exécuter Sysprep. 
> 
> 

1. Connectez-vous à la machine virtuelle Windows
2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\system32\sysprep**, puis exécutez `sysprep.exe`.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.
4. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
5. Cliquez sur **OK**.
   
    ![Démarrer Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. Ne redémarrez pas la machine virtuelle.


Vous pouvez également généraliser une machine virtuelle Linux à l’aide de `sudo waagent -deprovision+user`, puis utiliser PowerShell pour capturer la machine virtuelle. Pour plus d’informations sur l’utilisation de l’interface de ligne de commande pour capturer une machine virtuelle, consultez [Généraliser et capturer une machine virtuelle Linux à l’aide de l’interface de ligne de commande Azure](../linux/capture-image.md)

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


