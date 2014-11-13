<properties title="G&eacute;n&eacute;ration de recommandations de films en utilisant Mahout" pageTitle="G&eacute;n&eacute;ration de recommandations de films &agrave; l'aide de Mahout avec Microsoft Azure HDInsight (Hadoop)" description="Apprenez &agrave; utiliser la biblioth&egrave;que &agrave; apprentissage automatique Apache Mahout pour g&eacute;n&eacute;rer des recommandations de films avec HDInsight (Hadoop)" metaKeywords="Azure hdinsight mahout, Azure hdinsight machine learning, azure hadoop mahout, azure hadoop machine learning" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Génération de recommandations de films à l'aide d'Apache Mahout avec HDInsight (Hadoop)

Apprenez à utiliser la bibliothèque à apprentissage automatique [Apache Mahout][Apache Mahout] pour générer des recommandations de films avec Microsoft Azure HDInsight (Hadoop).

> [WACOM.NOTE] Vous devez avoir un cluster HDInsight pour utiliser les informations de cet article. Pour en créer un, consultez la page [Prise en main de Hadoop dans HDInsight][Prise en main de Hadoop dans HDInsight].
>
> Mahout est fourni avec des clusters HDInsight 3.1. Si vous utilisez une version antérieure de HDInsight, consultez la section [Installation de Mahout][Installation de Mahout] avant de continuer.

## <a name="learn"></a>Contenu

Mahout est une bibliothèque à [apprentissage automatique][apprentissage automatique] pour Apache Hadoop. Mahout contient des algorithmes pour le traitement des données, tel que le filtrage, la classification et le clustering. Dans cet article, vous utiliserez un moteur de recommandation pour générer des recommandations en fonction des films vus par vos amis. Vous apprendrez également à effectuer des classifications avec une forêt d'arbres décisionnels. Vous aborderez les éléments ci-après.

-   Exécution de tâches Mahout depuis PowerShell

-   Exécution de tâches Mahout depuis la ligne de commande Hadoop

-   Installation de Mahout sur les clusters HDInsight 2.0 et 3.0

## Dans cet article

-   [Génération de recommandations avec PowerShell][Génération de recommandations avec PowerShell]
-   [Classification de données avec la ligne de commande Hadoop][Classification de données avec la ligne de commande Hadoop]
-   [Résolution des problèmes][Résolution des problèmes]

## <a name="recommendations"></a>Génération de recommandations avec PowerShell

> [WACOM.NOTE] Bien que la tâche utilisée dans cette section fonctionne avec PowerShell, nombre des classes fournies avec Mahout ne fonctionnent pas actuellement avec PowerShell et doivent être exécutées à partir de la ligne de commande Hadoop. Pour obtenir une liste des classes qui ne fonctionnent pas avec PowerShell, consultez la section [Résolution des problèmes][Résolution des problèmes].
>
> Pour un exemple de l'utilisation de la ligne de commande Hadoop pour exécuter des tâches Mahout, consultez la section [Classification de données avec la ligne de commande Hadoop][Classification de données avec la ligne de commande Hadoop].

L'une des fonctions fournies par Mahout est un moteur de recommandation. Il accepte les données au format `userID`, `itemId`, `prefValue` (les préférences utilisateur pour l'élément). Mahout peut alors effectuer une analyse des co-occurrences afin de déterminer que *les utilisateurs ayant une préférence pour un élément ont également une préférence pour ces autres éléments*. Mahout détermine ensuite des utilisateurs avec des préférences similaires, qui peuvent alors être utilisées pour faire des recommandations.

Voici un exemple très simple de cette méthode pour les films :

-   **Co-occurrence** - Jean, Alice et Robert ont tous aimé *La Guerre des étoiles*, *L'Empire contre-attaque* et *Le Retour du Jedi*. Mahout détermine que les utilisateurs qui aiment l'un de ces films aiment également les deux autres.

-   **Co-occurrence** - Robert et Alice ont aussi aimé *La Menace fantôme*, *L'attaque des clones* et *La revanche des Sith*. Mahout détermine que les utilisateurs qui ont aimé les trois films précédents aiment également ces trois films.

-   **Recommandation par similarité** - Puisque Jean a aimé les trois premiers films, Mahout regarde les films que d'autres personnes ayant les mêmes goûts que lui ont aimé aussi, mais que Jean n'a pas encore vus (aimés/notés). Dans ce cas, Mahout recommanderait *La Menace fantôme*, *L'attaque des clones* et *La revanche des Sith*.

### Chargement des données

