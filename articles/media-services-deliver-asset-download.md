<properties linkid="develop-media-services-how-to-guides-deliver-media-assets" urlDisplayName="Delivering Media Assets" pageTitle="How to Deliver Media Assets - Azure" metaKeywords="" description="Learn about options for delivering media assets that have been uploaded to Media Services in Azure. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver an Asset by Download" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Utilisation d'un élément multimédia par téléchargement

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Gestion des éléments multimédias][].

Cette rubrique présente les options disponibles pour fournir des éléments multimédias téléchargés sur Media Services. De nombreux scénarios d'application permettent de fournir du contenu Media Services. Il est possible de télécharger des éléments multimédias ou d'y accéder en utilisant un localisateur. Vous pouvez envoyer du contenu multimédia vers une autre application ou un autre fournisseur de contenu. Pour améliorer les performances et l'évolutivité, vous pouvez également fournir du contenu en utilisant un réseau de distribution de contenu (CDN), tel que Azure CDN.

Cet exemple montre comment télécharger des éléments multimédias depuis Media Services. Le code lance une requête sur les tâches associées au compte Media Services par ID de tâche et accède à l'ensemble **OutputMediaAssets** du compte (qui regroupe un ou plusieurs éléments multimédias en sortie, suite à l'exécution d'une tâche). Cet exemple indique comment télécharger des éléments multimédias en sortie depuis une tâche, mais il est possible d'appliquer la même approche pour télécharger d'autres éléments.

     
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }

Pour plus d'informations sur la fourniture d'éléments multimédias, consultez les rubriques suivantes :

-   [Fourniture des éléments multimédias avec Media Services pour .NET][]
-   [Fourniture des éléments multimédias avec l'API REST de Media Services][]

</p>
## Étapes suivantes

Cette rubrique vous a indiqué comment télécharger un élément multimédia depuis Azure Storage. Pour plus d'informations sur les autres méthodes possibles pour fournir des éléments multimédias, consultez la page [Fourniture de contenu de diffusion en continu][].

  [Gestion des éléments multimédias]: http://go.microsoft.com/fwlink/?LinkID=301815&clcid=0x409
  [Fourniture des éléments multimédias avec Media Services pour .NET]: http://msdn.microsoft.com/en-us/library/jj129575.aspx
  [Fourniture des éléments multimédias avec l'API REST de Media Services]: http://msdn.microsoft.com/en-us/library/jj129578.aspx
  [Fourniture de contenu de diffusion en continu]: http://go.microsoft.com/fwlink/?LinkID=301942
