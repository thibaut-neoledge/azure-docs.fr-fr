---
title: "Kit de développement logiciel (SDK) .NET : opération de gestion du système de fichiers sur Azure Data Lake Store | Microsoft Docs"
description: "Utilisez le kit de développement logiciel (SDK) .NET d’Azure Data Lake Store pour réaliser des opérations de gestion du système de fichiers sur Data Lake Store, telles que la création de dossiers, etc."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: f525bd81ebbc27a4730bdced5c8778b6f2ac69b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>Opérations de gestion du système de fichiers sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET
> [!div class="op_single_selector"]
> * [Kit SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Dans cet article, vous apprenez à réaliser des opérations de gestion du système de fichiers sur Data Lake Store avec le kit de développement logiciel (SDK) .NET. Les opérations de gestion du système de fichiers comprennent la création de dossiers dans un compte Data Lake Store, le chargement et le téléchargement de fichiers, etc.

Pour obtenir des instructions sur l’exécution des opérations de gestion des comptes sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET, consultez la section relative aux [opérations de gestion de compte sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Composants requis
* **Visual Studio 2013, 2015 ou 2017**. Les instructions ci-dessous reposent sur Visual Studio 2017.

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Compte Azure Data Lake Store**. Pour savoir comment créer un compte, consultez [Prise en main d’Azure Data Lake Store](data-lake-store-get-started-portal.md)

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
5. Ajoutez les packages NuGet à votre projet.

   1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.
   2. Dans l’onglet **Gestionnaire de package NuGet**, vérifiez que **Source du package** a la valeur **nuget.org** et que la case **Inclure la version préliminaire** est cochée.
   3. Recherchez et installez les packages NuGet suivants :

      * `Microsoft.Azure.Management.DataLake.Store` - Ce didacticiel utilise v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Ce didacticiel utilise v2.2.12.

        ![Ajouter une source NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Créer un compte Azure Data Lake")
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

7. Déclarez les variables comme indiqué ci-dessous, et fournissez les valeurs des espaces réservés. En outre, assurez-vous que le chemin d’accès local et le nom de fichier que vous fournissez ici existent sur l’ordinateur.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static async void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

Dans les sections suivantes de cet article, vous pouvez découvrir comment utiliser les méthodes .NET pour effectuer des opérations telles que l’authentification des utilisateurs et le chargement de fichiers.

## <a name="authentication"></a>Authentification

* Pour en savoir plus sur l’authentification des utilisateurs accédant à votre application, consultez la section relative à [l’authentification de l’utilisateur avec Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Pour en savoir plus sur l’authentification entre les services dans le cadre de votre application, consultez la section relative à [l’authentification entre les services avec Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-objects"></a>Créer des objets clients
L’extrait de code suivant crée le compte Data Lake Store et les objets clients filesystem, qui sont utilisés pour adresser des demandes au service.

    // Create client objects
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## <a name="create-a-directory"></a>Créer un répertoire
Ajoutez la méthode suivante à votre classe. L’extrait de code montre une méthode `CreateDirectory()` que vous pouvez utiliser pour créer un répertoire dans un compte Data Lake Store.

    // Create a directory
    public static void CreateDirectory(string path)
    {
            _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

Ajoutez l’extrait de code suivant à votre méthode `Main()` pour invoquer la méthode `CreateDirectory()`.

    CreateDirectory(remoteFolderPath);
    Console.WriteLine("Created a directory in the Data Lake Store account. Press any key to continue ...");
    Console.ReadLine();

## <a name="upload-a-file"></a>Charger un fichier
Ajoutez la méthode suivante à votre classe. L’extrait de code montre une méthode `UploadFile()` que vous pouvez utiliser pour charger des fichiers vers un compte Data Lake Store. Le kit de développement logiciel (SDK) prend en charge le chargement et le téléchargement récursifs entre un chemin d’accès local et un chemin d’accès Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

Ajoutez l’extrait de code suivant à votre méthode `Main()` pour invoquer la méthode `UploadFile()`.

    UploadFile(localFilePath, remoteFilePath, true);
    Console.WriteLine("Uploaded file in the directory. Press any key to continue...");
    Console.ReadLine();
    

## <a name="get-file-or-directory-info"></a>Obtenir des informations sur un fichier ou répertoire
L’extrait de code suivant montre une méthode `GetItemInfo()` que vous pouvez utiliser pour récupérer des informations sur un fichier ou un répertoire disponibles dans Data Lake Store.

    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

Ajoutez l’extrait de code suivant à votre méthode `Main()` pour invoquer la méthode `GetItemInfo()`.

    
    var fileProperties = GetItemInfo(remoteFilePath);
    Console.WriteLine("The owner of the file at the path is:", fileProperties.Owner);
    Console.WriteLine("Press any key to continue...");
    Console.ReadLine();

## <a name="list-file-or-directories"></a>Répertorier des fichiers ou répertoires
L’extrait de code suivant montre une méthode `ListItems()` que vous pouvez utiliser pour répertorier les fichiers et répertoires dans un compte Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

Ajoutez l’extrait de code suivant à votre méthode `Main()` pour invoquer la méthode `ListItems()`.

    var itemList = ListItems(remoteFolderPath);
    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
    Console.WriteLine(String.Join("\r\n", fileMenuItems));
    Console.WriteLine("Files and directories listed. Press any key to continue ...");
    Console.ReadLine();

## <a name="concatenate-files"></a>Concaténation de fichiers
L’extrait de code suivant montre une méthode `ConcatenateFiles()` qui vous permet de concaténer des fichiers.

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

Ajoutez l’extrait de code suivant à votre méthode `Main()` pour invoquer la méthode `ConcatenateFiles()`. Cet extrait de code part du principe que vous avez chargé un autre fichier vers le compte Data Lake Store, et que le chemin de ce fichier est fourni dans la chaîne *anotherRemoteFilePath*.

    string[] stringOfFiles = new String[] {remoteFilePath, anotherRemoteFilePath};
    string destFilePath = Path.Combine(remoteFolderPath, "Concatfile.txt");
    ConcatenateFiles(stringOfFiles, destFilePath);
    Console.WriteLine("Files concatinated. Press any key to continue ...");
    Console.ReadLine();

## <a name="append-to-a-file"></a>Ajout à un fichier
L’extrait de code suivant montre une méthode `AppendToFile()` qui permet d’ajouter des données à un fichier déjà stocké dans un compte Data Lake Store.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

Ajoutez l’extrait de code suivant à votre méthode `Main()` pour invoquer la méthode `AppendToFile()`.

    AppendToFile(remoteFilePath, "123");
    Console.WriteLine("Content appended. Press any key to continue ...");
    Console.ReadLine();

## <a name="download-a-file"></a>Téléchargement d’un fichier
L’extrait de code suivant montre une méthode `DownloadFile()` que vous pouvez utiliser pour télécharger un fichier depuis un compte Data Lake Store.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:true);
    }

Ajoutez l’extrait de code suivant à votre méthode `Main()` pour invoquer la méthode `DownloadFile()`.

    DownloadFile(destFilePath, localFilePath);
    Console.WriteLine("File downloaded. Press any key to continue ...");
    Console.ReadLine();

## <a name="see-also"></a>Voir aussi
* [Opérations de gestion des comptes sur  Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store .NET SDK Reference (Informations de référence sur le Kit de développement logiciel (SDK) .NET Azure Data Lake Store)](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Étapes suivantes
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
