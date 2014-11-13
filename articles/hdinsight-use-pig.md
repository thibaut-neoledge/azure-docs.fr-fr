<properties urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Utilisation de Hadoop Pig dans HDInsight | Azure" metaKeywords="" description="Apprenez &agrave; utiliser Pig avec HDInsight. &Eacute;crivez des instructions Pig Latin pour analyser le fichier journal d'une application et ex&eacute;cutez des requ&ecirc;tes sur les donn&eacute;es afin de g&eacute;n&eacute;rer un r&eacute;sultat &agrave; des fins d'analyse." metaCanonical="" services="hdinsight" documentationCenter="" title="Utilisation de Hadoop Pig dans HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Utilisation de Pig avec Hadoop dans HDInsight

Ce didacticiel vous montre comment exécuter des tâches [Apache Pig][Apache Pig] sur HDInsight pour analyser des fichiers de données volumineux. Pig contient un langage de script pour exécuter les tâches *MapReduce*, ce qui permet d'éviter d'écrire du code Java. Le langage de script Pig est appelé *Pig Latin*.

**Configuration requise**

-   Vous devez avoir approvisionné un cluster Azure HDInsight. Pour obtenir des instructions, consultez le didacticiel [Prise en main d'Azure HDInsight][Prise en main d'Azure HDInsight] ou la rubrique [Mise en service de clusters HDInsight][Mise en service de clusters HDInsight]. Vous avez besoin du nom du cluster pour suivre le didacticiel.

-   Vous devez avoir installé et configuré Azure PowerShell sur votre poste de travail. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

**Durée de réalisation estimée :** 30 minutes

## Dans cet article

-   [Pourquoi utiliser Pig ?][Pourquoi utiliser Pig ?]
-   [Que dois-je faire dans ce didacticiel ?][Que dois-je faire dans ce didacticiel ?]
-   [Identification des données à analyser][Identification des données à analyser]
-   [Présentation de Pig Latin][Présentation de Pig Latin]
-   [Envoi de tâches Pig avec PowerShell][Envoi de tâches Pig avec PowerShell]
-   [Envoi de tâches Pig avec le Kit de développement logiciel (SDK) .NET HDInsight][Envoi de tâches Pig avec le Kit de développement logiciel (SDK) .NET HDInsight]
-   [Étapes suivantes][Étapes suivantes]

## <span id="usage"></span></a>Pourquoi utiliser Pig ?

L'utilisation de données volumineuses est difficile dans les bases de données relationnelles et les packages de statistiques et de visualisation. En raison des grandes quantités de données et des calculs effectués sur ces données, des logiciels parallèles exécutés sur des dizaines, des centaines, voire des milliers de serveurs sont souvent nécessaires pour traiter ces données dans un délai raisonnable. Hadoop contient une infrastructure *MapReduce* pour la création d'applications qui traitent en parallèle de grands volumes de données, structurées ou non, sur des clusters d'ordinateurs de façon extrêmement fiable et robuste.

![HDI.Pig.Architecture][HDI.Pig.Architecture]

[Apache *Pig*][Apache Pig] fournit une couche d'abstraction au-dessus de l'infrastructure MapReduce Java, ce qui permet aux utilisateurs d'analyser les données sans connaissance de Java ou MapReduce. Pig est une plateforme destinée à l'analyse de jeux de données volumineux avec un langage de flux de données facile à utiliser, *Pig Latin*. Pig réduit le temps nécessaire à la conception de programmes de mappage et de réduction. Il ne nécessite aucune connaissance de Java. Vous avez également la possibilité de combiner du code Java avec Pig. De nombreux algorithmes complexes peuvent être écrits en moins de cinq lignes de code Pig compréhensible.

Les instructions Pig Latin suivent en général ce modèle :

-   **Chargement** : lecture des données à manipuler dans le système de fichiers
-   **Transformation** : manipulation des données
-   **Sortie ou stockage** : affichage du résultat à l'écran ou stockage pour traitement

Pour plus d'informations sur Pig Latin, consultez les pages [Manuel de référence Pig Latin 1][Manuel de référence Pig Latin 1] et [Manuel de référence Pig Latin 2][Manuel de référence Pig Latin 2].

## <span id="what"></span></a>Que dois-je faire dans ce didacticiel ?

