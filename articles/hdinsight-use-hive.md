<properties linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Use Hadoop Hive in HDInsight | Azure" metaKeywords="" description="Learn how to use Hive with HDInsight. You'll use a log file as input into an HDInsight table, and use HiveQL to query the data and report basic statistics." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Hive in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Utilisation de Hive avec Hadoop dans HDInsight

[Apache Hive][Apache Hive] permet d'exécuter une tâche MapReduce par l'intermédiaire d'un langage de script similaire à SQL, appelé *HiveQL*, qui peut être appliqué pour synthétiser, interroger et analyser de gros volumes de données. Dans cet article, vous allez utiliser HiveQL pour interroger les données dans un fichier journal Apache log4j et générer des rapports sur les statistiques de base.

**Configuration requise :**

-   Vous devez avoir configuré un **cluster HDInsight**. Pour une description de la procédure à suivre avec le portail Azure, consultez la page [Prise en main de HDInsight][Prise en main de HDInsight]. Pour des instructions sur les autres méthodes disponibles pour créer ce type de clusters, consultez la page [Configuration de clusters HDInsight][Configuration de clusters HDInsight].

-   Vous devez avoir installé **Azure PowerShell** sur votre station de travail. Pour des instructions sur la marche à suivre, consultez la page [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

**Durée de réalisation estimée :** 30 minutes

## Dans cet article

-   [Hive : cas d'emploi][Hive : cas d'emploi]
-   [Téléchargement des fichiers de données vers le stockage d'objets blob Azure][Téléchargement des fichiers de données vers le stockage d'objets blob Azure]
-   [Exécution de requêtes Hive avec PowerShell][Exécution de requêtes Hive avec PowerShell]
-   [Étapes suivantes][Étapes suivantes]

## <span id="usage"></span></a>Hive : cas d'emploi

Les bases de données sont une solution appropriée lorsque vous gérez de petits ensembles de données pour lesquels des requêtes à faible latence sont possibles. Mais quand il s'agit d'ensembles de données volumineux contenant des téraoctets de données, les bases de données SQL classiques, souvent, ne sont pas une solution idéale. Jusqu'à présent, les administrateurs de base de données procédaient généralement à un changement d'échelle pour gérer ces ensembles de données plus volumineux, en achetant des équipements plus performants au fur et à mesure que la charge de la base de données augmentait et que les performances se dégradaient.

Hive résout les problèmes associés aux données volumineuses en autorisant les utilisateurs à procéder à une montée en charge lorsqu'ils interrogent de vastes ensembles de données. Hive interroge les données parallèlement dans plusieurs nœuds avec MapReduce, distribuant la base de données sur un nombre croissant d'hôtes au fur et à mesure que la charge augmente.

Hive et HiveQL offrent également une alternative à l'écriture de tâches MapReduce en Java lors de l'interrogation des données. Ils fournissent un wrapper simple, similaire à un wrapper SQL, qui permet d'écrire les requêtes en HiveQL, lesquelles sont ensuite compilées pour MapReduce par HDInsight et exécutées sur le cluster.

Hive permet également aux programmeurs familiers de l'infrastructure MapReduce de raccorder des mappeurs et des réducteurs personnalisés pour effectuer une analyse plus complexe qui n'est peut-être pas prise en charge par les fonctionnalités intégrées du langage HiveQL.

Hive convient le mieux pour le traitement par lots de grosses quantités de données immuables (comme les journaux Web). Il ne convient pas pour les applications de transactions qui ont besoin de temps de réponse rapides, notamment les systèmes de gestion de base de données. Hive est optimisé pour l'évolutivité (il est possible d'ajouter davantage de machines dynamiquement au cluster Hadoop), l'extensibilité (dans l'infrastructure MapReduce et avec d'autres interfaces de programmation) et la tolérance aux pannes. La latence ne fait pas partie des éléments clés considérés dans la conception.

