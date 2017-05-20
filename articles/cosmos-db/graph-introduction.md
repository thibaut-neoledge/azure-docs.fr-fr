---
title: "Introduction à l’API Graph d’Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment utiliser Azure Cosmos DB pour stocker, interroger et parcourir des graphiques volumineux avec une faible latence à l’aide du langage de requête Gremlin."
services: cosmosdb
author: arramac
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 963822f0927afea90c8a03b60b7ee93fe36b4070
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Présentation d’Azure Cosmos DB : API Graph

[Azure Cosmos DB](introduction.md) est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale pour les applications stratégiques. Azure Cosmos DB fournit la [distribution mondiale clés en main](../documentdb/documentdb-distribute-data-globally.md), la [mise à l’échelle élastique du débit et du stockage](partition-data.md), des latences de l’ordre de quelques millisecondes dans le monde entier dans plus de 99 pour cent des cas, [cinq niveaux de cohérence bien définis](../documentdb/documentdb-consistency-levels.md) et une garantie d’une haute disponibilité, le tout soutenu par nos [contrats SLA de pointe](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/). Azure Cosmos DB [indexe automatiquement les données](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sans avoir à gérer la gestion des schémas et des index. Il est multi-modèle et prend en charge les modèles de données en colonnes, documents, graphiques et clé-valeur. 

![Gremlin, graphiques et Azure Cosmos DB](./media/graph-introduction/graph-gremlin.png) 

Azure Cosmos DB fournit des API de modélisation de graphiques et de traversée, ainsi qu’une distribution mondiale clés en main, une mise à l’échelle élastique du débit et du stockage, et des latences de moins de 10 ms en lecture et de moins de 15 ms dans 99 % des cas, des fonctions d’indexation et d’interrogation automatiques, des niveaux de cohérence ajustables et des contrats SLA complets, garantissant une disponibilité de 99,99 %. Il est possible d’interroger Azure Cosmos DB en utilisant le [langage Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) et de l’intégrer à d’autres systèmes graphiques compatibles avec Gremlin.

Dans cet article, nous proposons une vue d’ensemble de l’API Graph d’Azure Cosmos DB, et expliquons comment l’utiliser pour stocker des graphiques volumineux avec des milliards de sommets et de bords, les interroger avec une latence de quelques millisecondes, et faire évoluer facilement la structure du graphique et le schéma. 

## <a name="graph-databases"></a>Bases de données de graphiques
Les données telles qu’elles apparaissent dans le monde réel sont naturellement connectées. La modélisation de données traditionnelle se concentre sur les entités. Mais pour de nombreuses applications, il est également nécessaire de modéliser les relations enrichies entre entités. Les graphiques vous permettent de modéliser naturellement à la fois les entités et les relations. 

Un [graphique](http://mathworld.wolfram.com/Graph.html) est une structure composée de [sommets](http://mathworld.wolfram.com/GraphVertex.html) et de [bords](http://mathworld.wolfram.com/GraphEdge.html). Les sommets et les bords peuvent avoir un nombre arbitraire de propriétés. Les sommets désignent des objets discrets comme une personne, un lieu ou un événement. Les bords désignent les relations entre les sommets. Par exemple, une personne peut connaître une autre personne, avoir participé à un événement et/ou s’être rendue récemment à un endroit précis. Les propriétés correspondent aux informations sur les bords et les sommets. Il peut par exemple s’agir du nom ou de l’âge d’un sommet, ou encore de l’horodatage et/ou du poids d’un bord. Plus formellement, ce modèle est appelé un [graphique de propriétés](https://github.com/tinkerpop/blueprints/wiki/Property-Graph-Model). Azure Cosmos DB prend en charge le modèle de graphique de propriétés. 

L’exemple de graphique en diagramme suivant montre la relation entre des personnes, des périphériques mobiles, des intérêts et des systèmes d’exploitation. 

![Exemple de base de données montrant des personnes, des périphériques et des intérêts](./media/graph-introduction/sample-graph.png) 

Les graphiques sont utiles pour comprendre un large éventail de jeux de données dans les domaines de la science, de la technologie et des activités professionnelles. Les bases de données de graphiques vous permettent de modéliser et de stocker des graphiques naturellement et efficacement, ce qui les rend attrayants pour de nombreux scénarios. Les bases de données de graphiques sont généralement des bases de données NoSQL, car ces cas d’utilisation requièrent souvent également une certaine flexibilité des schémas et une itération rapide. 

Les graphiques offrent une nouvelle technique puissante de modélisation des données. Mais cette raison seule n’est pas suffisante pour justifier l’utilisation d’une base de données de graphiques. Dans de nombreux modèles et cas d’utilisation impliquant la traversée de graphiques, ces derniers s’avèrent plus performants que les bases de données SQL et NoSQL traditionnelles par ordre de grandeur. Cette différence de performances s’amplifie lors de la traversée de plusieurs relations, comme l’ami d’un ami. 

Vous pouvez combiner les traversées rapides qu’offrent les bases de données de graphiques avec des algorithmes graphiques tels que la recherche de profondeur, la recherche de largeur, l’algorithme de Dijkstra, etc., pour résoudre des problèmes dans des domaines différents comme les réseaux sociaux, les fichiers géospatiaux, la gestion de contenu et les recommandations. 

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Graphiques à l’échelle de la planète avec Azure Cosmos DB
Azure Cosmos DB est une base de données de graphiques entièrement gérée qui offre une distribution mondiale, une mise à l’échelle élastique du débit et du stockage, des fonctions d’indexation et d’interrogation automatiques, des niveaux de cohérence ajustables et la prise en charge de la norme TinkerPop.  

![Architecture graphique Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png) 

Azure Cosmos DB se démarque des autres bases de données de graphiques sur le marché en proposant les fonctionnalités suivantes : 

* **Mise à l’échelle élastique du débit et du stockage** : dans le monde réel, les graphiques doivent pouvoir être mis à l’échelle au-delà de la capacité d’un seul serveur. Avec Azure Cosmos DB, vous pouvez mettre à l’échelle vos graphiques en toute transparence sur plusieurs serveurs. Vous pouvez également mettre à l’échelle le débit de votre graphique de manière indépendante, en fonction de vos modèles d’accès. Azure Cosmos DB prend en charge des bases de données de graphiques qui peuvent être mises à l’échelle pour atteindre des tailles de stockage quasi illimitées et un débit approvisionné. 

* **Réplication dans plusieurs régions** : Azure Cosmos DB réplique en toute transparence vos données graphiques dans toutes les régions associées à votre compte Azure Cosmos DB, ce qui vous permet de développer des applications qui requièrent l’accès global aux données tout en offrant un compromis entre cohérence, disponibilité et performances, tout en offrant les garanties correspondantes. Azure Cosmos DB fournit un basculement régional transparent avec les API multihébergement et la possibilité de mettre à l’échelle de manière élastique le débit et le stockage dans le monde entier.

* **Interrogation rapide et traversées avec une syntaxe Gremlin connue** : stockez des sommets et des bords hétérogènes et interrogez ces documents via une syntaxe Gremlin connue (une version SQL améliorée sera également disponible bientôt). Azure Cosmos DB utilise une technologie d'indexation parallèle, structurée par des journaux et sans verrouillage qui permet d'indexer automatiquement tout le contenu. Celle-ci autorise les requêtes enrichies en temps réel et les traversées sans qu’il soit nécessaire de spécifier des indicateurs de schéma, des index secondaires ou des vues. Pour en savoir plus, consultez [Interroger des graphiques à l’aide de Gremlin](gremlin-support.md).

* **Gestion globale** : ne vous souciez plus de gérer les ressources de base de données et d’ordinateur. Étant donné qu’il s’agit d’un service Microsoft Azure entièrement géré, vous n’avez pas à gérer de machines virtuelles, à déployer et configurer des logiciels, à gérer la mise à l’échelle ni à vous préoccuper des mises à niveau de la couche données. Chaque graphique est automatiquement sauvegardé et protégé contre les défaillances régionales. Vous pouvez facilement ajouter un compte Azure Cosmos DB et configurer la capacité dont vous avez besoin, et ainsi vous concentrer pleinement sur votre application plutôt que sur le fonctionnement et la gestion de votre base de données.

* **Indexation automatique** : par défaut, Azure Cosmos DB indexe automatiquement toutes les propriétés des nœuds et des bords du graphique et n’attend pas et ne nécessite aucun schéma ou création d’index secondaires. 

* **Compatibilité avec Gremlin** : Azure Cosmos DB prend nativement en charge la norme Open Source Gremlin et peut s’intégrer à d’autres systèmes graphiques compatibles avec Gremlin. Par conséquent, vous pouvez facilement effectuer une migration à partir d’une autre base de données de graphiques comme Titan ou Neo4j, ou utiliser Azure Cosmos DB avec des infrastructures d’analyse graphique comme Apache Spark GraphX.

* **Niveaux de cohérence ajustables** : opérez un choix parmi cinq niveaux de cohérence bien définis pour obtenir un équilibre optimal entre cohérence et performances. Pour les requêtes et les opérations de lecture, Azure Cosmos DB propose cinq niveaux de cohérence distincts : Fort, En fonction de l’obsolescence, Par session, Préfixe cohérent et Éventuel. Ces niveaux de cohérence bien définis et granulaires vous permettent de trouver un bon compromis entre cohérence, disponibilité et latence. Pour en savoir plus, consultez [Utilisation des niveaux de cohérence pour optimiser la disponibilité et les performances dans DocumentDB](../documentdb/documentdb-consistency-levels.md).

Azure Cosmos DB offre également la possibilité d’utiliser plusieurs modèles, tels que des documents et des graphiques dans les mêmes conteneurs/bases de données. Vous pouvez utiliser une collection de documents pour stocker des données graphiques côte à côte avec les documents, et utiliser à la fois des requêtes SQL sur JSON et des requêtes Gremlin pour interroger les mêmes données sous forme de graphique. 

## <a name="getting-started"></a>Prise en main
Il est possible de créer des comptes Azure Cosmos DB via l’interface de ligne de commande Azure, Azure Powershell ou le portail Azure avec prise en charge de l’API Graph. Une fois créé, le portail Azure fournit un point de terminaison de service comme `https://<youraccount>.graphs.azure.com`, qui fournit un serveur frontal WebSocket pour Gremlin. Vous pouvez configurer vos outils compatibles avec Gremlin, par exemple la [console Gremlin](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console), pour vous connecter à ce point de terminaison et créer des applications en Java, Node.js ou n’importe quel pilote client Gremlin.

Le tableau suivant montre les pilotes Gremlin les plus courants que vous pouvez utiliser avec Azure Cosmos DB :

| Télécharger | Documentation |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.JS](https://www.npmjs.com/package/gremlin) |[Gremlin-JavaScript sur Github](https://github.com/jbmusso/gremlin-javascript) |
| [Console Gremlin](https://tinkerpop.apache.org/downloads.html) |[Documents TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

Azure Cosmos DB fournit également une bibliothèque .NET interne avec des méthodes d’extension Gremlin par dessus le [kit de développement logiciel Azure Cosmos DB](../documentdb/documentdb-sdk-dotnet.md) via NuGet. Cette bibliothèque fournit un serveur Gremlin « in-proc », qui peut être utilisé pour se connecter directement à des partitions de données DocumentDB. 

| Télécharger | Documentation |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

[L’émulateur Azure Cosmos DB](../documentdb/documentdb-nosql-local-emulator.md) vous permet de procéder à un développement et à des tests localement à l’aide de l’API Graph, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur, vous pouvez commencer à utiliser un compte Azure Cosmos DB dans le cloud.

## <a name="scenarios-for-azure-cosmos-dbs-graph-support"></a>Scénarios de prise en charge des graphiques Azure Cosmos DB
Voici quelques scénarios permettant de tirer parti de la prise en charge des graphiques Azure Cosmos DB : 

* **Réseaux sociaux** : en associant des données sur vos clients et leurs interactions avec d’autres personnes, vous pouvez développer des expériences personnalisées, prédire le comportement des clients ou connecter entre elles des personnes ayant les mêmes intérêts. Azure Cosmos DB peut servir à gérer des réseaux sociaux et à suivre les données et les préférences des clients. 

* **Moteurs de recommandation** : couramment utilisés dans le secteur de la vente au détail. En associant des informations sur les produits, les utilisateurs et les interactions des utilisateurs (achats, navigation ou notation d’un élément), vous pouvez générer des recommandations personnalisées. Azure Cosmos DB, avec sa faible latence, son infrastructure élastique et sa prise en charge native des graphiques, est idéal pour la modélisation de ces interactions.

* **Fichiers géospatiaux** : de nombreuses applications dans les secteurs des télécommunications, de la logistique et de la planification de voyages nécessitent de trouver un lieu intéressant dans une zone donnée, ou de rechercher l’itinéraire le plus court/optimal entre deux lieux. Azure Cosmos DB constitue une solution naturelle à ces problèmes. 

* **Internet des objets** : avec le réseau et les connexions entre les périphériques IoT modélisés sous forme de graphique, vous pouvez créer un meilleur aperçu de l’état de vos périphériques et de vos ressources, et de l’impact de modifications dans une partie du réseau sur une autre partie. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la prise en charge des graphiques dans Azure Cosmos DB, consultez :

* Prise en main avec le [didacticiel graphique Azure Cosmos DB](create-graph-dotnet.md)
* En savoir plus sur [l’interrogation des graphiques dans Azure Cosmos DB à l’aide de Gremlin](gremlin-support.md)

