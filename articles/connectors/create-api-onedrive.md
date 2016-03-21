<properties
pageTitle="Ajouter l’API OneDrive à PowerApps Enterprise et à vos applications logiques | Microsoft Azure"
description="Vue d’ensemble de l’API OneDrive avec les paramètres de l’API REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="03/03/2016"
ms.author="mandia"/>

# Prendre en main l’API OneDrive

Connexion à OneDrive pour gérer vos fichiers, y compris le téléchargement de fichiers, la suppression de fichiers, et plus encore. L’API OneDrive peut être utilisée à partir de :

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

&nbsp;

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques. Pour la version de schéma 2014-12-01-preview, cliquez sur [API OneDrive](../app-service-logic/app-service-logic-connector-onedrive.md).

Avec OneDrive, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez de OneDrive. 
- Utiliser des déclencheurs quand un fichier est créé ou mis à jour.
- Utiliser des actions pour créer un fichier, supprimer un fichier et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand un fichier est créé dans OneDrive, vous pouvez l’envoyer par courrier électronique à l’aide d’Office 365.
- Ajouter l’API OneDrive à PowerApps Enterprise. Ensuite, vos utilisateurs peuvent utiliser cette API dans leurs applications. 

Pour plus d’informations sur l’ajout d’une API à PowerApps Enterprise, consultez [Inscrire une API dans PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
L’API OneDrive inclut les déclencheurs et les actions suivants.

| Déclencheurs | Actions|
| --- | --- |
|<ul><li>Quand un fichier est créé</li><li>Quand un fichier est modifié</li></ul> | <ul><li>Créer un fichier</li><li>Lister des fichiers dans un dossier</li><li>Quand un fichier est créé</li><li>Copier un fichier</li><li>Supprimer un fichier</li><li>Extraire un dossier</li><li>Obtenir le contenu d’un fichier à l’aide de l’identifiant</li><li>Obtenir le contenu d’un fichier à l’aide du chemin</li><li>Obtenir les métadonnées d’un fichier à l’aide de l’identifiant</li><li>Obtenir les métadonnées d’un fichier à l’aide du chemin</li><li>Lister des dossiers racines</li><li>Mettre à jour un fichier</li><li>Quand un fichier est modifié</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.

## Créer une connexion à OneDrive

Quand vous ajoutez cette API à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte OneDrive.

1. Connectez-vous à votre compte OneDrive.
2. Autorisez vos applications logiques à se connecter à votre compte OneDrive et à l’utiliser. 

Après avoir créé la connexion, vous entrez les propriétés OneDrive, telles que le chemin du dossier ou le nom du fichier. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion dans d’autres applications logiques.

## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.


### Obtenir les métadonnées d’un fichier à l’aide de l’identifiant
Récupère les métadonnées d’un fichier dans OneDrive à l’aide de l’identifiant. ```GET: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|Identificateur unique du fichier dans OneDrive|

### Réponses
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour un fichier
Met à jour un fichier dans OneDrive. ```PUT: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|Identificateur unique du fichier à mettre à jour dans OneDrive|
|body| |yes|body|(aucun)|Contenu du fichier à mettre à jour dans OneDrive|


### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Supprimer un fichier
Supprime un fichier de OneDrive. ```DELETE: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|Identificateur unique du fichier à supprimer de OneDrive|


### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier à l’aide du chemin
Récupère les métadonnées d’un fichier dans OneDrive à l’aide du chemin. ```GET: /datasets/default/GetFileByPath```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|(aucun)|Chemin unique du fichier dans OneDrive|


### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|




### Obtenir le contenu d’un fichier à l’aide du chemin
Récupère le contenu d’un fichier dans OneDrive à l’aide du chemin. ```GET: /datasets/default/GetFileContentByPath```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|(aucun)|Chemin unique du fichier dans OneDrive|


### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|




### Obtenir le contenu d’un fichier à l’aide de l’identifiant
Récupère le contenu d’un fichier dans OneDrive à l’aide de l’identifiant. ```GET: /datasets/default/files/{id}/content```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|Identificateur unique du fichier dans OneDrive|


### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|




### Créer un fichier
Charge un fichier sur OneDrive. ```POST: /datasets/default/files```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|(aucun)|Chemin du dossier OneDrive sur lequel charger le fichier|
|name|string|yes|query|(aucun)|Nom du fichier à créer dans OneDrive|
|body| |yes|body|(aucun)|Contenu du fichier à charger sur OneDrive|


### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Copier un fichier
Copie un fichier dans OneDrive. ```POST: /datasets/default/copyFile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query|(aucun)|URL du fichier source|
|destination|string|yes|query|(aucun)|Chemin de destination du fichier dans OneDrive, y compris le nom de fichier cible|
|overwrite|booléenne|no|query|false|Remplace le fichier de destination si la valeur est « true »|


### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Quand un fichier est créé
Déclenche un flux quand un fichier est créé dans un dossier OneDrive. ```GET: /datasets/default/triggers/onnewfile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderId|string|yes|query|(aucun)|Identificateur unique du dossier dans OneDrive|


### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Déclenche un flux quand un fichier est modifié dans un dossier OneDrive
Déclenche un flux quand un fichier est modifié dans un dossier OneDrive. ```GET: /datasets/default/triggers/onupdatedfile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderId|string|yes|query|(aucun)|Identificateur unique du dossier dans OneDrive|


### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



### Extraire un dossier
Extrait un dossier dans OneDrive. ```POST: /datasets/default/extractFolderV2```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query|(aucun)|Chemin du fichier d'archive|
|destination|string|yes|query|(aucun)|Chemin dans OneDrive indiquant où extraire le contenu de l’archive|
|overwrite|booléenne|no|query|false|Remplace les fichiers de destination si la valeur est « true »|


### Response

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|



## Définitions d'objet

#### DataSetsMetadata

|Nom de la propriété | Type de données | Requis|
|---|---|---|
|tabular|non défini|no|
|objet blob|non défini|no|


#### TabularDataSetsMetadata

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|



#### BlobMetadata

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|ID|string|no|
|Nom|string|no|
|DisplayName|string|no|
|Path|string|no|
|LastModified|string|no|
|Taille|integer|no|
|MediaType|string|no|
|IsFolder|booléenne|no|
|ETag|string|no|
|FileLocator|string|no|


## Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenir à la [liste des API](apis-list.md).

[5]: https://account.live.com/developers/applications/create
[6]: ./media/create-api-onedrive/onedrive-new-app.png
[7]: ./media/create-api-onedrive/onedrive-app-api-settings.png

<!------HONumber=AcomDC_0309_2016-->