<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Utilisation de Hadoop Hive dans HDInsight | Azure" metaKeywords="" description="Apprenez &agrave; utiliser Hive avec HDInsight. Vous allez utiliser un fichier journal comme entr&eacute;e dans une table HDInsight et vous servir de HiveQL pour interroger les donn&eacute;es et g&eacute;n&eacute;rer des rapports sur les statistiques de base." metaCanonical="" services="hdinsight" documentationCenter="" title="Utilisation de Hadoop Hive dans HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Utilisation de Hive avec Hadoop dans HDInsight

[Apache Hive][Apache Hive] permet d'exécuter une tâche MapReduce via un langage de création de scripts semblable à SQL, nommé *HiveQL*. Hive est un système d'entrepôt de données pour Hadoop qui facilite la synthèse de données, les requêtes et l'analyse d'ensembles de données volumineux. Dans cet article, vous allez utiliser HiveQL pour interroger un exemple de fichier de données fourni dans le cadre de l'approvisionnement du cluster HDInsight.

**Configuration requise :**

-   Vous devez avoir configuré un **cluster HDInsight**. Pour une description de la procédure à suivre avec le portail Azure, consultez la page [Prise en main de HDInsight][Prise en main de HDInsight]. Pour des instructions sur les autres méthodes disponibles pour créer ce type de clusters, consultez la page [Configuration de clusters HDInsight][Configuration de clusters HDInsight].

-   Vous devez avoir installé **Azure PowerShell** sur votre station de travail. Pour des instructions sur la marche à suivre, consultez la page [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

**Durée de réalisation estimée :** 30 minutes

## Dans cet article

-   [Hive : cas d'emploi][Hive : cas d'emploi]
-   [Téléchargement de données pour des tables Hive][Téléchargement de données pour des tables Hive]
-   [Exécution de requêtes Hive avec PowerShell][Exécution de requêtes Hive avec PowerShell]
-   [Utilisation de Tez pour des performances améliorées][Utilisation de Tez pour des performances améliorées]
-   [Étapes suivantes][Étapes suivantes]

## <span id="usage"></span></a>Hive : cas d'emploi

![HDI.HIVE.Architecture][HDI.HIVE.Architecture]

Hive projette une sorte de structure sur un ensemble de données non structurées (tout ce dont il est question avec Hadoop), puis vous laisse interroger ces données avec HiveQL. Hive fournit une couche d'abstraction au-dessus de l'infrastructure MapReduce Java, ce qui permet aux utilisateurs d'interroger les données sans connaissance de Java ou MapReduce. HiveQL fournit un wrapper simple, similaire à un wrapper SQL, qui permet d'écrire les requêtes en HiveQL, lesquelles sont ensuite compilées pour MapReduce par HDInsight et exécutées sur le cluster. Hive présente d'autres avantages :

-   Hive permet aux programmeurs familiers de l'infrastructure MapReduce de raccorder des mappeurs et des réducteurs personnalisés pour effectuer une analyse plus complexe qui n'est peut-être pas prise en charge par les fonctionnalités intégrées du langage HiveQL.
-   Hive convient le mieux pour le traitement par lots de grosses quantités de données immuables (comme les journaux Web). Il ne convient pas pour les applications de transactions qui ont besoin de temps de réponse rapides, notamment les systèmes de gestion de base de données.
-   Hive est optimisé pour l'évolutivité (il est possible d'ajouter davantage de machines dynamiquement au cluster Hadoop), l'extensibilité (dans l'infrastructure MapReduce et avec d'autres interfaces de programmation) et la tolérance aux pannes. La latence ne fait pas partie des éléments clés considérés dans la conception.

## <span id="uploaddata"></span></a>Téléchargement de données pour des tables Hive

HDInsight utilise le conteneur de stockage d'objets blob Azure comme système de fichiers par défaut pour les clusters Hadoop. Quelques exemples de fichiers de données sont ajoutés au stockage d'objets blob dans le cadre de l'approvisionnement du cluster. Vous pouvez vous en servir pour exécuter des requêtes Hive sur le cluster. Vous pouvez également, si vous le souhaitez, télécharger votre propre fichier de données vers le compte de stockage d'objets blob associé au cluster. Pour des instructions, consultez la page [Téléchargement de données vers HDInsight][Téléchargement de données vers HDInsight]. Pour plus d'informations sur l'utilisation du stockage d'objets blob Azure avec HDInsight, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