De façon pratique, GroupLens Research fournit des [données d'évaluation de films][données d'évaluation de films] dans un format compatible avec Mahout.

1.  Téléchargez l'archive [MovieLens 100k][MovieLens 100k], qui contient 100 000 évaluations de 1 000 utilisateurs sur 1 700 films.

2.  Extrayez l'archive. Elle doit contenir un répertoire **ml-100k**, qui contient de nombreux fichiers de données avec le préfixe **u.**. Le fichier analysé par Mahout est **u.data**. La structure de données de ce fichier est `userID`, `movieID`, `userRating` et `timestamp`. Voici un exemple de ces données.

        196 242 3   881250949
        186 302 3   891717742
        22  377 1   878887116
        244 51  2   880606923
        166 346 1   886397596

3.  Téléchargez le fichier **u.data** vers **example/data/u.data** sur votre cluster HDInsight. Si vous avez [Azure PowerShell][Azure PowerShell], vous pouvez utiliser le module PowerShell [HDInsight-Tools][HDInsight-Tools] pour télécharger le fichier. Pour d'autres façons de télécharger des fichiers, consultez la page [Téléchargement de données pour les tâches Hadoop dans HDInsight][Téléchargement de données pour les tâches Hadoop dans HDInsight]. Voici un exemple d'utilisation de `Add-HDInsightFile` pour télécharger le fichier.

        PS C:PS C:\> Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "your cluster name"gt; Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "your cluster name"

    Cela téléchargera le fichier **u.data** vers **example/data/u.data** sur le stockage par défaut de votre cluster. Il est ensuite possible d'accéder à ces données à partir de l'URI **wasb:///example/data/u.data** dans les tâches HDInsight.

### Exécution de la tâche

Utilisez le script PowerShell suivant pour exécuter une tâche à l'aide du moteur de recommandation Mahout avec le fichier **u.data** téléchargé.

    # The HDInsight cluster name.
    $clusterName = "the cluster name"

    # The location of the Mahout jar file.
    $jarFile = "file:///c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar"
    # NOTE: The version number portion of the file path
    # may change in future versions of HDInsight.
    # Use the following to find the location and name
    # of the Mahout jar on HDInsight 3.1, and modify
    # the $jarFile= line to point to it.
    #
    # Use-AzureHDInsightCluster -Name $clusterName
    # $jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasb:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "-s", "SIMILARITY_COOCCURRENCE",
                    "--input", "wasb:///example/data/u.data",
                    "--output", "wasb:///example/out",
                    "--tempDir", "wasb:///temp/mahout"

    # Create the job definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job

    # Write out any error information
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError

> [WACOM.NOTE] Les tâches Mahout ne suppriment pas les données temporaires créées lors du traitement de la tâche. C'est la raison pour laquelle le paramètre `--tempDir` est spécifié dans la tâche donnée en exemple : il isole les fichiers temporaires dans un chemin spécifique afin de pouvoir les effacer facilement.
>
> Pour supprimer ces fichiers, vous pouvez utiliser l'un des utilitaires mentionnés dans [Téléchargement de données pour les tâches Hadoop dans HDInsight][Téléchargement de données pour les tâches Hadoop dans HDInsight]. Vous pouvez également utiliser la fonction `Remove-HDInsightFile` du script PowerShell de [HDInsight-Tools][HDInsight-Tools].
>
> Si vous ne supprimez pas les fichiers temporaires ou le fichier de sortie, vous recevrez un message d'erreur si vous réexécutez la tâche.

La tâche Mahout ne renvoie pas la sortie vers STDOUT, mais la stocke dans le répertoire de sortie spécifié sous la forme **part-r-00000**. Pour télécharger et afficher le fichier, utilisez la fonction `Get-HDInsightFile` dans le module PowerShell de [HDInsight-Tools][HDInsight-Tools].

Voici un exemple de contenu du fichier :

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La première colonne est `userID`. Les valeurs contenues entre [ et ] sont `movieId` :`recommendationScore`.

### Affichage du résultat

Bien que le résultat généré puisse être utilisé dans une application, il n'est pas très lisible pour les humains. Certains des autres fichiers extraits plus tôt vers le dossier **ml-100k** peuvent être utilisés pour résoudre le `movieId` d'un nom de film. Bien qu'un script Python pour faire cela soit inclus dans le dossier **ml-100k** (**show\_recommendations.py**,) vous pouvez également utiliser le script PowerShell suivant.

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "u.data"
            -movieFile "u.item"
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

