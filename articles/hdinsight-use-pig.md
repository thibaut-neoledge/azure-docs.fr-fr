<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Use Hadoop Pig in HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Pig in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Utilisation de Pig avec Hadoop dans HDInsight

Découvrez comment exécuter des tâches [Apache Pig][] dans HDInsight pour analyser un fichier journal Apache log4j.

**Durée de réalisation estimée :** 30 minutes

## Dans cet article

-   [Pig : cas d'emploi][]
-   [Configuration requise][]
-   [Présentation de Pig Latin][]
-   [Envoi de tâches Pig avec PowerShell][]
-   [Envoi de tâches Pig avec le Kit de développement logiciel (SDK) .NET HDInsight][]
-   [Étapes suivantes][]

## <span id="usage"></span></a>Pig : cas d'emploi

Les bases de données sont parfaites pour les petits jeux de données et les requêtes à faible latence. Mais lorsqu'il s'agit de gérer de gros volumes de données et des jeux de données qui se comptent en téraoctets, les bases de données SQL ne sont plus une bonne solution. Au fur et à mesure de l'augmentation de la charge de la base de données et de la dégradation des performances, les administrateurs ont généralement recours à l'achat de matériel plus performant.

En général, toutes les applications enregistrent les erreurs, exceptions et autres problèmes dans un fichier journal pour que l'administrateur puisse consulter les problèmes ou bien générer des mesures à partir des données du fichier journal. Ces fichiers journaux atteignent généralement une taille assez importante et contiennent énormément de données qui doivent être traitées et exploitées.

Les fichiers journaux sont un bon exemple de données volumineuses. L'utilisation de données volumineuses est difficile dans les bases de données relationnelles et les packages de statistiques et de visualisation. En raison des grandes quantités de données et des calculs effectués sur ces données, des logiciels parallèles exécutés sur des dizaines, des centaines, voire des milliers de serveurs sont souvent nécessaires pour traiter ces données dans un délai raisonnable. Hadoop contient une infrastructure MapReduce pour la création d'applications qui traitent en parallèle de grands volumes de données, structurées ou non, sur des clusters d'ordinateurs de façon extrêmement fiable et robuste.

[Apache *Pig*][Apache Pig] contient un langage de script pour exécuter les tâches *MapReduce*, ce qui permet d'éviter d'écrire du code Java. Le langage de script Pig est appelé *Pig Latin*. Les instructions Pig Latin suivent en général ce modèle :

-   **Chargement** : lecture des données à manipuler dans le système de fichiers
-   **Transformation** : manipulation des données
-   **Sortie ou stockage** : affichage du résultat à l'écran ou stockage pour traitement

L'utilisation de Pig réduit le temps nécessaire à la conception de programmes de mappage et de réduction. Ce qui signifie qu'aucun code Java n'est nécessaire, pas plus que le code réutilisable. Vous avez également la possibilité de combiner du code Java avec Pig. De nombreux algorithmes complexes peuvent être écrits en moins de cinq lignes de code Pig compréhensible.

La représentation visuelle de ce que vous allez accomplir dans cet article se trouve dans les deux figures qui suivent. Celles-ci présentent un exemple de données qui illustre le flux et la transformation des données au fur et à mesure des lignes de code Pig du script. La première figure présente un extrait du fichier journal log4j :

![exemple de fichier complet][]

La deuxième figure présente la transformation des données :

![HDI.PIG.Data.Transformation][]

Pour plus d'informations sur Pig Latin, consultez les pages [Manuel de référence Pig Latin 1][] et [Manuel de référence Pig Latin 2][].

## <span id="prerequisites"></span></a>Conditions préalables

Notez la configuration requise avant de commencer la lecture de cet article :

-   Un cluster Azure HDInsight. Pour obtenir des instructions, consultez le didacticiel [Prise en main d'Azure HDInsight][] ou la rubrique [Mise en service de clusters HDInsight][]. Vous aurez besoin des données suivantes pour suivre ce didacticiel :

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <tr class="header">
    <th align="left">Propriété du cluster</th>
    <th align="left">Nom de la variable PowerShell</th>
    <th align="left">Valeur</th>
    <th align="left">Description</th>
    </tr>
    <tr class="odd">
    <td align="left">Nom du cluster HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Cluster HDInsight sur lequel vous exécutez ce didacticiel.</td>
    </tr>
    </table>

-   Installez et configurez Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][].

**Présentation du stockage HDInsight**

HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Il s'intitule *WASB* ou *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft du HDFS sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][].

Lors de l'approvisionnement d'un cluster HDInsight, un compte Azure Storage et un conteneur de stockage d'objets blob spécifique de ce compte sont désignés en tant que système de fichiers par défaut, comme dans HDFS. En plus de ce compte de stockage, pendant l'approvisionnement, vous pouvez ajouter des comptes de stockage supplémentaires à partir du même abonnement Azure ou d'autres abonnements Azure. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters HDInsight][Mise en service de clusters HDInsight]. Afin de simplifier le script PowerShell utilisé dans le cadre de ce didacticiel, tous les fichiers sont conservés dans le conteneur de système de fichiers par défaut, sous */tutorials/usepig*. Par défaut, ce conteneur porte le même nom que le cluster HDInsight.
La syntaxe WASB est :

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Seule la syntaxe *wasb://* est prise en charge dans le cluster HDInsight version 3.0. L'ancienne syntaxe *asv://* est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais ne l'est pas dans les clusters HDInsight 3.0 et ne le sera pas dans les versions ultérieures.

> [WACOM.NOTE] Le chemin d'accès WASB est un chemin d'accès virtuel. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][].

