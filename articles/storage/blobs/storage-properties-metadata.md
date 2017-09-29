---
title: "Définir et récupérer des propriétés et métadonnées d’objet dans Stockage Azure | Documents Microsoft"
description: "Stockez des métadonnées personnalisées sur des objets dans Azure Storage, et définissez et récupérez les propriétés système."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 6fe7d46e39de204874c8bc91a0101b9e0541539b
ms.contentlocale: fr-fr
ms.lasthandoff: 09/29/2017

---
# <a name="set-and-retrieve-properties-and-metadata"></a>Définir et récupérer des propriétés et métadonnées d’objet

Les objets dans Stockage Azure prennent en charge des propriétés système et des métadonnées définies par l’utilisateur, en plus des données qu’ils contiennent. Cet article décrit la gestion des propriétés système et des métadonnées définies par l’utilisateur avec la [Bibliothèque cliente Stockage Azure pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Propriétés système** : des propriétés système s’appliquent à chaque ressource de stockage. Certaines d'entre elles peuvent être lues ou configurées, alors que d'autres sont en lecture seule. En arrière-plan, certaines propriétés système correspondent à certains en-têtes HTTP standard. La bibliothèque cliente de stockage Azure gère ces en-têtes pour vous.

* **Métadonnées définies par l’utilisateur** : il s’agit de métadonnées que vous spécifiez pour une ressource donnée, sous la forme d’une paire nom-valeur. Vous pouvez utiliser des métadonnées pour stocker des valeurs supplémentaires avec une ressource de stockage. Ces valeurs de métadonnées supplémentaires sont destinées à votre usage personnel et n’affectent pas le comportement de la ressource.

La récupération des valeurs des propriétés et des métadonnées d’une ressource se déroule en deux étapes. Pour pouvoir lire ces valeurs, vous devez les récupérer explicitement en appelant la méthode **FetchAttributes** .

> [!IMPORTANT]
> Les valeurs des propriétés et des métadonnées d’une ressource de stockage ne sont pas renseignées, sauf si vous appelez l’une des méthodes **FetchAttributes** .
>
> Vous recevrez une erreur `400 Bad Request` si des paires nom/valeur contiennent des caractères non-ASCII. Les paires nom/valeur de métadonnées sont des en-têtes HTTP valides, et doivent donc respecter toutes les restrictions régissant les en-têtes HTTP. Par conséquent, il est recommandé d’utiliser l’encodage URL ou l’encodage Base64 pour les noms et valeurs contenant des caractères non-ASCII.
>

## <a name="setting-and-retrieving-properties"></a>Définition et récupération de propriétés
Pour récupérer des valeurs de propriétés, appelez la méthode **FetchAttributes** sur votre objet blob ou votre conteneur pour remplir les propriétés, puis lisez les valeurs.

Pour configurer les propriétés d’un objet blob, indiquez la valeur de propriété, puis appelez la méthode **SetProperties** .

L’exemple de code suivant crée un conteneur, puis écrit certaines de ses valeurs de propriété dans une fenêtre de console.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
container.FetchAttributes();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Définition et récupération de métadonnées
Vous pouvez indiquer des métadonnées sous la forme de paires nom-valeur sur une ressource d’objet blob ou de conteneur. Pour configurer des métadonnées, ajoutez des paires nom-valeur à la collection **Metadata** de la ressource, puis appelez la méthode **SetMetadata** pour enregistrer les valeurs sur le service.

> [!NOTE]
> Le nom de vos métadonnées doit respecter la convention d’affectation de noms pour les identificateurs C#.
>
>

L’exemple de code suivant définit les métadonnées d’un conteneur. Une valeur est définie à l’aide de la méthode **Add** de la collection. L’autre valeur est définie à l’aide d’une syntaxe implicite clé/valeur. Les deux sont valides.

```csharp
public static void AddContainerMetadata(CloudBlobContainer container)
{
    //Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    //Set the container's metadata.
    container.SetMetadata();
}
```

Pour récupérer des métadonnées, appelez la méthode **FetchAttributes** sur votre objet blob ou votre conteneur pour remplir la collection **Metadata**, puis lisez les valeurs, comme indiqué dans l’exemple suivant.

```csharp
public static void ListContainerMetadata(CloudBlobContainer container)
{
    //Fetch container attributes in order to populate the container's properties and metadata.
    container.FetchAttributes();

    //Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Référence de la bibliothèque cliente Stockage Azure pour .NET](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Package NuGet de la bibliothèque cliente Stockage Azure pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)

