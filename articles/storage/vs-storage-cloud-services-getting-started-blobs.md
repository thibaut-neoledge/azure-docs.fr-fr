<properties 
	pageTitle="Mise en route avec le stockage d’objets blob Azure et les appareils connectés Visual Studio"
	description="Mise en route de l'utilisation du stockage d'objets blob Azure dans un projet de service cloud dans Visual Studio"
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags 
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2015"
	ms.author="patshea123"/>

# Mise en route avec le stockage d’objets blob Azure et les appareils connectés Visual Studio

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-cloud-services-getting-started-blobs.md)
> - [What happened](vs-storage-cloud-services-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

##Vue d'ensemble

Cet article décrit comment prendre en main le stockage d’objets blob après avoir créé ou référencé un compte de stockage Azure dans un projet de services cloud via la boîte de dialogue **Ajouter des services connectés** de Visual Studio. Nous allons vous montrer comment créer des conteneurs d’objets blob, y accéder et effectuer des tâches courantes comme télécharger, répertorier et charger des objets blob. Les exemples ont été écrits en C# et utilisent la [bibliothèque cliente de stockage Azure pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Azure Blob storage is a service for storing large amounts of unstructured data that can be accessed from anywhere in the world via HTTP or HTTPS. Les objets blob peuvent être de toutes tailles. Il peut s'agir d'images, de fichiers audio ou vidéo, de données brutes ou de fichiers de documents.

De la même manière que les fichiers résident dans des dossiers, le stockage des objets blob s'effectue dans des conteneurs. Après avoir créé un stockage, créez un ou plusieurs conteneurs dans le stockage. Par exemple, dans un stockage appelé « Scrapbook », vous pouvez créer des conteneurs dans le stockage appelé « images » pour stocker des photos et un autre appelé « audio » pour stocker des fichiers audio. Une fois que vous avez créé les conteneurs, vous pouvez y charger des fichiers blob.

- Pour plus d’informations sur la manipulation par programme des objets blob, voir l’article [Utilisation du stockage d’objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md).
- Pour des informations générales sur le stockage Azure, consultez la [documentation du stockage](https://azure.microsoft.com/documentation/services/storage/).
- Pour des informations générales sur les services cloud Azure, consultez la [documentation des services cloud](http://azure.microsoft.com/documentation/services/cloud-services/).
- Pour plus d’informations sur la programmation d’applications ASP.NET, consultez la page [ASP.NET](http://www.asp.net).

##Accès aux conteneurs d'objets blob dans le code

Pour accéder par programmation aux objets blob dans des projets de service cloud, vous devez ajouter les éléments suivants, s'ils ne sont pas déjà présents.

1. Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenez un objet `CloudStorageAccount` représentant les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));


3. Obtenez un objet `CloudBlobClient` pour référencer un conteneur dans votre compte de stockage.

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

4. Obtenez un objet `CloudBlobContainer` pour référencer un conteneur d’objets blob spécifique.

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

**REMARQUE :** placez tout le code ci-dessus avant celui des sections suivantes.

##Création d'un conteneur dans le code

**REMARQUE :** parmi les API qui effectuent des appels au stockage Azure, certaines sont asynchrones. Pour plus d’informations, voir l’article [Programmation asynchrone avec Async et Await](http://msdn.microsoft.com/library/hh191443.aspx). Le code ci-dessous suppose que vous utilisez les méthodes de programmation asynchrones.

Pour créer un conteneur dans votre compte de stockage, il suffit d’ajouter un appel à `CreateIfNotExistsAsync` comme dans le code suivant :

	// Get a reference to a CloudBlobContainer with the variable name 'container'
    // as described in the "Access blob containers in code" section.

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();    


Pour rendre publics les fichiers présents dans le conteneur, vous pouvez configurer le conteneur en conséquence à l’aide du code suivant :

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Tous les utilisateurs d’Internet peuvent afficher les objets blob d’un conteneur public, mais seuls ceux possédant la clé d’accès adéquate peuvent les modifier ou les supprimer.

## Charger un objet blob dans un conteneur

Le stockage d’objets blob Azure prend en charge les objets blob de blocs et de page. Dans la plupart des cas, il est recommandé d’utiliser le type d’objet blob de blocs.

Pour télécharger un fichier vers un objet blob de blocs, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d’objet blob de blocs. Lorsque vous disposez d’une référence d’objet blob, vous pouvez charger un flux de données dans cet objet à l’aide de la méthode `UploadFromStream`. Si l’objet blob n’existe pas, cette opération entraîne sa création. S’il existe, il est remplacé. L’exemple suivant illustre le chargement d’un objet blob dans un conteneur en partant du principe que le conteneur existe déjà.

	// Get a reference to a CloudBlobContainer with the variable name 'container' as described in 
    // the "Access blob containers in code" section.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## Création d'une liste d'objets blob dans un conteneur

Pour créer une liste d’objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur. Vous pouvez ensuite appeler la méthode `ListBlobs` du conteneur pour récupérer les objets blob et/ou les répertoires qu’il contient. Pour accéder à l’ensemble complet de propriétés et de méthodes d’un `IListBlobItem` renvoyé, vous devez le convertir en objet `CloudBlockBlob`, `CloudPageBlob` ou `CloudBlobDirectory`. Si vous ne connaissez pas le type, vous pouvez lancer une vérification de type pour déterminer la cible de l’appel. Le code suivant illustre la récupération et la génération de l’URI de chaque élément du conteneur `photos` :

	// Get a reference to a CloudBlobContainer with the variable name 'container' as described in 
    // the "Access blob containers in code" section.

	// Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;
			
			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

Comme indiqué précédemment, le service BLOB suit également le concept de répertoires dans des conteneurs. Vous pouvez donc organiser vos objets blob selon une structure proche de celle des dossiers. Par exemple, prenez l’ensemble d’objets blob de blocs suivant, situé dans un conteneur nommé `photos` :

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Lorsque vous appelez `ListBlobs` sur le conteneur (comme dans l’exemple ci-dessus), la collection renvoyée contient les objets `CloudBlobDirectory` et `CloudBlockBlob` représentant les répertoires et objets blob contenus au niveau supérieur. Voici le résultat obtenu :

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Si vous le souhaitez, vous pouvez régler le paramètre `UseFlatBlobListing` de la méthode`ListBlobs` sur `true`. Cela permet de renvoyer chaque objet blob en tant que `CloudBlockBlob`, indépendamment du répertoire. Voici ce que donnerait l’appel à `ListBlobs` :

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

et voici les résultats :

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Pour plus d’informations, consultez [CloudBlobContainer.ListBlobs][].

## Télécharger des objets blob

Pour télécharger des objets blob, récupérez une référence d’objet blob puis appelez la méthode `DownloadToStream`. L’exemple suivant utilise la méthode `DownloadToStream` pour transférer le contenu de l’objet blob dans un objet de flux que vous pouvez enregistrer sous forme de fichier local.

	// Get a reference to a CloudBlobContainer with the variable name 'container' as described in 
    // the "Access blob containers in code" section.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Vous pouvez également utiliser la méthode `DownloadToStream` pour télécharger les contenus d’un objet blob en tant que chaîne de texte.

	// Get a reference to a CloudBlobContainer with the variable name 'container' as described in 
    // the "Access blob containers in code" section.

	// Get a reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Suppression d’objets blob

Pour supprimer un objet blob, obtenez une référence d’objet blob puis appelez la méthode `Delete`.

	// Get a reference to a CloudBlobContainer with the variable name 'container' as described in 
    // the "Access blob containers in code" section.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete(); 


## Création d’une liste d’objets blob dans des pages de manière asynchrone

Si vous répertoriez un grand nombre d’objets blob ou que vous souhaitez contrôler le nombre de résultats renvoyés lors d’une opération de création de liste, vous pouvez répertorier les objets blob dans des pages de résultats. Cet exemple montre comment renvoyer les résultats dans des pages de manière asynchrone afin que l’exécution ne soit pas bloquée lorsqu’un ensemble volumineux de résultats est attendu.

Cet exemple montre une liste simple d’objets blob. Vous pouvez également obtenir une liste hiérarchisée en attribuant la valeur `false` au paramètre `useFlatBlobListing` de la méthode `ListBlobsSegmentedAsync`.

Comme l’exemple de méthode appelle une méthode asynchrone, il doit être précédé du mot clé `async` et renvoyer un objet `Task`. Le mot clé d’attente spécifié pour la méthode `ListBlobsSegmentedAsync` suspend l’exécution de l’exemple de méthode jusqu’à la fin de la tâche de création de la liste.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=August15_HO7-->