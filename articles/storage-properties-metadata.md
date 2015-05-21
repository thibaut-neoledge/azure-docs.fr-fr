
<properties pageTitle="Configuration et récupération des propriétés et des métadonnées des objets blob de stockage | Microsoft Azure" description="Découvrez comment configurer et récupérer des propriétés et des métadonnées à partir des conteneurs et objets blob Azure Storage." services="storage" documentationCenter="" authors="tamram" manager="adinah" editor=""/>

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="04/21/2015" ms.author="tamram"/>


# Configuration et récupération des propriétés et des métadonnées #

## Vue d'ensemble

Les conteneurs et les objets blob prennent en charge deux formes de données associées, en plus des données qu'ils contiennent :

*   Les **propriétés système** : elles existent sur chaque ressource de conteneur d’objets blob. Certaines d'entre elles peuvent être lues ou configurées, alors que d'autres sont en lecture seule. En arrière-plan, certaines propriétés système correspondent à certains en-têtes HTTP standard, que la bibliothèque managée Azure conserve pour vous.  

*   Les **métadonnées configurées par l’utilisateur** : il s’agit de métadonnées que vous spécifiez pour une ressource donnée, sous la forme d’une paire nom-valeur. Vous pouvez utiliser les métadonnées pour stocker des valeurs supplémentaires avec un conteneur ou un objet blob ; ces valeurs sont pour votre usage personnel et n'affectent pas le comportement du conteneur ou de l’objet blob.

> [AZURE.IMPORTANT]La récupération des valeurs de propriétés et de métadonnées d’une ressource se déroule en deux étapes. Avant de pouvoir lire ces valeurs, vous devez les extraire de manière explicite de vos objets [CloudBlobContainer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx), [CloudBlockBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx) ou [CloudPageBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudpageblob.aspx). Pour extraire les propriétés et métadonnées de façon synchrone, appelez la méthode **FetchAttributes** sur le conteneur ou l’objet blob ; pour les extraire de façon asynchrone, appelez la méthode **FetchAttributesAsync**.

## Configuration et récupération des propriétés

Un conteneur a uniquement des propriétés en lecture seule, alors qu'un objet blob a des propriétés en lecture seule et en lecture-écriture. Pour configurer les propriétés d'un objet blob, indiquez la valeur de propriété, puis appelez la méthode [SetProperties](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.setproperties.aspx) ou [SetProperties](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudpageblob.setproperties.aspx).

Pour lire les propriétés sur un conteneur ou un objet blob, appelez la méthode **FetchAttributes**, puis récupérez la valeur de propriété.

L'exemple de code suivant crée un conteneur et un objet blob et écrit les valeurs de propriété dans une fenêtre de console. Cet exemple utilisant un émulateur de stockage, le service de stockage émulé doit être exécuté pour que le code fonctionne :

	// Use the storage emulator account.
	CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

	// As an alternative, you can create the credentials from the account name and key.
	// string accountName = "myaccount";
	// string accountKey = "SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";
	// StorageCredentials credentials = new StorageCredentials(accountName, accountKey);
	// CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);

	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Fetch container properties and write out their values.
	container.FetchAttributes();
	Console.WriteLine(<span style="color:#A31515;">"Properties for container " + container.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUTC: " + container.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + container.Properties.ETag);
	Console.WriteLine();

	// Create a blob.
	CloudBlockBlob blob = container.GetBlockBlobReference(<span style="color:#A31515;">"myblob.txt");

	// Create or overwrite the "myblob.txt" blob with contents from a local file.
	<span style="color:Blue;">using (<span style="color:Blue;">var fileStream = System.IO.File.OpenRead(<span style="color:#A31515;">@"c:\test\myblob.txt"))
	{
	   blob.UploadFromStream(fileStream);
	} 

	// Fetch container properties and write out their values.
	container.FetchAttributes();
	Console.WriteLine(<span style="color:#A31515;">"Properties for container " + container.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUTC: " + container.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + container.Properties.ETag);
	Console.WriteLine();

	// Create a blob.
	Uri blobUri =<span style="color:Blue;">new UriBuilder(containerUri.AbsoluteUri + <span style="color:#A31515;">"/ablob.txt").Uri;
	CloudPageBlob blob = <span style="color:Blue;">new CloudPageBlob(blobUri, credentials);
	blob.Create(1024);
				

	// Set the CacheControl property.
	blob.Properties.CacheControl = <span style="color:#A31515;">"public, max-age=31536000";
	blob.SetProperties();

	// Fetch blob attributes.
	blob.FetchAttributes();

	Console.WriteLine(<span style="color:#A31515;">"Read-only properties for blob " + blob.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"BlobType: " + blob.Properties.BlobType);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + blob.Properties.ETag);
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUtc: " + blob.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"Length: " + blob.Properties.Length);
	Console.WriteLine();

	Console.WriteLine(<span style="color:#A31515;">"Read-write properties for blob " + blob.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"CacheControl: " +
	   (blob.Properties.CacheControl == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.CacheControl));
	Console.WriteLine(<span style="color:#A31515;">"ContentEncoding: " +
	   (blob.Properties.ContentEncoding == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentEncoding));
	Console.WriteLine(<span style="color:#A31515;">"ContentLanguage: " +
	   (blob.Properties.ContentLanguage == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentLanguage));
	Console.WriteLine(<span style="color:#A31515;">"ContentMD5: " +
	   (blob.Properties.ContentMD5 == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentMD5));
	Console.WriteLine(<span style="color:#A31515;">"ContentType: " +
	   (blob.Properties.ContentType == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentType));

	// Clean up.
	blob.DeleteIfExists();
	container.Delete();
## Configuration et récupération des métadonnées

Vous pouvez indiquer des métadonnées sous la forme de paires nom-valeur sur une ressource d’objet blob ou de conteneur. Pour configurer des métadonnées, ajoutez des paires nom-valeur à la collection **Metadata** de la ressource, puis appelez la méthode **SetMetadata** pour enregistrer les valeurs sur le service.

> [AZURE.NOTE]: le nom de vos métadonnées doit respecter la convention d’affectation de noms pour les identificateurs C#.
 
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

<!--HONumber=52-->
