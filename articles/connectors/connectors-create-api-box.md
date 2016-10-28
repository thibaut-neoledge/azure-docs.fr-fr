<properties
    pageTitle="Ajouter le connecteur Box à vos applications logiques | Microsoft Azure"
    description="Vue d’ensemble du connecteur Box avec les paramètres d’API REST"
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
   ms.date="08/18/2016"
   ms.author="mandia"/>

# Prise en main du connecteur Box
Connectez-vous à Box pour créer des fichiers, supprimer des fichiers et bien plus encore.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec Box, vous pouvez :

- Créer votre flux d'activité en fonction des données que vous obtenez de Box.
- Utiliser des déclencheurs quand un fichier est créé ou mis à jour.
- Utiliser des actions pour copier un fichier, supprimer un fichier et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, lorsqu'un fichier est modifié dans Box, vous pouvez le sélectionner et l'envoyer par courrier électronique à l'aide d'Office 365.

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
Box inclut les déclencheurs et les actions suivants.

| Déclencheurs | Actions|
| --- | --- |
|<ul><li>Quand un fichier est créé</li><li>Quand un fichier est modifié</li></ul> | <ul><li>Créer un fichier</li><li>Quand un fichier est créé</li><li>Copier un fichier</li><li>Supprimer un fichier</li><li>Extraire une archive dans un dossier</li><li>Obtenir le contenu d’un fichier à l’aide de l’identifiant</li><li>Obtenir le contenu d’un fichier à l’aide du chemin</li><li>Obtenir les métadonnées d’un fichier à l’aide de l’identifiant</li><li>Obtenir les métadonnées d’un fichier à l’aide du chemin</li><li>Mettre à jour un fichier</li><li>Quand un fichier est modifié</li></ul>

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## Créer une connexion à Box
Quand vous ajoutez ce connecteur à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Box.

>[AZURE.INCLUDE [Procédure de création d’une connexion à Box](../../includes/connectors-create-api-box.md)]

Après avoir créé la connexion, vous entrez les propriétés de Box. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion Box dans d'autres applications logiques.

## Informations de référence sur l'API REST Swagger
S'applique à la version 1.0.

### Créer un fichier
Charge un fichier sur Box. ```POST: /datasets/default/files```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|Oui|query|Aucun |Chemin du dossier Box sur lequel charger le fichier|
|name|string|Oui|query|Aucun |Nom du fichier à créer dans Box|
|body|string(binary) |Oui|body|Aucun |Contenu du fichier à charger sur Box|

#### Réponse
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Quand un fichier est créé
Déclenche un flux quand un fichier est créé dans un dossier Box. ```GET: /datasets/default/triggers/onnewfile```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderId|string|Oui|query|Aucun |Identificateur unique du dossier dans Box|

#### Réponse
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Copier un fichier
Copie un fichier vers Box. ```POST: /datasets/default/copyFile```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|Oui|query|Aucun |URL du fichier source|
|destination|string|Oui|query| Aucun|Chemin de destination du fichier dans Box, y compris le nom de fichier cible|
|overwrite|booléenne|Non|query| Aucun|Remplace le fichier de destination si la valeur est « true »|

#### Réponse
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Supprimer un fichier
Supprime un fichier de Box. ```DELETE: /datasets/default/files/{id}```


| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|Oui|path|Aucun |Identificateur unique du fichier à supprimer de Box|

#### Réponse
|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Extraire une archive dans un dossier
Extrait un fichier d’archive dans un dossier de Box (exemple : .zip). ```POST: /datasets/default/extractFolderV2```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|Oui|query| |Chemin du fichier d'archive|
|destination|string|Oui|query| |Chemin dans Box indiquant où extraire le contenu de l'archive|
|overwrite|booléenne|Non|query| |Remplace les fichiers de destination si la valeur est « true »|

#### Réponse
|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir le contenu d’un fichier à l'aide de l’identifiant
Récupère le contenu d’un fichier de Box à partir de son identifiant. ```GET: /datasets/default/files/{id}/content```

| Name|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|Oui|path|Aucun |Identificateur unique du fichier dans Box|

#### Réponse
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir le contenu d’un fichier à l'aide du chemin
Récupère le contenu d’un fichier auprès de Box à partir du chemin. ```GET: /datasets/default/GetFileContentByPath```

| Name|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|Oui|query|Aucun |Chemin unique du fichier dans Box|

#### Réponse
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier à l'aide de l’identifiant
Récupère les métadonnées d’un fichier auprès de Box à partir de son identifiant. ```GET: /datasets/default/files/{id}```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|Oui|path| Aucun|Identificateur unique du fichier dans Box|

#### Réponse
|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier à l'aide du chemin
Récupère les métadonnées d’un fichier auprès de Box à partir du chemin. ```GET: /datasets/default/GetFileByPath```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|Oui|query|Aucun |Chemin unique du fichier dans Box|

#### Réponse
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour un fichier
Met à jour un fichier dans Box. ```PUT: /datasets/default/files/{id}```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|Oui|path| Aucun|Identificateur unique du fichier à mettre à jour dans Box|
|body|string(binary) |Oui|body|Aucun |Contenu du fichier à mettre à jour dans Box|

#### Réponse
|Name|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Quand un fichier est modifié
Déclenche un flux quand un fichier est modifié dans un dossier Box. ```GET: /datasets/default/triggers/onupdatedfile```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderId|string|Oui|query|Aucun |Identificateur unique du dossier dans Box|

#### Réponse
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## Définitions d’objet

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
|Chemin|string|no|
|LastModified|string|no|
|Taille|integer|no|
|MediaType|string|no|
|IsFolder|booléenne|no|
|ETag|string|no|
|FileLocator|string|no|

## Étapes suivantes

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0824_2016-->