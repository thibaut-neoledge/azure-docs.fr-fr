<properties 
	pageTitle="Utilisation du stockage d'objets blob à partir de .NET | Azure" 
	description="Découvrez comment utiliser le stockage d'objets blob Microsoft Azure pour télécharger, charger, répertorier et supprimer le contenu des objets blob. Les exemples sont écrits en C#." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>

# Utilisation du stockage d'objets blob à partir de .NET

Ce guide explique le déroulement des scénarios courants dans le cadre de l'utilisation du service de stockage d'objets blob Azure. Les exemples ont été écrits en C# et utilisent la bibliothèque du client de stockage Azure pour .NET. Les scénarios traités incluent le **chargement**, le **téléchargement**, l'**énumération** et la **suppression** d'objets blob. Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes][].

> [AZURE.NOTE] Ce guide cible la bibliothèque cliente de stockage Azure .NET 2.x et les versions ultérieures. Nous vous recommandons d'utiliser la version 4.x disponible via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou dans le [Kit de développement logiciel (SDK) Azure pour .NET](/fr-FR/downloads/). Consultez la rubrique [Procédure : Accès au stockage de files d'objets blob][] ci-dessous pour plus d'informations sur l'obtention de la bibliothèque cliente de stockage.

##Sommaire

-   [Présentation du stockage d'objets blob][]
-   [Concepts][]
-   [Création d'un compte Azure Storage][]
-   [Configuration d'une chaîne de connexion de stockage][]
-   [Procédure : Accès au stockage d'objets blob par programme][]
-   [Procédure : Création d'un conteneur][]
-   [Procédure : Téléchargement d'un objet blob dans un conteneur][]
-   [Procédure : Création d'une liste d'objets blob dans un conteneur][]
-   [Procédure : Téléchargement d'objets blob][]
-   [Procédure : Suppression d'objets blob][]
-   [Procédure : Création d'une liste d'objets blob dans des pages de manière asynchrone][]
-   [Étapes suivantes][]

[AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

[AZURE.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"></a>Procédure : Accès au stockage d'objets blob par programme

###Obtention de l'assembly

Nous vous recommandons d'utiliser NuGet pour obtenir l'assembly `Microsoft.WindowsAzure.Storage.dll`. Cliquez avec le bouton droit de la souris sur votre projet dans l'**Explorateur de solutions** et sélectionnez **Gérer les packages NuGet**.  Effectuez une recherche en ligne sur " WindowsAzure.Storage ", puis cliquez sur **Installer** pour lancer l'installation du package Azure Storage et de ses dépendances.

`Microsoft.WindowsAzure.Storage.dll` est également inclus dans le Kit de développement logiciel (SDK) Azure pour .NET, téléchargeable à partir du <a href="http://www.windowsazure.com/fr-FR/develop/net/#">centre de développement .NET</a>. L'assembly est installé dans le répertoire `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

###Déclarations d'espace de noms
Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Assurez-vous de bien référencer l'assembly `Microsoft.WindowsAzure.Storage.dll`.

###Récupération de votre chaîne de connexion
Vous pouvez utiliser le type **CloudStorageAccount** pour représenter vos informations de compte de stockage. Si vous utilisez un modèle de projet Azure et/ou qu'une référence pointe vers Microsoft.WindowsAzure.CloudConfigurationManager, vous pouvez utiliser le type **CloudConfigurationManager** pour extraire votre chaîne de connexion de stockage, ainsi que les informations de votre compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si vous créez une application sans référence pointant vers Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de connexion est située dans le fichier `web.config` ou `app.config` comme indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour récupérer la chaîne de connexion. Vous devez ajouter une référence pointant vers System.Configuration.dll à votre projet, ainsi qu'une autre déclaration d'espace de noms :

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Un type **CloudBlobClient** vous permet de récupérer des objets représentant des conteneurs et des objets blob stockés dans le service de stockage d'objets blob. Le code suivant crée un objet **CloudBlobClient** en utilisant l'objet de compte de stockage récupéré ci-dessus :

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Dépendances ODataLib
Les dépendances ODataLib de la bibliothèque de client de stockage pour .NET sont résolues via les packages ODataLib (version 5.0.2) disponibles avec NuGet et non pas avec les services de données WCF. Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet. Les packages ODataLib sont [OData], [Edm] et [Spatial].

## <a name="create-container"> </a>Procédure : Création d'un conteneur

Chaque objet blob du stockage Azure doit résider dans un conteneur. Cet exemple montre comment créer un conteneur, si celui-ci n'existe pas encore :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient publics, vous pouvez configurer le conteneur en utilisant le code suivant :

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Tous les utilisateurs d'Internet peuvent afficher les objets blob d'un conteneur public, mais seuls ceux possédant la clé d'accès adéquate peuvent les modifier ou les supprimer.

## <a name="upload-blob"></a>Procédure : Téléchargement d'un objet blob dans un conteneur

Le service de stockage d'objets blob Azure prend en charge les objets blob de blocs et de page. Dans la plupart des cas, il est recommandé d'utiliser le type d'objet blob de blocs.

Pour télécharger un fichier vers un objet blob de blocs, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d'objet blob de blocs. Avec cette référence d'objet blob, vous pouvez télécharger un flux de données vers cet objet en appelant la méthode **UploadFromStream**. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. L'exemple suivant illustre le téléchargement d'un objet blob dans un conteneur en partant du principe que le conteneur existe déjà.

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

##<a name="list-blob"></a>Procédure : Création d'une liste d'objets blob dans un conteneur

Pour créer une liste d'objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur. Vous pouvez ensuite utiliser la méthode **ListBlobs** du conteneur pour récupérer les objets blob et/ou les répertoires qu'il contient. Pour accéder aux nombreuses propriétés et méthodes d'une **IListBlobItem** renvoyée, vous devez l'appeler vers un objet **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Si vous ne connaissez pas le type, vous pouvez lancer une vérification de type pour déterminer la cible de l'appel. Le code suivant illustre la récupération et la génération de l'URI de chaque élément du conteneur `photos` :

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

Comme indiqué précédemment, le service d'objet blob suit également le concept de répertoires dans des conteneurs. Vous pouvez donc organiser vos objets blob selon une structure proche de celle des dossiers. Par exemple, prenez l'ensemble d'objets blob de blocs suivant, situé dans un conteneur nommé `photos` :

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Lorsque vous appelez **ListBlobs** pour le conteneur 'photos' (comme dans l'exemple ci-dessus), la collection renvoyée contient les objets **CloudBlobDirectory** et **CloudBlockBlob** qui représentent les répertoires et objets blob contenus au niveau supérieur. Voici le résultat obtenu :

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Vous pouvez également définir le paramètre **UseFlatBlobListing** de la méthode **ListBlobs** sur 
**true**. Ceci aura pour résultat le renvoi de chaque objet blob en tant que **CloudBlockBlob**, indépendamment du répertoire. Voici ce que donnerait l'appel vers **ListBlobs**:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}
 et voici les résultats :

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Pour plus d'informations, consultez [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"></a>Procédure : Téléchargement d'objets blob

Pour télécharger des objets blob, commencez par récupérer une référence d'objet blob, puis appelez la méthode **DownloadToStream**. L'exemple suivant utilise la méthode **DownloadToStream** pour transférer les contenus d'objets blob vers un objet de flux pouvant être rendu persistant dans un fichier local.

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

Vous pouvez également utiliser la méthode **DownloadToStream** pour télécharger les contenus d'un objet blob en tant que chaîne de texte.

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

##<a name="delete-blobs"></a>Procédure : Suppression d'objets blob

Pour supprimer un objet blob, commencez par obtenir une référence d'objet blob, puis appelez la méthode **Delete** associée.

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


##<a name="list-blobs-async"></a>Procédure : Création d'une liste d'objets blob dans des pages de manière asynchrone

Si vous répertoriez un grand nombre d'objets blob ou que vous souhaitez contrôler le nombre de résultats renvoyés lors d'une opération de création de liste, vous pouvez répertorier les objets blob dans des pages de résultats. Cet exemple montre comment renvoyer les résultats dans des pages de manière asynchrone afin que l'exécution ne soit pas bloquée lorsqu'un ensemble volumineux de résultats est attendu.

Cet exemple montre une liste plate d'objets blob. Vous pouvez également avoir une liste hiérarchique en définissant le paramètre `useFlatBlobListing` de la méthode **ListBlobsSegmentedAsync** sur `false`.

Comme l'exemple de méthode appelle une méthode asynchrone, il doit être précédé du mot clé `async` et doit renvoyer un objet **Task**. Le mot clé d'attente spécifié pour la méthode **ListBlobsSegmentedAsync** suspend l'exécution de l'exemple de méthode jusqu'à ce que la tâche de création de liste soit terminée.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //List blobs in pages.
        Console.WriteLine("List blobs in pages:");

        //List blobs with a paging size of 10, for the purposes of the example. 
		//The first call does not include the continuation token.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerate the result segment returned.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token, if there are additional pages of results.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Check whether there are more results and list them in pages of 10 while a continuation token is returned.
        while (continuationToken != null)
        {
            //This overload allows control of the page size. 
			//You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the next continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les bases du stockage d'objets blob, suivez ces liens pour apprendre des tâches de stockage plus complexes.
<ul>
<li>Pour plus d'informations sur les API disponibles, consultez la documentation de référence des services d'objets blob :
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Référence de la bibliothèque cliente de stockage pour .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/fr-FR/library/windowsazure/dd179355">Référence d'API REST</a></li>
  </ul>
</li>
<li>Pour plus d'informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page <a href="http://msdn.microsoft.com/fr-FR/library/windowsazure/gg433040.aspx">Stockage et accessibilité des données dans Azure</a>.</li>
<li>Découvrez comment simplifier le code que vous écrivez pour travailler avec Azure Storage à l'aide du <a href="../websites-dotnet-webjobs-sdk/">Kit de développment logiciel WebJobs Azure.</li>
<li>Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
  <ul>
    <li>Utilisez le <a href="/fr-FR/documentation/articles/storage-dotnet-how-to-use-tables/">stockage de table</a> pour stocker des données structurées.</li>
    <li>Utilisez le <a href="/fr-FR/documentation/articles/storage-dotnet-how-to-use-queues/">stockage de files d'attente</a> pour stocker des données non structurées.</li>
    <li>Utilisez une <a href="/fr-FR/documentation/articles/sql-database-dotnet-how-to-use/">base de données SQL</a> pour stocker des données relationnelles.</li>
  </ul>
</li>
</ul>

  [Étapes suivantes]: #next-steps
  [Présentation du stockage d'objets blob]: #what-is
  [Concepts]: #concepts
  [Création d'un compte Azure Storage]: #create-account
  [Configuration d'une chaîne de connexion de stockage]: #setup-connection-string
  [Procédure : Accès au stockage d'objets blob par programme]: #configure-access
  [Procédure : Création d'un conteneur]: #create-container
  [Procédure : Téléchargement d'un objet blob dans un conteneur]: #upload-blob
  [Procédure : Création d'une liste d'objets blob dans un conteneur]: #list-blob
  [Procédure : Téléchargement d'objets blob]: #download-blobs
  [Procédure : Suppression d'objets blob]: #delete-blobs
  [Procédure : Création d'une liste d'objets blob dans des pages de manière asynchrone]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/fr-FR/library/windowsazure/gg433040.aspx
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuration des chaînes de connexion]: http://msdn.microsoft.com/fr-FR/library/windowsazure/ee758697.aspx
  [Référence de bibliothèque cliente .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Référence d'API REST]: http://msdn.microsoft.com/fr-FR/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
<!--HONumber=42-->
