---
title: "Exemple de workflow pour préparer des disques durs pour un travail d’importation Azure Import/Export | Microsoft Docs"
description: "Obtenez la procédure pas à pas relative au processus de préparation des disques à un travail d’importation dans le service Azure Import/Export."
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
ms.date: 04/07/2017
ms.author: muralikk
ms.openlocfilehash: 60139ff36b66432620591ceaf201e046ad30217f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Exemple de workflow pour préparer des disques durs à un travail d’importation

Cet article vous guide tout au long du processus de préparation des disques pour un travail d’importation.

## <a name="sample-data"></a>Exemples de données

Cet exemple importe les données suivantes dans un compte de stockage Azure nommé `mystorageaccount` :

|Lieu|Description|Taille des données|
|--------------|-----------------|-----|
|H:\Video\ |Collection de vidéos|12 To|
|H:\Photo\ |Collection de photos|30 Go|
|K:\Temp\FavoriteMovie.ISO|Image de disque Blu-ray™|25 Go|
|\\\bigshare\john\music\ |Collection de fichiers de musique sur un partage réseau|10 Go|

## <a name="storage-account-destinations"></a>Destinations dans le compte de stockage

Le travail d’importation importe les données dans les destinations suivantes dans le compte de stockage :

|Source|Répertoire virtuel ou objet blob de destination|
|------------|-------------------------------------------|
|H:\Video\ |video/|
|H:\Photo\ |photo/|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |music|

Avec ce mappage, le fichier `H:\Video\Drama\GreatMovie.mov` sera importé dans l’objet blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Déterminer la configuration requise du disque dur

Ensuite, pour déterminer le nombre de disques durs nécessaires, calculez la taille des données :

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Pour cet exemple, deux disques durs de 8 To devraient suffire. Cependant, puisque le répertoire source `H:\Video` contient 12 To de données et que la capacité de votre seul disque dur est de 8 To seulement, vous pouvez le spécifier comme suit dans le fichier **driveset.csv** :

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
L’outil distribue les données sur deux disques durs de manière optimisée.

## <a name="attach-drives-and-configure-the-job"></a>Attacher des disques et configurer le travail
Vous allez attacher les deux disques à l’ordinateur et créer des volumes. Créez ensuite le fichier **dataset.csv** :
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

En outre, vous pouvez définir les métadonnées suivantes pour tous les fichiers :

* **UploadMethod :** Service Windows Azure Import/Export
* **DataSetName :** SampleData
* **CreationDate :** 10/1/2013

Pour définir les métadonnées pour les fichiers importés, créez un fichier texte, `c:\WAImportExport\SampleMetadata.txt`, avec le contenu suivant :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Vous pouvez également définir des propriétés pour l’objet blob `FavoriteMovie.ISO` :

* **Content-Type :** application/octet-stream
* **Content-MD5 :** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control :** no-cache

Pour définir ces propriétés, créez un fichier texte, `c:\WAImportExport\SampleProperties.txt` :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Exécuter l’outil Azure Import/Export (WAImportExport.exe)

Vous êtes maintenant prêt à exécuter l’outil Azure Import/Export pour préparer les deux disques durs.

**Pour la première session :**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Si des données supplémentaires doivent être ajoutées, créez un autre fichier de jeu de données (format identique à Initialdataset).

**Pour la deuxième session :**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Une fois les sessions de copie terminées, vous pouvez déconnecter les deux disques de l’ordinateur de copie et les expédier au centre de données Azure approprié. Vous allez télécharger les deux fichiers journaux, `<FirstDriveSerialNumber>.xml` et `<SecondDriveSerialNumber>.xml`, lors de la création du travail d’importation dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

* [Préparation des disques durs pour un travail d’importation](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Référence rapide pour les commandes fréquemment utilisées](../storage-import-export-tool-quick-reference.md)
