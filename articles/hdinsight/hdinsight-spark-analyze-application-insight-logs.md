<properties
pageTitle="Analyser les journaux d’Application Insight avec Spark sur HDInsight | Microsoft Azure"
description="Découvrez comment exporter des journaux d’Application Insight pour le stockage d’objets blob, puis comment analyser les journaux avec Spark sur HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/15/2016"
ms.author="larryfr"/>

# Analyser les journaux de télémétrie Application Insights avec Spark sur HDInsight

[Visual Studio Application Insights](../application-insights/app-insights-overview.md) est un service d’analyse qui surveille vos applications web. Les données de télémétrie générées par Application Insights peuvent être exportées vers Azure Storage, et de là, elles peuvent être analysées par HDInsight.

Dans ce document, vous allez apprendre à utiliser HDInsight pour analyser les données de télémétrie Application Insight à l’aide d’Apache Spark.

## Conditions préalables

* Un abonnement Azure.

* Une application configurée pour utiliser Application Insights.

* Maîtrise de la création d’un cluster HDInsight sous Linux. Si vous ne maîtrisez pas la création d’un cluster, consultez la rubrique [Créer Spark sur HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) pour plus d’informations.

    > [AZURE.NOTE] Ce document ne fournit aucune procédure détaillée concernant la création d’un cluster. Au lieu de cela, il fait référence à d’autres documents qui contiennent des informations sur la création d’un cluster capable d’accéder aux données de télémétrie.

* Un navigateur Web. Utilisé pour exécuter interactivement l’analyse à l’aide d’un notebook Jupyter.

Les éléments suivants ont été utilisés lors du développement et du test de ce document :

* Les données de télémétrie d’Application Insights ont été générées à l’aide d’une [application web Node.js configurée pour utiliser Application Insights](../application-insights/app-insights-nodejs.md).

* Un système Spark Linux sur le cluster HDInsight version 3.4 a été utilisé pour analyser les données.

## Architecture et planification

Le schéma suivant illustre l'architecture du service dans cet exemple :

