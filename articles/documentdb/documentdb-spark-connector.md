---
title: "Connexion d’Apache Spark à Azure Cosmos DB | Microsoft Docs"
description: "Grâce à ce didacticiel, vous en saurez davantage sur le connecteur Spark-Azure Cosmos DB, qui vous permet de connecter Apache Spark à Azure Cosmos DB pour réaliser des agrégations distribuées et des opérations de sciences des donnés sur un système de base de données Microsoft mutualisé, distribué dans le monde entier et conçu pour le cloud."
keywords: apache spark
services: cosmosdb
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: denlee
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6a5db515bec785aa29b29a3096dc20a72056bd08
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Accélérer l’analyse en temps réel des données volumineuses grâce au connecteur Spark-Azure Cosmos DB

Le connecteur Spark-Azure Cosmos DB permet à Cosmos DB de jouer le rôle de source d’entrée ou de récepteur de sortie pour les travaux Apache Spark. Connecter [Spark](http://spark.apache.org/) à [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) accélère votre capacité à résoudre les problèmes liés aux opérations de sciences de données dynamiques, où les données peuvent rapidement être à l’état persistant et interrogées à l’aide de Cosmos DB. Le connecteur Spark-Azure Cosmos DB tire le meilleur parti des index gérés Cosmos DB natifs et active les colonnes pouvant être mises à jour lors de l’exécution d’analyses et de filtres de prédicat sur les données distribuées dans le monde entier en constante évolution, pour les scénarios d’analyse, d’opération de sciences de données et IoT. 

## <a name="download"></a>Télécharger

Commencez en téléchargeant le connecteur Spark-Azure Cosmos DB (préversion) depuis le référentiel [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark/) sur GitHub.

## <a name="connector-components"></a>Composants du connecteur

Le connecteur utilise les composants suivants :

* [Azure Cosmos DB](http://documentdb.com), un [système de base de données Microsoft mutualisé](documentdb-distribute-data-globally.md), distribué dans le monde entier et conçu pour le cloud. Azure Cosmos DB permet à ses utilisateurs d’approvisionner et d’adapter leurs capacités de débit et de stockage partout dans le monde. Le service offre une latence faible garantie au 99e centile, une haute disponibilité garantie à 99,99 %, et [plusieurs modèles de cohérence bien définis](documentdb-consistency-levels.md) aux développeurs.

* [Apache Spark](http://spark.apache.org/), un moteur de traitement puissant et open source basé sur la vitesse, la facilité d’utilisation et des analyses sophistiquées. 

* [Apache Spark sur Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Vous pouvez déployer Apache Spark dans le cloud pour les déploiements stratégiques à l’aide de [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Versions officiellement prises en charge :

| Composant | Version |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| Kit de développement logiciel (SDK) Azure DocumentDB Java | 1.9.6 |
 
Cet article vous permet d’exécuter quelques exemples simples avec Python (via pyDocumentDB) et l’interface Scala.

Vous pouvez choisir entre deux approches pour connecter Apache Spark et Azure Cosmos DB :
- Utilisez pyDocumentDB via le [Kit de développement logiciel (SDK) Azure DocumentDB Python](https://github.com/Azure/azure-documentdb-python).
- Créez un connecteur Spark-DocumentDB basé sur Java qui utilise le [Kit de développement logiciel (SDK) Azure DocumentDB Java](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>Implémentation de pyDocumentDB 
Le [kit de développement logiciel (SDK) pyCosmos DB](https://github.com/Azure/azure-documentdb-python) actuel permet de connecter Spark à Azure Cosmos DB comme indiqué dans le schéma suivant :

![Flux de données Spark-Cosmos DB via pyCosmos DB](./media/documentdb-spark-connector/azure-documentdb-spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Flux de données de l’implémentation de pyDocumentDB

Le flux de données est le suivant :

1. La connexion est établie à partir du nœud maître Spark vers le nœud de passerelle Cosmos DB via pyCosmos DB.  Notez que l’utilisateur spécifie uniquement des connexions Spark et Cosmos DB, le fait qu’il se connecte aux nœuds maître et de passerelle respectifs est transparent pour l’utilisateur.
2. Des requêtes sont envoyées à Cosmos DB (via le nœud de passerelle), où elles s’exécutent ensuite sur les partitions de la collection dans les nœuds de données. La réponse à ces requêtes est renvoyée au nœud de passerelle, et ce jeu de résultats est renvoyé au nœud maître Spark.
3. Toutes les requêtes suivantes (par exemple, sur un tableau de données Spark) sont envoyées aux nœuds Worker Spark pour traitement.

L’opération importante est que la communication entre Spark et Cosmos DB est limitée au nœud maître Spark et aux nœuds de passerelle Cosmos DB.  Les requêtes sont aussi rapides que la couche de transport entre ces deux nœuds.

### <a name="installing-pydocumentdb"></a>Installation de pyDocumentDB
Vous pouvez installer pyDocumentDB sur votre nœud pilote à l’aide de **pip**, par exemple :

```
pip install pyDocumentDB
```


### <a name="connecting-spark-to-cosmos-db-via-pycosmos-db"></a>Connexion de Spark à Cosmos DB via pyCosmos DB 
À l’inverse du transport des communications pouvant être complexe, l’exécution d’une requête à partir de Spark vers Cosmos DB à l’aide de pyCosmos DB est relativement simple.

L’extrait de code suivant décrit comment utiliser pyDocumentDB dans un contexte Spark.

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery 
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Cosmos DB 
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==' 
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Comme indiqué dans l’extrait de code :

* Le Kit de développement logiciel (SDK) DocumentDB Python contient tous les paramètres de connexion nécessaires, y compris les emplacements préférés (p. ex. choix du réplica en lecture et son ordre de priorité).
*  Importez les bibliothèques nécessaires et configurez votre **masterKey** et **hôte** pour créer le *client* Cosmos DB (**pydocumentdb.document_client**).


### <a name="executing-spark-queries-via-pydocumentdb"></a>Exécution de requêtes Spark via pyDocumentDB
Les exemples suivants utilisent l’instance Cosmos DB créée dans l’extrait de code précédent à l’aide des clés en lecture seule spécifiées.  L’extrait de code suivant se connecte à la collection **airports.codes** (dans le compte DoctorWho, comme indiqué précédemment) en exécutant une requête pour extraire les villes possédant des aéroports dans l’État de Washington. 

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

Une fois que la requête a été exécutée via **query**, le résultat est un élément **query_iterable. QueryIterable** qui est converti en une liste Python. Une liste Python peut facilement être convertie en un tableau de données Spark en utilisant le code suivant :

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-documentdb"></a>Pourquoi utiliser pyDocumentDB pour connecter Spark à DocumentDB ?
pyCosmos DB est généralement utilisé pour connecter Spark à Cosmos DB pour les scénarios dans lesquels :

* Vous souhaitez utiliser Python.
* Un petit jeu de résultats est renvoyé de DocumentDB à Spark.  Notez que le jeu de données sous-jacent dans DocumentDB peut être assez volumineux. Vous n’appliquez pas simplement des filtres, vous exécutez des filtres de prédicat sur votre source DocumentDB.  

## <a name="spark-to-cosmos-db-connector"></a>Connecteur Spark-Cosmos DB

Le connecteur Spark-Cosmos DB utilise le [kit de développement logiciel (SDK) Azure Cosmos DB Java](https://github.com/Azure/azure-documentdb-java) et déplace les données entre les nœuds Worker Spark et Cosmos DB comme indiqué dans le schéma suivant :

![Flux de données dans le connecteur Spark-Cosmos DB](./media/documentdb-spark-connector/azure-documentdb-spark-connector.png)

Le flux de données est le suivant :

1. Une connexion est établie à partir du nœud maître Spark vers le nœud de passerelle Cosmos DB pour obtenir le mappage de partitions.  Notez que l’utilisateur spécifie uniquement des connexions Spark et Cosmos DB, le fait qu’il se connecte aux nœuds maître et de passerelle respectifs est transparent pour l’utilisateur.
2. Ces informations sont renvoyées au nœud maître Spark.  À ce stade, vous devez être en mesure d’analyser la requête pour déterminer les partitions (et leurs emplacements) auxquelles vous devez accéder dans Cosmos DB.
3. Ces informations sont transmises aux nœuds Worker Spark...
4. Cela permet donc aux nœuds Worker Spark de se connecter directement aux partitions Cosmos DB pour extraire les données qui sont nécessaires et renvoyer les données aux partitions Spark dans les nœuds Worker Spark.

L’opération importante est que la communication entre Spark et Cosmos DB est beaucoup plus rapide, car les données sont déplacées entre les nœuds Worker Spark et les nœuds de données Cosmos DB (partitions).

### <a name="building-the-spark-to-cosmos-db-connector"></a>Création du connecteur Spark-Cosmos DB
Actuellement, le projet de connecteur utilise maven. Pour créer le connecteur sans dépendances, vous pouvez exécuter :
```
mvn clean package
```
Vous pouvez également télécharger les dernières versions du fichier jar dans le dossier *releases*.

### <a name="including-the-azure-documentdb-spark-jar"></a>Intégration du fichier JAR Azure DocumentDB Spark
Avant d’exécuter du code, vous devez intégrer le fichier JAR Azure DocumentDB Spark.  Si vous utilisez **spark-shell**, vous pouvez intégrer le fichier JAR à l’aide l’option **--jars**.  

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1-jar-with-dependencies.jar
```

Ou, si vous souhaitez exécuter le fichier jar sans dépendances :

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1.jar,/$location/azure-documentdb-1.9.6.jar
```

Si vous utilisez un service de bloc-notes comme le bloc-notes Jupyter Azure HDInsight, vous pouvez utiliser les commandes **spark magic** :

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.9.6.jar","wasb:///example/jars/azure-documentdb-spark-0.0.1.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

La commande **jars** vous permet d’intégrer les deux fichiers jar nécessaires pour **azure-documentdb-spark** (lui-même et le Kit de développement logiciel (SDK) Azure DocumentDB Java) et exclut **scala-reflect** afin qu’il n’interfère pas avec les appels Livy (bloc-notes Jupyter > Livy > Spark).

### <a name="connecting-spark-to-cosmos-db-using-the-connector"></a>Connexion de Spark à Cosmos DB à l’aide du connecteur
Alors que le transport des communications est un peu plus complexe, l’exécution d’une requête de Spark vers Cosmos DB à l’aide du connecteur est très rapide.

L’extrait de code suivant décrit comment utiliser le connecteur dans un contexte Spark.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))
 
// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Comme indiqué dans l’extrait de code :

- **azure-documentdb-spark** contient tous les paramètres de connexion nécessaires, y compris les emplacements préférés (p. ex. choix du réplica en lecture et son ordre de priorité).
- Importez simplement les bibliothèques nécessaires et configurez votre masterKey et hôte pour créer le client Cosmos DB.

### <a name="executing-spark-queries-via-the-connector"></a>Exécution de requêtes Spark via le connecteur

L’exemple suivant utilise l’instance Cosmos DB créée dans l’extrait de code précédent à l’aide des clés en lecture seule spécifiées. L’extrait de code suivant se connecte à la collection DepartureDelays.flights_pcoll (dans le compte DoctorWho, comme indiqué précédemment) en exécutant une requête pour extraire les informations de retard des vols au départ de Seattle.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-cosmos-db-connector-implementation"></a>Pourquoi utiliser l’implémentation du connecteur Spark-Cosmos DB ?

Le connecteur est généralement utilisé pour connecter Spark à Cosmos DB pour les scénarios dans lesquels :

* Vous souhaitez utiliser Scala (et le mettre à jour pour inclure un wrapper Python, comme indiqué dans [Issue 3: Add Python wrapper and examples](https://github.com/Azure/azure-documentdb-spark/issues/3) (Problème n° 3 : Ajouter un wrapper Python et des exemples)).
* Vous avez une grande quantité de données à transférer entre Apache Spark et Cosmos DB.

Pour vous donner une idée de la différence des performances de requête, consultez la [wiki sur les exécutions de tests de requête](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Exemple d’agrégations distribuées
Cette section fournit des exemples pour réaliser des agrégations distribuées et des analyses à l’aide d’Apache Spark et d’Azure Cosmos DB.  Notez qu’Azure Cosmos DB prend déjà en charge les agrégations, comme indiqué dans [l’article de blog Planet scale aggregates with Azure DocumentDB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/) (Agrégats à l’échelle de la planète avec Azure DocumentDB), et voici donc comment passer au niveau supérieur avec Apache Spark.

Notez que ces agrégations font référence au [bloc-notes de connecteur Spark-Cosmos DB](https://github.com/Azure/azure-documentdb-spark/blob/master/samples/notebooks/Spark-to-Cosmos DB_Connector.ipynb).

### <a name="connecting-to-flights-sample-data"></a>Connexion à des données d’exemple de vols
Pour ces exemples d’agrégations, nous accédons à certaines données de performances de vol stockées dans notre base de données Cosmos DB **DoctorWho**.  Pour vous connecter à celle-ci, vous devez utiliser l’extrait de code suivant :

```
// Import Spark to Cosmos DB connector
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Connect to Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))

// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Avec cet extrait de code, nous allons également exécuter une requête de base qui transfère l’ensemble de données filtré de Cosmos DB à Spark (où ce dernier peut réaliser des agrégats distribués).  Dans ce cas, nous demandons des vols au départ de Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Les résultats suivants ont été générés par l’exécution des requêtes à partir du service de bloc-notes Jupyter.  Notez que tous les extraits de code sont génériques et non spécifiques à un service.

### <a name="running-limit-and-count-queries"></a>Exécution des requêtes LIMIT et COUNT
Comme vous y êtes habitué dans SQL/Spark SQL, commençons par une requête **LIMIT** :

![Requête LIMIT Spark](./media/documentdb-spark-connector/azure-documentdb-spark-sql-query.png)

La requête suivante est une requête **COUNT** simple et rapide :

![Requête COUNT Spark](./media/documentdb-spark-connector/azure-documentdb-spark-count-query.png)

### <a name="group-by-query"></a>Requête GROUP BY
Dans l’ensemble suivant, nous pouvons facilement exécuter des requêtes **GROUP BY** sur notre base de données Cosmos DB :

```
select destination, sum(delay) as TotalDelays 
from originSEA 
group by destination 
order by sum(delay) desc limit 10
```

![Graphique de la requête GROUP BY Spark](./media/documentdb-spark-connector/azure-documentdb-group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>Requête DISTINCT, ORDER BY
Et voici une requête **DISTINCT, ORDER BY** :

![Graphique de la requête GROUP BY Spark](./media/documentdb-spark-connector/azure-documentdb-order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Continuer l’analyse des données de vol
Vous pouvez utiliser les exemples de requête suivants pour continuer l’analyse des données de vol :

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Les 5 principales destinations (villes) vers lesquelles il y a du retard au départ de Seattle
```
select destination, sum(delay) 
from originSEA
where delay < 0 
group by destination 
order by sum(delay) limit 5
```
![Graphique des retards principaux Spark](./media/documentdb-spark-connector/azure-documentdb-top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Calculer les retards médians par villes de destination au départ de Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay 
from originSEA
where delay < 0 
group by destination 
order by percentile_approx(delay, 0.5)
```

![Graphique des retards médians Spark](./media/documentdb-spark-connector/azure-documentdb-median-delays-graph.png)

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas encore fait, téléchargez le connecteur Spark-Cosmos DB à partir du référentiel GitHub [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark) et explorez les ressources supplémentaires dans le référentiel :

* [Distributed Aggregations Examples](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples) (Exemples d’agrégations distribuées)
* [Sample Scripts and Notebooks](https://github.com/Azure/azure-documentdb-spark/tree/master/samples) (Exemples de scripts et blocs-notes)

Vous pouvez également consulter [Apache Spark SQL, DataFrames, and Datasets Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html) (Guide sur Apache Spark SQL, les tableaux de données et les jeux de données) et l’article [Prise en main : Créer le cluster Apache Spark sur Azure HDInsight et exécuter des requêtes interactives à l’aide de Spark SQL](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md).