Pour utiliser ce script, vous devez avoir extrait le dossier **ml-100k**, ainsi qu'une copie locale du fichier de sortie **part-r-00000** généré par la tâche Mahout. Voici un exemple d'exécution du script.

    PS C:PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txtgt; show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt

> [WACOM.NOTE] Le script Python d'exemple, **show\_recommendations.py**, accepte les mêmes paramètres.

Le résultat doit ressembler à ce qui suit.

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

## <a name="classify"></a>Classification de données avec la ligne de commande Hadoop

L'une des méthodes de classification disponibles avec Mahout est la construction d'une [forêt aléatoire][forêt aléatoire]. Il s'agit d'un processus en plusieurs étapes qui implique l'utilisation de données de formation afin de générer des arbres de décision, qui sont ensuite utilisés pour classer des données. Il utilise la classe **org.apache.mahout.classifier.df.tools.Describe** fournie par Mahout, et doit être exécuté à partir de la ligne de commande Hadoop.

### Chargement des données

L'implémentation Mahout actuelle est compatible avec le format du référentiel de l'université de Californie, Irvine (UCI) [en quoi est-ce important ? quel est ce format ?]

1.  Téléchargez les fichiers ci-après dans <http://nsl.cs.unb.ca/NSL-KDD/>.

-   [KDDTrain+.ARFF][KDDTrain+.ARFF] - fichier de formation

-   [KDDTest+.ARFF][KDDTest+.ARFF] - données du test

1.  Ouvrez chaque fichier et supprimez les lignes en haut commençant par « @ », puis enregistrez les fichiers. Si les lignes ne sont pas supprimées, vous obtiendrez des erreurs lorsque vous utiliserez ces données avec Mahout.

2.  Téléchargez vers **example/data**. Pour cela, utilisez la fonction `Add-HDInsightFile` dans le module PowerShell de [HDInsight-Tools][HDInsight-Tools].

### Exécution de la tâche

1.  Cette tâche nécessitant la ligne de commande Hadoop, vous devez d'abord activer le Bureau à distance via le [portail de gestion Azure][portail de gestion Azure]. Dans le portail, sélectionnez votre cluster HDInsight, puis choisissez **Activer Distant** en bas de la page **Configuration**.

    ![enable remote][enable remote]

    Lorsque vous y êtes invité, saisissez un nom d'utilisateur et un mot de passe pour les sessions à distance.

2.  Une fois l'accès à distance activé, sélectionnez **Connecter** pour lancer la connexion. Un fichier **.rdp** sera alors téléchargé afin de démarrer une session Bureau à distance.

    ![connect][connect]

3.  Une fois la connexion établie, utilisez l'icône de **ligne de commande Hadoop** pour ouvrir la ligne de commande Hadoop.

    ![hadoop cli][hadoop cli]

4.  Utilisez la commande suivante pour générer le descripteur de fichier (**KDDTrain+.info**) à l'aide de Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` décrit les attributs des données du fichier. Un attribut numérique, 2 catégoriques, etc. L indique une étiquette.

5.  Construisez une forêt d'arbres décisionnels à l'aide de la commande suivante.

        hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    Le résultat de cette opération est stocké dans le répertoire **nsl-forest**, situé dans le stockage de votre cluster HDInsight à \_\_wasb://user/\<nom\_utilisateur\>/nsl-forest/nsl-forest.seq. \<nom\_utilisateur\> est le nom d'utilisateur utilisé pour votre session Bureau à distance. Ce fichier n'est pas lisible par les humains.

6.  Testez la forêt en classant l'ensemble de données **KDDTest+.arff** à l'aide de la commande ci-après.

        hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasb:///example/data/KDDTest+.arff -ds wasb:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasb:///example/data/predictions

    Cette commande renverra des informations récapitulatives sur un processus de classification du type suivant.

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

Cette tâche produit également un fichier situé dans **wasb:///example/data/predictions/KDDTest+.arff.out**. Toutefois, il n'est pas lisible par les humains.

> [WACOM.NOTE] Les tâches Mahout ne remplacent pas les fichiers. Si vous souhaitez réexécuter ces tâches, vous devez supprimer les fichiers créés par les tâches précédentes.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="install"></a>Installation de Mahout

Mahout est installé sur les clusters HDInsight 3.1 et peut être installé manuellement sur les clusters 3.0 ou 2.1 en suivant les étapes ci-dessous.

1.  La version de Mahout à utiliser dépend de la version de HDInsight de votre cluster. Vous trouverez la version de cluster grâce au processus suivant dans [Azure PowerShell][Azure PowerShell] :

        PS C:PS C:\> Get-AzureHDInsightCluster -Name YourClusterName | Select versiongt; Get-AzureHDInsightCluster -Name YourClusterName | Select version

