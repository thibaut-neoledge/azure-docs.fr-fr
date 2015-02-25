<properties pageTitle="Encodage d'un élément multimédia pour Media Services - Azure" description="Apprenez à utiliser Azure Media Encoder pour encoder un contenu multimédia sur Media Services. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>


# Codage d'un élément multimédia
Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [ Obtention d'un processeur multimédia](../media-services-get-media-processor/).

Vous pouvez encoder le contenu multimédia sur le serveur avec un grand nombre d'encodages et de formats grâce à Azure Media Encoder. Vous pouvez aussi utiliser un encodeur fourni par un partenaire Media Services. Ces encodeurs sont disponibles sur [Azure Marketplace][]. Vous pouvez définir les détails de la tâche d'encodage avec les chaînes de [Préréglage des tâches][] ou avec les fichiers de configuration. 

## Encodage sous forme d'ensembles MP4 à débit adaptatif
Il est recommandé d'encoder votre fichier mezzanine en ensembles MP4 à débit adaptatif, puis d'utiliser l'empaquetage dynamique pour fournir votre contenu. Pour plus d'informations, consultez les pages [Création d'une tâche d'encodage avec le Kit de développement logiciel (SDK) Media Services pour .NET](http://msdn.microsoft.com/fr-fr/library/azure/dn282273.aspx), [Empaquetage dynamique](http://msdn.microsoft.com/fr-fr/library/azure/jj889436.aspx) et [Distribution de contenu](http://msdn.microsoft.com/fr-fr/library/azure/hh973618.aspx).

## Encodage en MP4
La méthode qui suit utilise un seul élément multimédia et crée une tâche pour l'encoder au format MP4 à l'aide de la présélection " H264 Broadband 720p ", qui crée un fichier MP4 avec l'encodage H264 et une résolution de 720p :
<pre><code>
	static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
	{
    	//Create an encrypted asset and upload to storage.
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
			inputMediaFilePath);

		// Declare a new job.

    	IJob job = _context.Jobs.Create("My encoding job");
	
		// Get a reference to the Azure Media Encoder
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
    
		// Create a task with the encoding details, using a string preset.
    	ITask task = job.Tasks.AddNew("My encoding task",
        	processor,
	        "H264 Broadband 720p",
        	_protectedConfig);
    
		// Specify the input asset to be encoded.
    	task.InputAssets.Add(asset);
    
		// Add an output asset to contain the results of the job. 
    	// This output is specified as AssetCreationOptions.None, which 
    	// means the output asset is in the clear (unencrypted). 
    	task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
    
		// Use the following event handler to check job progress.  
    	job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    
		// Launch the job.
    	job.Submit();
    
		// Optionally log job details. This displays basic job details
    	// to the console and saves them to a JobDetails-JobId.txt file 
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

	            // Cast sender as a job.
            	IJob job = (IJob)sender;

	            // Display or log error details as needed.
            	LogJobStop(job.Id);
            	break;
        	default:
	            break;
    	}
	}
</code></pre>
<h2>Encodage pour Smooth Streaming</h2>
Si vous souhaitez encoder une vidéo pour Smooth Streaming, vous avez deux possibilités :
<ul>
<li> encoder directement pour Smooth Streaming ; </li>
<li> encoder en MP4, puis convertir en Smooth Streaming.</li>
</ul>

Pour effectuer l'encodage directement en Smooth Streaming, utilisez le code présenté plus haut, mais utilisez une des présélections Smooth Streaming. Pour obtenir la liste complète des présélections de l'encodeur, consultez la page [Chaînes de présélection des tâches pour Azure Media Encoder](http://msdn.microsoft.com/fr-fr/library/jj129582.aspx). 

Pour convertir un MP4 en Smooth Streaming, utilisez Azure Media Packager. Azure Media Packager ne prend pas en charge les présélections de chaîne afin que vous puissiez spécifier les options de configuration dans le XML. Le code XML pour la conversion de MP4 en Smooth Streaming se trouve dans [Présélection de tâche pour Azure Media Packager][]. Copiez et collez le XML dans un fichier appelé MediaPackager_MP4ToSmooth.xml dans votre projet. Le code qui suit présente la création d'un élément multimédia MP4 en Smooth Streaming. Cette méthode prend un élément multimédia existant et le convertit. 
<pre><code>
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
	// Declare a new job to contain the tasks
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Set up the first Task to convert from MP4 to Smooth Streaming. 
    // Read in task configuration XML
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Get a media packager reference
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Create a task with the conversion details, using the configuration data
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Specify the input asset to be converted.
    task.InputAssets.Add(assetToConvert);
    // Add an output asset to contain the results of the job.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Use the following event handler to check job progress. 
	// The StateChange method is the same as the one in the previous sample
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    // Launch the job.
    job.Submit();
    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Get a refreshed job reference after waiting on a thread.
    job = GetJob(job.Id);
    // Check for errors
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
</code></pre>

Pour plus d'informations sur le traitement des éléments multimédias, consultez les pages suivantes :
<ul>
<li><a href="http://msdn.microsoft.com/fr-fr/library/jj129580.aspx">Traitement des éléments multimédias avec le Kit de développement logiciel (SDK) Media Services pour .NET</a></li>
<li><a href="http://msdn.microsoft.com/fr-fr/library/jj129574.aspx">Traitement des éléments multimédias avec l'API REST de Media Services</a></li>
</ul>

## Étapes suivantes
Maintenant que vous savez comment créer une tâche pour encoder un élément multimédia, consultez la rubrique [Vérification de la progression d'une tâche avec Media Services](../media-services-check-job-progress/).

[Azure Marketplace]: https://datamarket.azure.com/
[Préréglage des tâches]: http://msdn.microsoft.com/fr-fr/library/dn619392.aspx
[Obtention d'une instance de processeur multimédia]:http://go.microsoft.com/fwlink/?LinkId=301732
[Téléchargement d'un élément multimédia chiffré]:http://go.microsoft.com/fwlink/?LinkId=301733
[Fourniture d'un élément multimédia par téléchargement]:http://go.microsoft.com/fwlink/?LinkId=301734
[Vérification de la progression des tâches]:http://go.microsoft.com/fwlink/?LinkId=301737
[Présélection de tâche pour Azure Media Packager]:http://msdn.microsoft.com/fr-fr/library/windowsazure/hh973635.aspx


<!--HONumber=42-->
