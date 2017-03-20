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
ms.date: 03/3/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: bed6fa355f3b32bb53aee002e34ca61f2ea2aa5b
ms.lasthandoff: 03/06/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gestion d’Azure Data Lake Analytics à l’aide du SDK Azure .NET
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Apprenez à gérer des comptes Azure Data Lake Analytics, des sources de données, des utilisateurs et des travaux à l’aide du kit SDK Azure .NET. Pour afficher les rubriques de gestion à l’aide d’autres outils, cliquez sur l’onglet de sélection ci-dessus.

## <a name="prerequisites"></a>Composants requis

* **Visual Studio 2015, Visual Studio 2013 mise à jour 4 ou Visual Studio 2012 avec Visual C++ installé**.
* **Kit SDK Microsoft Azure pour .NET version 2.5 ou ultérieure**.  Installez-le avec [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* **Packages Nuget exigés**

### <a name="install-nuget-packages"></a>Installer les packages NuGet
   
   1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.
   2. Dans l’onglet **Gestionnaire de package NuGet**, vérifiez que **Source du package** a la valeur **nuget.org** et que la case **Inclure la version préliminaire** est cochée.

   3. Recherchez et installez les packages NuGet suivants :

    - Microsoft.Rest.ClientRuntime.Azure.Authentication - Ce didacticiel utilise la version V2.2.12
    - Microsoft.Azure.Management.DataLake.Analytics - Ce didacticiel utilise la préversion V2.1.0
    - Microsoft.Azure.Management.DataLake.Store - Ce didacticiel utilise la préversion V2.1.0

   4. Fermez le **Gestionnaire de package NuGet**.

## <a name="client-management-objects"></a>Objets de gestion de client
Les API Azure Data Lake Analytics et Azure Data Lake Store comprennent des jeux d’objets de gestion de client à l’aide desquels vous pouvez effectuer la majeure partie de votre programmation. Ces objets appartiennent aux deux espaces de noms suivants :
* Microsoft.Azure.Management.DataLake.Analytics
* Microsoft.Azure.Management.DataLake.Store

Le tableau suivant répertorie les objets de gestion de client et les variables utilisées dans les exemples de code tout au long de cet article.

| Objets de gestion de client                  | Variable du code        |
| ----------------------------------------- | -------------------- |
| DataLakeStoreAccountManagementClient      | adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | adlaClient           |
| DataLakeStoreFileSystemManagementClient   | adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | adlaJobClient        |

### <a name="data-lake-store-management-client-objects"></a>Objets de gestion de client Data Lake Store :
* DataLakeStoreAccountManagementClient : permet de créer et de gérer les comptes Data Lake Store.
* DataLakeFileSystemAccountManagementClient : permet d’effectuer des tâches de gestion de fichiers, telles que créer des dossiers et des fichiers, télécharger des fichiers, afficher une liste de fichiers, accéder aux ACL et aux informations d’identification, et ajouter des liens aux objets blob Azure Storage.

Même si vous pouvez créer des liens vers le stockage Azure à partir de Data Lake, vous n’avez pas accès à son contenu. Pour ce faire, vous devez utiliser les API du kit SDK de Stockage Azure. Vous pouvez, toutefois, exécuter des scripts U-SQL sur les objets blob du stockage Azure.

### <a name="data-lake-analytics-management-client-objects"></a>Objets de gestion de client Data Lake Analytics :
* DataLakeAnalyticsAccountManagementClient : permet de créer et de gérer les comptes Data Lake Analytics.
* DataLakeAnalyticsCatalogManagementClient : permet d’explorer les éléments de catalogue dans Data Lake Analytics.
* DataLakeAnalyticsJobManagementClient : permet de soumettre et de gérer les travaux dans Data Lake Analytics.

### <a name="example"></a>Exemple

Cet exemple initialise des objets de gestion de client à l’aide d’informations d’identification (creds), obtenues par la méthode d’authentification de votre choix. Les différentes méthodes d’authentification sont décrites ci-après. 

    // Only the Data Lake Analytics and Data Lake Store  
    // objects need a subscription ID.
    adlsClient = new DataLakeStoreAccountManagementClient(creds);
    adlsClient.SubscriptionId = <Subscription-ID>;

    adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
    adlaClient.SubscriptionId = <Subscription-ID>;

    adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);
    adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(creds);
    adlaJobClient = new DataLakeAnalyticsJobManagementClient(creds);


    // Methods to create and manage Data Lake Analytics
    . . .

## <a name="authenticate-and-connect-to-azure-data-lake-analytics"></a>S’authentifier et se connecter à Azure Data Lake Analytics
Pour vous connecter à Azure Data Lake Analytics, plusieurs options s’offrent à vous.

### <a name="interactive-with-provided-credentials"></a>Authentification interactive avec informations d’identification fournies
La méthode d’authentification la plus simple consiste à demander à l’utilisateur de fournir des informations d’identification, comme un nom d’utilisateur et un mot de passe ou un code PIN. L’extrait suivant présente cette méthode.

    // User login via interactive popup
    // Use the client ID of an existing AAD "native nlient" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Tenant ID>"; // Replace this string with the user's Azure Active Directory tenant ID.
    var clientId = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(_tenantId, activeDirectoryClientSettings).Result;

