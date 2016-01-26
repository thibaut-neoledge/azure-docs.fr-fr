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

	Data Lake Tools ne prend pas en charge la création de comptes Data Lake Analytics. Par conséquent, vous devez le créer avec le portail Azure, Azure PowerShell, le Kit de développement logiciel (SDK) .NET ou Azure CLI.

##Création d’une application de console

Dans ce didacticiel, vous allez traiter des journaux de recherche. Le journal de recherche peut être stocké dans Data Lake Store ou dans le stockage d’objets blobs Azure.

Un exemple de journal de recherche a été copié vers un conteneur d’objets blobs Azure public. Dans l’application, vous allez télécharger le fichier sur votre station de travail, puis télécharger le fichier dans le compte Data Lake Store par défaut.

**Pour créer une application**

1. Ouvrez Visual Studio.
2. Création d’une application console C#.
3. Ouvrez la console de gestion de package Nuget suivante, puis exécutez les commandes suivantes :

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.AnalyticsCatalog -Pre
        Install-Package Microsoft.Azure.Management.DataLake.AnalyticsJob -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreFileSystem -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
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

    >[AZURE.NOTE]Les conteneurs d’objets blobs Azure avec autorisations d’accès aux objets blobs publics ou aux conteneurs publics ne sont pas pris en charge actuellement.
       
       
