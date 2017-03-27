---
title: "Envoi de travaux MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight | Microsoft Docs"
description: "Apprenez à envoyer des travaux MapReduce à Azure HDInsight Hadoop à l’aide du Kit de développement logiciel (SDK) .NET HDInsight."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 03f3898182d405bced8ad41b281e2ce2037b8343
ms.lasthandoff: 03/04/2017


---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Exécuter des travaux MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Apprenez à envoyer des travaux MapReduce à l’aide du Kit de développement logiciel (SDK) .NET HDInsight. Les clusters HDInsight comportent un fichier jar qui contient des exemples MapReduce. Le fichier jar est le suivant : */example/jars/hadoop-mapreduce-examples.jar*.  Un des exemples est *wordcount*. Vous développez une application de console C# pour envoyer une tâche wordcount.  Le travail lit le fichier */example/data/gutenberg/davinci.txt* et renvoie les résultats vers */example/data/davinciwordcount*.  Si vous souhaitez réexécuter l’application, vous devez nettoyer le dossier de sortie.

> [!NOTE]
> Les étapes décrites dans cet article doivent être effectuées à partir d'un client Windows. Pour plus d’informations sur l’utilisation d’un client Linux, OS X ou Unix pour utiliser Hive, utilisez le sélecteur d’onglet en haut de l’article.
> 
> 

## <a name="prerequisites"></a>Composants requis
Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Cluster Hadoop dans HDInsight**. Consultez [Prise en main de Hadoop sous Linux dans HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Envoi de tâches MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight
Le Kit de développement logiciel (SDK) HDInsight .NET fournit des bibliothèques clientes .NET, ce qui facilite l'utilisation des clusters HDInsight à partir de .NET. 

**Pour soumettre les travaux**

1. Créez une application console C# dans Visual Studio.
2. À partir de la console du gestionnaire de package Nuget, exécutez la commande suivante.
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Utilisez le code suivant :
   
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
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
   
                private const string DefaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitMRJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitMRJob()
                {
                    List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
   
                    var paras = new MapReduceJobSubmissionParameters
                    {
                        JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                        JarClass = "wordcount",
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the MR job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
4. Appuyez sur **F5** pour exécuter l'application.

Pour réexécuter le travail, vous devez modifier le nom du dossier de sortie du travail. Dans l’exemple, il s’agit de « /example/data/davinciwordcount ».

Une fois le travail terminé, le résultat est vide. Pour afficher le résultat du travail MapReduce, utilisez le portail Azure pour explorer le conteneur de stockage par défaut dans le stockage Blob.  Le nom du fichier est : « part-r-00000 ».

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté différentes méthodes pour créer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* Pour soumettre un travail Hive, consultez [Exécuter des requêtes Hive avec le Kit de développement logiciel (SDK) .NET HDInsight](hdinsight-hadoop-use-hive-dotnet-sdk.md).
* Pour créer des clusters HDInsight, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Pour gérer des clusters HDInsight, consultez [Gestion de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-administer-use-portal-linux.md).
* Pour découvrir le Kit de développement logiciel (SDK) .NET HDInsight, consultez [Référence du Kit de développement logiciel (SDK) .NET de HDInsight](https://msdn.microsoft.com/library/mt271028.aspx).
* Pour l’authentification non interactive sur Azure, consultez [Créer des applications HDInsight .NET d’authentification non interactive](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