Dans ce didacticiel, vous analysez un fichier journal Apache (*sample.log*) pour déterminer le nombre de niveaux de journal différents (INFO, DEBUG, TRACE, etc.). Les deux figures ci-dessous illustrent visuellement ce que vous allez réaliser dans cet article. La première figure présente un extrait du fichier sample.log.

![exemple de fichier complet][exemple de fichier complet]

La seconde figure illustre le flux et la transformation des données au fur et à mesure que vous parcourez les lignes de code Pig dans le script :

![HDI.PIG.Data.Transformation][HDI.PIG.Data.Transformation]

La tâche Pig créée dans ce didacticiel suit le même flux.

## <span id="data"></span></a>Identification des données à analyser

HDInsight utilise le conteneur de stockage d'objets blob Azure comme système de fichiers par défaut pour les clusters Hadoop. Quelques exemples de fichiers de données sont ajoutés au stockage d'objets blob dans le cadre de l'approvisionnement du cluster. Vous pouvez vous en servir pour exécuter des requêtes Hive sur le cluster. Vous pouvez également, si vous le souhaitez, télécharger votre propre fichier de données vers le compte de stockage d'objets blob associé au cluster. Pour des instructions, consultez la page [Téléchargement de données vers HDInsight][Téléchargement de données vers HDInsight]. Pour plus d'informations sur l'utilisation du stockage d'objets blob Azure avec HDInsight, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

La syntaxe pour accéder aux fichiers dans le stockage d'objets blob est la suivante :

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Seule la syntaxe *wasb://* est prise en charge dans le cluster HDInsight version 3.0. L'ancienne syntaxe *asv://* est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais ne l'est pas dans les clusters HDInsight 3.0 et ne le sera pas dans les versions ultérieures.

Vous pouvez également accéder à un fichier stocké dans le conteneur du système de fichiers par défaut à partir de HDInsight en utilisant l'un des URI suivants (sample.log, le fichier de données utilisé dans ce didacticiel, vous servira d'exemple) :

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

    example/data/sample.log

Cet article utilise un exemple de fichier *log4j* distribué avec les clusters HDInsight qui est stocké dans *\\example\\data\\sample.log*. Pour plus d'informations sur le téléchargement de vos propres fichiers de données, consultez la rubrique [Téléchargement de données vers HDInsight][Téléchargement de données vers HDInsight].

## <span id="understand"></span></a> Présentation de Pig Latin

Dans cette section, vous allez aborder quelques instructions Pig Latin, ainsi que les résultats générés après leur exécution. Dans la section suivante, vous exécuterez PowerShell afin d'exécuter les instructions Pig ensemble pour analyser l'exemple de fichier journal. Les instructions Pig Latin individuelles doivent être exécutées directement sur le cluster HDInsight.

1.  Activez le Bureau à distance pour le cluster HDInsight en suivant les instructions de la page [Connexion aux clusters HDInsight avec RDP][Connexion aux clusters HDInsight avec RDP]. Connectez-vous au nœud de cluster et, sur le Bureau, cliquez sur **Ligne de commande Hadoop**.

2.  À partir de la ligne de commande, accédez au répertoire dans lequel est installé **Pig**. Type :

        C:\apps\dist\hadoop-<version>> cd %pig_home%\bin

3.  À l'invite de commandes, tapez *pig* et appuyez sur ENTRÉE pour accéder à l'interpréteur de commandes *grunt*.

        C:\apps\dist\pig-<version>\bin>pig
        ...
        grunt>  

4.  Entrez les informations suivantes pour charger les données de l'exemple de fichier dans le système de fichiers, puis afficher les résultats :

        grunt> LOGS = LOAD 'wasb:///example/data/sample.log';
        grunt> DUMP LOGS;

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

5.  Lisez chaque ligne du fichier de données pour rechercher une correspondance dans les 6 niveaux du journal :

        grunt> LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

6.  Excluez les lignes qui ne contiennent pas de correspondance et affichez les résultats. Le code qui suit permet d'éliminer les lignes vides.

        grunt> FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        grunt> DUMP FILTEREDLEVELS;

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

7.  Regroupez tous les niveaux de journal dans leur propre ligne et affichez le résultat :

        grunt> GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        grunt> DUMP GROUPEDLEVELS;

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

8.  Pour chaque groupe, comptez le nombre d'occurrences des niveaux de journal et affichez le résultat :

        grunt> FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        grunt> DUMP FREQUENCIES;

    Le résultat ressemble à ce qui suit :

        (INFO,96)
        (WARN,11)
        (DEBUG,434)
        (ERROR,6)
        (FATAL,2)
        (TRACE,816)

