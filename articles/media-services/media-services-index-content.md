<properties
	pageTitle="Indexation de fichiers multimédias avec Azure Media Indexer"
	description="Azure Media Indexer permet de rendre le contenu de vos fichiers multimédias consultable et de générer une transcription en texte intégral de sous-titrages et de mots-clés. Cette rubrique explique comment utiliser Media Indexer."
	services="media-services"
	documentationCenter=""
	authors="Asolanki"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/17/2016"   
	ms.author="juliako;adsolank;johndeu"/>


# Indexation de fichiers multimédias avec Azure Media Indexer

> [AZURE.SELECTOR]
- [Portal](media-services-manage-content.md#index)
- [.NET](media-services-index-content.md)


Azure Media Indexer permet de rendre le contenu de vos fichiers multimédias consultable et de générer une transcription en texte intégral de sous-titrages et de mots-clés. Vous pouvez traiter un fichier multimédia ou plusieurs dans un lot.

>[AZURE.IMPORTANT] Lors de l’indexation de contenu, veillez à utiliser des fichiers multimédias avec des mots clairs (sans musique de fond, bruit, effets ou sifflement du microphone). Voici quelques exemples de contenu approprié : des réunions, des conférences ou des présentations enregistrées. Le contenu suivant peut ne pas convenir à l’indexation : des films, des émissions de télévision, des fichiers avec du son et des effets sonores mélangés, du contenu mal enregistré avec un bruit de fond (sifflement).


Une tâche d’indexation peut générer les sorties suivantes :

- Fichiers de sous-titres codés dans les formats suivants : **SAMI**, **TTML** et **WebVTT**.

	Les fichiers de sous-titres codés incluent une balise appelée Recognizability, qui note une tâche d’indexation en fonction de la possibilité de reconnaître les mots de la vidéo source. Vous pouvez utiliser la valeur de Recognizability pour filtrer les fichiers de sortie en fonction de leur usage. Un faible score sous-entend de mauvais résultats d’indexation en raison de la qualité audio.
- Un fichier de mot-clé(XML).
- Un fichier blob d’indexation audio (AIB) à utiliser avec SQL Server.

	Pour plus d’informations, consultez [Utilisation de fichiers AIB avec Azure Media Indexer et SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Cette rubrique explique comment créer des tâches d’indexation pour **indexer un élément multimédia** et **indexer plusieurs fichiers**.

Pour connaître les dernières mises à jour de l’indexeur multimédia Azure, consultez les [blogs Media Services](#preset).

## Utilisation des fichiers de configuration et manifeste pour l’indexation des tâches

Vous pouvez définir plus de détails pour vos tâches d’indexation en utilisant une configuration de tâche. Par exemple, vous pouvez spécifier les métadonnées à utiliser pour votre fichier multimédia. Ces métadonnées sont utilisées par le moteur de langue pour développer son vocabulaire et améliorent considérablement la précision de la reconnaissance vocale. Vous pouvez également spécifier vos fichiers de sortie souhaités.

Vous pouvez également traiter plusieurs fichiers multimédias à la fois à l’aide d’un fichier manifeste.

Pour plus d’informations, consultez [Présélection de tâches pour Azure Media Indexer](#).

## Indexation d’une ressource

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
<!-- __ -->
### <a id="output_files"></a>Fichiers de sortie

Par défaut, une tâche d’indexation génère les fichiers de sortie suivants. Les fichiers sont stockés dans la première ressource de sortie.

Lorsqu’il existe plusieurs fichiers multimédias d’entrée, Indexer génère un fichier manifeste pour les sorties de la tâche, nommé « JobResult.txt ». Pour chaque fichier multimédia d’entrée, les fichiers AIB, SAMI, TTML et WebVTT et les fichiers de mots clés qui en résultent sont numérotés de façon séquentielle et nommé à l’aide d’« Alias ».

Nom de fichier | Description
----------|------------
__InputFileName.aib__ | Fichier blob d’indexation audio. <br /><br /> Un fichier blob d’indexation audio (AIB) est un fichier binaire qui peut être recherché dans le serveur Microsoft SQL à l’aide de la recherche de texte intégral. Un fichier AIB est plus puissant que les fichiers de sous-titres simples, car il contient des alternatives pour chaque mot, pour une expérience de recherche plus riche. <br/> <br/>Il requiert l’installation du module complémentaire d’indexeur SQL sur un ordinateur exécutant Microsoft SQL Server 2008 ou une version ultérieure. La recherche du fichier AIB à l’aide de la recherche de texte intégral de Microsoft SQL Server fournit des résultats de recherche plus précis que pour les fichiers de sous-titres générés par WAMI. Cela vient du fait que l’AIB contient des alternatives phonétiquement proches, tandis que les fichiers de sous-titres contiennent le mot avec le niveau de confiance le plus élevé pour chaque segment du fichier audio. Si la recherche de mots est très importante, il est recommandé d’utiliser AIB avec Microsoft SQL Server.<br/><br/> Pour télécharger le composant additionnel, cliquez sur <a href="http://aka.ms/indexersql">Composant additionnel d’Indexeur multimédia SQL</a>. <br/><br/>Il est également possible d’utiliser d’autres moteurs de recherche comme Apache Lucene/Solr pour indexer la vidéo selon les sous-titres et les fichiers XML de mots clés, mais les résultats sont moins précis.
__InputFileName.smi__<br />\_\_InputFileName.ttml\_\_<br />\_\_InputFileName.vtt\_\_ |Fichiers de sous-titres (CC) aux formats SAMI, TTML et WebVTT.<br/><br/>Ils peuvent être utilisés pour rendre les fichiers audio et vidéo accessibles aux personnes malentendantes.<br/><br/>Les fichiers de sous-titres incluent une balise appelée <b>Recognizability</b> qui évalue un travail d’indexation en fonction de du degré de reconnaissance vocale dans la vidéo source. Vous pouvez utiliser la valeur de <b>Recognizability</b> pour filtrer les fichiers de sortie en fonction de leur usage. Un faible score sous-entend de mauvais résultats d’indexation en raison de la qualité audio.
__InputFileName.kw.xml<br />InputFileName.info__ |Fichiers de mots clés et d’informations. <br/><br/>Un fichier de mot-clé est un fichier XML qui contient les mots clés extraits à partir du contenu de la reconnaissance vocale, avec les informations relatives à la fréquence et à la référence. <br/><br/>Un fichier d’informations est un fichier de texte brut qui contient des informations précises sur chaque terme reconnu. La première ligne est spéciale et contient le score Recognizability. Chaque ligne suivante est une liste des données suivantes séparées par des tabulations : heure de début, heure de fin, mot/expression, fiabilité. Les heures sont exprimées en secondes et la fiabilité est exprimée comme un nombre compris entre 0 et 1. <br/><br/>Ligne exemple : « 1.20 1.45 word 0.67» <br/><br/>Ces fichiers peuvent être utilisés à différentes fins, par exemple pour effectuer une analyse vocale ou être présentés à des moteurs de recherche comme Bing, Google ou Microsoft SharePoint pour rendre les fichiers multimédia plus détectables, ou même pour fournir des publicités plus pertinentes.
__JobResult.txt__ |Manifeste de sortie présent uniquement lors de l’indexation de plusieurs fichiers, qui contient les informations suivantes :<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>erreur</th></tr><tr><td>a.mp4</td><td>Media\_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media\_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media\_3</td><td>600</td><td>0</td></tr></table><br/>



Si tous les fichiers multimédias d’entrée ne sont pas correctement indexés, la tâche d’indexation échoue avec le code d’erreur 4000. Pour plus d’informations, consultez les [codes d’erreur](#error_codes).

## Indexation de plusieurs fichiers

La méthode suivante télécharge plusieurs fichiers multimédias en tant que ressource et crée une tâche pour indexer tous ces fichiers en lot.

Un fichier manifeste avec l’extension .lst est créé et téléchargé dans la ressource. Le fichier manifeste contient la liste de tous les fichiers de ressources. Pour plus d’informations, consultez [Présélection de tâches pour Indexeur multimédia](https://msdn.microsoft.com/library/azure/dn783454.aspx).

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

### Tâche partiellement réussie

Si tous les fichiers multimédias d’entrée ne sont pas correctement indexés, la tâche d’indexation échoue avec le code d’erreur 4000. Pour plus d’informations, consultez les [codes d’erreur](#error_codes).


Les mêmes sorties que pour des tâches réussies sont générées. Vous pouvez consulter le fichier manifeste de sortie pour savoir quels fichiers d’entrée ont échoué, en fonction des valeurs de la colonne d’erreur. Pour les fichiers d’entrée ayant échoué, les fichiers AIB, SAMI, TTML et WebVTT et les fichiers de mots clés ne sont PAS générés.

### <a id="preset"></a> Présélection de tâches pour l’Indexeur multimédia Azure

Le traitement de l’Indexeur multimédia Azure peut être personnalisé par une tâche facultative prédéfinie à côté de la tâche. La section suivante décrit le format de ce fichier xml de configuration.

Nom | Require | Description
----|----|---
__entrée__ | false | Fichier(s) de l’élément multimédia que vous souhaitez indexer.</p><p>L’indexeur multimédia Azure prend en charge les formats de fichier multimédia suivants : MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Vous pouvez spécifier le(s) nom(s) de fichier dans l’attribut **nom** ou **liste** de l’attribut de l’élément **d’entrée** (comme indiqué ci-dessous). Si vous ne spécifiez pas de fichier d’élément multimédia à indexer, c’est le fichier principal qui est choisi. Si aucun fichier principal n’est défini, le premier fichier d’élément multimédia d’entrée est indexé.</p><p>Pour spécifier explicitement le nom du fichier d’élément multimédia, effectuez :<br />`<input name="TestFile.wmv">`<br /><br />Vous pouvez également indexer plusieurs ressources fichiers à la fois (jusqu’à 10 fichiers). Pour ce faire :<br /><br /><ol class="ordered"><li><p>créer un fichier texte (fichier manifeste) et lui affecter une extension .lst. </p></li><li><p>Ajoutez une liste de tous les noms de fichier d’élément multimédia à ce fichier de manifeste. </p></li><li><p>Ajoutez (téléchargez) le fichier manifeste à l’élément multimédia. </p></li><li><p>Spécifiez le nom du fichier manifeste dans l’attribut de liste d’entrée.<br />`<input list="input.lst">`</li></ol><br /><br />Remarque : Si vous ajoutez plus de 10 fichiers au fichier manifeste, la tâche d’indexation échoue avec le code d’erreur 2006.
__métadonnées__ | false | Métadonnées pour les fichiers de ressources actives utilisés pour l’Adaptation du vocabulaire. Il est utile préparer l’indexeur à reconnaître les mots de vocabulaire non standard tels que des noms propres.<br />`<metadata key="..." value="..."/>` <br /><br />Vous pouvez fournir des __valeurs__ pour les __clés__ prédéfinies. Actuellement, les clés suivantes sont prises en charge :<br /><br />« titre » et « description ». Elles servent à l’adaptation du vocabulaire pour sélectionner le modèle de langue pour votre travail et améliorer la précision de la reconnaissance vocale. Les valeurs amorcent des recherches sur Internet pour rechercher des documents pertinents du point de vue contextuel en utilisant le contenu pour étoffer le dictionnaire interne pour la durée de la tâche d’indexation.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__fonctionnalités__ <br /><br /> ajoutées dans la version 1.2. Actuellement, la seule fonctionnalité prise en charge est la reconnaissance vocale (« ASR »).| false | La fonctionnalité de reconnaissance vocale dispose des clés de paramètre suivantes :<table><tr><th><p>clé</p></th> <th><p>Description</p></th><th><p>exemple de valeur</p></th></tr><tr><td><p>langue</p></td><td><p>Le langage naturel à reconnaître dans le fichier multimédia.</p></td><td><p>Anglais, Espagnol,</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>liste délimitée par des points-virgules répertoriant les formats de sous-titres souhaités (le cas échéant)</p></td><td><p>ttml ; sami ; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Indicateur booléen indiquant si un fichier AIB est requis (pour une utilisation avec SQL Server et l’indexeur IFilter client). Pour plus d’informations, consultez <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Utilisation de fichiers AIB avec l’indexeur multimédia Azure et SQL Server</a>.</p></td><td><p>True ; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Indicateur booléen indiquant si un fichier XML de mot clé est requis.</p></td><td><p>True ; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Indicateur booléen spécifiant s’il faut forcer ou non les sous-titres complets (quel que soit le niveau de confiance). </p><p>La valeur par défaut est false, auquel cas les mots et expressions dont le niveau de confiance est inférieur ou égal à 50 % sont omis dans les sous-titres finaux et remplacés par des points de suspension («... »). Les points de suspension sont utiles pour le contrôle de la qualité et l’audit des sous-titres.</p></td><td><p>True ; False. </p></td></tr></table>

### <a id="error_codes"></a>Codes d’erreur

En cas d’erreur, Azure Media indexeur doit signaler un des codes d’erreur suivants :

Code | Nom | Causes possibles
-----|------|------------------
2000 | Configuration non valide | Configuration non valide
2001 | Ressources d’entrée non valides | Ressources d’entrée manquantes ou vides.
2002 | Manifeste non valide | Le manifeste est vide ou contient des éléments non valides.
2003 | Impossible de télécharger le fichier multimédia | L’URL dans le fichier manifeste n’est pas valide.
2004 | Protocole non pris en charge | Le protocole de l'URL du média n’est pas pris en charge.
2005 | Type de fichier non pris en charge | Le type de fichier multimédia d’entrée n’est pas pris en charge.
2006 | Trop de fichiers d’entrée | Le manifeste d’entrée comporte plus de 10 fichiers.
3000 | Impossible de décoder le fichier multimédia | Codec de média non pris en charge <br/>ou<br/> fichier multimédia corrompu <br/>ou<br/> Aucun flux audio dans le média d’entrée.
4000 | Indexation en lot partiellement réussie | Impossible d'indexer certains des fichiers multimédias d’entrée. Pour plus d’informations, consultez la section <a href="output_files">Fichiers de sortie</a>.
autres | Erreurs internes | Veuillez contacter l’équipe du support technique. indexer@microsoft.com


## <a id="supported_languages"></a>Langues prises en charge

Les langues prises en charge pour le moment sont l’anglais et l’espagnol. Pour plus d’informations, consultez [ce billet de blog de v1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



## Liens connexes

[Utilisation de fichiers AIB avec Azure Media Indexer et SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0218_2016-->