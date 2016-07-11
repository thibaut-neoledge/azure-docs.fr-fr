<properties
	pageTitle="Détecter les mouvements avec Azure Media Analytics"
	description="Le processeur multimédia Azure Media Motion Detector vous permet d’identifier efficacement les passages intéressants dans une vidéo qui, autrement, serait longue et monotone."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/22/2016"  
	ms.author="milanga;juliako;"/>
 
# Détecter les mouvements avec Azure Media Analytics

##Vue d'ensemble

Le processeur multimédia **Azure Media Motion Detector** vous permet d’identifier efficacement les passages intéressants dans une vidéo qui, autrement, serait longue et monotone. La détection de mouvement peut être utilisée sur des séquences d’une caméra fixe pour identifier les passages de la vidéo où un mouvement se produit. Elle génère un fichier JSON contenant des métadonnées avec des horodateurs et le cadre de limitation de la vidéo où s’est produit l’événement.

Ciblant les vidéos de surveillance, cette technologie est en mesure de classer les mouvements en événements pertinents et en faux positifs, tels que les ombres et les variations d’éclairage. Cela vous permet de générer des alertes de sécurité à partir de séquences vidéo sans perdre de temps avec d’innombrables faux positifs, et tout en accédant rapidement aux moments clés dans des vidéos de surveillance extrêmement longues.

Le processeur multimédia **Azure Media Motion Detector** est uniquement disponible en version préliminaire.

Cette rubrique fournit des informations détaillées sur **Azure Media Motion Detector** et illustre son utilisation avec le Kit de développement logiciel (SDK) Media Services pour .NET

##Fichiers d’entrée du détecteur de mouvement

Fichiers vidéo. Les formats suivants sont actuellement pris en charge : MP4, MOV et WMV.

##Fichiers de sortie du détecteur de mouvement

Une tâche de détection de mouvement renvoie un fichier JSON dans l’élément multimédia de sortie qui décrit les alertes de mouvement et leurs catégories dans la vidéo. Le fichier contient des informations sur l’heure et la durée du mouvement détecté dans la vidéo.

Actuellement, la détection de mouvement prend uniquement en charge la catégorie de mouvement générique, qui est appelée ***type 2*** dans la sortie.

Les coordonnées X et Y et les tailles sont répertoriées entre 0,0 et 1,0 à l’aide d’un flottant normalisé. Multipliez cela par la résolution de hauteur et de largeur de la vidéo pour obtenir le cadre de limitation de la région dans laquelle un mouvement a été détecté.

Chaque sortie est divisée en fragments, puis subdivisée en intervalles pour définir les données au sein de la vidéo. Les fragments ne doivent pas nécessairement être d’une longueur égale, et peuvent même contenir des séquences dans lesquelles aucun mouvement n’a été détecté.

L’API de détecteur de mouvement indique lorsqu’un mouvement a été détecté dans une vidéo d’arrière-plan fixe (par exemple, une vidéo de surveillance). Le détecteur de mouvement est optimisé pour réduire au minimum les fausses alertes, telles que les ombres et les variations d’éclairage. Les limitations actuelles des algorithmes incluent les vidéos en vision nocturne, les objets semi-transparents et les petits objets.

###<a id="output_elements"></a>Éléments du fichier de sortie JSON

Le tableau suivant décrit les éléments du fichier de sortie JSON.

Élément|Description
---|---
Version|Cela vaut pour la version de l’API vidéo.
Échelle de temps|« Cycles » par seconde de la vidéo.
Offset|Le décalage des horodatages en « cycles ». Cette valeur sera toujours 0 dans la version 1.0 des API vidéo. Cette valeur est susceptible d’être modifiée dans les scénarios pris en charge ultérieurement.
Framerate|Images par seconde de la vidéo.
Width, Height|Fait référence à la largeur et à la hauteur de la vidéo en pixels.
Démarrer|L’horodatage de début en « cycles ».
Durée|La durée de l’événement en « cycles ».
Intervalle|L’intervalle de chaque entrée dans l’événement en « cycles ».
Événements|Chaque fragment d’événement contient le mouvement détecté pendant cette durée.
Type|Dans la version actuelle, cette valeur est toujours de « 2 » pour le mouvement générique. Ce libellé permet aux API vidéo de classer le mouvement dans les versions ultérieures.
RegionID|Comme expliqué ci-dessus, cette valeur sera toujours « 0 » dans la présente version. Ce libellé permet aux API vidéo de détecter du mouvement dans différentes régions dans les versions ultérieures.
Régions|Fait référence à la zone dans la vidéo où un mouvement est susceptible de vous intéresser. Il est impossible de spécifier une région dans la version actuelle des API vidéo ; le mouvement sera détecté dans l’ensemble de la surface de la vidéo à la place.<br/>-ID représente la zone de la région ; dans cette version, la seule valeur existante est ID 0. <br/>-Rectangle représente la forme de la région dans laquelle un mouvement vous intéresse. Dans cette version, il s’agit toujours d’un rectangle. <br/>-Les dimensions de la région sont en X, Y, Width et Height. Les coordonnées X et Y représentent les coordonnées XY de l’angle supérieur gauche de la région sur une échelle normalisée de 0,0 à 1,0. La largeur et la hauteur représentent la taille de la région sur une échelle normalisée de 0,0 à 1,0. Dans la version actuelle, X, Y, Width et Height sont toujours fixés à 0, 0 et 1, 1.<br/>-Fragments Les métadonnées sont mémorisées dans différents segments appelés fragments. Chaque fragment contient des valeurs de début (start), de durée (duration), un numéro d’intervalle et des événements (event). Un fragment sans aucun événement signifie qu’aucun mouvement n’a été détecté pendant cette heure de début et la durée.
Crochets|Chaque crochet représente un intervalle dans l’événement. Les crochets vides pour cet intervalle signifient qu’aucun mouvement n’a été détecté.
 

