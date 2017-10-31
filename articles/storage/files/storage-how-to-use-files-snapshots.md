---
title: "Guide pratique pour utiliser un instantané de partage de fichiers Azure (préversion) | Microsoft Docs"
description: "Utilisez un instantané de partage de fichiers Azure. Les instantanés de partage de fichiers Azure sont une version en lecture seule d’un partage de fichiers Azure pris à un point dans le temps. Un instantané de partage peut être lu, copié ou supprimé, mais pas modifié. Les instantanés de partage sont une façon de sauvegarder le partage à un instant T."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 33b64e1ad3fd5a2a6954a02da0fb303acca54c40
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2017
---
# <a name="work-with-azure-file-share-snapshots-preview"></a>Utiliser des instantanés de partage de fichiers Azure (préversion)
Les instantanés de partage de fichiers Azure (préversion) sont une version en lecture seule d’un partage de fichiers Azure pris à un point dans le temps. Un instantané de partage peut être lu, copié ou supprimé, mais pas modifié. Les instantanés de partage sont une façon de sauvegarder le partage à un instant T. Dans cet article, vous allez découvrir comment créer, gérer et supprimer des instantanés de partage de fichiers Azure. Pour en savoir plus sur les instantanés de partage, consultez [Vue d’ensemble des instantanés de partage](storage-snapshots-files.md) ou [Forum aux questions sur les instantanés](storage-files-faq.md).

## <a name="create-azure-files-share-snapshots"></a>Créer des instantanés de partage de fichiers Azure

Vous pouvez créer un instantané de partage à l’aide du portail, de PowerShell, de l’interface CLI, de l’API REST ou de n’importe quel SDK Stockage. Les sections suivantes vous indiquent comment créer un instantané de partage à l’aide du portail, de l’interface CLI et de PowerShell. 

Vous pouvez prendre un instantané d’un partage de fichiers alors qu’il est en cours d’utilisation. Toutefois, les instantanés de partage capturent uniquement les données qui étaient déjà écrites dans le partage de fichiers Azure au moment de l’émission de la commande de l’instantané de partage. Ainsi, toutes les données mises en cache par les applications ou le système d’exploitation risquent d’être exclues.

### <a name="create-share-snapshot-using-portal"></a>Créer un instantané de partage à l’aide du portail  
Vous pouvez simplement accéder à votre partage de fichiers dans le portail, puis sélectionner le bouton `Create a Snapshot` pour créer un instantané de partage à un point dans le temps.

