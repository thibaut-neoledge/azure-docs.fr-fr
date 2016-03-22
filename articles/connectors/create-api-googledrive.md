<properties
	pageTitle="Ajouter l’API Google Drive à PowerApps ou à des applications logiques | Microsoft Azure"
	description="Vue d’ensemble de l’API Google Drive avec les paramètres de l’API REST"
	services=""
    suite=""
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
   ms.date="03/02/2016"
   ms.author="mandia"/>

# Prendre en main l’API Google Drive
Connectez-vous à Google Drive pour créer des fichiers, obtenir des lignes et plus encore. L’API Google Drive peut être utilisée à partir de :

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-googledrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-googledrive.md)

Avec Google Drive, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez pendant votre recherche. 
- Utiliser des actions pour rechercher des images, des actualités et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, vous pouvez rechercher une vidéo, puis utiliser Twitter pour la publier dans un flux Twitter.
- Ajouter l’API Google Drive à PowerApps Enterprise. Ensuite, vos utilisateurs peuvent utiliser cette API dans leurs applications. 

Pour plus d’informations sur l’ajout d’une API à PowerApps Enterprise, consultez [Inscrire une API dans PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Déclencheurs et actions
Google Drive inclut les actions suivantes. Il n’y a aucun déclencheur.

Déclencheurs | Actions
--- | ---
Aucun | <ul><li>Créer un fichier</li><li>Insérer une ligne</li><li>Copier le fichier</li><li>Supprimer le fichier</li><li>Supprimer la ligne</li><li>Extraire l’archive dans un dossier</li><li>Obtenir le contenu d’un fichier à l’aide de l’identifiant</li><li>Obtenir le contenu d’un fichier à l’aide du chemin</li><li>Obtenir les métadonnées d’un fichier à l’aide de l’identifiant</li><li>Obtenir les métadonnées d’un fichier à l’aide du chemin</li><li>Obtenir une ligne</li><li>Mettre à jour un fichier</li><li>Mettre à jour une ligne</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.


## Créer la connexion à Google Drive

Quand vous ajoutez cette API à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Google Drive.

1. Connectez-vous à votre compte Google Drive.
2. Autorisez vos applications logiques à se connecter à votre compte Google Drive et à l’utiliser. 

Après avoir créé la connexion, vous entrez les propriétés Google Drive, telles que le chemin du dossier ou le nom du fichier. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion Google Drive dans d’autres applications logiques.


## Informations de référence sur l'API REST Swagger
S'applique à la version 1.0.

### Créer un fichier    
Charge un fichier sur Google Drive. ```POST: /datasets/default/files```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|(aucun) |Chemin du dossier Google Drive sur lequel charger le fichier|
|name|string|yes|query|(aucun) |Nom du fichier à créer dans Google Drive|
|body|string(binary) |yes|body| (aucun)|Contenu du fichier à charger sur Google Drive|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Insérer une ligne    
Insère une ligne dans une feuille Google. ```POST: /datasets/{dataset}/tables/{table}/items```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path| (aucun)|Identificateur unique du fichier Google Sheet|
|table|string|yes|path|(aucun) |Identificateur unique de la feuille de calcul|
|item|ItemInternalId: string |yes|body|(aucun) |Ligne à insérer dans la feuille spécifiée|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Copier un fichier    
Copie un fichier sur Google Drive. ```POST: /datasets/default/copyFile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query| (aucun)|URL du fichier source|
|destination|string|yes|query|(aucun) |Chemin de destination du fichier dans Google Drive, y compris le nom de fichier cible|
|overwrite|booléenne|no|query|(aucun) |Remplace le fichier de destination si la valeur est « true »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Supprimer un fichier    
Supprime un fichier de Google Drive. ```DELETE: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier à supprimer de Google Drive|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Supprimer la ligne    
Supprime une ligne d’une feuille Google. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun) |Identificateur unique du fichier Google Sheet|
|table|string|yes|path|(aucun) |Identificateur unique de la feuille de calcul|
|id|string|yes|path|(aucun) |Identificateur unique de la ligne à supprimer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Extraire une archive dans un dossier    
Extrait un fichier d’archive dans un dossier Google Drive (exemple : .zip). ```POST: /datasets/default/extractFolderV2```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query|(aucun) |Chemin du fichier d'archive|
|destination|string|yes|query|(aucun) |Chemin dans Google Drive indiquant où extraire le contenu de l’archive|
|overwrite|booléenne|no|query|(aucun) |Remplace les fichiers de destination si la valeur est « true »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir le contenu d’un fichier à l’aide de l’identifiant    
Récupère le contenu d’un fichier de Google Drive à l’aide de l’identifiant. ```GET: /datasets/default/files/{id}/content```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier à récupérer dans Google Drive|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir le contenu d’un fichier à l’aide du chemin    
Récupère le contenu d’un fichier de Google Drive à l’aide du chemin. ```GET: /datasets/default/GetFileContentByPath```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|(aucun) |Chemin du fichier dans Google Drive|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier à l’aide de l’identifiant    
Récupère les métadonnées d’un fichier de Google Drive à l’aide de l’identifiant. ```GET: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier dans Google Drive|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier à l’aide du chemin    
Récupère les métadonnées d’un fichier de Google Drive à l’aide du chemin. ```GET: /datasets/default/GetFileByPath```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|(aucun) |Chemin du fichier dans Google Drive|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir une ligne    
Récupère une seule ligne d’une feuille Google. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun) |Identificateur unique du fichier Google Sheet|
|table|string|yes|path|(aucun) |Identificateur unique de la feuille de calcul|
|id|string|yes|path| (aucun)|Identificateur unique de la ligne à récupérer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour un fichier    
Met à jour un fichier dans Google Drive. ```PUT: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier à mettre à jour dans Google Drive|
|body|string(binary) |yes|body| (aucun)|Contenu du fichier à charger sur Google Drive|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour une ligne    
Met à jour une ligne dans une feuille Google. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|(aucun) |Identificateur unique du fichier Google Sheet|
|table|string|yes|path| (aucun)|Identificateur unique de la feuille de calcul|
|id|string|yes|path|(aucun) |Identificateur unique de la ligne à mettre à jour|
|item|ItemInternalId: string |yes|body|(aucun) |Ligne avec valeurs mises à jour|

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

#### TableMetadata

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|non défini|no|

#### TablesList

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|value|array|no|

#### Table

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|Nom|string|no|
|DisplayName|string|no|

#### Item

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|value|array|no|


## Étapes suivantes

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenez à la [liste des API](apis-list.md).


<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/create-api-googledrive/google-developers-console.png
[8]: ./media/create-api-googledrive/use-google-apis.png
[9]: ./media/create-api-googledrive/googledrive-api-overview.png
[10]: ./media/create-api-googledrive/enable-googledrive-api.png
[12]: ./media/create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/create-api-googledrive/configure-consent-screen.png
[14]: ./media/create-api-googledrive/create-client-id.png

<!---------HONumber=AcomDC_0309_2016-->