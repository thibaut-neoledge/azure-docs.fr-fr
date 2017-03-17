---
title: "Gestion d’Azure Data Lake Analytics à l’aide du SDK Azure .NET | Microsoft Docs"
description: "Apprenez à gérer des travaux Data Lake Analytics, des sources de données, des utilisateurs. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: cf8873a3cc5067717edf586da209b05cef3092ff
ms.openlocfilehash: fc921785e5e4aae84982a348814c1760ddd6bb8c
ms.lasthandoff: 02/23/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gestion d’Azure Data Lake Analytics à l’aide du Kit de développement logiciel (SDK) Azure .NET
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Apprenez à gérer des comptes Azure Data Lake Analytics, des sources de données, des utilisateurs et des travaux à l’aide du Kit de développement logiciel (SDK) Azure .NET. Pour afficher les rubriques de gestion à l’aide d’autres outils, cliquez sur l’onglet de sélection ci-dessus.

**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="create-an-azure-resource-group"></a>Créer un groupe de ressources Azure
Si ce n’est déjà fait, vous devez créer un groupe de ressources Azure pour pouvoir créer vos composants Data Lake Analytics. Le code suivant montre comment créer un groupe de ressources :

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

Pour plus d’informations, consultez [Azure Resource Groups and Data Lake Analytics (Groupes de ressources Azure et Data Lake Analytics)](## Azure Resource Groups and Data Lake Analytics).


## <a name="connect-to-azure-data-lake"></a>Se connecter à Azure Data Lake
Les packages Nuget suivants doivent être installés :

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common
    Install-Package Microsoft.Azure.Common.Dependencies
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
    Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
    Install-Package Microsoft.WindowsAzure.Common
    Install-Package Microsoft.WindowsAzure.Common.Dependencies


Dans l’exemple de code suivant, la méthode Main montre comment se connecter à Azure et initialiser des objets de gestion de client Data Lake pour un compte Analytics et un compte de stockage.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string _SubID = "<Specify your Azure subscription ID>"; 
            private const string _ClientID = "1950a258-227b-4e31-a9cf-717495945fc2"; // An ID made availble for developers
            private const string _resourceGroupName ="<Specify your resource group name>";
            private static string _location = "East US 2"; // Specify your location

            // Replace 'common' with user's Azure Active Directory tenant ID or domain name, if needed.
            private const string _Domain = "common"; 

            // Data Lake client management objects
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobsClient;

            private static void Main(string[] args)
            {

                // Call logon method
                var creds = AuthenticateAzure(_Domain, _ClientID);

                // Initialize Data Lake management client objects, using
                // your credentials (creds). Initialize others as needed.
                _adlsClient = new DataLakeStoreAccountManagementClient(creds);
                _adlsClient.SubscriptionId = _SubID;
                
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = _SubID; 


                // Methods to create and manage Data Lake accounts and resources
                . . .

            }

            // Interactive logon
            public static ServiceClientCredentials AuthenticateAzure(string domainName, string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }
        }
    }

## <a name="data-lake-client-management-objects"></a>Objets de gestion de client Data Lake
Le kit de développement logiciel (SDK) Azure Data Lake comprend des objets de gestion de client qui vous permettent d’effectuer la majeure partie de votre programmation et qui appartiennent à ces deux espaces de noms :
* Microsoft.Azure.Management.DataLake.Analytics
* Microsoft.Azure.Management.DataLake.Store

Le tableau suivant répertorie ces objets et leurs variables, utilisés dans les exemples de cet article.

| Objets de gestion de client                  | Variable du code         |
| ----------------------------------------- | --------------------- |
| DataLakeStoreAccountManagementClient      | _adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | _adlaClient           |
| DataLakeStoreFileSystemManagementClient   | _adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | _adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | _adlaJobsClient       |

