<properties linkid="manage-services-hdinsight-submit-hadoop-jobs" urlDisplayName="HDInsight Administration" pageTitle="Submit Hadoop jobs in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hive, mapreduce, HDInsight .NET SDK, powershell, submit mapreduce jobs, submit hive jobs, development, hadoop, apache" description="Learn how to submit Hadoop jobs to Azure HDInsight Hadoop." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Submit  Hadoop jobs in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Submit Hadoop jobs in HDInsight

Cet article explique comment envoyer des tâches MapReduce et Hive en utilisant PowerShell et le Kit de développement logiciel (SDK) .NET HDInsight.

**Configuration requise :**

Avant de commencer cet article, vous devez disposer des éléments suivants :

-   Un cluster Azure HDInsight. pour obtenir des instructions, consultez le didacticiel [Prise en main de HDInsight][Prise en main de HDInsight] ou la rubrique [Configuration de clusters HDInsight][Configuration de clusters HDInsight].
-   Installez et configurez Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

## Dans cet article

-   [Envoi de tâches MapReduce avec PowerShell][Envoi de tâches MapReduce avec PowerShell]
-   [Envoi de tâches Hive avec PowerShell][Envoi de tâches Hive avec PowerShell]
-   [Envoi de tâches Sqoop avec PowerShell][Envoi de tâches Sqoop avec PowerShell]
-   [Envoi de tâches MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight][Envoi de tâches MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight]
-   [Submit Hadoop Streaming MapReduce jobs using HDInsight .NET SDK][Submit Hadoop Streaming MapReduce jobs using HDInsight .NET SDK]
-   [Envoi de tâches Hive avec le Kit de développement logiciel (SDK) .NET HDInsight][Envoi de tâches Hive avec le Kit de développement logiciel (SDK) .NET HDInsight]
-   [Étapes suivantes][Étapes suivantes]

## <span id="mapreduce-powershell"></span></a> Envoi de tâches MapReduce avec PowerShell

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d'informations sur l'utilisation de PowerShell avec HDInsight, consultez [Administration de HDInsight avec PowerShell][Administration de HDInsight avec PowerShell].

Hadoop MapReduce est une infrastructure logicielle permettant d'écrire des applications traitant d'importantes quantités de données. Les clusters HDInsight sont fournis avec un fichier .jar, situé sur *\\example\\jars\\hadoop-examples.jar*, qui contient différents exemples MapReduce. Ce fichier a été renommé hadoop-mapreduce-examples.jar sur les clusters HDInsight version 3.0. L'un des exemples suivants permet de compter les fréquences des mots dans les fichiers source. Dans cette session, vous apprendrez à utiliser PowerShell à partir d'un poste de travail exécutant l'exemple de comptage des mots. Pour plus d'informations sur le développement et l'exécution des tâches MapReduce, consultez l'article [Utilisation de MapReduce avec HDInsight][Utilisation de MapReduce avec HDInsight].

**Exécution d'une tâche MapReduce de comptage des mots avec PowerShell**

1.  Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

2.  Configurez ces deux variables en exécutant les commandes PowerShell suivantes :

        $subscriptionName = "<SubscriptionName>"   
        $clusterName = "<HDInsightClusterName>"    

    Vous allez utiliser l'abonnement vous ayant déjà servi à créer le cluster HDInsight. Et vous allez utiliser le cluster HDInsight pour exécuter la tâche MapReduce.

3.  Exécutez les commandes suivantes pour créer une définition de tâche MapReduce :

        # Define the word count MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    Il y a deux arguments. Le premier est le nom du fichier source ; le deuxième est le chemin d'accès au fichier de sortie. Pour plus d'informations sur le préfixe wasb, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

4.  Exécutez la commande suivante pour exécuter la tâche MapReduce :

        # Submit the MapReduce job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    En plus de la définition de la tâche MapReduce, fournissez également le nom du cluster HDInsight sur lequel vous souhaitez exécuter la tâche MapReduce.

5.  Exécutez la commande suivante pour vérifier que la tâche MapReduce est terminée :

        # Wait for the job to complete
        Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Exécutez la commande suivante pour vérifier si l'exécution de la tâche MapReduce génère des erreurs :

        # Get the job standard error output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

    La capture d'écran ci-dessous présente la sortie d'une exécution réussie. En cas d'échec, elle aurait comporté des messages d'erreur.

    ![HDI.GettingStarted.RunMRJob][HDI.GettingStarted.RunMRJob]

