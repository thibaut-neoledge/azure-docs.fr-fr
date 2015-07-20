<properties 
	pageTitle="Prise en main d'Azure Storage" 
	description="Prise en main du stockage d’objets blob Azure au sein d’un projet ASP.NET dans Visual Studio" 
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
	ms.date="06/24/2015" 
	ms.author="patshea123"/>

# Prise en main d'Azure Storage (projets ASP.NET)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

Azure Blob storage is a service for storing large amounts of unstructured data that can be accessed from anywhere in the world via HTTP or HTTPS. Les objets blob peuvent être de toutes tailles. Il peut s'agir d'images, de fichiers audio ou vidéo, de données brutes ou de fichiers de documents.

Pour commencer, vous devez créer un compte de stockage Azure, puis un ou plusieurs conteneurs pour le stockage. Par exemple, vous pouvez créer un compte de stockage appelé « Scrapbook », puis créer des conteneurs pour ce compte de stockage, un que vous appellerez « images » pour stocker les images et un autre nommé « audio » pour vos fichiers audio. Une fois que vous avez créé les conteneurs, vous pouvez y charger des fichiers blob. Pour plus d’informations sur la manipulation par programme des objets blob, voir l’article [Utilisation du stockage d’objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md/ "Utilisation du stockage d’objets blob à partir de .NET").

Cet article explique le déroulement des scénarios courants dans le cadre de l’utilisation du service de stockage d’objets blob Azure. Les exemples ont été écrits en C# et utilisent la bibliothèque du client de stockage Azure pour .NET. Les scénarios traités incluent le **chargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d’une source)** et la **suppression** d’objets blob.

