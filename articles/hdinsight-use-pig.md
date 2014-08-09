<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Pig with HDInsight" pageTitle="Use Pig with HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Pig with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Utilisation de Pig avec HDInsight
=================================

[Apache *Pig*](http://pig.apache.org/) contient un langage de script pour exécuter les tâches *MapReduce*, ce qui permet d'éviter d'écrire du code Java. Dans ce didacticiel, vous allez utiliser PowerShell pour exécuter des instructions Pig Latin pour analyser un fichier journal Apache log4j et exécuter des requêtes sur les données pour générer un résultat. Ce didacticiel présente les avantages de Pig et comment ce langage peut être utilisé pour simplifier les tâches MapReduce.

Le langage de script Pig est appelé *Pig Latin*. Les instructions Pig Latin suivent en général ce modèle :

-   **Chargement** : lecture des données à manipuler dans le système de fichiers
-   **Transformation** : manipulation des données
-   **Sortie ou stockage** : affichage du résultat à l'écran ou stockage pour traitement

Pour plus d'informations sur Pig Latin, consultez les pages [Manuel de référence Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) et [Manuel de référence Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

**Configuration requise**

Notez la configuration requise avant de commencer la lecture de cet article :

-   Un cluster Azure HDInsight. Pour obtenir des instructions, consultez le didacticiel [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) ou la rubrique [Mise en service de clusters HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).
-   Installez et configurez Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).

**Durée de réalisation estimée :** 30 minutes

Dans cet article
----------------

