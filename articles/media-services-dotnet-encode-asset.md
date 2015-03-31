<properties 
	pageTitle="Encodage d'un élément multimédia à l'aide d'Azure Media Encoder" 
	description="Apprenez à utiliser Azure Media Encoder pour encoder un contenu multimédia sur Media Services. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>


#Encodage d'un élément multimédia à l'aide d'Azure Media Encoder

Cet article fait partie de la série [workflow de vidéo à la demande Media Services](../media-services-video-on-demand-workflow) . 

##Vue d'ensemble

Pour fournir une vidéo numérique sur Internet, vous devez compresser le contenu multimédia. Les fichiers vidéo numériques sont volumineux et peuvent être trop gros pour être fournis sur Internet ou pour que les appareils de vos clients les affichent correctement. L'encodage est le processus de compression audio et vidéo permettant à vos clients d'afficher votre contenu multimédia.

Les tâches d'encodage sont une des opérations de traitement les plus courantes dans Media Services. Vous créez des tâches d'encodage pour convertir des fichiers multimédias d'un encodage à un autre. Lorsque vous les encodez, vous pouvez utiliser l'encodeur multimédia intégré de Media Services. Vous pouvez aussi utiliser un encodeur fourni par un partenaire Media Services. Ces encodeurs tiers sont disponibles sur Azure Marketplace. Vous pouvez spécifier les détails des tâches d'encodage à l'aide de chaînes de présélection définies pour votre encodeur ou en utilisant des fichiers de configuration prédéfinis. Pour voir les types de présélections disponibles, consultez Présélections de tâches pour Azure Media Services. Si vous avez utilisé un encodeur tiers, vous devez [valider vos fichiers](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Il est recommandé de toujours encoder vos fichiers mezzanine en un ensemble de fichiers MP4 adaptatifs, puis de convertir le jeu au format souhaité en utilisant l'[empaquetage dynamique](https://msdn.microsoft.com/library/azure/jj889436.aspx).


##Création d'un travail avec une seule tâche d'encodage 

Lors de l'encodage avec l'encodeur Azure Media, vous pouvez utiliser des préréglages de configuration de tâche, définis [ici](https://msdn.microsoft.com/library/azure/dn619389.aspx).

###Utilisation du Kit de développement logiciel (SDK) Media Services pour .NET  

La méthode **EncodeToAdaptiveBitrateMP4Set** suivante crée une tâche de codage et y ajoute une seule tâche de codage. La tâche utilise " l'encodeur Azure Media " pour encoder vers " Jeu de MP4 H264 à débit adaptatif 720p ". 

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
    {
        var encodingPreset = "H264 Adaptive Bitrate MP4 Set 720p";

        IJob job = _context.Jobs.Create(String.Format("Encoding {0} into to {1}",
                                inputAsset.Name,
                                encodingPreset));

        var mediaProcessors = GetLatestMediaProcessorByName("Azure Media Encoder");

        ITask encodeTask = job.Tasks.AddNew("Encoding", mediaProcessors, encodingPreset, TaskOptions.None);
        
        encodeTask.InputAssets.Add(inputAsset);

        // Specify the storage-encrypted output asset.
        encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 
            AssetCreationOptions.StorageEncrypted);


        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }

    private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                break;
            default:
                break;
        }
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
           ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

###Utilisation du Kit de développement logiciel (SDK) Media Services pour les extensions .NET

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
    {
        // 1. Prepare a job with a single task to transcode the specified mezzanine asset
        //    into a multi-bitrate asset.
        IJob job = _context.Jobs.CreateWithSingleTask(
            MediaProcessorNames.AzureMediaEncoder,
            MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
            asset,
            "Adaptive Bitrate MP4",
            AssetCreationOptions.None);

        Console.WriteLine("Submitting transcoding job...");

        // 2. Submit the job and wait until it is completed.
        job.Submit();
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    } 

##Création d'un travail avec des tâches chaînées 

Dans de nombreux scénarios d'application, les développeurs souhaitent créer une série de tâches de traitement. Dans Media Services, vous pouvez créer une série de tâches chaînées. Chaque tâche effectue différentes étapes de traitement et peut utiliser différents processeurs multimédias. Les tâches chaînées peuvent transférer un élément multimédia d'une tâche à une autre, en effectuant une séquence linéaire de tâches sur l'élément multimédia. Toutefois, les tâches effectuées dans un travail ne le sont pas obligatoirement dans une séquence. Lorsque vous créez une tâche chaînée, les objets chaînés **ITask** sont créés dans un seul objet **IJob**.

>[AZURE.NOTE] Il existe actuellement une limite de 30 tâches par travail. Si vous devez chaîner plus de 30 tâches, créez plusieurs travaux pour contenir les tâches.

La méthode **CreateChainedTaskEncodingJob** suivante crée un travail qui contient deux tâches chaînées. Par conséquent, la méthode retourne un travail qui contient deux éléments multimédias de sortie.

	
    public static IJob CreateChainedTaskEncodingJob(IAsset asset)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("My task-chained encoding job");

        // Set up the first task to encode the input file.

        // Get a media processor reference
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
           "H264 Adaptive Bitrate MP4 Set 720p",
            TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Specify the storage-encrypted output asset.
        task.OutputAssets.AddNew("My storage-encrypted output asset",
            AssetCreationOptions.StorageEncrypted);

        // Set up the second task to decrypt the encoded output file from 
        // the first task.

        // Get another media processor instance
        IMediaProcessor decryptProcessor = GetLatestMediaProcessorByName("Storage Decryption");

        // Declare the decryption task. 
        ITask decryptTask = job.Tasks.AddNew("My decryption task",
            decryptProcessor,
            string.Empty,
            TaskOptions.None);

        // Specify the input asset to be decrypted. This is the output 
        // asset from the first task. 
        decryptTask.InputAssets.Add(task.OutputAssets[0]);

        // Specify an output asset to contain the results of the job. 
        // This should have AssetCreationOptions.None. 
        decryptTask.OutputAssets.AddNew("My decrypted output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(JobStateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        //return job that contains two output assets.
        return job;
    }


##Étapes suivantes
Maintenant que vous savez comment créer une tâche pour encoder un élément multimédia, consultez la rubrique [Vérification de la progression d'une tâche avec Media Services](../media-services-check-job-progress/) .

[Azure Marketplace]: https://datamarket.azure.com/
[Préréglage de l'encodeur]: http://msdn.microsoft.com/library/dn619392.aspx
[Procédure : obtention d'une instance de processeur multimédia]:http://go.microsoft.com/fwlink/?LinkId=301732
[Procédure : téléchargement d'un élément multimédia chiffré]:http://go.microsoft.com/fwlink/?LinkId=301733
[Procédure : remise d'un élément multimédia par téléchargement]:http://go.microsoft.com/fwlink/?LinkId=301734
[Vérification de la progression des tâches]:http://go.microsoft.com/fwlink/?LinkId=301737
[Présélection de tâches pour Azure Media Packager]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx

<!--HONumber=47-->
