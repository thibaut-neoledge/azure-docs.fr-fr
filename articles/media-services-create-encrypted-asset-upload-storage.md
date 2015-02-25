<properties pageTitle="Création d'un élément multimédia chiffré et chargement dans Azure Storage" description="Apprenez à obtenir du contenu multimédia dans Media Services en créant et en chargeant un élément multimédia chiffré." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>



<h1><a name="create-asset"> </a><span class="short header">Création d'un élément multimédia chiffré et chargement dans le stockage</span></h1>

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente était [Configuration de votre ordinateur pour le développement Media Services](http://go.microsoft.com/fwlink/?LinkID=301751&clcid=0x409).

Pour charger du contenu multimédia dans Media Services, créez d'abord un élément multimédia, ajoutez-y des fichiers, puis chargez l'élément. Ce processus est appelé réception de contenu.  

Lorsque vous créez des éléments multimédias, vous pouvez spécifier trois options de chiffrement différentes. 

- **AssetCreationOptions.None** : aucun chiffrement. Si vous voulez créer un élément multimédia non chiffré, vous devez définir cette option.
- **AssetCreationOptions.CommonEncryptionProtected** : pour les fichiers CENC (Common Encryption Protected). Par exemple, un ensemble de fichiers pour lesquels le chiffrement PlayReady est déjà activé. 
- **AssetCreationOptions.StorageEncrypted** : chiffrement de stockage. Chiffre un fichier d'entrée clair avant de le charger sur le stockage Azure.

> AZURE.NOTE
> Media Services fournit pour vos éléments multimédias un chiffrement de stockage sur disque, et non pas sur le réseau comme pour la gestion des droits numériques (DRM).

L'exemple de code qui suit effectue les opérations suivantes : 

- création d'un élément multimédia vide ;
- création d'une instance AssetFile que nous voulons associer à l'élément multimédia ;
- création d'une instance AccessPolicy qui définit les autorisations et la durée de l'accès à l'élément multimédia ;
- création d'une instance Locator qui fournit l'accès à l'élément multimédia ;
- chargement d'un seul fichier multimédia dans Media Services. 

<pre><code>
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
{
    var asset = _context.Assets.Create(assetName, assetCreationOptions);

    Console.WriteLine("Asset name: " + asset.Name);
    Console.WriteLine("Time created: " + asset.Created.Date.ToString());

    return asset;
}

static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
{
    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var fileName = Path.GetFileName(singleFilePath);

    var assetFile = asset.AssetFiles.Create(fileName);

    Console.WriteLine("Created assetFile {0}", assetFile.Name);
    Console.WriteLine("Upload {0}", assetFile.Name);

    assetFile.Upload(singleFilePath);
    Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

    return asset;
}
</code></pre>

Le code qui suit présente la création d'un élément multimédia et le chargement de plusieurs fichiers.

<pre><code>
static public IAsset CreateAssetAndUploadMultipleFiles( AssetCreationOptions assetCreationOptions, string folderPath)
{
    var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                        AccessPermissions.Write | AccessPermissions.List);
    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

    var blobTransferClient = new BlobTransferClient();
	blobTransferClient.NumberOfConcurrentTransfers = 20;
    blobTransferClient.ParallelTransferThreadCount = 20;

    blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

    var filePaths = Directory.EnumerateFiles(folderPath);

    Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

    if (!filePaths.Any())
    {
        throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
    }

    var uploadTasks = new List&lt;Task&gt;();
    foreach (var filePath in filePaths)
    {
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        Console.WriteLine("Created assetFile {0}", assetFile.Name);
                
        // It is recommended to validate AccestFiles before upload. 
        Console.WriteLine("Start uploading of {0}", assetFile.Name);
        uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
    }

    Task.WaitAll(uploadTasks.ToArray());
    Console.WriteLine("Done uploading the files");

    blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

    locator.Delete();
    accessPolicy.Delete();

    return asset;
}

static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
{
    if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
    {
        Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
    }
}

</code></pre>

<h2>Étapes suivantes</h2>
Maintenant que vous avez chargé un élément multimédia dans Media Services, consultez la rubrique [Obtention d'un processeur multimédia][].

[Obtention d'un processeur multimédia]: ../media-services-get-media-processor/


<!--HONumber=42-->
