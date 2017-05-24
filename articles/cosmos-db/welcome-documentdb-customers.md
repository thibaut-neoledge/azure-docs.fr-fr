---
title: Clients DocumentDB, bienvenue dans Azure Cosmos DB | Documents Microsoft
description: "En savoir plus sur l’annonce faite à //build 2017, annonçant que les clients DocumentDB sont désormais des clients Azure Cosmos DB."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.devlang: n/a
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/12/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f60c3502ed51424647088fa8109f13cabc4e73da
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017

---

# <a name="welcome-documentdb-customers-to-azure-cosmos-db"></a>Bienvenue aux clients DocumentDB dans Azure Cosmos DB

Clients DocumentDB, nous sommes heureux de vous informer que vous faites désormais partie de la famille Azure Cosmos DB ! 

Azure Cosmos DB, [annoncé à la conférence Microsoft Build 2017](https://azure.microsoft.com/blog/azure-cosmos-db-microsofts-globally-distributed-multi-model-database-service/), est le premier service de base de données mondialement distribué vous permettant de mettre à l’échelle de manière flexible le débit et le stockage pour le nombre de régions de votre choix, tout en garantissant une latence faible, une haute disponibilité et cohérence : reposant sur les contrats SLA les plus complets du secteur et prenant en charge une multitude de modèles de données et d’API. 

![Azure Cosmos DB est le service de base de données distribué mondialement de Microsoft qui propose une augmentation de la taille des instances, une faible latence, cinq modèles de cohérence et des contrats SLA offrant des garanties complètes](./media/welcome-documentdb-customers/azure-cosmos-db.png)

L’une des API qu’Azure Cosmos DB prend en charge est l’API DocumentDB et le modèle de données de document. Vous êtes déjà familiarisé avec les API de DocumentDB. Ce sont les API que vous utilisez déjà actuellement pour exécuter vos applications DocumentDB. Ces API ne changent _pas_ : le package NuGet, les espaces de noms et toutes les dépendances restent les mêmes. **Vous n’avez pas besoin de modifier quoi que ce soit** pour continuer à exécuter vos applications générées avec l’API DocumentDB. Azure Cosmos DB est simplement le nom du service dont elles font désormais partie. 

Examinons quelques questions que vous pourriez avoir. 

## <a name="why-move-to-azure-cosmos-db"></a>Pourquoi passer à Azure Cosmos DB ? 

Azure Cosmos DB est la prochaine grande étape en matière de bases de données cloud mondialement distribuées et à grande échelle. En tant que client DocumentDB, vous avez désormais accès au nouveau système révolutionnaire et aux capacités offertes par Azure Cosmos DB.

Azure Cosmos DB a été lancé sous le nom de « Project Florence » en 2010 pour répondre aux problématiques des développeurs auxquelles font face les applications à grande échelle chez Microsoft. Constatant que les difficultés liées à la création d’applications mondialement distribuées ne sont pas un problème propre à Microsoft, en 2015 nous rendions accessible la première génération de cette technologie aux développeurs Azure sous la forme d’Azure DocumentDB. 

Depuis, nous avons ajouté de nouvelles fonctionnalités et introduit de nouvelles possibilités significatives.  Azure Cosmos DB en est le résultat.  Dans le cadre de cette version d’Azure Cosmos DB, les clients DocumentDB, avec leurs données, sont automatiquement des clients Azure Cosmos DB. La transition se fait dans la continuité et vous avez désormais accès au nouveau système révolutionnaire et aux capacités offertes par Azure Cosmos DB. Ces fonctionnalités touchent les domaines du moteur de base de données, de la distribution globale, de l’extensibilité élastique et des contrats SLA performants et complets. Plus précisément, nous avons amélioré le moteur de base de données d’Azure Cosmos DB pour lui permettre de mapper efficacement tous les modèles de données courants, les systèmes de types et les API au modèle de données sous-jacent d’Azure Cosmos DB. 

L’innovation pensée pour les développeurs sur ce projet est la prise en charge de [Gremlin](graph-introduction.md) et des [API de stockage de Table](table-introduction.md). Et ce n’est que le début. Nous ajouterons d’autres API attendues et de nouveaux modèles de données au fil du temps avec davantage de progrès en matière de performances et de stockage à l’échelle mondiale. 

Il est important de souligner que le [dialecte SQL](../documentdb/documentdb-sql-query.md) de DocumentDB n’a toujours été qu’une des nombreuses API que le modèle de données sous-jacent de Cosmos DB était capable de prendre en charge. Pour un développeur utilisant un service entièrement géré comme Azure Cosmos DB, les seules interfaces communiquant avec le service sont les API exposées par le service. À cette fin, rien ne change vraiment pour vous en tant que client DocumentDB existant. Azure Cosmos DB propose exactement la même API SQL que DocumentDB. Toutefois, maintenant (et dans le futur) vous pouvez accéder à d’autres fonctionnalités qui n’étaient pas accessibles précédemment. 

Un autre témoin de notre travail continu est la base étendue pour une extensibilité globale et flexible du débit et du stockage. Un des toute premières démonstrations à ce niveau concerne les [RU/m](request-units-per-minute.md) , mais nous avons plusieurs fonctionnalités qui seront annoncées dans ce domaine. Ces nouvelles fonctionnalités aident à réduire les coûts de nos clients pour différentes charges de travail. Nous avons apporté plusieurs améliorations fondamentales au sous-système de distribution globale. Un des nombreux résultats de notre travail pour les développeurs est le modèle de cohérence de préfixe cohérent (représentant au total cinq modèles de cohérence bien définis). Toutefois, il existe de nombreuses autres fonctionnalités intéressantes que nous publierons une fois terminées. 

## <a name="what-do-i-need-to-do-to-ensure-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>Que dois-je faire pour que mes ressources DocumentDB continuent à s’exécuter sur Azure Cosmos DB ?

Rien. Vous n’avez aucune modification à effectuer. Vos ressources DocumentDB sont désormais des ressources Azure Cosmos DB et aucune interruption de service ne s’est produite lors du processus.

## <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Quelles modifications dois-je effectuer pour que mon application fonctionne avec Azure Cosmos DB ?

Vous n’avez aucune modification à effectuer. Les classes, les espaces de noms et les noms de package NuGet n’ont pas changé. Comme toujours, nous vous recommandons de conserver vos kits de développement logiciel à jour pour tirer parti des dernières fonctionnalités et améliorations. 

## <a name="whats-changed-in-the-azure-portal"></a>Quelles sont les nouveautés dans le portail Azure ?

Azure Document DB n’apparaît plus comme un service Azure dans le portail. Au lieu de cela, Azure Cosmos DB apparaît avec une nouvelle icône, comme illustré dans l’image suivante. Toutes vos collections sont disponibles comme avant, vous pouvez toujours faire évoluer le débit, modifier la cohérence et analyser des SLA. Les fonctionnalités de l’**Explorateur de données (version préliminaire)** ont été améliorées. Vous pouvez maintenant afficher et modifier des documents, créer et exécuter des requêtes et utiliser des procédures stockées, des déclencheurs et des UDF à partir d’une page, comme illustré dans l’image suivante. 

![Afficher et copier une touche d’accès rapide dans le portail Azure, panneau Clés](./media/welcome-documentdb-customers/cosmos-db-data-explorer.png)

## <a name="are-there-changes-to-pricing"></a>La tarification change-t-elle ?

Non, le coût d’exécution de votre application sur Azure Cosmos DB est le même qu’auparavant. Toutefois, vous pouvez tirer profit de la nouvelle **fonctionnalité unités de requête par minute** que nous avons annoncée. Pour plus d’informations, consultez l’article [Mise à l’échelle du débit par minute](request-units-per-minute.md).

## <a name="are-there-changes-to-the-service-level-agreements-slas"></a>Existe-t-il des modifications apportées aux Contrats de niveau de service (SLA) ?

Non, les contrats SLA concernant la disponibilité, la cohérence, la latence et le débit restent inchangés et sont toujours affichés dans le portail. Pour plus d’informations sur les contrats SLA, consultez [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Application To-Do avec des exemples de données](./media/welcome-documentdb-customers/azure-cosmosdb-portal-metrics-slas.png)

## <a name="whats-next-with-azure-cosmos-db"></a>Quelle est la suite concernant Azure Cosmos DB ?

Azure Cosmos DB est un service de base de données en perpétuelle évolution. Toutes les nouvelles fonctionnalités sont validées sur des applications à grande échelle chez Microsoft, rendues accessibles par la suite à des clients externes clés et enfin, proposées au public. 

La nouvelle prise en charge de Gremlin et de l’API de table n’est qu’un début, Azure Cosmos DB prendra en charge d’autres API et modèles de données populaires et nous sommes impatients de vous faire découvrir de nouveaux modèles de données au plus tôt. Nous faisons également de grandes avancées en matière de performances et de stockage à l’échelle mondiale. 

Comme toujours, nous vous remercions d’être un client Azure DocumentDB, nous sommes impatients de partager Azure Cosmos DB avec chacun d’entre vous, nos développeurs et clients dans le monde entier. Notre mission est d’être le service de base de données le plus fiable au monde et de vous permettre de créer plus facilement des applications incroyablement puissantes, à l’échelle du cosmos. Essayez les nouvelles fonctionnalités d’Azure Cosmos DB et donnez-nous votre avis !  Nous avons hâte de voir ce que vous allez créer avec Cosmos DB.

Vos amis d’Azure Cosmos DB [@AzureCosmosDB](https://twitter.com/AzureCosmosDB)