### <a name="data-lake-store-management-client-objects"></a>Objets de gestion de client Data Lake Store :
* DataLakeStoreAccountManagementClient : permet de créer et de gérer les magasins Data Lake.
* DataLakeFileSystemAccountManagementClient : permet d’effectuer des tâches de gestion de fichiers, telles que créer des dossiers et des fichiers, télécharger des fichiers, afficher une liste de fichiers, accéder aux ACL et aux informations d’identification, et ajouter des liens aux objets blob Azure Storage.

Même si vous pouvez créer des liens vers le stockage Azure à partir de Data Lake, vous n’avez pas accès à son contenu. Pour ce faire, vous devez utiliser les API du kit de développement logiciel (SDK) du stockage Azure. Vous pouvez, toutefois, exécuter des scripts U-SQL sur les objets blob du stockage Azure.

### <a name="data-lake-analytics-management-client-objects"></a>Objets de gestion de client Data Lake Analytics :
* DataLakeAnaylyticsAccountManagementClient : permet de créer et de créer des comptes Data Lake Analytics.
* DataLakeAnalyticsCatalogManagementClient : permet de configurer des bases de données SQL, ainsi que le schéma de création de listes.
* DataLakeAnalyticsJobManagementClient : permet de créer et de gérer des travaux U-SQL.

## <a name="create-accounts"></a>Création de comptes
Avant d'exécuter des travaux Data Lake Analytics, vous devez avoir un compte Data Lake Analytics. Contrairement à Azure HDInsight, vous ne payez pas pour un compte Analytics lorsque celui-ci n'exécute aucun travail.  Vous ne payez que le temps nécessaire à l’exécution d’un travail.  Pour plus d'informations, consultez [Présentation d'Azure Data Lake Analytics](data-lake-analytics-overview.md).

De plus, un compte Data Lake Analytics nécessite au moins un compte Data Lake Store.
  
### <a name="create-a-data-lake-store-account"></a>Créer un compte Data Lake Store
Le code suivant montre comment créer un compte Data Lake Store. Avant d’utiliser la méthode Create, vous devez définir ses paramètres en spécifiant un emplacement.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Créer un compte Data Lake Analytics
Le code suivant montre comment créer un compte Data Lake Analytics. La méthode Create de l’objet DataLakeAnalyticsAccountManagementClient accepte une collection de comptes Data Lake Store dans ses paramètres. Cette collection doit contenir des instances d’objets DataLakeStoreAccountInfo. Dans cet exemple, ces objets DataLakeStoreAccountInfo sont obtenus à partir d’une méthode d’assistance (AdlaFromAdlsStoreAccounts). Par ailleurs, comme certains comptes Data Lake Store d’un abonnement peuvent ne pas figurer dans un compte Data Lake Analytics, ce code compare les noms à une liste approuvée.

        // create analytics account
        public void CreateAnalyticsAccount(string acctname)
        {
            IEnumerable<DataLakeStoreAccountInfo> dlaInfos = AdlaFromAdlsStoreAccounts();

            var dlInfo = new DataLakeAnalyticsAccount()
            {
                DefaultDataLakeStoreAccount = _adlsAccountName,
                Location = _location,
                DataLakeStoreAccounts = dlaInfos.ToList<DataLakeStoreAccountInfo>()
            };

            _adlaClient.Account.Create(_resourceGroupName, acctname, dlInfo);
        }

        // A helper method to collect Data Lake Store account information to create an  
        // an analytics account, and also validates accounts before including.
        public IEnumerable<DataLakeStoreAccountInfo> AdlaFromAdlsStoreAccounts()
        {
            List<DataLakeStoreAccount> adlsAccounts = _adlsClient.Account.List().ToList();

            // Create a collection for approved accounts
            List<DataLakeStoreAccount> approvedAccounts = new List<DataLakeStoreAccount>();

            foreach (DataLakeStoreAccount dlsa in adlsAccounts)
            {
                // The IsApprovedDataStore method (not shown) 
                // evaluates a Data Lake store name.
                if (IsApprovedDataStore(dlsa.Name))
                {
                    approvedAccounts.Add(dlsa);
                }
            }

            return approvedAccounts.Select(element => new DataLakeStoreAccountInfo(element.Name));
        }

