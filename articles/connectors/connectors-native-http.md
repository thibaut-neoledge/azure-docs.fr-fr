<properties
	pageTitle="Ajout de l’action HTTP dans des applications logiques | Microsoft Azure"
	description="Vue d’ensemble de l’action HTTP et de ses propriétés"
	services=""
	documentationCenter=""
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# Prise en main de l’action HTTP

Avec l’action HTTP, vous pouvez étendre les workflows pour votre organisation et communiquer avec n’importe quel point de terminaison par le biais de HTTP.

Vous pouvez :

- Créez des workflows d’application logique qui s’activent (se déclenchent) lors d’une défaillance d’un site Web que vous gérez.
- Communiquez avec n’importe quel point de terminaison par le biais de HTTP afin d’étendre vos workflows à d’autres services.

Pour commencer à utiliser l’action HTTP dans une application logique, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Utilisation du déclencheur HTTP

Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de configuration du déclencheur HTTP dans le concepteur d’application logique.

1. Ajoutez le déclencheur HTTP dans votre application logique.
2. Renseignez les paramètres du point de terminaison HTTP que vous souhaitez interroger.
3. Modifiez l’intervalle de périodicité sur la fréquence d’interrogation souhaitée.
4. L’application logique se déclenche maintenant avec n’importe quel contenu retourné lors de chaque vérification.

![Déclencheur HTTP](./media/connectors-native-http/using-trigger.png)

### Fonctionnement du déclencheur HTTP

Le déclencheur HTTP effectue un appel sur un point de terminaison HTTP selon un intervalle récurrent. Par défaut, tout code de réponse HTTP inférieur à 300 entraîne l’exécution d’une application logique. Vous pouvez ajouter une condition en mode Code qui évalue l’appel HTTP afin de déterminer si l’application logique doit se déclencher. Voici un exemple de déclencheur HTTP qui se déclenche chaque fois que le code d’état renvoyé est supérieur ou égal à `400`.

```javascript
"Http":
{
	"conditions": [
		{
			"expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
		}
	],
	"inputs": {
		"method": "GET",
		"uri": "https://blogs.msdn.microsoft.com/logicapps/",
		"headers": {
			"accept-language": "en"
		}
	},
	"recurrence": {
		"frequency": "Second",
		"interval": 15
	},
	"type": "Http"
}
```

Consultez [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger) pour obtenir des informations complètes sur les paramètres du déclencheur HTTP.

## Utilisation de l’action HTTP

Une action est une opération effectuée par le flux de travail défini dans une application logique. [Apprenez-en davantage sur les actions](connectors-overview.md).

1. Sélectionnez le bouton **Nouvelle étape**.
2. Choisissez **Ajouter une action**.
3. Dans la zone de recherche Action , **http** pour répertorier l’action HTTP.

	![Sélection de l’action HTTP](./media/connectors-native-http/using-action-1.png)

4. Ajoutez tout paramètre nécessaire à l’appel HTTP.

	![Exécution de l’action HTTP](./media/connectors-native-http/using-action-2.png)

5. Cliquez en haut à gauche de la barre d’outils pour enregistrer. Votre application logique est maintenant en mode d’enregistrement et de publication (activation).

## Déclencheur HTTP

Voici les détails du déclencheur que ce connecteur prend en charge. Le connecteur HTTP possède un déclencheur.

|Déclencheur|Description|
|---|---|
|HTTP|Exécute un appel HTTP et renvoie le contenu de la réponse.|

## Action HTTP

Voici les détails de l’action que ce connecteur prend en charge. Le connecteur HTTP n’a qu’une seule action possible.

|Action|Description|
|---|---|
|HTTP|Exécute un appel HTTP et renvoie le contenu de la réponse.|

## Détails HTTP

Les tableaux suivants décrivent les champs de saisie obligatoires et facultatifs pour l’action, ainsi que les détails des résultats correspondants associés à son utilisation.


