---
title: "Aperçu de l’utilisation d’un lecteur pour un travail d’exportation Azure Import/Export - v1 | Microsoft Docs"
description: "Découvrez comment afficher un aperçu de la liste d’objets blob que vous avez sélectionnés pour un travail d’exportation dans le service Azure Import-Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 7707d744-7ec7-4de8-ac9b-93a18608dc9a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6ec74ae0b0931f3fed99a43f4f7e58f9d425b138
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="previewing-drive-usage-for-an-export-job"></a>Aperçu de l’utilisation des lecteurs pour un travail d’exportation
Avant de créer un travail d’exportation, vous devez choisir un ensemble d’objets blob à exporter. Le service Microsoft Azure Import/Export vous permet d’utiliser une liste de chemins d’accès ou de préfixes d’objets blob pour représenter les objets blob que vous avez sélectionnés.  
  
Ensuite, vous devez déterminer le nombre de lecteurs à envoyer. L’outil Import/Export offre la commande `PreviewExport` permettant d’afficher un aperçu de l’utilisation du disque pour les objets blob que vous avez sélectionnés, en fonction de la taille des disques que vous voulez utiliser.

## <a name="command-line-parameters"></a>Paramètres de ligne de commande

Vous pouvez utiliser les paramètres suivants lorsque vous utilisez la commande `PreviewExport` de l’outil Import/Export.

|Paramètre de ligne de commande|Description|  
|--------------------------|-----------------|  
|**/logdir:**<LogDirectory\>|facultatif. Répertoire contenant les journaux. Les fichiers journaux détaillés seront écrits dans ce répertoire. Si aucun répertoire de journaux n’est spécifié, le répertoire courant est utilisé comme répertoire de journaux.|  
|**/sn:**<StorageAccountName\>|Obligatoire. Nom du compte de stockage du travail d’exportation.|  
|**/sk:**<StorageAccountKey\>|Obligatoire si et seulement si aucune SAP de conteneur n’est spécifiée. Clé du compte de stockage du travail d’exportation.|  
|**/csas:**<ContainerSas\>|Obligatoire si et seulement si aucune clé du compte de stockage n’est spécifiée. SAP du conteneur pour lister les objets blob à exporter dans le travail d’exportation.|  
|**/ExportBlobListFile:**<ExportBlobListFile\>|Obligatoire. Chemin d’accès au fichier XML contenant la liste des chemins d’accès ou des préfixes de chemin d’accès aux objets blob à exporter. Format du fichier utilisé dans l’élément `BlobListBlobPath` dans l’opération [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) de l’API REST du service Import/Export.|  
|**/DriveSize:**<DriveSize\>|Obligatoire. Taille des disques à utiliser pour un travail d’exportation, *par exemple* 500 Go ou 1,5 To.|  

## <a name="command-line-example"></a>Exemple de ligne de commande

L’exemple suivant illustre la commande `PreviewExport` :  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Le fichier de liste d’objets blob à exporter peut contenir des noms et des préfixes d’objets blob, comme l’illustre ce code :  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

L’outil Azure Import/Export liste tous les objets blob à exporter et calcule leur répartition sur des lecteurs de la taille spécifiée, en prenant en compte les éventuelles surcharges nécessaires, puis évalue le nombre de disques requis pour contenir les objets blob et les informations sur l’utilisation des lecteurs.  
  
Voici un exemple de sortie, les journaux d’information étant omis :  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>Étapes suivantes

* [Référence sur l’outil Azure Import-Export](../storage-import-export-tool-how-to-v1.md)

