<properties linkid="develop-media-services-how-to-guides-encode-an-asset" urlDisplayName="How to Encode an Asset" pageTitle="How to Encode an Asset for Media Services - Azure" metaKeywords="" description="Learn how to use the Azure Media Encoder to encode media content on Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Encode an Asset" authors="migree" solutions="" manager="" editor="" />

Encodage d'un élément multimédia
================================

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Obtention d'un processeur multimédia](http://go.microsoft.com/fwlink/?LinkID=301732&ampclcid=0x409).

Vous pouvez encoder le contenu multimédia sur le serveur avec un grand nombre d'encodages et de formats grâce à Azure Media Encoder. Vous pouvez aussi utiliser un encodeur fourni par un partenaire Media Services. Ces encodeurs sont disponibles sur [Azure Marketplace](https://datamarket.azure.com/). Vous pouvez définir les détails des tâches d'encodage avec les chaînes de [Préréglage des tâches](http://msdn.microsoft.com/fr-fr/library/hh973610.aspx) ou avec les fichiers de configuration.

Encodage en MP4
---------------

La méthode qui suit utilise un seul élément multimédia et crée une tâche pour l'encoder au format MP4 à l'aide de la présélection « H264 Broadband 720p », qui crée un fichier MP4 avec l'encodage H264 et une résolution de 720p :

``` {}
    static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
    {
        //Créez un élément multimédia chiffré et chargez-le dans le stockage.
        IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
            inputMediaFilePath);

        // Déclarez une nouvelle tâche.

        IJob job = _context.Jobs.Create("My encoding job");
    
        // Obtenez une référence pour Azure Media Encoder
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
    
        // Créez une tâche avec les détails de l'encodage, à l'aide d'une chaîne de présélection.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
            "H264 Broadband 720p",
            _protectedConfig);
    
        // Spécifiez l'élément multimédia d'entrée à encoder.
        task.InputAssets.Add(asset);
    
        // Ajoutez un élément multimédia de sortie pour contenir les résultats de la tâche. 
        // Ce résultat est spécifié en tant que AssetCreationOptions.None, ce qui 
        // signifie que l'élément multimédia de sortie n'est pas chiffré (est en clair). 
        task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
    
        // Utilisez le gestionnaire d'événements suivant pour suivre la progression de la tâche.  
        job.StateChanged += new EventHandler&ltJobStateChangedEventArgs>(StateChanged);
    
        // Lancez la tâche.
        job.Submit();
    
        // Notez les détails de la tâche (facultatif). Ceci affiche les détails de base de la tâche
        // dans la console et les enregistre dans un fichier JobDetails-JobId.txt 
        // dans votre dossier de résultat.
        LogJobDetails(job.Id);
    
        // Contrôlez l'exécution de la tâche et attendez la fin de la tâche. 
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

    private static void StateChanged(object sender, JobStateChangedEventArgs e)
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

                // Effectuez une conversion de type de l'expéditeur en tant que tâche.
                IJob job = (IJob)sender;

                // Affichez ou notez les détails de l'erreur si nécessaire.
                LogJobStop(job.Id);
                break;
            default:
                break;
        }?  }
```

Encodage pour Smooth Streaming
------------------------------

Si vous souhaitez encoder une vidéo pour Smooth Streaming, vous avez deux possibilités :

-   encoder directement pour Smooth Streaming ;
-   encoder en MP4, puis convertir en Smooth Streaming.

Pour effectuer l'encodage directement en Smooth Streaming, utilisez le code présenté plus haut, mais utilisez une des présélections Smooth Streaming. Pour obtenir la liste complète des présélections de l'encodeur, consultez la page [Chaînes de présélection des tâches pour Azure Media Encoder](http://msdn.microsoft.com/fr-fr/library/jj129582.aspx).

Pour convertir un MP4 en Smooth Streaming, utilisez Azure Media Packager. Azure Media Packager ne prend pas en charge les présélections de chaîne afin que vous puissiez spécifier les options de configuration dans le XML. Le code XML pour la conversion de MP4 en Smooth Streaming se trouve dans [Présélection de tâche pour Azure Media Packager](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh973635.aspx). Copiez et collez le XML dans un fichier appelé MediaPackager\_MP4ToSmooth.xml dans votre projet. Le code qui suit présente la création d'un élément multimédia MP4 en Smooth Streaming. Cette méthode prend un élément multimédia existant et le convertit.

``` {}
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
    // Déclarez une nouvelle tâche comme contenant les tâches
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Définissez la première tâche pour effectuer la conversion de MP4 en Smooth Streaming. 
    // Lisez le XML de configuration de la tâche
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Obtenez une référence media packager
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Créez une tâche avec les détails de la conversion, à l'aide des données de configuration.
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Spécifiez l'élément multimédia d'entrée à convertir.
    task.InputAssets.Add(assetToConvert);
    // Ajoutez un élément multimédia de sortie pour contenir les résultats de la tâche.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Utilisez le gestionnaire d'événements suivant pour suivre la progression de la tâche. 
    // La méthode StateChange est la même que dans l'exemple précédent
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs>(StateChanged);
    // Lancez la tâche.
    job.Submit();
    // Contrôlez l'exécution de la tâche et attendez la fin de la tâche. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Obtenez une référence de tâche actualisée et attendez le thread.
    job = GetJob(job.Id);
    // Recherchez les erreurs
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
```

Pour plus d'informations sur le traitement des éléments multimédias, consultez les pages suivantes :

-   [Traitement des éléments multimédias avec le Kit de développement logiciel (SDK) Media Services pour .NET](http://msdn.microsoft.com/fr-fr/library/jj129580.aspx)
-   [Traitement des éléments multimédias avec l'API REST de Media Services](http://msdn.microsoft.com/fr-fr/library/jj129574.aspx)

Étapes suivantes
----------------

Maintenant que vous savez comment créer une tâche pour encoder un élément multimédia, consultez la rubrique [Vérification de la progression d'une tâche avec Media Services](http://go.microsoft.com/fwlink/?LinkID=301737&ampclcid=0x409).