#### Demande HTTP
Vous trouverez ci-dessous les champs de saisie de l’action permettant de générer une demande HTTP sortante. Le symbole * désigne est un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Method (Méthode)*|statique|Verbe HTTP à utiliser|
|URI*|URI|URI de la requête HTTP|
|En-têtes|headers|Un objet JSON d’en-têtes HTTP à inclure|
|Corps|body|Le texte de la requête HTTP|
|Authentification|authentication|Détails contenus dans la section [Authentification](#authentication)|
<br>

#### Détails des résultats

Vous trouverez ci-dessous les détails de sortie correspondant à la requête HTTP.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|En-têtes|objet|En-têtes de réponse|
|Corps|objet|Objet Réponse|
|Code d’état|int|Code d'état HTTP|

## Authentification

La fonction Logic Apps d’Azure App Service vous permet d’utiliser différents types d’authentification sur vos points de terminaison HTTP. Vous pouvez utiliser cette authentification avec les connecteurs **HTTP**, **[HTTP + Swagger](./connectors-native-http-swagger.md)** et **[HTTP Webhook](./connectors-native-webhook.md)** Les types d’authentification suivants sont configurables :

* [Authentification de base](#basic-authentication)
* [Authentification par certificat client](#client-certificate-authentication)
* [Authentification OAuth Azure Active Directory (Azure AD)](#azure-active-directory-oauth-authentication)

#### Authentification de base

L’objet d’authentification suivant est obligatoire pour l’authentification de base. Le symbole * désigne est un champ obligatoire.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Entrez*|type|Type d’authentification (doit être `Basic` dans le cas d’une authentification de base)|
|Nom d’utilisateur*|username|Nom d’utilisateur utilisé pour l’authentification|
|Mot de passe*|password|Mot de passe à authentifier|

>[AZURE.TIP] Si vous souhaitez utiliser un mot de passe qui ne peut pas être récupéré à partir de la définition, utilisez un paramètre `securestring` et la [fonction de définition de flux de travail](http://aka.ms/logicappdocs) `@parameters()`.

Vous créez donc un objet comme celui-ci dans le champ d’authentification :

```javascript
{
	"type": "Basic",
	"username": "user",
	"password": "test"
}
```

#### Authentification par certificat client

L’objet d’authentification suivant est requis pour l’authentification du certificat client. Le symbole * désigne est un champ obligatoire.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Entrez*|type|Type d’authentification (doit être `ClientCertificate` pour les certificats client SSL)|
|PFX*|pfx|Contenu codé en Base64 du fichier Personal Information Exchange (PFX)|
|Mot de passe*|password|Mot de passe d’accès au fichier PFX|

>[AZURE.TIP] Vous pouvez utiliser un paramètre `securestring` et la [fonction de définition de flux de travail](http://aka.ms/logicappdocs) `@parameters()` pour utiliser un paramètre qui ne sera pas lisible dans la définition après l’enregistrement de votre application logique.

Par exemple :

```javascript
{
	"type": "ClientCertificate",
	"pfx": "aGVsbG8g...d29ybGQ=",
	"password": "@parameters('myPassword')"
}
```

#### Authentification OAuth Azure AD

L’objet d’authentification suivant est obligatoire pour l’authentification OAuth Azure AD. Le symbole * désigne est un champ obligatoire.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Entrez*|type|Type d’authentification (doit être `ActiveDirectoryOAuth` dans le cas d’une authentification OAuth Azure AD)|
|Locataire*|locataire|L’identifiant de locataire pour le locataire Azure AD|
|Public ciblé*|audience|Paramètre à définir sur `https://management.core.windows.net/`|
|ID de client*|clientId|Identifiant client de l’application Azure AD|
|Secret*|secret|Phrase secrète du client qui demande le jeton|

>[AZURE.TIP] Vous pouvez utiliser un paramètre `securestring` et la [fonction de définition de flux de travail](http://aka.ms/logicappdocs) `@parameters()` pour utiliser un paramètre qui ne sera pas lisible dans la définition après l’enregistrement.

Par exemple :

```javascript
{
	"type": "ActiveDirectoryOAuth",
	"tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
	"audience": "https://management.core.windows.net/",
	"clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
	"secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## Étapes suivantes

Essayez maintenant la plateforme et [créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->