-   [Pig : cas d'emploi](#usage)
-   [Téléchargement des fichiers de données vers le stockage d'objets blob Azure](#uploaddata)
-   [Présentation de Pig Latin](#understand)
-   [Exécution de Pig Latin avec PowerShell](#powershell)
-   [Étapes suivantes](#nextsteps)

Pig : cas d'emploi
------------------

Les bases de données sont parfaites pour les petits jeux de données et les requêtes à faible latence. Mais lorsqu'il s'agit de gérer de gros volumes de données et des jeux de données qui se comptent en téraoctets, les bases de données SQL ne sont plus une bonne solution. Au fur et à mesure de l'augmentation de la charge de la base de données et de la dégradation des performances, les administrateurs ont généralement recours à l'achat de matériel plus performant.

En général, toutes les applications enregistrent les erreurs, exceptions et autres problèmes dans un fichier journal pour que l'administrateur puisse consulter les problèmes ou bien générer des mesures à partir des données du fichier journal. Ces fichiers journaux atteignent généralement une taille assez importante et contiennent énormément de données qui doivent être traitées et exploitées.

Les fichiers journaux sont un bon exemple de données volumineuses. L'utilisation de données volumineuses est difficile dans les bases de données relationnelles et les packages de statistiques et de visualisation. En raison des grandes quantités de données et des calculs effectués sur ces données, des logiciels parallèles exécutés sur des dizaines, des centaines, voire des milliers de serveurs sont souvent nécessaires pour traiter ces données dans un délai raisonnable. Hadoop contient une infrastructure MapReduce pour la création d'applications qui traitent en parallèle de grands volumes de données, structurées ou non, sur des clusters d'ordinateurs de façon extrêmement fiable et robuste.

L'utilisation de Pig réduit le temps nécessaire à la conception de programmes de mappage et de réduction. Ce qui signifie qu'aucun code Java n'est nécessaire, pas plus que le code réutilisable. Vous avez également la possibilité de combiner du code Java avec Pig. De nombreux algorithmes complexes peuvent être écrits en moins de cinq lignes de code Pig compréhensible.

La représentation visuelle de ce que vous allez accomplir dans cet article se trouve dans les deux figures qui suivent. Celles-ci présentent un exemple de données qui illustre le flux et la transformation des données au fur et à mesure des lignes de code Pig du script. La première figure présente un extrait du fichier journal log4j :

![exemple de fichier complet](./media/hdinsight-use-pig/HDI.wholesamplefile.png)

La deuxième figure présente la transformation des données :

![HDI.PIG.Data.Transformation](./media/hdinsight-use-pig/HDI.DataTransformation.gif)

Téléchargement d'un fichier de données vers le stockage d'objets blob Azure
---------------------------------------------------------------------------

HDInsight utilise le conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/). Dans cet article, vous allez utiliser un exemple de fichier log4j distribué avec le cluster HDInsight qui est stocké dans *\\example\\data\\sample.log*. Pour plus d'informations sur le téléchargement de données, consultez la rubrique [Téléchargement de données vers HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/).

Pour accéder aux fichiers, utilisez la syntaxe suivante :

     wasb[s]://[<containerName>@<storageAccountName>.blob.core.windows.net]/<path>/<filename>

Par exemple :

     wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Remplacez *mycontainer* par le nom du conteneur et *mystorage* par le nom du compte de stockage d'objets blob.

Comme le fichier est stocké dans le système de fichiers par défaut, vous pouvez également y accéder avec la syntaxe suivante :

     wasb:///example/data/sample.log
        /example/data/sample.log

Présentation de Pig Latin
-------------------------

Dans cette session, vous allez aborder quelques instructions Pig Latin, ainsi que les résultats générés après leur exécution. Dans la session qui suit, vous allez lancer PowerShell pour exécuter les instructions Pig.

1.  Chargez les données depuis le système de fichiers, puis affichez les résultats.

         LOGS = LOAD 'wasb:///example/data/sample.log';
         DUMP LOGS;

    Le résultat ressemble à ce qui suit :

         (2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
         (2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
         (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
         (2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
         (2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
         (2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
         (2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
         (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
         ...

2.  Lisez chaque ligne du fichier de données pour rechercher une correspondance dans les 6 niveaux du journal :

         LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  Excluez les lignes qui ne contiennent pas de correspondance. Par exemple, les lignes vides.

         FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
         DUMP FILTEREDLEVELS;

    Le résultat ressemble à ce qui suit :

         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         ...

4.  Regroupez tous les niveaux de journal dans leur propre ligne :

         GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
         DUMP GROUPEDLEVELS;

    Le résultat ressemble à ce qui suit :

         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE), ...

5.  Pour chaque groupe, comptez le nombre d'occurrences des niveaux de journal. Voici les fréquences de chaque niveau de journal :

         FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
         DUMP FREQUENCIES;

    Le résultat ressemble à ce qui suit :

         (INFO,3355)
         (WARN,361)
         (DEBUG,15608)
         (ERROR,181)
         (FATAL,37)
         (TRACE,29950)

6.  Classez les fréquences par ordre décroissant :

         RESULT = order FREQUENCIES by COUNT desc;
         DUMP RESULT;   

    Le résultat ressemble à ce qui suit :

        (TRACE,29950)
        (DEBUG,15608)
        (INFO,3355)
        (WARN,361)
	    (ERROR,181)
		(FATAL,37)

Exécution de Pig Latin avec PowerShell
--------------------------------------

Cette section contient des instructions sur l'utilisation des cmdlets PowerShell. Avant de parcourir cette section, vous devez configurer l'environnement local et la connexion à Azure. Pour plus de détails, consultez le didacticiel [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) et la rubrique [Administration de HDInsight avec PowerShell](/en-use/manage/services/hdinsight/administer-hdinsight-using-powershell/).

**Exécution de Pig Latin avec PowerShell**

1.  Ouvrez une fenêtre de console Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).
2.  Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

    Vous êtes invité à entrer les informations d'identification de votre compte Azure.

3.  Définissez la variable dans le script suivant et exécutez-le :

        $clusterName = "<HDInsightClusterName>" 

4.  Exécutez les commandes suivantes pour définir la chaîne de requête Pig Latin :

         # Créer la définition de la tâche Pig
         $0 = '$0';
         $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                         "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                         "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                         "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                         "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                         "RESULT = order FREQUENCIES by COUNT desc;" +
                         "DUMP RESULT;" 
            
         $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString 

    Vous pouvez également utiliser le commutateur -File pour spécifier un fichier de script Pig sur HDFS.

5.  Exécutez le script suivant pour envoyer la tâche Pig :

         # Envoyer la tâche Pig
         Select-AzureSubscription $subscriptionName
         $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition 

6.  Exécutez le script suivant pour attendre que la tâche Pig soit terminée :

         # Attendre la fin de la tâche Hive
         Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

7.  Exécutez le script suivant pour imprimer le résultat de la tâche Pig :

         # Imprimer l'erreur standard et la sortie standard de la tâche Pig.
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    ![HDI.Pig.PowerShell](./media/hdinsight-use-pig/hdi.pig.powershell.png)

    La tâche Pig calcule la fréquence des différents types de journaux.

Étapes suivantes
----------------

Alors que Pig vous permet d'analyser les données, d'autres langages fournis avec HDInsight sont susceptibles de vous intéresser. Hive contient un langage de requête similaire à SQL qui permet d'effectuer des requêtes de façon simple sur les données stockées dans HDInsight. Les tâches MapReduce écrites en Java permettent quant à elles d'effectuer des opérations complexes d'analyse des données. Pour plus d'informations, consultez les liens suivants :

-   [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Téléchargement de données vers HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [Utilisation de Hive avec HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)

