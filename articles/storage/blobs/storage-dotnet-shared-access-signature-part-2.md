---
title: "Créer et utiliser une signature d’accès partagé (SAP) avec le Stockage Blob Azure | Documents Microsoft"
description: "Ce didacticiel vous montre comment créer des signatures d’accès partagé en vue d’une utilisation avec Stockage Blob et comment les utiliser dans vos applications clientes."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: marsma
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 0d7bede352667931527c8583cb172a46a37b5aa8
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Signatures d’accès partagé, partie 2 : créer et utiliser une signature d’accès partagé avec Blob Storage

[partie 1](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) de ce didacticiel traitait des signatures d'accès partagé et indiquait les meilleures pratiques les concernant. La partie 2 indique comment générer et utiliser les signatures d’accès partagé avec Blob Storage. Les exemples ont été écrits en C# et utilisent la bibliothèque du client Azure Storage pour .NET. Le didacticiel traite des points suivants :

* Générer une signature d'accès partagé sur un conteneur
* Générer une signature d'accès partagé sur un blob
* Créer une stratégie d’accès afin de gérer les signatures sur les ressources d’un conteneur
* Tester les signatures d’accès partagé dans une application cliente

## <a name="about-this-tutorial"></a>À propos de ce didacticiel
Au cours de ce didacticiel, nous allons créer deux consoles d’application qui présentent la création et l’utilisation des signatures d’accès partagé pour les conteneurs et les blobs :

**Application n°1** : l’application de gestion. Elle génère une signature d’accès partagé pour un conteneur et un blob. Elle inclut la clé d’accès au compte de stockage dans le code source.

**Application n°2** : l’application cliente. Elle accède aux ressources du conteneur et du blob à l’aide des signatures d’accès partagé, créées grâce à la première application. Elle utilise uniquement les signatures d’accès partagé pour accéder aux ressources du conteneur et du blob. La clé d’accès au compte de stockage *n’est pas* incluse.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Première partie : créer une application console pour générer des signatures d’accès partagé
Commencez par vérifier que la bibliothèque cliente Azure Storage pour .NET est installée. Vous pouvez installer le [package NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "package NuGet") qui contient les assemblies les plus récentes pour la bibliothèque cliente. Il s’agit de la méthode recommandée pour vérifier que vous disposez des correctifs les plus récents. Vous pouvez également télécharger la bibliothèque cliente avec la version la plus récente du [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/).