Nous vous recommandons de créer votre propre application et votre propre principal de service dans votre locataire Azure Active Directory, puis d’utiliser l’ID client de cette application plutôt que l’exemple d’ID utilisé ici.

### <a name="non-interactive-with-a-client-secret"></a>Authentification non interactive avec une clé secrète client
Vous pouvez utiliser l’extrait de code suivant pour authentifier votre application de manière non interactive, en utilisant la clé secrète du client pour une application/un principal de service. Utilisez cette option d’authentification avec une [« application web » Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md) existante.

    // Service principal / application authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(tenantId, clientCredential).Result;

### <a name="non-interactive-with-a-service-principal"></a>Authentification non interactive avec un principal de service
Ou vous pouvez l’utiliser pour authentifier votre application en mode non interactif, en utilisant le certificat pour une application / un principal de service. Utilisez cette option d’authentification avec une [« application web » Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md) existante.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(tenantId, clientAssertionCertificate).Result;

## <a name="create-accounts"></a>Création de comptes
Avant d'exécuter des travaux Data Lake Analytics, vous devez avoir un compte Data Lake Analytics. De plus, un compte Data Lake Analytics nécessite au moins un compte Data Lake Store. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Data Lake Analytics](data-lake-analytics-overview.md).

### <a name="create-an-azure-resource-group"></a>Créer un groupe de ressources Azure
Si ce n’est déjà fait, vous devez créer un groupe de ressources Azure pour pouvoir créer vos composants Data Lake Analytics. Vous avez besoin de vos informations d’identification d’authentification, d’un ID d’abonnement et d’un emplacement. Le code suivant montre comment créer un groupe de ressources :

    string rgName == "<value>"; // specify name for the new resrouce group
    var resourceManagementClient = new ResourceManagementClient(credential) { SubscriptionId = subscriptionId };
    var resourceGroup = new ResourceGroup { Location = location };
    resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rgName);

Pour plus d’informations, consultez [Groupes de ressources Azure et Data Lake Analytics](#Azure-Resource-Groups-and-Data-Lake-Analytics).


### <a name="create-a-data-lake-store-account"></a>Créer un compte Data Lake Store
Le code suivant montre comment créer un compte Data Lake Store. Avant d’utiliser la méthode Create, vous devez définir ses paramètres en spécifiant un emplacement.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Créer un compte Data Lake Analytics
Le code suivant montre comment créer un compte Data Lake Analytics à l’aide de la méthode asynchrone. La méthode CreateAsync accepte une collection de comptes Data Lake Store comme paramètre. Cette collection doit contenir des instances d’objets DataLakeStoreAccountInfo. Dans cet exemple, ces objets DataLakeStoreAccountInfo sont obtenus à partir d’une méthode d’assistance (AdlaFromAdlsStoreAccounts).

Pour tout compte Data Lake Analytics, vous devez uniquement inclure les comptes Data Lake Store dont vous avez besoin pour effectuer les opérations d’analytique nécessaires. L’un de ces comptes Data Lake Store doit être le compte Data Lake Store par défaut.

    try
    {
        var adlaAccount = new DataLakeAnalyticsAccount()
        {
            DefaultDataLakeStoreAccount = “Accounting”,
            Location = _location,
            DataLakeStoreAccounts = new DataLakeStoreAccountInfo[]{
                new DataLakeStoreAccountInfo(“Expenditures”),
                new DataLakeStoreAccountInfo(“Accounting”)
            }
        };
        adlaClient.Account.Create(_resourceGroupName, newAccountName, adlaAccount);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }

## <a name="manage-accounts"></a>Gérer les comptes

### <a name="list-data-lake-store-and-data-lake-analytics-accounts"></a>Répertorier les comptes Data Lake Store et Data Lake Analytics
Le code suivant répertorie les comptes Data Lake Store dans un abonnement. La liste affichée ne contient pas toujours toutes les propriétés d’un objet et, dans certains cas, vous devez effectuer une opération Get sur l’objet.

    var adlsAccounts = adlsClient.Account.List().ToList();
    foreach (var adls in adlsAccounts)
    {
        Console.WriteLine($"\t{adls.Name});

    }

    var adlaAccounts = adlaClient.Account.List().ToList();
    for (var adla in AdlaAccounts)
    {
        Console.WriteLine($"\t{adla.Name}");
    }



### <a name="get-an-account"></a>Obtenir un compte
Le code suivant utilise un DataLakeAnalyticsAccountManagementClient pour obtenir un compte Data Lake Analytics. Il commence par vérifier que le compte existe.

    DataLakeAnalyticsAccount adlaGet;
    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name}\tCreated: {adlaGet.CreationTime}");
    }

Vous pouvez utiliser DataLakeStoreAccountManagementClient (adlsClient) de la même façon pour obtenir un compte Data Lake Store.

### <a name="delete-an-account"></a>Supprimer un compte
Le code suivant supprime un compte Data Lake Analytics (si celui-ci existe).

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaClient.Account.Delete(_resourceGroupName, accountName);
        Console.WriteLine($"{accountName} Deleted");
    }
    else
    {
        Console.WriteLine($"{accountName} does not exist.");
    }