## <a name="manage-accounts"></a>Gérer les comptes

### <a name="list-data-lake-store-and-analytic-accounts"></a>Afficher la liste des comptes Data Lake Store et Data Lake Analytics
Le code suivant répertorie les comptes Data Lake Store dans un abonnement. La liste affichée ne contient pas toujours toutes les propriétés d’un objet et, dans certains cas, vous devez effectuer une opération Get sur l’objet.
            
    var adlsAccounts = _adlsClient.Account.List().ToList();
    Console.WriteLine($"You have {adlsAccounts.Count} Data Lake Store accounts.");
    for (int i = 0; i < adlsAccounts.Count; i++)
    {
        Console.WriteLine($"\t{adlsAccounts[i].Name}");
    }

    var adlaAccounts = _adlaClient.Account.List().ToList();
    Console.WriteLine($"\nYou have {adlaAccounts.Count} Data Lake Analytic accounts.");
    for (int j = 0; j < adlaAccounts.Count; j++)
    {
        Console.WriteLine($"\t{adlaAccounts[j].Name}");
    }
        

        
### <a name="get-an-account"></a>Obtenir un compte
Le code suivant utilise un DataLakeAnalyticsAccountManagementClient pour renvoyer un compte Data Lake Analytics, si ce dernier existe. 

    public DataLakeAnalyticsAccount GetDlaAccount(string strName)
    {
        DataLakeAnalyticsAccount dlaGet;
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            dlaGet = _adlaClient.Account.Get(_resourceGroupName, strName);
            Console.WriteLine($"{dlaGet.Name}\tCreated: {dlaGet.CreationTime}");
            return dlaGet;
        }
        else
        {
            return null;
        }

De même, vous pouvez utiliser DataLakeStoreAccountManagementClient (_adlsClient) de la même façon pour obtenir un compte Data Lake Store.        
### <a name="delete-an-account"></a>Supprimer un compte
Le code suivant supprime un compte Data Lake Analytics. 

    public void DeleteAnalyticsAccount(string strName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            _adlaClient.Account.Delete(_resourceGroupName, strName);
            Console.WriteLine($"{strName} Deleted");
        }
        else
        {
            Console.WriteLine($"{strName} does not exist.");
        }

    }

Vous pouvez également supprimer un compte Data Lake Store de la même façon qu’un DataLakeStoreAccountManagementClient.

### <a name="get-the-default-data-lake-store-account"></a>Afficher le compte Data Lake Store par défaut
Chaque compte Data Lake Analytics nécessite un compte Data Lake Store par défaut. Utilisez ce code pour identifier le compte par défaut d’un compte Analytics.

    public void GetDefaultDLStoreAccount(string DLAaccountName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, DLAaccountName))
        {
            DataLakeAnalyticsAccount dlaGet = _adlaClient.Account.Get(_resourceGroupName, DLAaccountName);
            Console.WriteLine($"{dlaGet.Name} default DL store account: {dlaGet.DefaultDataLakeStoreAccount}");
        }
    }

## <a name="manage-data-sources"></a>Gérer les sources de données
Data Lake Analytics prend actuellement en charge les sources de données suivantes :

* [Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/storage-introduction.md)

Lorsque vous créez un compte Analytics, vous devez désigner un compte Azure Data Lake Storage comme compte de stockage par défaut. Le compte Data Lake Store par défaut est utilisé pour stocker les métadonnées du travail et les journaux d'audit du travail. Après avoir créé un compte Analytics, vous pouvez ajouter plusieurs comptes Data Lake Storage et des liens vers des comptes de stockage Azure. 

### <a name="include-a-link-to-azure-storage-in-data-lake"></a>Inclure un lien vers le stockage Azure dans Data Lake
Vous pouvez créer des liens vers des blogs de stockage Azure dans votre environnement Data Lake. 

    string storageKey = "<paste the key value here>";

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(storageKey);            
    _adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-data-sources"></a>Afficher la liste des sources de données Data Lake