Dans Visual Studio, créez une application console Windows et nommez-la **GenerateSharedAccessSignatures**. Ajoutez des références à [Microsoft.WindowsAzure.Configuration.dll](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) et [Microsoft.WindowsAzure.Storage.dll](https://www.nuget.org/packages/WindowsAzure.Storage/) en utilisant l’une des méthodes suivantes :

* Utilisez le [gestionnaire des packages NuGet](https://docs.nuget.org/consume/installing-nuget) dans Visual Studio. Sélectionnez **Projet** > **Gérer les packages NuGet**, puis effectuez une recherche en ligne pour chaque package (Microsoft.WindowsAzure.ConfigurationManager et WindowsAzure.Storage), et installez-les.
* Vous pouvez également rechercher ces assemblies dans l’installation de votre Kit de développement logiciel Azure SDK et y ajouter des références :
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

Au tout début du fichier Program.cs, ajoutez les directives **d’utilisation** suivantes :

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Modifiez le fichier app.config pour qu'il contienne un paramètre de configuration avec une chaîne de connexion qui pointe vers votre compte de stockage. Votre fichier app.config ressemble à ceci :

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Générer une URI de signature d’accès partagé pour un conteneur
Pour commencer, nous allons ajouter une méthode pour générer une signature d'accès partagé sur un nouveau conteneur. Ici, la signature n'est pas associée à une stratégie d'accès stocké. C’est pourquoi elle reporte sur l’URI les informations concernant son heure d’expiration et les autorisations qu’elle accorde.

Ajoutons du code à la méthode **Main()** pour authentifier l'accès à votre compte de stockage et créer un conteneur :

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

Ensuite, ajoutez une nouvelle méthode qui génère la signature d'accès partagé pour le conteneur et renvoie l'URI de signature :

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Ajoutez les lignes suivantes à la fin de la méthode **Main()** avant d’appeler **Console.ReadLine()** pour appeler **GetContainerSasUri()** et écrire l’URI de la signature dans la fenêtre de la console :

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Compilez et exécutez pour renvoyer l'URI de la signature d'accès partagé pour le nouveau conteneur. L’URI ressemblera à l’exemple suivant :

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

Dès lors que le code est exécuté, la signature d’accès partagé que vous avez créée pour le conteneur sera valide durant les prochaines 24 heures. La signature accorde une autorisation client pour lister des blobs dans le conteneur et pour écrire de nouveaux blobs dans le conteneur.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Générer une URI de signature d’accès partagé pour un blob
Nous allons ensuite écrire un code similaire afin de créer un blob au sein du conteneur et lui générer une signature d'accès partagé. Cette signature d'accès partagé n'est pas associée à une stratégie d'accès stocké. C’est pourquoi elle inclut dans l’URI l’heure de début, l’heure d’expiration et les informations concernant l’autorisation.

Ajoutez une nouvelle méthode qui crée un blob et y écrit quelques textes, puis génère une signature d’accès partagé et renvoie l’URI de signature :

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Ajoutez les lignes suivantes à la fin de la méthode **Main()** avant d’appeler **Console.ReadLine()** pour appeler **GetBlobSasUri()** et écrire l’URI de la signature d’accès partagé dans la fenêtre de la console :

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Compilez et exécutez pour renvoyer l'URI de la signature d'accès partagé pour le nouvel objet blob. L’URI ressemblera à l’exemple suivant :

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>Créer une stratégie d’accès stocké sur le conteneur
Créons une stratégie d'accès stockée sur le conteneur pour définir les contraintes des signatures d'accès partagé qui y sont associées.

Dans les exemples précédents, nous avons spécifié l'heure de début (implicitement ou explicitement), l'heure d'expiration et les autorisations sur l'URI de la signature d'accès partagé lui-même. Dans les exemples suivants, nous allons les spécifier sur la stratégie d'accès stocké, et non sur la signature d’accès partagé. Cela nous permettra de modifier ces contraintes sans devoir émettre de nouveau la signature d'accès partagé.

Il est possible de définir sur la signature d’accès partagé un certain nombre de contraintes et de définir le reste sur la stratégie d’accès stocké. Toutefois, vous ne pouvez spécifier que l’heure de début, l’heure d’expiration et les autorisations dans un emplacement ou un autre. Par exemple, vous ne pouvez pas spécifier d’autorisations sur la signature d’accès partagé et dans le même temps, les spécifier sur une stratégie d’accès stocké.

Lorsque vous ajoutez une stratégie d’accès stocké à un conteneur, vous devez obtenir les autorisations existantes du conteneur, ajouter la nouvelle stratégie d’accès et ensuite configurer les autorisations du conteneur.

Ajoutez une nouvelle méthode qui crée une stratégie d’accès stockée sur un conteneur et renvoie le nom de la stratégie :

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

À la fin de la méthode **Main()**, avant d’appeler **Console.ReadLine()**, ajoutez les lignes suivantes pour supprimer dans un premier temps toute stratégie d’accès existante, puis appeler la méthode **CreateSharedAccessPolicy()** :

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

Avant de désactiver les stratégies d’accès sur un conteneur, vous devez obtenir les autorisations existantes du conteneur. Vous pouvez par la suite désactiver les autorisations et en configurer de nouvelles.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Générer une URI de signature d’accès partagé sur un conteneur qui utilise une stratégie d’accès
Nous allons dès à présent créer une nouvelle fois une signature d’accès partagé pour le conteneur créé précédemment. Cette fois-ci, nous allons associer la signature à la stratégie d’accès stocké de l’exemple précédent.

Ajouter une nouvelle méthode afin de générer une autre signature d’accès partagé pour le conteneur :

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Ajoutez les lignes suivantes à la fin de la méthode **Main()** avant d’appeler **Console.ReadLine()** pour appeler la méthode **GetContainerSasUriWithPolicy** :

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Génération d'un URI de signature d'accès partagé sur l'objet blob qui utilise une stratégie d'accès
Pour finir, nous allons ajouter une méthode similaire afin de créer un autre blob et générer une signature d’accès partagé, associée à une stratégie d’accès partagé.

Ajoutez une nouvelle méthode pour créer un objet blob et générer une signature d'accès partagé :

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Ajoutez les lignes suivantes à la fin de la méthode **Main()** avant d’appeler **Console.ReadLine()** pour appeler la méthode **GetBlobSasUriWithPolicy** :

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

La méthode **Main()** doit maintenant ressembler à ceci. Lancez-la pour écrire les URI de signature d'accès partagé sur la fenêtre de la console, puis copiez et collez-les dans un fichier texte pour les utiliser dans la deuxième partie de ce didacticiel.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

Lorsque vous exécutez l'application console GenerateSharedAccessSignatures, vous verrez une sortie semblable à l’exemple suivant. Il s'agit des signatures d'accès partagé que vous utilisez dans la deuxième partie du didacticiel.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Deuxième partie : créer une application console afin de tester les signatures d’accès partagé
Afin de tester les signatures d’accès partagé créées dans les exemples précédents, nous allons créer une deuxième application console qui utilise les signatures pour exécuter les opérations sur le conteneur et sur un blob.

> [!NOTE]
> Si plus de 24 heures se sont écoulées depuis la fin de la première partie de ce didacticiel, les signatures que vous avez générées ne sont plus valides. Dans ce cas, exécutez le code dans la première application console pour générer de nouvelles signatures d’accès partagé pour la seconde partie du didacticiel.
>

Dans Visual Studio, créez une application console Windows et nommez-la **ConsumeSharedAccessSignatures**. Ajoutez des références à [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) et [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/), comme précédemment.

Au tout début du fichier Program.cs, ajoutez les directives **d’utilisation** suivantes :

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Dans le corps de la méthode **Main()**, ajoutez les constantes de chaîne suivantes. Cela entraîne une modification des valeurs par les signatures d’accès partagé générées dans la première partie de ce didacticiel.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Ajouter une méthode afin de tester les opérations sur un conteneur à l’aide d’une signature d’accès partagé
Par la suite, nous allons ajouter une méthode qui teste quelques opérations sur un conteneur à l’aide d’une signature d’accès partagé pour le conteneur. La signature d'accès partagé permet de renvoyer une référence au conteneur, authentifiant l'accès au conteneur uniquement sur la base de la signature.

Ajoutez la méthode suivante au fichier Program.cs :

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Mettez à jour la méthode **Main()** afin d’appeler **UseContainerSAS()** grâce aux deux signatures d’accès partagé créées sur le conteneur :

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Ajoutez une méthode pour tester les opérations sur un blob à l’aide d’une signature d'accès partagé
Pour terminer, nous allons ajouter une méthode qui teste quelques opérations sur un blob à l’aide d’une signature d’accès partagé sur le blob. Ici, nous utilisons le constructeur **CloudBlockBlob(String)**, en passant dans la signature d'accès partagé, pour renvoyer une référence vers le blob. Aucune autre authentification n'est nécessaire, car l'opération est basée uniquement sur la signature.

Ajoutez la méthode suivante au fichier Program.cs :

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Mettez à jour la méthode **Main()** pour appeler **UseBlobSAS()** avec les deux signatures d’accès partagé que vous avez créées sur l’objet blob :

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

Exécutez l'application console et observez la sortie pour connaître les opérations autorisées en fonction des signatures. La sortie dans la fenêtre de la console ressemble à ceci :

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>Étapes suivantes

* [Signatures d'accès partagé, partie 1 : présentation du modèle SAP](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](storage-manage-access-to-resources.md)
* [Délégation de l’accès avec une signature d’accès partagé (API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Présentation des signatures d’accès partagé de table et de file d’attente](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

