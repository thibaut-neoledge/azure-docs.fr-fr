---
title: "Découvrez comment utiliser le connecteur Azure Service Bus dans vos applications logiques | Microsoft Docs"
description: "Créez des applications logiques avec Azure App Service. Connectez-vous à Azure Service Bus pour envoyer et recevoir des messages. Vous pouvez effectuer des actions comme envoyer vers une file d&quot;attente, envoyer vers une rubrique, recevoir d’une file d&quot;attente et recevoir d&quot;un abonnement."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 1e23402cbc63aeb262bfb471745589cc0bbd734f


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Prise en main du connecteur Azure Service Bus
Connectez-vous à Azure Service Bus pour envoyer et recevoir des messages. Vous pouvez effectuer des actions comme envoyer vers une file d'attente, envoyer vers une rubrique, recevoir d’une file d'attente et recevoir d'un abonnement.

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Se connecter à Service Bus
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une connexion à celui-ci. Une [connexion](connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service.  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>Utiliser un déclencheur Service Bus
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>Utiliser une action Service Bus
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="technical-details"></a>Détails techniques
Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion.

### <a name="service-bus-triggers"></a>Déclencheurs Service Bus
Service Bus comporte les déclencheurs suivants :  

| Déclencheur | Description |
| --- | --- |
| [Réception d’un message dans une file d’attente](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue) |Cette opération déclenche un flux lorsqu’un message est reçu dans une file d’attente. |
| [Réception d’un message dans un abonnement à une rubrique](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription) |Cette opération déclenche un flux lorsqu’un message est reçu dans un abonnement à une rubrique. |

### <a name="service-bus-actions"></a>Actions Service Bus
Service Bus comporte les actions suivantes :

| Action | Description |
| --- | --- |
| [Envoyer un message](connectors-create-api-servicebus.md#send-message) |Cette opération envoie un message à une file d’attente ou à une rubrique. |

### <a name="action-and-trigger-details"></a>Détails des actions et déclencheurs
Voici les détails des actions et des déclencheurs de ce connecteur, ainsi que leurs réponses.

#### <a name="send-message"></a>Envoyer un message
| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| ContentData* |Contenu |Contenu du message. |
| ContentType |Type de contenu |Type du contenu du message. |
| Propriétés |Propriétés |Paires clé-valeur pour chaque propriété répartie. |
| entityName* |Queue/Topic name (Nom de la file d’attente/rubrique) |Nom de la file d’attente ou de la rubrique. |

Les paramètres avancés suivants sont également disponibles :

| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| MessageId |ID du message |Valeur définie par l’utilisateur que Service Bus peut utiliser pour identifier les messages en double, si cette fonctionnalité est activée. |
| À |À |Adresse de destination. |
| ReplyTo |Adresse de réponse |Adresse de la file d’attente à laquelle répondre. |
| ReplyToSessionId |ID de session de réponse |Identificateur de la session à laquelle répondre. |
| Étiquette |Étiquette |Étiquette spécifique à l’application. |
| ScheduledEnqueueTimeUtc |ScheduledEnqueueTimeUtc |Date et heure, en temps universel coordonné, auxquelles le message sera ajouté à la file d’attente. |
| SessionId |ID de la session |Identificateur de la session. |
| CorrelationId |ID de corrélation |Identificateur de la corrélation. |
| TimeToLive |Durée de vie |Durée, en nombre de cycles, pendant laquelle un message est valide. Le début de cette durée correspond au moment de l’envoi du message à Service Bus. |

Le caractère * indique qu’une propriété est obligatoire.

#### <a name="when-a-message-is-received-in-a-queue"></a>Réception d’un message dans une file d’attente
| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| queueName* |Nom de la file d'attente |Nom de la file d’attente. |

Le caractère * indique qu’une propriété est obligatoire.

##### <a name="output-details"></a>Détails des résultats
ServiceBusMessage : cet objet présente le contenu et les propriétés d’un message Service Bus.

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| ContentData |string |Contenu du message. |
| ContentType |string |Type du contenu du message. |
| Propriétés |objet |Paires clé-valeur pour chaque propriété répartie. |
| MessageId |string |Valeur définie par l’utilisateur que Service Bus peut utiliser pour identifier les messages en double, si cette fonctionnalité est activée. |
| À |string |Adresse de destination. |
| ReplyTo |string |Adresse de la file d’attente à laquelle répondre. |
| ReplyToSessionId |string |Identificateur de la session à laquelle répondre. |
| Étiquette |string |Étiquette spécifique à l’application. |
| ScheduledEnqueueTimeUtc |string |Date et heure, en temps universel coordonné, auxquelles le message sera ajouté à la file d’attente. |
| SessionId |string |Identificateur de la session. |
| CorrelationId |string |Identificateur de la corrélation. |
| TimeToLive |string |Durée, en nombre de cycles, pendant laquelle un message est valide. Le début de cette durée correspond au moment de l’envoi du message à Service Bus. |

#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>When a message is received in a topic subscription (Quand un message est reçu dans un abonnement à une rubrique)
| Nom de la propriété | Nom complet | Description |
| --- | --- | --- |
| topicName* |Nom de la rubrique |Nom de la rubrique. |
| subscriptionName* |Topic subscription name (Nom d’abonnement à la rubrique) |Nom de l'abonnement de rubrique. |

Le caractère * indique qu’une propriété est obligatoire.

##### <a name="output-details"></a>Détails des résultats
ServiceBusMessage : cet objet présente le contenu et les propriétés d’un message Service Bus.

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| ContentData |string |Contenu du message. |
| ContentType |string |Type du contenu du message. |
| Propriétés |objet |Paires clé-valeur pour chaque propriété répartie. |
| MessageId |string |Valeur définie par l’utilisateur que Service Bus peut utiliser pour identifier les messages en double, si cette fonctionnalité est activée. |
| À |string |Adresse de destination. |
| ReplyTo |string |Adresse de la file d’attente à laquelle répondre. |
| ReplyToSessionId |string |Identificateur de la session à laquelle répondre. |
| Étiquette |string |Étiquette spécifique à l’application. |
| ScheduledEnqueueTimeUtc |string |Date et heure, en temps universel coordonné, auxquelles le message sera ajouté à la file d’attente. |
| SessionId |string |Identificateur de la session. |
| CorrelationId |string |Identificateur de la corrélation. |
| TimeToLive |string |Durée, en nombre de cycles, pendant laquelle un message est valide. Le début de cette durée correspond au moment de l’envoi du message à Service Bus. |

### <a name="http-responses"></a>Réponses HTTP
Les actions et déclencheurs précédents peuvent renvoyer un ou plusieurs des codes d’état HTTP suivants :

| Name | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).




<!--HONumber=Jan17_HO3-->


