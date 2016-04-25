<properties
	pageTitle="Ajouter l'API du stockage d’objets blob Azure à vos applications logiques | Microsoft Azure"
	description="Vue d'ensemble de l'API du stockage d’objets Azure avec les paramètres de l'API REST"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/16/2016"
   ms.author="mandia"/>

# Prise en main de l’API de stockage d’objets blob
Connectez-vous à un objet blob Azure pour gérer les fichiers d’un conteneur d'objets blob, par exemple pour créer des fichiers, supprimer des fichiers et bien plus encore. L’API de stockage d’objets blob Azure peut être utilisée à partir de :

- Logic apps 

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec le stockage d'objets blob Azure, vous pouvez :

- Créer votre flux d'activité en fonction des données que vous obtenez de l’objet blob. 
- Utiliser des actions permettant de créer un fichier, obtenir le contenu d’un fichier, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d'autres actions. Par exemple, vous pourrez rechercher le terme « urgent » dans un fichier de votre objet blob et envoyer tous les fichiers contenant le terme « urgent » dans un message électronique à l'aide d'Office 365. 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
L’objet blob Azure inclut les actions suivantes. Il n'y a aucun déclencheur.

| Déclencheurs | Actions|
| --- | --- |
| Aucun. | <ul><li>Créer un fichier</li><li>Copier un fichier</li><li>Supprimer un fichier</li><li>Extraire une archive dans un dossier</li><li>Obtenir le contenu d’un fichier</li><li>Obtenir le contenu du fichier à l’aide du chemin d’accès</li><li>Obtenir les métadonnées d’un fichier</li><li>Obtenir les métadonnées d’un fichier à l’aide d’un chemin d’accès</li><li>Mettre à jour le fichier</li></ul> |

Toutes les API prennent en charge les données aux formats JSON et XML.

## Créer une connexion à un objet blob Azure
Quand vous ajoutez cette API à vos applications logiques, entrez les valeurs de compte de stockage suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|Nom du compte de stockage Azure | yes | Nom de votre compte de stockage d’objets blob|
|Clé d'accès au compte de stockage Azure | yes | Clé d’accès associée à votre compte de stockage d’objets blob.|

Après avoir créé la connexion, vous entrez les propriétés des objets blob, telles que le chemin du dossier ou le nom du fichier. La section **Informations de référence sur l'API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion blob dans d'autres applications logiques.
 

## Informations de référence sur l'API REST Swagger
S'applique à la version 1.0.

### Créer un fichier
Télécharge un fichier local vers un stockage d'objets blob Azure. ```POST: /datasets/default/files```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|yes|query| (aucun) |Chemin d'accès au dossier contenant le fichier à télécharger vers le stockage d'objets Blob Azure|
|name|string|yes|query|(aucun) |Nom du fichier à créer dans le stockage d'objets blob Azure|
|body|string(binary) |yes|body|(aucun)|Contenu du fichier à télécharger vers le stockage d'objets blob Azure|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Copier un fichier
Copie un fichier dans un stockage d’objets blob Azure.```POST: /datasets/default/copyFile```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query|(aucun) |URL du fichier source|
|destination|string|yes|query| (aucun)|Chemin de destination du fichier dans le stockage d’objets blob, y compris le nom de fichier cible|
|overwrite|booléenne|no|query|(aucun) |Remplace le fichier de destination si la valeur est « true »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Supprimer un fichier
Supprime un fichier du stockage d'objets blob Azure. ```DELETE: /datasets/default/files/{id}```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier à supprimer du stockage d’objets blob Azure|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Extraire une archive dans un dossier
Extrait un fichier d'archive vers un dossier dans le stockage d’objets blob Azure (exemple : .zip). ```POST: /datasets/default/ExtractFolderV2```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query| (aucun)|Chemin du fichier d'archive|
|destination|string|yes|query|(aucun) |Chemin d'accès au dossier dans le stockage d’objets blob Azure où extraire le contenu de l'archive|
|overwrite|booléenne|no|query|(aucun) |Remplace les fichiers de destination si la valeur est « true »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir le contenu d’un fichier
Extrait le contenu du fichier du stockage d’objets blob Azure à l’aide de l’identifiant. ```GET: /datasets/default/files/{id}/content```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|Identificateur unique du fichier|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir le contenu d’un fichier à l'aide du chemin
Extrait le contenu du fichier du stockage d’objets blob Azure à l’aide du chemin. ```GET: /datasets/default/GetFileContentByPath```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|(aucun) |Chemin d'accès unique au fichier dans le stockage d'objets blob Azure|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier
Extrait les métadonnées d’un fichier du stockage d’objets blob Azure à l'aide de l’identifiant du fichier. ```GET: /datasets/default/files/{id}```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier à l'aide du chemin
Extrait les métadonnées d’un fichier du stockage d’objets blob Azure à l'aide du chemin. ```GET: /datasets/default/GetFileByPath```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|(aucun)|Chemin d'accès unique au fichier dans le stockage d'objets blob Azure|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour un fichier
Met à jour un fichier dans le stockage d'objets blob Azure. ```PUT: /datasets/default/files/{id}```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier à mettre à jour dans le stockage d’objets blob Azure|
|body|string(binary) |yes|body|(aucun) |Contenu du fichier à mettre à jour dans le stockage d'objets blob Azure|

#### Response
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

<!---HONumber=AcomDC_0413_2016-->