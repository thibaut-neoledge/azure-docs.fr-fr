<properties 
	pageTitle="Envoi de tâches Hadoop dans HDInsight | Azure" 
	description="Apprenez à envoyer des tâches Hadoop à Azure HDInsight Hadoop." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>

# Envoi de tâches Hadoop dans HDInsight

Découvrez comment utiliser Azure PowerShell pour envoyer des tâches MapReduce et Hive et comment utiliser le Kit de développement logiciel (SDK) HDInsight .NET pour envoyer des tâches MapReduce, des tâches de diffusion Hadoop et des tâches Hive.

##Configuration requise

Avant de commencer cet article, vous devez disposer des éléments suivants :

* Un cluster Azure HDInsight. Pour obtenir des instructions, consultez le didacticiel [Prise en main de HDInsight][hdinsight-get-started] ou la rubrique [Configuration de clusters HDInsight][hdinsight-provision].
* Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install-configure].


##Envoi de tâches MapReduce avec Azure PowerShell
Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d'informations sur l'utilisation d’Azure PowerShell avec HDInsight, consultez [Gestion de HDInsight avec PowerShell][hdinsight-admin-powershell].

Hadoop MapReduce est une infrastructure logicielle qui permet d'écrire des applications traitant d'importantes quantités de données. Les clusters HDInsight sont fournis avec un fichier JAR (situé sous *\\example\\jars\\hadoop-examples.jar*), qui contient différents exemples MapReduce.

L'un des exemples suivants permet de compter les fréquences des mots dans les fichiers source. Dans cette session, vous apprendrez à utiliser Azure PowerShell à partir d'un poste de travail pour exécuter l'exemple de comptage des mots. Pour plus d'informations sur le développement et l'exécution des tâches MapReduce, consultez [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce].

**Exécution du programme MapReduce de comptage des mots avec Azure PowerShell**

1.	Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez [Installation et configuration d'Azure PowerShell][powershell-install-configure].

3. Configurez les variables suivantes en exécutant ces commandes Azure PowerShell :
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"    

	Le nom de l’abonnement est celui que vous avez utilisé pour créer le cluster HDInsight. Vous allez utiliser le cluster HDInsight pour exécuter la tâche MapReduce.
	
5. Exécutez les commandes suivantes pour créer une définition de tâche MapReduce :

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	Il y a deux arguments. Le premier est le nom du fichier source ; le deuxième est le chemin d'accès au fichier de sortie. Pour plus d'informations sur le préfixe wasb://, consultez [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

6. Exécutez la commande suivante pour exécuter la tâche MapReduce :

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

	En plus de la définition de la tâche MapReduce, vous devez également fournir le nom du cluster HDInsight sur lequel vous souhaitez exécuter la tâche MapReduce.

7. Exécutez la commande suivante pour vérifier que la tâche MapReduce est terminée :

		# Wait for the job to complete
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 
		

8. Exécutez la commande suivante pour vérifier si l'exécution de la tâche MapReduce génère des erreurs :

		# Get the job standard error output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
					
	La capture d'écran ci-dessous présente la sortie d'une exécution réussie. En cas d'échec, elle aurait comporté des messages d'erreur.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]

		
**Pour récupérer les résultats de la tâche MapReduce**

1. Ouvrez **Azure PowerShell**.
2. Configurez les variables suivantes en exécutant ces commandes Azure PowerShell :

		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"			

	Le nom du compte de stockage est le compte de stockage Azure que vous avez indiqué lors de l’approvisionnement du cluster HDInsight. Le compte de stockage sert à héberger le conteneur d'objets blob utilisé comme système de fichiers par défaut du cluster HDInsight. Le nom du conteneur a généralement le même nom que celui du cluster HDInsight, sauf si vous indiquez un autre nom lors de l’approvisionnement du cluster.

3. Exécutez les commandes suivantes pour créer un objet de contexte de stockage d’objets blob Azure :

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription** sert à définir l'abonnement actuel dans le cas où vous disposez de plusieurs abonnements et que l'abonnement par défaut n'est pas celui qui doit être utilisé.

4. Exécutez la commande suivante pour télécharger la sortie de la tâche MapReduce du conteneur d'objets blob vers le poste de travail :

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Le dossier *example/data/WordCountOutput* est le dossier de sortie spécifié lors de l'exécution de la tâche MapReduce. *part-r-00000* est le nom de fichier par défaut pour la sortie de la tâche MapReduce. Le fichier est téléchargé dans la même structure de dossiers que le dossier local. Par exemple, dans la capture d'écran suivante, le dossier actuel est le dossier racine C:. Le fichier est téléchargé dans :

*C:\\example\\data\\WordCountOutput* 

5. Exécutez la commande suivante pour imprimer le fichier de sortie de la tâche MapReduce :

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	La tâche MapReduce génère un fichier nommé *part-r-00000* qui contient les mots et les décomptes. Le script utilise la commande **findstr** pour répertorier tous les mots contenant « there ».