5. Dans Program.cs, collez le code suivant :

        using System;
        using System.Security;
        using System.IO;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreFileSystem;
        using Microsoft.Azure.Management.DataLake.StoreFileSystem.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Common.Authentication.Factories;
        
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.Azure.Management.DataLake.AnalyticsJob;
        using Microsoft.Azure.Management.DataLake.AnalyticsJob.Models;
        
        using Microsoft.WindowsAzure.Storage.Blob;
        
        namespace data_lake_analytics_get_started
        {
            class Program
            {
        
                private const string AzureSubscriptionID = "<Your Azure Subscription ID>";
        
                private const string ResourceGroupName = "<Existing Azure Resource Group Name>"; //See the Get started using portal article
                private const string Location = "<Azure Data Center>";  //For example, EAST US 2
                private const string DataLakeStoreAccountName = "<Data Lake Store Account Name>"; // The application will create this account.
                private const string DataLakeAnalyticsAccountName = "<Data Lake Analytics Account Name>"; //The application will create this account.
        
                private const string LocalFolder = @"C:\tutorials\downloads\";  //local folder with write permission for file transferring.
        
                private static DataLakeStoreManagementClient _dataLakeStoreClient;
                private static DataLakeStoreFileSystemManagementClient _dataLakeStoreFileSystemClient;
        
                private static DataLakeAnalyticsManagementClient _dataLakeAnalyticsClient;
                private static DataLakeAnalyticsJobManagementClient _dataLakeAnalyticsJobClient;
        
                static void Main(string[] args)
                {
                    var subscriptionId = new Guid(AzureSubscriptionID);
                    var _credentials = GetAccessToken();
        
                    _credentials = GetCloudCredentials(_credentials, subscriptionId);
                    _dataLakeStoreClient = new DataLakeStoreManagementClient(_credentials);
                    _dataLakeStoreFileSystemClient = new DataLakeStoreFileSystemManagementClient(_credentials);
                    _dataLakeAnalyticsClient = new DataLakeAnalyticsManagementClient(_credentials);
                    _dataLakeAnalyticsJobClient = new DataLakeAnalyticsJobManagementClient(_credentials);
        
                    //=========================
                    Console.WriteLine("Creating the Azure Data Lake Store account ...");
                    var storeAccountParameters = new DataLakeStoreAccountCreateOrUpdateParameters();
                    storeAccountParameters.DataLakeStoreAccount = new Microsoft.Azure.Management.DataLake.Store.Models.DataLakeStoreAccount
                    {
                        Name = DataLakeStoreAccountName,
                        Location = Location
                    };
                    _dataLakeStoreClient.DataLakeStoreAccount.Create(ResourceGroupName, storeAccountParameters);
        
                    //=========================
                    Console.WriteLine("Preparing the source data file ...");
        
                    // Download the source file from a public Azure Blob container.
                    CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                    blob.DownloadToFile(LocalFolder + "SearchLog.tsv", System.IO.FileMode.Create);
        
                    // Create a folder in the default Data Lake Store account.
                    _dataLakeStoreFileSystemClient.FileSystem.Mkdirs("/Samples/Data/", DataLakeStoreAccountName, "777");
        
                    // Upload the source file to the default Data Lake Store account
                    var uploadParameters = new UploadParameters(LocalFolder + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv", DataLakeStoreAccountName, isOverwrite: true);
                    var uploader = new DataLakeStoreUploader(uploadParameters, new DataLakeStoreFrontEndAdapter(DataLakeStoreAccountName, _dataLakeStoreFileSystemClient));
                    uploader.Execute();
        
                    //=========================
                    Console.WriteLine("Creating the Data Lake Analytics account ...");
                    var analyticsAccountParameters = new DataLakeAnalyticsAccountCreateOrUpdateParameters();
                    analyticsAccountParameters.DataLakeAnalyticsAccount = new DataLakeAnalyticsAccount
                    {
                        Name = DataLakeAnalyticsAccountName,
                        Location = Location,
                        Properties = new DataLakeAnalyticsAccountProperties()
                    };
        
                    //create a DataLakeStoreAccount object, add it to the analytics client and then set it as the default ADL Store account
                    Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeStoreAccount storeAccountObject = new Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeStoreAccount();
                    storeAccountObject.Name = DataLakeStoreAccountName;
                    analyticsAccountParameters.DataLakeAnalyticsAccount.Properties.DataLakeStoreAccounts.Add(storeAccountObject);
                    analyticsAccountParameters.DataLakeAnalyticsAccount.Properties.DefaultDataLakeStoreAccount = DataLakeStoreAccountName;
        
                    _dataLakeAnalyticsClient.DataLakeAnalyticsAccount.Create(ResourceGroupName, analyticsAccountParameters);
        
                    //=========================
                    Console.WriteLine("Submitting the job ...");
        
                    USqlProperties uSQLProperties = new USqlProperties
                    {
                        Type = JobType.USql,
                        Script = File.ReadAllText("SampleUSQLScript.txt")
                    };
        
                    JobInformation jobParameters = new JobInformation
                    {
                        JobId = Guid.NewGuid(),
                        Name = "myfirstdatalakeanalyticsjob",
                        Properties = uSQLProperties,
                        Type = JobType.USql,
                        DegreeOfParallelism = 1,
                        Priority = 1
                    };
        
                    _dataLakeAnalyticsJobClient.Jobs.Create(ResourceGroupName, DataLakeAnalyticsAccountName, new JobInfoBuildOrCreateParameters { Job = jobParameters });
        
                    Console.WriteLine(" Downloading results ...");
                    FileCreateOpenAndAppendResponse beginOpenResponse = _dataLakeStoreFileSystemClient.FileSystem.BeginOpen("/Output/SearchLog-from-Data-Lake.csv", DataLakeStoreAccountName, new FileOpenParameters());
                    FileOpenResponse openResponse = _dataLakeStoreFileSystemClient.FileSystem.Open(beginOpenResponse.Location);
                    System.IO.File.WriteAllBytes(LocalFolder + "SearchLog-from-Data-Lake.csv", openResponse.FileContents);
        
                    Console.WriteLine("Done");
                }
        
                public static SubscriptionCloudCredentials GetAccessToken(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();
        
                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };
        
                    if (username != null && password != null)
                        account.Id = username;
        
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    return new TokenCloudCredentials(authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken);
                }
        
                public static SubscriptionCloudCredentials GetCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
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

<!---HONumber=AcomDC_0121_2016-->