<properties
 pageTitle="Utiliser JDBC pour interroger Hive sur Azure HDInsight"
 description="Découvrez comment utiliser JDBC pour vous connecter à Hive sur Azure HDInsight et exécuter des requêtes à distance sur les données stockées dans le cloud."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="07/17/2015"
 ms.author="larryfr"/>

#Se connecter à Hive sur Azure HDInsight à l’aide du pilote JDBC Hive

[AZURE.INCLUDE [sélecteur-JDBC-ODBC](../../includes/hdinsight-selector-odbc-jdbc.md)]

Dans ce document, vous allez apprendre à utiliser JDBC dans une application Java pour soumettre à distance des requêtes Hive à un cluster HDInsight. Pour plus d’informations sur l’interface JDBC pour Hive, consultez [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

##Composants requis

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop sur HDInsight. Les clusters basés sur Linux ou Windows fonctionnent.

* Le [Kit de développeur Java (JDK) version 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou supérieure.

* [Apache Maven](https://maven.apache.org). Maven est un système de conception de projets Java qui est utilisé par le projet associé à cet article.

##Chaîne de connexion

Les connexions JDBC à un cluster HDInsight sur Azure sont établies sur le port 443, et le trafic est sécurisé à l’aide de SSL. La passerelle publique derrière laquelle se trouvent les clusters redirige le trafic vers le port d’écoute réel d’HiveServer2. Ainsi, une chaîne de connexion classique ressemble à ce qui suit :

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

##Authentification

Lorsque vous établissez la connexion, vous devez spécifier le nom d’administrateur et le mot de passe du cluster HDInsight. Ces éléments authentifient la demande envoyée à la passerelle. Par exemple, le code Java suivant ouvre une nouvelle connexion à l’aide de la chaîne de connexion, du nom d’administrateur et du mot de passe :

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##Requêtes

Une fois la connexion établie, vous pouvez exécuter des requêtes sur Hive. Par exemple, le code Java suivant exécute une requête __SELECT__ dans une table, en limitant les résultats à trois lignes seulement, puis affiche ces résultats :

    sql = "SELECT querytime, market, deviceplatform, devicemodel, state, country from " + tableName + " LIMIT 3";
    stmt2 = conn.createStatement();
    System.out.println("\nRetrieving inserted data:");

    res2 = stmt2.executeQuery(sql);

    while (res2.next()) {
      System.out.println( res2.getString(1) + "\t" + res2.getString(2) + "\t" + res2.getString(3) + "\t" + res2.getString(4) + "\t" + res2.getString(5) + "\t" + res2.getString(6));
    }

##Exemple de projet Java

Un exemple d’utilisation de client Java pour interroger Hive sur HDInsight est disponible à l’adresse [https://github.com/Blackmist/hdinsight-hive-jdbc](https://github.com/Blackmist/hdinsight-hive-jdbc). Suivez les instructions indiquées dans le référentiel pour générer et exécuter l’exemple.

##Étapes suivantes

Maintenant que vous avez vu comment utiliser JDBC avec Hive, utilisez les liens suivants pour découvrir d’autres façons d’utiliser Azure HDInsight.

* [Téléchargement de données vers HDInsight](hdinsight-upload-data.md)
* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO8-->