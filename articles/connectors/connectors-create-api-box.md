---
title: "Ajouter le connecteur Box à vos applications logiques | Microsoft Docs"
description: "Vue d’ensemble du connecteur Box avec les paramètres d’API REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 949579cf-a81c-4790-9ef5-fe39b4fbd0c5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: bb6bef230f4ba4f7019fd2f120c2115d97649b81


---
# <a name="get-started-with-the-box-connector"></a>Prise en main du connecteur Box
Connectez-vous à Box pour créer des fichiers, supprimer des fichiers et bien plus encore. 

> [!NOTE]
> Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.
> 
> 

Avec Box, vous pouvez :

* Créer votre flux d'activité en fonction des données que vous obtenez de Box. 
* Utiliser des déclencheurs quand un fichier est créé ou mis à jour.
* Utiliser des actions pour copier un fichier, supprimer un fichier et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d'autres actions. Par exemple, lorsqu'un fichier est modifié dans Box, vous pouvez le sélectionner et l'envoyer par courrier électronique à l'aide d'Office 365.

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Box inclut les déclencheurs et les actions suivants.

| Déclencheurs | Actions |
| --- | --- |
| <ul><li>Quand un fichier est créé</li><li>Quand un fichier est modifié</li></ul> |<ul><li>Créer un fichier</li><li>Quand un fichier est créé</li><li>Copier un fichier</li><li>Supprimer un fichier</li><li>Extraire une archive dans un dossier</li><li>Obtenir le contenu d’un fichier à l’aide de l’identifiant</li><li>Obtenir le contenu d’un fichier à l’aide du chemin</li><li>Obtenir les métadonnées d’un fichier à l’aide de l’identifiant</li><li>Obtenir les métadonnées d’un fichier à l’aide du chemin</li><li>Mettre à jour un fichier</li><li>Quand un fichier est modifié</li></ul> |

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## <a name="create-a-connection-to-box"></a>Créer une connexion à Box
Quand vous ajoutez ce connecteur à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Box.

> [!INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]
> 
> 

Après avoir créé la connexion, vous entrez les propriétés de Box. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

> [!TIP]
> Vous pouvez utiliser cette même connexion Box dans d'autres applications logiques.
> 
> 

## <a name="swagger-rest-api-reference"></a>Informations de référence sur l'API REST Swagger
S'applique à la version 1.0.

### <a name="create-file"></a>Créer un fichier
Charge un fichier sur Box.  
```POST: /datasets/default/files```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |Oui |query |Aucun |Chemin du dossier Box sur lequel charger le fichier |
| Nom |string |Oui |query |Aucun |Nom du fichier à créer dans Box |
| body |string(binary) |Oui |body |Aucun |Contenu du fichier à charger sur Box |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="when-a-file-is-created"></a>Quand un fichier est créé
Déclenche un flux quand un fichier est créé dans un dossier Box.  
```GET: /datasets/default/triggers/onnewfile```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| folderId |string |Oui |query |Aucun |Identificateur unique du dossier dans Box |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="copy-file"></a>Copier un fichier
Copie un fichier vers Box.  
```POST: /datasets/default/copyFile```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| source |string |Oui |query |Aucun |URL du fichier source |
| destination |string |Oui |query |Aucun |Chemin de destination du fichier dans Box, y compris le nom de fichier cible |
| overwrite |booléenne |Non |query |Aucun |Remplace le fichier de destination si la valeur est « true » |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="delete-file"></a>Supprimer un fichier
Supprime un fichier de Box.  
```DELETE: /datasets/default/files/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |Aucun |Identificateur unique du fichier à supprimer de Box |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="extract-archive-to-folder"></a>Extraire une archive dans un dossier
Extrait un fichier d’archive dans un dossier de Box (exemple : .zip).  
```POST: /datasets/default/extractFolderV2```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| source |string |Oui |query | |Chemin du fichier d'archive |
| destination |string |Oui |query | |Chemin dans Box indiquant où extraire le contenu de l'archive |
| overwrite |booléenne |Non |query | |Remplace les fichiers de destination si la valeur est « true » |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-file-content-using-id"></a>Obtenir le contenu d’un fichier à l'aide de l’identifiant
Récupère le contenu d’un fichier de Box à partir de son identifiant.  
```GET: /datasets/default/files/{id}/content```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |Aucun |Identificateur unique du fichier dans Box |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-file-content-using-path"></a>Obtenir le contenu d’un fichier à l'aide du chemin
Récupère le contenu d’un fichier de Box à l’aide du chemin.  
```GET: /datasets/default/GetFileContentByPath```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| path |string |Oui |query |Aucun |Chemin unique du fichier dans Box |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-file-metadata-using-id"></a>Obtenir les métadonnées d’un fichier à l'aide de l’identifiant
Récupère les métadonnées d’un fichier de Box à l'aide de son identifiant.  
```GET: /datasets/default/files/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |Aucun |Identificateur unique du fichier dans Box |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-file-metadata-using-path"></a>Obtenir les métadonnées d’un fichier à l'aide du chemin
Récupère les métadonnées d’un fichier de Box à l'aide du chemin.  
```GET: /datasets/default/GetFileByPath```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| path |string |Oui |query |Aucun |Chemin unique du fichier dans Box |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="update-file"></a>Mettre à jour un fichier
Met à jour un fichier dans Box.  
```PUT: /datasets/default/files/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |Aucun |Identificateur unique du fichier à mettre à jour dans Box |
| body |string(binary) |Oui |body |Aucun |Contenu du fichier à mettre à jour dans Box |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="when-a-file-is-modified"></a>Quand un fichier est modifié
Déclenche un flux quand un fichier est modifié dans un dossier Box.  
```GET: /datasets/default/triggers/onupdatedfile```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| folderId |string |Oui |query |Aucun |Identificateur unique du dossier dans Box |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d'objet
#### <a name="datasetsmetadata"></a>DataSetsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| tabular |non défini |no |
| objet blob |non défini |no |

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| source |string |no |
| displayName |string |no |
| urlEncoding |string |no |
| tableDisplayName |string |no |
| tablePluralName |string |no |

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| source |string |no |
| displayName |string |no |
| urlEncoding |string |no |

#### <a name="blobmetadata"></a>BlobMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |no |
| Nom |string |no |
| displayName |string |no |
| path |string |no |
| LastModified |string |no |
| Taille |integer |no |
| MediaType |string |no |
| IsFolder |booléenne |no |
| ETag |string |no |
| FileLocator |string |no |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).




<!--HONumber=Feb17_HO1-->


