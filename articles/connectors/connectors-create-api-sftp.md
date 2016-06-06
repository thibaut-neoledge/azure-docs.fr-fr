<properties
    pageTitle="Ajouter le connecteur SFTP à vos applications logiques | Microsoft Azure"
    description="Vue d’ensemble du connecteur SFTP avec les paramètres d’API REST"
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
   ms.date="05/18/2016"
   ms.author="mandia"/>

# Prise en main du connecteur SFTP 
Connectez-vous à un serveur SFTP pour gérer vos fichiers. Vous pouvez effectuer différentes tâches sur le serveur SFTP, telles que le chargement de fichiers et la suppression de fichiers. Le connecteur SFTP peut être utilisé dans :

- Logic Apps

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec SFTP, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez de SFTP. 
- Utiliser un déclencheur quand un fichier est mis à jour.
- Utiliser des actions pour créer des fichiers, supprimer des fichiers et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, vous pouvez obtenir le contenu d’un fichier, puis mettre à jour une base de données SQL. 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Déclencheurs et actions
Le connecteur SFTP propose les déclencheurs et les actions suivants :

Déclencheurs | Actions
--- | ---
<ul><li>Quand un fichier est créé ou modifié</li></ul> | <ul><li>Créer un fichier</li><li>Copier un fichier</li><li>Supprimer un fichier</li><li>Extraire un dossier</li><li>Obtenir le contenu d’un fichier</li><li>Obtenir le contenu d’un fichier à l’aide du chemin</li><li>Obtenir les métadonnées d’un fichier</li><li>Obtenir les métadonnées d’un fichier à l’aide du chemin</li><li>Mettre à jour un fichier</li><li>Quand un fichier est créé ou modifié</li></ul>

Tous les connecteurs prennent en charge les données aux formats JSON et XML.


## Créer une connexion à SFTP
Quand vous ajoutez ce connecteur à vos applications logiques, entrez les valeurs suivantes :

|Propriété| Requis|Description|
| ---|---|---|
|Adresse du serveur hôte| Oui | Entrez le nom de domaine complet (FQDN) ou l’adresse IP du serveur SFTP.|
|Nom d'utilisateur| Oui | Entrez le nom d’utilisateur pour vous connecter au serveur SFTP.|
|Mot de passe | Oui | Entrez le mot de passe de l’utilisateur.|
|Empreinte de la clé d’hôte du serveur SSH | Oui | Entrez l’empreinte de la clé d’hôte publique du serveur SSH. <br/><br/>En règle générale, l’administrateur du serveur peut vous fournir cette clé. Vous pouvez également utiliser les outils ```WinSCP``` ou ```ssh-keygen-g3 -F``` pour obtenir l’empreinte de la clé. | 

Voici une procédure pas à pas pour créer la connexion :

>[AZURE.INCLUDE [Procédure de création d’une connexion à SFTP](../../includes/connectors-create-api-sftp.md)]

Après avoir créé la connexion, vous entrez les propriétés SFTP, telles que le chemin du dossier ou le fichier. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion SFTP dans d’autres applications logiques.


## Informations de référence sur l'API REST Swagger
S'applique à la version 1.0.

### Créer un fichier
Charge un fichier dans SFTP. ```POST: /datasets/default/files```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|(aucun) |Chemin unique du dossier dans SFTP|
|name|string|yes|query| (aucun)|Nom du fichier|
|body|string(binary) |yes|body|(aucun) |Contenu du fichier à créer dans SFTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Copier un fichier
Copie un fichier dans SFTP. ```POST: /datasets/default/copyFile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query| (aucun)|Chemin du fichier source|
|destination|string|yes|query|(aucun) |Chemin du fichier de destination, y compris le nom du fichier|
|overwrite|booléenne|no|query|(aucun)|Remplace le fichier de destination si la valeur est « true »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Supprimer un fichier 
Supprime un fichier dans SFTP. ```DELETE: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier dans SFTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Extraire un dossier
Extrait un fichier d’archive dans un dossier à l’aide de SFTP (exemple : .zip). ```POST: /datasets/default/extractFolderV2```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query|(aucun) |Chemin du fichier d'archive|
|destination|string|yes|query|(aucun) |Chemin du dossier de destination|
|overwrite|booléenne|no|query|(aucun)|Remplace les fichiers de destination si la valeur est « true »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|

### Obtenir le contenu d’un fichier
Récupère le contenu d’un fichier de SFTP à partir de son identifiant. ```GET: /datasets/default/files/{id}/content```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier dans SFTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir le contenu d’un fichier à l’aide du chemin
Récupère le contenu d’un fichier de SFTP à l’aide du chemin d’accès. ```GET: /datasets/default/GetFileContentByPath```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query| (aucun)|Chemin unique du fichier dans SFTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier 
Récupère les métadonnées d’un fichier de SFTP à l’aide de son identifiant. ```GET: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path| (aucun)|Identificateur unique du fichier dans SFTP|

#### Response
| Nom | Description |
| --- | --- |
| 200 | OK | 
| default | L’opération a échoué.


### Obtenir les métadonnées d’un fichier à l’aide du chemin
Récupère les métadonnées d’un fichier de SFTP à l’aide du chemin d’accès. ```GET: /datasets/default/GetFileByPath```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|(aucun) |Chemin unique du fichier dans SFTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour un fichier
Met à jour le contenu d’un fichier à l’aide de SFTP. ```PUT: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier dans SFTP|
|body|string(binary) |yes|body| (aucun)|Contenu du fichier à mettre à jour dans SFTP|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Quand un fichier est créé ou modifié 
Déclenche un flux quand un fichier est modifié dans SFTP. ```GET: /datasets/default/triggers/onupdatedfile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderId|string|yes|query|(aucun) |Identificateur unique du dossier|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## Définitions d'objet

#### DataSetsMetadata

| Nom | Type de données | Requis|
|---|---|---|
|tabular|non défini|no|
|objet blob|non défini|no|

#### TabularDataSetsMetadata

| Nom | Type de données | Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

| Nom | Type de données | Requis|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### BlobMetadata

| Nom | Type de données | Requis|
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

<!---HONumber=AcomDC_0525_2016-->