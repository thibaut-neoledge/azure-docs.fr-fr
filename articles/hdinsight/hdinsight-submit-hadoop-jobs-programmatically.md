<properties
	pageTitle="Envoi de tâches Hadoop dans HDInsight | Microsoft Azure"
	description="Apprenez à envoyer des tâches Hadoop à Azure HDInsight Hadoop."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="jgao"/>

# Envoi de tâches Hadoop dans HDInsight

Découvrez comment utiliser Azure PowerShell pour envoyer des tâches MapReduce et Hive et comment utiliser le Kit de développement logiciel (SDK) HDInsight .NET pour envoyer des tâches MapReduce, des tâches de diffusion Hadoop et des tâches Hive.

> [AZURE.NOTE]Les étapes décrites dans cet article doivent être effectuées à partir d'un client Windows. Pour plus d'informations sur l'utilisation d'un client Linux, OS X ou Unix avec MapReduce, Hive ou Pig dans HDInsight, consultez les articles suivants et sélectionnez les liens **SSH** ou **Curl** de chacun :
>
> - [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
> - [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
> - [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

##Composants requis

Avant de commencer cet article, vous devez disposer des éléments suivants :

* Un **cluster Azure HDInsight**. Pour obtenir des instructions, consultez [Prendre en main HDInsight][hdinsight-get-started] ou [Créer des clusters Hadoop dans HDInsight][hdinsight-provision].
- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez [Installation et utilisation d'Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



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

	Le nom du compte de stockage est le compte de stockage Azure que vous avez indiqué pendant la création du cluster HDInsight. Le compte de stockage sert à héberger le conteneur d'objets blob utilisé comme système de fichiers par défaut du cluster HDInsight. Le nom du conteneur a généralement le même nom que celui du cluster HDInsight, sauf si vous indiquez un autre nom pendant la création du cluster.

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

**C:\\example\\data\\WordCountOutput*

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

**Pour envoyer la tâche MapReduce de décompte de mots**

1. Créez une application console C# dans Visual Studio.
2. À partir de la console du gestionnaire de package Nuget, exécutez la commande suivante.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Utilisez les instructions using suivantes dans le fichier Program.cs :

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Dans la fonction Main(), ajoutez le code suivant.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
	    HDInsightJobManagementClient _hdiJobManagementClient;

	    List<string> arguments = new List<string> { { "wasb:///example/data/gutenberg/davinci.txt" }, { "wasb:///example/data/WordCountOutput" } };
	
	    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
	    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
	
	    var parameters = new MapReduceJobSubmissionParameters
	    {
	        UserName = ExistingClusterUsername,
	        JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
	        JarClass = "wordcount",
	        Arguments = ConvertArgsToString(arguments)
	    };
	
	    System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
	    var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(parameters);
	    System.Console.WriteLine("Validating that the response is as expected...");
	    System.Console.WriteLine("Response status code is " + response.StatusCode);
	    System.Console.WriteLine("Validating the response object...");
	    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
	    Console.WriteLine("Press ENTER to continue ...");
	    Console.ReadLine();

4. Ajoutez la fonction d’assistance suivante à la classe.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Appuyez sur **F5** pour exécuter l'application.










##Envoi de tâches de diffusion en continu Hadoop avec le Kit de développement logiciel (SDK) .NET HDInsight
Les clusters HDInsight sont fournis avec un programme de diffusion en continu Hadoop pour le comptage de mots développé en C#. Le programme de mappage et le programme de réduction sont, respectivement, */example/apps/cat.exe* et */example/apps/wc.exe*. Dans cette session, vous allez apprendre à créer une application .NET pour exécuter l'exemple de comptage des mots.

Pour des informations détaillées sur la création d'une application .NET pour l'envoi de tâches MapReduce, consultez la page [Envoi de tâches MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight](#mapreduce-sdk).

Pour plus d'informations sur le développement et le déploiement de tâches de diffusion en continu Hadoop, consultez la page [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight][hdinsight-develop-streaming-jobs].

La procédure suivante ne fonctionne que sur les clusters HDInsight sous Windows. Le flux C# n’est pas encore pris en charge sur les clusters Linux. Toutefois, vous pouvez utiliser un programme .NET pour soumettre une tâche de flux écrite dans d’autres langages de programmation pris en charge par les clusters Linux, tels que le langage Python. Pour obtenir un exemple de diffusion en continu en langage Python, consultez [Développement de programmes de diffusion en continu Python pour HDInsight](hdinsight-hadoop-streaming-python.md).

**Pour envoyer la tâche MapReduce de décompte de mots**

1. Dans la console du gestionnaire de package Visual Studio, exécutez la commande Nuget suivante pour importer le package.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Utilisez les instructions using suivantes dans le fichier Program.cs :

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Dans la fonction Main(), ajoutez le code suivant.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";

        List<string> arguments = new List<string> { { "/example/apps/cat.exe" }, { "/example/apps/wc.exe" } };

        HDInsightJobManagementClient _hdiJobManagementClient;
        var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
        _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

        var parameters = new MapReduceStreamingJobSubmissionParameters
        {
            UserName = ExistingClusterUsername,
            File = ConvertArgsToString(arguments),
            Mapper = "cat.exe",
            Reducer = "wc.exe",
            Input = "/example/data/gutenberg/davinci.txt",
            Output = "/tutorials/wordcountstreaming/output"
        };

        System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
        var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceStreamingJob(parameters);
        System.Console.WriteLine("Validating that the response is as expected...");
        System.Console.WriteLine("Response status code is " + response.StatusCode);
        System.Console.WriteLine("Validating the response object...");
        System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadLine();

4. Ajoutez une fonction d’assistance à la classe.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Appuyez sur **F5** pour exécuter l'application.

##Envoi de tâches Hive avec le Kit de développement logiciel (SDK) .NET HDInsight
Les clusters HDInsight sont fournis avec un exemple de table Hive nommé *hivesampletable*. Dans cette session, vous allez créer une application .NET pour exécuter une tâche Hive afin de répertorier les tables Hive créées dans un cluster HDInsight. Pour plus d'informations sur l’utilisation de Hive, consultez [Utilisation de Hive avec HDInsight][hdinsight-use-hive].

Les procédures suivantes sont nécessaires pour créer un cluster HDInsight en utilisant le Kit de développement logiciel (SDK) :

- Installation du Kit de développement logiciel (SDK) HDInsight
- Création d'une application console
- Exécution de l'application


**Installation du Kit de développement logiciel (SDK) .NET HDInsight** : vous pouvez installer la dernière version du Kit de développement logiciel (SDK) publiée sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Pour créer une application console Visual Studio**

1. Ouvrir Visual Studio 2013 ou 2015

2. Créer un projet avec les paramètres suivants :

	|Propriété|Valeur|
	|--------|-----|
	|Modèle|Templates/Visual C#/Windows/Console Application|
	|Nom|SubmitHiveJob|

3. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**.
4. Exécutez la commande suivante dans la console pour installer les packages :

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

	Ces commandes ajoutent des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

5. Dans l’Explorateur de solutions, double-cliquez sur **Program.cs** pour l’ouvrir, collez le code suivant et fournissez des valeurs pour les variables :

		using System.Collections.Generic;
		using System.Linq;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHiveJob
		{
		    class Program
		    {
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private const string ExistingClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		
		        private const string ExistingClusterUsername = "<HDINSIGHT HTTP USER NAME>";  //The default name is admin.
		        private const string ExistingClusterPassword = "<HDINSIGHT HTTP USER PASSWORD>";
		
		        private static void Main(string[] args)
		        {
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                UserName = ExistingClusterUsername,
		                Query = "SHOW TABLES",
		                Defines = ConvertDefinesToString(defines),
		                Arguments = ConvertArgsToString(args)
		            };
		
		            System.Console.WriteLine("Submitting the Hive job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            System.Console.WriteLine("Press ENTER to continue ...");
		            System.Console.ReadLine();
		        }
		
		        private static string ConvertDefinesToString(Dictionary<string, string> defines)
		        {
		            if (defines.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
		        }
		        private static string ConvertArgsToString(List<string> args)
		        {
		            if (args.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&arg=", args.ToArray());
		        }
		    }
		}

6. Appuyez sur **F5** pour exécuter l'application.

##Exécution de travaux Hive à l'aide des outils HDInsight pour Visual Studio

Vous pouvez exécuter des requêtes Hive et Pig à l'aide des outils HDInsight pour Visual Studio Consultez [Prise en main des outils Hadoop de Visual Studio pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).


##Étapes suivantes
Cet article vous a présenté différentes méthodes pour créer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d'Azure HDInsight][hdinsight-get-started]
* [Créer des clusters Hadoop dans HDInsight][hdinsight-provision]
* [Gestion de HDInsight à l'aide de PowerShell][hdinsight-admin-powershell]
* [Documentation de référence des cmdlets HDInsight][hdinsight-powershell-reference]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=Oct15_HO3-->