**Pour récupérer les résultats de la tâche MapReduce**

1.  Ouvrez **Azure PowerShell**.
2.  Configurez ces trois variables en exécutant les commandes PowerShell suivantes :

        $subscriptionName = "<SubscriptionName>"       
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"          

    Le compte Azure Storage est celui que vous avez indiqué durant la configuration du cluster HDInsight. Il sert à héberger le conteneur d'objets blob utilisé comme système de fichiers par défaut du cluster HDInsight. Le conteneur de stockage d'objets blob et le cluster HDInsight partagent généralement le même nom, à moins que vous spécifiiez un nom différent lors de la configuration du cluster.

3.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage Azure :

        # Create the storage account context object
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Select-AzureSubscription sert à définir l'abonnement actif dans le cas où vous disposez de plusieurs abonnements et que l'abonnement par défaut n'est pas celui qui doit être utilisé.

4.  Exécutez la commande suivante pour télécharger la sortie de la tâche MapReduce du conteneur d'objets blob vers la station de travail :

        # Get the blob content
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Le dossier *example/data/WordCountOutput* est le dossier de sortie spécifié lors de l'exécution de la tâche MapReduce. *part-r-00000* est le nom de fichier par défaut pour la sortie de la tâche MapReduce. Le fichier est téléchargé dans la même structure de dossiers que le dossier local. Par exemple, dans la capture d'écran suivante, le dossier actif est le dossier racine C. Le fichier est donc téléchargé dans le dossier \*C:\\example\\data\\WordCountOutput\*.

5.  Exécutez la commande suivante pour imprimer le fichier de sortie de la tâche MapReduce :

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput][HDI.GettingStarted.MRJobOutput]

    La tâche MapReduce produit un fichier nommé *part-r-00000* avec les mots et les décomptes. Le script utilise la commande findstr pour répertorier tous les mots contenant "there".

> [WACOM.NOTE] Si vous ouvrez ./example/data/WordCountOutput/part-r-00000, sortie de plusieurs lignes résultant d'une tâche MapReduce, dans le Bloc-notes, vous remarquerez que les sauts de ligne ne sont pas correctement rendus. Ceci est normal.

## <span id="hive-powershell"></span></a> Envoi de tâches Hive avec PowerShell

Apache [hdinsight-use-hive][hdinsight-use-hive] provides a means of running MapReduce job through an SQL-like scripting language, called *HiveQL*, which can be applied towards summarization, querying, and analysis of large volumes of data.

Les clusters HDInsight sont fournis avec un exemple de table Hive nommé *hivesampletable*. Dans cette session, vous allez utiliser PowerShell pour exécuter une tâche Hive pour répertorier certaines données à partir de la table Hive.

**Exécution d'une tâche Hive avec PowerShell**

1.  Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

2.  Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

        $subscriptionName = "<SubscriptionName>"   
        $clusterName = "<HDInsightClusterName>"             
        $querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

    $querystring est la requête HiveQL.

3.  Exécutez les commandes suivantes pour sélectionner un abonnement Azure et le cluster pour exécuter la tâche Hive :

        Select-AzureSubscription -SubscriptionName $subscriptionName

4.  Envoyez la tâche Hive :

        Use-AzureHDInsightCluster $clusterName
        Invoke-Hive -Query $queryString

    Vous pouvez utiliser le commutateur -File pour indiquer un fichier de script HiveQL sur HDFS.

Pour plus d'informations sur Hive, consultez l'article [Utilisation de Hive avec HDInsight][Utilisation de Hive avec HDInsight].

## <span id="sqoop-powershell"></span></a>Envoi de tâches Sqoop avec PowerShell

Consultez l'article [Utilisation de Sqoop avec HDInsight][Utilisation de Sqoop avec HDInsight].

## <span id="mapreduce-sdk"></span></a> Envoi de tâches MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation des clusters HDInsight à partir de .NET. Les clusters HDInsight sont fournis avec un fichier .jar, situé sur *\\example\\jars\\hadoop-examples.jar*, qui contient différents exemples MapReduce. L'un des exemples suivants permet de compter les fréquences des mots dans les fichiers source. Dans cette session, vous apprendrez à créer une application .NET pour exécuter l'exemple de comptage des mots. Pour plus d'informations sur le développement et l'exécution des tâches MapReduce, consultez l'article [Utilisation de MapReduce avec HDInsight][Utilisation de MapReduce avec HDInsight].