>   ![./media/storage-snapshots-create/portal-create-snapshot.png](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-share-snapshot-using-cli-20"></a>Créer un instantané de partage à l’aide de CLI 2.0
Vous pouvez créer un instantané de partage à l’aide de la commande `az storage share snapshot` :

```azurecli-interactive
az storage share snapshot -n <share name>
```

Exemple de sortie
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-share-snapshot-using-powershell"></a>Créer un instantané de partage à l’aide de PowerShell
Vous pouvez supprimer un instantané de partage à l’aide de la commande `$share.Snapshot()` :

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="common-share-snapshot-operations"></a>Opérations courantes sur les instantanés de partage

Vous pouvez énumérer les instantanés de partage associés à votre partage de fichiers à l’aide de l’onglet « Versions précédentes » sous Windows, via l’API REST, la bibliothèque cliente, PowerShell et le portail. Une fois le partage de fichiers Azure monté, vous pouvez afficher toutes les versions antérieures du fichier à l’aide de l’onglet « Versions précédentes » sous Windows. Dans les sections suivantes, vous allez apprendre à utiliser le portail Azure, Windows et Azure CLI 2.0 pour répertorier, parcourir et restaurer des instantanés de partage.

### <a name="share-snapshot-operations-in-portal"></a>Opérations sur les instantanés de partage dans le portail

Dans le portail, vous pouvez répertorier tous les instantanés de partage pour un partage de fichiers et les parcourir pour consulter leur contenu.

#### <a name="view-share-snapshot"></a>Afficher un instantané de partage
Sur votre partage de fichiers, sous Instantané, sélectionnez **Afficher les instantanés**.

![./media/storage-snapshots-list-browse/snapshot-view-portal.png](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-share-snapshot-content"></a>Répertorier les instantanés de partage et parcourir leur contenu
Affichez la liste des instantanés de partage, puis parcourez directement le contenu de l’instantané de votre choix en sélectionnant son horodatage.

![./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

Vous pouvez également sélectionner le bouton **Se connecter** dans la vue de la liste des instantanés pour obtenir la commande `net use` et le chemin du répertoire d’un instantané de partage particulier afin d’accéder directement à celui-ci.


![./media/storage-snapshots-list-browse/snapshot-connect-portal.pngsnapshot-list-portal.png](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-share-snapshot"></a>Effectuer un téléchargement ou une restauration à partir d’un instantané de partage
Dans le portail, téléchargez ou restaurez le fichier approprié à partir d’un instantané.

![./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="file-share-snapshot-operations-in-windows"></a>Opérations sur les instantanés de partage de fichiers dans Windows
Si vous avez déjà pris des instantanés de votre partage de fichiers, vous pouvez afficher les versions précédentes d’un partage, d’un répertoire ou d’un fichier particulier à partir de votre partage de fichiers Azure monté sur Windows. Par exemple, voici comment vous pouvez utiliser la fonctionnalité **Versions précédentes** pour afficher et restaurer une version précédente d’un répertoire particulier dans Windows :

> [!Note]  
> Vous pouvez effectuer les mêmes opérations au niveau des partages, ainsi que des fichiers. Seules sont répertoriées les versions qui contiennent des modifications pour ce répertoire ou fichier. Si un fichier ou un répertoire particulier n’a pas changé entre deux instantanés de partage, l’instantané de partage apparaît dans la liste des versions précédentes au niveau du partage, mais pas dans la liste des versions précédentes du répertoire ou du fichier.

#### <a name="mount-file-share"></a>Monter le partage de fichiers
Tout d’abord, montez le partage de fichiers à l’aide de la commande net use.

#### <a name="open-mounted-share-in-explorer"></a>Ouvrir le partage monté dans l’Explorateur
Accédez à l’Explorateur de fichiers, puis recherchez le partage monté.

![./media/storage-snapshots-list-browse/snapshot-windows-mount.png](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Répertorier les versions antérieures
 Accédez à l’élément ou à l’élément parent à restaurer. Double-cliquez sur pour accéder au répertoire souhaité. Cliquez avec le bouton droit et sélectionnez « Propriétés » dans le menu.

![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Sélectionnez **Versions précédentes** pour afficher la liste des instantanés de partage associés à ce répertoire. L’affichage de la liste peut prendre quelques secondes, selon la vitesse du réseau et le nombre d’instantanés de partage.

 ![./media/storage-snapshots-list-browse/snapshot-windows-list.png](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Vous pouvez sélectionner **Ouvrir** pour parcourir un instantané spécifique. 

 ![./media/storage-snapshots-list-browse/snapshot-browse-windows.png](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Effectuer une restauration à partir d’une version précédente
Effectuez une **restauration** pour copier à l’emplacement d’origine le contenu de l’ensemble du répertoire de manière récursive à l’heure de création de l’instantané de partage.
 ![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="file-share-snapshot-operations-in-azure-cli-20"></a>Opérations sur les instantanés de partage de fichiers dans Azure CLI 2.0
Vous pouvez utiliser Azure CI 2.0 pour effectuer les mêmes opérations, telles que répertorier les instantanés de partage, explorer le contenu des instantanés de partage, restaurer ou télécharger des fichiers à partir des instantanés de partage ou supprimer des instantanés de partage.

#### <a name="list-share-snapshots"></a>Répertorier les instantanés de partage

Vous pouvez répertorier les instantanés d’un partage spécifique à l’aide de la commande [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) avec `--include-snapshots`.

```azurecli-interactive 
az storage share list --include-snapshots
```

#### <a name="sample-output"></a>Exemple de sortie
La commande vous donne la liste des instantanés de partage, ainsi que toutes ses propriétés associées.

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-share-snapshots"></a>Parcourir les instantanés de partage
Vous pouvez également accéder à un instantané de partage spécifique pour afficher son contenu à l’aide de la commande [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Vous devez spécifier le nom de partage `--share-name` et l’horodatage auquel accéder (`--snapshot '2017-10-04T19:45:18.0000000Z'`).

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

#### <a name="sample-output"></a>Exemple de sortie

Comme vous pouvez le constater, le contenu de l’instantané de partage est identique au contenu du partage au moment où cet instantané de partage a été créé.

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-share-snapshots"></a>Effectuer une restauration à partir d’instantanés de partage

Vous pouvez restaurer un fichier en le copiant ou téléchargeant à partir de l’instantané de partage à l’aide de la commande `az storage file download`.

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

#### <a name="sample-output"></a>Exemple de sortie

Comme vous pouvez le constater, le contenu du fichier téléchargé et ses propriétés sont identiques aux contenu et propriétés au moment de la création de l’instantané de partage.

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

## <a name="delete-azure-files-share-snapshot"></a>Supprimer un instantané de partage de fichiers Azure

Vous pouvez supprimer des instantanés de partage de fichiers à l’aide du portail Azure, de PowerShell, de l’interface CLI, de l’API REST ou de n’importe quel SDK Stockage. Les sections suivantes vous indiquent comment supprimer des instantanés de partage à l’aide du portail Azure, de l’interface CLI et de PowerShell.

Vous pouvez parcourir les instantanés de partage et afficher les différences entre deux instantanés de partage à l’aide de n’importe quel outil de comparaison pour déterminer l’instantané de partage à supprimer. 

Vous ne pouvez pas supprimer un partage qui a des instantanés de partage. Vous devez au préalable supprimer tous ses instantanés de partage.

### <a name="delete-share-snapshot-using-portal"></a>Supprimer un instantané de partage à l’aide du portail  
Vous pouvez accéder au panneau des partages de fichier, puis sélectionner le bouton `delete` dans le portail pour supprimer un ou plusieurs instantanés de partage.

>   ![./media/storage-snapshots-delete/portal-snapshots-delete.png](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-using-azure-cli-20"></a>Supprimer un instantané de partage à l’aide d’Azure CLI 2.0
Vous pouvez supprimer un instantané de partage à l’aide de la commande `[az storage share delete]` en définissant le paramètre `--snapshot '2017-10-04T23:28:35.0000000Z' ` sur l’horodatage de l’instantané de partage :

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Exemple de sortie
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-using-powershell"></a>Supprimer un instantané de partage à l’aide de PowerShell
Vous pouvez supprimer un instantané de partage à l’aide de la commande `Remove-AzureStorageShare -Share` :

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble des instantanés](storage-snapshots-files.md)
* [Forum aux questions sur les instantanés](storage-files-faq.md)