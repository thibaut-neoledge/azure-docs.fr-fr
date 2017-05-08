---
title: "Créer une copie d’une machine virtuelle spécialisée dans Azure | Microsoft Docs"
description: "Apprenez à créer une copie d’une machine virtuelle Windows spécialisée qui s’exécute dans Azure avec le modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ce7e6cd3-6a4a-4fab-bf66-52f699b1398a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 1b2b013fb29c66817aab2ea5e63917b59726b4e8
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Créer une copie d’une machine virtuelle Windows spécialisée qui s’exécute dans Azure
Cet article vous montre comment utiliser l’outil AzCopy pour créer une copie du disque dur virtuel à partir d’une machine virtuelle Windows spécialisée qui s’exécute dans Azure. Vous pouvez ensuite utiliser la copie du disque dur virtuel pour créer une machine virtuelle. 

* Si souhaitez copier une machine virtuelle généralisée, consultez [Créer une image de machine virtuelle à partir d’une machine virtuelle Azure généralisée existante](capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Si vous souhaitez charger un disque dur virtuel à partir d’une machine virtuelle locale, par exemple créé avec Hyper-V, consultez [Charger un disque dur virtuel Windows à partir d’une machine virtuelle locale vers Azure](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="before-you-begin"></a>Avant de commencer
Veillez à :

* Avoir à votre disposition des informations sur les **comptes de stockage source et de destination**. Pour la machine virtuelle source, vous devez disposer du nom du compte de stockage et du conteneur. En général, le nom du conteneur est **vhds**. Vous devez également disposer d’un compte de stockage de destination. Si ce n’est pas le cas, vous pouvez en créer un avec le portail (**Plus de services** > Comptes de stockage > Ajouter) ou l’applet de commande [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). 
* Installer Azure [PowerShell 1.0](/powershell/azure/overview) (ou une version ultérieure).
* Télécharger et installer [l’outil AzCopy](../../storage/storage-use-azcopy.md). 

## <a name="deallocate-the-vm"></a>Libérer la machine virtuelle
Libérez la machine virtuelle, ce qui libère le disque dur virtuel à copier. 

* **Portail** : cliquez sur **Machines virtuelles** > **myVM** > Arrêter
* **PowerShell** : `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` libère la machine virtuelle nommée **myVM** dans le groupe de ressources **myResourceGroup**.

**L’état** de la machine virtuelle dans le Portail Azure passe de **Arrêté** à **Arrêté (libéré)**.

## <a name="get-the-storage-account-urls"></a>Récupérer les URL des comptes de stockage
Vous avez besoin des URL des comptes de stockage source et de destination. Les URL ressemblent à : `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Si vous connaissez déjà le nom du compte de stockage et du conteneur, vous pouvez simplement remplacer les informations entre crochets pour créer votre URL. 

Vous pouvez utiliser le Portail Azure ou Azure PowerShell pour récupérer l’URL :

* **Portail** : cliquez sur **Plus de services** > **Comptes de stockage** > <storage account> **Blobs** : votre fichier de disque dur virtuel source se trouve probablement dans le conteneur **vhds**. Cliquez sur les **Propriétés** du conteneur et copiez le texte intitulé **URL**. Vous aurez besoin des URL des conteneurs source et de destination. 
* **PowerShell** : `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` récupère les informations de la machine virtuelle nommée **myVM** dans le groupe de ressources **myResourceGroup**. Dans les résultats, recherchez **l’URI du disque dur virtuel** dans la section **Profil de stockage**. La première partie de l’URI est l’URL du conteneur et la dernière est le nom du disque dur virtuel du système d’exploitation de la machine virtuelle.

## <a name="get-the-storage-access-keys"></a>Récupérer les clés d’accès de stockage
Récupérez les clés d’accès des comptes de stockage source et de destination. Pour plus d’informations sur les clés d’accès, consultez la page [À propos des comptes de stockage Azure](../../storage/storage-create-storage-account.md).

* **Portail** : cliquez sur **Plus de services** > **Comptes de stockage** > <storage account> **Tous les paramètres** > **Clés d’accès**. Copiez la clé portant le nom **key1**.
* **PowerShell** : `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` récupère la clé de stockage du compte de stockage **mystorageaccount** dans le groupe de ressources **myResourceGroup**. Copiez la clé portant le nom **key1**.

## <a name="copy-the-vhd"></a>Copier le disque dur virtuel
Vous pouvez copier des fichiers entre comptes de stockage avec AzCopy. Si le conteneur de destination spécifié n’existe pas, il est créé pour vous. 

Pour utiliser AzCopy, ouvrez une invite de commandes sur votre machine locale et accédez au dossier dans lequel AzCopy est installé. Il peut être du type *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy*. 

Pour copier tous les fichiers d’un conteneur, utilisez le commutateur **/S**. Cette procédure peut servir à copier le disque dur virtuel du système d’exploitation et tous les disques de données, s’ils se trouvent dans le même conteneur. Cet exemple montre comment copier tous les fichiers du conteneur **mysourcecontainer** dans le compte de stockage **mysourcestorageaccount** vers le conteneur **mydestinationcontainer** dans le compte de stockage **mydestinationstorageaccount**. Remplacez le nom des comptes de stockage et des conteneurs par les vôtres. Remplacez `<sourceStorageAccountKey1>` et `<destinationStorageAccountKey1>` par vos propres clés.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Si vous souhaitez copier un disque dur virtuel en particulier dans un conteneur par plusieurs fichiers, vous pouvez également spécifier le nom du fichier avec le commutateur /Pattern. Dans cet exemple, seul le fichier nommé **myFileName.vhd** est copié.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Une fois la copie terminée, vous recevez un message du type :

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>résolution des problèmes
* Lorsque vous utilisez AZCopy, si l’erreur « Le serveur n’a pas pu authentifier cette demande » s’affiche, vérifiez la valeur de l’en-tête d’autorisation : il doit être correctement formé et comporter notamment la signature. Si vous utilisez la clé 2 ou la clé de stockage secondaire, essayez d’utiliser la clé primaire ou la première clé de stockage.

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez créer une machine virtuelle en [joignant la copie du disque dur virtuel à une machine virtuelle en tant que disque du système d’exploitation](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


