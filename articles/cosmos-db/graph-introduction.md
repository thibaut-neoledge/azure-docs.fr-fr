---
title: "Introduction à l’API Graph d’Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment utiliser Azure Cosmos DB pour stocker, interroger et parcourir des graphiques volumineux avec une faible latence à l’aide du langage de requête de graphe Gremlin d’Apache TinkerPop."
services: cosmos-db
author: dennyglee
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: af4c67accf43c2f4f1498e3cafad6e9087a923af
ms.contentlocale: fr-fr
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Présentation d’Azure Cosmos DB : API Graph

[Azure Cosmos DB](introduction.md) est le service de base de données multimodèle globalement distribué de Microsoft pour les applications stratégiques. Azure Cosmos DB fournit la [distribution mondiale clés en main](distribute-data-globally.md), la [mise à l’échelle élastique du débit et du stockage](partition-data.md), des latences de l’ordre de quelques millisecondes dans le monde entier dans 99 pour cent des cas, [cinq niveaux de cohérence bien définis](consistency-levels.md) et une garantie de haute disponibilité, le tout soutenu par nos [contrats SLA de pointe](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexe automatiquement les données](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sans avoir à s’occuper de la gestion des schémas et des index. Il est multi-modèle et prend en charge les modèles de données en colonnes, documents, graphes et clé-valeur.

Il est conseillé de commencer par visionner la vidéo suivante, dans laquelle Kirill Gavrylyuk montre comment bien démarrer avec les graphes Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Graphs-with-Azure-Cosmos-DB-Gremlin-API/player]
> 
> 
L’API Graph Azure Cosmos DB offre ce qui suit :

- Modélisation de graphe
- API Traversal
- Distribution mondiale clé en main
- Mise à l’échelle élastique du stockage et du débit avec moins de 10 ms de latences de lecture, et moins de 15 ms dans 99 pour cent des cas
- Indexation automatique avec disponibilité de requête instantanée
- Modèles de cohérence ajustables
- SLA complets, incluant une disponibilité à 99,99 %

