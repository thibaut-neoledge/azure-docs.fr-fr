---
title: Format de fichier de manifeste du service Azure Import/Export | Microsoft Docs
description: "Découvrez le format du fichier de manifeste de disque qui décrit le mappage entre les objets blob dans le stockage Blob Azure et le fichier sur le disque dans un travail d’importation ou d’exportation dans le service d’importation/exportation"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f3119e1c-2c25-48ad-8752-a6ed4adadbb0
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: 2c76120a967aabf546fdb5246478f78e8cf47f94
ms.lasthandoff: 02/16/2017


---

# <a name="azure-importexport-service-manifest-file-format"></a>Format de fichier de manifeste du service Azure Import/Export
Le fichier de manifeste de disque décrit le mappage entre les objets blob dans le stockage Blob Azure et les fichiers sur le disque comprenant un travail d’importation ou d’exportation. Pour une opération d’importation, le fichier de manifeste est créé dans le cadre du processus de préparation du disque et est stocké sur le disque avant d’envoyer le disque vers le centre de données Azure. Pendant une opération d’exportation, le manifeste est créé et stocké sur le disque par le service d’importation/exportation Azure.  
  
Pour les travaux d’importation et d’exportation, le fichier de manifeste de disque est stocké sur le disque d’importation ou d’exportation ; il n’est pas transmis au service via une opération d’API.  
  
