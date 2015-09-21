
<properties 
  pageTitle="Configurer et récupérer les propriétés et les métadonnées pour les objets dans Azure Storage | Microsoft Azure" 
  description="Stockez des métadonnées personnalisées sur des objets dans Azure Storage, et définissez et récupérez les propriétés système." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="09/03/2015" 
  ms.author="tamram"/>


# Configuration et récupération des propriétés et des métadonnées #

## Vue d'ensemble

Objets dans les propriétés du système de support de stockage Azure et des métadonnées définies par l’utilisateur, en plus des données qu’ils contiennent :

*   Les **propriétés système** : propriétés existant sur chaque ressource de stockage. Certaines d'entre elles peuvent être lues ou configurées, alors que d'autres sont en lecture seule. En arrière-plan, certaines propriétés système correspondent à certains en-têtes HTTP standard. La bibliothèque cliente de stockage Azure gère ces en-têtes pour vous.  

*   Les **métadonnées configurées par l’utilisateur** : il s’agit de métadonnées que vous spécifiez pour une ressource donnée, sous la forme d’une paire nom-valeur. Vous pouvez utiliser les métadonnées pour stocker des valeurs supplémentaires avec une ressource de stockage. Ces valeurs sont pour votre usage personnel et n’ont aucun impact sur le comportement de la ressource.

## Configuration et récupération des propriétés

La récupération des valeurs des propriétés et des métadonnées d’une ressource se déroule en deux étapes. Pour pouvoir lire ces valeurs, vous devez les récupérer explicitement en appelant la méthode **FetchAttributes** ou **FetchAttributesAsync**.

> [AZURE.IMPORTANT]Les valeurs des propriétés et des métadonnées d’une ressource de stockage ne sont pas renseignées, sauf si vous appelez l’une des méthodes **FetchAttributes**.

Pour définir des propriétés sur un objet blob, indiquez la valeur des propriétés, puis appelez la méthode **SetProperties** ou **SetProperties**.

L’exemple de code suivant crée un conteneur et un objet blob, et écrit les valeurs des propriétés dans une fenêtre de console :

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
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

> [AZURE.NOTE]\: le nom de vos métadonnées doit respecter la convention d’affectation de noms pour les identificateurs C#.
 
Pour récupérer des métadonnées, appelez la méthode **FetchAttributes** sur votre objet blob ou votre conteneur pour remplir la collection **Metadata**, puis lisez les valeurs.

L’exemple de code suivant crée un conteneur et configure ses métadonnées, puis lit les valeurs des métadonnées :

         
	// Account name and key.  Modify for your account.
	<span style="color:Blue;">string accountName = <span style="color:#A31515;">"myaccount";
	<span style="color:Blue;">string accountKey = <span style="color:#A31515;">"SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";

	// Get a reference to the storage account and client with authentication credentials.
	StorageCredentials credentials = <span style="color:Blue;">new StorageCredentials(accountName, accountKey);
	CloudStorageAccount storageAccount = <span style="color:Blue;">new CloudStorageAccount(credentials, <span style="color:Blue;">true);
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Set metadata for the container.
	container.Metadata[<span style="color:#A31515;">"category"] = <span style="color:#A31515;">"images";
	container.Metadata[<span style="color:#A31515;">"owner"] = <span style="color:#A31515;">"azure";
	container.SetMetadata();

	// Get container metadata.
	container.FetchAttributes();
	<span style="color:Blue;">foreach (<span style="color:Blue;">string key <span style="color:Blue;">in container.Metadata.Keys)
	{
	   Console.WriteLine(<span style="color:#A31515;">"Metadata key: " + key);
	   Console.WriteLine(<span style="color:#A31515;">"Metadata value: " + container.Metadata[key]);
	}

	//Clean up.
	container.Delete();

## Voir aussi  

- [Référence de la bibliothèque cliente de stockage Azure](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Prise en main du stockage d’objets blob pour .NET](storage-dotnet-how-to-use-blobs.md)  
 

<!---HONumber=Sept15_HO2-->