![diagramme montrant les données transmises par Application Insights vers le stockage d’objets blob, puis traitées par Spark sur HDInsight](./media/hdinsight-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### Stockage Azure

Un cluster HDInsight peut accéder directement aux objets blob de blocs d’un compte de stockage Azure, et Application Insights peut être configurée pour exporter en permanence des informations de télémétrie vers des objets blob dans Azure Storage. Toutefois, il existe certaines conditions que vous devez remplir :

* __Emplacement__ : le compte de stockage doit figurer dans la même région que HDInsight. Cela réduit la latence lors de l’accès aux données et évite les frais d’acheminement qu’entraîne le déplacement des données entre différentes régions.

* __Type d’objet blob__ : HDInsight prend uniquement en charge les objets blob de blocs. Application Insights utilise par défaut des objets blob de blocs, et devrait donc fonctionner par défaut avec HDInsight.

* __Autorisations d’accès__ : si vous utilisez le même compte de stockage pour l’exportation continue d’Application Insights et le stockage par défaut HDInsight, HDInsight dispose d’un accès total aux données de télémétrie Application Insight. Cela signifie qu’il est possible de supprimer les données de télémétrie du cluster HDInsight.

    Au lieu de cela, il est recommandé d’utiliser des comptes de stockage distincts pour la télémétrie HDInsight et Application Insights, et d[’utiliser des signatures d’accès partagé (SAP) pour restreindre l’accès aux données à partir de HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md). L’utilisation d’une signature d’accès partagé vous permet d’accorder un accès en lecture seule à HDInsight pour les données de télémétrie.

### Schéma de données

Application Insights fournit des informations [Modèle d’exportation de données](../application-insights/app-insights-export-data-model.md) pour le format des données de télémétrie exportées vers des objets blob. Les étapes décrites dans ce document permettent d’utiliser Spark SQL avec les données. Spark SQL peut générer automatiquement un schéma pour la structure de données JSON consignées par Application Insights, et vous n’avez donc pas à définir manuellement le schéma lors de l’analyse.

## Exporter les données de télémétrie

Suivez les étapes de la procédure [Configuration de l’exportation continue](../application-insights/app-insights-export-telemetry.md) pour configurer votre Application Insights afin d’exporter les informations de télémétrie vers un objet blob de stockage Azure.

## Configurer HDInsight pour accéder aux données

Utilisez les informations de la rubrique [Utiliser des signatures d’accès partagé (SAP) pour restreindre l’accès aux données à partir de HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md) afin de créer une SAP pour le conteneur d’objets blob contenant les données de télémétrie exportées. Les SAP devraient fournir un accès en lecture seule aux données.

Le document Signature d’accès partagé fournit des informations sur l’ajout du stockage SAP à un cluster HDInsight Linux. Il fournit également des informations sur l’ajout de ce stockage lors de la création d’un cluster HDInsight.

## Analyser les données à l’aide de Python (PySpark)

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre Spark sur le cluster HDInsight. Dans la section __Liens rapides__, sélectionnez __Tableaux de bord de Cluster__, puis __Notebook Jupyter__ dans le panneau du tableau de bord du cluster.

    ![Les tableaux de bord du cluster](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)

2. Dans l’angle supérieur droit de la page Jupyter, sélectionnez __Nouveau__, puis __PySpark__. Un nouvel onglet de navigateur s’ouvre, contenant un notebook Jupyter basé sur Python.

3. Dans le premier champ (appelé une __cellule__) sur la page, entrez les informations suivantes :

        sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')

    Cela permet à Spark d’accéder de manière récursive à la structure de répertoires des données d’entrée. Les données de télémétrie Application Insights sont consignées dans une structure de répertoires similaire à ce qui suit :

        /{telemetry type}/YYYY-MM-DD/{##}/

4. Utilisez __MAJ + ENTRÉE__ pour exécuter le code. Sur le côté gauche de la cellule, un symbole '*’ apparaît entre crochets pour indiquer que le code de cette cellule est en cours d’exécution. Une fois l’opération terminée, le symbole ’*’ se transforme en nombre et une sortie similaire à ce qui suit s’affiche au-dessous de la cellule :

        Creating SparkContext as 'sc'

        ID	YARN Application ID	Kind	State	Spark UI	Driver log	Current session?
        3	application_1468969497124_0001	pyspark	idle	Link	Link	✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Une nouvelle cellule est créée sous la première. Entrez les informations suivantes dans la nouvelle cellule. Remplacez __CONTAINER__ et __STORAGEACCOUNT__ par le nom du compte de stockage Azure et le nom du conteneur d’objets blob que vous avez utilisés lors de la configuration de l’exportation continue d’Application Insights.

        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
    
    Utilisez __MAJ + ENTRÉE__ pour exécuter cette cellule. Vous devez voir un résultat similaire à ce qui suit :

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    
    Le chemin d’accès wasb retourné correspond à l’emplacement des données de télémétrie Application Insights. Modifiez la ligne `hdfs dfs -ls` de la cellule afin d’utiliser le chemin d’accès wasb retourné, puis utilisez __MAJ + ENTRÉE__ pour réexécuter la cellule. Cette fois, les résultats devraient afficher les répertoires contenant les données de télémétrie.

    > [AZURE.NOTE] Pour le reste des étapes de cette section, le répertoire `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` a été utilisé. Ce répertoire n’existe peut-être pas, sauf si vos données de télémétrie sont destinées à une application web. Si vous utilisez les données de télémétrie qui n’incluent aucun répertoire de requêtes, choisissez un autre répertoire et ajustez le reste des étapes afin d’utiliser ce répertoire et le schéma des données qui y sont stockées.

6. Dans la prochaine cellule, entrez les informations suivantes. Remplacez __WASB\_PATH__ par le chemin d’accès de l’étape précédente.

        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)

    Un nouveau tableau de données est créé à partir des fichiers JSON exportés via processus d’exportation continue. Utilisez __MAJ + ENTRÉE__ pour exécuter cette cellule.

7. Dans la cellule suivante, entrez et exécutez la commande ci-dessous pour afficher le schéma Spark créé pour les fichiers JSON :

        jsonData.printSchema()
    
    Le schéma sera différent pour chaque type de données de télémétrie. Voici le schéma généré pour les requêtes web (données stockées dans le sous-répertoire `Requests`) :

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Utilisez les informations suivantes pour enregistrer le tableau de données comme tableau temporaire, puis exécutez une requête sur les données :

        jsonData.registerTempTable("requests")
        sqlContext.sql("select context.location.city from requests where context.location.city is not null")

    Cette requête renvoie les informations deville pour les 20 premiers enregistrements pour lesquelles la valeur context.location.city n’est pas nulle.

    > [AZURE.NOTE] La structure de contexte est présente dans toutes les données de télémétrie consignées par Application Insights ; toutefois, l’élément de ville ne peut pas renseigné dans vos journaux. Utilisez le schéma pour identifier d’autres éléments que vous pouvez interroger et qui peuvent contenir des données pour vos journaux.

    Cette requête retourne des informations similaires à ce qui suit :

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## Analyser les données à l’aide de Scala

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre Spark sur le cluster HDInsight. Dans la section __Liens rapides__, sélectionnez __Tableaux de bord de Cluster__, puis __Notebook Jupyter__ dans le panneau du tableau de bord du cluster.

    ![Les tableaux de bord du cluster](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)

2. Dans l’angle supérieur droit de la page Jupyter, sélectionnez __Nouveau__, puis __Scala__. Un nouvel onglet de navigateur s’ouvre, contenant un notebook Jupyter basé sur Scala.

3. Dans le premier champ (appelé une __cellule__) sur la page, entrez les informations suivantes :

        sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")

    Cela permet à Spark d’accéder de manière récursive à la structure de répertoires des données d’entrée. Les données de télémétrie Application Insights sont consignées dans une structure de répertoires similaire à ce qui suit :

        /{telemetry type}/YYYY-MM-DD/{##}/

4. Utilisez __MAJ + ENTRÉE__ pour exécuter le code. Sur le côté gauche de la cellule, un symbole '*’ apparaît entre crochets pour indiquer que le code de cette cellule est en cours d’exécution. Une fois l’opération terminée, le symbole ’*’ se transforme en nombre et une sortie similaire à ce qui suit s’affiche au-dessous de la cellule :

        Creating SparkContext as 'sc'

        ID	YARN Application ID	Kind	State	Spark UI	Driver log	Current session?
        3	application_1468969497124_0001	spark	idle	Link	Link	✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Une nouvelle cellule est créée sous la première. Entrez les informations suivantes dans la nouvelle cellule. Remplacez __CONTAINER__ et __STORAGEACCOUNT__ par le nom du compte de stockage Azure et le nom du conteneur d’objets blob que vous avez utilisés lors de la configuration de l’exportation continue d’Application Insights.

        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
    
    Utilisez __MAJ + ENTRÉE__ pour exécuter cette cellule. Vous devez voir un résultat similaire à ce qui suit :

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    
    Le chemin d’accès wasb retourné correspond à l’emplacement des données de télémétrie Application Insights. Modifiez la ligne `hdfs dfs -ls` de la cellule afin d’utiliser le chemin d’accès wasb retourné, puis utilisez __MAJ + ENTRÉE__ pour réexécuter la cellule. Cette fois, les résultats devraient afficher les répertoires contenant les données de télémétrie.

    > [AZURE.NOTE] Pour le reste des étapes de cette section, le répertoire `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` a été utilisé. Ce répertoire n’existe peut-être pas, sauf si vos données de télémétrie sont destinées à une application web. Si vous utilisez les données de télémétrie qui n’incluent aucun répertoire de requêtes, choisissez un autre répertoire et ajustez le reste des étapes afin d’utiliser ce répertoire et le schéma des données qui y sont stockées.

6. Dans la prochaine cellule, entrez les informations suivantes. Remplacez __WASB\_PATH__ par le chemin d’accès de l’étape précédente.

        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)

    Un nouveau tableau de données est créé à partir des fichiers JSON exportés via processus d’exportation continue. Utilisez __MAJ + ENTRÉE__ pour exécuter cette cellule.

7. Dans la cellule suivante, entrez et exécutez la commande ci-dessous pour afficher le schéma Spark créé pour les fichiers JSON :

        jsonData.printSchema
    
    Le schéma sera différent pour chaque type de données de télémétrie. Voici le schéma généré pour les requêtes web (données stockées dans le sous-répertoire `Requests`) :

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Utilisez les informations suivantes pour enregistrer le tableau de données comme tableau temporaire, puis exécutez une requête sur les données :

        jsonData.registerTempTable("requests")
        var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()

    Cette requête renvoie les informations deville pour les 20 premiers enregistrements pour lesquelles la valeur context.location.city n’est pas nulle.

    > [AZURE.NOTE] La structure de contexte est présente dans toutes les données de télémétrie consignées par Application Insights ; toutefois, l’élément de ville ne peut pas renseigné dans vos journaux. Utilisez le schéma pour identifier d’autres éléments que vous pouvez interroger et qui peuvent contenir des données pour vos journaux.

    Cette requête retourne des informations similaires à ce qui suit :

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## Étapes suivantes

Pour d’autres d’exemples d’utilisation de Spark avec des données et des services dans Azure, consultez les documents suivants :

* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

Pour plus d’informations sur la création et l’exécution d’applications Spark, consultez les documents suivants :

* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécution de travaux à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

<!---HONumber=AcomDC_0914_2016-->