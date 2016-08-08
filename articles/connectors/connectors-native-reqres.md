<properties
	pageTitle="Utilisation d’actions de requête et de réponse | Microsoft Azure"
	description="Présentation des déclencheurs et des actions de requête et de réponse dans une application logique Azure"
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
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Familiarisation avec les composants de requête et de réponse

Avec les composants de requête et réponse dans une application logique, vous pouvez répondre en temps réel aux événements, notamment :

- répondre à une requête HTTP avec des données d’une base de données locale par le biais d’une application logique ;
- déclencher une application logique à partir d’un événement webhook externe ;
- appeler une application logique avec une action de requête et de réponse depuis une autre application logique.

Pour commencer à utiliser les actions de requête et de réponse dans une application logique, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Utilisation d’un déclencheur de requête HTTP

Un déclencheur est un événement qui peut être utilisé pour lancer le workflow défini dans une application logique. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de configuration d’une requête HTTP dans le concepteur d’application logique.

1. Ajoutez le déclencheur **Requête quand une demande HTTP est reçue** à votre application logique
	- Vous pouvez éventuellement fournir un schéma JSON (à l’aide d’un outil tel que [jsonschema.net](http://jsonschema.net)) pour le corps de texte de la requête. Ainsi, le concepteur peut générer des jetons pour les propriétés dans la requête HTTP.
1. Ajoutez une autre action afin d’enregistrer l’application logique
1. Après l’enregistrement, vous pouvez obtenir l’URL de la requête HTTP à partir de la carte de requête
1. Une requête HTTP POST (vous pouvez utiliser un outil tel que [Postman](https://www.getpostman.com/)) à l’URL déclenche l’application logique

>[AZURE.NOTE] Si vous ne définissez pas une action de réponse, une réponse `202 ACCEPTED` est immédiatement renvoyée à l’appelant. Vous pouvez utiliser l’action de réponse pour personnaliser une réponse.

![Déclencheur de réponse](./media/connectors-native-reqres/using-trigger.png)

## Utilisation d’une action Réponse HTTP
	
L’action Réponse HTTP est uniquement valide lorsqu’elle est utilisée dans un workflow déclenché par une requête HTTP. Si vous ne définissez pas une action de réponse, une réponse `202 ACCEPTED` est immédiatement renvoyée à l’appelant. Vous pouvez ajouter une action de réponse à tout moment dans le workflow. L’application logique conserve la requête entrante ouverte pendant 1 minute seulement pour attendre une réponse. Si aucune réponse n’a été envoyée après 1 minute depuis le workflow (et qu’une action de réponse existe dans la définition) un `504 GATEWAY TIMEOUT` sera envoyé à l’appelant. Voici comment ajouter une action Réponse HTTP :

1. Sélectionner le bouton **Nouvelle étape**
1. Choisissez **Ajouter une action**
1. Dans la zone de recherche Action, tapez « Response » pour répertorier l’action de réponse

	![Sélectionner une action de réponse](./media/connectors-native-reqres/using-action-1.png)

1. Ajoutez tout paramètre nécessaire au message de réponse HTTP

	![Exécuter l’action de réponse](./media/connectors-native-reqres/using-action-2.png)

1. Cliquez sur Enregistrer dans le coin supérieur gauche de la barre d’outils, et votre application logique est à la fois enregistrée et publiée (activation)

---

## Détails techniques

Vous trouverez ci-dessous les détails des déclencheurs et des actions de que ce connecteur prend en charge.

## Déclencheur de requête

Un déclencheur est un événement qui peut être utilisé pour lancer le workflow défini dans une application logique. [En savoir plus sur les déclencheurs.](connectors-overview.md) Il existe un seul déclencheur de requête.

|Déclencheur|Description|
|---|---|
|Demande|Quand une requête HTTP est reçue|

## Action de réponse

Une action est une opération effectuée par le workflow défini dans une application logique. [En savoir plus sur les actions.](connectors-overview.md) Il existe une action de réponse unique qui est utilisable uniquement lorsqu’elle est accompagnée d’un déclencheur de requête.

|Action|Description|
|---|---|
|Réponse|Renvoyer une réponse à requête HTTP corrélée|

### Détails de l’action

Vous trouverez ci-dessous plus d’informations sur chacune des actions, leurs champs obligatoires et facultatifs, et les détails des résultats correspondants associés à leur utilisation.

#### Déclencheur de requête
Déclencher à partir d’une requête HTTP entrante Une * signifie que le champ est obligatoire.

|Display Name|Nom de la propriété|Description|
|---|---|---|
|JSON Schema (Schéma JSON)|schema|Le schéma JSON du corps de texte de la requête HTTP|
<br>

**Détails des résultats**

Demande

|Nom de la propriété|Type de données|Description|
|---|---|---|
|En-têtes|objet|En-têtes de requête|
|Corps|objet|Objet Requête|

#### Action de réponse

Réponse HTTP Une * signifie que le champ est obligatoire.

|Display Name|Nom de la propriété|Description|
|---|---|---|
|Status Code (Code d’état)*|statusCode|Le code d’état HTTP|
|En-têtes|headers|Un objet JSON de tout en-tête de réponse à inclure|
|Corps|body|Le corps de texte de la réponse|

## Étapes suivantes

Vous trouverez ci-dessous plus d’informations sur la manière d’avancer le développement de vos applications logiques, ainsi que sur notre communauté.

## Créer une application logique

Essayer la plateforme et [créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) dès maintenant. Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->