En général, les applications enregistrent les erreurs, les exceptions et les autres problèmes codés dans un fichier journal. Les administrateurs peuvent ainsi utiliser les données des fichiers journaux pour examiner les problèmes qui sont susceptibles de survenir et générer des mesures concernant les erreurs ou d'autres questions telles que les performances. Ces fichiers journaux atteignent généralement une taille assez importante et contiennent énormément de données qui doivent être traitées et exploitées pour l'intelligence sur l'application.

Les fichiers journaux sont donc un exemple paradigmatique de données volumineuses. HDInsight fournit un système d'entrepôt de données Hive qui facilite la synthèse de données, les requêtes ad-hoc et l'analyse de ces ensembles de données volumineux stockés dans des systèmes de fichiers compatibles avec Hadoop, tels que le stockage d'objets blob Azure.

## <span id="uploaddata"></span></a>Téléchargement des fichiers de données vers le stockage d'objets blob

HDInsight utilise le conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

Dans cet article, vous allez utiliser un exemple de fichier log4j distribué avec le cluster HDInsight qui est stocké dans *\\example\\data\\sample.log*. Chaque journal à l'intérieur du fichier est constitué d'une ligne de champs qui contient un champ `[LOG LEVEL]` pour indiquer le type et la gravité. Par exemple :

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

Pour accéder aux fichiers, utilisez la syntaxe suivante :

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Par exemple :

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Remplacez *mycontainer* par le nom du conteneur et *mystorage* par le nom du compte de stockage d'objets blob.

Comme le fichier est stocké dans le système de fichiers par défaut, vous pouvez également y accéder avec la syntaxe suivante :

    wasb:///example/data/sample.log
    /example/data/sample.log

Pour générer vos propres fichiers log4j, utilisez l'utilitaire de journalisation [Apache Log4j][Apache Log4j]. Pour des informations sur le téléchargement des données vers le stockage d'objets blob Azure, consultez la page [Téléchargement de données vers HDInsight][Téléchargement de données vers HDInsight].

## <span id="runhivequeries"></span></a> Exécution de requêtes Hive avec PowerShell

Dans la dernière section, vous avez téléchargé un fichier log4j nommé sample.log vers le conteneur du système de fichiers par défaut. Dans cette section, vous allez exécuter HiveQL pour créer une table hive, charger des données dans la table hive, puis interroger les données pour savoir combien il y a eu de journaux d'erreurs.

Cet article fournit les instructions pour exécuter une requête Hive en utilisant les cmdlets d'Azure PowerShell. Avant de parcourir cette section, vous devez configurer l'environnement local et la connexion à Azure, comme décrit dans la section **Configuration requise** au début de cette rubrique.

Les requêtes Hive peuvent être exécutées dans PowerShell avec la cmdlet **Start-AzureHDInsightJob** ou **Invoke-Hive**.

**Pour exécuter les requêtes Hive avec Start-AzureHDInsightJob**

1.  Ouvrez une fenêtre de console Azure PowerShell. Pour les instructions, consultez la page [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].
2.  Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

    Vous êtes invité à entrer les informations d'identification de votre compte Azure.

3.  Définissez les variables dans le script suivant et exécutez ce dernier :

        # Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Exécutez le script suivant pour définir les requêtes HiveQL :

        # HiveQL queries
        # Use the internal table option. 
        $queryString = "DROP TABLE log4jLogs;" +
                       "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
                       "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
                       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

        # Use the external table option. 
        $queryString = "DROP TABLE log4jLogs;" +
                        "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
                        "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

    La commande HiveQL LOAD DATA provoque le transfert du fichier de données dans le dossier \\hive\\warehouse\\. La commande DROP TABLE supprime la table et le fichier de données. Si vous utilisez l'option de table interne et que vous souhaitez réexécuter le script, vous devez télécharger à nouveau le fichier sample.log. Si vous voulez conserver le fichier de données, vous devez utiliser la commande CREATE EXTERNAL TABLE, comme indiqué dans le script.

    Vous pouvez également utiliser la table externe si le fichier de données se trouve dans un conteneur ou un compte de stockage différent.

    Utilisez la commande DROP TABLE en premier si vous réexécutez le script et que la table log4jlogs existe déjà.

