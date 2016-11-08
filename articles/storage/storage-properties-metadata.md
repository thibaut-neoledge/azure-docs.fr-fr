---
title: Configurer et récupérer les propriétés et les métadonnées pour les objets dans Azure Storage | Microsoft Docs
description: Stockez des métadonnées personnalisées sur des objets dans Azure Storage, et définissez et récupérez les propriétés système.
services: storage
documentationcenter: ''
author: tamram
manager: carmonm
editor: tysonn

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: dineshm;tamram

---
# Configuration et récupération des propriétés et des métadonnées
## Vue d'ensemble
Objets dans les propriétés du système de support de stockage Azure et des métadonnées définies par l’utilisateur, en plus des données qu’ils contiennent :

* **Les propriétés système :** propriétés existant sur chaque ressource de stockage. Certaines d'entre elles peuvent être lues ou configurées, alors que d'autres sont en lecture seule. En arrière-plan, certaines propriétés système correspondent à certains en-têtes HTTP standard. La bibliothèque cliente de stockage Azure gère ces en-têtes pour vous.
* **Les métadonnées configurées par l’utilisateur :** il s’agit de métadonnées que vous spécifiez pour une ressource donnée, sous la forme d’une paire nom-valeur. Vous pouvez utiliser les métadonnées pour stocker des valeurs supplémentaires avec une ressource de stockage. Ces valeurs sont pour votre usage personnel et n’ont aucun impact sur le comportement de la ressource.

La récupération des valeurs des propriétés et des métadonnées d’une ressource se déroule en deux étapes. Pour pouvoir lire ces valeurs, vous devez les récupérer explicitement en appelant la méthode **FetchAttributes**.

> [!IMPORTANT]
> Les valeurs des propriétés et des métadonnées d’une ressource de stockage ne sont pas renseignées, sauf si vous appelez l’une des méthodes **FetchAttributes**.
> 
> 

## Configuration et récupération des propriétés
Pour récupérer des valeurs de propriétés, appelez la méthode **FetchAttributes** sur votre objet blob ou votre conteneur pour remplir les propriétés, puis lisez les valeurs.

Pour configurer les propriétés d’un objet blob, indiquez la valeur de propriété, puis appelez la méthode **SetProperties**.

L’exemple de code suivant crée un conteneur et un objet blob, et écrit certaines des valeurs des propriétés dans une fenêtre de console :

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

## Configuration et récupération des métadonnées
Vous pouvez indiquer des métadonnées sous la forme de paires nom-valeur sur une ressource d’objet blob ou de conteneur. Pour configurer des métadonnées, ajoutez des paires nom-valeur à la collection **Metadata** de la ressource, puis appelez la méthode **SetMetadata** pour enregistrer les valeurs sur le service.

> [!NOTE]
> Le nom de vos métadonnées doit respecter la convention d’affectation de noms pour les identificateurs C#.
> 
> 

L’exemple de code suivant définit les métadonnées d’un conteneur. Une valeur est définie à l’aide de la méthode **Add** de la collection. L’autre valeur est définie à l’aide d’une syntaxe implicite clé/valeur. Les deux sont valides.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Pour récupérer des métadonnées, appelez la méthode **FetchAttributes** sur votre objet blob ou votre conteneur pour remplir la collection **Metadata**, puis lisez les valeurs, comme indiqué dans l’exemple suivant.

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

## Voir aussi
* [Références sur la bibliothèque cliente Azure Storage pour .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [Package de la bibliothèque cliente Azure Storage pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)

<!---HONumber=AcomDC_0928_2016-->