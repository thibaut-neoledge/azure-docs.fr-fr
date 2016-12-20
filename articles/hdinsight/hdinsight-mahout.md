---
title: "Génération de recommandations à l’aide de Mahout et de HDInsight basé sur Windows | Microsoft Docs"
description: "Apprenez à utiliser la bibliothèque à apprentissage automatique Apache Mahout pour générer des recommandations de films avec HDInsight basé sur Windows (Hadoop)."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cc59d7785975e3f9acd574b516d20cd782c22dac
ms.openlocfilehash: 3653ce2f2a251d3163fa843ef7126aab295c2a14


---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>Génération de recommandations de films à l’aide d’Apache Mahout avec Hadoop dans HDInsight
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Apprenez à utiliser la bibliothèque à apprentissage automatique [Apache Mahout](http://mahout.apache.org) avec Azure HDInsight pour générer des recommandations de films

> [!NOTE]
> Les étapes décrites dans ce document nécessitent un client Windows et un cluster HDInsight Windows. Pour plus d'informations sur l'utilisation de Mahout avec un client Unix, Linux ou OS X ou avec un cluster HDInsight Linux, consultez [Générer des recommandations de film à l'aide d'Apache Mahout avec un Hadoop Linux dans HDInsight](hdinsight-hadoop-mahout-linux-mac.md)
>
>

## <a name="a-namelearnawhat-you-will-learn"></a><a name="learn"></a>Contenu
Mahout est une bibliothèque à [apprentissage automatique][ml] pour Apache Hadoop. Mahout contient des algorithmes pour le traitement des données, tel que le filtrage, la classification et le clustering. Dans cet article, vous utiliserez un moteur de recommandation pour générer des recommandations en fonction des films vus par vos amis. Vous apprendrez également à effectuer des classifications avec une forêt d’arbres décisionnels. Vous aborderez les éléments ci-après :

* Exécution de tâches de Mahout à l’aide de Windows PowerShell
* Exécution de tâches Mahout depuis la ligne de commande Hadoop
* Installation de Mahout sur les clusters HDInsight 3.0 et 2.0

  > [!NOTE]
  > Mahout est fourni avec la version HDInsight 3.1 des clusters. Si vous utilisez une version antérieure de HDInsight, consultez la section [Installation de Mahout](#install) avant de continuer.
  >
  >

## <a name="prerequisites"></a>configuration requise
* **Un cluster Hadoop Windows dans HDInsight**. Pour des informations sur la création de ce type de cluster, consultez [Prise en main de Hadoop dans HDInsight][getstarted]
* **Un poste de travail sur lequel est installé Azure PowerShell**.

    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="a-namerecommendationsagenerate-recommendations-by-using-windows-powershell"></a><a name="recommendations"></a>Génération de recommandations avec Windows PowerShell
> [!NOTE]
> Bien que la tâche utilisée dans cette section fonctionne avec Windows PowerShell, nombre des classes fournies avec Mahout ne fonctionnent pas actuellement avec Windows PowerShell et doivent être exécutées à partir de la ligne de commande Hadoop. Pour obtenir une liste des classes qui ne fonctionnent pas avec Windows PowerShell, consultez la section [Résolution des problèmes](#troubleshooting) .
>
> Pour un exemple de l’utilisation de la ligne de commande Hadoop pour exécuter des tâches Mahout, consultez la section [Classification de données avec la ligne de commande Hadoop](#classify).
>
>

L’une des fonctions fournies par Mahout est un moteur de recommandation. Ce moteur accepte les données au format `userID`, `itemId` et `prefValue` (les préférences utilisateur pour l’élément). Mahout peut alors effectuer une analyse des co-occurrences afin de déterminer que *les utilisateurs ayant une préférence pour un élément ont également une préférence pour ces autres éléments*. Mahout détermine ensuite des utilisateurs avec des préférences similaires, qui peuvent alors être utilisées pour faire des recommandations.

Voici un exemple très simple de cette méthode pour les films :

* **Co-occurrence** : Jean, Alice et Robert ont tous aimé *La Guerre des étoiles*, *L’Empire contre-attaque* et *Le Retour du Jedi*. Mahout détermine que les utilisateurs qui aiment l’un de ces films aiment également les deux autres.
* **Co-occurrence** : Robert et Alice ont aussi aimé *La Menace fantôme*, *L’attaque des clones* et *La revanche des Sith*. Mahout détermine que les utilisateurs qui ont aimé les trois films précédents aiment également ces trois films.
* **Recommandation par similarité**: Puisque Jean a aimé les trois premiers films, Mahout regarde les films que d’autres personnes ayant les mêmes goûts que lui ont aimés aussi, mais que Jean n’a pas encore vus (aimés/notés). Dans ce cas, Mahout recommande *La Menace fantôme*, *L’attaque des clones* et *La revanche des Sith*.

### <a name="understanding-the-data"></a>Vue d’ensemble des données
De façon pratique, [GroupLens Research][movielens] fournit des données d’évaluation de films dans un format compatible avec Mahout. Ces données sont disponibles sur le stockage par défaut de votre cluster à `/HdiSamples/MahoutMovieData`.

Il existe deux fichiers, `moviedb.txt` (informations sur les films,) et `user-ratings.txt`. Le fichier user-ratings.txt de l'utilisateur est utilisé pendant l'analyse, tandis que le fichier moviedb.txt est utilisé pour fournir des informations texte conviviales lors de l'affichage des résultats de l'analyse.

Les données contenues dans le fichier user-ratings.txt respectent la structure suivante : `userID`, `movieID`, `userRating` et `timestamp`, ce qui nous indique la note attribuée par chaque utilisateur à un film. Voici un exemple de ces données :

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Exécution de la tâche
Utilisez le script Windows PowerShell suivant pour exécuter une tâche à l’aide du moteur de recommandation Mahout sur les données de film :

    # The HDInsight cluster name.
    $clusterName = "the cluster name"

    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    # NOTE: The version number in the file path
    # may change in future versions of HDInsight.
    $jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
                    "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                    "--output", "wasbs:///example/out",
                    "--tempDir", "wasbs:///example/temp"

    # Create the job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context

    # Write out any error information
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [!NOTE]
> Les tâches Mahout ne suppriment pas les données temporaires créées lors du traitement de la tâche. C’est la raison pour laquelle le paramètre `--tempDir` est spécifié dans la tâche donnée en exemple : il isole les fichiers temporaires dans un répertoire spécifique.
>
>

La tâche Mahout ne retourne pas la sortie dans STDOUT. Au lieu de cela, elle est stockée dans le répertoire de sortie spécifié en tant que **part-r-00000**. Le script télécharge ce fichier dans **output.txt** , dans le répertoire actif sur votre station de travail.

Voici un exemple de contenu de ce fichier :

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La première colonne est `userID`. Les valeurs contenues entre  « [ » et « ] » sont `movieId`:`recommendationScore`.

### <a name="view-the-output"></a>Affichage du résultat
Bien que le résultat généré puisse être utilisé dans une application, il n’est pas très lisible. Le `moviedb.txt` du serveur peut être utilisé pour résoudre le `movieId` en nom de film, mais vous devez d’abord le télécharger ainsi que le fichier de notation à partir du serveur en utilisant le script suivant :

    # The HDInsight cluster name.
    $clusterName = "the cluster name"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

Une fois que vous avez téléchargé les fichiers, utilisez le script PowerShell suivant pour afficher des recommandations avec des noms de film :

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "user-ratings.txt"
            -movieFile "moviedb.txt"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

Voici un exemple d’exécution du script :

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

Le résultat doit ressembler à ce qui suit :

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

## <a name="a-nameclassifyaclassify-data-by-using-the-hadoop-command-line"></a><a name="classify"></a>Classification de données avec la ligne de commande Hadoop
L’une des méthodes de classification disponibles avec Mahout est la construction d’une [forêt aléatoire][forest]. Il s’agit d’un processus en plusieurs étapes qui implique l’utilisation de données de formation afin de générer des arbres de décision, qui sont ensuite utilisés pour classer des données. Il utilise la classe **org.apache.mahout.classifier.df.tools.Describe** fournie par Mahout. Il doit être actuellement exécuté à l’aide de la ligne de commande Hadoop.

### <a name="load-the-data"></a>Chargement des données
1. Téléchargez les fichiers ci-après dans le [jeu de fichiers NSL-KDD](http://nsl.cs.unb.ca/NSL-KDD/).

   * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): fichier de formation
   * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): données du test
2. Ouvrez chaque fichier et supprimez les lignes en haut commençant par '@',, puis enregistrez les fichiers. Si les lignes ne sont pas supprimées, vous obtenez des messages d’erreur lorsque vous utilisez ces données avec Mahout.
3. Téléchargez les fichiers vers **example/data**. Pour ce faire, vous pouvez utiliser le script suivant. Remplacez **CLUSTERNAME** par le nom du cluster HDInsight. Remplacez FILENAME par le nom du fichier à télécharger.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey

        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

### <a name="run-the-job"></a>Exécution de la tâche
1. Cette tâche requiert la ligne de commande Hadoop. Activez le Bureau à distance pour le cluster HDInsight, puis connectez-vous à lui en suivant les instructions fournies dans [Connexion à des clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. Une fois la connexion établie, utilisez l’icône de **ligne de commande Hadoop** pour ouvrir la ligne de commande Hadoop :

    ![hadoop cli][hadoopcli]
3. Utilisez la commande suivante pour générer le descripteur de fichier (**KDDTrain+.info**), qui utilise Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` décrit les attributs des données du fichier. Par exemple, L indique une étiquette.
4. Construisez une forêt d’arbres décisionnels à l’aide de la commande suivante :

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    Le résultat de cette opération est stocké dans le répertoire **nsl-forest**, situé dans le stockage de votre cluster HDInsight à l’adresse __wasbs://user/&lt;nom_utilisateur>/nsl-forest/nsl-forest.seq. &lt;nom_utilisateur> est le nom d’utilisateur utilisé pour votre session Bureau à distance. Ce fichier n’est pas lisible par les humains.
5. Testez la forêt en classant l’ensemble de données **KDDTest+.arff** . Utilisez la commande suivante :

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    Cette commande renvoie des informations récapitulatives sur un processus de classification du type suivant :

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

   Cette tâche produit également un fichier situé dans **wasbs:///example/data/predictions/KDDTest+.arff.out**. Toutefois, ce fichier n’est pas lisible par les humains.

> [!NOTE]
> Les tâches Mahout ne remplacent pas les fichiers. Si vous souhaitez réexécuter ces tâches, vous devez supprimer les fichiers créés par les tâches précédentes.
>
>

## <a name="a-nametroubleshootingatroubleshooting"></a><a name="troubleshooting"></a>Résolution des problèmes
### <a name="a-nameinstallainstall-mahout"></a><a name="install"></a>Installation de Mahout
Mahout est installé sur les clusters HDInsight 3.1 et peut être installé manuellement sur les clusters HDInsight 3.0 ou 2.1 en suivant les étapes ci-dessous.

1. La version de Mahout à utiliser dépend de la version de HDInsight de votre cluster. La version de cluster est affichée dans les propriétés du cluster dans le portail Azure.

   * **Pour HDInsight 2.1**, vous pouvez télécharger un fichier JAR (Java Archive) qui contient [Mahout 0,9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).
   * **Pour HDInsight 3.0**, vous devez [générer Mahout à partir de la source][build] et spécifier la version Hadoop fournie par HDInsight. Installez les éléments requis répertoriés sur la page de génération, téléchargez la source, puis utilisez la commande ci-après pour créer les fichiers jar de Mahout :

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        Une fois la génération terminée, le fichier JAR s’affiche sous **mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar**.

     > [!NOTE]
     > Une fois Mahout 1.0 disponible, vous devez être en mesure d’afficher les packages préconfigurés avec HDInsight 3.0.
     >
     >
2. Téléchargez le fichier jar vers **example/jars** dans le stockage par défaut de votre cluster. Dans le script suivant, remplacez CLUSTERNAME par le nom de votre cluster HDInsight et FILENAME par le chemin d'accès du fichier **mahout-coure-0.9-job.jar** .

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey

        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

### <a name="cannot-overwrite-files"></a>Remplacement de fichiers impossible
Les tâches Mahout ne suppriment pas les fichiers temporaires créés lors du traitement. En outre, les tâches ne remplaceront pas les fichiers de sortie existants.

Pour éviter les erreurs lors de l’exécution des tâches Mahout, supprimez les fichiers temporaires et de sortie entre les exécutions ou utilisez des noms de répertoire temporaire et de sortie uniques.

### <a name="cannot-find-the-jar-file"></a>Fichier JAR introuvable
Les clusters HDInsight 3.1 incluent Mahout. Le chemin d’accès et le nom de fichier comprennent le numéro de version de l’installation de Mahout sur le cluster. L'exemple de script Windows PowerShell dans ce didacticiel utilise un chemin d'accès valide à partir de novembre 2015, mais le numéro de version changera lors de mises à jour ultérieures de HDInsight. Pour déterminer le chemin d’accès actuel du fichier jar Mahout pour votre cluster, utilisez les commandes Windows PowerShell ci-après, puis modifiez le script pour référencer le chemin de fichier renvoyé :

    Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

### <a name="a-namenopowershellaclasses-that-do-not-work-with-windows-powershell"></a><a name="nopowershell"></a>Classes ne fonctionnant pas avec Windows PowerShell
Les tâches Mahout qui utilisent les classes suivantes renvoient divers messages d’erreur si elles sont utilisées à partir de Windows PowerShell :

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Pour exécuter des tâches utilisant ces classes, connectez-vous au cluster HDInsight et exécutez les tâches à partir de la ligne de commande Hadoop. Pour un exemple, consultez la section [Classification de données avec la ligne de commande Hadoop](#classify) .

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à utiliser Mahout, découvrez d’autres façons d’utiliser les données dans HDInsight :

* [Hive avec HDInsight](hdinsight-use-hive.md)
* [Pig avec HDInsight](hdinsight-use-pig.md)
* [MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools



<!--HONumber=Nov16_HO3-->


