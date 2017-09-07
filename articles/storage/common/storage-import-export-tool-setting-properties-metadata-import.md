---
title: "Définition des propriétés et des métadonnées avec Azure Import/Export | Microsoft Docs |"
description: "Découvrez comment spécifier les propriétés et les métadonnées sur les objets blob de destination lors de l’exécution de l’outil Azure Import/Export pour préparer vos disques."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 1ba6d157402fae0c7d7bf841d2b4e4f6b1ee1084
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="setting-properties-and-metadata-during-the-import-process"></a>Définition des propriétés et métadonnées pendant le processus d’importation

Lorsque vous exécutez l’outil Microsoft Azure Import/Export pour préparer vos disques, vous pouvez spécifier des propriétés et des métadonnées à définir sur les objets blob de destination. Procédez comme suit :

1.  Pour définir les propriétés d’objets blob, créez un fichier texte sur votre ordinateur local qui spécifie la valeur et le nom des propriétés.
2.  Pour définir les métadonnées d’objets blob, créez un fichier texte sur votre ordinateur local qui spécifie les valeurs et les noms des métadonnées.
3.  Transférez le chemin d’accès complet de l’un ou des deux fichiers à l’outil Azure Import/Export dans le cadre de l’opération `PrepImport`.

> [!NOTE]
>  Lorsque vous spécifiez un fichier de propriétés ou de métadonnées dans le cadre d’une session de copie, ces propriétés ou métadonnées sont définies pour chaque objet blob importé dans le cadre de cette session de copie. Si vous souhaitez spécifier un autre ensemble de propriétés ou de métadonnées pour certains des objets blob importés, vous devez créer une session de copie distincte avec des fichiers de propriétés ou de métadonnées différents.

## <a name="specify-blob-properties-in-a-text-file"></a>Spécification des propriétés d’objets blob dans un fichier texte

Pour spécifier les propriétés d’objets blob, créez un fichier texte local et incluez un fichier XML qui spécifie les noms de propriétés en tant qu’éléments et les valeurs des propriétés en tant que valeurs. Voici un exemple qui spécifie certaines valeurs de propriétés :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

Enregistrez le fichier dans un emplacement local comme `C:\WAImportExport\ImportProperties.txt`.

## <a name="specify-blob-metadata-in-a-text-file"></a>Spécification des métadonnées d’objets blob dans un fichier texte

De même, pour spécifier les métadonnées d’objets blob, créez un fichier texte local qui spécifie les noms des métadonnées en tant qu’éléments et les valeurs des métadonnées en tant que valeurs. Voici un exemple qui spécifie certaines valeurs de métadonnées :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Enregistrez le fichier dans un emplacement local comme `C:\WAImportExport\ImportMetadata.txt`.

## <a name="add-the-path-to-properties-and-metadata-files-in-datasetcsv"></a>Ajoutez le chemin d’accès aux fichiers de propriétés et de métadonnées dans dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="next-steps"></a>Étapes suivantes

* [Format de fichier de propriétés et de métadonnées du service d’importation / exportation](../storage-import-export-file-format-metadata-and-properties.md)

