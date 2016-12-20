---
title: "Télécharger des éléments multimédias"
description: "Découvrez comment télécharger des éléments multimédias sur votre ordinateur. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 25b74bdcdbc5ec5a134bb2f800cd687de6ebc1e7


---
# <a name="how-to-deliver-an-asset-by-download"></a>Fourniture d’un élément multimédia par téléchargement
Cette rubrique présente les options disponibles pour fournir des éléments multimédias chargés sur Media Services. De nombreux scénarios d'application permettent de fournir du contenu Media Services. Il est possible de télécharger des éléments multimédias ou d'y accéder en utilisant un localisateur. Vous pouvez envoyer du contenu multimédia vers une autre application ou un autre fournisseur de contenu. Pour améliorer les performances et l’évolutivité, vous pouvez également fournir du contenu en utilisant un réseau de distribution de contenu (CDN).

Cet exemple montre comment télécharger des éléments multimédias depuis Media Services sur un ordinateur local. Le code lance une requête sur les tâches associées au compte Media Services par ID de tâche et accède à l'ensemble **OutputMediaAssets** du compte (qui regroupe un ou plusieurs éléments multimédias en sortie, suite à l'exécution d'une tâche). Cet exemple indique comment télécharger des éléments multimédias en sortie depuis une tâche, mais il est possible d’appliquer la même approche pour télécharger d’autres éléments.

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
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

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



## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Voir aussi
[de diffusion de contenu en continu](media-services-deliver-streaming-content.md)




<!--HONumber=Nov16_HO3-->


