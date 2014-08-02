<properties linkid="develop-media-services-how-to-guides-encrypt-assets" urlDisplayName="Encrypt Assets in Media Services" pageTitle="How to Encrypt Assets in Media Services - Azure" metaKeywords="" description="Learn how to use Microsoft PlayReady Protection to encrypt an asset in Media Services. Code samples are written in C# and use the Media Services SDK for .NET. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Protect an Asset with PlayReady Protection" authors="migree" solutions="" manager="" editor="" />

Protection d'un élément multimédia à l'aide de la protection PlayReady
======================================================================

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Vérification de la progression d'une tâche.](http://go.microsoft.com/fwlink/?LinkID=301737&clcid=0x409).

Azure Media Services vous permet d'envoyer une tâche qui s'intègre à la protection Microsoft PlayReady pour chiffrer un élément multimédia. Le code présenté ici prélève plusieurs fichiers de diffusion en continu dans un dossier d'entrée, crée une tâche et la chiffre avec la protection PlayReady.

L'exemple suivant indique comment créer une tâche simple pour appliquer la protection PlayReady.

1.  Récupérez les données de configuration. Un exemple de fichier de configuration est disponible à la rubrique [Présélection de tâche pour Azure Media Encryptor](http://msdn.microsoft.com/en-us/library/hh973610.aspx).
2.  Téléchargez un fichier d'entrée MP4.
3.  Convertissez le fichier MP4 en élément multimédia de diffusion en continu lisse.
4.  Chiffrez l'élément multimédia à l'aide de PlayReady.
5.  Envoyez la tâche.

L'exemple de code suivant montre comment implémenter les étapes :

``` {}

private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
{
    // Créez un élément multimédia chiffré pour le stockage et téléchargez le fichier MP4. 
    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

    // Déclarez une nouvelle tâche comme contenant les tâches.
    IJob job = _context.Jobs.Create("My PlayReady Protection job");

    // Définissez la première tâche. 

    // Lisez les données de configuration de la tâche dans une chaîne. 
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

    // Obtenez une instance de processeur multimédia
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");

    // Créez une tâche avec les détails de la conversion, à l'aide des données de configuration
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
        processor,
        configMp4ToSmooth,
        _clearConfig);

    // Spécifiez l'élément multimédia d'entrée à convertir.
    task.InputAssets.Add(asset);

    // Ajoutez un élément multimédia de sortie pour contenir les résultats de la tâche. Il n’est pas nécessaire de
    // conserver l'élément multimédia de sortie dans le stockage, par conséquent définissez le paramètre shouldPersistOutputOnCompletion
    // sur "false". 
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);?
    IAsset smoothOutputAsset = task.OutputAssets[0];

    // Définissez la tâche de chiffrement. 

    // Lisez les données de configuration dans une chaîne. 
    string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

    // Obtenez une instance de processeur multimédia
    IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Azure Media Encryptor");

    // Créez une deuxième tâche en spécifiant un nom de tâche, le processeur multimédia et la configuration
    ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
        playreadyProcessor,
        configPlayReady,
        _protectedConfig);

    // Ajoutez l'élément multimédia d'entrée, qui correspond à l'élément de sortie de diffusion en continu lisse de la première tâche. 

    playreadyTask.InputAssets.Add(smoothOutputAsset);

    // Ajoutez un élément multimédia de sortie pour contenir les résultats de la tâche. Conservez la sortie en définissant le paramètre

    // shouldPersistOutputOnCompletion sur "true".
    playreadyTask.OutputAssets.AddNew("PlayReady protected output asset",
        AssetCreationOptions.None);

    // Utilisez le gestionnaire d'événements suivant pour vérifier la progression de la tâche. 
    job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(StateChanged);

    // Lancez la tâche.
    job.Submit();

    // Consignez les détails de la tâche (facultatif). Affiche les détails de base de la tâche 
    // sur la console et les enregistre dans un fichier JobDetails-{JobId}.txt 
    // de votre dossier de sortie.
    LogJobDetails(job.Id);

    // Contrôlez l'exécution de la tâche et attendez la fin de celle-ci. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();

    // Si l'état de la tâche est Error, la méthode de gestion 
    // des erreurs de la progression de la tâche doit noter les erreurs.  Ici, nous vérifions 
    // l'état de l'erreur, et quittons si nécessaire.
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
        return job;
    }
    // Effectuez d'autres tâches. Par exemple, accédez aux éléments multimédias qui sont le résultat de la tâche,
    // soit en créant des URL vers l'élément multimédia sur le serveur, ou bien en le téléchargeant. 

    return job;
}
```

Pour plus d'informations sur la protection PlayReady, consultez la rubrique :

-   [Protection des éléments multimédias avec Microsoft PlayReady](http://msdn.microsoft.com/en-us/library/dn189154.aspx)
-   [Microsoft PlayReady](http://www.microsoft.com/PlayReady/)

Étapes suivantes
----------------

Maintenant que vous savez comment protéger des éléments multimédias avec Media Services, consultez la rubrique [Gestion des éléments multimédias](http://go.microsoft.com/fwlink/?LinkID=301943&clcid=0x409).

