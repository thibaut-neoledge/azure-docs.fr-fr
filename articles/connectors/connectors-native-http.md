<properties
	pageTitle="Ajout de l'action HTTP dans des applications logiques | Microsoft Azure"
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

- Créez des workflows d’application logique qui s’activent (se déclenchent) lorsqu’un site Web que vous gérez est défaillant.
- Communiquez avec n’importe quel point de terminaison par le biais de HTTP afin d’étendre vos workflows à d’autres services.

Pour commencer à utiliser l’action HTTP dans une application logique, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Utilisation d’un déclencheur HTTP

Un déclencheur est un événement qui peut être utilisé pour lancer le workflow défini dans une application logique. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de configuration d’un déclencheur HTTP dans le concepteur d’application logique.

1. Ajoutez le déclencheur HTTP dans votre application logique.
1. Renseignez les paramètres pour le point de terminaison HTTP que vous souhaitez interroger.
1. Modifiez l’intervalle de périodicité sur la fréquence d’interrogation souhaitée.
1. L’application logique se déclenchera maintenant avec tout contenu retourné lors de chaque vérification.

![Déclencheur HTTP](./media/connectors-native-http/using-trigger.png)

### Fonctionnement du déclencheur HTTP

Le déclencheur HTTP effectue un appel d’un point de terminaison HTTP en fonction d’un intervalle récurrent. Par défaut, tout code de réponse HTTP < 300 entraîne l’exécution d’une application logique. Vous pouvez ajouter une condition en mode Code qui évalue l’appel HTTP afin de déterminer si l’application logique doit se déclencher. Voici un exemple de déclencheur HTTP qui se déclenche chaque fois que le code d’état renvoyé est supérieur ou égal à `400`.

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

Vous trouverez plus de détails sur les paramètres de déclencheur HTTP [sur MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## Utilisation d’une action HTTP
	
Une action est une opération effectuée par le workflow défini dans une application logique. [En savoir plus sur les actions.](connectors-overview.md)

1. Sélectionner le bouton **Nouvelle étape**
1. Choisissez **Ajouter une action**
1. Dans la zone de recherche Action , tapez « HTTP » pour répertorier l’action HTTP

	![Sélectionner l'action HTTP](./media/connectors-native-http/using-action-1.png)

1. Ajoutez tout paramètre nécessaire à l’appel HTTP

	![Exécuter l’action HTTP](./media/connectors-native-http/using-action-2.png)

1. Cliquez sur Enregistrer dans le coin supérieur gauche de la barre d’outils, et votre application logique est à la fois enregistrée et publiée (activation)

---

## Détails techniques

Vous trouverez ci-dessous les détails des déclencheurs et des actions de que ce connecteur prend en charge.

## Déclencheurs HTTP

Un déclencheur est un événement qui peut être utilisé pour lancer le workflow défini dans une application logique. [En savoir plus sur les déclencheurs.](connectors-overview.md) Le connecteur HTTP a 1 déclencheur.

|Déclencheur|Description|
|---|---|
|HTTP|Exécuter un appel HTTP et obtenir le contenu de la réponse|

## Actions HTTP

Une action est une opération effectuée par le workflow défini dans une application logique. [En savoir plus sur les actions.](connectors-overview.md) Le connecteur HTTP a 1 action possible.

|Action|Description|
|---|---|
|HTTP|Exécuter un appel HTTP et obtenir le contenu de la réponse|

## Détails HTTP

Le connecteur HTTP est créé avec 1 action possible. Vous trouverez ci-dessous plus d’informations sur chacune des actions, leurs champs obligatoires et facultatifs, et les détails des résultats correspondants associés à leur utilisation.

### Demande HTTP
Exécuter une requête HTTP sortante. Une * signifie que le champ est obligatoire.

|Display Name|Nom de la propriété|Description|
|---|---|---|
|Method (Méthode)*|statique|Verbe HTTP à utiliser|
|URI*|URI|URI de la requête HTTP|
|En-têtes|headers|Un objet JSON d’en-têtes HTTP à inclure|
|Corps|body|Le texte de la requête HTTP|
|Authentification|authentication|[Plus de détails ici](#authentication)|
<br>

**Détails des résultats**

Réponse HTTP

|Nom de la propriété|Type de données|Description|
|---|---|---|
|En-têtes|objet|En-têtes de réponse|
|Corps|objet|Objet Réponse|
|Code d’état|int|Code d'état HTTP|

## Authentification

Les applications logiques vous permettent d’utiliser différents types d’authentification sur vos points de terminaison HTTP. Cette authentification peut être utilisée avec les connecteurs HTTP, [HTTP + Swagger](./connectors-native-http-swagger.md), et [HTTP Webhook](./connectors-native-webhook.md). Les types d’authentification suivants sont configurables :

* [Authentification de base](#basic-authentication)
* [Authentification ClientCertificate](#client-certificate-authentication)
* [Authentification ActiveDirectoryOAuth](#azure-active-directory-oauth-authentication)

#### Authentification de base

L’objet d’authentification suivant est obligatoire pour l’authentification de base : * signifie que le champ est obligatoire.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Entrez*|type|Type d'authentification. Pour l'authentification de base, la valeur doit être `Basic`|
|Nom d’utilisateur*|username|Nom d'utilisateur à authentifier|
|Mot de passe*|password|Mot de passe à authentifier|

>[AZURE.TIP] Si vous souhaitez utiliser un mot de passe qui ne peut pas être récupéré à partir de la définition, utilisez un paramètre `securestring` et la [fonction de définition de flux de travail](http://aka.ms/logicappdocs) `@parameters()`

Vous créez donc un objet comme celui-ci dans le champ d’authentification :

```javascript
{
	"type": "Basic",
	"username": "user",
	"password": "test"
}
```

#### Authentification par certificat client

L’objet d’authentification suivant est requis pour l’authentification du certificat client : * signifie que le champ est obligatoire.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Entrez*|type|Type d'authentification. Pour les certificats client SSL, la valeur doit être `ClientCertificate`|
|PFX*|pfx|Contenu codé en base64 du fichier PFX|
|Mot de passe*|password|Mot de passe pour accéder au fichier PFX|

>[AZURE.TIP] Vous pouvez utiliser un paramètre `securestring` et la [fonction de définition de flux de travail](http://aka.ms/logicappdocs) `@parameters()` pour utiliser un paramètre qui ne sera pas lisible dans la définition après l’enregistrement.

Par exemple :

```javascript
{
	"type": "ClientCertificate",
	"pfx": "aGVsbG8g...d29ybGQ=",
	"password": "@parameters('myPassword')"
}
```

#### Authentification Azure Active Directory OAuth

L’objet d’authentification suivant est requis pour l’authentification OAuth à Azure Active Directory : * signifie que le champ est obligatoire.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Entrez*|type|Type d'authentification. Pour ActiveDirectoryOAuth, la valeur doit être `ActiveDirectoryOAuth`|
|Locataire*|locataire|L’identifiant de locataire pour le locataire Azure AD|
|Public ciblé*|audience|Cette option est définie sur `https://management.core.windows.net/`.|
|ID de client*|clientId|Fournissez l’identifiant client pour l’application Azure AD|
|Secret*|secret|Secret du client qui demande le jeton|

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

---

## Étapes suivantes

Vous trouverez ci-dessous plus d’informations sur la manière d’avancer le développement de vos applications logiques, ainsi que sur notre communauté.

## Créer une application logique

Essayez la plateforme et [créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) dès maintenant. Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->