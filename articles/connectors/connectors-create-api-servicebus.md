<properties
pageTitle="Découvrez comment utiliser le connecteur Azure Service Bus dans vos applications logiques | Microsoft Azure"
description="Créez des applications logiques avec Azure App Service. Connectez-vous à Azure Service Bus pour envoyer et recevoir des messages. Vous pouvez effectuer des actions comme envoyer vers une file d'attente, envoyer vers une rubrique, recevoir d’une file d'attente et recevoir d'un abonnement."
services="logic-apps"
documentationCenter=".net,nodejs,java" 	
authors="msftman"
manager="erikre"
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/02/2016"
ms.author="deonhe"/>

# Prise en main du connecteur Azure Service Bus

Connectez-vous à Azure Service Bus pour envoyer et recevoir des messages. Vous pouvez effectuer des actions comme envoyer vers une file d'attente, envoyer vers une rubrique, recevoir d’une file d'attente et recevoir d'un abonnement.

Pour utiliser [n’importe quel connecteur](./apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Se connecter à Service Bus

Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une connexion à celui-ci. Une [connexion](./connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service.

>[AZURE.INCLUDE [Procédure de création d’une connexion à Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]

## Utiliser un déclencheur Service Bus

Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Procédure de création d’un déclencheur Service Bus](../../includes/connectors-create-api-servicebus-trigger.md)]

## Utiliser une action Service Bus

Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Procédure de création d’une action Service Bus](../../includes/connectors-create-api-servicebus-action.md)]

## Détails techniques

Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion.

### Déclencheurs Service Bus

Service Bus comporte les déclencheurs suivants :

|Déclencheur | Description|
|--- | ---|
|[Réception d’un message dans une file d’attente](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|Cette opération déclenche un flux lorsqu’un message est reçu dans une file d’attente.|
|[When a message is received in a topic subscription (Quand un message est reçu dans un abonnement à une rubrique)](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|Cette opération déclenche un flux lorsqu’un message est reçu dans un abonnement à une rubrique.|


### Actions Service Bus

Service Bus comporte les actions suivantes :


|Action|Description|
|--- | ---|
|[Envoyer un message](connectors-create-api-servicebus.md#send-message)|Cette opération envoie un message à une file d’attente ou à une rubrique.|
### Détails des actions et déclencheurs

Voici les détails des actions et des déclencheurs de ce connecteur, ainsi que leurs réponses.



#### Envoyer un message

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|ContentData*|Contenu|Contenu du message.|
|ContentType|Type de contenu|Type du contenu du message.|
|Propriétés|Propriétés|Paires clé-valeur pour chaque propriété répartie.|
|entityName*|Queue/Topic name (Nom de la file d’attente/rubrique)|Nom de la file d’attente ou de la rubrique.|

Les paramètres avancés suivants sont également disponibles :

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|MessageId|ID du message|Valeur définie par l’utilisateur que Service Bus peut utiliser pour identifier les messages en double, si cette fonctionnalité est activée.|
|À|À|Adresse de destination.|
|ReplyTo|Adresse de réponse|Adresse de la file d’attente à laquelle répondre.|
|ReplyToSessionId|ID de session de réponse|Identificateur de la session à laquelle répondre.|
|Étiquette|Étiquette|Étiquette spécifique à l’application.|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|Date et heure, en temps universel coordonné, auxquelles le message sera ajouté à la file d’attente.|
|SessionId|ID de la session|Identificateur de la session.|
|CorrelationId|ID de corrélation|Identificateur de la corrélation.|
|TimeToLive|Durée de vie|Durée, en nombre de cycles, pendant laquelle un message est valide. Le début de cette durée correspond au moment de l’envoi du message à Service Bus.|



Le caractère * indique qu’une propriété est obligatoire.


#### Réception d’un message dans une file d’attente

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|queueName*|Nom de la file d'attente|Nom de la file d’attente.|


Le caractère * indique qu’une propriété est obligatoire.


##### Détails des résultats

ServiceBusMessage : cet objet présente le contenu et les propriétés d’un message Service Bus.


| Nom de la propriété | Type de données | Description |
|---|---|---|
|ContentData|string|Contenu du message.|
|ContentType|string|Type du contenu du message.|
|Propriétés|objet|Paires clé-valeur pour chaque propriété répartie.|
|MessageId|string|Valeur définie par l’utilisateur que Service Bus peut utiliser pour identifier les messages en double, si cette fonctionnalité est activée.|
|À|string|Adresse de destination.|
|ReplyTo|string|Adresse de la file d’attente à laquelle répondre.|
|ReplyToSessionId|string|Identificateur de la session à laquelle répondre.|
|Étiquette|string|Étiquette spécifique à l’application.|
|ScheduledEnqueueTimeUtc|string|Date et heure, en temps universel coordonné, auxquelles le message sera ajouté à la file d’attente.|
|SessionId|string|Identificateur de la session.|
|CorrelationId|string|Identificateur de la corrélation.|
|TimeToLive|string|Durée, en nombre de cycles, pendant laquelle un message est valide. Le début de cette durée correspond au moment de l’envoi du message à Service Bus.|




#### When a message is received in a topic subscription (Quand un message est reçu dans un abonnement à une rubrique)

|Nom de la propriété| Nom complet|Description|
| ---|---|---|
|topicName*|Nom de la rubrique|Nom de la rubrique.|
|subscriptionName*|Topic subscription name (Nom d’abonnement à la rubrique)|Nom de l'abonnement de rubrique.|


Le caractère * indique qu’une propriété est obligatoire.


##### Détails des résultats

ServiceBusMessage : cet objet présente le contenu et les propriétés d’un message Service Bus.


| Nom de la propriété | Type de données | Description |
|---|---|---|
|ContentData|string|Contenu du message.|
|ContentType|string|Type du contenu du message.|
|Propriétés|objet|Paires clé-valeur pour chaque propriété répartie.|
|MessageId|string|Valeur définie par l’utilisateur que Service Bus peut utiliser pour identifier les messages en double, si cette fonctionnalité est activée.|
|À|string|Adresse de destination.|
|ReplyTo|string|Adresse de la file d’attente à laquelle répondre.|
|ReplyToSessionId|string|Identificateur de la session à laquelle répondre.|
|Étiquette|string|Étiquette spécifique à l’application.|
|ScheduledEnqueueTimeUtc|string|Date et heure, en temps universel coordonné, auxquelles le message sera ajouté à la file d’attente.|
|SessionId|string|Identificateur de la session.|
|CorrelationId|string|Identificateur de la corrélation.|
|TimeToLive|string|Durée, en nombre de cycles, pendant laquelle un message est valide. Le début de cette durée correspond au moment de l’envoi du message à Service Bus.|



### Réponses HTTP

Les actions et déclencheurs précédents peuvent renvoyer un ou plusieurs des codes d’état HTTP suivants :

|Name|Description|
|---|---|
|200|OK|
|202|Acceptée|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|

## Étapes suivantes
[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!-----HONumber=AcomDC_0810_2016-->