Cet article utilise un exemple de fichier *log4j* distribué avec le cluster HDInsight et stocké dans *\\example\\data\\sample.log*, dans votre conteneur de stockage d'objets blob. Vous pouvez également générer vos propres fichiers log4j à l'aide de l'utilitaire de journalisation [Apache Log4j][Apache Log4j], puis les télécharger vers le conteneur d'objets blob. Chaque journal à l'intérieur du fichier est constitué d'une ligne de champs qui contient un champ `[LOG LEVEL]` pour indiquer le type et la gravité. Par exemple :

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

Dans l'exemple ci-dessus, le niveau de journal est ERROR.

Vous pouvez accéder aux fichiers en utilisant la syntaxe suivante dans votre application :

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Pour accéder au fichier sample.log, vous devez donc utiliser la syntaxe suivante :

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Remplacez *mycontainer* par le nom du conteneur d'objets blob Azure et *mystorage* par le nom du compte de stockage Azure.

Comme le fichier est stocké dans le système de fichiers par défaut, vous pouvez également y accéder avec la syntaxe suivante :

    wasb:///example/data/sample.log
    /example/data/sample.log

## <span id="runhivequeries"></span></a> Exécution de requêtes Hive avec PowerShell

Dans la dernière section, vous avez identifié un fichier *sample.log* dont vous allez vous servir pour exécuter des requêtes Hive. Dans cette section, vous allez exécuter HiveQL pour créer une table hive, charger l'exemple de données dans la table hive, puis interroger les données pour savoir combien de journaux d'erreurs sont présents dans le fichier.

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

4.  Exécutez le script suivant pour définir les requêtes HiveQL. Vous pouvez choisir de créer une table Hive *interne* ou *externe*.

    -   Avec les tables internes, l'exemple de données utilisé est transféré de son emplacement existant vers le dossier \\hive\\warehouse\\\<*nom\_table\>*. Ainsi, lorsque vous supprimez une table interne, les données associées sont également supprimées. Si vous utilisez les tables internes et que vous souhaitez réexécuter le script, vous devez télécharger à nouveau le fichier *sample.log* dans le stockage d'objets blob.
    -   Avec les tables externes, les données ne sont pas transférées à partir de leur emplacement d'origine. Vous pouvez également utiliser la table externe pour les cas où le fichier de données se trouve dans un conteneur ou un compte de stockage différent.

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

    La commande DROP TABLE supprime la table et le fichier de données, si la table existe déjà. La commande HiveQL LOAD DATA déplace le fichier de données de \\example\\data vers le dossier \\hive\\warehouse\\\<*nom\_table\>*.

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

        # Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    Voici le résultat :

        [ERROR] 3

    qui signifie que le fichier *sample.log* contenait trois instances de journaux ERROR.

Si besoin est, vous pouvez également importer le résultat de vos requêtes dans Microsoft Excel pour une analyse plus approfondie. Pour des instructions, consultez la page [Connexion d'Excel à Hadoop à l'aide de Power Query][Connexion d'Excel à Hadoop à l'aide de Power Query].

**Pour envoyer des requêtes Hive avec Invoke-Hive**

1.  Ouvrez une fenêtre de console Azure PowerShell.
2.  Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

    Vous êtes invité à entrer les informations d'identification de votre compte Azure.

3.  Définissez les variables dans le script suivant et exécutez ce dernier :

        # Provide Azure subscription name, Azure Storage account, and container.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Connectez-vous au cluster HDInsight.

        # Connect to the cluster
        Use-AzureHDInsightCluster $clusterName

5.  Exécutez le script suivant pour appeler des requêtes HiveQL :

        # Run the query to create an internal Hive table, load sample data
        $response = Invoke-Hive -Query @"
        >> DROP TABLE log4jLogs;
        >> CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW   
            FORMAT DELIMITED FIELDS TERMINATED BY ' ';
        >> LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/
            sample.log' OVERWRITE INTO TABLE log4jLogs;
        >> SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4; 
        >> "@

