<properties
pageTitle="RSS | Microsoft Azure"
description="Créer des applications logiques avec Azure App Service. Le connecteur RSS permet aux utilisateurs de publier et d’extraire des éléments de flux. Il permet également aux utilisateurs de déclencher des opérations lorsqu’un nouvel élément est publié dans le flux."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="04/29/2016"
ms.author="deonhe"/>

# Prise en main du connecteur RSS



Le connecteur RSS peut être utilisé à partir de :

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flux](http://flows.microsoft.com)  

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Vous pouvez commencer en créant une application logique. Consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

Le connecteur RSS peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur RSS met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions de RSS
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|Obtenir tous les éléments du flux RSS.|
### Déclencheurs de RSS
Vous pouvez écouter les événements suivants :

|Déclencheur | Description|
|--- | ---|
|Quand un nouvel élément de flux est publié|Déclenche un flux de travail lors de la publication d’un nouveau flux|


## Créer une connexion à RSS
Pour créer des applications logiques avec RSS, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

|Propriété| Requis|Description|
| ---|---|---|
Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d'autres applications logiques.

## Référence pour RSS
S’applique à la version : 1.0

## OnNewFeed
Quand un nouvel élément de flux est publié : déclenche un flux de travail lors de la publication d’un nouveau flux

```GET: /OnNewFeed```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|feedUrl|string|yes|query|(aucun)|Feed url|

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


## ListFeedItems
Obtenir tous les éléments du flux RSS : obtient tous les éléments du flux RSS

```GET: /ListFeedItems```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|feedUrl|string|yes|query|(aucun)|Feed url|

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


## Définitions d’objet 

### TriggerBatchResponse[FeedItem]


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|value|array|Non |



### FeedItem


| Nom de la propriété | Type de données | Requis |
|---|---|---|
|id|string|Oui |
|title|string|Oui |
|Contenu|string|Oui |
|links|array|Non |
|updatedOn|string|Non |


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->