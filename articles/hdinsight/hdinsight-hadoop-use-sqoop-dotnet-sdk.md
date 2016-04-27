<properties
	pageTitle="Utilisation de Hadoop Sqoop dans HDInsight | Microsoft Azure"
	description="Découvrez comment utiliser le Kit de développement logiciel (SDK) .NET HDInsight pour exécuter l’exportation et l’importation Sqoop entre un cluster Hadoop et une base de données SQL Azure."
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
	ms.date="04/06/2016"
	ms.author="jgao"/>

#Exécuter des tâches Sqoop à l’aide du Kit de développement logiciel (SDK) .NET pour Hadoop dans HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser le Kit de développement logiciel (SDK) .NET HDInsight pour exécuter des tâches Sqoop dans HDInsight afin d’effectuer des opérations d’importation et d’exportation entre un cluster HDInsight et une base de données SQL Azure ou SQL Server.

> [AZURE.NOTE] Les étapes décrites dans cet article peuvent être utilisées avec un cluster HDInsight Windows ou Linux. Toutefois, ces étapes fonctionnent uniquement à partir d’un client Windows. Utilisez le sélecteur d’onglet en haut de cet article pour choisir d’autres méthodes.

###Composants requis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Cluster Hadoop dans HDInsight**. Consultez [Création du cluster et de la base de données SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## Exécuter Sqoop à l’aide du Kit de développement logiciel (SDK) .NET

Le Kit de développement logiciel (SDK) HDInsight .NET fournit des bibliothèques clientes .NET, ce qui facilite l'utilisation des clusters HDInsight à partir de .NET. Dans cette section, vous allez créer une application console C# pour exporter la table hivesampletable vers la table de base de données SQL créée précédemment dans ce didacticiel.

L’exemple suivant utilise l’authentification utilisateur interactive. Pour utiliser l’authentification non interactive, consultez [Créer des applications HDInsight .NET d’authentification non interactive](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


**Pour envoyer une tâche Sqoop**

1. Créez une application console C# dans Visual Studio.
2. Dans la console du gestionnaire de package Visual Studio, exécutez la commande Nuget suivante pour importer le package.

		Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
        
3. Utilisez le code suivant dans le fichier Program.cs :

        using System;
        using System.Collections.Generic;
        using System.Security;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
                private static HDInsightJobManagementClient _hdiJobManagementClient;

                private static Guid SubscriptionId = new Guid("<Your Subscription ID>");
                private const string ResourceGroupName = "<Your Resource Group Name>";

                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";

                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

                    var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                    var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");

                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitSqoopJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();

                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };

                    if (username != null && password != null)
                        account.Id = username;

                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

                    var accessToken =
                        authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                            .AccessToken;

                    return new TokenCloudCredentials(accessToken);
                }

                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), creds.Token);
                }


                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";

                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";

                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;

                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string>{"/user/oozie/share/lib/sqoop/sqljdbc41.jar"}, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };

                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
        
4. Appuyez sur **F5** pour exécuter le programme.

##Étapes suivantes

Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

- [Utilisation d'Oozie avec HDInsight](hdinsight-use-oozie.md) : utilisez l’action Sqoop dans un flux de travail Oozie.
- [Analyse des données sur les retards de vol avec HDInsight](hdinsight-analyze-flight-delay-data.md) : utilisez Hive pour analyser des données sur les retards de vol, puis utilisez Sqoop pour exporter ces données vers une base de données SQL Azure.
- [Téléchargement de données vers HDInsight](hdinsight-upload-data.md) : découvrez d'autres méthodes pour télécharger des données vers HDInsight ou le stockage d'objets blob Azure.

<!---HONumber=AcomDC_0413_2016-->