<properties 
	pageTitle="Indexation de fichiers multimédias avec Azure Media Indexer" 
	description="Azure Media Indexer permet de rendre le contenu de vos fichiers multimédias consultable et de générer une transcription en texte intégral de sous-titrages et de mots-clés. Cette rubrique explique comment utiliser Media Indexer." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="juliako"/>


# Indexation de fichiers multimédias avec Azure Media Indexer

> [AZURE.SELECTOR]
- [Portal](media-services-manage-content.md#index)
- [.NET](media-services-index-content.md)


Azure Media Indexer permet de rendre le contenu de vos fichiers multimédias consultable et de générer une transcription en texte intégral de sous-titrages et de mots-clés. Vous pouvez traiter un fichier multimédia ou plusieurs dans un lot.

>[AZURE.NOTE]Lors de l’indexation de contenu, veillez à utiliser des fichiers multimédias avec des mots clairs (sans musique de fond, bruit, effets ou sifflement du microphone). Voici quelques exemples de contenu approprié : des réunions, des conférences ou des présentations enregistrées. Le contenu suivant peut ne pas convenir à l’indexation : des films, des émissions de télévision, des fichiers avec du son et des effets sonores mélangés, du contenu mal enregistré avec un bruit de fond (sifflement).


Un travail d’indexation génère quatre sorties pour chaque fichier d’indexation :

- Un fichier de sous-titres codés au format SAMI.
- Un fichier de sous-titres codés au format TTML (Timed Text Markup Language).

	SAMI et TTML incluent une balise appelée Recognizability, qui note une tâche d’indexation en fonction de la possibilité de reconnaître les mots de la vidéo source. Vous pouvez utiliser la valeur de Recognizability pour filtrer les fichiers de sortie en fonction de leur usage. Un faible score sous-entend de mauvais résultats d’indexation en raison de la qualité audio.
- Un fichier de mot-clé(XML).
- Un fichier blob d’indexation audio (AIB) à utiliser avec SQL Server.
	
	Pour plus d’informations, consultez la rubrique [Utilisation de fichiers AIB avec Azure Media Indexer et SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Cette rubrique explique comment créer des tâches d’indexation pour **indexer un élément multimédia** et **indexer plusieurs fichiers**.

Pour connaître les derniers développements d’Azure Media Indexer, consultez les [blogs Media Services](http://azure.microsoft.com/blog/topics/media-services/).

##Utilisation des fichiers de configuration et manifeste pour l’indexation des tâches

Vous pouvez définir plus de détails pour vos tâches d’indexation en utilisant une configuration de tâche. Par exemple, vous pouvez spécifier les métadonnées à utiliser pour votre fichier multimédia. Ces métadonnées sont utilisées par le moteur de langue pour développer son vocabulaire et améliorent considérablement la précision de la reconnaissance vocale.

Vous pouvez également traiter plusieurs fichiers multimédias à la fois à l’aide d’un fichier manifeste.

Pour plus d’informations, consultez la rubrique [Présélection de tâches pour Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783454.aspx).

##Indexation d’une ressource

La méthode suivante télécharge un fichier multimédia en tant que ressource et crée une tâche pour indexer la ressource.

Notez que si aucun fichier de configuration n’est spécifié, la ressource est indexée avec tous les paramètres par défaut.
	
	static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
	{
	    // Create an asset and upload the input media file to storage.
	    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	        "My Indexing Input Asset",
	        AssetCreationOptions.None);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer",
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration from file if specified.
	    string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
	
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
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
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
	
###<a id="output_files"></a>Fichiers de sortie

La tâche d’indexation génère les fichiers de sortie suivants. Les fichiers sont stockés dans la première ressource de sortie.


<table border="1">
<tr><th>Nom de fichier</th><th>Description</th></tr>
<tr><td>InputFileName.aib </td>
<td>Fichier blob d’indexation audio.<br/><br/>
Un fichier blob d’indexation audio (AIB) est un fichier binaire qui peut être recherché dans Microsoft SQL server à l’aide de la recherche de texte intégral. Un fichier AIB est plus puissant que les fichiers de sous-titres simples, car il contient des alternatives pour chaque mot, pour une expérience de recherche plus riche.
<br/>
<br/>
Il requiert l’installation du module complémentaire d’indexeur SQL sur un ordinateur exécutant Microsoft SQL server&#160;2008 ou une version ultérieure. La recherche du fichier AIB à l’aide de la recherche de texte intégral de Microsoft SQL Server fournit des résultats de recherche plus précis que pour les fichiers de sous-titres générés par WAMI. Cela vient du fait que l’AIB contient des alternatives phonétiquement proches, tandis que les fichiers de sous-titres contiennent le mot avec le niveau de confiance le plus élevé pour chaque segment du fichier audio. Si la recherche de mots est très importante, il est recommandé d’utiliser AIB avec Microsoft SQL Server.
<br/><br/>
Pour télécharger le composant additionnel, cliquez sur <a href="http://aka.ms/indexersql">Composant additionnel SQL Azure Media Indexer</a>.
<br/><br/>
Il est également possible d’utiliser d’autres moteurs de recherche comme Apache Lucene/Solr pour indexer la vidéo selon les sous-titres et les fichiers XML de mots clés, mais les résultats sont moins précis.</td></tr>
<tr><td>NomFichierEntrée.smi<br/>NomFichierEntrée.ttml</td>
<td>Fichiers de sous-titres aux formats SAMI et TTML.
<br/><br/>
Ils permettent de rendre un fichier audio et vidéo accessible aux malentendants.
<br/><br/>
SAMI et TTML incluent une balise appelée <b>Recognizability</b>, qui note une tâche d’indexation en fonction de la possibilité de reconnaître les mots de la vidéo source. Vous pouvez utiliser la valeur de <b>Recognizability</b> pour filtrer les fichiers de sortie en fonction de leur usage. Un faible score sous-entend de mauvais résultats d’indexation en raison de la qualité audio.</td></tr>
<tr><td>NomFichierEntrée.kw.xml</td>
<td>Fichier de mot-clé.
<br/><br/>
Un fichier de mot-clé est un fichier XML qui contient les mots clés extraits à partir du contenu de la reconnaissance vocale, avec les informations relatives à la fréquence et à la référence.
<br/><br/>
Le fichier peut utilisé pour plusieurs raisons, par exemple, pour effectuer une analyse vocale ou être exposé à des moteurs de recherche comme Bing, Google ou Microsoft SharePoint pour rendre les fichiers de support plus détectables ou pour fournir des publicités plus pertinentes.</td></tr>
</table>

Si tous les fichiers multimédias d’entrée ne sont pas correctement indexés, la tâche d’indexation échoue avec le code d’erreur 4000. Pour plus d’informations, consultez les [codes d’erreur](#error_codes).

##Indexation de plusieurs fichiers

La méthode suivante télécharge plusieurs fichiers multimédias en tant que ressource et crée une tâche pour indexer tous ces fichiers en lot.

Un fichier manifeste avec l’extension .lst est créé et téléchargé dans la ressource. Le fichier manifeste contient la liste de tous les fichiers de ressources. Pour plus d’informations, consultez la rubrique [Présélection de tâches pour Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783454.aspx).
	
	static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
	{
	    // Create an asset and upload to storage.
	    IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
	        "My Indexing Input Asset - Batch Mode",
	        AssetCreationOptions.None);
	
	    // Create a manifest file that contains all the asset file names and upload to storage.
	    string manifestFile = "input.lst";            
	    File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
	    assetFile.Upload(manifestFile);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer";
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration.
	    string configuration = File.ReadAllText("batch.config");
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job.
	    task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
	
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
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    foreach (string filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        assetFile.Upload(filePath);
	    }
	
	    return asset;
	}


###Fichiers de sortie

Lorsqu’il existe plusieurs fichiers multimédias d’entrée, WAMI génère un fichier manifeste pour les sorties de la tâche, nommé « JobResult.txt ». Pour chaque fichier multimédia d’entrée, les fichiers AIB, SAMI, TTML et mots clés de sortie sont numérotés de façon séquentielle, comme indiqué ci-dessous.

Pour obtenir une description des fichiers de sortie, consultez la rubrique [Fichiers de sortie](#output_files).


<table border="1">
<tr><th>Nom de fichier</th><th>Description</th></tr>
<tr><td>JobResult.txt</td>
<td>Manifeste de sortie
<br/><br/>Voici le format du fichier manifeste de sortie (JobResult.txt).
<br/><br/>

<table border="1">
<tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Erreur</th></tr>
<tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr>
<tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr>
<tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr>
</table><br/>
Chaque ligne représente un fichier multimédia d’entrée&#160;:
<br/><br/>
InputFile&#160;: nom du fichier de ressource ou URL du fichier multimédia d’entrée.
<br/><br/>
Alias&#160;: nom du fichier de sortie correspondant.
<br/><br/>
MediaLength&#160;: longueur du fichier multimédia d’entrée, en secondes. Elle peut être de 0 en cas d’erreur.
<br/><br/>
Error&#160;: indique si ce fichier multimédia a été indexé avec succès. 0 en cas de réussite, différent en cas d’échec. Reportez-vous aux <a href="#error_codes">codes d’erreur</a> pour des erreurs concrètes.
</td></tr>
<tr><td>Media_1.aib </td>
<td>Fichier #0&#160;: fichier blob d’indexation audio.</td></tr>
<tr><td>Media_1.smi<br/>Media_1.ttml</td>
<td>Fichier #0&#160;: fichiers de sous-titres aux formats SAMI et TTML.</td></tr>
<tr><td>Media_1.kw.xml</td>
<td>Fichier #0&#160;: fichier de mot-clé</td></tr>
<tr><td>Media_2.aib </td>
<td>Fichier #1&#160;: fichier blob d’indexation audio.</td></tr>
</table>

Si tous les fichiers multimédias d’entrée ne sont pas correctement indexés, la tâche d’indexation échoue avec le code d’erreur 4000. Pour plus d’informations, consultez les [codes d’erreur](#error_codes).

###Tâche partiellement réussie

Si tous les fichiers multimédias d’entrée ne sont pas correctement indexés, la tâche d’indexation échoue avec le code d’erreur 4000. Pour plus d’informations, consultez les [codes d’erreur](#error_codes).


Les mêmes sorties que pour des tâches réussies sont générées. Vous pouvez consulter le fichier manifeste de sortie pour savoir quels fichiers d’entrée ont échoué, en fonction des valeurs de la colonne d’erreur. Pour les fichiers d’entrée ayant échoué, les fichiers AIB, SAMI, TTML et de mot-clé ne sont pas générés.


### <a id="error_codes"></a>Codes d’erreur


<table border="1">
<tr><th>Code</th><th>Nom</th><th>Causes possibles</th></tr>
<tr><td>2000</td><td>Configuration non valide</td><td>Configuration non valide</td></tr>
<tr><td>2001</td><td>Ressources d’entrée non valides</td><td>Ressources d’entrée manquantes ou vides.</td></tr>
<tr><td>2002</td><td>Manifeste non valide</td><td>Le manifeste est vide ou contient des éléments non valides.</td></tr>
<tr><td>2003</td><td>Impossible de télécharger le fichier multimédia</td><td>L’URL dans le fichier manifeste n’est pas valide.</td></tr>
<tr><td>2004</td><td>Protocole non pris en charge</td><td>Le protocole de l'URL du média n’est pas pris en charge.</td></tr>
<tr><td>2005</td><td>Type de fichier non pris en charge</td><td>Le type de fichier multimédia d’entrée n’est pas pris en charge.</td></tr>
<tr><td>2006</td><td>Trop de fichiers d’entrée</td><td>Le manifeste d’entrée comporte plus de 10&#160;fichiers. </td></tr>
<tr><td>3000</td><td>Impossible de décoder le fichier multimédia</td>
<td>Le codec de média n’est pas pris en charge.
<br/>ou<br/>
Le fichier multimédia est endommagé.
<br/>ou<br/>
Il n’y a aucun flux audio dans le fichier d’entrée.</td></tr>
<tr><td>4000</td><td>Indexation en lot partiellement réussie</td><td>Impossible d'indexer certains des fichiers multimédias d’entrée. Pour plus d’informations, consultez la section <a href="output_files">Fichiers de sortie</a>.</td></tr>
<tr><td>autres</td><td>Erreurs internes</td><td>Veuillez contacter l’équipe du support technique.</td></tr>
</table>


##<a id="supported_languages"></a>Langues prises en charge :

Les langues prises en charge pour le moment sont l’anglais et l’espagnol. Pour plus d’informations, consultez la [Version espagnole d’Azure Media Indexer](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

##Liens connexes

[Utilisation de fichiers AIB avec Azure Media Indexer et SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=August15_HO6-->