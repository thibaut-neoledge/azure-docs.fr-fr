<properties 
   pageTitle="Didacticiel : prise en main d’Analytique Data Lake Azure à l’aide du kit de développement logiciel .NET | Azure" 
   description="Apprenez à utiliser le kit de développement .NET pour créer les comptes Data Lake Store, créez des travaux Analytique Data Lake et envoyez des travaux rédigés en U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/14/2016"
   ms.author="edmaca"/>

# Didacticiel : prise en main d’Analytique Data Lake Azure à l’aide du kit de développement logiciel .NET

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Découvrez comment utiliser le kit de développement logiciel .NET Azure pour créer des comptes Analytique Data Lake Azure, définir des travaux Analytique Data Lake dans [U-SQL](data-lake-analytics-u-sql-get-started.md), et envoyer des travaux à des comptes Analytique Data Lake. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

Dans ce didacticiel, vous allez développer une application de console C# qui contient un script U-SQL qui lit un fichier TSV (valeurs séparées par des tabulations) et le convertit en fichier CSV (valeurs séparées par des virgules). Pour suivre ce même didacticiel à l’aide d’autres outils pris en charge, cliquez sur les onglets en haut de cette section.

**Processus Analytique Data Lake de base :**

![Diagramme du flux de processus Analytique Data Lake Azure](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Créer un compte Analytique Data Lake.
2. Préparer les données source. Les travaux Analytique Data Lake peuvent lire les données depuis les comptes Azure Data Store Lake ou les comptes de stockage d’objets blobs Azure.   
3. Développer un script U-SQL.
4. Envoyer un travail (script U-SQL) vers le compte Analytique Data Lake. Le travail lit les données source, traite les données conformément au script U-SQL, puis enregistre la sortie dans un compte Data Lake Store ou un compte de stockage d’objets Blobs.

##Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Visual Studio 2015, Visual Studio 2013 mise à jour 4 ou Visual Studio 2012 avec Visual C+**.
- **Kit de développement logiciel (SDK) Microsoft Azure pour .NET version 2.5 ou ultérieure**. Installez-le avec [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Outils Data Lake pour Visual Studio](http://aka.ms/adltoolsvs)**. 
- **Un compte Analytique Data Lake**. Consultez [Créer un compte Analytique Data Lake Azure](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

	Data Lake Tools pour Visual Studio ne prend pas en charge la création de comptes Data Lake Analytics. Pour en créer un, utilisez le portail Azure, Azure PowerShell, le Kit de développement logiciel (SDK) .NET Azure ou l’interface de ligne de commande Azure.

##Création d’une application de console

Dans ce didacticiel, vous allez traiter des journaux de recherche. Le journal de recherche peut être stocké dans Data Lake Store ou dans le stockage d’objets blobs Azure.

Un exemple de journal de recherche a été copié vers un conteneur d’objets blobs Azure public. Dans l’application, vous allez télécharger le fichier sur votre station de travail, puis télécharger le fichier dans le compte Data Lake Store par défaut.

**Pour créer une application**

1. Ouvrez Visual Studio.
2. Création d’une application console C#.
3. Ouvrez la console de gestion de package Nuget suivante, puis exécutez les commandes suivantes :

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
        Install-Package WindowsAzure.Storage

4. Ajoutez un nouveau fichier au projet nommé **SampleUSQLScript.txt**, puis collez le script U-SQL suivant. Les travaux Analytique Data Lake sont écrits en langage U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main du langage U-SQL](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Ce script U-SQL lit le fichier de données source avec **Extractors.Tsv()**, puis crée un fichier csv à l’aide d’**Outputters.Csv()**.
    
    Dans le programme C#, vous devez préparer le dossier **/Samples/Data/SearchLog.tsv** fichier et le **/Output/**.
	
	Il est plus simple d’utiliser des chemins d’accès relatifs pour les fichiers stockés dans les comptes Data Lake par défaut. Vous pouvez également utiliser des chemins d’accès absolus. Par exemple :
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés. La syntaxe des fichiers stockés dans le compte de stockage Azure lié est la suivante :
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Les conteneurs d’objets blobs Azure avec autorisations d’accès aux objets blobs publics ou aux conteneurs publics ne sont pas pris en charge actuellement.
       
       
5. Dans Program.cs, collez le code suivant :

        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.WindowsAzure.Storage.Blob;

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeAnalyticsAccountManagementClient _adlaClient;
                private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
                private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlaAccountName;
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>"; // TODO: Replace this value with the name for a NEW Analytics account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";
                    
                    string localFolderPath = @"C:\local_path"; // TODO: Make sure this exists and contains the U-SQL script.
                    
                    // Authenticate the user
                    // For more information about applications and instructions on how to get a client ID, see: 
                    //   https://azure.microsoft.com/fr-FR/documentation/articles/resource-group-create-service-principal-portal/
                    var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                        "<APPLICATION-CLIENT-ID>", new Uri("https://<APPLICATION-REDIRECT-URI>")); // TODO: Replace bracketed values.
                    
                    SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.
                    
                    // Run sample scenarios
                    WaitForNewline("Authenticated.", "Creating NEW accounts.");
                    CreateAccounts();
                    WaitForNewline("Accounts created.", "Preparing the source data file.");

                    // Transfer the source file from a public Azure Blob container to Data Lake Store.
                    CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                    blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
                    UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
                    WaitForNewline("Source data file prepared.", "Submitting a job.");

                    // Submit the job
                    string jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
                    WaitForNewline("Job submitted.", "Waiting for job completion.");

                    // Wait for job completion
                    WaitForJob(jobId);
                    WaitForNewline("Job completed.", "Downloading job output.");

                    // Download job output
                    DownloadFile("/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
                    WaitForNewline("Job output downloaded.", "Deleting accounts.");

                    // Delete accounts
                    DeleteAccounts();
                    WaitForNewline("Accounts deleted. You can now exit.");
                }

                // Helper function to show status and wait for user input
                public static void WaitForNewline(string reason, string nextAction = "")
                {
                    if (!String.IsNullOrWhiteSpace(nextAction))
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                        Console.WriteLine(nextAction);
                    }
                    else
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                    }
                }

                // Authenticate the user with AAD through an interactive popup.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see: 
                //   https://azure.microsoft.com/fr-FR/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);

                    var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
                        PromptBehavior.Auto, UserIdentifier.AnyUser);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                // Authenticate the application with AAD through the application's secret key.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see: 
                //   https://azure.microsoft.com/fr-FR/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
                    var credential = new ClientCredential(appClientId, clientSecret);

                    var tokenAuthResult = authContext.AcquireToken(resource, credential);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                //Set up clients
                public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
                {
                    _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
                    _adlaClient.SubscriptionId = subscriptionId;

                    _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);
                    _adlaJobClient.SubscriptionId = subscriptionId;

                    _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(tokenCreds);
                    _adlaCatalogClient.SubscriptionId = subscriptionId;

                    _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                    _adlsClient.SubscriptionId = subscriptionId;

                    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
                    _adlsFileSystemClient.SubscriptionId = subscriptionId;
                }

                // Create accounts
                public static void CreateAccounts()
                {
                    // Create ADLS account
                    var adlsParameters = new DataLakeStoreAccount(location: _location);
                    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

                    // Create ADLA account
                    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(_adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
                    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: _adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
                    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: _location);
                    _adlaClient.Account.Create(_resourceGroupName, _adlaAccountName, adlaParameters);
                }

                // Delete accounts
                public static void DeleteAccounts()
                {
                    _adlaClient.Account.Delete(_resourceGroupName, _adlaAccountName);

                    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
                }

                // List all ADLA accounts within the subscription
                public static List<DataLakeAnalyticsAccount> ListAdlAnalyticsAccounts()
                {
                    var response = _adlaClient.Account.List(_adlaAccountName);
                    var accounts = new List<DataLakeAnalyticsAccount>(response);

                    while (response.NextPageLink != null)
                    {
                        response = _adlaClient.Account.ListNext(response.NextPageLink);
                        accounts.AddRange(response);
                    }

                    return accounts;
                }

                // List all ADLS accounts within the subscription
                public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
                {
                    var response = _adlsClient.Account.List(_adlsAccountName);
                    var accounts = new List<DataLakeStoreAccount>(response);
                    
                    while (response.NextPageLink != null)
                    {
                        response = _adlsClient.Account.ListNext(response.NextPageLink);
                        accounts.AddRange(response);
                    }

                    return accounts;
                }

                // Submit a U-SQL job by providing script contents.
                // Returns the job ID
                public static string SubmitJobByScript(string script, string jobName)
                {
                    var jobId = Guid.NewGuid().ToString();
                    var properties = new USqlJobProperties(script);
                    var parameters = new JobInformation(jobName, JobType.USql, properties);

                    var jobInfo = _adlaJobClient.Job.Create(jobId, parameters, _adlaAccountName);
                    
                    return jobId;
                }

                // Submit a U-SQL job by providing a path to the script
                public static string SubmitJobByPath(string scriptPath, string jobName)
                {
                    var script = File.ReadAllText(scriptPath);

                    var jobId = Guid.NewGuid().ToString();
                    var properties = new USqlJobProperties(script);
                    var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1000, degreeOfParallelism: 1);

                    var jobInfo = _adlaJobClient.Job.Create(jobId, parameters, _adlaAccountName);

                    return jobId;
                }

                public static JobResult WaitForJob(string jobId)
                {
                    var jobInfo = _adlaJobClient.Job.Get(jobId, _adlaAccountName);
                    while (jobInfo.State != JobState.Ended)
                    {
                        jobInfo = _adlaJobClient.Job.Get(jobId, _adlaAccountName);
                    }
                    return jobInfo.Result.Value;
                }

                // List jobs
                public static List<JobInformation> ListJobs()
                {
                    var response = _adlaJobClient.Job.List(_adlaAccountName);
                    var jobs = new List<JobInformation>(response);

                    while (response.NextPageLink != null)
                    {
                        response = _adlaJobClient.Job.ListNext(response.NextPageLink);
                        jobs.AddRange(response);
                    }

                    return jobs;
                }

                // Upload a file
                public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
                {
                    var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                    var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                    var uploader = new DataLakeStoreUploader(parameters, frontend);
                    uploader.Execute();
                }

                // Download file
                public static void DownloadFile(string srcPath, string destPath)
                {
                    var stream = _adlsFileSystemClient.FileSystem.Open(srcPath, _adlsAccountName);
                    var fileStream = new FileStream(destPath, FileMode.Create);

                    stream.CopyTo(fileStream);
                    fileStream.Close();
                    stream.Close();
                }
            }
        }

7. Appuyez sur **F5** pour exécuter l'application.

## Voir aussi

- Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
- Pour voir une requête plus complexe, consultez [Analyse de journaux de site web à l’aide d’Analytique Data Lake Azure](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec les outils Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour découvrir U-SQL, consultez les articles [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md) et [Référence sur le langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de l’analyse de données Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0224_2016-->