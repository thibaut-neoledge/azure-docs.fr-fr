---
title: "Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comme le partitionnement fonctionne dans Azure Cosmos DB, comment configurer le partitionnement et les clés de partition, et comment choisir la clé de partition appropriée pour votre application."
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 5f501bdb0a3c478a436d729dfe045ad8e39bd3bb
ms.contentlocale: fr-fr
ms.lasthandoff: 06/14/2017


---

# <a name="how-to-partition-and-scale-in-azure-cosmos-db"></a>Guide de partitionnement et de mise à l’échelle dans Azure Cosmos DB

[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) est un service de base de données multi-modèle, distribué globalement conçu pour vous aider à obtenir des performances rapides et prévisibles, et à monter en charge en toute transparence parallèlement à l’évolution de votre application. Cet article fournit une vue d’ensemble du fonctionnement du partitionnement pour tous les modèles de données dans Azure Cosmos DB et décrit comment vous pouvez configurer les conteneurs Azure Cosmos DB pour mettre à l’échelle vos applications efficacement.

Le partitionnement et les clés de partition sont également abordés dans cette vidéo Azure Friday avec Scott Hanselman et Shireesh Thota, responsable principal de l’ingénierie Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Partitionnement dans Azure Cosmos DB
Dans Azure Cosmos DB, vous pouvez stocker et interroger des données sans schéma avec des délais de réponse de l’ordre des millisecondes à n’importe quelle échelle. Azure Cosmos DB fournit des conteneurs pour le stockage des données, appelés **collections (pour les documents), graphiques ou tables**. Les conteneurs sont des ressources logiques. Ils peuvent s’étendre sur plusieurs partitions physiques ou serveurs. Le nombre de partitions est déterminé par Azure Cosmos DB en fonction de la taille de stockage et du débit approvisionné du conteneur. Chaque partition dans Azure Cosmos DB dispose d’une quantité fixe de stockage SSD associé. Elle est également répliquée pour offrir une haute disponibilité. La gestion des partitions est entièrement exécutée par Azure Cosmos DB. Vous n’avez pas à écrire du code complexe, ni à gérer vos partitions. Les conteneurs Cosmos DB sont illimités en termes de débit et de stockage. 

![horizontal](./media/introduction/azure-cosmos-db-partitioning.png) 

Le partitionnement est transparent pour votre application. Azure Cosmos DB prend en charge les lectures et écritures rapides, les requêtes, la logique transactionnelle basée sur JavaScript, les niveaux de cohérence et le contrôle d’accès précis via des API/méthode à une ressource de conteneur unique. Le service gère la distribution des données entre les partitions et le routage des demandes de requête vers la partition appropriée. 

Comment le partitionnement fonctionne-t-il ? Chaque élément doit avoir une clé de partition et une clé de ligne, qui l’identifient de façon. Votre clé de partition agit comme une partition logique pour vos données et fournit à Cosmos DB une frontière naturelle pour la distribution des données sur plusieurs partitions. En bref, voici comment le partitionnement fonctionne dans Azure Cosmos DB :

* Vous devez configurer un conteneur Cosmos DB avec un débit de `T` requêtes/s
* En arrière-plan, Cosmos DB configure les partitions nécessaires pour servir `T` requêtes/s. Si `T` est plus élevé que le débit maximal par partition `t`, Cosmos DB provisionne `N`  =  `T/t` partitions
* Cosmos DB alloue l’espace des hachages de clé de partition uniformément à travers les partitions `N`. Par conséquent, chaque partition (physique) héberge N-1 valeurs de clé de partition (partitions logiques)
* Lorsqu’une partition physique `p` atteint sa limite de stockage, Cosmos DB divise sans problème `p` en deux nouvelles partitions, `p1` et `p2`, et distribue les valeurs correspondant à environ la moitié des clés pour chacune des partitions. Cette opération de division est invisible pour votre application.
* De même, lorsque vous déployez un débit supérieur à `t*N`, Cosmos DB divise une ou plusieurs de vos partitions pour soutenir le débit plus élevé

La sémantique pour les clés de partition est légèrement différente pour correspondre à celle de chaque API, comme indiqué dans le tableau suivant :

| API | Partition Key | Row Key |
| --- | --- | --- |
| Base de données de documents | custom partition key path | `id` (fixe) | 
| MongoDB | clé de partitionnement personnalisée  | `_id` (fixe) | 
| Graph | propriété de clé de partition personnalisée | `id` (fixe) | 
| Table | `PartitionKey` (fixe) | `RowKey` (fixe) | 

Cosmos DB utilise le partitionnement basé sur le hachage. Lorsque vous écrivez un élément, Cosmos DB hache la valeur de clé de partition et utilise le résultat haché pour déterminer dans quelle partition stocker l’élément. Cosmos DB stocke tous les éléments avec la même clé de partition dans la même partition physique. Le choix de la clé de partition est une décision importante que vous devrez prendre au moment de la conception. Vous devez choisir un nom de propriété qui possède une large plage de valeurs et des modèles d’accès uniformes.

> [!NOTE]
> Il est recommandé de disposer d’une clé de partition avec de nombreuses valeurs distinctes (100 à 1000 s au minimum).
>

