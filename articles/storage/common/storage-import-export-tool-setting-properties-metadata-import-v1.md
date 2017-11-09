---
title: "Définition des propriétés et des métadonnées avec Azure Import/Export - v1 | Microsoft Docs"
description: "Découvrez comment spécifier les propriétés et les métadonnées sur les objets blob de destination lors de l’exécution de l’outil Azure Import/Export pour préparer vos disques. Cela s’applique à la version v1 de l’outil d’importation/exportation."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 77bdaa5559de86cd1de9f30e70656e47fd5719e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Définition des propriétés et métadonnées pendant le processus d’importation
Lorsque vous exécutez l’outil Microsoft Azure Import/Export pour préparer vos disques, vous pouvez spécifier des propriétés et des métadonnées à définir sur les objets blob de destination. Procédez comme suit :  
  
1.  Pour définir les propriétés d’objets blob, créez un fichier texte sur votre ordinateur local qui spécifie la valeur et le nom des propriétés.  
  
2.  Pour définir les métadonnées d’objets blob, créez un fichier texte sur votre ordinateur local qui spécifie les valeurs et les noms des métadonnées.  
  
3.  Transférez le chemin d’accès complet de l’un ou des deux fichiers à l’outil Azure Import/Export dans le cadre de l’opération `PrepImport`.  
  
> [!NOTE]
>  Lorsque vous spécifiez un fichier de propriétés ou de métadonnées dans le cadre d’une session de copie, ces propriétés ou métadonnées sont définies pour chaque objet blob importé dans le cadre de cette session de copie. Si vous souhaitez spécifier un autre ensemble de propriétés ou de métadonnées pour certains des objets blob importés, vous devez créer une session de copie distincte avec des fichiers de propriétés ou de métadonnées différents.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Spécification des propriétés d’objets blob dans un fichier texte  
Pour spécifier les propriétés d’objets blob, créez un fichier texte local et incluez un fichier XML qui spécifie les noms des propriétés en tant qu’éléments et les valeurs des propriétés en tant que valeurs. Voici un exemple qui spécifie certaines valeurs de propriétés :  
  
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
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Créer une session de copie comprenant les fichiers de propriétés ou de métadonnées  
Lorsque vous exécutez l’outil Azure Import/Export pour préparer le travail d’importation, spécifiez le fichier de propriétés en ligne de commande à l’aide du paramètre `PropertyFile`. Spécifiez le fichier de métadonnées en ligne de commande à l’aide du paramètre`/MetadataFile`. Voici un exemple qui spécifie les deux fichiers :  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Étapes suivantes

* [Format de fichier de propriétés et de métadonnées du service d’importation / exportation](../storage-import-export-file-format-metadata-and-properties.md)
