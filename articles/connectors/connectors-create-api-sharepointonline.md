---
title: "Découvrez comment utiliser le connecteur SharePoint Online dans les applications logiques | Microsoft Docs"
description: "Créez des applications logiques avec le connecteur SharePoint Online pour gérer des listes sur SharePoint."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 3ecf3e30fe2fcb9d6473d7eda450536cddfa97f4


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>Prise en main du connecteur SharePoint Online
Utilisez le connecteur SharePoint Online pour gérer des listes SharePoint.  

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sharepoint-online"></a>Se connecter à SharePoint Online
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une [connexion](connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service.  

### <a name="create-a-connection-to-sharepoint-online"></a>Créer une connexion à SharePoint Online
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]
> 
> 

## <a name="use-a-sharepoint-online-trigger"></a>Utiliser un déclencheur SharePoint Online
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]
> 
> 

## <a name="use-a-sharepoint-online-action"></a>Utiliser une action SharePoint Online
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]
> 
> 

## <a name="technical-details"></a>Détails techniques
Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion :

## <a name="sharepoint-online-triggers"></a>Déclencheurs SharePoint Online
SharePoint comporte les déclencheurs suivants :  

| Déclencheur | Description |
| --- | --- |
| [Quand un fichier est créé](connectors-create-api-sharepointonline.md#when-a-file-is-created) |Cette opération déclenche un flux lorsqu’un fichier est créé dans un dossier SharePoint. |
| [Quand un fichier est modifié](connectors-create-api-sharepointonline.md#when-a-file-is-modified) |Cette opération déclenche un flux lorsqu’un fichier est modifié dans un dossier SharePoint. |
| [Quand un élément est créé](connectors-create-api-sharepointonline.md#when-a-new-item-is-created) |Cette opération déclenche un flux lorsqu’un élément est créé dans une liste SharePoint. |
| [Quand un élément existant est modifié](connectors-create-api-sharepointonline.md#when-an-existing-item-is-modified) |Cette opération déclenche un flux lorsqu’un élément existant est modifié dans une liste SharePoint. |

## <a name="sharepoint-online-actions"></a>Actions SharePoint Online
SharePoint comporte les actions suivantes :

| Action | Description |
| --- | --- |
| [Obtenir les métadonnées d’un fichier](connectors-create-api-sharepointonline.md#get-file-metadata) |Cette opération récupère les métadonnées d’un fichier à l’aide de l’identificateur du fichier. |
| [Mettre à jour un fichier](connectors-create-api-sharepointonline.md#update-file) |Cette opération met à jour le contenu d’un fichier. |
| [Supprimer un fichier](connectors-create-api-sharepointonline.md#delete-file) |Cette opération supprime un fichier. |
| [Obtenir les métadonnées d’un fichier à l’aide du chemin](connectors-create-api-sharepointonline.md#get-file-metadata-using-path) |Cette opération récupère les métadonnées d’un fichier à l’aide du chemin d’accès au fichier. |
| [Obtenir le contenu d’un fichier à l’aide du chemin](connectors-create-api-sharepointonline.md#get-file-content-using-path) |Cette opération récupère le contenu d’un fichier à l’aide du chemin d’accès au fichier. |
| [Obtenir le contenu d’un fichier](connectors-create-api-sharepointonline.md#get-file-content) |Cette opération récupère le contenu d’un fichier à l’aide de l’identificateur du fichier. |
| [Créer un fichier](connectors-create-api-sharepointonline.md#create-file) |Cette opération charge un fichier dans un site SharePoint. |
| [Copier un fichier](connectors-create-api-sharepointonline.md#copy-file) |Cette opération copie un fichier dans un site SharePoint. |
| [Lister le dossier](connectors-create-api-sharepointonline.md#list-folder) |Cette opération récupère la liste des fichiers contenus dans un dossier SharePoint. |
| [Lister le dossier racine](connectors-create-api-sharepointonline.md#list-root-folder) |Cette opération récupère la liste des fichiers du dossier SharePoint racine. |
| [Extraire un dossier](connectors-create-api-sharepointonline.md#extract-folder) |Cette opération extrait un fichier d’archives dans un dossier SharePoint (exemple : .zip). |
| [Obtenir les éléments](connectors-create-api-sharepointonline.md#get-items) |Cette opération récupère les éléments d’une liste SharePoint. |
| [Créer un élément](connectors-create-api-sharepointonline.md#create-item) |Cette opération crée un élément dans une liste SharePoint. |
| [Obtenir un élément](connectors-create-api-sharepointonline.md#get-item) |Cette opération récupère un élément d’une liste SharePoint par le biais de son identificateur. |
| [Supprimer un élément](connectors-create-api-sharepointonline.md#delete-item) |Cette opération supprime un élément d’une liste SharePoint. |
| [Mettre à jour un élément](connectors-create-api-sharepointonline.md#update-item) |Cette opération met à jour un élément dans une liste SharePoint. |
| [Obtenir les valeurs d’une entité](connectors-create-api-sharepointonline.md#get-entity-values) |Cette opération récupère les valeurs possibles d’une entité SharePoint. |
| [Obtenir les listes](connectors-create-api-sharepointonline.md#get-lists) |Cette opération récupère les listes SharePoint d’un site. |

### <a name="action-details"></a>Détails de l’action
Voici les détails des actions et des déclencheurs de ce connecteur, ainsi que leurs réponses :

### <a name="get-file-metadata"></a>Obtenir les métadonnées d’un fichier
Cette opération récupère les métadonnées d’un fichier à l’aide de l’identificateur du fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |
| id* |File identifier (Identificateur du fichier) |Sélectionner un fichier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
| id |string |
| Nom |string |
| displayName |string |
| path |string |
| LastModified |string |
| Taille |integer |
| MediaType |string |
| IsFolder |booléenne |
| ETag |string |
| FileLocator |string |

### <a name="update-file"></a>Mettre à jour un fichier
Cette opération met à jour le contenu d’un fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |
| id* |File identifier (Identificateur du fichier) |Sélectionner un fichier |
| body* |Contenu du fichier |Contenu du fichier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
| id |string |
| Nom |string |
| displayName |string |
| path |string |
| LastModified |string |
| Taille |integer |
| MediaType |string |
| IsFolder |booléenne |
| ETag |string |
| FileLocator |string |

### <a name="delete-file"></a>Supprimer un fichier
Cette opération supprime un fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |
| id* |File identifier (Identificateur du fichier) |Sélectionner un fichier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="get-file-metadata-using-path"></a>Obtenir les métadonnées d’un fichier à l’aide du chemin
Cette opération récupère les métadonnées d’un fichier à l’aide du chemin d’accès au fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |
| path* |Chemin de fichier |Sélectionner un fichier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
| id |string |
| Nom |string |
| displayName |string |
| path |string |
| LastModified |string |
| Taille |integer |
| MediaType |string |
| IsFolder |booléenne |
| ETag |string |
| FileLocator |string |

### <a name="get-file-content-using-path"></a>Obtenir le contenu d’un fichier à l’aide du chemin
Cette opération récupère le contenu d’un fichier à l’aide du chemin d’accès au fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |
| path* |Chemin de fichier |Sélectionner un fichier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="get-file-content"></a>Obtenir le contenu d’un fichier
Cette opération récupère le contenu d’un fichier à l’aide de l’identificateur du fichier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |
| id* |File identifier (Identificateur du fichier) |Sélectionner un fichier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="create-file"></a>Créer un fichier
Cette opération charge un fichier dans un site SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |
| folderPath* |Chemin d'accès du dossier |Sélectionner un fichier |
| name* |Nom de fichier |Nom du fichier |
| body* |Contenu du fichier |Contenu du fichier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
| id |string |
| Nom |string |
| displayName |string |
| path |string |
| LastModified |string |
| Taille |integer |
| MediaType |string |
| IsFolder |booléenne |
| ETag |string |
| FileLocator |string |

### <a name="copy-file"></a>Copier un fichier
Cette opération copie un fichier dans un site SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |
| source* |Source file Path (Chemin d’accès au fichier source) |Chemin du fichier source |
| destination* |Chemin de destination du fichier |Chemin du fichier de destination |
| overwrite |Overwrite flag (Indicateur de remplacement) |Indique si le fichier de destination doit ou non être remplacé s’il existe déjà |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
| id |string |
| Nom |string |
| displayName |string |
| path |string |
| LastModified |string |
| Taille |integer |
| MediaType |string |
| IsFolder |booléenne |
| ETag |string |
| FileLocator |string |

### <a name="when-a-file-is-created"></a>Quand un fichier est créé
Cette opération déclenche un flux lorsqu’un fichier est créé dans un dossier SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL du site SharePoint |
| folderId* |ID du dossier |Sélectionner un dossier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="when-a-file-is-modified"></a>Quand un fichier est modifié
Cette opération déclenche un flux lorsqu’un fichier est modifié dans un dossier SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL du site SharePoint |
| folderId* |ID du dossier |Sélectionner un dossier |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="list-folder"></a>List folder (Répertorier les fichiers d’un dossier)
Cette opération récupère la liste des fichiers contenus dans un dossier SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |
| id* |File identifier (Identificateur du fichier) |Identificateur unique du dossier |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
| id |string |
| Nom |string |
| displayName |string |
| path |string |
| LastModified |string |
| Taille |integer |
| MediaType |string |
| IsFolder |booléenne |
| ETag |string |
| FileLocator |string |

### <a name="list-root-folder"></a>Répertorier le dossier racine
Cette opération récupère la liste des fichiers du dossier SharePoint racine. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
| id |string |
| Nom |string |
| displayName |string |
| path |string |
| LastModified |string |
| Taille |integer |
| MediaType |string |
| IsFolder |booléenne |
| ETag |string |
| FileLocator |string |

### <a name="extract-folder"></a>Extraire un dossier
Cette opération extrait un fichier d’archives dans un dossier SharePoint (exemple : .zip). 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint, telle que http://contoso.sharepoint.com/sites/mysite |
| source* |Source file Path (Chemin d’accès au fichier source) |Chemin du fichier source |
| destination* |Chemin de destination du dossier |Chemin du dossier de destination |
| overwrite |Overwrite flag (Indicateur de remplacement) |Indique si le fichier de destination doit ou non être remplacé s’il existe déjà |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
BlobMetadata

| Nom de la propriété | Type de données |
| --- | --- | --- |
| id |string |
| Nom |string |
| displayName |string |
| path |string |
| LastModified |string |
| Taille |integer |
| MediaType |string |
| IsFolder |booléenne |
| ETag |string |
| FileLocator |string |

### <a name="when-a-new-item-is-created"></a>When a new item is created (Quand un élément est créé)
Cette opération déclenche un flux lorsqu’un élément est créé dans une liste SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite) |
| table* |Nom de la liste |Nom de la liste SharePoint |
| $filter |Requête de filtre |Requête de filtre ODATA pour limiter le nombre d’entrées renvoyées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

### <a name="when-an-existing-item-is-modified"></a>When an existing item is modified (Quand un élément existant est modifié)
Cette opération déclenche un flux lorsqu’un élément existant est modifié dans une liste SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite) |
| table* |Nom de la liste |Nom de la liste SharePoint |
| $filter |Requête de filtre |Requête de filtre ODATA pour limiter le nombre d’entrées renvoyées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

### <a name="get-items"></a>Get items (Obtenir les éléments)
Cette opération récupère les éléments d’une liste SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite) |
| table* |Nom de la liste |Nom de la liste SharePoint |
| $filter |Requête de filtre |Requête de filtre ODATA pour limiter le nombre d’entrées renvoyées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
ItemsList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

### <a name="create-item"></a>Créer un élément
Cette opération crée un élément dans une liste SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite) |
| table* |Nom de la liste |Nom de la liste SharePoint |
| item* |Item |Élément à créer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
Item

| Nom de la propriété | Type de données |
| --- | --- |
| ItemInternalId |string |

### <a name="get-item"></a>Get item (Obtenir un élément)
Cette opération récupère un élément d’une liste SharePoint par le biais de son identificateur. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite) |
| table* |Nom de la liste |Nom de la liste SharePoint |
| id* |Id |Identificateur unique de l’élément à récupérer |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
Item

| Nom de la propriété | Type de données |
| --- | --- |
| ItemInternalId |string |

### <a name="delete-item"></a>Delete item (Supprimer un élément)
Cette opération supprime un élément d’une liste SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite) |
| table* |Nom de la liste |Nom de la liste SharePoint |
| id* |Id |Identificateur unique de l’élément à supprimer |

Le caractère * indique qu’une propriété est obligatoire.

### <a name="update-item"></a>Update item (Mettre à jour un élément)
Cette opération met à jour un élément dans une liste SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite) |
| table* |Nom de la liste |Nom de la liste SharePoint |
| id* |Id |Identificateur unique de l’élément à mettre à jour |
| item* |Item |Élément avec des propriétés modifiées |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
Item

| Nom de la propriété | Type de données |
| --- | --- |
| ItemInternalId |string |

### <a name="get-entity-values"></a>Get entity values (Obtenir les valeurs d’une entité)
Cette opération récupère les valeurs possibles d’une entité SharePoint. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site SharePoint |URL du site SharePoint |
| table* |Nom de la table |Nom de la table |
| id* |ID d’entité |Identificateur de l’entité |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
### <a name="get-lists"></a>Get lists (Obtenir les listes)
Cette opération récupère les listes SharePoint d’un site. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| dataset* |URL du site |URL de site SharePoint (exemple : http://contoso.sharepoint.com/sites/mysite) |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="output-details"></a>Détails des résultats
TablesList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

## <a name="http-responses"></a>Réponses HTTP
Les actions et déclencheurs ci-dessus peuvent renvoyer un ou plusieurs des codes d’état HTTP suivants : 

| Nom | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Feb17_HO2-->


