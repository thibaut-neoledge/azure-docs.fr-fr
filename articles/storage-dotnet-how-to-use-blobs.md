<properties urlDisplayName="Blob Service" pageTitle="Utilisation du stockage d'objets blob à partir de .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Découvrez comment utiliser le stockage d'objets blob Microsoft Azure pour télécharger, charger, répertorier et supprimer le contenu des objets blob. Les exemples sont écrits en C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# Utilisation du stockage d'objets blob à partir de .NET

Ce guide décrit le déroulement des scénarios courants dans le cadre de l'utilisation du
service de stockage d'objets blob Azure. Les exemples sont écrits en code C\# et
utilisent la bibliothèque cliente de stockage Azure pour .NET. Les scénarios traités incluent
**le chargement**, **la création de listes**, **le téléchargement** et **la suppression** d'objets blob. Pour
plus d'informations sur les objets blob, consultez la section [Étapes suivantes][].

> [WACOM.NOTE] Ce guide cible la bibliothèque cliente de stockage Azure .NET 2.x et les versions ultérieures. La version recommandée est la bibliothèque cliente de stockage version 4.x, disponible via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou dans le [Kit de développement logiciel (SDK) Azure pour .NET](/fr-fr/downloads/). Pour plus d'informations sur l'obtention de la bibliothèque cliente de stockage, consultez la rubrique ci-dessous [ Accès au stockage d'objets blob par programme][].

##Sommaire

-   [Présentation du stockage d'objets blob][]
-   [Concepts][]
-   [Création d'un compte Azure Storage][]
-   [Configuration d'une chaîne de connexion de stockage][]
-   [ Accès au stockage d'objets blob par programme][]
-   [ Création d'un conteneur][]
-   [ Téléchargement d'un objet blob dans un conteneur][]
-   [ Création d'une liste d'objets blob dans un conteneur][]
-   [ Téléchargement d'objets blob][]
-   [ Suppression d'objets blob][]
-   [ Création d'une liste d'objets blob dans des pages de manière asynchrone][]
-   [Étapes suivantes][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>Création d'un compte Azure Storage
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a>Configuration d'une chaîne de connexion de stockage

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>Accès au stockage d'objets blob par programme

###Obtention de l'assembly
Nous vous recommandons d'utiliser NuGet pour obtenir l'assembly " Microsoft.WindowsAzure.Storage.dll ". Cliquez avec le bouton droit sur votre projet dans l'**Explorateur de solutions** et sélectionnez **Gérer les packages NuGet**.  Effectuez une recherche en ligne sur " WindowsAzure.Storage ", puis cliquez sur **Installer** pour installer le package Azure Storage et ses dépendances.

" Microsoft.WindowsAzure.Storage.dll " est également inclus dans le Kit de développement logiciel (SDK) Azure pour .NET, téléchargeable dans le <a href="http://www.windowsazure.com/fr-fr/develop/net/#">Centre de développement .NET.</a>. L'assembly est installé dans le répertoire " %Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<version-sdk>\ref\ ".

###Déclarations d'espace de noms
Ajoutez les déclarations d'espace de noms suivantes au début de chaque fichier C\#
dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Assurez-vous de bien référencer l'assembly " Microsoft.WindowsAzure.Storage.dll ".

###Récupération de votre chaîne de connexion
Vous pouvez utiliser le type **CloudStorageAccount** pour représenter 
les informations de votre compte de stockage. Si vous utilisez un 
modèle de projet Windows Azure et/ou qu'une référence pointe vers l'espace de noms 
Microsoft.WindowsAzure.CloudConfigurationManager, vous 
pouvez utiliser le type **CloudConfigurationManager**
pour extraire votre chaîne de connexion de stockage, ainsi que les informations de
votre compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si vous créez une application sans référence pointant vers Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de connexion est située dans le fichier " web.config " ou " app.config " comme indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour extraire la chaîne de connexion.  Vous devez ajouter une référence pointant vers System.Configuration.dll à votre projet, ainsi qu'une autre déclaration d'espace de noms :

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Un type **CloudBlobClient** vous permet de récupérer des objets représentant
des conteneurs et des objets blob stockés dans le serveur de stockage d'objets blob. Exemple
Le code suivant crée un objet **CloudBlobClient** en utilisant l'objet de compte de
stockage récupéré ci-dessus :

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Dépendances ODataLib
Les dépendances ODataLib de la bibliothèque cliente de stockage pour .NET sont résolues via les packages ODataLib (version 5.0.2) disponibles avec NuGet et non pas avec les services de données WCF.  Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet.  Les packages ODataLib spécifiques sont [OData], [Edm] et [Spatial].

## <a name="create-container"> </a>Création d'un conteneur

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

## <a name="upload-blob"> </a>Téléchargement d'un objet blob dans un conteneur

Le service de stockage d'objets blob Azure prend en charge les objets blob de blocs et de page.  Dans la plupart des cas, il est recommandé d'utiliser le type d'objet blob de blocs.

Pour télécharger un fichier vers un objet blob de blocs, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d'objet blob de blocs. Lorsque vous disposez d'une référence d'objet blob, vous pouvez télécharger un flux de données vers cet objet en appelant la méthode **UploadFromStream**. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. L'exemple suivant illustre le téléchargement d'un objet blob dans un conteneur en partant du principe que le conteneur existe déjà.

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

##<a name="list-blob"> </a>Création d'une liste d'objets blob dans un conteneur

Pour créer une liste d'objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur. Vous pouvez ensuite utiliser la méthode **ListBlobs** du conteneur pour récupérer les objets blob et/ou les répertoires qu'il contient. Pour accéder aux nombreuses propriétés et méthodes d'une **IListBlobItem** renvoyée, vous devez effectuer un transtypage vers un objet **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**.  Si vous ne connaissez pas le type, vous pouvez lancer une vérification de type pour déterminer la cible de l'appel.  Le code suivant illustre la récupération et la génération de l'URI de chaque élément du conteneur 
" photos " :

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

Comme indiqué précédemment, le service d'objet blob suit également le concept de répertoires dans des conteneurs. Vous pouvez donc organiser vos objets blob selon une structure proche de celle des dossiers. Par exemple, prenez l'ensemble suivant d'objets blob de blocs, situé dans un conteneur nommé " photos " :

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Lorsque vous appelez la méthode **ListBlobs** sur le conteneur " photos " (comme dans l'exemple ci-dessus), l'ensemble renvoyé contient des objets **CloudBlobDirectory** et **CloudBlockBlob**
qui représentent les répertoires et objets blob contenus au niveau supérieur. Voici le résultat obtenu :

	Répertoire: https://<accountname>.blob.core.windows.net/photos/2010/
	Répertoire: https://<accountname>.blob.core.windows.net/photos/2011/
	Objet blob de blocs de longueur 505623 :: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Vous pouvez également définir le paramètre **UseFlatBlobListing** de la méthode **ListBlobs** sur **true**. Ceci aura pour résultat le renvoi de chaque objet blob en tant que **CloudBlockBlob**, indépendamment du répertoire.  Voici ce que donnerait l'appel de **ListBlobs** :

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

et voici les résultats :

	Objet blob de blocs de longueur 4 :: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Objet blob de blocs de longueur 314618 :: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Objet blob de blocs de longueur 522713 :: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Objet blob de blocs de longueur 4 :: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Objet blob de blocs de longueur 419048 :: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Objet blob de blocs de longueur 506388 :: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Objet blob de blocs de longueur 399751 :: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Objet blob de blocs de longueur 505623 :: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Pour plus d'informations, consultez [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a>Téléchargement d'objets blob

Pour télécharger des objets blob, commencez par récupérer une référence d'objet blob, puis appelez la méthode **DownloadToStream**. L'exemple suivant
utilise la méthode **DownloadToStream** pour transférer le contenu des objets blob
vers un objet de flux que vous pouvez enregistrer sous forme de fichier local.

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

##<a name="delete-blobs"> </a>Suppression d'objets blob

Pour supprimer un objet blob, obtenez une référence d'objet blob et appelez la méthode
**Delete** associée.

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


##<a name="list-blobs-async"> </a>Création d'une liste d'objets blob dans des pages de manière asynchrone

Si vous répertoriez un grand nombre d'objets blob ou que vous souhaitez contrôler le nombre de résultats renvoyés lors d'une opération de création de liste, vous pouvez répertorier les objets blob dans des pages de résultats. Cet exemple montre comment renvoyer les résultats dans des pages de manière asynchrone afin que l'exécution ne soit pas bloquée lorsqu'un ensemble volumineux de résultats est attendu.

Cet exemple montre une liste plate d'objets blob. Vous pouvez également avoir une liste hiérarchique en définissant le paramètre " useFlatBlobListing " de la méthode **ListBlobsSegmentedAsync** sur " false ".

Comme l'exemple de méthode appelle une méthode asynchrone, il doit être précédé du mot clé " async " et doit renvoyer un objet **Task**. Le mot clé d'attente spécifié pour la méthode **ListBlobsSegmentedAsync** suspend l'exécution de l'exemple de méthode jusqu'à ce que la tâche de création de liste soit terminée.

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
    <li><a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179355">Référence d'API REST</a></li>
  </ul>
</li>
<li>Pour plus d'informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx">Stockage et accessibilité des données dans Azure</a>.</li>
<li>Pour apprendre à utiliser Azure Storage dans les processus principaux relatifs à Sites Web Azure, consultez la page <a href="/fr-fr/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Prise en main du Kit de développement logiciel (SDK) WebJobs Azure</a>.</li>
<li>Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
  <ul>
    <li>Utilisation du <a href="/fr-fr/documentation/articles/storage-dotnet-how-to-use-tables/">stockage de tables</a> pour stocker les données structurées.</li>
    <li>Utilisation du <a href="/fr-fr/documentation/articles/storage-dotnet-how-to-use-queues/">stockage de files d'attente</a> pour stocker les données non structurées.</li>
    <li>Utilisation de la <a href="/fr-fr/documentation/articles/sql-database-dotnet-how-to-use/">base de données SQL</a> pour stocker les données relationnelles.</li>
  </ul>
</li>
</ul>

  [Étapes suivantes]: #next-steps
  [Présentation du stockage d'objets blob]: #what-is
  [Concepts]: #concepts
  [Création d'un compte Azure Storage]: #create-account
  [Configuration d'une chaîne de connexion de stockage]: #setup-connection-string
  [ Accès au stockage d'objets blob par programme]: #configure-access
  [ Création d'un conteneur]: #create-container
  [ Téléchargement d'un objet blob dans un conteneur]: #upload-blob
  [ Création d'une liste d'objets blob dans un conteneur]: #list-blob
  [ Téléchargement d'objets blob]: #download-blobs
  [ Suppression d'objets blob]: #delete-blobs
  [ Création d'une liste d'objets blob dans des pages de manière asynchrone]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuration des chaînes de connexion]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758697.aspx
  [Référence de bibliothèque cliente .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Référence d'API REST]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=35.1-->
