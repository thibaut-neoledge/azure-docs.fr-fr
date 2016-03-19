<properties
	pageTitle="Ajouter l'API FTP à vos applications logiques | Microsoft Azure"
	description="Vue d'ensemble de l'API FTP avec les paramètres de l'API REST"
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
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Prendre en main l’API FTP
Connexion à un serveur FTP pour gérer vos fichiers, y compris le téléchargement de fichiers, la suppression de fichiers, et plus encore. L’API FTP peut être utilisée à partir de :

- Logic Apps

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques. Pour la version de schéma 2014-12-01-preview, cliquez sur [Connecteur FTP](../app-service-logic/app-service-logic-connector-ftp.md).

Avec FTP, vous pouvez :

- Créer votre flux d'activité en fonction des données que vous obtenez de FTP. 
- Utiliser un déclencheur quand un fichier est mis à jour.
- Utiliser des actions permettant de créer des fichiers, obtenir le contenu d’un fichier, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d'autres actions. Par exemple, vous pouvez obtenir le contenu d’un fichier, puis mettre à jour une base de données SQL. 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Déclencheurs et actions
FTP propose les déclencheurs et les actions suivants.

Déclencheurs | Actions
--- | ---
<ul><li>Obtient un fichier mis à jour</li></ul> | <ul><li>Créer un fichier</li><li>Copier un fichier</li><li>Supprimer un fichier</li><li>Extraire un dossier</li><li>Obtenir le contenu d’un fichier</li><li>Obtenir le contenu d’un fichier à l’aide du chemin</li><li>Obtenir les métadonnées d’un fichier</li><li>Obtenir les métadonnées d’un fichier à l’aide du chemin</li><li>Obtenir un fichier mis à jour</li><li>Mettre à jour un fichier</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.

## Créer une connexion à FTP
Quand vous ajoutez cette API à vos applications logiques, entrez les valeurs suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|Adresse du serveur| Oui | Entrez le nom de domaine complet (FQDN) ou l'adresse IP du serveur FTP.|
|Nom d'utilisateur| Oui | Entrez le nom d'utilisateur pour vous connecter au serveur FTP.|
|Mot de passe | Oui | Entrez le mot de passe de l’utilisateur.|

Après avoir créé la connexion, vous entrez les propriétés FTP, telles que le fichier source ou le dossier de destination. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion FTP dans d'autres applications logiques.

## Informations de référence sur l'API REST Swagger
S'applique à la version 1.0.

### Créer un fichier
Charge un fichier sur le serveur FTP. ```POST: /datasets/default/files```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|(aucun) |Chemin du serveur FTP sur lequel charger le fichier|
|name|string|yes|query| (aucun)|Nom du fichier à créer sur le serveur FTP|
|body| |yes|body|(aucun) |Contenu du fichier à charger sur le serveur FTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Copier un fichier
Copie un fichier sur le serveur FTP. ```POST: /datasets/default/copyFile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query|(aucun) |URL du fichier source|
|destination|string|yes|query|(aucun) |Chemin de destination du fichier sur le serveur FTP, y compris le nom de fichier cible|
|overwrite|booléenne|no|query|(aucun) |Remplace le fichier de destination si la valeur est « true »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Supprimer un fichier 
Supprime un fichier du serveur FTP. ```DELETE: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier à supprimer du serveur FTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Extraire un dossier
Extrait un fichier d’archive dans un dossier sur le serveur FTP (exemple : .zip). ```POST: /datasets/default/extractFolderV2```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query| (aucun)|Chemin du fichier d'archive|
|destination|string|yes|query| (aucun)|Chemin du dossier de destination|
|overwrite|booléenne|no|query|(aucun)|Remplace les fichiers de destination si la valeur est « true »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Obtenir le contenu d’un fichier
Récupère le contenu d’un fichier sur le serveur FTP à partir de son identifiant. ```GET: /datasets/default/files/{id}/content```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir le contenu d’un fichier à l'aide du chemin
Récupère le contenu d’un fichier sur le serveur FTP à l’aide du chemin. ```GET: /datasets/default/GetFileContentByPath```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|(aucun) |Chemin unique du fichier sur le serveur FTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier 
Récupère les métadonnées d’un fichier sur le serveur FTP à l'aide de son identifiant. ```GET: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|Identificateur unique du fichier|

#### Response
| Nom | Description |
| --- | --- |
| 200 | OK | 
| default | L’opération a échoué.


### Obtenir les métadonnées d’un fichier à l'aide du chemin
Récupère les métadonnées d’un fichier sur le serveur FTP à l'aide du chemin. ```GET: /datasets/default/GetFileByPath```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query| (aucun)|Chemin unique du fichier sur le serveur FTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir un fichier mis à jour
Obtient un fichier mis à jour. ```GET: /datasets/default/triggers/onupdatedfile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderId|string|yes|query|(aucun) |ID du dossier dans lequel rechercher un fichier mis à jour|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour un fichier 
Met à jour un fichier sur le serveur FTP. ```PUT: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path| (aucun)|Identificateur unique du fichier à mettre à jour sur le serveur FTP|
|body| |yes|body|(aucun) |Contenu du fichier à mettre à jour sur le serveur FTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## Définitions d'objet

#### DataSetsMetadata

| Nom | Type de données | Requis |
|---|---|---|
|tabular|non défini|no|
|objet blob|non défini|no|

#### TabularDataSetsMetadata

| Nom | Type de données | Requis |
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

| Nom | Type de données | Requis |
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### BlobMetadata

| Nom | Type de données | Requis |
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

<!---HONumber=AcomDC_0302_2016-->