---
title: "Utiliser le Kit de développement logiciel (SDK) .NET pour développer des applications dans Azure Data Lake Store | Microsoft Docs"
description: "Utiliser le Kit de développement logiciel (SDK) .NET Azure Data Lake Store pour créer un compte Data Lake Store et effectuer des opérations de base dans Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/09/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 70f94a07b0102e3135eaf85e5877e3502762d7e3
ms.contentlocale: fr-fr
ms.lasthandoff: 08/03/2017

---
# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Prise en main d’Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) .NET
> [!div class="op_single_selector"]
> * [Portail](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Découvrez comment utiliser le [Kit de développement logiciel (SDK) .NET Azure Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet) pour effectuer des opérations de base comme créer des dossiers ou charger et télécharger des fichiers de données. Pour plus d’informations sur Data Lake, consultez [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Composants requis
* **Visual Studio 2013, 2015 ou 2017**. Les instructions ci-dessous utilisent Visual Studio 2015 Update 2.

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Compte Azure Data Lake Store**. Pour savoir comment créer un compte, consultez [Prise en main d’Azure Data Lake Store](data-lake-store-get-started-portal.md)

* **Créez une application Azure Active Directory**. Vous utilisez l’application Azure AD pour authentifier l’application Data Lake Store auprès d’Azure AD. Il existe différentes approches pour l’authentification auprès d’Azure AD : **authentification de l’utilisateur final** ou **authentification de service à service**. Pour obtenir des instructions et plus d’informations sur l’authentification, consultez [l’authentification de l’utilisateur final](data-lake-store-end-user-authenticate-using-active-directory.md) ou [l’authentification de service à service](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Créer une application .NET
1. Ouvrez Visual Studio et créez une application console.
2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

   | Propriété | Valeur |
   | --- | --- |
   | Catégorie |Modèles/Visual C#/Windows |
   | Modèle |Application console |
   | Nom |CreateADLApplication |
4. Cliquez sur **OK** pour créer le projet.
5. Ajoutez les packages Nuget à votre projet.

   1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.
   2. Dans l’onglet **Gestionnaire de package NuGet**, vérifiez que **Source du package** a la valeur **nuget.org** et que la case **Inclure la version préliminaire** est cochée.
   3. Recherchez et installez les packages NuGet suivants :

      * `Microsoft.Azure.Management.DataLake.Store` - Ce didacticiel utilise v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Ce didacticiel utilise v2.2.12.

        ![Ajouter une source Nuget](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Créer un compte Azure Data Lake")
   4. Fermez le **Gestionnaire de package NuGet**.
6. Ouvrez **Program.cs**, supprimez le code existant, puis insérez les instructions suivantes pour ajouter des références aux espaces de noms.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

7. Déclarez les variables comme indiqué ci-dessous et indiquez les valeurs pour le nom du Data Lake Store et le nom du groupe de ressources qui existent déjà. En outre, assurez-vous que le chemin d'accès local et le nom de fichier que vous fournissez ici existent sur l'ordinateur. Ajoutez l'extrait de code suivant après les déclarations d'espace de noms.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

Dans les sections suivantes de cet article, vous pouvez découvrir comment utiliser les méthodes .NET pour effectuer des opérations telles que l’authentification des utilisateurs et le chargement de fichiers.

## <a name="authentication"></a>Authentification

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Si vous utilisez l’authentification de l’utilisateur final (recommandée pour ce didacticiel)

Utilisez-le avec une application native Azure AD pour authentifier votre application **interactivement**, ce qui signifie que vous devrez entrer vos informations d’identification Azure.

Pour simplifier l’utilisation, l’extrait de code ci-dessous utilise les valeurs par défaut pour l’ID client et l’URI de redirection qui fonctionne avec n’importe quel abonnement Azure. Pour effectuer ce didacticiel plus rapidement, nous vous recommandons d’utiliser cette approche. Dans l’extrait de code ci-dessous, fournissez simplement la valeur de votre ID locataire. Vous pouvez les récupérer en suivant les instructions fournies sur la page [Créer une Application Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

    // User login via interactive popup
    // Use the client ID of an existing AAD Web application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;

Voici quelques informations utiles concernant l’extrait de code ci-dessus :

* Pour vous aider à effectuer le didacticiel plus rapidement, l’extrait de code ci-dessus utilise un domaine et un ID client Azure AD qui sont disponibles par défaut pour tous les abonnements Azure. Vous pouvez donc **utiliser cet extrait de code en l’état dans votre application**.
* Cependant, si vous souhaitez utiliser votre propre ID client application et domaine Azure AD, vous devez créer une application native Azure AD, puis utiliser l’ID locataire, l’ID client et l’URI de redirection Azure AD de l’application que vous avez créée. Consultez la page [créer une Application Active Directory pour l’authentification de l’utilisateur final avec Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md) pour obtenir des instructions.

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Si vous utilisez l’authentification de service à service avec une clé secrète client
Vous pouvez faire appel à l’extrait de code suivant pour authentifier votre application en mode **non interactif**, en utilisant la clé secrète du client pour une application/un principal du service. Utilisez-le avec une « application web » Azure AD existante. Pour obtenir des instructions sur la création de l’application web Azure AD et comment récupérer l’ID et la clé secrète du client requis dans l’extrait de code ci-dessous, consultez la page [Créer une Application Active Directory pour l’authentification de service à service avec Data Lake Store](data-lake-store-authenticate-using-active-directory.md).

    // Service principal / appplication authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = await ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential);

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Si vous utilisez l’authentification de service à service avec un certificat

Vous pouvez également, utiliser l’extrait de code pour authentifier votre application en mode **non interactif**, en utilisant le certificat pour une application Azure Active Directory/un principal de service. Utilisez-le avec une [Application Azure AD existante dotée de certificats](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = await ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate);

## <a name="create-client-objects"></a>Créer des objets clients
L’extrait de code suivant crée le compte Data Lake Store et les objets clients filesystem, qui sont utilisés pour adresser des demandes au service.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds) { SubscriptionId = _subId };
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Répertorier tous les comptes Data Lake Store d’un abonnement
L’extrait de code suivant répertorie tous les comptes Data Lake Store d’un abonnement Azure donné.

    // List all ADLS accounts within the subscription
    public static async Task<List<DataLakeStoreAccount>> ListAdlStoreAccounts()
    {
        var response = await _adlsClient.Account.ListAsync();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="create-a-directory"></a>Créer un répertoire
L’extrait de code suivant montre une méthode `CreateDirectory` que vous pouvez utiliser pour créer un répertoire dans un compte Data Lake Store.

    // Create a directory
    public static async Task CreateDirectory(string path)
    {
        await _adlsFileSystemClient.FileSystem.MkdirsAsync(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Charger un fichier
L’extrait de code suivant montre une méthode `UploadFile` que vous pouvez utiliser pour charger des fichiers vers un compte Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

Le kit de développement logiciel (SDK) prend en charge le chargement et le téléchargement récursifs entre un chemin d’accès local et un chemin d’accès Data Lake Store.    

## <a name="get-file-or-directory-info"></a>Obtenir des informations sur un fichier ou répertoire
L’extrait de code suivant montre une méthode `GetItemInfo` que vous pouvez utiliser pour récupérer des informations sur un fichier ou un répertoire disponibles dans Data Lake Store.

    // Get file or directory info
    public static async Task<FileStatusProperties> GetItemInfo(string path)
    {
        return await _adlsFileSystemClient.FileSystem.GetFileStatusAsync(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Répertorier des fichiers ou répertoires
L’extrait de code suivant montre une méthode `ListItem` que vous pouvez utiliser pour répertorier les fichiers et répertoires dans un compte Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Concaténation de fichiers
L’extrait de code suivant montre une méthode `ConcatenateFiles` qui vous permet de concaténer des fichiers.

    // Concatenate files
    public static Task ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        await _adlsFileSystemClient.FileSystem.ConcatAsync(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Ajout à un fichier
L’extrait de code suivant montre une méthode `AppendToFile` qui permet d’ajouter des données à un fichier déjà stocké dans un compte Data Lake Store.

    // Append to file
    public static async Task AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            await _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

## <a name="download-a-file"></a>Téléchargement d’un fichier
L’extrait de code suivant montre une méthode `DownloadFile` que vous pouvez utiliser pour télécharger un fichier depuis un compte Data Lake Store.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
         _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath);
    }

## <a name="next-steps"></a>Étapes suivantes
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Store .NET SDK Reference (Informations de référence sur le Kit de développement logiciel (SDK) .NET Azure Data Lake Store)](https://docs.microsoft.com/dotnet/api/?view=azuremgmtdatalakestore-2.1.0-preview&term=DataLake.Store)
* [Data Lake Store REST Reference (Informations de référence sur les API REST Data Lake Store)](https://msdn.microsoft.com/library/mt693424.aspx)