Le code suivant répertorie les comptes Data Lake Store et les comptes Data Lake Storage (pour le stockage Azure) d’un compte Data Lake Analytics.

    var sAccnts = _adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = _adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-a-file-to-a-data-lake-store-account"></a>Charger un fichier dans un compte Data Lake Store
Le code suivant utilise un DataLakeStoreFileSystemManagementClient pour charger un fichier local dans un compte Data Lake Store.

    bool force = true;
    string adlsAccnt = "Accounting";
    string srcFilePath = @"c:\DataLakeTemp\localData.csv";
    string dstFilePath = "/Reports/FY2016/2016data.csv";
    var parameters = new UploadParameters(srcFilePath, dstFilePath, adlsAccnt, isOverwrite: force);
    var frontend = new DataLakeStoreFrontEndAdapter(adlsAccnt, _adlsFileSystemClient);
    var uploader = new DataLakeStoreUploader(parameters, frontend);
    uploader.Execute();

### <a name="create-a-file-in-a-data-lake-store-account"></a>Créer un fichier dans un compte Data Lake Store
Outre le chargement de fichiers, vous pouvez facilement créer des fichiers par programmation dans votre compte Data Lake Store en vue de les analyser. Le code suivant écrit les quatre premières valeurs des 100 tableaux d’octets aléatoires dans un fichier .csv.

        MemoryStream azMem = new MemoryStream();
        StreamWriter sw = new StreamWriter(azMem, UTF8Encoding.UTF8);

        for (int i = 0; i < 100; i++)
        {
            byte[] gA = Guid.NewGuid().ToByteArray();
            string dataLine = string.Format($"{gA[0].ToString()},{gA[1].ToString()},{gA[2].ToString()},{gA[3].ToString()},{gA[4].ToString()}");
            sw.WriteLine(dataLine);
        }
        sw.Flush();
        azMem.Position = 0;

        _adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

        sw.Dispose();
        azMem.Dispose();

