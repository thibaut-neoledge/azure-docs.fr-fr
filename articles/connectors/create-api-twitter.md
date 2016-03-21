<properties
	pageTitle="Ajouter l’API Twitter à PowerApps Enterprise et à des applications logiques | Microsoft Azure"
	description="Vue d’ensemble de l’API Twitter avec les paramètres de l’API REST"
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
   ms.date="03/03/2016"
   ms.author="mandia"/>


# Prendre en main l’API Twitter
Connectez-vous à Twitter pour publier un tweet, obtenir le fil d’un utilisateur et bien plus encore. L’API Twitter peut être utilisée à partir de :

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-twitter.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-twitter.md)

&nbsp;

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques. Pour la version de schéma 2014-12-01-preview, cliquez sur [Connecteur Twitter](../app-service-logic/app-service-logic-connector-twitter.md).

Avec Twitter, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez de Twitter. 
- Utiliser des déclencheurs quand il y a un nouveau tweet.
- Utiliser des actions pour publier un tweet, rechercher des tweets et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand un nouveau tweet s’affiche, vous pouvez le publier sur Facebook.
- Ajouter l’API Twitter à PowerApps Enterprise. Ensuite, vos utilisateurs peuvent utiliser cette API dans leurs applications. 

Pour plus d’informations sur l’ajout d’une API à PowerApps Enterprise, consultez [Inscrire une API dans PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Déclencheurs et actions
Twitter inclut les déclencheurs et les actions suivants.

Déclencheur | Actions
--- | ---
<ul><li>Quand un nouveau tweet apparaît</li></ul>| <ul><li>Publier un nouveau tweet</li><li>Quand un nouveau tweet apparaît</li><li>Obtenir un fil d’actualités</li><li>Obtenir un utilisateur</li><li>Obtenir un fil d’utilisateur</li><li>Rechercher un tweet</li><li>Obtenir des abonnés</li><li>Obtenir mes abonnés</li><li>Obtenir un abonnement</li><li>Obtenir mon abonnement</li></ul>

Toutes les API prennent en charge les données aux formats JSON et XML.


## Créer la connexion à Twitter

Quand vous ajoutez cette API à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Twitter.

1. Connectez-vous à votre compte Twitter.
2. Sélectionnez **Autoriser** et permettez à vos applications logiques de se connecter à votre compte Twitter et de l’utiliser. 

Après avoir créé la connexion, vous entrez les propriétés Twitter, telles que le texte d’un tweet. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion Twitter dans d’autres applications logiques.


## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### Publier un nouveau tweet 
Tweet. ```POST: /posttweet```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|tweetText|string|no|query|(aucun)|Texte à publier|
|body| chaîne (binaire) |no|body|(aucun)|Média à publier|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


### Quand un nouveau tweet apparaît 
Déclenche un flux de travail quand un nouveau tweet publié correspond à votre requête de recherche. ```GET: /onnewtweet```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|searchQuery|string|yes|query|(aucun)|Texte de requête (vous pouvez utiliser les opérateurs de requête pris en charge par Twitter : http://www.twitter.com/search)|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|202|Acceptée|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


### Obtenir un fil d’actualités 
Récupère les tweets et retweets les plus récents publiés par mes abonnés et moi-même. ```GET: /hometimeline```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|Nombre maximal de tweets à récupérer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


### Obtenir un utilisateur 
Récupère les détails de l’utilisateur spécifié (exemple : nom d’utilisateur, description, nombre d’abonnés, etc.). ```GET: /user```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|userName|string|yes|query|(aucun)|Nom d’utilisateur de l’utilisateur|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


### Obtenir un fil d’utilisateur 
Récupère une collection des tweets les plus récents publiés par l’utilisateur spécifié. ```GET: /usertimeline```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|userName|string|yes|query|(aucun)|Nom d’utilisateur Twitter|
|maxResults|integer|no|query|20|Nombre maximal de tweets à récupérer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


### Rechercher un tweet 
Récupère une collection de tweets pertinents correspondant à une requête spécifiée. ```GET: /searchtweets```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|searchQuery|string|yes|query|(aucun)|Texte de requête (vous pouvez utiliser les opérateurs de requête pris en charge par Twitter : http://www.twitter.com/search)|
|maxResults|integer|no|query|20|Nombre maximal de tweets à récupérer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


### Obtenir des abonnés 
Récupère les utilisateurs qui suivent l’utilisateur spécifié. ```GET: /followers```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|userName|string|yes|query|(aucun)|Nom d’utilisateur de l’utilisateur|
|maxResults|integer|no|query|20|Nombre maximal d’utilisateurs à récupérer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


### Obtenir mes abonnés 
Récupère les utilisateurs qui me suivent. ```GET: /myfollowers```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|Nombre maximal d’utilisateurs à récupérer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


### Obtenir un abonnement 
Récupère les utilisateurs suivis par l’utilisateur spécifié. ```GET: /friends```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|userName|string|yes|query|(aucun)|Nom d’utilisateur de l’utilisateur|
|maxResults|integer|no|query|20|Nombre maximal d’utilisateurs à récupérer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


### Obtenir mon abonnement 
Récupère les utilisateurs que je suis. ```GET: /myfriends```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|Nombre maximal d’utilisateurs à récupérer|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|


## Définitions d’objet

### TweetModel : représentation d’un objet tweet

| Nom de la propriété | Type de données | Requis |
|---|---| --- | 
|TweetText|string|yes|
|TweetId|string|no|
|CreatedAt|string|no|
|RetweetCount|integer|yes|
|TweetedBy|string|yes|
|MediaUrls|array|no|

### UserDetailsModel : représentation des détails d’un utilisateur Twitter

|Nom de la propriété | Type de données | Requis |
|---|---|---|
|FullName|string|yes|
|Emplacement|string|yes|
|ID|integer|no|
|Nom d’utilisateur|string|yes|
|FollowersCount|integer|no|
|Description|string|yes|
|StatusesCount|integer|no|
|FriendsCount|integer|no|

### TweetResponseModel : modèle de représentation d’un tweet publié

| Nom | Type de données | Requis |
|---|---|---|
|TweetId|string|yes|

### TriggerBatchResponse[TweetModel]

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|value|array|no|


## Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenir à la [liste des API](apis-list.md).

<!--References-->

[6]: ./media/create-api-twitter/twitter-apps-page.png
[7]: ./media/create-api-twitter/twitter-app-create.png

<!---------HONumber=AcomDC_0309_2016-->