9.  Classez les fréquences dans l'ordre décroissant et affichez le résultat :

        grunt> RESULT = order FREQUENCIES by COUNT desc;
        grunt> DUMP RESULT;   

    Le résultat ressemble à ce qui suit :

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <span id="powershell"></span></a>Envoi de tâches Pig avec PowerShell

Cette section contient des instructions sur l'utilisation des cmdlets PowerShell. Avant de parcourir cette section, vous devez configurer l'environnement local et la connexion à Azure. Pour plus de détails, consultez le didacticiel [Prise en main d'Azure HDInsight][Prise en main d'Azure HDInsight] et la rubrique [Administration de HDInsight avec PowerShell][Administration de HDInsight avec PowerShell].

**Exécution de Pig Latin avec PowerShell**

1.  Ouvrez Windows PowerShell ISE. Dans l'écran d'accueil Windows 8, tapez **PowerShell\_ISE**, puis cliquez sur **Windows PowerShell ISE**. Pour plus d'informations, consultez la page [Démarrage de Windows PowerShell sur Windows 8 et Windows][Démarrage de Windows PowerShell sur Windows 8 et Windows].

2.  Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

    Vous êtes invité à entrer les informations d'identification de votre compte Azure. Cette méthode d'ajout d'une connexion à un abonnement expire ; 12 heures plus tard, vous devez à nouveau exécuter la cmdlet.

    > [WACOM.NOTE] Si vous disposez de plusieurs abonnements Azure et que vous ne souhaitez pas utiliser l'abonnement défini par défaut, utilisez la cmdlet **Select-AzureSubscription** pour sélectionner l'abonnement actuel.

3.  Dans le volet du script, copiez et collez les lignes suivantes :

        $clusterName = "<HDInsightClusterName>"     #Specify the cluster name
        $statusFolder = "/tutorials/usepig/status"  #Specify the folder to dump results

    Si le dossier status indiqué n'existe pas, le script le créera.

4.  Ajoutez les lignes suivantes dans le volet de script. Ces lignes définissent la chaîne de requête Pig Latin et créent une définition de tâche Pig :

        # Create the Pig job definition
        $0 = '$0';
        $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                        "RESULT = order FREQUENCIES by COUNT desc;" +
                        "DUMP RESULT;" 

        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

    Vous pouvez également utiliser le commutateur **-File** pour spécifier un fichier de script Pig sur HDFS. Le commutateur **-StatusFolder** place le journal d'erreur standard et le fichier de sortie standard dans le dossier.

5.  Ajoutez les lignes suivantes pour envoyer la tâche Pig :

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

6.  Ajoutez les lignes suivantes pour attendre que la tâche Pig soit terminée :

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

7.  Exécutez les lignes suivantes pour imprimer le résultat de la tâche Pig :

        # Print the standard error and the standard output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] L'une des cmdlets Get-AzureHDInsightJobOut est mise en commentaire pour raccourcir la sortie dans la capture d'écran suivante.

8.  Appuyez sur **F5** pour exécuter le script :

    ![HDI.Pig.PowerShell][HDI.Pig.PowerShell]

    La tâche Pig calcule la fréquence des différents types de journaux.

## <span id="sdk"></span></a>Envoi de tâches Pig avec le Kit de développement logiciel (SDK) .NET HDInsight

Procédez comme suit pour envoyer une tâche Pig avec une application C#. Pour des instructions sur la création d'une application C# pour l'envoi des tâches Hadoop, consultez la page [Envoi de tâches Hadoop par programme][Envoi de tâches Hadoop par programme].