Les conteneurs Azure Cosmos DB peuvent être créés comme étant « fixes » ou « illimités ». Les conteneurs de taille fixe ont une limite maximale de 10 Go et de 10 000 RU/s de débit. Certaines API permettent d’omettre la clé de partition pour les conteneurs de taille fixe. Pour créer un conteneur comme étant illimité, vous devez spécifier un débit minimum de 2500 RU/s.

## <a name="partitioning-and-provisioned-throughput"></a>Partitionnement et débit approvisionné
Cosmos DB est conçu pour offrir des performances prévisibles. Lorsque vous créez un conteneur, vous réservez le débit en termes de **[d’unités de requête](request-units.md) (RU) par seconde avec un ajout potentiel pour les unités de requête par minute**. Un coût en unités de demande proportionnel à la quantité de ressources système, comme le processeur, la mémoire et les E/S consommés par l’opération, est affecté à chaque demande. La lecture d’un document de 1 ko avec une cohérence de session consomme une unité de demande. Une lecture correspond à 1 RU, quel que soit le nombre d’éléments stockés ou le nombre de demandes simultanées en cours d’exécution. Les éléments plus volumineux nécessitent plus d’unités de demande selon leur taille. Si vous connaissez la taille de vos entités et le nombre de lectures nécessaires à prendre en charge pour votre application, vous pouvez approvisionner la quantité exacte de débit requis pour les besoins en lecture de votre application. 

> [!NOTE]
> Afin d’optimiser le débit total du conteneur, vous devez choisir une clé de partition qui vous permet de répartir uniformément les demandes entre certaines valeurs de clé de partition distinctes.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="working-with-the-azure-cosmos-db-apis"></a>Utilisation de l’API Azure Cosmos Azure
Vous pouvez utiliser le portail Azure ou l’interface CLI Azure pour créer des conteneurs et les faire évoluer à tout moment. Cette section montre comment créer des conteneurs et spécifier la définition de clé de partition et le débit dans chacune des API prises en charge.

### <a name="documentdb-api"></a>API DocumentDB
L’exemple suivant montre comment créer un conteneur (collection) à l’aide de l’API DocumentDB. Vous trouverez plus de détails dans [Partitionnement avec l’API DocumentDB](partition-data.md).

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Vous pouvez lire un élément (document) à l’aide de la méthode `GET` dans l’API REST ou à l’aide de `ReadDocumentAsync` dans un des kits de développement logiciel.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="mongodb-api"></a>API MongoDB
Avec l’API MongoDB, vous pouvez créer une collection partitionnée MongoDB via l’outil, le pilote ou le SDK de votre choix. Dans cet exemple, nous utilisons l’interpréteur de commandes Mongo pour la création de la collection.

Dans l’interpréteur de commandes Mongo :

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Résultats :

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>API de table

Avec l’API de table, vous spécifiez le débit pour les tables dans la configuration appSettings de votre application :

```xml
<configuration>
    <appSettings>
      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
    </appSettings>
</configuration>
```