> [AZURE.NOTE]Si vous ouvrez./example/data/WordCountOutput/part-r-00000 (une sortie de plusieurs lignes résultant d’une tâche MapReduce) dans le Bloc-notes, vous remarquerez que les sauts de ligne ne sont pas correctement rendus. Ceci est normal.









































































































































##Envoi de tâches Hive avec Azure PowerShell
[Apache Hive][apache-hive] permet d'exécuter une tâche MapReduce par l'intermédiaire d'un langage de script similaire à SQL, appelé *HiveQL*, qui peut être appliqué pour résumer, interroger et analyser de gros volumes de données.

Les clusters HDInsight sont fournis avec un exemple de table Hive nommé *hivesampletable*. Dans cette session, vous allez utiliser Azure PowerShell pour exécuter une tâche Hive afin de répertorier certaines données à partir de la table Hive.

**Envoi d’une tâche Hive avec Azure PowerShell**

1.	Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez [Installation et configuration d'Azure PowerShell][powershell-install-configure].

2. Définissez les deux variables dans les commandes suivantes, puis exécutez-les :
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"             
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring est la requête HiveQL.

3. Exécutez la commande suivante pour sélectionner un abonnement Azure et le cluster pour exécuter la tâche Hive :

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. Exécutez la commande suivante pour envoyer la tâche Hive :

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	Vous pouvez utiliser le commutateur **-File** pour spécifier un fichier de script HiveQL dans le système de fichiers distribué de Hadoop (HDFS).

Pour plus d'informations sur Hive, consultez l'article [Utilisation de Hive avec HDInsight][hdinsight-use-hive].


## Envoi de tâches Hive avec Visual Studio

Consultez [Prise en main des outils HDInsight Hadoop pour Visual Studio][hdinsight-visual-studio-tools].

##Envoi de tâches Sqoop avec Azure PowerShell

Consultez l'article [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop].

##Envoi de tâches MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight
Le Kit de développement logiciel (SDK) HDInsight .NET fournit des bibliothèques clientes .NET, ce qui facilite l'utilisation des clusters HDInsight à partir de .NET. Les clusters HDInsight sont fournis avec un fichier JAR (situé sous *\\example\\jars\\hadoop-examples.jar*), qui contient différents exemples MapReduce. L'un des exemples suivants permet de compter les fréquences des mots dans les fichiers source. Dans cette session, vous apprendrez à créer une application .NET pour exécuter l'exemple de comptage des mots. Pour plus d'informations sur le développement et l'exécution de tâches MapReduce, consultez [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce].


Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight en utilisant le Kit de développement logiciel (SDK) :

- Installation du Kit de développement logiciel (SDK) HDInsight
- Création d'une application console
- Exécution de l'application


**Installation du Kit de développement logiciel (SDK) .NET HDInsight** : vous pouvez installer la dernière version du SDK publiée sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Création d'une application console Visual Studio**

1. Ouvrez Visual Studio.

2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriété</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valeur</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Catégorie</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modèles/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modèle</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Application console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
</table>

4. Cliquez sur **OK** pour créer le projet.


5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package de bibliothèques**, puis cliquez sur **Console du gestionnaire de package**.

6. Exécutez les commandes suivantes dans la console pour installer les packages.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Cette commande ajoute des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours. La version doit être 0.11.0.1 ou une version ultérieure.

7. Dans l'**Explorateur de solutions**, double-cliquez sur **Program.cs** pour l'ouvrir.

8. Ajoutez les instructions using suivantes au début du fichier :

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;
		
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Ajoutez la définition de fonction suivante à la classe. Cette fonction permet d'attendre la fin d'une tâche Hadoop :

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
10. Dans la fonction **Main()**, collez le code suivant :
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string certFriendlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";
		
		string storageAccountName = "<Azure storage account name>";
		string storageAccountKey = "<Azure storage account key>";
		string containerName = "<Blob container name>";
		
	
	Vous devez définir toutes ces variables pour le programme. Vous pouvez obtenir le nom de l'abonnement Azure à partir du [portail Azure][azure-management-portal].

	Pour plus d'informations sur le certificat, consultez [Créer et télécharger un certificat de gestion pour Azure][azure-certificate]. Pour configurer facilement les certificats, exécutez les cmdlets PowerShell **Get-AzurePublishSettingsFile** et **Import-AzurePublishSettingsFile**. Elles vont créer le certificat de gestion et le télécharger automatiquement. Après avoir exécuté ces cmdlets, ouvrez **certmgr.msc** sur le poste de travail puis recherchez le certificat en développant **Personnel** > **Certificats** Le certificat qui est créé par les cmdlets d’Azure PowerShell dispose des outils Azure pour les champs **Émis à** et **Émis par**.

	Le nom du compte Azure est celui que vous avez indiqué lors de l’approvisionnement du cluster HDInsight. Par défaut, le nom du conteneur est identique à celui du cluster HDInsight.
	
11. Dans la fonction **Main()**, ajoutez le code suivant pour définir la tâche MapReduce :


        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

	Il y a deux arguments. Le premier est le nom du fichier source ; le deuxième est le chemin d'accès au fichier de sortie. Pour plus d'informations sur le préfixe wasb://, consultez [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].
		
12. Dans la fonction **Main()**, ajoutez le code suivant pour créer un objet JobSubmissionCertificateCredential :

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. Dans la fonction **Main()**, ajoutez le code suivant pour exécuter la tâche et attendre qu'elle soit terminée :

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. Dans la fonction **Main()**, ajoutez le code suivant pour imprimer le résultat de la tâche MapReduce :

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

	Le dossier de sortie est spécifié lorsque vous définissez la tâche MapReduce. Le nom de fichier par défaut est **part-r-00000**.

**Exécution de l'application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application, ainsi que son résultat.

##Envoi de tâches de diffusion en continu Hadoop avec le Kit de développement logiciel (SDK) .NET HDInsight
Les clusters HDInsight sont fournis avec un programme de diffusion en continu Hadoop pour le comptage de mots développé en C#. Le programme de mappage et le programme de réduction sont, respectivement, */example/apps/cat.exe* et */example/apps/wc.exe*. Dans cette session, vous allez apprendre à créer une application .NET pour exécuter l'exemple de comptage des mots.

Pour des informations détaillées sur la création d'une application .NET pour l'envoi de tâches MapReduce, consultez la page [Envoi de tâches MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight](#mapreduce-sdk).

Pour plus d'informations sur le développement et le déploiement de tâches de diffusion en continu Hadoop, consultez la page [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight][hdinsight-develop-streaming-jobs].

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






##Envoi de tâches Hive avec le Kit de développement logiciel (SDK) .NET HDInsight 
Les clusters HDInsight sont fournis avec un exemple de table Hive nommé *hivesampletable*. Dans cette session, vous allez créer une application .NET pour exécuter une tâche Hive afin de répertorier les tables Hive créées dans un cluster HDInsight. Pour plus d'informations sur l’utilisation de Hive, consultez [Utilisation de Hive avec HDInsight][hdinsight-use-hive].

Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight en utilisant le Kit de développement logiciel (SDK) :

- Installation du Kit de développement logiciel (SDK) HDInsight
- Création d'une application console
- Exécution de l'application


**Installation du Kit de développement logiciel (SDK) .NET HDInsight** : vous pouvez installer la dernière version du Kit de développement logiciel (SDK) publiée sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Création d'une application console Visual Studio**

1. Ouvrez Visual Studio.

2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriété</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valeur</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Catégorie</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modèles/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modèle</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Application console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td></tr>
</table>

4. Cliquez sur **OK** pour créer le projet.


5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package de bibliothèques**, puis cliquez sur **Console du gestionnaire de package**.

6. Exécutez la commande suivante dans la console pour installer le package :

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Cette commande ajoute des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

7. Dans l'**Explorateur de solutions**, double-cliquez sur **Program.cs** pour l'ouvrir.

8. Ajoutez les instructions **using** suivantes au début du fichier :

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Ajoutez la définition de fonction suivante à la classe. Cette fonction permet d'attendre la fin d'une tâche Hadoop :

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
10. Dans la fonction **Main()**, collez le code suivant :
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string clusterName = "<HDInsight cluster name>";
		string certFriendlyName = "<certificate friendly name>";		
		
	
	Vous devez définir toutes ces variables pour le programme. Vous pouvez obtenir l'ID d'abonnement Azure auprès de votre administrateur système.

	Pour plus d'informations sur le certificat, consultez [Créer et télécharger un certificat de gestion pour Azure][azure-certificate]. Pour configurer facilement les certificats, exécutez les cmdlets PowerShell **Get-AzurePublishSettingsFile** et **Import-AzurePublishSettingsFile**. Elles vont créer le certificat de gestion et le télécharger automatiquement. Après avoir exécuté ces cmdlets, ouvrez **certmgr.msc** sur le poste de travail puis recherchez le certificat en développant **Personnel** > **Certificats** Le certificat qui est créé par les cmdlets d’Azure PowerShell dispose des outils Azure pour les champs **Émis à** et **Émis par**.
	
11. Dans la fonction **Main()**, ajoutez le code suivant pour définir la tâche Hive :

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

	Vous pouvez également utiliser le paramètre **File** pour indiquer un fichier de script HiveQL dans HDFS, par exemple :

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

		
12. Dans la fonction **Main()**, ajoutez le code suivant pour créer un objet **JobSubmissionCertificateCredential** :
	
        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. Dans la fonction **Main()**, ajoutez le code suivant pour exécuter la tâche et attendre qu'elle soit terminée :

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);
		
14. Dans la fonction **Main()**, ajoutez le code suivant pour imprimer le résultat de la tâche Hive :

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**Exécution de l'application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. La sortie doit être :

	hivesampletable




##Étapes suivantes
Cet article vous a présenté différentes méthodes pour configurer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d'Azure HDInsight][hdinsight-get-started]
* [Approvisionnement des clusters HDInsight][hdinsight-provision]
* [Gestion de HDInsight à l'aide de PowerShell][hdinsight-admin-powershell]
* [Documentation de référence des cmdlets HDInsight][hdinsight-powershell-reference]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: http://manage.windowsazure.com/

[hdinsight-visual-studio-tools]: hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!--HONumber=54-->