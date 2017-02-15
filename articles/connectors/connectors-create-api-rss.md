---
title: RSS | Microsoft Docs
description: "Créez des applications logiques avec Azure App Service. Le connecteur RSS permet aux utilisateurs de publier et d’extraire des éléments de flux. Il permet également aux utilisateurs de déclencher des opérations lorsqu’un nouvel élément est publié dans le flux."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 5e13e126fecda66a453b4ced619016121af98b2c


---
# <a name="get-started-with-the-rss-connector"></a>Prise en main du connecteur RSS
RSS est un format de syndication populaire web utilisé pour publier du contenu fréquemment mise à jour, tel que des entrées de blog et des titres d’actualité.  De nombreux éditeurs de contenu fournissent un flux RSS pour permettre aux utilisateurs d’y souscrire.  Le connecteur RSS permet de récupérer le flux d’informations et de déclencher des flux lorsque de nouveaux éléments sont publiés dans un flux RSS.

> [!NOTE]
> Cette version de l’article s’applique à la version de schéma 2015-08-01-preview des applications logiques. 
> 
> 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur RSS peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le connecteur RSS met à votre disposition les actions et/ou les déclencheurs ci-après.

### <a name="rss-actions"></a>Actions de RSS
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| [ListFeedItems](connectors-create-api-rss.md#listfeeditems) |Obtenir tous les éléments du flux RSS. |

### <a name="rss-triggers"></a>Déclencheurs de RSS
Vous pouvez écouter les événements suivants :

| Déclencheur | Description |
| --- | --- |
| Quand un nouvel élément de flux est publié |Déclenche un flux de travail lors de la publication d’un nouveau flux |

## <a name="create-a-connection-to-rss"></a>Créer une connexion à RSS
> [!INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]
> 
> [!TIP]
> Vous pouvez utiliser cette connexion dans d'autres applications logiques.
> 
> 

## <a name="reference-for-rss"></a>Référence pour RSS
S’applique à la version 1.0.

## <a name="onnewfeed"></a>OnNewFeed
Quand un nouvel élément de flux est publié : déclenche un flux de travail lors de la publication d’un nouveau flux 

```GET: /OnNewFeed``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| feedUrl |string |yes |query |(aucun) |Feed url |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="listfeeditems"></a>ListFeedItems
Obtenir tous les éléments du flux RSS : obtient tous les éléments du flux RSS 

```GET: /ListFeedItems``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| feedUrl |string |yes |query |(aucun) |Feed url |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d’objet
### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse[FeedItem]
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| value |array |Non |

### <a name="feeditem"></a>FeedItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Oui |
| title |string |Oui |
| Contenu |string |Oui |
| links |array |Non |
| updatedOn |string |Non |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Feb17_HO2-->