Pour interroger Azure Cosmos DB, vous pouvez utiliser le langage de requête graphique d’[Apache TinkerPop](http://tinkerpop.apache.org), [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), ou d’autres systèmes graphiques compatibles avec TinkerPop tels qu’[Apache Spark GraphX](spark-connector-graph.md).

Cet article fournit une vue d’ensemble de l’API Graph Azure Cosmos DB, et explique comment l’utiliser pour stocker des graphes volumineux comportant des milliards de vertex et de bords. Vous pouvez interroger les graphes avec une latence de quelques millisecondes et faire évoluer facilement leur structure et leur schéma.

## <a name="graph-database"></a>Base de données de graphes
Les données telles qu’elles apparaissent dans le monde réel sont naturellement connectées. La modélisation de données traditionnelle se concentre sur les entités. Pour de nombreuses applications, il est également nécessaire de modéliser les entités et relations de façon naturelle.

Un [graphe](http://mathworld.wolfram.com/Graph.html) est une structure composée de [sommets](http://mathworld.wolfram.com/GraphVertex.html) et de [bords](http://mathworld.wolfram.com/GraphEdge.html). Les sommets et les bords peuvent avoir un nombre arbitraire de propriétés. Les sommets désignent des objets discrets comme une personne, un lieu ou un événement. Les bords désignent les relations entre les sommets. Par exemple, une personne peut en connaître une autre, être impliquée dans un événement et avoir récemment été dans un lieu. Les propriétés correspondent aux informations sur les bords et les sommets. Les exemples de propriétés incluent un sommet qui a un nom et un âge, et un bord qui a un horodatage et/ou un poids. Plus formellement, ce modèle est appelé un [graphique de propriétés](http://tinkerpop.apache.org/docs/current/reference/#intro). Azure Cosmos DB prend en charge le modèle de graphe de propriétés.

Ainsi, l’exemple de graphe suivant présente des relations entre des personnes, des appareils mobiles, des intérêts et des systèmes d’exploitation.

![Exemple de base de données montrant des personnes, des appareils et des centres d’intérêt](./media/graph-introduction/sample-graph.png)

Les graphes sont utiles pour comprendre un vaste éventail de jeux de données dans les domaines des sciences, des technologies et des affaires. Les bases de données de graphes vous permettent de modéliser et de stocker des graphes naturellement et efficacement, ce qui les rend utiles pour de nombreux scénarios. Les bases de données de graphes sont généralement des bases de données NoSQL, car ces cas d’utilisation requièrent souvent aussi une flexibilité des schémas et une itération rapide.

Les graphes offrent une nouvelle technique puissante de modélisation des données. Mais ce seul fait ne constitue pas une raison suffisante pour justifier l’utilisation d’une base de données de graphes. Dans de nombreux cas et modèles d’utilisation impliquant des traversées de graphes, ces derniers s’avèrent plus performants que les bases de données SQL et NoSQL traditionnelles en termes de magnitude. Cette différence de performances est encore amplifiée lors de la traversée de plusieurs relations telles que ami d’un ami.

Vous pouvez combiner les traversées rapides qu’offrent les bases de données de graphes avec des algorithmes de graphe tels que la recherche de profondeur, la recherche de largeur et l’algorithme de Dijkstra, pour résoudre des problèmes dans divers domaines tels que les réseaux sociaux, la gestion de contenu, les questions géospatiales et les recommandations.

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Graphiques à l’échelle de la planète avec Azure Cosmos DB
Azure Cosmos DB est une base de données de graphes entièrement gérée, qui offre une distribution mondiale, une mise à l’échelle élastique du débit et du stockage, des fonctions d’indexation et d’interrogation automatiques, des niveaux de cohérence ajustables et la prise en charge de la norme TinkerPop.  

![Architecture des graphes Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

Azure Cosmos DB se démarque des autres bases de données de graphes sur le marché en proposant les fonctionnalités suivantes :

* Stockage et débit extensibles de façon élastique

 Dans la vraie vie, les graphes doivent pouvoir augmenter leur échelle au-delà de la capacité d’un serveur unique. Avec Azure Cosmos DB, vous pouvez mettre à l’échelle vos graphes de manière fluide sur plusieurs serveurs. Vous pouvez également mettre à l’échelle le débit de votre graphe de manière indépendante, en fonction de vos modèles d’accès. Azure Cosmos DB prend en charge des bases de données de graphes qui peuvent être mises à l’échelle pour atteindre des tailles de stockage quasi illimitées et un débit approvisionné.

* Réplication multirégion

 Azure Cosmos DB réplique de manière fluide les données de vos graphes sur toutes les régions que vous avez associées à votre compte. La réplication vous permet de développer des applications qui requièrent un accès global aux données. Il existe des compromis dans les domaines de la cohérence, de la disponibilité, des performances et des garanties correspondantes. Azure Cosmos DB fournit un basculement régional transparent avec des API d’hébergement multiple. Vous pouvez mettre à l’échelle de façon élastique le débit et le stockage dans le monde entier.

* Requêtes et parcours rapides avec une syntaxe Gremlin familière

 Stockez des sommets et des bords hétérogènes, et interrogez ces documents via une syntaxe Gremlin familière. Azure Cosmos DB utilise une technologie d’indexation parallèle, structurée par des journaux et sans verrouillage qui permet d’indexer automatiquement tout le contenu. Cette capacité autorise des requêtes et traversées enrichies en temps réel, sans qu’il soit nécessaire de spécifier des indicateurs de schéma, des index secondaires ou des vues. Pour en savoir plus, consultez [Interroger des graphes à l’aide de Gremlin](gremlin-support.md).

* Gestion intégrale

 Ne vous souciez plus de gérer les ressources de base de données et d’ordinateur. Étant donné qu’il s’agit d’un service Microsoft Azure entièrement géré, vous n’avez pas à gérer de machines virtuelles, à déployer et configurer des logiciels, à gérer la mise à l’échelle ni à vous préoccuper des mises à niveau de la couche données. Chaque graphe est automatiquement sauvegardé et protégé contre les défaillances régionales. Vous pouvez facilement ajouter un compte Azure Cosmos DB et approvisionner la capacité dont vous avez besoin afin de vous concentrer pleinement sur votre application plutôt que sur l’exploitation et la gestion de votre base de données.

* Indexation automatique

 Par défaut, Azure Cosmos DB indexe automatiquement toutes les propriétés des nœuds et des bords du graphe, et n’attend ou ne nécessite aucun schéma ou création d’index secondaires.

* Compatibilité avec Apache TinkerPop

 Azure Cosmos DB prend nativement en charge la norme Open Source Apache TinkerPop, et peut être intégré à d’autres systèmes de graphes compatibles avec TinkerPop. Par conséquent, vous pouvez facilement effectuer une migration à partir d’une autre base de données de graphes comme Titan ou Neo4j, ou utiliser Azure Cosmos DB avec des frameworks d’analytique de graphe comme [Apache Spark GraphX](spark-connector-graph.md).

* Niveaux de cohérence ajustables

 choisissez entre cinq niveaux de cohérence bien définis pour obtenir un équilibre optimal entre cohérence et performances. Pour les requêtes et les opérations de lecture, Azure Cosmos DB propose cinq niveaux de cohérence distincts : Fort, En fonction de l’obsolescence, Par session, Préfixe cohérent et Éventuel. Ces niveaux de cohérence bien définis et granulaires vous permettent de trouver un bon compromis entre cohérence, disponibilité et latence. Pour en savoir plus, consultez [Utilisation des niveaux de cohérence pour optimiser la disponibilité et les performances dans DocumentDB](consistency-levels.md).

Azure Cosmos DB peut également utiliser plusieurs modèles, tels que des documents et des graphes, au sein des mêmes conteneurs/bases de données. Vous pouvez utiliser une collection de documents pour stocker des données de graphes côte à côte avec des documents. Vous pouvez utiliser tant des requêtes SQL sur JSON que des requêtes Gremlin pour interroger les mêmes données en tant que graphe.

## <a name="getting-started"></a>Prise en main
Vous pouvez utiliser l’interface de ligne de commande (CLI) Azure, Azure Powershell ou le portail Azure avec prise en charge de l’API Graph pour créer des comptes Azure Cosmos DB. Une fois les comptes créés, le portail Azure fournit un point de terminaison de service, tel que `https://<youraccount>.graphs.azure.com`, qui fournit un WebSocket frontal pour Gremlin. Vous pouvez configurer vos outils compatibles avec TinkerPop, telle la [console Gremlin](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console), pour vous connecter à ce point de terminaison et créer des applications en Java, Node.js, ou tout pilote de client Gremlin.

Le tableau suivant présente des pilotes Gremlin courants que vous pouvez utiliser sur Azure Cosmos DB :

| Télécharger | Documentation |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.JS](https://www.npmjs.com/package/gremlin) |[Gremlin-JavaScript sur Github](https://github.com/jbmusso/gremlin-javascript) |
| [Console Gremlin](https://tinkerpop.apache.org/downloads.html) |[Documents TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

Azure Cosmos DB fournit également une bibliothèque .NET avec des méthodes d’extension Gremlin par dessus les [Kits de développement logiciel (SDK) Azure Cosmos DB](documentdb-sdk-dotnet.md) via NuGet. Cette bibliothèque fournit un serveur Gremlin « in-process » que vous pouvez utiliser pour vous connecter directement à des partitions de données DocumentDB.

| Télécharger | Documentation |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

L’[émulateur Azure Cosmos DB](local-emulator.md) vous permet de procéder à un développement et à des tests localement à l’aide de l’API Graph, sans créer d’abonnement Azure et sans exposer de frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur, vous pouvez commencer à utiliser un compte Azure Cosmos DB dans le cloud.

## <a name="scenarios-for-graph-support-of-azure-cosmos-db"></a>Scénarios de prise en charge des graphes par Azure Cosmos DB
Voici quelques scénarios où la prise en charge des graphes par Azure Cosmos DB peut être utile :

* Réseaux sociaux

 En associant des données sur vos clients et leurs interactions avec d’autres personnes, vous pouvez développer des expériences personnalisées, prédire le comportement des clients ou connecter entre elles des personnes ayant les mêmes intérêts. Azure Cosmos DB peut servir à gérer des réseaux sociaux et à suivre les données et les préférences des clients.

* Moteurs de recommandation

 Ce scénario est couramment utilisé dans le secteur de la vente au détail. En associant des informations sur les produits, les utilisateurs et les interactions des utilisateurs (achats, navigation ou notation d’un article), vous pouvez générer des recommandations personnalisées. Azure Cosmos DB, avec sa faible latence, sa mise à l’échelle élastique et sa prise en charge native des graphes, est idéal pour la modélisation de ces interactions.

* Questions géospatiales

 De nombreuses applications dans les secteurs des télécommunications, de la logistique et de la planification de voyages nécessitent de trouver un lieu intéressant dans une zone donnée, ou de rechercher l’itinéraire le plus court/optimal entre deux lieux. Azure Cosmos DB constitue une solution naturelle à ces problèmes.

* Internet des Objets

 Avec le réseau et les connexions entre les appareils IoT modélisés sous forme de graphe, vous pouvez créer un meilleur aperçu de l’état de vos appareils et ressources, et de l’impact de modifications d’une partie du réseau sur une autre.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la prise en charge des graphes dans Azure Cosmos DB, consultez :

* Prise en main avec le [didacticiel graphique Azure Cosmos DB](create-graph-dotnet.md).
* Découvrez comment [interroger des graphes dans Azure Cosmos DB à l’aide de Gremlin](gremlin-support.md).

