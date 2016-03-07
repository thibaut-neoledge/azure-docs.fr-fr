<properties
	pageTitle="Ajouter l’API Dropbox à PowerApps Enterprise ou des applications logiques | Microsoft Azure"
	description="Vue d’ensemble de l’API Dropbox avec les paramètres de l’API REST"
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
   ms.date="02/11/2016"
   ms.author="mandia"/>

# Prendre en main l’API Dropbox 
Connectez-vous à Dropbox pour effectuer des tâches de gestion de fichiers, telles que la création ou la récupération de fichiers.

L’API Dropbox peut être utilisée à partir de PowerApps Enterprise et d’applications logiques.

Avec Dropbox, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez de Dropbox. 
- Utiliser des déclencheurs quand un fichier est créé ou mis à jour.
- Utiliser des actions pour créer un fichier, supprimer un fichier et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand un fichier est créé dans Dropbox, vous pouvez l’envoyer par courrier électronique à l’aide d’Office 365.
- Ajouter l’API Dropbox à PowerApps Enterprise. Ensuite, vos utilisateurs peuvent utiliser cette API dans leurs applications. 

Pour plus d’informations sur l’ajout d’une API à PowerApps Enterprise, consultez [Inscrire une API dans PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Pour ajouter une opération à des applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
Dropbox inclut les déclencheurs et les actions suivants.

Déclencheurs | Actions
--- | ---
<ul><li>Quand un fichier est créé</li><li>Quand un fichier est modifié</li></ul> | <ul><li>Créer un fichier</li><li>Quand un fichier est créé</li><li>Copier un fichier</li><li>Supprimer un fichier</li><li>Extraire une archive dans un dossier</li><li>Obtenir le contenu d’un fichier à l’aide de l’identifiant</li><li>Obtenir un fichier à l’aide du chemin</li><li>Obtenir les métadonnées d’un fichier à l’aide de l’identifiant</li><li>Obtenir les métadonnées d’un fichier à l’aide du chemin</li><li>Mettre à jour un fichier</li><li>Quand un fichier est modifié</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.

## Créer la connexion à Dropbox

### Ajouter une configuration à PowerApps
Quand vous ajoutez Dropbox à PowerApps Enterprise, vous entrez les valeurs **Clé d’application** et **Question secrète de l’application** de votre application Dropbox. La valeur **URL de redirection** est également utilisée dans votre application Dropbox. Si vous n’avez pas d’application Dropbox, vous pouvez utiliser les étapes suivantes pour en créer une :

1. Connectez-vous à [Dropbox][1].
2. Accédez au site de développement Dropbox et sélectionnez **My Apps** (Mes applications) : ![Site de développement Dropbox][8]  
3. Sélectionnez **Create app** (Créer une application) : ![Création d’application Dropbox][9]  
4. Dans **Create a new app on the Dropbox platform** (Créer une application sur la plateforme Dropbox) :  

	1. Dans **Choose API**, sélectionnez **Dropbox API**.
	2. Dans **Choose the type of access you need**, sélectionnez **Full Dropbox...**.  
	3. Entrez le nom de votre application.  

	![Création d’application Dropbox page 1][10]

5. Dans la page des paramètres de l’application :

	1. Dans **OAuth 2**, entrez la valeur **URL de redirection** affichée quand vous ajoutez l’API Dropbox dans le portail Azure. Sélectionnez **Ajouter**.  
	2. Sélectionnez le lien **Show** pour révéler la **question secrète de l’application** :  

	![Création d’application Dropbox page 2][11]

Maintenant, copiez/collez ces valeurs **Clé d’application** et **Question secrète de l’application** dans votre configuration Dropbox dans le portail Azure.

### Ajouter une configuration à des applications logiques
Quand vous ajoutez cette API à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Dropbox.

1. Connectez-vous à votre compte Dropbox.
2. Sélectionnez **Autoriser** et permettez à vos applications logiques de se connecter à votre compte Dropbox et de l’utiliser. 

Après avoir créé la connexion, vous entrez les propriétés Dropbox, telles que le chemin du dossier ou le nom du fichier. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion Dropbox dans d’autres applications logiques.

## Informations de référence sur l’API REST Swagger

### Créer un fichier    
Charge un fichier sur Dropbox. ```POST: /datasets/default/files```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|(aucun) |Chemin du dossier Dropbox sur lequel charger le fichier|
|name|string|yes|query|(aucun) |Nom du fichier à créer dans Dropbox|
|body|chaîne (binaire) |yes|body|(aucun) |Contenu du fichier à charger sur Dropbox|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Quand un fichier est créé    
Déclenche un flux quand un fichier est créé dans un dossier Dropbox. ```GET: /datasets/default/triggers/onnewfile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderId|string|yes|query|(aucun) |Identificateur unique du dossier dans Dropbox|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Copier un fichier    
Copie un fichier vers Dropbox. ```POST: /datasets/default/copyFile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query|(aucun) |URL du fichier source|
|destination|string|yes|query| (aucun)|Chemin de destination du fichier dans Dropbox, y compris le nom de fichier cible|
|overwrite|booléenne|no|query|(aucun) |Remplace le fichier de destination si la valeur est « true »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Supprimer un fichier    
Supprime un fichier de Dropbox. ```DELETE: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun)|Identificateur unique du fichier à supprimer de Dropbox|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Extraire une archive dans un dossier    
Extrait un fichier d’archive dans un dossier de Dropbox (exemple : .zip). **```POST: /datasets/default/extractFolderV2```**

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|string|yes|query|(aucun) |Chemin du fichier d’archive|
|destination|string|yes|query|(aucun) |Chemin dans Dropbox indiquant où extraire le contenu de l’archive|
|overwrite|booléenne|no|query|(aucun) |Remplace les fichiers de destination si la valeur est « true »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir le contenu d’un fichier à l’aide de l’identifiant    
Récupère le contenu d’un fichier de Dropbox à partir de son identifiant. ```GET: /datasets/default/files/{id}/content```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier dans Dropbox|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir le contenu d’un fichier à l’aide du chemin    
Récupère le contenu d’un fichier de Dropbox à l’aide du chemin. ```GET: /datasets/default/GetFileContentByPath```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|(aucun) |Chemin unique du fichier dans Dropbox|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier à l’aide de l’identifiant    
Récupère les métadonnées d’un fichier de Dropbox à l’aide de son identifiant. ```GET: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|(aucun) |Identificateur unique du fichier dans Dropbox|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Obtenir les métadonnées d’un fichier à l’aide du chemin    
Récupère les métadonnées d’un fichier de Dropbox à l’aide du chemin. ```GET: /datasets/default/GetFileByPath```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|(aucun) |Chemin unique du fichier dans Dropbox|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Mettre à jour un fichier    
Met à jour un fichier dans Dropbox. ```PUT: /datasets/default/files/{id}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|id|string|yes|path| (aucun)|Identificateur unique du fichier à mettre à jour dans Dropbox|
|body|chaîne (binaire) |yes|body|(aucun) |Contenu du fichier à mettre à jour dans Dropbox|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Quand un fichier est modifié    
Déclenche un flux quand un fichier est modifié dans un dossier Dropbox. ```GET: /datasets/default/triggers/onupdatedfile```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderId|string|yes|query|(aucun) |Identificateur unique du dossier dans Dropbox|

#### Response
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
|Path|string|no|
|LastModified|string|no|
|Taille|integer|no|
|MediaType|string|no|
|IsFolder|booléenne|no|
|ETag|string|no|
|FileLocator|string|no|

## Étapes suivantes
Après avoir ajouté l’API Dropbox à PowerApps Enterprise, [donnez aux utilisateurs des autorisations](../power-apps/powerapps-manage-api-connection-user-access.md) pour qu’ils puissent utiliser l’API dans leurs applications.

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/create-api-dropbox/dropbox-developer-site.png
[9]: ./media/create-api-dropbox/dropbox-create-app.png
[10]: ./media/create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0224_2016-->