### <a name="copy-files-from-a-data-lake-store-account"></a>Copier des fichiers à partir d’un compte Azure Data Lake Store
Le code suivant montre comment effectuer des opérations de système de fichiers à l’aide d’un objet DataLakeFileSystemAccountManagementClient. Il copie les fichiers .csv du répertoire Samples/Data/AmbulanceData dans un répertoire local sur votre ordinateur.

    // This method takes the name of a Data Lake Store account,
    // and the the path to a directory in the account. In this

    public void CopyCSVFiles(string accnt, string fPath)
    {
        try
        {
            if (_adlsFileSystemClient.FileSystem.PathExists(accnt,fPath))
            {
                var fStatus = _adlsFileSystemClient.FileSystem.ListFileStatus(accnt, fPath);
                foreach (var fs in fStatus.FileStatuses.FileStatus)
                {
                    string localF = string.Empty;
                    if (fs.Type == Microsoft.Azure.Management.DataLake.Store.Models.FileType.FILE &&
                        fs.PathSuffix.Contains("csv"))
                    {
                        Stream fStream = _adlsFileSystemClient.FileSystem.Open(accnt, fPath + "/" + fs.PathSuffix);
                        localF = @"c:\DataLakeTemp\" + fs.PathSuffix;
                        FileStream localStream = new FileStream(localF, FileMode.Create);
                        fStream.CopyTo(localStream);

                    }
                    Console.WriteLine($"Copied {localF}.");
                }
            }
            else
            {
                Console.WriteLine($"File path {fPath} does not exist.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

### <a name="list-azure-storage-containers"></a>Afficher la liste des conteneurs de stockage Azure
Le code suivant répertorie les conteneurs du compte de stockage Azure spécifié.

    string DLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = _adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, DLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Vérifier les chemins des comptes de stockage Azure
Le code suivant vérifie si un compte de stockage Azure (storageAccntName) existe dans un compte Data Lake Analytics (analyticsAccountName) et si un conteneur (containerName) existe dans le compte de stockage Azure. 

    bool accountExists = _adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = _adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>Gérer le catalogue et les travaux
L’objet DataLakeAnalyticsCatalogManagementClient fournit des méthodes pour gérer la base de données SQL fournie pour chaque Azure Data Lake Store. L’objet DataLakeAnalyticsJobManagementClient fournit des méthodes pour envoyer et gérer les tâches exécutées sur la base de données avec des scripts U-SQL.

### <a name="list-databases-and-schemas"></a>Afficher la liste des bases de données et des schémas
Parmi les éléments dont vous pouvez afficher la liste, les plus courants sont les bases de données et leur schéma. Le code suivant obtient une collection de bases de données, puis indique le schéma pour chaque base de données.

    private void ListCatalogItems(string dlaAccountName)
    {
        var databases = _adlaCatalogClient.Catalog.ListDatabases(dlaAccountName);
        foreach (var db in databases)
        {
            Console.WriteLine($"Database: {db.Name}");
            Console.WriteLine(" - Schemas:");
            var schemas = _adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
            foreach (var schm in schemas)
            {
                Console.WriteLine($"\t{schm.Name}");
            }
        }
    }

Exécuté sur la base de données par défaut, cet exemple fournit la sortie suivante :

    Database: master
    - Schemas:
            dbo
            INFORMATION_SCHEMA
            sys
            usql

### <a name="list-table-columns"></a>Afficher la liste des colonnes d’une table
Le code suivant montre comment accéder à la base de données avec un objet DataLakeAnalyticsCatalogManagementClient pour répertorier les colonnes de la table spécifiée.

    var tbl = _adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = _adlaJobsClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>Afficher la liste des travaux ayant échoué
Le code suivant répertorie les informations sur les travaux qui ont échoué.

    var jobs = _adlaJobsClient.Job.List(_adlaAnalyticsAccountName);

    foreach (var j in jobs)
    {
        if (j.Result == JobResult.Failed)
        {
            Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
        }
    }
### <a name="reference-azure-storage-in-u-sql-scripts"></a>Référencer le stockage Azure dans les Scripts U-SQL
Le code suivant est le début d’un script U-SQL. Ce script commande la lecture des données d’un fichier sur un compte Data Lake Store : « / Samples/Data/SearchLog.tsv ».

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

Pour lire les données d’un objet blob sur un compte de stockage Azure lié, vous devez utiliser l’URL complète de cet objet blob au format suivant :

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/<path to source>

Par exemple, si un fichier source (SearchLog.tsv) est stocké dans un conteneur d’objets blob nommé « samples » dans le compte de stockage « contoso_33 », le chemin d’accès à l’instruction FROM est :

    FROM: "wasb://samples@constoso_33.blob.core.windows.net/SearchLog.tsv"

## <a name="azure-resource-groups-and-data-lake-analytics"></a>Groupes de ressources Azure et Data Lake Analytics
Les applications sont généralement constituées de nombreux composants, par exemple une application web, base de données, serveur de base de données, stockage et services tiers. Azure Resource Manager vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe. Pour plus d'informations, consultez [Présentation d'Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

Un service Data Lake Analytics peut inclure les composants suivants :

* Compte Azure Data Lake Analytics
* Compte Azure Data Lake Storage par défaut requis
* Un ou plusieurs comptes Azure Data Lake Analytics
* Un ou plusieurs comptes Azure Data Lake Store (un au minimum)
* Comptes Azure Data Lake Storage liés supplémentaires
* Comptes Azure Storage supplémentaires

Vous pouvez créer tous ces composants dans un groupe Resource Management pour les rendre plus facile à gérer.

![Compte et stockage Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un compte Data Lake Analytics et les compte de stockage dépendants doivent se trouver dans le même centre de données Azure.
Le groupe Resource Management peut cependant se trouver dans un autre centre de données.  

## <a name="see-also"></a>Voir aussi
* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
* [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