Vous créez ensuite une table en utilisant le kit de développement logiciel de stockage de table Azure. La clé de partition est implicitement créée en tant que valeur `PartitionKey`. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists();
```

Vous pouvez récupérer une entité unique à l’aide de l’extrait de code suivant :

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Consultez la page [Développement avec l’API de table](tutorial-develop-table-dotnet.md) pour plus de détails.

### <a name="graph-api"></a>API Graph

Avec l’API Graph, vous devez utiliser le portail Azure ou l’interface CLI pour créer des conteneurs. Azure Cosmos DB étant multimodèle, vous pouvez également utiliser un des autres modèles pour créer et mettre à l’échelle votre conteneur graphique.

Vous pouvez lire n’importe quel vertex ou bord à l’aide de la clé de partition et l’id dans Gremlin. Par exemple, pour un graphique avec la région (« USA ») en tant que clé de partition et « Seattle » en tant que la clé de ligne, vous trouverez un vertex à l’aide de la syntaxe suivante :

```
g.V(['USA', 'Seattle'])
```

Idem pour les bords, vous pouvez référencer un bord à l’aide de la clé de partition et la clé de ligne.

```
g.E(['USA', 'I5'])
```

Consultez la page [Prise en charge de Gremlin pour Cosmos DB](gremlin-support.md) pour plus de détails.


<a name="designing-for-partitioning"></a>
## <a name="designing-for-partitioning"></a>Conception du partitionnement
Pour mettre à l’échelle efficacement avec Azure Cosmos DB, vous devez choisir une bonne clé de partition lorsque vous créez votre conteneur. Il existe deux considérations essentielles pour le choix d’une clé de partition :

* **Limite pour la requête et les transactions :** Votre choix de clé de partition doit équilibrer la nécessité d’utiliser des transactions et la nécessité de répartir les entités sur plusieurs clés de partitions pour garantir une solution évolutive. D’un côté, vous pouvez définir la même clé de partition pour tous vos éléments, mais cela peut limiter l’extensibilité de votre solution. D’un autre côté, vous pouvez attribuer une clé de partition unique à chaque élément, ce qui optimise l’évolutivité, mais peut vous empêcher d’utiliser des transactions entre les documents par le biais des procédures stockées et des déclencheurs. Une clé de partition idéale vous permet d’utiliser des requêtes efficaces et possède une cardinalité suffisante pour garantir l’évolutivité de votre solution. 
* **Aucun goulot d’étranglement des performances et du stockage** : il est important de choisir une propriété qui permet de distribuer les lectures sur plusieurs valeurs distinctes. Les demandes auprès de la même clé de partition ne peuvent pas surpasser le débit d’une partition unique et sont limitées. Il est donc important de choisir une clé de partition qui n’entraîne pas de « zones réactives » au sein de votre application. Étant donné que toutes les données pour une clé de partition unique doivent être stockées dans une partition, il est également recommandé d’éviter les clés de partition qui ont des volumes importants de données pour la même valeur. 

Nous allons examiner quelques scénarios réels et les bonnes clés de partition à chaque fois :
* Si vous implémentez un service principal de profil utilisateur, alors l’ID utilisateur constitue un bon choix pour la clé de partition.
* Si vous stockez des données IoT, comme l’état d’appareil, un ID d’appareil constitue un bon choix pour la clé de partition.
* Si vous utilisez DocumentDB pour journaliser des données de série chronologique, le nom d’hôte et l’ID de processus constituent un bon choix pour une clé de partition.
* Si vous disposez d’une architecture mutualisée, l’ID client constitue un bon choix pour la clé de partition.

Dans certains cas d’utilisation, comme l’IoT et les profils utilisateur, la clé de partition peut être identique à votre ID (clé de document). Dans d’autres cas, comme les données de série chronologique, la clé de partition peut être différente de l’ID.

### <a name="partitioning-and-loggingtime-series-data"></a>Partitionnement et journalisation de données de série chronologique
Cosmos DB est très souvent utilisé à des fins de journalisation et de télémétrie. Il est important de choisir une bonne clé de partition, car il se peut que vous deviez lire et écrire des volumes importants de données. Le choix dépend de vos taux de lecture et d’écriture, et des types de requêtes que vous prévoyez d’exécuter. Voici quelques conseils sur la façon de choisir une clé de partition appropriée.

* Si votre mode d’utilisation implique un petit taux d’écritures s’accumulant sur une longue période, et la nécessité d’interroger sur la base de plages d’horodatage et d’autres filtres, l’utilisation d’un cumul de l’horodatage, par exemple d’une date, comme clé de partition constitue une bonne approche. Cela vous permet d’interroger toutes les données correspondant à une date à partir d’une seule partition. 
* Si votre charge de travail est lourde en écriture, ce qui est plus courant, vous devez utiliser une clé de partition qui n’est pas basée sur l’horodatage, afin que Cosmos DB puisse répartir uniformément les écritures sur diverses partitions. Dans ce cas, un nom d’hôte, un ID de processus, un ID d’activité ou une autre propriété présentant une cardinalité élevée constituent un bon choix. 
* Une troisième solution consiste à adopter une approche hybride, où vous avez plusieurs conteneurs, une pour chaque jour/mois, et où la clé de partition est une propriété granulaire telle que le nom d’hôte. Cela présente l’avantage que vous pouvez définir différents débits en fonction de la fenêtre temporelle. Par exemple, le conteneur pour le mois en cours est approvisionné avec un débit plus élevé, car elle sert les lectures et écritures, tandis que les mois précédents offrent un débit inférieur, car ils servent uniquement les lectures.

### <a name="partitioning-and-multi-tenancy"></a>Partitionnement et mutualisation
Si vous implémentez une application mutualisée à l’aide de Cosmos DB, il existe deux modèles populaires : une clé de partition par client et un conteneur par client. Voici les avantages et inconvénients de chaque modèle :

* Une clé de partition par client : dans ce modèle, les clients sont colocalisés au sein d’un conteneur unique. Mais les requêtes et les insertions pour les éléments avec un seul client peuvent être exécutées sur une partition unique. Vous pouvez également implémenter une logique transactionnelle sur tous les éléments avec un client. Étant donné que plusieurs clients partagent un conteneur, vous économisez des coûts de stockage et de débit en regroupant les ressources pour les clients dans un seul conteneur plutôt que d’approvisionner une marge supplémentaire pour chaque client. L’inconvénient est que vous n’avez pas d’isolation des performances par client. Les augmentations de performances/débit s’appliquent à l’ensemble du conteneur au lieu d’augmentations ciblées pour les clients.
* Un conteneur par client : chaque client possède son propre conteneur. Dans ce modèle, vous pouvez réserver des performances par client. Avec le nouveau modèle de tarification du provisionnement de Cosmos DB, ce modèle est plus rentable pour les applications mutualisées avec peu de clients.

Vous pouvez également utiliser une approche à plusieurs niveaux/combinée qui regroupe les petits clients et migre les clients plus volumineux vers leur propre conteneur.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, nous avons proposé une vue d’ensemble des concepts et bonnes pratiques pour le partitionnement avec l’API Azure Cosmos DB. 

* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md)
* En savoir plus sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md)




