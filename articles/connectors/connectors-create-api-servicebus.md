<properties
pageTitle="Utiliser le connecteur Azure Service Bus dans vos applications logiques | Microsoft Azure"
description="Utiliser le connecteur Azure Service Bus dans vos applications logiques Microsoft Azure App Service"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/23/2016"
ms.author="deonhe"/>

# Prise en main du connecteur Azure Service Bus 

Connectez-vous à Azure Service Bus pour envoyer et recevoir des messages. Vous pouvez effectuer des actions comme envoyer vers une file d'attente, envoyer vers une rubrique, recevoir d’une file d'attente, recevoir d'un abonnement, etc.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec Azure Service Bus, vous pouvez :

* Créer des applications logiques  

Pour ajouter une opération à des applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## À propos des déclencheurs et des actions

Le connecteur Azure Service Bus peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur Azure Service Bus met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions Azure Service Bus
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|SendMessage|Envoie un message à la file d'attente Azure Service Bus ou à une rubrique.|
### Déclencheurs Azure Service Bus
Vous pouvez écouter les événements suivants :

|Déclencheur | Description|
|--- | ---|
|GetMessageFromQueue|Obtient un nouveau message de la file d'attente Azure Service Bus.|
|GetMessageFromTopic|Obtient un nouveau message de l'abonnement à la rubrique Azure Service Bus.|


## Créer une connexion à Azure Service Bus
Pour utiliser le connecteur Azure Service Bus, vous devez créer une **connexion**, puis fournir les détails de ces propriétés :

>[AZURE.INCLUDE [Procédure de création d’une connexion à ServiceBus](../../includes/connectors-create-api-servicebus.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d'autres applications logiques.

## Référence de l’API REST d’Azure Service Bus
#### Cette documentation concerne la version 1.0.


### Envoie un message à la file d'attente Azure Service Bus ou à une rubrique.
**```POST: /{entityName}/messages```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|message| |yes|body|(aucun)|Message Service Bus|
|entityName|string|yes|path|(aucun)|Nom de la file d’attente ou de la rubrique|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Obtient un nouveau message de la file d'attente Azure Service Bus.
**```GET: /{queueName}/messages/head```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|queueName|string|yes|path|(aucun)|Nom de la file d’attente.|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



### Obtient un nouveau message de l'abonnement à la rubrique Azure Service Bus.
**```GET: /{topicName}/subscriptions/{subscriptionName}/messages/head```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|topicName|string|yes|path|(aucun)|Nom de la rubrique.|
|subscriptionName|string|yes|path|(aucun)|Nom de l'abonnement de rubrique.|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|
------



## Définition(s) d'objet : 

 **ServiceBusMessage** : message composé de contenu et de propriétés

Propriétés requises pour ServiceBusMessage :

ContentTransferEncoding

**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|ContentData|string|
|ContentType|string|
|ContentTransferEncoding|string|
|Propriétés|objet|


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0525_2016-->