---
title: "Azure Cosmos DB : Exécuter des analyses graphiques à l’aide de Spark et d’Apache TinkerPops Gremlin | Microsoft Docs"
description: "Présente des instructions pour configurer et exécuter des analyses graphiques, ainsi que le parallélisme Azure Cosmos DB avec Spark GraphX"
services: cosmos-db
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: khdang
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 7a95953fadb3089f60fd55973fdb3410012655af
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-perform-graph-analytics-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB : Exécuter des analyses graphiques à l’aide de Spark et d’Apache TinkerPop Gremlin

[Azure Cosmos DB](introduction.md) est le service de base de données multi-modèle mondialement distribué de Microsoft. Vous pouvez créer et interroger des bases de données de documents, de paires clé-valeur et de graphiques, qui bénéficient toutes des fonctionnalités de distribution mondiale et de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. Azure Cosmos DB prend en charge les charges de travail graphiques OLTP à l’aide de [Gremlin d’Apache TinkerPop](graph-introduction.md)

[Spark](http://spark.apache.org/) est un projet Apache Software Foundation consacré au traitement des données OLAP à usage général. Spark fournit un modèle informatique distribué hybride en mémoire/sur disque, similaire au modèle MapReduce de Hadoop. Vous pouvez déployer Apache Spark dans le cloud à l’aide [d’Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/). Spark inclut la [bibliothèque GraphX](http://spark.apache.org/graphx/) pour les graphiques, ainsi que le graphisme-parallélisme qui utilise également Apache TinkerPop Gremlin.

En associant Azure Cosmos DB et Spark, vous pouvez exécuter des charges de travail OLTP et OLAP à l’aide de Gremlin. Ce guide de démarrage rapide montre comment exécuter des requêtes Gremlin dans Azure Cosmos DB sur un cluster Azure HDInsight Spark.

## <a name="prerequisites"></a>Composants requis

* Avant de pouvoir exécuter cet exemple, vous devez posséder les composants requis suivants :
   * Cluster Azure HDInsight Spark 2.0
   * JDK 1.8+ (exécutez `apt-get install default-jdk` si vous ne possédez pas JDK)
   * Maven (exécutez `apt-get install maven` si vous ne possédez pas Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Pour plus d’informations sur la configuration d’un cluster Azure HDInsight Spark, consultez [Approvisionnement des clusters HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Créer un compte de base de données Azure Cosmos DB

Tout d’abord, nous créons un compte de base de données avec l’API Graph.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Ajouter une collection

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="get-apache-tinkerpop"></a>Obtenir Apache TinkerPop

* À distance du nœud principal du cluster HDInsight `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`

* Cloner le code source de TinkerPop3, le générer localement et l’installer dans le cache de Maven

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

* Installer le plug-in Spark-Gremlin 

    1. L’installation du plug-in est gérée par Grape. Nous devons d’abord renseigner les informations des référentiels pour Grape de sorte qu’il puisse télécharger le plug-in et ses dépendances. 

        * Créez le fichier de configuration Grape s’il est absent de `~/.groovy/grapeConfig.xml`. Utilisez les paramètres suivants :

            ```xml
            <ivysettings>
            <settings defaultResolver="downloadGrapes"/>
            <resolvers>
                <chain name="downloadGrapes">
                <filesystem name="cachedGrapes">
                    <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
                    <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
                </filesystem>
                <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
                <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
                <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
                <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
                <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
                <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
                </chain>
            </resolvers>
            </ivysettings>
            ``` 

    2. Lancer la console Gremlin `bin/gremlin.sh`
        
    3. Installer le plug-in Spark-Gremlin avec la version 3.3.0-SNAPSHOT créée dans les étapes précédentes

        ```bash
        $ bin/gremlin.sh

                \,,,/
                (o o)
        -----oOOo-(3)-oOOo-----
        plugin activated: tinkerpop.server
        plugin activated: tinkerpop.utilities
        plugin activated: tinkerpop.tinkergraph
        gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
        ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
        gremlin> :q
        $ bin/gremlin.sh

                \,,,/
                (o o)
        -----oOOo-(3)-oOOo-----
        plugin activated: tinkerpop.server
        plugin activated: tinkerpop.utilities
        plugin activated: tinkerpop.tinkergraph
        gremlin> :plugin use tinkerpop.spark
        ==>tinkerpop.spark activated
        ```

    4. Si `Hadoop-Gremlin` est activé avec `:plugin list` : nous devons désactiver ce plug-in, car il peut interférer avec le plug-in Spark-Gremlin `:plugin unuse tinkerpop.hadoop`

## <a name="prepare-tinkerpop3-dependencies"></a>Préparer les dépendances TinkerPop3

Lorsque nous avons créé TinkerPop3 à l’étape précédente, toutes les dépendances JAR pour Spark et Hadoop ont également été extraites dans le répertoire cible. Nous allons utiliser les fichiers JAR qui sont préinstallés avec HDI et extraire uniquement les dépendances supplémentaires en cas de besoin.

    1. Accédez au répertoire cible de la console Gremlin à l’emplacement `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

    2. Là, déplacez tous les fichiers JAR sous `ext/` vers `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`

    3. Sous `lib/`, supprimez toutes les bibliothèques JAR qui ne figurent pas dans la liste ci-dessous.

        ```bash
        # TinkerPop3
        gremlin-core-3.3.0-SNAPSHOT.jar       
        gremlin-groovy-3.3.0-SNAPSHOT.jar     
        gremlin-shaded-3.3.0-SNAPSHOT.jar     
        hadoop-gremlin-3.3.0-SNAPSHOT.jar     
        spark-gremlin-3.3.0-SNAPSHOT.jar      
        tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

        # Gremlin depedencies
        asm-3.2.jar                                
        avro-1.7.4.jar                             
        caffeine-2.3.1.jar                         
        cglib-2.2.1-v20090111.jar                  
        gbench-0.4.3-groovy-2.4.jar                
        gprof-0.3.1-groovy-2.4.jar                 
        groovy-2.4.9-indy.jar                      
        groovy-2.4.9.jar                           
        groovy-console-2.4.9.jar                   
        groovy-groovysh-2.4.9-indy.jar             
        groovy-json-2.4.9-indy.jar                 
        groovy-jsr223-2.4.9-indy.jar               
        groovy-sql-2.4.9-indy.jar                  
        groovy-swing-2.4.9.jar                     
        groovy-templates-2.4.9.jar                 
        groovy-xml-2.4.9.jar                       
        hadoop-yarn-server-nodemanager-2.7.2.jar   
        hppc-0.7.1.jar                             
        javatuples-1.2.jar                         
        jaxb-impl-2.2.3-1.jar                      
        jbcrypt-0.4.jar                            
        jcabi-log-0.14.jar                         
        jcabi-manifests-1.1.jar                    
        jersey-core-1.9.jar                        
        jersey-guice-1.9.jar                       
        jersey-json-1.9.jar                        
        jettison-1.1.jar                           
        scalatest_2.11-2.2.6.jar                   
        servlet-api-2.5.jar                        
        snakeyaml-1.15.jar                         
        unused-1.0.0.jar                           
        xml-apis-1.3.04.jar                        
        ```

## <a name="get-the-cosmos-db-spark-connector"></a>Obtenir le connecteur Spark Cosmos DB

1. Obtenir le connecteur Spark Cosmos DB `azure-documentdb-spark-0.0.3-SNAPSHOT.jar` et le Kit de développement logiciel (SDK) Java Cosmos DB `azure-documentdb-1.10.0.jar` à partir du [connecteur Spark Azure Cosmos DB sur Github](https://github.com/Azure/azure-documentdb-spark/tree/master/releases/azure-documentdb-spark-0.0.3_2.0.2_2.11)

2. Sinon, vous pouvez le créer localement. Étant donné que la dernière version de Spark-Gremlin a été créée avec Spark 1.6.1 et qu’elle n’est pas compatible avec la version Spark 2.0.2 actuellement utilisée dans le connecteur Spark Cosmos DB, vous pouvez générer le code TinkerPop3 le plus récent et installer manuellement les fichiers JAR.

    1. Cloner le connecteur Spark Cosmos DB

    2. Générer TinkerPop3 (déjà effectué lors des étapes précédentes). Installer tous les fichiers JAR TinkerPop 3.3.0-SNAPSHOT localement
    
    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    2. Mettre à jour `tinkerpop.version` `azure-documentdb-spark/pom.xml` vers `3.3.0-SNAPSHOT`
    
    3. Générer avec Maven. Les fichiers JAR nécessaires sont placés dans `target` et `target/alternateLocation`

    ```bash
    git clone https://github.com/Azure/azure-documentdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Copier les fichiers JAR mentionnés ci-dessus vers un répertoire local à l’emplacement ~/azure-documentdb-spark

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Distribuer les dépendances sur les nœuds de travail Spark 

1. Étant donné que la transformation des données de graphique dépend de TinkerPop3, nous devons distribuer les dépendances associées à tous les nœuds de travail Spark.

2. Copier les dépendances Gremlin indiquées précédemment, ainsi que le fichier JAR du connecteur Spark CosmosDB et le Kit de développement logiciel (SDK) CosmosDB Java sur les nœuds de travail comme suit

    1. Copier tous les fichiers JAR dans `~/azure-documentdb-spark`

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    2. Obtenir la liste de tous les nœuds de travail Spark, qui se trouve sur le tableau de bord Ambari, dans la liste `Spark2 Clients`, section `Spark2`.

    3. Copier ce répertoire sur chacun des nœuds

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Définir les variables d’environnement

1. Rechercher la version HDP du cluster Spark, il s’agit du nom de répertoire sous `/usr/hdp/`, par exemple « 2.5.4.2-7 ».

2. Définir hdp.version pour tous les nœuds : dans le tableau de bord Ambari, accéder à `YARN section -> Configs -> Advanced`. Dans `Custom yarn-site`, ajoutez une nouvelle propriété `hdp.version` avec la valeur de la version HDP sur le nœud principal. Enregistrez les configurations ; des avertissements s’affichent mais vous pouvez les ignorer. Ensuite, redémarrez les services YARN et Oozie, comme l’indiquent les icônes de notification.

3. Définissez les variables d’environnement suivantes sur le nœud principal (remplacez les valeurs appropriées) :

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Préparer la configuration du graphique

1. Créer un fichier de configuration avec les paramètres de connexion Cosmos DB et les paramètres Spark, et le placer à l’emplacement `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark Connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Mettre à jour `spark.driver.extraClassPath` et `spark.executor.extraClassPath` pour inclure le répertoire des fichiers JAR que nous avons distribués à l’étape précédente, dans le cas présent `/home/sshuser/azure-documentdb-spark/*`

3. Renseignez les informations suivantes pour Cosmos DB :

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-tinkerpops-graph-and-save-to-cosmos-db"></a>Charger le graphique de TinkerPop et l’enregistrer dans Cosmos DB
Pour illustrer la manière dont nous conservons un graphique dans Cosmos DB, nous allons utiliser le graphique moderne TinkerPop prédéfini en tant qu’exemple. Le graphique a été stocké au format Kryo et a été fourni dans le référentiel tinkerpop.

1. Étant donné que nous exécutons Gremlin en mode YARN, nous devons mettre les données de graphique à disposition dans le système de fichiers Hadoop. Les commandes ci-dessous créent un répertoire et copient le fichier graphique local dans celui-ci. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Mettez à jour temporairement le fichier `gremlin-spark.properties` pour utiliser `GryoInputFormat` pour lire le graphique. En outre, nous indiquons également `inputLocation` comme répertoire créé par nos soins, comme suit :

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

2. Lancer la console Gremlin et créer ces étapes de calcul pour conserver les données dans la collection Cosmos DB configurée :  

    1. Créer le graphique `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`

    2. Utiliser SparkGraphComputer pour l’écriture : `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

3. Dans l’Explorateur de données, nous pouvons vérifier que les données ont été ingérées dans Cosmos DB.

## <a name="load-the-graph-from-cosmos-db-and-run-gremlin-queries"></a>Charger le graphique de charge depuis Cosmos DB et exécuter des requêtes Gremlin

1. Pour charger le graphique, modifiez `gremlin-spark.properties` pour définir `graphReader` sur `DocumentDBInputRDD` :

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Suivez les étapes ci-dessous pour charger le graphique, parcourir les données et exécuter des requêtes Gremlin avec celui-ci :

    1. Lancer la console Gremlin`bin/gremlin.sh`

    2. Créer le graphique avec les configurations `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`

    3. Créer une traversée de graphique avec SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`

    4. Exécuter des requêtes de graphique Gremlin :

        ```bash
        gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
        ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
        gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
        ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
        gremlin> g.V().count()
        ==>6
        gremlin> g.E().count()
        ==>6
        gremlin> g.V(1).out().values('name')
        ==>josh
        ==>vadas
        ==>lop
        gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
        ==>josh
        ==>peter
        ==>vadas
        ==>marko
        gremlin> g.V().hasLabel('person').
               choose(values('name')).
                 option('marko', values('age')).
                 option('josh', values('name')).
                 option('vadas', valueMap()).
                 option('peter', label())
        ==>josh
        ==>person
        ==>[name:[vadas],age:[27]]
        ==>29
        ```

> [!NOTE]
> Pour afficher une journalisation plus détaillée, dans `conf/log4j-console.properties`, définissez un niveau de journalisation plus détaillé.
>

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à travailler avec des graphiques en associant Azure Cosmos DB et Spark.

> [!div class="nextstepaction"]