5.  Exécutez le script suivant pour créer une définition de tâche Hive :

        # Create a Hive job definition 
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

    Vous pouvez également utiliser le commutateur -File pour spécifier un fichier de script HiveQL sur HDFS.

6.  Exécutez le script suivant pour envoyer la tâche Hive :

        # Submit the job to the cluster 
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7.  Exécutez le script suivant pour attendre que la tâche Hive soit terminée :

        # Wait for the Hive job to complete
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8.  Exécutez le script suivant pour imprimer la sortie standard :
9.  # Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    Voici le résultat :

        [ERROR] 3

**Pour envoyer des requêtes Hive avec Invoke-Hive**

1.  Ouvrez une fenêtre de console Azure PowerShell.
2.  Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

    Vous êtes invité à entrer les informations d'identification de votre compte Azure.

3.  Définissez la variable, puis exécutez-la :

        $clusterName = "<HDInsightClusterName>"

4.  Exécutez le script suivant pour appeler des requêtes HiveQL :

        Use-AzureHDInsightCluster $clusterName 
        $response = Invoke-Hive -Query @"
            SELECT * FROM hivesampletable
                WHERE devicemake LIKE "HTC%"
                LIMIT 10; 
        "@

        Write-Host $response

    La sortie est la suivante :

    ![Sortie de la cmdlet Invoke-Hive de PowerShell][Sortie de la cmdlet Invoke-Hive de PowerShell]

    Pour les requêtes HiveQL plus longues, il est recommandé d'utiliser le fichier de script Here-Strings ou HiveQL de PowerShell. Les exemples suivants montrent comment utiliser la cmdlet Invoke-Hive pour exécuter un fichier de script HiveQL. Ce dernier doit être téléchargé vers WASB.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Pour plus d'informations sur Here-Strings, consultez la page [Utilisation du fichier de script Here-Strings de PowerShell][Utilisation du fichier de script Here-Strings de PowerShell].

## <span id="nextsteps"></span></a>Étapes suivantes

Bien que Hive facilite l'interrogation des données avec un langage de requête similaire à SQL, d'autres composants disponibles avec HDInsight fournissent des fonctionnalités supplémentaires telles que le transfert et la transformation des données. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main d'Azure HDInsight][Prise en main de HDInsight]
-   [Analyse des données sur les retards de vol avec HDInsight][Analyse des données sur les retards de vol avec HDInsight]
-   [Utilisation d'Oozie avec HDInsight][Utilisation d'Oozie avec HDInsight]
-   [Envoi de tâches Hadoop par programme][Envoi de tâches Hadoop par programme]
-   [Téléchargement de données vers HDInsight][Téléchargement de données vers HDInsight]
-   [Utilisation de Pig avec HDInsight][Utilisation de Pig avec HDInsight]
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][Documentation du Kit de développement logiciel (SDK) Azure HDInsight]

  [Apache Hive]: http://hive.apache.org/
  [Prise en main de HDInsight]: ../hdinsight-get-started/
  [Configuration de clusters HDInsight]: ../hdinsight-provision-clusters/
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [Hive : cas d'emploi]: #usage
  [Téléchargement des fichiers de données vers le stockage d'objets blob Azure]: #uploaddata
  [Exécution de requêtes Hive avec PowerShell]: #runhivequeries
  [Étapes suivantes]: #nextsteps
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [Téléchargement de données vers HDInsight]: ../hdinsight-upload-data/
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [Sortie de la cmdlet Invoke-Hive de PowerShell]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Utilisation du fichier de script Here-Strings de PowerShell]: http://technet.microsoft.com/fr-fr/library/ee692792.aspx
  [Analyse des données sur les retards de vol avec HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Utilisation d'Oozie avec HDInsight]: ../hdinsight-use-oozie/
  [Envoi de tâches Hadoop par programme]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Utilisation de Pig avec HDInsight]: ../hdinsight-use-pig/