6.  Imprimez la sortie sur la console.

        # print the output on the console
        Write-Host $response

    La sortie a l'aspect suivant :

    ![Sortie de la cmdlet Invoke-Hive de PowerShell][Sortie de la cmdlet Invoke-Hive de PowerShell]

    Pour les requêtes HiveQL plus longues, vous pouvez utiliser les fichiers de script Here-Strings ou HiveQL de PowerShell. L'extrait suivant montre comment utiliser la cmdlet *Invoke-Hive* pour exécuter un fichier de script HiveQL. Ce dernier doit être téléchargé vers WASB.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Pour plus d'informations sur Here-Strings, consultez la page [Utilisation du fichier de script Here-Strings de PowerShell][Utilisation du fichier de script Here-Strings de PowerShell].

Si besoin est, vous pouvez également importer le résultat de vos requêtes dans Microsoft Excel pour une analyse plus approfondie. Pour des instructions, consultez la page [Connexion d'Excel à Hadoop à l'aide de Power Query][Connexion d'Excel à Hadoop à l'aide de Power Query].

## <span id="usetez"></span></a>Utilisation de Tez pour des performances améliorées

[Apache Tez][Apache Tez] est une infrastructure qui permet une exécution à l'échelle beaucoup plus efficace pour les applications qui manipulent de grandes quantités de données telles que Hive. Dans la dernière version de HDInsight, Hive prend en charge l'exécution sur Tez. Cette fonctionnalité est actuellement désactivée par défaut et doit être activée. Dans les prochaines versions du cluster, elle sera activée par défaut. Pour pouvoir tirer parti de Tez, vous devez définir la valeur suivante pour une requête Hive :

        set hive.execution.engine=tez;
        

Vous pouvez le faire individuellement pour chaque requête en plaçant cette instruction au début de la requête. Il est également possible d'activer cette fonctionnalité par défaut sur un cluster en définissant la valeur de configuration à la création du cluster. Pour plus d'informations, consultez la page [Approvisionnement des clusters HDInsight][Configuration de clusters HDInsight].

Les [documents de conception Hive sur Tez][documents de conception Hive sur Tez] contiennent des informations détaillées sur les options d'implémentation et les configurations du réglage.

## <span id="nextsteps"></span></a>Étapes suivantes

Bien que Hive facilite l'interrogation des données avec un langage de requête similaire à SQL, d'autres composants disponibles avec HDInsight fournissent des fonctionnalités supplémentaires telles que le transfert et la transformation des données. Pour en savoir plus, consultez les articles suivants :

-   [Utilisation d'Oozie avec HDInsight][Utilisation d'Oozie avec HDInsight]
-   [Envoi de tâches Hadoop par programme][Envoi de tâches Hadoop par programme]
-   [Utilisation de Pig avec HDInsight][Utilisation de Pig avec HDInsight]
-   [Analyse des données sur les retards de vol avec HDInsight][Analyse des données sur les retards de vol avec HDInsight]
-   [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][Documentation du Kit de développement logiciel (SDK) Azure HDInsight]
-   [Téléchargement de données vers HDInsight][Téléchargement de données vers HDInsight]
-   [Prise en main d'Azure HDInsight][Prise en main de HDInsight]

  [Apache Hive]: http://hive.apache.org/
  [Prise en main de HDInsight]: ../hdinsight-get-started/
  [Configuration de clusters HDInsight]: ../hdinsight-provision-clusters/
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [Hive : cas d'emploi]: #usage
  [Téléchargement de données pour des tables Hive]: #uploaddata
  [Exécution de requêtes Hive avec PowerShell]: #runhivequeries
  [Utilisation de Tez pour des performances améliorées]: #usetez
  [Étapes suivantes]: #nextsteps
  [HDI.HIVE.Architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
  [Téléchargement de données vers HDInsight]: ../hdinsight-upload-data/
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [Connexion d'Excel à Hadoop à l'aide de Power Query]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-connect-excel-power-query/
  [Sortie de la cmdlet Invoke-Hive de PowerShell]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Utilisation du fichier de script Here-Strings de PowerShell]: http://technet.microsoft.com/fr-fr/library/ee692792.aspx
  [Apache Tez]: http://tez.apache.org
  [documents de conception Hive sur Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Utilisation d'Oozie avec HDInsight]: ../hdinsight-use-oozie/
  [Envoi de tâches Hadoop par programme]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Utilisation de Pig avec HDInsight]: ../hdinsight-use-pig/
  [Analyse des données sur les retards de vol avec HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Documentation du Kit de développement logiciel (SDK) Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/fr-fr/library/dn479185.aspx
