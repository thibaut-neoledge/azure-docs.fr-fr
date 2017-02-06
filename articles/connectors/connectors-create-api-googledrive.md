---
title: "Ajouter le connecteur Google Drive à des applications logiques | Microsoft Docs"
description: "Vue d’ensemble du connecteur Google Drive avec les paramètres de l’API REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2bcebc5-02d2-435b-b0da-ef53bc51c4b6
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 497f963870d24a335379a7f015a515c425737a73


---
# <a name="get-started-with-the-google-drive-connector"></a>Prise en main du connecteur Google Drive
Connectez-vous à Google Drive pour créer des fichiers, obtenir des lignes et plus encore. Avec Google Drive, vous pouvez effectuer les opérations suivantes : 

* Créer votre flux d’activité en fonction des données que vous obtenez pendant votre recherche. 
* Utiliser des actions pour rechercher des images, des actualités et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, vous pouvez rechercher une vidéo, puis utiliser Twitter pour la publier dans un flux Twitter.

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Google Drive inclut les actions suivantes. Il n’y a aucun déclencheur. 

| Déclencheurs | Actions |
| --- | --- |
| Aucun |<ul><li>Créer un fichier</li><li>Insérer une ligne</li><li>Copier un fichier</li><li>Supprimer un fichier</li><li>Supprimer la ligne</li><li>Extraire une archive dans un dossier</li><li>Obtenir le contenu d’un fichier à l’aide de l’identifiant</li><li>Obtenir le contenu d’un fichier à l’aide du chemin</li><li>Obtenir les métadonnées d’un fichier à l’aide de l’identifiant</li><li>Obtenir les métadonnées d’un fichier à l’aide du chemin</li><li>Obtenir une ligne</li><li>Mettre à jour un fichier</li><li>Mettre à jour une ligne</li></ul> |

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## <a name="create-the-connection-to-google-drive"></a>Créer la connexion à Google Drive
Quand vous ajoutez ce connecteur à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Google Drive.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

Après avoir créé la connexion, vous entrez les propriétés Google Drive, telles que le chemin du dossier ou le nom du fichier. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

> [!TIP]
> Vous pouvez utiliser cette même connexion Google Drive dans d’autres applications logiques.
> 
> 

## <a name="swagger-rest-api-reference"></a>Informations de référence sur l'API REST Swagger
S'applique à la version 1.0.

### <a name="create-file"></a>Créer un fichier
Charge un fichier sur Google Drive.  
```POST: /datasets/default/files```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |yes |query |Aucun |Chemin du dossier Google Drive sur lequel charger le fichier |
| Nom |string |yes |query |Aucun |Nom du fichier à créer dans Google Drive |
| body |string(binary) |Oui |body |Aucun |Contenu du fichier à charger sur Google Drive |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="insert-row"></a>Insérer une ligne
Insère une ligne dans une feuille Google.  
```POST: /datasets/{dataset}/tables/{table}/items```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |Aucun |Identificateur unique du fichier Google Sheet |
| table |string |Oui |path |Aucun |Identificateur unique de la feuille de calcul |
| item |ItemInternalId: string |Oui |body |Aucun |Ligne à insérer dans la feuille spécifiée |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="copy-file"></a>Copier un fichier
Copie un fichier sur Google Drive.  
```POST: /datasets/default/copyFile```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| source |string |yes |query |(aucun) |URL du fichier source |
| destination |string |yes |query |Aucun |Chemin de destination du fichier dans Google Drive, y compris le nom de fichier cible |
| overwrite |booléenne |no |query |(aucun) |Remplace le fichier de destination si la valeur est « true » |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="delete-file"></a>Supprimer un fichier
Supprime un fichier de Google Drive.  
```DELETE: /datasets/default/files/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |Aucun |Identificateur unique du fichier à supprimer de Google Drive |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="delete-row"></a>Supprimer la ligne
Supprime une ligne d’une feuille Google.  
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |Aucun |Identificateur unique du fichier Google Sheet |
| table |string |Oui |path |Aucun |Identificateur unique de la feuille de calcul |
| id |string |Oui |path |Aucun |Identificateur unique de la ligne à supprimer |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="extract-archive-to-folder"></a>Extraire une archive dans un dossier
Extrait un fichier d’archive dans un dossier Google Drive (exemple : .zip).  
```POST: /datasets/default/extractFolderV2```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| source |string |yes |query |(aucun) |Chemin du fichier d'archive |
| destination |string |yes |query |Aucun |Chemin dans Google Drive indiquant où extraire le contenu de l’archive |
| overwrite |booléenne |no |query |(aucun) |Remplace les fichiers de destination si la valeur est « true » |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-file-content-using-id"></a>Obtenir le contenu d’un fichier à l’aide de l’identifiant
Récupère le contenu d’un fichier de Google Drive à l’aide de l’identifiant.  
```GET: /datasets/default/files/{id}/content```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |Aucun |Identificateur unique du fichier à récupérer dans Google Drive |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-file-content-using-path"></a>Obtenir le contenu d’un fichier à l’aide du chemin
Récupère le contenu d’un fichier de Google Drive à l’aide du chemin.  
```GET: /datasets/default/GetFileContentByPath```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| path |string |yes |query |Aucun |Chemin du fichier dans Google Drive |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-file-metadata-using-id"></a>Obtenir les métadonnées d’un fichier à l’aide de l’identifiant
Récupère les métadonnées d’un fichier de Google Drive à l’aide de l’identifiant.  
```GET: /datasets/default/files/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |Aucun |Identificateur unique du fichier dans Google Drive |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-file-metadata-using-path"></a>Obtenir les métadonnées d’un fichier à l’aide du chemin
Récupère les métadonnées d’un fichier de Google Drive à l’aide du chemin.  
```GET: /datasets/default/GetFileByPath```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| path |string |yes |query |Aucun |Chemin du fichier dans Google Drive |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="get-row"></a>Obtenir une ligne
Récupère une seule ligne d’une feuille Google.  
```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |Aucun |Identificateur unique du fichier Google Sheet |
| table |string |Oui |path |Aucun |Identificateur unique de la feuille de calcul |
| id |string |Oui |path |Aucun |Identificateur unique de la ligne à récupérer |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="update-file"></a>Mettre à jour un fichier
Met à jour un fichier dans Google Drive.  
```PUT: /datasets/default/files/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |Oui |path |Aucun |Identificateur unique du fichier à mettre à jour dans Google Drive |
| body |string(binary) |Oui |body |Aucun |Contenu du fichier à charger sur Google Drive |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="update-row"></a>Mettre à jour une ligne
Met à jour une ligne dans une feuille Google.  
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Oui |path |Aucun |Identificateur unique du fichier Google Sheet |
| table |string |Oui |path |Aucun |Identificateur unique de la feuille de calcul |
| id |string |Oui |path |Aucun |Identificateur unique de la ligne à mettre à jour |
| item |ItemInternalId: string |Oui |body |Aucun |Ligne avec valeurs mises à jour |

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

#### <a name="tablemetadata"></a>TableMetadata
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |no |
| title |string |no |
| x-ms-permission |string |no |
| schema |non défini |no |

#### <a name="tableslist"></a>TablesList
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |no |

#### <a name="table"></a>table
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |no |
| displayName |string |no |

#### <a name="item"></a>item
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| ItemInternalId |string |no |

#### <a name="itemslist"></a>ItemsList
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |no |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

Revenir à la [liste des API](apis-list.md).

<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png



<!--HONumber=Feb17_HO1-->


