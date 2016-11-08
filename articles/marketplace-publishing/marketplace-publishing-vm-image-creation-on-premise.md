---
title: Création d’une image de machine virtuelle locale pour Azure Marketplace | Microsoft Docs
description: Découvrez et exécutez les étapes de création d’une image de machine virtuelle locale et déployez-la dans Azure Marketplace pour que d’autres utilisateurs puissent l’acheter.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: ''
editor: ''

ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte

---
# Développer une image de machine virtuelle locale pour Azure Marketplace
Nous vous recommandons fortement de développer les disques durs virtuels (VHD) Azure directement dans le cloud à l’aide du protocole RDP. Toutefois, si nécessaire, vous pouvez télécharger un disque dur virtuel et le développer à l’aide d’une infrastructure locale.

Pour le développement local, vous devez télécharger le disque dur virtuel de système d’exploitation de la machine virtuelle créée. Ces étapes s’intègrent à l’étape 3.3 présentée plus haut.

## Télécharger une image de disque dur virtuel
### Localiser une URL d’objet blob
Pour télécharger le disque dur virtuel, vous devez d’abord localiser l’URL d’objet blob du disque de système d’exploitation.

Recherchez l’URL d’objet blob à partir du nouveau [portail Microsoft Azure](https://portal.azure.com) :

1. Accédez à **Parcourir** > **Machines virtuelles**, puis sélectionnez la machine virtuelle déployée.
2. Sous **Configurer**, sélectionnez la mosaïque **Disques** qui ouvre le panneau Disques.
   
   ![drawing](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Sélectionnez le **disque de système d’exploitation**, qui ouvre un autre panneau indiquant les propriétés du disque, y compris l’emplacement du disque dur virtuel.
4. Copiez cette URL d’objet blob.
   
   ![dessin](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Supprimez la machine virtuelle déployée, sans supprimer les disques de stockage. Vous pouvez également arrêter la machine virtuelle au lieu de la supprimer. Ne téléchargez pas le disque dur virtuel de système d’exploitation quand la machine virtuelle est en cours d’exécution.
   
   ![dessin](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### Télécharger un disque dur virtuel
Une fois que vous connaissez l’URL d’objet blob, vous pouvez télécharger le disque dur virtuel à l’aide du [portail Azure](http://manage.windowsazure.com/) ou de PowerShell.

> [!NOTE]
> Au moment de la création de ce guide, la fonctionnalité de téléchargement d’un disque dur virtuel n’est pas encore présente dans le nouveau portail Microsoft Azure.
> 
> 

**Télécharger le disque dur virtuel de système d’exploitation à partir du [portail Azure](http://manage.windowsazure.com/)** actuel

1. Si ce n’est pas déjà fait, connectez-vous au portail Azure.
2. Cliquez sur l’onglet **Stockage**.
3. Sélectionnez le compte de stockage dans lequel est stocké le disque dur virtuel.
   
   ![dessin](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Les propriétés du compte de stockage s’affichent. Sélectionnez l’onglet **Conteneurs**.
   
   ![dessin](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Sélectionnez le conteneur dans lequel est stocké le disque dur virtuel. Par défaut, quand il est créé à partir du portail, le disque dur virtuel est stocké dans un conteneur de disques durs virtuels.
   
   ![dessin](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Sélectionnez le disque dur virtuel de système d’exploitation approprié en comparant l’URL à celle que vous avez enregistrée.
7. Cliquez sur **Télécharger**.
   
   ![dessin](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### Télécharger un disque dur virtuel à l’aide de PowerShell
Outre le portail Azure, vous pouvez utiliser l’applet de commande [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) pour télécharger le disque dur virtuel de système d’exploitation.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Par exemple, Save-AzureVhd -Source “https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd” -LocalFilePath “C:\\Users\\Administrator\\Desktop\\baseimagevm.vhd” -StorageKey <String>

> [!NOTE]
> **Save-AzureVhd** a également une option **NumberOfThreads** qui peut servir à augmenter le parallélisme pour tirer le meilleur parti de la bande passante disponible pour le téléchargement.
> 
> 

## Télécharger des disques durs virtuel dans un compte de stockage Azure
Si vous avez préparé vos disques durs virtuels localement, vous devez les télécharger dans un compte de stockage dans Azure. Cette étape a lieu après la création de votre disque dur virtuel local, mais avant d’obtenir la certification pour votre image de machine virtuelle.

### Créer un compte de stockage et un conteneur
Comme mentionné précédemment, nous vous recommandons de télécharger les disques durs virtuels dans un compte de stockage dans une région aux États-Unis. Tous les disques durs virtuels pour une seule référence doivent être placés dans un seul conteneur au sein d’un seul compte de stockage.

Pour créer un compte de stockage, vous pouvez utiliser le [portail Microsoft Azure](https://portal.azure.com/), PowerShell ou l’outil en ligne de commande Linux.

**Créer un compte de stockage à partir du portail Microsoft Azure**

1. Cliquez sur **Nouveau**.
2. Sélectionnez **Stockage**.
3. Renseignez le nom du compte de stockage et sélectionnez un emplacement.
   
   ![dessin](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Cliquez sur **Create**.
5. Le panneau du compte de stockage créé doit être ouvert. Dans le cas contraire, sélectionnez **Parcourir** > **Comptes de stockage**. Dans le panneau Compte de stockage, sélectionnez le compte de stockage créé.
6. Sélectionnez **Conteneurs**.
   
   ![dessin](media/marketplace-publishing-vm-image-creation-on-premise/img09.png)
7. Dans le panneau Conteneurs, sélectionnez **Ajouter** et entrez le nom et les autorisations du conteneur. Sélectionnez **Privé** pour les autorisations du conteneur.

> [!TIP]
> Nous vous recommandons de créer un conteneur par référence que vous envisagez de publier.
> 
> 

  ![dessin](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### Créer un compte de stockage à l’aide de PowerShell
À l’aide de PowerShell, créez un compte de stockage au moyen de l’applet de commande [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx).

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Ensuite, vous pouvez créer un conteneur dans ce compte de stockage au moyen de l’applet de commande [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx).

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Ces commandes supposent que le contexte actuel du compte de stockage a déjà été défini dans PowerShell. Reportez-vous à [Configuration d’Azure PowerShell](marketplace-publishing-powershell-setup.md) pour plus d’informations sur la configuration de PowerShell.
> 
> ### Créer un compte de stockage à l’aide de l’outil en ligne de commande pour Mac et Linux
> Dans l’[outil en ligne de commande Linux](../virtual-machines/virtual-machines-linux-cli-manage.md), créez un compte de stockage comme suit :
> 
> 

        azure storage account create mystorageaccount --location "West US"

Créez un conteneur comme suit :

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## Télécharger un disque dur virtuel
Une fois le compte de stockage et le conteneur créés, vous pouvez télécharger vos disques durs virtuels préparés. Vous pouvez utiliser PowerShell, l’outil en ligne de commande Linux ou d’autres outils de gestion Azure Storage.

### Télécharger un disque dur virtuel à l’aide de PowerShell
Utilisez l’applet de commande [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### Télécharger un disque dur virtuel à l’aide de l’outil en ligne de commande pour Mac et Linux
Avec l’[outil en ligne de commande Linux](../virtual-machines/command-line-tools.md), utilisez la commande suivante : azure vm image create <image name> --location <Location of the data center> --OS Linux <LocationOfLocalVHD>

## Voir aussi
* [Création d’une image de machine virtuelle pour Azure Marketplace](marketplace-publishing-vm-image-creation.md)
* [Configuration d’Azure PowerShell](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0615_2016-->