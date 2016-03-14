<properties 
	pageTitle="Encodage avancé avec Media Encoder Premium Workflow" 
	description="Découvrez comment encoder avec Media Encoder Premium Workflow. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="juliako"/>

#Encodage avancé avec Media Encoder Premium Workflow

>[AZURE.NOTE]Pour les questions relatives à Encoder Premium, envoyez un e-mail à mepd sur Microsoft.com.
>
>Le processeur multimédia Media Encoder Premium Workflow présenté dans cette rubrique n'est pas disponible en Chine.

##Vue d'ensemble

Microsoft Azure Media Services lance le processeur multimédia **Media Encoder Premium Workflow**. Ce processeur offre des fonctionnalités d’encodage avancées pour vos flux de travail à la demande premium.

Vous trouverez dans les rubriques suivantes des détails concernant **Media Encoder Premium Workflow** :

- [Formats pris en charge par le flux de travail Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md) : présente les formats et les codecs pris en charge par **Media Encoder Premium Workflow**.

- La section [Comparer les encodeurs](media-services-encode-asset.md#compare_encoders) compare les fonctionnalités d’encodage de **Media Encoder Premium Workflow** et de **Media Encoder Standard**.

Cette rubrique montre comment encoder avec **Media Encoder Premium Workflow** en utilisant .NET.

Les tâches d’encodage pour **Media Encoder Premium Workflow** nécessitent un fichier de configuration distinct appelé fichier de flux de travail. Ces fichiers ont une extension .workflow et sont créés à l’aide de l’outil [Concepteur de workflow](media-services-workflow-designer.md)

>[AZURE.NOTE]Pour les questions relatives à Encoder Premium, envoyez un e-mail à mepd sur Microsoft.com.

##Encoder

Les tâches d’encodage pour **Media Encoder Premium Workflow** nécessitent un fichier de configuration distinct appelé fichier de flux de travail. Ces fichiers ont une extension .workflow et sont créés à l’aide de l’outil [Concepteur de workflow](media-services-workflow-designer.md)


Vous pouvez aussi vous procurer les fichiers de flux de travail par défaut [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Le dossier contient aussi la description de ces fichiers.

Les fichiers de flux de travail doivent être téléchargés vers votre compte Media Services sous forme de ressource, qui doit être transmise à la tâche d’encodage.

L’exemple suivant montre comment encoder avec **Media Encoder Premium Workflow**.

Les étapes à exécuter sont les suivantes :
 
1. Créez une ressource et téléchargez un fichier de flux de travail. 
2. Créez une ressource et téléchargez un fichier multimédia source.
3. Procurez-vous le processeur multimédia « Media Encoder Premium Workflow ».
4. Créez un travail et une tâche. 

	Dans la plupart des cas, la chaîne de configuration de la tâche est vide (comme dans l'exemple suivant). Il existe certains scénarios avancés (qui nécessitent de définir dynamiquement les propriétés d'exécution) auquel cas, vous fournissez une chaîne XML à la tâche d'encodage. La création d'une superposition, l'assemblage parallèle ou séquentiel multimédia, le sous-titrage sont des exemples de ces scénarios.
5. Ajoutez deux ressources d’entrée à la tâche.
	
	a. En premier lieu, la ressource de flux de travail.

	b. En second lieu, la ressource vidéo.
	
	**Remarque** : la ressource de flux de travail doit être ajoutée à la tâche avant la ressource multimédia. La chaîne de configuration de cette tâche doit être vide.

6. Soumettez le travail d’encodage.

L’exemple ci-dessous est complet. Pour plus d’informations sur la configuration dans le cadre du développement .NET Media Services, consultez [Développement Media Services avec .NET](media-services-dotnet-how-to-use.md).


 	using System; 
	using System.Linq;
	using System.Configuration;
	using System.IO;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace MediaEncoderPremiumWorkflowSample
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
	
	        private static readonly string _supportFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        private static readonly string _workflowFilePath =
	            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");
	        
	        private static readonly string _singleMP4InputFilePath =
	            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");
	
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
	            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
	            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset); 
	
	        }
	
	        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
	        {
	            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
	            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	
	            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
	                                                                AccessPermissions.Write | AccessPermissions.List);
	
	            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
	
	            Console.WriteLine("Upload {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            Console.WriteLine("Done uploading {0}", assetFile.Name);
	
	            locator.Delete();
	            accessPolicy.Delete();
	
	            return asset;
	        }
	
	        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
	            // Get a media processor reference, and pass to it the name of the 
	            // processor to use for the specific task.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");
	
	            // Create a task with the encoding details, using a string preset.
	            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
	                processor,
	                "",
	                TaskOptions.None);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(workflow);
	            task.InputAssets.Add(video); // we add one asset
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is not encrypted. 
	            task.OutputAssets.AddNew("Output asset",
	                AssetCreationOptions.None);
	
	            // Use the following event handler to check job progress.  
	            job.StateChanged += new
	                    EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	            // Launch the job.
	            job.Submit();
	
	            // Check job execution and wait for job to finish. 
	            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	            progressJobTask.Wait();
	
	            // If job state is Error the event handling 
	            // method for job progress should log errors.  Here we check 
	            // for error state and exit if needed.
	            if (job.State == JobState.Error)
	            {
	                throw new Exception("\nExiting method due to job error.");
	            }
	
	            return job.OutputMediaAssets[0];
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
	                    LogJobStop(job.Id);
	                    break;
	                default:
	                    break;
	            }
	        }
	
	        static private void LogJobStop(string jobId)
	        {
	            StringBuilder builder = new StringBuilder();
	            IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();
	
	            builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	            builder.AppendLine("***************************");
	            builder.AppendLine("Job ID: " + job.Id);
	            builder.AppendLine("Job Name: " + job.Name);
	            builder.AppendLine("Job State: " + job.State.ToString());
	            builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	            builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
	            // Log job errors if they exist.  
	            if (job.State == JobState.Error)
	            {
	                builder.Append("Error Details: \n");
	                foreach (ITask task in job.Tasks)
	                {
	                    foreach (ErrorDetail detail in task.ErrorDetails)
	                    {
	                        builder.AppendLine("  Task Id: " + task.Id);
	                        builder.AppendLine("    Error Code: " + detail.Code);
	                        builder.AppendLine("    Error Message: " + detail.Message + "\n");
	                    }
	                }
	            }
	            builder.AppendLine("***************************\n");
	
	            Console.Write(builder.ToString());
	        }
	
	
	        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	        {
	            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	            if (processor == null)
	                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	            return processor;
	        }
	    }
	}



##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0302_2016-->