Pour plus d’informations sur les projets ASP.NET, consultez le site [ASP.NET](http://www.asp.net).

## Accès au stockage d’objets blob par programme

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Déclarations d’espace de noms
Ajoutez les déclarations d’espace de noms suivantes en haut de chaque fichier C# dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Prenez soin de bien référencer l’assembly `Microsoft.WindowsAzure.Storage.dll`.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

Un type **CloudBlobClient** vous permet de récupérer des objets représentant des conteneurs et des objets blob stockés dans le serveur de stockage d’objets blob. Le code suivant crée un objet **CloudBlobClient** en utilisant l’objet de compte de stockage récupéré ci-dessus :

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

[AZURE.INCLUDE [storage-dotnet-odatalib-dependencies](../../includes/storage-dotnet-odatalib-dependencies.md)]

## Création d'un conteneur

Chaque objet blob du stockage Azure doit résider dans un conteneur. Cet exemple montre comment créer un conteneur, si celui-ci n’existe pas encore :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d’accès de stockage pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient publics, vous pouvez configurer le conteneur en utilisant le code suivant :

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Tous les utilisateurs d’Internet peuvent afficher les objets blob d’un conteneur public, mais seuls ceux possédant la clé d’accès adéquate peuvent les modifier ou les supprimer.

## Charger un objet blob dans un conteneur

Le service de stockage d’objets blob Azure prend en charge les objets blob de blocs et de page. Dans la plupart des cas, il est recommandé d’utiliser le type d’objet blob de blocs.

Pour télécharger un fichier vers un objet blob de blocs, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d’objet blob de blocs. Lorsque vous disposez d’une référence d’objet blob, vous pouvez télécharger un flux de données vers cet objet en appelant la méthode **UploadFromStream**. Si l’objet blob n’existe pas, cette opération entraîne sa création. S’il existe, il est remplacé. L’exemple suivant illustre le chargement d’un objet blob dans un conteneur en partant du principe que le conteneur existe déjà.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## Création d'une liste d'objets blob dans un conteneur

Pour créer une liste d’objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur. Vous pouvez ensuite utiliser la méthode **ListBlobs** du conteneur pour récupérer les objets blob et/ou les répertoires qu’il contient. Pour accéder aux nombreuses propriétés et méthodes d’une **IListBlobItem** renvoyée, vous devez l’appeler vers un objet **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Si vous ne connaissez pas le type, vous pouvez lancer une vérification de type pour déterminer la cible de l’appel. Le code suivant illustre la récupération et la génération de l’URI de chaque élément du conteneur `photos` :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client. 
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

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

Lorsque vous appelez **ListBlobs** pour le conteneur « photos » (comme dans l’exemple ci-dessus), la collection renvoyée contient les objets **CloudBlobDirectory** et **CloudBlockBlob** qui représentent les répertoires et objets blob contenus au niveau supérieur. Voici le résultat obtenu :

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Vous pouvez également affecter au paramètre **UseFlatBlobListing** de la méthode **ListBlobs** la valeur **true**. Ceci aura pour résultat le renvoi de chaque objet blob en tant que **CloudBlockBlob**, indépendamment du répertoire. Voici ce que donnerait l’appel vers **ListBlobs** :

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

## Télécharger des blobs.

Pour télécharger des objets blob, commencez par récupérer une référence d’objet blob, puis appelez la méthode **DownloadToStream**. L’exemple suivant utilise la méthode **DownloadToStream** pour transférer les contenus d’objets blob vers un objet de flux pouvant être rendu persistant dans un fichier local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Vous pouvez également utiliser la méthode **DownloadToStream** pour télécharger les contenus d’un objet blob en tant que chaîne de texte.

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Suppression d’objets blob

Pour supprimer un objet blob, commencez par obtenir une référence d’objet blob, puis appelez la méthode **Delete** associée.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete(); 


## Création d’une liste d’objets blob dans des pages de manière asynchrone

Si vous répertoriez un grand nombre d’objets blob ou que vous souhaitez contrôler le nombre de résultats renvoyés lors d’une opération de création de liste, vous pouvez répertorier les objets blob dans des pages de résultats. Cet exemple montre comment renvoyer les résultats dans des pages de manière asynchrone afin que l’exécution ne soit pas bloquée lorsqu’un ensemble volumineux de résultats est attendu.

Cet exemple montre une liste plate d’objets blob. Vous pouvez également obtenir une liste hiérarchique en affectant au paramètre `useFlatBlobListing` de la méthode **ListBlobsSegmentedAsync** la valeur `false`.

Comme l’exemple de méthode appelle une méthode asynchrone, il doit être précédé du mot clé `async` et doit retourner un objet **Task**. Le mot clé d’attente spécifié pour la méthode **ListBlobsSegmentedAsync** suspend l’exécution de l’exemple de méthode jusqu’à ce que la tâche de création de liste soit terminée.

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

Maintenant que vous connaissez les bases du stockage d’objets blob, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes. <ul> <li>Pour plus d’informations sur les API disponibles, consultez la documentation de référence des services d’objets blob : <ul> <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731">Référence de la bibliothèque cliente de stockage pour .NET</a> </li> <li><a href="http://msdn.microsoft.com/library/azure/dd179355">Référence d’API REST</a></li> </ul> </li> <li>Pour plus d’informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page <a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Stockage et accessibilité des données dans Azure</a>.</li> <li>Découvrez comment simplifier le code que vous écrivez pour travailler avec Azure Storage à l’aide du <a href="../websites-dotnet-webjobs-sdk/">￼Kit de développement logiciel (SDK) Azure WebJobs</li>. <li>Pour plus d’informations sur les autres options de stockage de données dans Azure, consultez d’autres guides de fonctionnalités. <ul> <li>Utilisez le <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">stockage de table</a> pour stocker des données structurées.</li> <li>Utilisez le <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">stockage de files d’attente</a> pour stocker des données non structurées.</li> <li>Utilisez une <a href="/documentation/articles/sql-database-dotnet-how-to-use/">base de données SQL</a> pour stocker des données relationnelles.</li> </ul> </li> </ul>

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
 

<!---HONumber=July15_HO2-->