Vous pouvez également supprimer un compte Data Lake Store de la même façon qu’un DataLakeStoreAccountManagementClient.

### <a name="get-the-default-data-lake-store-account"></a>Afficher le compte Data Lake Store par défaut
Chaque compte Data Lake Analytics nécessite un compte Data Lake Store par défaut. Utilisez ce code pour identifier le compte par défaut d’un compte Analytics.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        DataLakeAnalyticsAccount adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name} default DL store account: {adlaGet.DefaultDataLakeStoreAccount}");
    }


## <a name="manage-data-sources"></a>Gérer les sources de données
Data Lake Analytics prend actuellement en charge les sources de données suivantes :

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Compte Stockage Azure](../storage/storage-introduction.md)

Quand vous créez un compte Analytics, vous devez désigner un compte Azure Data Lake Store comme compte Data Lake Store par défaut. Le compte Data Lake Store par défaut est utilisé pour stocker les métadonnées du travail et les journaux d'audit du travail. Après avoir créé un compte Analytics, vous pouvez ajouter d’autres comptes Data Lake Store et des liens vers des comptes Stockage Azure (objets blob).

### <a name="link-to-an-azure-storage-account-from-a-data-lake-analytics-account"></a>Lier un compte Data Lake Analytics à un compte Stockage Azure
Vous pouvez créer des liens vers des comptes Stockage Azure.

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(<storage key value>);            
    adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-store-data-sources"></a>Afficher la liste des sources de données Data Lake Store
Le code suivant répertorie les comptes Data Lake Store et les comptes Stockage Azure utilisés pour un compte Data Lake Analytics spécifié.

    var sAccnts = adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Azure Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-and-download-folders-and-files"></a>Charger et télécharger des dossiers et des fichiers
Vous pouvez utiliser l’objet de gestion de client du système de fichiers Data Lake Store pour charger et télécharger des fichiers ou des dossiers à partir d’Azure sur votre ordinateur local. Pour cela, utilisez les méthodes suivantes :

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

Le premier paramètre de ces méthodes est le nom du compte Data Lake Store, suivi des paramètres du chemin source et du chemin de destination.

L’exemple suivant montre comment télécharger un dossier dans le Data Lake Store.


    try
    {
        if (adlsFileSystemClient.FileSystem.PathExists(account, sourcePath))
        {
            adlsFileSystemClient.FileSystem.DownloadFolder(account, sourcePath, destinationPath);
        }
        else
        {
            Console.WriteLine("Path does not exist");
        }
    }
    catch (IOException ioex)
    {
        Console.WriteLine(ioex.Message);
    }


### <a name="create-a-file-in-a-data-lake-store-account"></a>Créer un fichier dans un compte Data Lake Store
Vous pouvez utiliser les opérations d’E/S du .NET Framework pour créer le contenu d’un fichier dans un Data Lake Store. Le code suivant écrit les quatre premières valeurs des 100 tableaux d’octets aléatoires dans un fichier .csv.

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

    adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

    sw.Dispose();
    azMem.Dispose();

### <a name="list-blob-containers-of-an-azure-storage-account"></a>Afficher la liste des conteneurs d’objets blob d’un compte Stockage Azure
Le code suivant répertorie les conteneurs d’un compte Stockage Azure spécifié.

    string ADLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, ADLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Vérifier les chemins des comptes de stockage Azure
Le code suivant vérifie si un compte de stockage Azure (storageAccntName) existe dans un compte Data Lake Analytics (analyticsAccountName) et si un conteneur (containerName) existe dans le compte de stockage Azure.

    bool accountExists = adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>Gérer le catalogue et les travaux
L’objet DataLakeAnalyticsCatalogManagementClient fournit des méthodes pour gérer la base de données SQL fournie pour chaque Azure Data Lake Store. L’objet DataLakeAnalyticsJobManagementClient fournit des méthodes pour envoyer et gérer les tâches exécutées sur la base de données avec des scripts U-SQL.

### <a name="list-databases-and-schemas"></a>Afficher la liste des bases de données et des schémas
Parmi les éléments dont vous pouvez afficher la liste, les plus courants sont les bases de données et leur schéma. Le code suivant obtient une collection de bases de données, puis indique le schéma pour chaque base de données.

    var databases = adlaCatalogClient.Catalog.ListDatabases(adlaAccountName);
    foreach (var db in databases)
    {
        Console.WriteLine($"Database: {db.Name}");
        Console.WriteLine(" - Schemas:");
        var schemas = adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
        foreach (var schm in schemas)
        {
            Console.WriteLine($"\t{schm.Name}");
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

    var tbl = adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = adlaJobClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>Afficher la liste des travaux ayant échoué
Le code suivant répertorie les informations sur les travaux qui ont échoué.

    var jobs = adlaJobClient.Job.List(adlaClient,
        new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" });
    foreach (var j in jobs)
    {
        Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
    }


## <a name="see-also"></a>Voir aussi
* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
* [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