Les procédures suivantes sont nécessaires pour configurer un cluster HDInsight en utilisant le Kit de développement logiciel (SDK) :

-   Installation du Kit de développement logiciel (SDK) HDInsight
-   Création d'une application console
-   Exécution de l'application

**Installation du Kit de développement logiciel (SDK) .NET HDInsight**
 : vous pouvez installer la dernière version du SDK publiée sur [NuGet][NuGet]. Vous pourrez consulter les instructions dans la procédure suivante.

**Création d'une application console Visual Studio**

1.  Ouvrez Visual Studio 2012.

2.  Dans le menu Fichier, cliquez sur **Nouveau**, puis sur **Projet**.

3.  Dans Nouveau projet, entrez ou sélectionnez les valeurs suivantes :

    | Propriété | Valeur                     |
    |-----------|----------------------------|
    | Catégorie | Modèles/Visual C#/Windows |
    | Modèle    | Application console        |
    | Nom       | SubmitMapReduceJob         |

4.  Cliquez sur **OK** pour créer le projet.

5.  Dans le menu **Outils**, cliquez sur **Library Package Manager**, puis sur **Console du Gestionnaire de package**.

6.  Exécutez les commandes suivantes dans la console pour installer les packages.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Cette commande ajoute des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours. La version doit être 0.11.0.1 ou plus récente.

7.  Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8.  Ajoutez les instructions using suivantes au début du fichier :

        using System.IO;
        using System.Threading;
        using System.Security.Cryptography.X509Certificates;

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.Hadoop.Client;

9.  Ajoutez la définition de fonction suivante à la classe. Cette fonction permet d'attendre la fin d'une tâche Hadoop :

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }

10. Dans la fonction Main(), copiez et collez le code suivant :

        // Set the variables
        string subscriptionID = "<Azure subscription ID>";
        string certFriendlyName = "<certificate friendly name>";

        string clusterName = "<HDInsight cluster name>";

        string storageAccountName = "<Azure storage account name>";
        string storageAccountKey = "<Azure storage account key>";
        string containerName = "<Blob container name>";

    Vous devez définir toutes ces variables pour le programme. Vous pouvez obtenir le nom de l'abonnement Azure à partir du [portail de gestion Azure][portail de gestion Azure].

    Pour plus d'informations sur les certificats, consultez la page [Créer et télécharger un certificat de gestion pour Windows Azure][Créer et télécharger un certificat de gestion pour Windows Azure]. Pour configurer facilement les certificats, exécutez les cmdlets PowerShell *Get-AzurePublishSettingsFile* et *Import-AzurePublishSettingsFile*. Elles vont créer le certificat de gestion et le télécharger automatiquement. Après avoir exécuté les cmdlets PowerShell, ouvrez *certmgr.msc* sur le poste de travail, puis recherchez le certificat en développant *Personal/Certificates*. La valeur des champs *Délivré à* et *Délivré par* du certificat créé par les cmdlets PowerShell est *Outils Azure*.

    Le nom du compte Azure Storage est celui que vous avez indiqué durant la configuration du cluster HDInsight. Par défaut, le nom du conteneur est identique à celui du cluster HDInsight.

11. Dans la fonction Main(), ajoutez le code suivant pour définir la tâche MapReduce :

        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

    Il y a deux arguments. Le premier est le nom du fichier source ; le deuxième est le chemin d'accès au fichier de sortie. Pour plus d'informations sur le préfixe wasb, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

12. Dans la fonction Main(), ajoutez le code suivant pour créer un objet JobSubmissionCertificateCredential :

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. Dans la fonction Main(), ajoutez le code suivant pour exécuter la tâche et attendre qu'elle soit terminée :

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. Dans la fonction Main(), ajoutez le code suivant pour imprimer le résultat de la tâche MapReduce :

        // Print the MapReduce job output
        Stream stream = new MemoryStream();

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");

        blockBlob.DownloadToStream(stream);
        stream.Position = 0;

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

    Le dossier de sortie est spécifié lorsque vous définissez la tâche MapReduce. Le nom de fichier par défaut est *part-r-00000*.