1.  Créez un certificat auto-signé, installez-le sur votre poste de travail et téléchargez-le dans votre abonnement Azure. Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé][Création d'un certificat auto-signé].

2.  Créez une application console Visual Studio et installez le package HDInsight. Dans le menu Outils, cliquez sur **Gestionnaire de package NuGet**, puis cliquez sur **Console du Gestionnaire de package**. À l'invite de commandes, entrez les informations suivantes :

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

3.  Double-cliquez sur Program.cs et collez le code suivant pour envoyer une tâche Pig. Renseignez les variables.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        using System.IO;
        using System.Threading;
        using System.Security.Cryptography.X509Certificates;

        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.Hadoop.Client;

        namespace SubmitPigJobs
        {
            class Program
            {
                static void Main(string[] args)
                {
                    // Set the variables
                    string subscriptionID = "<Azure subscription ID>";
                    string certFriendlyName = "<certificate friendly name>";

                    string clusterName = "<HDInsight cluster name>";
                    string statusFolderName = @"/tutorials/usepig/status";

                    string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                        "RESULT = order FREQUENCIES by COUNT desc;" +
                        "DUMP RESULT;";

                    // Define the Pig job
                    PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
                    {
                        Query = queryString,
                        StatusFolder = statusFolderName
                    };

                    // Get the certificate object from certificate store using the friendly name to identify it
                    X509Store store = new X509Store();
                    store.Open(OpenFlags.ReadOnly);
                    X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

                    JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                    // Create a hadoop client to connect to HDInsight
                    var jobClient = JobSubmissionClientFactory.Connect(creds);

                    // Run the MapReduce job
                    Console.WriteLine("----- Submit the Pig job ...");
                    JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);

                    // Wait for the job to complete
                    Console.WriteLine("----- Wait for the Pig job to complete ...");
                    WaitForJobCompletion(mrJobResults, jobClient);

                    // Display the error log
                    Console.WriteLine("----- The Pig job error log.");
                    using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
                    {
                        var reader = new StreamReader(stream);
                        Console.WriteLine(reader.ReadToEnd());
                    }

                    // Display the output log
                    Console.WriteLine("----- The Pig job output log.");
                    using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
                    {
                        var reader = new StreamReader(stream);
                        Console.WriteLine(reader.ReadToEnd());
                    }

                    Console.WriteLine("----- Press ENTER to continue.");
                    Console.ReadLine();
                }

                private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
                {
                    JobDetails jobInProgress = client.GetJob(jobResults.JobId);
                    while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
                    {
                        jobInProgress = client.GetJob(jobInProgress.JobId);
                        Thread.Sleep(TimeSpan.FromSeconds(10));
                    }
                }
            }
        }

## <span id="nextsteps"></span></a>Étapes suivantes

Alors que Pig vous permet d'analyser les données, d'autres langages fournis avec HDInsight sont susceptibles de vous intéresser. Hive contient un langage de requête similaire à SQL qui permet d'effectuer des requêtes de façon simple sur les données stockées dans HDInsight. Les tâches MapReduce écrites en Java permettent quant à elles d'effectuer des opérations complexes d'analyse des données. Pour plus d'informations, consultez les liens suivants :

-   [Prise en main d'Azure HDInsight][Prise en main d'Azure HDInsight]
-   [Téléchargement de données vers HDInsight][Téléchargement de données vers HDInsight]
-   [Envoi de tâches Hadoop par programme][Envoi de tâches Hadoop par programme]
-   [Utilisation de Hive avec HDInsight][Utilisation de Hive avec HDInsight]

  [Apache Pig]: http://pig.apache.org/
  [Prise en main d'Azure HDInsight]: ../hdinsight-get-started/
  [Mise en service de clusters HDInsight]: ../hdinsight-provision-clusters/
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [Pourquoi utiliser Pig ?]: #usage
  [Que dois-je faire dans ce didacticiel ?]: #what
  [Identification des données à analyser]: #data
  [Présentation de Pig Latin]: #understand
  [Envoi de tâches Pig avec PowerShell]: #powershell
  [Envoi de tâches Pig avec le Kit de développement logiciel (SDK) .NET HDInsight]: #sdk
  [Étapes suivantes]: #nextsteps
  [HDI.Pig.Architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
  [Manuel de référence Pig Latin 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [Manuel de référence Pig Latin 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [exemple de fichier complet]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [Téléchargement de données vers HDInsight]: ../hdinsight-upload-data/
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [Connexion aux clusters HDInsight avec RDP]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-administer-use-management-portal/#rdp
  [Administration de HDInsight avec PowerShell]: ../hdinsight-administer-use-powershell/
  [Démarrage de Windows PowerShell sur Windows 8 et Windows]: http://technet.microsoft.com/fr-fr/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [Envoi de tâches Hadoop par programme]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [Création d'un certificat auto-signé]: http://go.microsoft.com/fwlink/?LinkId=511138
  [Utilisation de Hive avec HDInsight]: ../hdinsight-use-hive/
