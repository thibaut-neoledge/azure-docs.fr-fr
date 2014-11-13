<properties urlDisplayName="Encrypt Assets in Media Services" pageTitle="Chiffrement des &eacute;l&eacute;ments multim&eacute;dias dans Media Services - Azure" metaKeywords="" description="Apprenez &agrave; utiliser Microsoft PlayReady Protection pour chiffrer un &eacute;l&eacute;ment multim&eacute;dia dans Media Services. Les exemples de code sont &eacute;crits en C# et utilisent le Kit de d&eacute;veloppement logiciel (SDK) Media Services pour .NET. Les exemples de code sont &eacute;crits en C# et utilisent le Kit de d&eacute;veloppement logiciel (SDK) Media Services pour .NET." metaCanonical="" services="media-services" documentationCenter="" title="Utilisation Protection d'un &eacute;l&eacute;ment multim&eacute;dia &agrave; l'aide de la protection PlayReady" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <a name="playready"></a> Protection d'un élément multimédia à l'aide de la protection PlayReady

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Vérification de la progression d'une tâche.][Vérification de la progression d'une tâche.].

Azure Media Services vous permet d'envoyer une tâche qui s'intègre à la protection Microsoft PlayReady pour chiffrer un élément multimédia. Le code présenté ici prélève plusieurs fichiers de diffusion en continu dans un dossier d'entrée, crée une tâche et la chiffre avec la protection PlayReady.

L'exemple suivant indique comment créer une tâche simple pour appliquer la protection PlayReady.

1.  Récupérez les données de configuration. Un exemple de fichier de configuration est disponible à la rubrique [Présélection de tâche pour Azure Media Encryptor][Présélection de tâche pour Azure Media Encryptor].
2.  Téléchargez un fichier d'entrée MP4.
3.  Convertissez le fichier MP4 en élément multimédia de diffusion en continu lisse.
4.  Chiffrez l'élément multimédia à l'aide de PlayReady.
5.  Envoyez la tâche.

L'exemple de code suivant montre comment implémenter les étapes :

    private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
    {
        // Create a storage-encrypted asset and upload the MP4. 
        IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

        // Declare a new job to contain the tasks.
        IJob job = _context.Jobs.Create("My PlayReady Protection job");

        // Set up the first task. 

        // Read the task configuration data into a string. 
        string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

        // Get a media processor instance
        IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Packager");

        // Create a task with the conversion details, using the configuration data 
        ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
            processor,
            configMp4ToSmooth,
            _clearConfig);

        // Specify the input asset to be converted.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job. We do not need 
        // to persist the output asset to storage, so set the shouldPersistOutputOnCompletion
        // parameter to false. 
        task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
        IAsset smoothOutputAsset = task.OutputAssets[0];

        // Set up the encryption task. 

        // Read the configuration data into a string. 
        string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

        // Get a media processor instance
        IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Windows Azure Media Encryptor");

        // Create a second task, specifying a task name, the media processor, and configuration
        ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
            playreadyProcessor,
            configPlayReady,
            _protectedConfig);

        // Add the input asset, which is the smooth streaming output asset from the first task. 
        playreadyTask.InputAssets.Add(smoothOutputAsset);

        // Add an output asset to contain the results of the job. Persist the output by setting 
        // the shouldPersistOutputOnCompletion param to true.
        playreadyTask.OutputAssets.AddNew("PlayReady protected output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
                EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Optionally log job details. This displays basic job details
        // to the console and saves them to a JobDetails-{JobId}.txt file 
        // in your output folder.
        LogJobDetails(job.Id);

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("\nExiting method due to job error.");
            return job;
        }
        // Perform other tasks. For example, access the assets that are the output of a job, 
        // either by creating URLs to the asset on the server, or by downloading. 

        return job;
    }

Pour plus d'informations sur la protection PlayReady, consultez les pages suivantes :

-   [Protection des éléments multimédias avec Microsoft PlayReady][Protection des éléments multimédias avec Microsoft PlayReady]
-   [Microsoft PlayReady][Microsoft PlayReady]

</p>
## Étapes suivantes

Maintenant que vous savez comment protéger des éléments multimédias avec Media Services, consultez la rubrique [Gestion des éléments multimédias][Gestion des éléments multimédias].

  [Vérification de la progression d'une tâche.]: ../media-services-check-job-progress/
  [Présélection de tâche pour Azure Media Encryptor]: http://msdn.microsoft.com/fr-fr/library/hh973610.aspx
  [Protection des éléments multimédias avec Microsoft PlayReady]: http://msdn.microsoft.com/fr-fr/library/dn189154.aspx
  [Microsoft PlayReady]: http://www.microsoft.com/PlayReady/
  [Gestion des éléments multimédias]: ../media-services-manage-assets/
