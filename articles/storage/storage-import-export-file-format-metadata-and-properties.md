---
title: "Format de fichier de propriétés et de métadonnées d’Azure Import/Export | Microsoft Docs"
description: "Découvrez comment spécifier les métadonnées et les propriétés d’un ou plusieurs objets blob qui font partie d’un travail d’importation ou d’exportation."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: 0cac5419d4b481a5b783b4b9fbd4ad2d4d637042
ms.lasthandoff: 02/16/2017


---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Format de fichier de propriétés et de métadonnées du service Azure Import/Export
Vous pouvez spécifier les métadonnées et les propriétés d’un ou plusieurs objets blob dans le cadre d’un travail d’importation ou d’exportation. Pour définir les métadonnées ou les propriétés d’objets blob créés dans le cadre d’un travail d’importation, vous devez fournir un fichier de métadonnées ou de propriétés sur le disque dur contenant les données à importer. Pour un travail d’exportation, les métadonnées et les propriétés sont écrites dans un fichier de métadonnées ou de propriétés inclus sur le disque dur retourné.  
  
## <a name="metadata-file-format"></a>Format du fichier de métadonnées  
Le format d’un fichier de métadonnées est le suivant :  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|Élément XML|Type|Description|  
|-----------------|----------|-----------------|  
|`Metadata`|Élément racine|Élément racine du fichier de métadonnées.|  
|`metadata-name`|String|facultatif. L’élément XML spécifie le nom des métadonnées de l’objet blob, et sa valeur spécifie la valeur du paramètre des métadonnées.|  
  
## <a name="properties-file-format"></a>Format du fichier de propriétés  
Le format d’un fichier de propriétés est le suivant :  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|Élément XML|Type|Description|  
|-----------------|----------|-----------------|  
|`Properties`|Élément racine|Élément racine du fichier de propriétés.|  
|`Last-Modified`|String|facultatif. Heure de dernière modification de l’objet blob. Travaux d’exportation uniquement.|  
|`Etag`|String|facultatif. Valeur ETag de l’objet blob. Travaux d’exportation uniquement.|  
|`Content-Length`|String|facultatif. Taille de l’objet blob en octets. Travaux d’exportation uniquement.|  
|`Content-Type`|String|facultatif. Type de contenu de l’objet blob.|  
|`Content-MD5`|String|facultatif. Hash MD5 de l’objet blob.|  
|`Content-Encoding`|String|facultatif. Codage du contenu de l’objet blob.|  
|`Content-Language`|String|facultatif. Langage du contenu de l’objet blob.|  
|`Cache-Control`|String|facultatif. Chaîne de contrôle du cache de l’objet blob.|  
  
 Consultez les pages [Définir les propriétés d’objets blob](/rest/api/storageservices/fileservices/set-blob-properties), [Définir les métadonnées d’objets blob](/rest/api/storageservices/fileservices/set-blob-metadata) et [Définir et extraire les propriétés et métadonnées de ressources blob](/rest/api/storageservices/fileservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) pour obtenir des instructions détaillées sur la définition de propriétés et de métadonnées d’objets blob.