-   **Pour HDInsight 2.1**, vous pouvez télécharger un fichier jar contenant [Mahout 0.9][Mahout 0.9].

-   **Pour HDInsight 3.0**, vous devez [générer Mahout à partir de la source][générer Mahout à partir de la source] et spécifier la version Hadoop fournie par HDInsight. Installez les éléments requis répertoriés sur la page de génération, téléchargez la source, puis utilisez la commande ci-après pour créer les fichiers jar de Mahout.

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        Once the build completes, the jar file will be created at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [WACOM.NOTE] Once Mahout 1.0 is released, you should be able to use the pre-built packages with HDInsight 3.0.

1.  Téléchargez le fichier jar vers **example/jars** dans le stockage par défaut de votre cluster. L'exemple suivant utilise le script [send-hdinsight][sendhdinsight] pour télécharger le fichier.

        PS C:PS C:\> .\Send-HDInsight -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"gt; .\Send-HDInsight -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"

### Remplacement de fichiers impossible

Les tâches Mahout ne suppriment pas les fichiers temporaires créés lors du traitement. En outre, les tâches ne remplaceront pas les fichiers de sortie existants.

Pour éviter les erreurs lors de l'exécution des tâches Mahout, supprimez les fichiers temporaires et de sortie entre les exécutions ou utilisez des noms de répertoire temporaire et de sortie uniques.

### Fichier jar introuvable

Bien que HDInsight 3.1 inclue Mahout, le chemin d'accès et le nom de fichier comprennent le numéro de version de l'installation de Mahout sur le cluster. L'exemple de script PowerShell dans ce didacticiel utilise un chemin d'accès valide en juillet 2014, mais le numéro de version changera lors des futures mises à jour de HDInsight. Pour déterminer le chemin d'accès actuel du fichier jar Mahout pour votre cluster, utilisez les commandes PowerShell ci-après, puis modifiez le script pour référencer le chemin de fichier renvoyé.

    Use-AzureHDInsightCluster -Name $clusterName
    $jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

### <a name="nopowershell"></a>Classes ne fonctionnant pas avec PowerShell

Les tâches Mahout qui utilisent les classes suivantes renvoient diverses erreurs si elles sont utilisées à partir de PowerShell.

-   org.apache.mahout.utils.clustering.ClusterDumper
-   org.apache.mahout.utils.SequenceFileDumper
-   org.apache.mahout.utils.vectors.lucene.Driver
-   org.apache.mahout.utils.vectors.arff.Driver
-   org.apache.mahout.text.WikipediaToSequenceFile
-   org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
-   org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
-   org.apache.mahout.classifier.sgd.TrainLogistic
-   org.apache.mahout.classifier.sgd.RunLogistic
-   org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
-   org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
-   org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
-   org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
-   org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
-   org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
-   org.apache.mahout.classifier.df.tools.Describe

Pour exécuter des tâches utilisant ces classes, connectez-vous au cluster HDInsight et exécutez les tâches à partir de la ligne de commande Hadoop. Pour un exemple, consultez la section [Classification de données avec la ligne de commande Hadoop][Classification de données avec la ligne de commande Hadoop].

  [Apache Mahout]: http://mahout.apache.org
  [Prise en main de Hadoop dans HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-get-started/
  [Installation de Mahout]: #install
  [apprentissage automatique]: http://en.wikipedia.org/wiki/Machine_learning
  [Génération de recommandations avec PowerShell]: #recommendations
  [Classification de données avec la ligne de commande Hadoop]: #classify
  [Résolution des problèmes]: #troubleshooting
  [données d'évaluation de films]: http://grouplens.org/datasets/movielens/
  [MovieLens 100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
  [Azure PowerShell]: http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/
  [HDInsight-Tools]: https://github.com/Blackmist/hdinsight-tools
  [Téléchargement de données pour les tâches Hadoop dans HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-upload-data/
  [forêt aléatoire]: http://en.wikipedia.org/wiki/Random_forest
  [KDDTrain+.ARFF]: http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff
  [KDDTest+.ARFF]: http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [enable remote]: ./media/hdinsight-mahout/enableremote.png
  [connect]: ./media/hdinsight-mahout/connect.png
  [hadoop cli]: ./media/hdinsight-mahout/hadoopcli.png
  [Mahout 0.9]: http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar
  [générer Mahout à partir de la source]: http://mahout.apache.org/developers/buildingmahout.html
