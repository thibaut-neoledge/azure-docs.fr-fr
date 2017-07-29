---
title: "Azure CosmosDB : Unités de requête par minute (RU/m) | Microsoft Docs"
description: "Découvrez comment réduire les coûts grâce aux unités de requête par minute."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 0c597fa4afa816f9731edb744ae494b6ef928b9e
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---
# <a name="request-units-per-minute-in-azure-cosmos-db"></a>Unités de requête par minute dans Azure Cosmos DB

Azure Cosmos DB est conçu pour vous aider à obtenir des performances rapides et prévisibles, et à monter en charge en toute transparence parallèlement à l’évolution de votre application. Vous pouvez configurer le débit sur un conteneur Cosmos DB sur les granularités par minute (RU/m), par seconde ou les deux. Le débit configuré au niveau de granularité par minute est utilisé pour gérer les pics inattendus de la charge de travail en cours survenant à un niveau de granularité par seconde. 

Cet article fournit une vue d’ensemble du fonctionnement de la configuration des unités de requête par minute (RU/m). L’objectif à garder avec l’approvisionnement de RU/m est de fournir des performances prévisibles pour des besoins imprévisibles (surtout si vous devez exécuter des analyses sur vos données opérationnelles) et les charges de travail qui connaissent des pointes. Nous voulons que nos clients consomment plus le débit qu’ils configurent afin qu’ils puissent mettre à l’échelle rapidement avec tranquillité d’esprit.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

* Comment une unité de requête par minute fonctionne-t-elle ?
* Quelle est la différence entre les unités de requête par minute et par seconde ?
* Comment configurer les RU/m ?
* Dans quels cas dois-je envisager d’approvisionner des unités de demande par minute ?
* Comment utiliser les métriques du portail pour optimiser les coûts et les performances ?
* Définir le type de requête qui consomme le budget RU/m ?

## <a name="provisioning-request-units-per-minute-rum"></a>Configuration des unités de requête par minute (RU/m)

Lorsque vous configurez Azure Cosmos DB au niveau de granularité par seconde (RU/s), vous obtenez la garantie que votre demande est traitée à faible latence si le débit n’a pas dépassé la capacité configurée dans cette seconde. Avec les RU/m, la granularité est à la minute avec la garantie que votre requête sera traitée dans cette minute. Par rapport aux systèmes de débordement (bursting), nous nous assurons que les performances que vous obtenez sont prévisibles et vous permettent de planifier correctement.

La façon dont la configuration par minute fonctionne est simple :