Vous pouvez accéder à un fichier stocké dans le conteneur du système de fichiers par défaut à partir de HDInsight en utilisant l'un des URI suivants (sample.log est utilisé comme exemple) : Ce fichier est le fichier de données utilisé dans ce didacticiel :

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

    example/data/sample.log

Dans cet article, vous allez utiliser un exemple de fichier log4j distribué avec les clusters HDInsight stockés dans *\\example\\data\\sample.log*. Pour plus d'informations sur le téléchargement de vos propres fichiers de données, consultez la rubrique [Téléchargement de données vers HDInsight][].

## <span id="understand"></span></a> Présentation de Pig Latin

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

## <span id="powershell"></span></a>Envoi de tâches Pig avec PowerShell

Cette section contient des instructions sur l'utilisation des cmdlets PowerShell. Avant de parcourir cette section, vous devez configurer l'environnement local et la connexion à Azure. Pour plus de détails, consultez le didacticiel [Prise en main d'Azure HDInsight][] et la rubrique [Administration de HDInsight avec PowerShell][].

**Exécution de Pig Latin avec PowerShell**

1.  Ouvrez Windows PowerShell ISE (dans l'écran d'accueil Windows 8, tapez **PowerShell\_ISE**, puis cliquez sur **Windows PowerShell ISE**. Consultez la page [Démarrage de Windows PowerShell sur Windows 8 et Windows][]).
2.  Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

    Vous êtes invité à entrer les informations d'identification de votre compte Azure. Cette méthode d'ajout d'une connexion à un abonnement expire ; 12 heures plus tard, vous devez à nouveau exécuter la cmdlet.

    > [WACOM.NOTE] Si vous disposez de plusieurs abonnements Azure et que vous ne souhaitez pas utiliser l'abonnement défini par défaut, utilisez la cmdlet **Select-AzureSubscription** pour sélectionner l'abonnement actuel.

3.  Dans le volet de script, copiez et collez les lignes suivantes :

        $clusterName = "<HDInsightClusterName>" 
        $statusFolder = "/tutorials/usepig/status"

4.  Définissez la variable $clusterName.

5.  Ajoutez les lignes suivantes dans le volet de script. Ces lignes définissent la chaîne de requête Pig Latin et créent une définition de tâche Pig :

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

    Vous pouvez également utiliser le commutateur -File pour spécifier un fichier de script Pig sur HDFS. Le commutateur -StatusFolder place le journal des erreurs standard et le fichier de sortie standard dans le dossier.

6.  Ajoutez les lignes suivantes pour l'envoi de la tâche Pig :

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

7.  Ajoutez les lignes suivantes pour attendre la fin de la tâche Pig :

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8.  Ajoutez les lignes suivantes pour imprimer la sortie de la tâche Pig :

        # Print the standard error and the standard output of the Pig job.
        #Write-Host "Display the standard error log ..." -ForegroundColor Green
        #Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] L'une des cmdlets Get-AzureHDInsightJobOut est commentée pour raccourcir la sortie dans la capture d'écran suivante.

9.  Appuyez sur **F5** pour exécuter le script :
    ![HDI.Pig.PowerShell][]

    La tâche Pig calcule la fréquence des différents types de journaux.

## <span id="sdk"></span></a>Envoi de tâches Pig avec le Kit de développement logiciel (SDK) .NET HDInsight

Voici un exemple d'envoi de tâche Pig utilisant le Kit de développement logiciel (SDK) HDInsight .NET. Pour des instructions sur la création d'une application C# pour l'envoi de tâches Hadoop, consultez la rubrique [Envoi de tâches Hadoop par programme][].

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

-   [Prise en main d'Azure HDInsight][]
-   [Téléchargement de données vers HDInsight][]
-   [Envoi de tâches Hadoop par programme][]
-   [Utilisation de Hive avec HDInsight][]

  [Apache Pig]: http://pig.apache.org/
  [Pig : cas d'emploi]: #usage
  [Configuration requise]: #prerequisites
  [Présentation de Pig Latin]: #understand
  [Envoi de tâches Pig avec PowerShell]: #powershell
  [Envoi de tâches Pig avec le Kit de développement logiciel (SDK) .NET HDInsight]: #sdk
  [Étapes suivantes]: #nextsteps
  [exemple de fichier complet]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [Manuel de référence Pig Latin 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [Manuel de référence Pig Latin 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Prise en main d'Azure HDInsight]: ../hdinsight-get-started/
  [Mise en service de clusters HDInsight]: ../hdinsight-provision-clusters/
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [Téléchargement de données vers HDInsight]: ../hdinsight-upload-data/
  [Administration de HDInsight avec PowerShell]: ../hdinsight-administer-use-powershell/
  [Démarrage de Windows PowerShell sur Windows 8 et Windows]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [Envoi de tâches Hadoop par programme]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [Utilisation de Hive avec HDInsight]: ../hdinsight-use-hive/