**Exécution de l'application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application, ainsi que son résultat.

## <span id="streaming-sdk"></span></a> Submit Hadoop streaming jobs using HDInsight .NET SDK

HDInsight clusters come with a word counting Hadoop stream program developed in C#. The mapper program is */example/apps/cat.exe*, and the reduce program is */example/apps/wc.exe*. In this session, you will learn how to create a .NET application to run the word counting sample.

For the details of creating a .Net application for submitting MapReduce jobs, see [Submit MapReduce jobs using HDInsight .NET SDK][Envoi de tâches MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight].

For more information on developing and deploying Hadoop streaming jobs, see [Develop C# Hadoop streaming programs for HDInsight][Develop C# Hadoop streaming programs for HDInsight].

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

    namespace SubmitStreamingJob
    {
        class Program
        {
            static void Main(string[] args)
            {

                // Set the variables
                string subscriptionID = "<Azure subscription ID>";
                string certFriendlyName = "<certificate friendly name>";
        
                string clusterName = "<HDInsight cluster name>";
                string statusFolderName = @"/tutorials/wordcountstreaming/status";

                // Define the Hadoop streaming MapReduce job
                StreamingMapReduceJobCreateParameters myJobDefinition = new StreamingMapReduceJobCreateParameters()
                {
                    JobName = "my word counting job",
                    StatusFolder = statusFolderName,
                    Input = "/example/data/gutenberg/davinci.txt",
                    Output = "/tutorials/wordcountstreaming/output",
                    Reducer = "wc.exe",
                    Mapper = "cat.exe"
                };

                myJobDefinition.Files.Add("/example/apps/wc.exe");
                myJobDefinition.Files.Add("/example/apps/cat.exe");

                // Get the certificate object from certificate store using the friendly name to identify it
                X509Store store = new X509Store();
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

                JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                // Create a hadoop client to connect to HDInsight
                var jobClient = JobSubmissionClientFactory.Connect(creds);

                // Run the MapReduce job
                Console.WriteLine("----- Submit the Hadoop streaming job ...");
                JobCreationResults mrJobResults = jobClient.CreateStreamingJob(myJobDefinition);

                // Wait for the job to complete
                Console.WriteLine("----- Wait for the Hadoop streaming job to complete ...");
                WaitForJobCompletion(mrJobResults, jobClient);

                // Display the error log
                Console.WriteLine("----- The hadoop streaming job error log.");
                using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                // Display the output log
                Console.WriteLine("----- The hadoop streaming job output log.");
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

## <span id="hive-sdk"></span></a> Envoi de tâches Hive avec le Kit de développement logiciel (SDK) .NET HDInsight

Les clusters HDInsight sont fournis avec un exemple de table Hive nommé *hivesampletable*. Dans cette session, vous allez créer une application .NET pour exécuter une tâche Hive écoutant les tables Hive créées sur le cluster HDInsight. Pour plus d'informations sur l'utilisation de Hive, consultez l'article [Utilisation de Hive avec HDInsight][Utilisation de Hive avec HDInsight].

Les procédures suivantes sont nécessaires pour configurer un cluster HDInsight en utilisant le Kit de développement logiciel (SDK) :

-   Installation du Kit de développement logiciel (SDK) HDInsight
-   Création d'une application console
-   Exécution de l'application

**Installation du Kit de développement logiciel (SDK) .NET HDInsight**
 : vous pouvez installer la dernière version du SDK publiée sur [NuGet][NuGet]. Vous pourrez consulter les instructions dans la procédure suivante.

**Création d'une application console Visual Studio**

1.  Ouvrez Visual Studio 2012.

2.  Dans le menu Fichier, cliquez sur **Nouveau**, puis sur **Projet**.

3.  Dans Nouveau projet, entrez ou sélectionnez les valeurs suivantes :

    | Propriété | Valeur                     |
    |-----------|----------------------------|
    | Catégorie | Modèles/Visual C#/Windows |
    | Modèle    | Application console        |
    | Nom       | SubmitHiveJob              |

4.  Cliquez sur **OK** pour créer le projet.

5.  Dans le menu **Outils**, cliquez sur **Library Package Manager**, puis sur **Console du Gestionnaire de package**.

6.  Exécutez les commandes suivantes dans la console pour installer les packages.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Cette commande ajoute des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

7.  Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8.  Ajoutez les instructions using suivantes au début du fichier :

        using System.IO;
        using System.Threading;
        using System.Security.Cryptography.X509Certificates;

        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.Hadoop.Client;

9.  Ajoutez la définition de fonction suivante à la classe. Cette fonction permet d'attendre la fin d'une tâche Hadoop :

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }

10. Dans la fonction Main(), copiez et collez le code suivant :

        // Set the variables
        string subscriptionID = "<Azure subscription ID>";
        string clusterName = "<HDInsight cluster name>";
        string certFriendlyName = "<certificate friendly name>";        

    Vous devez définir toutes ces variables pour le programme. Vous pouvez obtenir l'ID d'abonnement Azure auprès de votre administrateur système.

    Pour plus d'informations sur les certificats, consultez la page [Créer et télécharger un certificat de gestion pour Windows Azure][Créer et télécharger un certificat de gestion pour Windows Azure]. Pour configurer facilement les certificats, exécutez les cmdlets PowerShell *Get-AzurePublishSettingsFile* et *Import-AzurePublishSettingsFile*. Elles vont créer le certificat de gestion et le télécharger automatiquement. Après avoir exécuté les cmdlets PowerShell, ouvrez *certmgr.msc* sur le poste de travail, puis recherchez le certificat en développant *Personal/Certificates*. La valeur des champs *Délivré à* et *Délivré par* du certificat créé par les cmdlets PowerShell est *Outils Azure*.

11. Dans la fonction Main(), ajoutez le code suivant pour définir la tâche Hive :

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

    Vous pouvez également utiliser le paramètre File pour indiquer un fichier de script HiveQL sur HDFS. Par exemple :

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

12. Dans la fonction Main(), ajoutez le code suivant pour créer un objet JobSubmissionCertificateCredential :

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. Dans la fonction Main(), ajoutez le code suivant pour exécuter la tâche et attendre qu'elle soit terminée :

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);

14. Dans la fonction Main(), ajoutez le code suivant pour imprimer le résultat de la tâche Hive :

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**Exécution de l'application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. Le résultat doit être :

    hivesampletable

## <span id="nextsteps"></span></a>Étapes suivantes

Cet article vous a présenté différentes méthodes pour configurer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main d'Azure HDInsight][Prise en main de HDInsight]
-   [Configuration de clusters HDInsight][Configuration de clusters HDInsight]
-   [Administration de HDInsight à l'aide de PowerShell][Administration de HDInsight avec PowerShell]
-   [Documentation de référence des cmdlets HDInsight][Documentation de référence des cmdlets HDInsight]
-   [Utilisation de Hive avec HDInsight][Utilisation de Hive avec HDInsight]
-   [Utilisation de Pig avec HDInsight][Utilisation de Pig avec HDInsight]

  [Prise en main de HDInsight]: ../hdinsight-get-started/
  [Configuration de clusters HDInsight]: ../hdinsight-provision-clusters/
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [Envoi de tâches MapReduce avec PowerShell]: #mapreduce-powershell
  [Envoi de tâches Hive avec PowerShell]: #hive-powershell
  [Envoi de tâches Sqoop avec PowerShell]: #sqoop-powershell
  [Submit Hadoop Streaming MapReduce jobs using HDInsight .NET SDK]: #streaming-sdk
  [Étapes suivantes]: #nextsteps
  [Administration de HDInsight avec PowerShell]: ../hdinsight-administer-use-powershell/
  [Utilisation de MapReduce avec HDInsight]: ../hdinsight-use-mapreduce/
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [HDI.GettingStarted.RunMRJob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
  [HDI.GettingStarted.MRJobOutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png
  [hdinsight-use-hive]: http://hive.apache.org/
  [Utilisation de Hive avec HDInsight]: ../hdinsight-use-hive/
  [Utilisation de Sqoop avec HDInsight]: ../hdinsight-use-sqoop/
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [portail de gestion Azure]: http://manage.windowsazure.com/
  [Créer et télécharger un certificat de gestion pour Windows Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg551722.aspx
  [Develop C# Hadoop streaming programs for HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Documentation de référence des cmdlets HDInsight]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn479228.aspx
  [Utilisation de Pig avec HDInsight]: ../hdinsight-use-pig/