Vous trouverez ci-dessous une description du format général d’un fichier de manifeste de disque :  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  
```

Les éléments et attributs de données du format XML de manifeste de disque sont spécifiés dans le tableau suivant.  
  
|Élément XML|Type|Description|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Élément racine|Élément racine du fichier de manifeste. Tous les autres éléments du fichier se trouvent sous cet élément.|  
|`Version`|Attribut, Chaîne|Version du fichier de manifeste.|  
|`Drive`|Élément XML imbriqué|Contient le manifeste de chaque disque.|  
|`DriveId`|String|Identificateur de disque unique pour le disque. L’identificateur de disque peut être trouvé en recherchant le numéro de série du disque. Le numéro de série du disque est également généralement imprimé à l’extérieur du disque. L’élément `DriveID` doit apparaître avant tout élément `BlobList` dans le fichier de manifeste.|  
|`StorageAccountKey`|String|Obligatoire pour les travaux d’importation si et seulement si `ContainerSas` n’est pas spécifié. Clé du compte de stockage Azure associé au travail.<br /><br /> Cet élément est omis du manifeste pour une opération d’exportation.|  
|`ContainerSas`|String|Obligatoire pour les travaux d’importation si et seulement si `StorageAccountKey` n’est pas spécifié. SAP de conteneur pour accéder aux objets blob associés au travail. Consultez [Travail Put](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) pour connaître son format. Cet élément est omis du manifeste pour une opération d’exportation..|  
|`ClientCreator`|String|Spécifie le client qui a créé le fichier XML. Cette valeur n’est pas interprétée par le service d’importation/exportation.|  
|`BlobList`|Élément XML imbriqué|Contient une liste d’objets blob qui font partie du travail d’importation ou d’exportation. Chaque objet blob d’une liste d’objets blob partage les mêmes métadonnées et propriétés.|  
|`BlobList/MetadataPath`|String|facultatif. Spécifie le chemin d’accès relatif d’un fichier sur le disque qui contient les métadonnées par défaut définies sur les objets blob dans la liste d’objets blob pour une opération d’importation. Ces métadonnées peuvent éventuellement être remplacées, objet blob par objet blob.<br /><br /> Cet élément est omis du manifeste pour une opération d’exportation.|  
|`BlobList/MetadataPath/@Hash`|Attribut, Chaîne|Spécifie la valeur de hachage MD5 encodée en Base16 pour le fichier de métadonnées.|  
|`BlobList/PropertiesPath`|String|facultatif. Spécifie le chemin d’accès relatif d’un fichier sur le disque qui contient les propriétés par défaut définies sur les objets blob dans la liste d’objets blob pour une opération d’importation. Ces propriétés peuvent éventuellement être remplacées, objet blob par objet blob.<br /><br /> Cet élément est omis du manifeste pour une opération d’exportation.|  
|`BlobList/PropertiesPath/@Hash`|Attribut, Chaîne|Spécifie la valeur de hachage MD5 encodée en Base16 pour le fichier de propriétés.|  
|`Blob`|Élément XML imbriqué|Contient des informations sur chaque objet blob de chaque liste d’objets blob.|  
|`Blob/BlobPath`|String|URI relatif de l’objet blob commençant par le nom du conteneur. Si l’objet blob se trouve dans le conteneur racine, il doit commencer par `$root`.|  
|`Blob/FilePath`|String|Spécifie le chemin d’accès relatif au fichier sur le disque. Pour les travaux d’exportation, dans la mesure du possible, le chemin d’accès à l’objet blob sert de chemin d’accès au fichier ; *par exemple,*, `pictures/bob/wild/desert.jpg` est exporté vers `\pictures\bob\wild\desert.jpg`. Toutefois, en raison des limitations des noms NTFS, un objet blob peut être exporté dans un fichier avec un chemin d’accès différent du chemin d’accès à l’objet blob.|  
|`Blob/ClientData`|String|facultatif. Contient des commentaires du client. Cette valeur n’est pas interprétée par le service d’importation/exportation.|  
|`Blob/Snapshot`|DateTime|Facultatif pour les travaux d’exportation. Spécifie l’identificateur d’instantané pour un instantané d’objet blob exporté.|  
|`Blob/Length`|Entier |Spécifie la longueur totale de l’objet blob en octets. La valeur peut atteindre jusqu’à 200 Go pour un objet blob de blocs et jusqu’à 1 To pour un objet blob de pages. Pour un objet blob de pages, cette valeur doit être un multiple de 512.|  
|`Blob/ImportDisposition`|String|Facultatif pour les travaux d’importation, omis pour les travaux d’exportation. Spécifie comment le service d’importation/exportation doit gérer le cas d’un travail d’importation dans lequel un objet blob portant le même nom existe déjà. Si cette valeur est omise dans le manifeste d’importation, la valeur par défaut est `rename`.<br /><br /> Les valeurs de cet élément incluent les suivantes :<br /><br /> -   `no-overwrite` : si un objet blob de destination portant le même nom existe déjà, l’opération d’importation ignore l’importation de ce fichier.<br />-   `overwrite` : un objet blob de destination existant est remplacé par le nouveau fichier importé.<br />-   `rename` : le nouvel objet blob est téléchargé sous un autre nom.<br /><br /> La règle de changement de nom est la suivante :<br /><br /> -   Si le nom de l’objet blob ne contient pas de point, un nouveau nom est généré en ajoutant `(2)` au nom de l’objet blob d’origine ; si ce nom est en conflit avec un nom d’objet blob existant, `(3)` est alors ajouté à la place de `(2)`, et ainsi de suite.<br />-   Si le nom de l’objet blob contient un point, la partie suivant le dernier point est considérée comme le nom d’extension. Comme dans la procédure ci-dessus, `(2)` est inséré avant le dernier point pour générer un nouveau nom ; si le nouveau nom est toujours en conflit avec un nom d’objet blob existant, le service essaie alors `(3)`, `(4)`, et ainsi de suite, jusqu’à ce qu’un nom non conflictuel soit trouvé.<br /><br /> Voici quelques exemples :<br /><br /> L’objet blob `BlobNameWithoutDot` est renommé en :<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> L’objet blob `Seattle.jpg` est renommé en :<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Élément XML imbriqué|Obligatoire pour un objet blob de pages.<br /><br /> Pour une opération d’importation, spécifie une liste de plages d’octets d’un fichier à importer. Chaque plage de pages est décrite par un décalage et la longueur dans le fichier source qui décrit la plage de pages, ainsi que par un hachage MD5 de la région. L’attribut `Hash` d’une plage de pages est obligatoire. Le service vérifie que le hachage des données dans l’objet blob correspond au hachage MD5 calculé à partir de la plage de pages. N’importe quel nombre de plages de pages peut être utilisé pour décrire un fichier pour une importation, avec une taille totale maximale de 1 To. Toutes les plages de pages doivent être triées par décalage et aucun chevauchement n’est autorisé.<br /><br /> Pour une opération d’exportation, spécifie un ensemble de plages d’octets d’un objet blob exporté sur le disque.<br /><br /> Les plages de pages combinées ne peuvent couvrir que des sous-plages d’un objet blob ou d’un fichier.  Le reste du fichier n’est pas couvert par une plage de pages et son contenu peut être indéfini.|  
|`PageRange`|Élément XML|Représente une plage de pages.|  
|`PageRange/@Offset`|Attribut, Entier|Spécifie le décalage dans le fichier de transfert et l’objet blob où la plage de pages spécifiée commence. Cette valeur doit être un multiple de 512.|  
|`PageRange/@Length`|Attribut, Entier|Spécifie la longueur de la plage de pages. Cette valeur doit être un multiple de 512 et inférieure à 4 Mo.|  
|`PageRange/@Hash`|Attribut, Chaîne|Spécifie la valeur de hachage MD5 encodée en Base16 pour la plage de pages.|  
|`BlockList`|Élément XML imbriqué|Obligatoire pour un objet blob de blocs avec des blocs nommés.<br /><br /> Pour une opération d’importation, la liste de blocs spécifie un ensemble de blocs qui seront importés dans le stockage Azure. Pour une opération d’exportation, la liste de blocs spécifie où chaque bloc a été stocké dans le fichier sur le disque d’exportation. Chaque bloc est décrit par un décalage dans le fichier et une longueur de bloc ; chaque bloc est en outre nommé par un attribut d’ID de bloc et contient un hachage MD5 pour le bloc. Jusqu’à 50 000 blocs peut être utilisés pour décrire un objet blob.  Tous les blocs doivent être classées par décalage et doivent couvrir ensemble la plage complète du fichier, *c'est-à-dire* qu’il ne doit y avoir aucun écart entre les blocs. Si la taille de l’objet blob est inférieure à 64 Mo, les ID de chaque bloc doivent être tous absents ou tous présents. Les ID de bloc doivent être des chaînes encodées en Base64. Consultez [Put Block](/rest/api/storageservices/fileservices/put-block) pour plus d’informations sur les conditions requises pour les ID de bloc.|  
|`Block`|Élément XML|Représente un bloc.|  
|`Block/@Offset`|Attribut, Entier|Spécifie le décalage où le bloc spécifié commence.|  
|`Block/@Length`|Attribut, Entier|Spécifie le nombre d’octets dans le bloc ; cette valeur doit être inférieure à 4 Mo.|  
|`Block/@Id`|Attribut, Chaîne|Spécifie une chaîne représentant l’ID du bloc.|  
|`Block/@Hash`|Attribut, Chaîne|Spécifie le hachage MD5 encodé en Base16 du bloc.|  
|`Blob/MetadataPath`|String|facultatif. Spécifie le chemin d’accès relatif à un fichier de métadonnées. Pendant une importation, les métadonnées sont définies sur l’objet blob de destination. Pendant une opération d’exportation, les métadonnées de l’objet blob sont stockées dans le fichier de métadonnées sur le disque.|  
|`Blob/MetadataPath/@Hash`|Attribut, Chaîne|Spécifie le hachage MD5 encodé en Base16 du fichier de métadonnées de l’objet blob.|  
|`Blob/PropertiesPath`|String|facultatif. Spécifie le chemin d’accès relatif à un fichier de propriétés. Pendant une importation, les propriétés sont définies sur l’objet blob de destination. Pendant une opération d’exportation, les propriétés de l’objet blob sont stockées dans le fichier de propriétés sur le disque.|  
|`Blob/PropertiesPath/@Hash`|Attribut, Chaîne|Spécifie le hachage MD5 encodé en Base16 du fichier de propriétés de l’objet blob.|  
  
## <a name="see-also"></a>Voir aussi  
[API REST d’importation/exportation de stockage](/rest/api/storageimportexport/)

