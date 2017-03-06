---
title: OneDrive Entreprise | Microsoft Docs
description: "Créez des applications logiques avec Azure App Service. Connectez-vous à OneDrive Entreprise pour gérer vos fichiers. Vous pouvez effectuer diverses actions, comme charger, mettre à jour, obtenir et supprimer des fichiers."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cf9484e9-7a20-4de0-93c8-0fa132221f2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 992b4b7bab8878355cbe936b75768e67b086286e
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>Prise en main du connecteur OneDrive Entreprise
Connectez-vous à OneDrive Entreprise pour gérer vos fichiers. Vous pouvez effectuer diverses actions, comme charger, mettre à jour, obtenir et supprimer des fichiers.

> [!NOTE]
> Cette version de l’article s’applique à la version de schéma 2015-08-01-preview des applications logiques.
> 
> 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur OneDrive Entreprise peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur OneDrive Entreprise met à votre disposition les actions et/ou les déclencheurs ci-après.

### <a name="onedrive-for-business-actions"></a>Actions de OneDrive Entreprise
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| [GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata) |Récupère les métadonnées d’un fichier dans OneDrive Entreprise à l’aide de l’identifiant |
| [UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile) |Met à jour un fichier dans OneDrive Entreprise |
| [DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile) |Supprime un fichier de OneDrive Entreprise |
| [GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath) |Récupère les métadonnées d’un fichier dans OneDrive Entreprise à l’aide du chemin d’accès |
| [GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath) |Récupère le contenu d’un fichier dans OneDrive Entreprise à l’aide du chemin d’accès |
| [GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent) |Récupère le contenu d’un fichier dans OneDrive Entreprise à l’aide de l’identifiant |
| [CreateFile](connectors-create-api-onedriveforbusiness.md#createfile) |Charge un fichier sur OneDrive Entreprise |
| [CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile) |Copie un fichier dans OneDrive Entreprise |
| [ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder) |Répertorie les fichiers dans un dossier OneDrive Entreprise |
| [ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder) |Répertorie les fichiers dans le dossier racine OneDrive Entreprise |
| [ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2) |Extrait un dossier dans OneDrive Entreprise |

### <a name="onedrive-for-business-triggers"></a>Déclencheurs de OneDrive Entreprise
Vous pouvez écouter les événements suivants :

| Déclencheur | Description |
| --- | --- |
| Quand un fichier est créé |Déclenche un flux quand un fichier est créé dans un dossier OneDrive Entreprise |
| Quand un fichier est modifié |Déclenche un flux quand un fichier est modifié dans un dossier OneDrive Entreprise |

## <a name="create-a-connection-to-onedrive-for-business"></a>Créer une connexion à OneDrive Entreprise
Pour créer des applications logiques avec OneDrive Entreprise, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

| Propriété | Requis | Description |
| --- | --- | --- |
| Jeton |Oui |Fournir des informations d’identification pour OneDrive Entreprise |

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 
> [!TIP]
> Vous pouvez utiliser cette connexion dans d'autres applications logiques.
> 
> 

## <a name="reference-for-onedrive-for-business"></a>Référence pour OneDrive Entreprise
S’applique à la version 1.0.

## <a name="getfilemetadata"></a>GetFileMetadata
Obtenir les métadonnées d’un fichier à l’aide de l’identifiant : récupère les métadonnées d’un fichier dans OneDrive Entreprise à l’aide de l’identifiant

```GET: /datasets/default/files/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |(aucun) |Spécifier le fichier |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="updatefile"></a>UpdateFile
Mettre à jour le fichier : met à jour un fichier dans OneDrive Entreprise

```PUT: /datasets/default/files/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |(aucun) |Spécifier le fichier à mettre à jour |
| body | |Oui |body |(aucun) |Contenu du fichier à mettre à jour dans OneDrive Entreprise |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="deletefile"></a>DeleteFile
Supprimer un fichier : supprime un fichier de OneDrive Entreprise

```DELETE: /datasets/default/files/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |(aucun) |Spécifier le fichier à supprimer |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="getfilemetadatabypath"></a>GetFileMetadataByPath
Obtenir les métadonnées d’un fichier à l’aide du chemin : récupère les métadonnées d’un fichier dans OneDrive Entreprise à l’aide du chemin

```GET: /datasets/default/GetFileByPath```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| path |string |yes |query |(aucun) |Chemin unique du fichier dans OneDrive Entreprise |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="getfilecontentbypath"></a>GetFileContentByPath
Obtenir le contenu d’un fichier à l’aide du chemin : récupère le contenu d’un fichier dans OneDrive Entreprise à l’aide du chemin

```GET: /datasets/default/GetFileContentByPath```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| path |string |yes |query |(aucun) |Chemin unique du fichier dans OneDrive Entreprise |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="getfilecontent"></a>GetFileContent
Obtenir le contenu d’un fichier à l’aide de l’identifiant : récupère le contenu d’un fichier dans OneDrive Entreprise à l’aide de l’identifiant

```GET: /datasets/default/files/{id}/content```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |(aucun) |Spécifier le fichier |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="createfile"></a>CreateFile
Créer un fichier : charge un fichier sur OneDrive Entreprise

```POST: /datasets/default/files```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |yes |query |(aucun) |Chemin du dossier OneDrive Entreprise sur lequel charger le fichier |
| name |string |yes |query |(aucun) |Nom du fichier à créer dans OneDrive Entreprise |
| body | |Oui |body |(aucun) |Contenu du fichier à charger sur OneDrive Entreprise |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="copyfile"></a>CopyFile
Copier un fichier : copie un fichier dans OneDrive Entreprise

```POST: /datasets/default/copyFile```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| source |string |yes |query |(aucun) |URL du fichier source |
| destination |string |yes |query |(aucun) |Chemin de destination du fichier dans OneDrive Entreprise, y compris le nom de fichier cible |
| overwrite |booléenne |no |query |false |Remplace le fichier de destination si la valeur est « true » |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="onnewfile"></a>OnNewFile
Quand un fichier est créé : déclenche un flux quand un fichier est créé dans un dossier OneDrive Entreprise

```GET: /datasets/default/triggers/onnewfile```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| folderId |string |yes |query |(aucun) |Spécifier un dossier |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="onupdatedfile"></a>OnUpdatedFile
Quand un fichier est modifié : déclenche un flux quand un fichier est modifié dans un dossier OneDrive Entreprise

```GET: /datasets/default/triggers/onupdatedfile```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| folderId |string |yes |query |(aucun) |Spécifier un dossier |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="listfolder"></a>ListFolder
Répertorier les fichiers dans un dossier : répertorie les fichiers dans un dossier OneDrive Entreprise

```GET: /datasets/default/folders/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |(aucun) |Spécifier le dossier |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="listrootfolder"></a>ListRootFolder
Répertorier le dossier racine : répertorie les fichiers dans le dossier racine OneDrive Entreprise

```GET: /datasets/default/folders```

Il n’existe aucun paramètre pour cet appel

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="extractfolderv2"></a>ExtractFolderV2
Extraire un dossier : extrait un dossier dans OneDrive Entreprise

```POST: /datasets/default/extractFolderV2```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| source |string |yes |query |(aucun) |Chemin du fichier d'archive |
| destination |string |yes |query |(aucun) |Chemin dans OneDrive Entreprise indiquant où extraire le contenu de l’archive |
| overwrite |booléenne |no |query |false |Remplace les fichiers de destination si la valeur est « true » |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d'objet
### <a name="datasetsmetadata"></a>DataSetsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| tabular |non défini |Non |
| objet blob |non défini |Non |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| source |string |Non |
| displayName |string |Non |
| urlEncoding |string |Non |
| tableDisplayName |string |Non |
| tablePluralName |string |Non |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| source |string |Non |
| displayName |string |Non |
| urlEncoding |string |Non |

### <a name="blobmetadata"></a>BlobMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Non |
| Nom |string |Non |
| DisplayName |string |Non |
| Chemin |string |Non |
| LastModified |string |Non |
| Taille |integer |Non |
| MediaType |string |Non |
| IsFolder |booléenne |Non |
| ETag |string |Non |
| FileLocator |string |Non |

### <a name="object"></a>Object
## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)


