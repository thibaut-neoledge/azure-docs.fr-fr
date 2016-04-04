<properties
   pageTitle="Utilisation de Hadoop Pig avec .NET dans HDInsight | Microsoft Azure"
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
   ms.date="02/05/2016"
   ms.author="larryfr"/>

#Exécution de tâches Pig à l’aide du Kit de développement logiciel (SDK) .NET pour Hadoop dans HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit un exemple d’utilisation du Kit de développement logiciel (SDK) .NET pour Hadoop afin de soumettre des tâches Pig à un Hadoop sur un cluster HDInsight.

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation des clusters HDInsight à partir de .NET. Pig permet de créer des opérations MapReduce en modélisant une série de transformations de données. Vous apprendrez à utiliser une application de base C# pour soumettre un travail Pig sur un cluster HDInsight.

[AZURE.INCLUDE [azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Exécution de tâches Pig à l’aide du Kit de développement logiciel (SDK) .NET pour Hadoop dans HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk-v1.md)

##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux

* Visual Studio 2012, 2013 ou 2015

##<a id="certificate"></a>Création d'un certificat de gestion

Pour authentifier l’application sur Azure HDInsight, vous devez créer un certificat auto-signé, l’installer sur votre station de travail de développement et le télécharger sur votre abonnement Azure.

Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138).

> [AZURE.NOTE] Lors de la création du certificat, veillez à noter le nom convivial que vous utilisez, car il vous sera utile ultérieurement.

##<a id="subscriptionid"></a>Recherche de votre ID d'abonnement

Chaque abonnement Azure est identifié par une valeur GUID, appelée ID d’abonnement. Procédez comme suit pour trouver cette valeur.

1. Visitez le [portail Azure][preview-portal].

2. Dans la barre située sur la gauche du portail, sélectionnez __Parcourir tout__, puis sélectionnez __Abonnements__ dans le panneau __Parcourir__.

3. Dans les informations présentées sur le panneau __Abonnements__, recherchez l'abonnement que vous souhaitez utiliser et notez la valeur dans la colonne **ID d'abonnement**.

Enregistrez l’ID d’abonnement, car il sera utilisé ultérieurement.

##<a id="create"></a>Création de l'application

1. Ouvrez Visual Studio 2012 ou 2013
2. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet**.
3. Pour le nouveau projet, entrez ou sélectionnez les valeurs suivantes :

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

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

7. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir. Remplacez le code existant par ce qui suit.

        using System;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
        
        namespace HDInsightSubmitPigJobsDotNet
        {
            class Program
            {
                static void Main(string[] args)
                {
                    var ExistingClusterName = "<HDInsightClusterName>";
                    var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                    var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
                    var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
        
                    // The Pig Latin statements to run
                    string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                        "RESULT = order FREQUENCIES by COUNT desc;" +
                        "DUMP RESULT;";
        
        
                    HDInsightJobManagementClient _hdiJobManagementClient;
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    // Define the Pig job
                    var parameters = new PigJobSubmissionParameters()
                    {
                        Query = queryString,
                    };
        
                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                    Console.WriteLine("Press ENTER to continue ...");
                    Console.ReadLine();
                }
            }
        }

7. Appuyez sur **F5** pour lancer l’application.
8. Appuyez sur **Entrée** pour quitter l’application.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, le Kit de développement logiciel (SDK) .NET pour Hadoop vous permet de créer des applications .NET qui envoient des tâches Pig à un cluster HDInsight, de surveiller l’état du travail et d’en récupérer la sortie.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Pig sur HDInsight.

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0323_2016-->