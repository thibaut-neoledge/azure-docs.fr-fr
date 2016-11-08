---
title: Utilisation de Hadoop Pig avec .NET dans HDInsight | Microsoft Docs
description: Apprenez à utiliser le Kit de développement logiciel (SDK) .NET pour Hadoop afin de soumettre des tâches Pig vers Hadoop sur HDInsight.
services: hdinsight
documentationcenter: .net
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2016
ms.author: larryfr

---
# Exécution de tâches Pig à l’aide du Kit de développement logiciel (SDK) .NET pour Hadoop dans HDInsight
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit un exemple d’utilisation du Kit de développement logiciel (SDK) .NET pour Hadoop afin de soumettre des tâches Pig à un Hadoop sur un cluster HDInsight.

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation des clusters HDInsight à partir de .NET. Pig permet de créer des opérations MapReduce en modélisant une série de transformations de données. Vous apprendrez à utiliser une application de base C# pour soumettre un travail Pig sur un cluster HDInsight.

[!INCLUDE [azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Exécution de tâches Pig à l’aide du Kit de développement logiciel (SDK) .NET pour Hadoop dans HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk-v1.md)

## Composants requis
Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux.
* Visual Studio 2012, 2013 ou 2015.

## Création de l'application
Le Kit de développement logiciel (SDK) HDInsight .NET fournit des bibliothèques clientes .NET, ce qui facilite l'utilisation des clusters HDInsight à partir de .NET.

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
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
7. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir. Remplacez le code existant par ce qui suit.
   
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitPigJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
                                    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                    RESULT = order FREQUENCIES by COUNT desc;
                                    DUMP RESULT;"
                    };
   
                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
8. Appuyez sur **F5** pour lancer l’application.
9. Appuyez sur **Entrée** pour quitter l’application.

## Résumé
Comme vous pouvez le voir, le Kit de développement logiciel (SDK) .NET pour Hadoop vous permet de créer des applications .NET qui envoient des tâches Pig à un cluster HDInsight, et de surveiller l’état du travail.

## Étapes suivantes
Pour obtenir des informations générales sur Pig sur HDInsight.

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0914_2016-->