##Configuration de la tâche (préconfiguration)

Lors de la création d’une tâche de vidéo **Azure Media Motion Detector**, vous devez spécifier une présélection de configuration. Actuellement, il n’est pas possible de définir des options dans la présélection de configuration Azure Media Motion Detector. Vous devez fournir la présélection de configuration minimale suivante :

	{"version":"1.0"}

##Exemples de vidéos et de sorties du détecteur de mouvement

###Exemple avec un mouvement réel

[Exemple avec un mouvement réel](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fd54876c6-89a5-41de-b1f4-f45b6e10a94f%2FGarage.ism%2Fmanifest)

###Sortie JSON

	 {
	 "version": "1.0",
	 "timescale": 60000,
	 "offset": 0,
	 "framerate": 30,
	 "width": 1920,
	 "height": 1080,
	 "regions": [
	   {
	     "id": 0,
	     "type": "rectangle",
	     "x": 0,
	     "y": 0,
	     "width": 1,
	     "height": 1
	   }
	 ],
	 "fragments": [
	   {
	     "start": 0,
	     "duration": 68510
	   },
	   {
	     "start": 68510,
	     "duration": 969999,
	     "interval": 969999,
	     "events": [
	       [
	         {
	           "type": 2,
	           "regionId": 0
	         }
	       ]
	     ]
	   },
	   {
	     "start": 1038509,
	     "duration": 41489
	   }
	 ]
	}

###Exemple avec des faux positifs

[Exemple avec des faux positifs (variations d’éclairage) :](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Ffdc6656b-1c10-4f3f-aa7c-07ba073c1615%2FLivingRoomLight.ism%2Fmanifest&tech=flash)

###Sortie JSON

	{
	    "version": "1.0",
	    "timescale": 30000,
	    "offset": 0,
	    "framerate": 29.97,
	    "width": 1920,
	    "height": 1080,
	    "regions": [
	    {
	        "id": 0,
	        "type": "rectangle",
	        "x": 0,
	        "y": 0,
	        "width": 1,
	        "height": 1
	    }
	    ],
	    "fragments": [
	    {
	        "start": 0,
	        "duration": 320320
	    }
	    ]
	}


##Limitations

- Les formats de fichier vidéo d’entrée pris en charge incluent WMV, MOV et MP4.
- La détection de mouvement est optimisée pour les vidéos dont l’arrière-plan est fixe. L’algorithme est axé sur la réduction des fausses alertes, telles que les variations d’éclairage et les ombres.
- Certains mouvements peuvent ne pas être détectés en raison de défis techniques ; par exemple, des vidéos en vision nocturne, les objets semi-transparents et les petits objets.


## Exemple de code

Le programme suivant montre comment effectuer les tâches suivantes :

1. Créer un élément multimédia et charger un fichier multimédia dans l’élément multimédia.
1. Créer un travail avec une tâche de détection de mouvement vidéo basée sur un fichier de configuration qui contient la présélection JSON suivante :
					
		{
		    "version": "1.0"
		}

1. Télécharger les fichiers JSON de sortie.
		 
        using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace VideoMotionDetection
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
		
		            // Run the VideoMotionDetection job.
		            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\VideoMotionDetection\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
		        }
		
		        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Video Motion Detection Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Video Motion Detection Job");
		
		            // Get a reference to Azure Media Motion Detector.
		            string MediaProcessorName = "Azure Media Motion Detector";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
		
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


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Liens connexes

[Vue d’ensemble d’Azure Media Services Analytics](media-services-analytics-overview.md)

[Démonstrations Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0629_2016-->