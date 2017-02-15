---
title: Charger un disque dur virtuel (VHD) Windows pour Resource Manager | Microsoft Docs
description: "Apprenez à télécharger un disque dur virtuel (VHD) de machine virtuelle Windows depuis un environnement local vers Azure à l’aide du modèle de déploiement Resource Manager. Vous pouvez télécharger un disque dur virtuel à partir d’une machine virtuelle généralisée ou spécialisée."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 192c8e5a-3411-48fe-9fc3-526e0296cf4c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 5aa6b2149170ef04af0ebde957feda5630c5d5eb


---
# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Télécharger un disque dur virtuel Windows d’une machine virtuelle locale vers Azure
Cet article vous montre comment créer et télécharger un disque dur virtuel (VHD) Windows afin de créer une machine virtuelle Azure. Vous pouvez télécharger un disque dur virtuel à partir d’une machine virtuelle généralisée ou spécialisée. 

Pour en savoir plus sur les disques et les disques durs virtuels dans Azure, consultez [À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prepare-the-vm"></a>Préparation de la machine virtuelle
Vous pouvez télécharger des disques durs virtuels généralisés et spécialisés dans Azure. Chaque type nécessite que vous prépariez la machine virtuelle avant de commencer.

* **Disque dur virtuel généralisé** : toutes les informations de votre compte personnel de ce type de disque ont été supprimées avec Sysprep. Si vous prévoyez d’utiliser le disque dur virtuel en tant qu’image pour créer des machines virtuelles, vous devez :
  
  * [Préparer un disque dur virtuel Windows à charger sur Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
  * [Généraliser la machine virtuelle avec Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* **Disque dur virtuel spécialisé** : ce type de disque gère les comptes d’utilisateurs, les applications et les autres données d’état à partir de votre machine virtuelle d’origine. Si vous prévoyez d’utiliser le disque dur virtuel tel quel pour créer une machine virtuelle, veillez à effectuer les étapes suivantes. 
  
  * [Préparez un disque dur virtuel Windows à charger sur Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne généralisez pas** la machine virtuelle à l’aide de Sysprep.
  * Supprimez tous les outils de virtualisation invité et les agents installés sur la machine virtuelle (c’est-à-dire les outils VMware).
  * Vérifiez que la machine virtuelle est configurée pour extraire son adresse IP et ses paramètres DNS via DHCP. Cela permet de s’assurer que le serveur obtient une adresse IP dans le réseau virtuel lors de son démarrage. 

## <a name="log-in-to-azure"></a>Connexion à Azure
Si vous n’avez pas déjà installé PowerShell version 1.4 ou ultérieure, lisez [Installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs).

1. Ouvrez Azure PowerShell et connectez-vous à votre compte Azure. Une fenêtre contextuelle s’ouvre pour vous permettre d’entrer les informations d’identification de votre compte Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Obtenez les ID d’abonnement de vos abonnements disponibles.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Définissez l’abonnement approprié à l’aide de l’ID d’abonnement. Remplacez `<subscriptionID>` par l’ID de l’abonnement approprié.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obtention du compte de stockage
Vous avez besoin d’un compte de stockage dans Azure pour stocker l’image de la machine virtuelle téléchargée. Vous pouvez utiliser un compte de stockage existant ou en créer un. 

Pour afficher les comptes de stockage disponibles, tapez :

```powershell
Get-AzureRmStorageAccount
```

Si vous voulez utiliser un compte de stockage existant, passez à la section [Charger l’image de la machine virtuelle](#upload-the-vm-vhd-to-your-storage-account).

Si vous devez créer un compte de stockage, procédez comme suit :

1. Vous avez besoin du nom du groupe de ressources dans lequel doit être créé le compte de stockage. Pour rechercher tous les groupes de ressources dans votre abonnement, tapez :
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Pour créer un groupe de ressources nommé **MyResourceGroup** dans la région **Ouest des États-Unis**, tapez :

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Créez un compte de stockage nommé **mystorageaccount** dans ce groupe de ressources en utilisant l’applet de commande [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) :
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Les valeurs valides pour -SkuName sont :
   
   * **Standard_LRS** - Stockage localement redondant. 
   * **Standard_ZRS** - Stockage redondant dans une zone.
   * **Standard_GRS** - Stockage géo-redondant. 
   * **Standard_RAGRS** - Stockage géo-redondant avec accès en lecture. 
   * **Premium_LRS** - Stockage Premium localement redondant. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Téléchargement du disque dur virtuel vers votre compte de stockage
Utilisez l’applet de commande [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) pour charger l’image vers un conteneur de votre compte de stockage. Cet exemple télécharge le fichier **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` vers un compte de stockage nommé **mystorageaccount** dans le groupe de ressources **myResourceGroup**. Le fichier est placé dans le conteneur nommé **mycontainer** et le nouveau nom de fichier est **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Si l’opération réussit, vous obtenez une réponse semblable à celle-ci :

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Selon votre connexion réseau et la taille de votre fichier de disque dur virtuel, l’exécution de cette commande peut prendre un certain temps.

## <a name="next-steps"></a>Étapes suivantes
* [Créer une machine virtuelle dans Azure à partir d’un disque dur virtuel généralisée](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Créez une machine virtuelle dans Azure à partir d’un disque dur virtuel spécialisé](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en le joignant comme disque de système d’exploitation lorsque vous créez une nouvelle machine virtuelle.




<!--HONumber=Feb17_HO2-->


