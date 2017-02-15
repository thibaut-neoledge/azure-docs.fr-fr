---
title: "Indexation de fichiers multimédias avec Azure Media Indexer 2 | Microsoft Docs"
description: "Azure Media Indexer permet de rendre le contenu de vos fichiers multimédias consultable et de générer une transcription en texte intégral de sous-titrages et de mots-clés. Cette rubrique explique comment utiliser Media Indexer 2 Preview."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 85d25525-a498-44eb-ae3a-2ca5ceb8e53d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/07/2016
ms.author: adsolank;juliako;
translationtype: Human Translation
ms.sourcegitcommit: 7065d5e87dbf47153bc89b37b56af81927b8b92b
ms.openlocfilehash: 902e872b224b267c989f741345efdd8ef3fa8ce3


---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexation de fichiers multimédias avec Azure Media Indexer 2 Preview
## <a name="overview"></a>Vue d'ensemble
Le processeur multimédia **Azure Media Indexer 2 Preview** permet d’effectuer des recherches dans les fichiers multimédias et le contenu et de générer des pistes de sous-titrage. Par rapport à la version précédente d’ [Azure Media Indexer](media-services-index-content.md), **Azure Media Indexer 2 Preview** effectue une indexation plus rapide et offre une prise en charge plus large des langues. Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (mandarin, simplifié), le portugais, l’arabe et le japonais.

Le processeur multimédia **Azure Media Indexer 2 Preview** est actuellement disponible en version préliminaire.

Cette rubrique explique comment créer des travaux d’indexation avec **Azure Media Indexer 2 Preview**.

> [!NOTE]
> Les considérations suivantes s'appliquent :
> 
> Indexer 2 n’est pas pris en charge dans Azure China et Azure Government.
> 
> Lors de l’indexation de contenu, veillez à utiliser des fichiers multimédias avec des mots clairs (sans musique de fond, bruit, effets ou sifflement du microphone). Voici quelques exemples de contenu approprié : des réunions, des conférences ou des présentations enregistrées. Le contenu suivant peut ne pas convenir à l’indexation : des films, des émissions de télévision, des fichiers avec du son et des effets sonores mélangés, du contenu mal enregistré avec un bruit de fond (sifflement).
> 
> 

Cette rubrique donne des informations détaillées sur **Azure Media Indexer 2 Preview** et illustre son utilisation avec le SDK Media Services pour .NET.

## <a name="input-and-output-files"></a>Fichiers d’entrée et de sortie
### <a name="input-files"></a>Fichiers d'entrée
Fichiers audio ou vidéo

### <a name="output-files"></a>Fichiers de sortie
Un travail d’indexation peut générer des fichiers de sous-titres dans les formats suivants :  

* **SAMI**
* **TTML**
* **WebVTT**

Les fichiers de sous-titre dans ces formats permettent de rendre un fichier audio et vidéo accessible aux malentendants.

## <a name="task-configuration-preset"></a>Configuration de la tâche (préconfiguration)
Lors de la création d’une tâche d’indexation avec **Azure Media Indexer 2 Preview**, vous devez spécifier une présélection de configuration.

Le code JSON suivant définit les paramètres disponibles.

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

## <a name="supported-languages"></a>Langues prises en charge
Azure Media Indexer 2 Preview prend en charge la reconnaissance vocale pour les langues suivantes (lorsque vous spécifiez le nom de la langue dans la configuration de la tâche, utilisez un code à 4 caractères entre crochets, comme indiqué ci-dessous) :

* Anglais [EnUs]
* Espagnol [EsEs]
* Chinois (mandarin, simplifié) [ZhCn]
* Français [FrFr]
* Allemand [DeDe]
* Italien [ItIt]
* Portugais [PtBr]
* Arabe (égyptien) [ArEg]
* Japonais [JaJp]

## <a name="sample-code"></a>Exemple de code

Le programme suivant montre comment effectuer les tâches suivantes :

1. Créer un élément multimédia et charger un fichier multimédia dans l’élément multimédia.
2. Créer un travail avec une tâche d’indexation basée sur un fichier de configuration qui contient la présélection JSON suivante.
   
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }
3. Télécharger les fichiers de sortie. 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace IndexContent
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
   
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
   
                static void Main(string[] args)
                {
   
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
   
                    // Run indexing job.
                    var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                                @"C:\supportFiles\Indexer\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
                }
   
                static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Indexing Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Indexing Job");
   
                    // Get a reference to Azure Media Indexer 2 Preview.
                    string MediaProcessorName = "Azure Media Indexer 2 Preview";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Indexing Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset to be indexed.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
   
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
   
                    // Launch the job.
                    job.Submit();
   
                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
   
                    progressJobTask.Wait();
   
                    // If job state is Error, the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                        Console.WriteLine(string.Format("Error: {0}. {1}",
                                                        error.Code,
                                                        error.Message));
                        return null;
                    }
   
                    return job.OutputMediaAssets[0];
                }
   
                static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
                {
                    IAsset asset = _context.Assets.Create(assetName, options);
   
                    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                    assetFile.Upload(filePath);
   
                    return asset;
                }
   
                static void DownloadAsset(IAsset asset, string outputDirectory)
                {
                    foreach (IAssetFile file in asset.AssetFiles)
                    {
                        file.Download(Path.Combine(outputDirectory, file.Name));
                    }
                }
   
                static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors
                        .Where(p => p.Name == mediaProcessorName)
                        .ToList()
                        .OrderBy(p => new Version(p.Version))
                        .LastOrDefault();
   
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor",
                                                                   mediaProcessorName));
   
                    return processor;
                }
   
                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
   
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine();
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
                            // Display or log error details as needed.
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
            }
        }

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Liens connexes
[Vue d’ensemble d’Azure Media Services Analytics](media-services-analytics-overview.md)

[Démonstrations Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)




<!--HONumber=Dec16_HO2-->


