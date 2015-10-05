<properties
   pageTitle="Utilisation de Hadoop Pig avec .NET dans HDInsight | Microsoft Azure"
   description="Apprenez à utiliser le Kit de développement logiciel (SDK) .NET pour Hadoop afin de soumettre des tâches Pig vers Hadoop sur HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Exécution de tâches Pig à l’aide du Kit de développement logiciel (SDK) .NET pour Hadoop dans HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit un exemple d’utilisation du Kit de développement logiciel (SDK) .NET pour Hadoop afin de soumettre des tâches Pig à un Hadoop sur un cluster HDInsight.

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation des clusters HDInsight à partir de .NET. Pig permet de créer des opérations MapReduce en modélisant une série de transformations de données. Vous apprendrez à utiliser une application de base C# pour soumettre un travail Pig sur un cluster HDInsight.

[AZURE.INCLUDE [azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Exécution de tâches Pig à l’aide du Kit de développement logiciel (SDK) .NET pour Hadoop dans HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk-v1.md)

##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux

* Visual Studio 2012 ou 2013

##<a id="certificate"></a>Création d'un certificat de gestion

Pour authentifier l’application sur Azure HDInsight, vous devez créer un certificat auto-signé, l’installer sur votre station de travail de développement et le télécharger sur votre abonnement Azure.

Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138).

> [AZURE.NOTE] Lors de la création du certificat, veillez à noter le nom convivial que vous utilisez, car il vous sera utile ultérieurement.

##<a id="subscriptionid"></a>Recherche de votre ID d'abonnement

Chaque abonnement Azure est identifié par une valeur GUID, appelée ID d’abonnement. Procédez comme suit pour trouver cette valeur.

1. Visitez la [version préliminaire du portail Azure][version préliminaire du portail].

2. Dans la barre située sur la gauche du portail, sélectionnez __Parcourir tout__, puis sélectionnez __Abonnements__ dans le panneau __Parcourir__.

3. Dans les informations présentées sur le panneau __Abonnements__, recherchez l'abonnement que vous souhaitez utiliser et notez la valeur dans la colonne **ID d'abonnement**.

Enregistrez l’ID d’abonnement, car il sera utilisé ultérieurement.

##<a id="create"></a>Création de l'application

1. Ouvrez Visual Studio 2012 ou 2013

2. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet**.

3. Pour le nouveau projet, entrez ou sélectionnez les valeurs suivantes :

	<table>
<tr>
<th>Propriété</th>
<th>Valeur</th>
</tr>
<tr>
<th>Catégorie</th>
<th>Modèles/Visual C#/Windows</th>
</tr>
<tr>
<th>Modèle</th>
<th>Application console</th>
</tr>
<tr>
<th>Nom</th>
<th>SubmitPigJob</th>
</tr>
</table>

4. Cliquez sur **OK** pour créer le projet.

5. À partir du menu **Outils**, sélectionnez **Gestionnaire de package de bibliothèque** ou **Gestionnaire de package Nuget**, puis sélectionnez **Console du gestionnaire de package**.

6. Exécutez la commande suivante dans la console pour installer les packages du Kit de développement logiciel (SDK) .NET.

		Install-Package Microsoft.Windowsazure.Management.HDInsight

7. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir. Remplacez le code existant par ce qui suit.

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

		namespace SubmitPigJob
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            // Get the subscription ID
		            string subscriptionID = PromptForInput("Enter your Azure Subscription ID:");

		            // Get the certificate name
		            string certFriendlyName = PromptForInput("Enter the management certificate name:");

		            // Get the cluster name
		            string clusterName = PromptForInput("Enter the HDInsight cluster name:");

		            // Set the folder that job status is written to
		            string statusFolderName = @"/tutorials/usepig/status";

		            // The Pig Latin statements to run
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
		            JobCreationResults mrJobResults = jobClient.CreatePigJob		(myJobDefinition);

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

		        private static string PromptForInput(string message)
		        {
		            Console.WriteLine(message);
		            return Console.ReadLine();
		        }
		    }
		}


7. Enregistrez le fichier.

##<a id="run"></a>Exécution de l'application

Appuyez sur **F5** pour lancer l’application. Lorsque vous y êtes invité, entrez l’**ID d’abonnement**, le **nom convivial du certificat** et le **nom du cluster HDInsight**. L’application génère plusieurs lignes d’informations lors de son exécution, qui se terminent par quelque chose de similaire à ce qui suit.

```
----- The Pig job output log.
(TRACE,816)
(DEBUG,434)
(INFO,96)
(WARN,11)
(ERROR,6)
(FATAL,2)

----- Press ENTER to continue.
```

Appuyez sur **Entrée** pour quitter l’application.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, le Kit de développement logiciel (SDK) .NET pour Hadoop vous permet de créer des applications .NET qui envoient des tâches Pig à un cluster HDInsight, de surveiller l’état du travail et d’en récupérer la sortie.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Pig sur HDInsight.

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
[version préliminaire du portail]: https://portal.azure.com/

<!---HONumber=Sept15_HO4-->