* Les RU/m sont facturées à l’heure et en plus des RU/s. Pour plus d’informations, consultez la [page de tarification](https://aka.ms/acdbpricing) d’Azure Cosmos DB.
* Les RU/m peuvent être activées au niveau de la collection. Cela peut être effectué via les kits de développement logiciel (Node.js, Java ou .Net) ou via le portail (qui comprend également les charges de travail de l’API MongoDB)
* Lorsque les RU/m sont activées, pour 100 RU/s configurées, vous obtenez également 1 000 RU/m configurées (le rapport est de 10x)
* À une seconde donnée, une unité de requête consomme votre configuration de RU/m uniquement si vous avez dépassé votre configuration par seconde pendant cette seconde
* Une fois la période de 60 secondes (UTC) terminée, la configuration par minute est à nouveau remplie
* Les RU/m peuvent être activées uniquement pour les collections avec une configuration maximale de 5 000 RU/s par partition. Si vous mettez à l’échelle de vos besoins de débit avec un haut niveau de configuration par partition, vous obtiendrez un message d’avertissement

Vous trouverez ci-dessous un exemple concret, dans lequel un client peut configurer 10 kRU/s avec 100 kRU/m, et économiser 73 % des coûts par rapport à la configuration pour les pointes (à 50 kRU/s) sur une période de 90 secondes sur une collection qui a une configuration de 10 000 RU/s et 100 000 RU/m :

* 1re seconde : le budget de RU/m est défini sur 100 000
* 3e seconde : au cours de cette seconde, la consommation d’unités de requête a été de 11 010 RU, soit 1 010 RU au-dessus de l’approvisionnement en RU/s. Par conséquent, 1,010 RU sont déduites du budget de RU/m. 98 990 RU sont disponibles pour les prochaines 57 secondes dans le budget de RU/m
* 29e seconde : au cours de cette seconde, un pic important (>4x plus élevé que la configuration par seconde) est survenu, et la consommation d’unités de requête a été de 46 920 RU. 36 920 RU sont déduites du budget de RU/m, qui passe de 92 323 RU (28e seconde) à 55 403 RU (29e seconde)
* 61e seconde : le budget de RU/m budget est à nouveau défini sur 100 000 unités de requête.
 
![Graphique montrant la consommation et l’approvisionnement d’Azure Cosmos DB](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute.png)

## <a name="specifying-request-unit-capacity-with-rum"></a>Spécification de la capacité d’unités de requête avec RU/m

Quand vous créez une collection Azure Cosmos DB, vous spécifiez le nombre d’unités de requête (RU par seconde) à réserver pour la collection. Vous pouvez également décider si vous souhaitez ajouter des RU par minute. Cela est possible via le portail ou le kit de développement logiciel. 

### <a name="through-the-portal"></a>Par le biais du portail

L’activation ou désactivation des unités de requête par minute nécessite simplement un clic lors de la configuration d’une collection. 

 ![Capture d’écran montrant comment définir les RU/m dans le portail Azure](./media/request-units-per-minute/azure-cosmos-db-request-unit-per-minute-portal.png)

### <a name="through-the-sdk"></a>Via le kit de développement logiciel
Tout d’abord, il est important de noter que les RU/m sont disponibles uniquement pour les kits de développement suivants :

* .Net 1.14.0
* Java 1.11.0
* Node.js 1.12.0
* Python 2.2.0

L’extrait de code suivant permet de créer une collection avec 3 000 unités de requête par seconde et 30 000 unités de requête par minute à l’aide du SDK .NET :

```csharp
// Create a collection with RU/m enabled
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

// Set the throughput to 3,000 request units per second which will give you 30,000 request units per minute as the RU/m budget
await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000, OfferEnableRUPerMinuteThroughput = true });
```

L’extrait de code suivant permet de changer le débit d’une collection en 5 000 unités de requête par seconde sans configurer de RU par minute à l’aide du SDK .NET :

```csharp
// Get the current offer
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to 5000 request units per second without RU/m enabled (the last parameter to OfferV2 constructor below)
OfferV2 offerV2 = new OfferV2(offer, 5000, false);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offerV2);
```

## <a name="good-fit-scenarios"></a>Scénarios adaptés

Dans cette section, nous fournissons une vue d’ensemble des scénarios qui sont adaptés à l’activation des unités de demande par minute.

**Environnement de développement/test :** convient bien. Durant la phase de développement, si vous testez votre application avec différentes charges de travail, les RU/m peuvent fournir la flexibilité nécessaire à ce stade. [L’émulateur](local-emulator.md) est un excellent outil gratuit pour tester Azure Cosmos DB. Toutefois, si vous souhaitez démarrer dans un environnement cloud, vous aurez besoin d’une grande flexibilité avec des RU/m pour vos besoins de performances ad hoc. Vous passerez le plus de temps à développer, et moins à vous inquiéter des exigences de performances au départ. Nous recommandons de commencer avec la configuration minimale de RU/s et d’activer les RU/m.

**Besoins de granularité imprévisibles, à pics, par minute :** Bien adapté – Économies : 25 à 75 %. Nous avons vu une nette amélioration des RU/m, et la plupart des scénarios de production entrent dans ce groupe. Si vous avez une charge de travail IoT qui connaît plusieurs pics par minute, si vous avez des requêtes en cours d’exécution lorsque votre système effectue une insertion en masse en même temps, vous aurez besoin de capacité supplémentaire pour gérer les pointes. Nous vous recommandons d’optimiser vos besoins en ressources en appliquant notre approche étape par étape ci-dessous.

 ![Graphique indiquant la consommation de requête dans une granularité de 5 minutes](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-consumption.png)
 
 *Figure - Test d’évaluation de la consommation de RU*

**Tranquillité d’esprit :** Bien adapté - Économies : 10-20 %. Parfois, vous voulez simplement avoir plus de tranquillité d’esprit et ne pas avoir à vous inquiéter des pics et des limitations. Cette fonctionnalité est celle qu’il vous faut. Dans ce cas, nous vous recommandons d’activer les RU/m et de légèrement réduire votre approvisionnement par seconde. Ce cas est différent du précédent, car vous ne tentez pas d’optimiser agressivement votre configuration. Vous êtes davantage dans une mentalité « Zéro limite ».

Les opérations critiques avec des besoins ad hoc : nous vous recommandons parfois de laisser uniquement les opérations critiques accéder au budget de RU/m, le budget n’est donc pas consommé par les opérations ad hoc ou moins importantes. Vous pouvez facilement les définir dans la section ci-dessous.

## <a name="using-the-portal-metrics-to-optimize-cost-and-performance"></a>Utilisation des métriques du portail pour optimiser les performances et les coûts

**Dans les semaines à venir, nous développerons davantage le contenu concernant l’analyse de la consommation de RU/m pour optimiser vos besoins de débit.**

Via les métriques du portail, vous pouvez voir combien de RU/s normales vous consommez par rapport aux RU/m. La surveillance de ces métriques devrait vous aider à optimiser votre configuration. 

Nous vous recommandons une approche étape par étape pour utiliser les RU/m à votre avantage. Pour chaque étape, vous devez avoir une vue d’ensemble de la consommation de RU représentant un cycle complet de votre charge de travail (il peut s’agir d’heures, de jours ou de semaines) et obtenir des informations sur l’utilisation de ce que vous configurez.

Le principe de cette approche consiste à rendre votre configuration de débit aussi proche que possible que celle d’un point de configuration qui correspond à vos critères de performances ci-dessous. 

![Graphique indiquant la consommation de requête dans une granularité de 5 minutes](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-adjust-provisioning.png)
 
Pour comprendre le point de configuration optimal pour votre charge de travail, vous devez comprendre :

* Les modèles de consommation : pics inexistants, peu fréquents ou courants ? Pics petits (2x en moyenne), moyens ou grands en taille (>10x en moyenne) ?
* Pourcentage de demandes limitées : restez-vous à l’aise même si vous avez un peu de limitation ? Si oui, dans quelle mesure ? 

Une fois que vous avez identifié vos objectifs, vous serez en mesure de vous rapprocher de la configuration optimale.

Pour vous aider, nous voulons fournir une aide générale sur la façon d’optimiser votre configuration en fonction de votre consommation de RU/m. Ce guide ne s’applique pas à tous les types de charges de travail, mais est basé sur nos connaissances de la version préliminaire. Nous pourrions modifier ces lignes de base lorsque nous en apprendrons davantage :

|Utilisation de % de RU/m|Degré d’utilisation des RU/m|Actions recommandées pour la configuration|
|---|---|---|
|0-1%|Sous-utilisation|Réduire les RU/s pour consommer plus de RU/m|
|1-10%|Utilisation normale|Conserver le même niveau de configuration|
|Supérieur à 10 %|Utilisation excessive|Augmenter les RU/s pour compter moins sur les RU/m|

## <a name="select-which-operations-can-consume-the-rum-budget"></a>Sélectionner les opérations qui peuvent consommer le budget de RU/m

Au niveau de la requête, vous pouvez également activer/désactiver le budget RU/m pour répondre à la demande, quel que soit le type d’opération. Si le budget de RU/s configuré est consommé et que la requête ne peut pas consommer le budget de RU/m, cette demande est limitée. Par défaut, toute requête est servie par le budget de RU/m si le budget de débit de RU/m est activé. 

Voici un extrait de code pour la désactivation du budget de RU/m avec l’API DocumentDB pour CRUD et les opérations de requête.

```csharp
// In order to disable any CRUD request for RU/m, set DisableRUPerMinuteUsage to true in RequestOptions
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    new Document { Id = "Cosmos DB" },
    new RequestOptions { DisableRUPerMinuteUsage = true });
// In order to disable any query request for RU/m, set DisableRUPerMinuteOnRequest to true in RequestOptions
FeedOptions feedOptions = new FeedOptions();
feedOptions.DisableRUPerMinuteUsage = true;
var query = client.CreateDocumentQuery<Book>(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    "select * from c",feedOptions).AsDocumentQuery();
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons décrit le fonctionnement du partitionnement dans Azure Cosmos DB, la création de collections partitionnées et la sélection d’une clé de partition adéquate pour votre application.

* Effectuez un test des performances et de la mise à l’échelle avec Azure Cosmos DB. Consultez la page [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md) pour obtenir un exemple.
* Commencez à coder avec les [Kits de développement logiciel (SDK)](documentdb-sdk-dotnet.md) ou [l’API REST](/rest/api/documentdb/).
* Informez-vous sur le [débit approvisionné](request-units.md) dans Azure Cosmos DB 


