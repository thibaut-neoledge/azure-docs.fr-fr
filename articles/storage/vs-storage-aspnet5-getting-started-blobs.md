<properties 
	pageTitle="Prise en main d'Azure Storage" 
	description="Prise en main du stockage d’objets blob Azure dans un projet ASP.NET 5 dans Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="kempb"/>

# Prise en main d'Azure Storage (projets ASP.NET 5)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

Azure Blob storage is a service for storing large amounts of unstructured data that can be accessed from anywhere in the world via HTTP or HTTPS. Les objets blob peuvent être de toutes tailles. Il peut s'agir d'images, de fichiers audio ou vidéo, de données brutes ou de fichiers de documents.

Pour commencer, vous devez créer un compte de stockage Azure, puis un ou plusieurs conteneurs pour le stockage. Par exemple, vous pouvez créer un compte de stockage appelé « Scrapbook », puis créer des conteneurs pour ce compte de stockage, un que vous appellerez « images » pour stocker les images et un autre nommé « audio » pour vos fichiers audio. Une fois que vous avez créé les conteneurs, vous pouvez y charger des fichiers blob. Pour plus d’informations sur la manipulation par programme des objets blob, voir l’article [Utilisation du stockage d’objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md "Utilisation du stockage d’objets blob à partir de .NET").

Pour accéder par programme aux objets blob des projets ASP.NET 5, vous devez ajouter les éléments suivants, s'ils ne sont pas déjà présents.

1. Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;

2. Utilisez le code ci-dessous pour obtenir le paramètre de configuration.

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Obtention de la chaîne de connexion de stockage
Avant de commencer à manipuler des objets blob, vous devez obtenir la chaîne de connexion pour le compte de stockage dans lequel résideront les objets blob. Vous pouvez utiliser le type **CloudStorageAccount** pour représenter les informations de votre compte de stockage. Si vous travaillez dans un projet ASP.NET 5, vous pouvez appeler la méthode get de l'objet Configuration pour obtenir votre chaîne de connexion de stockage et les informations de votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####Créer un conteneur.
De la même manière que les fichiers résident dans des dossiers, le stockage des objets blob s'effectue dans des conteneurs. Vous pouvez utiliser un objet **CloudBlobClient** pour référencer un conteneur existant, ou appeler la méthode CreateCloudBlobClient() pour créer un conteneur.

Le code suivant montre comment créer un conteneur de stockage d'objets blob. Ce code commence par créer un objet **BlobClient** vous permettant d'accéder aux fonctions de l'objet, notamment pour créer un conteneur de stockage. Il tente ensuite de référencer le conteneur de stockage nommé « mycontainer ». S'il ne trouve aucun conteneur ainsi nommé, il en crée un.

**REMARQUE** : les API qui effectuent des appels vers le stockage Azure dans ASP.NET 5 sont asynchrones. Pour plus d’informations, voir l’article [Programmation asynchrone avec Async et Await](http://msdn.microsoft.com/library/hh191443.aspx). Le code ci-dessous suppose que les méthodes de programmation asynchrone sont utilisées.

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “mycontainer.”
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();    

Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient publics, vous pouvez configurer le conteneur en utilisant le code suivant :

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

**REMARQUE** : utilisez l’ensemble de ce code devant le code des sections suivantes.

#####Charger un blob dans un conteneur.
Pour télécharger un fichier blob vers un conteneur, obtenez la référence du conteneur et utilisez-la pour celle de l'objet blob. Dès que vous disposez d’une référence d'objet blob, vous pouvez télécharger un flux de données vers cet objet en appelant la méthode **UploadFromStreamAsync()**. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. L’exemple suivant illustre le téléchargement d’un objet blob dans un conteneur en partant du principe que le conteneur existe déjà.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

#####Création d'une liste d'objets blob dans un conteneur
Pour créer une liste d'objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur. Vous pouvez ensuite appeler la méthode **ListBlobsSegmentedAsync()** du conteneur pour récupérer les objets blob et/ou les répertoires figurant dans ce conteneur. Pour accéder aux nombreuses propriétés et méthodes d’une **IListBlobItem** renvoyée, vous devez l’appeler vers un objet **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Si vous ne savez pas de quel type d'objet blob il s'agit, vous pouvez lancer une vérification de type pour déterminer la cible de l'appel. Le code suivant illustre la récupération et la génération de l'URI de chaque élément d'un conteneur.

	BlobContinuationToken token = null;
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
            token = resultSegment.ContinuationToken;

            foreach (IListBlobItem item in resultSegment.Results)
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
        } while (token != null);

D'autres méthodes permettent de répertorier le contenu d'un conteneur d'objets blob. Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md/#list-blob).

#####Téléchargement d'un objet blob
Pour télécharger un objet blob, commencez par en obtenir la référence, puis appelez la méthode **DownloadToStreamAsync()**. L’exemple ci-après utilise la méthode **DownloadToStreamAsync()** pour transférer le contenu des objets blob vers un objet de flux que vous pouvez ensuite enregistrer sous la forme d’un fichier local.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

Il existe plusieurs façons d'enregistrer les objets blob sous forme de fichiers. Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md/#download-blobs).

#####Supprimer un blob.
Pour supprimer un objet blob, commencez par en obtenir la référence, puis appelez la méthode **DeleteAsync()**.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

[En savoir plus sur Azure Storage](http://azure.microsoft.com/documentation/services/storage/) Voir aussi [Consultation et gestion des ressources de stockage avec l’Explorateur de serveurs](http://msdn.microsoft.com/library/azure/ff683677.aspx) et [ASP.NET 5](http://www.asp.net/vnext) (en anglais).

<!---HONumber=58--> 