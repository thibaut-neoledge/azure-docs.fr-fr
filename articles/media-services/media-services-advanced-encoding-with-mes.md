<properties 
	pageTitle="Encodage avancé avec Media Encoder Standard" 
	description="Cette rubrique explique comment effectuer un encodage avancé en personnalisant les présélections de tâches Media Encoder Standard. Elle décrit comment utiliser le Kit de développement logiciel (SDK) .NET de Media Services pour créer une tâche et un travail d’encodage. Elle explique également comment spécifier des présélections personnalisées pour le travail d’encodage." 
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
	ms.date="04/24/2016"    
	ms.author="juliako"/>


#Encodage avancé avec Media Encoder Standard

##Vue d’ensemble

Cette rubrique explique comment exécuter des tâches d’encodage avancé avec Media Encoder Standard. La présente rubrique décrit [comment utiliser .NET pour créer une tâche d’encodage et générer un travail qui exécute cette tâche](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet). Elle présente également la procédure à suivre pour attribuer des paramètres personnalisés et prédéfinis à la tâche d’encodage. Pour obtenir une description des éléments utilisés par les présélections, consultez [ce document](https://msdn.microsoft.com/library/mt269962.aspx).

Il présente les présélections personnalisées qui exécutent les tâches d’encodage suivantes :

- [Génération de miniatures](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [Rognage d’une vidéo (extrait)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [Création d’une superposition](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [Insertion d’une piste audio en mode silencieux lorsque l’entrée ne produit pas de son](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [Désactiver le désentrelacement automatique](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
- [Présélections audio uniquement](media-services-custom-mes-presets-with-dotnet.md#audio_only)
- [Concaténation de deux fichiers vidéo ou plus](media-services-custom-mes-presets-with-dotnet.md#concatenate)

##<a id="encoding_with_dotnet"></a>Encodage à l’aide du Kit de développement logiciel (SDK) .NET de Media Services

Le code suivant utilise le Kit de développement logiciel (SDK) .NET de Media Services pour effectuer les tâches suivantes :

- Création d’une tâche d’encodage.
- Obtention d’une référence à l’encodeur Media Encoder Standard.
- Chargement de la présélection XML ou JSON personnalisée. Vous pouvez enregistrer le code XML ou JSON (par exemple, [XML](media-services-custom-mes-presets-with-dotnet.md#xml) ou [JSON](media-services-custom-mes-presets-with-dotnet.md#json)) dans un fichier et utiliser le code suivant pour charger le fichier.

		// Load the XML (or JSON) from the local file.
	    string configuration = File.ReadAllText(fileName);  
- Ajout d’une tâche d’encodage au travail. 
- Spécification de l’élément multimédia d’entrée à encoder.
- Création d’un élément multimédia de sortie qui contiendra l’élément multimédia encodé.
- Ajout d’un gestionnaire d’événements pour vérifier la progression de la tâche.
- Envoyez le travail.
	
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Net;
		using System.Security.Cryptography;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Newtonsoft.Json.Linq;
		using System.Threading;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		using System.Web;
		using System.Globalization;
		
		namespace CustomizeMESPresests
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
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the chached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            // Get an uploaded asset.
		            var asset = _context.Assets.FirstOrDefault();
		
		            // Encode and generate the output using custom presets.
		            EncodeToAdaptiveBitrateMP4Set(asset);
		
		            Console.ReadLine();
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
				{
				    // Declare a new job.
				    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
				    // Get a media processor reference, and pass to it the name of the 
				    // processor to use for the specific task.
				    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
				
		
				    // Load the XML (or JSON) from the local file.
				    string configuration = File.ReadAllText("CustomPreset_JSON.json");
				
				    // Create a task
		            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		                processor,
		                configuration,
		                TaskOptions.None);
				
				    // Specify the input asset to be encoded.
				    task.InputAssets.Add(asset);
				    // Add an output asset to contain the results of the job. 
				    // This output is specified as AssetCreationOptions.None, which 
				    // means the output asset is not encrypted. 
				    task.OutputAssets.AddNew("Output asset",
				        AssetCreationOptions.None);
				
				    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
				    job.Submit();
				    job.GetExecutionProgressTask(CancellationToken.None).Wait();
				
				    return job.OutputMediaAssets[0];
				}
		
		        static public IAsset UploadMediaFilesFromFolder(string folderPath)
		        {
		            IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
		
		            foreach (var af in asset.AssetFiles)
		            {
		                // The following code assumes 
		                // you have an input folder with one MP4 and one overlay image file.
		                if (af.Name.Contains(".mp4"))
		                    af.IsPrimary = true;
		                else
		                    af.IsPrimary = false;
		
		                af.Update();
		            }
		
		            return asset;
		        }
		
		
		        static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
		        {
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		            // Get a media processor reference, and pass to it the name of the 
		            // processor to use for the specific task.
		            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		
		            // Load the XML (or JSON) from the local file.
		            string configuration = File.ReadAllText(customPresetFileName);
		
		            // Create a task
		            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input assets to be encoded.
		            // This asset contains a source file and an overlay file.
		            task.InputAssets.Add(assetSource);
		
		            // Add an output asset to contain the results of the job. 
		            task.OutputAssets.AddNew("Output asset",
		                AssetCreationOptions.None);
		
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
		
		    }
		}


##<a id="thumbnails"></a>Génération de miniatures

Cette section montre comment personnaliser une présélection qui génère des miniatures. La présélection définie ci-dessous contient des informations sur la façon dont vous souhaitez encoder votre fichier, ainsi que les informations nécessaires à la génération des miniatures. Vous pouvez utiliser l’une des présélections MES documentées [ici](https://msdn.microsoft.com/library/mt269960.aspx) et ajouter le code qui génère des miniatures.

>[AZURE.NOTE]Le paramètre **SceneChangeDetection** figurant dans la présélection qui suit ne peut présenter la valeur true que si votre encodage porte sur une vidéo à vitesse de transmission unique. Si votre encodage s’applique à une vidéo à plusieurs vitesses de transmission et que vous définissez **SceneChangeDetection** sur true, l’encodeur renverra une erreur.


Pour plus d’informations sur le schéma, consultez [cette](https://msdn.microsoft.com/library/mt269962.aspx) rubrique.

Assurez-vous d’examiner la section [Considérations](media-services-custom-mes-presets-with-dotnet.md#considerations).

###<a id="json"></a>Présélection JSON


	{
	  "Version": 1.0,
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "SceneChangeDetection": "true",
	      "H264Layers": [
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 4500,
	          "MaxBitrate": 4500,
	          "BufferWindow": "00:00:05",
	          "Width": 1280,
	          "Height": 720,
	          "ReferenceFrames": 3,
	          "EntropyMode": "Cabac",
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	   
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "JpgLayers": [
	        {
	          "Quality": 90,
	          "Type": "JpgLayer",
	          "Width": 640,
	          "Height": 360
	        }
	      ],
	      "Start": "{Best}",
	      "Type": "JpgImage"
	    },
	    {
	      "PngLayers": [
	        {
	          "Type": "PngLayer",
	          "Width": 640,
	          "Height": 360,
	        }
	      ],
	      "Start": "00:00:01",
		  "Step": "00:00:10",
	      "Range": "00:00:58",
	      "Type": "PngImage"
	    },
	    {
	      "BmpLayers": [
	        {
	          "Type": "BmpLayer",
	          "Width": 640,
	          "Height": 360
	        }
	      ],
	      "Start": "10%",
		  "Step": "10%",
	      "Range": "90%",
	      "Type": "BmpImage"
	    },
	    {
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "JpgFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "PngFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "BmpFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}


###<a id="xml"></a>Présélection XML


	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <SceneChangeDetection>true</SceneChangeDetection>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>4500</Bitrate>
	          <Width>1280</Width>
	          <Height>720</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>4500</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	    </H264Video>
	    <AACAudio>
	      <Profile>AACLC</Profile>
	      <Channels>2</Channels>
	      <SamplingRate>48000</SamplingRate>
	      <Bitrate>128</Bitrate>
	    </AACAudio>
	    <JpgImage Start="{Best}">
	      <JpgLayers>
	        <JpgLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	          <Quality>90</Quality>
	        </JpgLayer>
	      </JpgLayers>
	    </JpgImage>
	    <BmpImage Start="10%" Step="10%" Range="90%">
	      <BmpLayers>
	        <BmpLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	        </BmpLayer>
	      </BmpLayers>
	    </BmpImage>
	    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
	      <PngLayers>
	        <PngLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	        </PngLayer>
	      </PngLayers>
	    </PngImage>
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
	      <MP4Format />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <JpgFormat />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <BmpFormat />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <PngFormat />
	    </Output>
	  </Outputs>
	</Preset>

###Considérations

Les considérations suivantes s'appliquent :

- L’utilisation d’horodatages explicites pour Début/Étape/Plage suppose que la source d’entrée a une longueur minimale de 1 minute.
- Les éléments Jpg/Png/BmpImage possèdent les attributs de chaîne Start, Step et Range, qui peuvent être interprétés comme suit :

	- Entiers non négatifs : nombre d’images, par exemple "Start": "120"
	- Présence du suffixe % : durée par rapport à la source, par exemple "Start": "15%"
	- Format HH:MM:SS : horodatage, par exemple "Start": "00: 01:00"

	Vous pouvez combiner et apparier les notations à votre guise.
	
	En outre, Start prend également en charge une macro spéciale, {Best}, qui tente de déterminer la première image de contenu « intéressante ». REMARQUE : Step et Range sont ignorés quand Start est défini sur {Best}.
	
	- La configuration par défaut est « Start:{Best} ».
- Le format de sortie doit être fourni explicitement pour chaque format d’image : Png/Jpg/BmpFormat. Quand il est présent, MES fait correspondre JpgVideo à JpgFormat et ainsi de suite. OutputFormat introduit une nouvelle macro spécifique au codec d’image, {Index}, qui doit être présente (une fois seulement) pour les formats de sortie d’image.

##<a id="trim_video"></a>Rognage d’une vidéo (extrait)

Cette section explique comment modifier les présélections de l’encodeur pour découper ou rogner la vidéo d’entrée, dans laquelle l’entrée est ce que l’on appelle un fichier mezzanine ou un fichier à la demande. L’encodeur peut également servir à découper ou extraire un élément multimédia capturé ou archivé à partir d’un flux en direct. Pour plus d’informations à ce sujet, voir [ce blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Pour découper vos vidéos, vous pouvez utiliser l’une des présélections MES documentées [ici](https://msdn.microsoft.com/library/mt269960.aspx) et modifier l’élément **Sources** (comme indiqué ci-dessous). La valeur de StartTime doit correspondre aux horodatages absolus de la vidéo d'entrée. Par exemple, si la première image de la vidéo d'entrée a un horodatage de 12:00:10.000, la valeur de StartTime doit être égale ou supérieure à 12:00:10.000. Dans l'exemple ci-dessous, nous supposons que la vidéo d'entrée a un horodatage de début égal à zéro. Notez que **Sources** doit être placé au début de la présélection.
 
###<a id="json"></a>Présélection JSON
	
	{
	  "Version": 1.0,
	  "Sources": [
	    {
	      "StartTime": "00:00:04",
	      "Duration": "00:00:16"
	    }
	  ],
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "StretchMode": "AutoSize",
	      "H264Layers": [
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 3400,
	          "MaxBitrate": 3400,
	          "BufferWindow": "00:00:05",
	          "Width": 1280,
	          "Height": 720,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 2250,
	          "MaxBitrate": 2250,
	          "BufferWindow": "00:00:05",
	          "Width": 960,
	          "Height": 540,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 1500,
	          "MaxBitrate": 1500,
	          "BufferWindow": "00:00:05",
	          "Width": 960,
	          "Height": 540,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 1000,
	          "MaxBitrate": 1000,
	          "BufferWindow": "00:00:05",
	          "Width": 640,
	          "Height": 360,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 650,
	          "MaxBitrate": 650,
	          "BufferWindow": "00:00:05",
	          "Width": 640,
	          "Height": 360,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 400,
	          "MaxBitrate": 400,
	          "BufferWindow": "00:00:05",
	          "Width": 320,
	          "Height": 180,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "Profile": "AACLC",
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	} 

###Présélection XML
	
Pour découper vos vidéos, vous pouvez utiliser l’une des présélections MES documentées [ici](https://msdn.microsoft.com/library/mt269960.aspx) et modifier l’élément **Sources** (comme indiqué ci-dessous).

	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Sources>
	    <Source StartTime="PT4S" Duration="PT14S"/>
	  </Sources>
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>3400</Bitrate>
	          <Width>1280</Width>
	          <Height>720</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>3400</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>2250</Bitrate>
	          <Width>960</Width>
	          <Height>540</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>2250</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>1500</Bitrate>
	          <Width>960</Width>
	          <Height>540</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>1500</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>1000</Bitrate>
	          <Width>640</Width>
	          <Height>360</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>1000</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>650</Bitrate>
	          <Width>640</Width>
	          <Height>360</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>650</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>400</Bitrate>
	          <Width>320</Width>
	          <Height>180</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>400</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	    </H264Video>
	    <AACAudio>
	      <Profile>AACLC</Profile>
	      <Channels>2</Channels>
	      <SamplingRate>48000</SamplingRate>
	      <Bitrate>128</Bitrate>
	    </AACAudio>
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
	      <MP4Format />
	    </Output>
	  </Outputs>
	</Preset>

##<a id="overlay"></a>Création d’une superposition

Media Encoder Standard vous permet de superposer une image sur une vidéo existante. Les formats suivants sont actuellement pris en charge : png, jpg, gif et bmp. La présélection définie ci-dessous illustre un exemple de superposition vidéo de base.

Après avoir défini un fichier de présélection, vous devez également indiquer à Media Services quel fichier de la ressource représente l’image de superposition et quel fichier représente la vidéo source sur laquelle vous souhaitez superposer l’image. Le fichier vidéo doit être le fichier **principal**.

L’exemple .NET ci-dessus définit deux fonctions : **UploadMediaFilesFromFolder** et **EncodeWithOverlay**. La fonction UploadMediaFilesFromFolder charge les fichiers d'un dossier (par exemple, BigBuckBunny.mp4 et Image001.png) et définit le fichier mp4 comme fichier principal dans la ressource. La fonction **EncodeWithOverlay** utilise le fichier de présélection personnalisé qu’elle a reçu (par exemple, la présélection suivante) pour créer la tâche d’encodage.

>[AZURE.NOTE]Limitations actuelles :
>
>Le paramètre d’opacité de superposition n’est pas pris en charge.
>
>Votre fichier vidéo source et le fichier d'image de superposition doivent être dans le même élément multimédia, et le fichier vidéo doit être défini en tant que fichier principal dans cet élément multimédia.

###Présélection JSON
	
	{
	  "Version": 1.0,
	  "Sources": [
	    {
	      "Streams": [],
	      "Filters": {
	        "VideoOverlay": {
	          "Position": {
	            "X": 100,
	            "Y": 100,
	            "Width": 100,
	            "Height": 50
	          },
	          "AudioGainLevel": 0.0,
	          "MediaParams": [
	            {
	              "OverlayLoopCount": 1
	            },
	            {
	              "IsOverlay": true,
	              "OverlayLoopCount": 1,
	              "InputLoop": true
	            }
	          ],
	          "Source": "Image001.png",
	          "Clip": {
	            "Duration": "00:00:05"
	          },
	          "FadeInDuration": {
	            "Duration": "00:00:01"
	          },
	          "FadeOutDuration": {
	            "StartTime": "00:00:03",
	            "Duration": "00:00:04"
	          }
	        }
	      },
	      "Pad": true
	    }
	  ],
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "H264Layers": [
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 1045,
	          "MaxBitrate": 1045,
	          "BufferWindow": "00:00:05",
	          "ReferenceFrames": 3,
	          "EntropyMode": "Cavlc",
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "Width": "640",
	          "Height": "360",
	          "FrameRate": "0/1"
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "Type": "CopyAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}{Extension}",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}


###Présélection XML
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Sources>
	    <Source>
	      <Streams />
	      <Filters>
	        <VideoOverlay>
	          <Source>Image001.png</Source>
	          <Clip Duration="PT5S" />
	          <FadeInDuration Duration="PT1S" />
	          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
	          <Position X="100" Y="100" Width="100" Height="50" />
	          <Opacity>0</Opacity>
	          <AudioGainLevel>0</AudioGainLevel>
	          <MediaParams>
	            <MediaParam>
	              <IsOverlay>false</IsOverlay>
	              <OverlayLoopCount>1</OverlayLoopCount>
	              <InputLoop>false</InputLoop>
	            </MediaParam>
	            <MediaParam>
	              <IsOverlay>true</IsOverlay>
	              <OverlayLoopCount>1</OverlayLoopCount>
	              <InputLoop>true</InputLoop>
	            </MediaParam>
	          </MediaParams>
	        </VideoOverlay>
	      </Filters>
	      <Pad>true</Pad>
	    </Source>
	  </Sources>
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>1045</Bitrate>
	          <Width>640</Width>
	          <Height>360</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>0</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cavlc</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>1045</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	    </H264Video>
	    <CopyAudio />
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}{Extension}">
	      <MP4Format />
	    </Output>
	  </Outputs>
	</Preset>


##<a id="silent_audio"></a>Insertion d’une piste audio en mode silencieux lorsque l’entrée ne produit pas de son

Par défaut, si vous envoyez à l’encodeur une entrée contenant uniquement de la vidéo (sans contenu audio), l’élément multimédia de sortie regroupera les fichiers qui contiennent uniquement des données vidéo. Certains lecteurs ne sont peut-être pas capables de gérer ces flux de sortie. Dans ce cas, vous pouvez utiliser ce paramètre pour forcer l’encodeur à ajouter à la sortie une piste audio en mode silencieux.

Pour forcer l’encodeur à produire un élément multimédia contenant une piste audio en mode silencieux lorsque l’entrée ne comporte pas de son, spécifiez la valeur « InsertSilenceIfNoAudio ».

Vous pouvez utiliser l’une des présélections MES documentées [ici](https://msdn.microsoft.com/library/mt269960.aspx) et apporter la modification suivante :

###Présélection JSON

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###Présélection XML

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a id="deinterlacing"></a>Désactiver le désentrelacement automatique

Si les clients souhaitent que le contenu d’entrelacement soit automatiquement désentrelacé, aucune action n’est nécessaire. Quand le désentrelacement automatique est activé (par défaut), MES détecte automatiquement les images entrelacées et désentrelace uniquement les images marquées comme entrelacées.

Vous pouvez désactiver le désentrelacement automatique. Cette option n’est pas recommandée.

###Présélection JSON
	
	"Sources": [
	{
	 "Filters": {
	    "Deinterlace": {
	      "Mode": "Off"
	    }
	  },
	}
	]

###Présélection XML
	
	<Sources>
	<Source>
	  <Filters>
	    <Deinterlace>
	      <Mode>Off</Mode>
	    </Deinterlace>
	  </Filters>
	</Source>
	</Sources>


##<a id="audio_only"></a>Présélections audio uniquement

Cette section présente deux présélections MES audio uniquement : Audio AAC et Bonne qualité audio AAC.

###Audio AAC 

	{
	  "Version": 1.0,
	  "Codecs": [
	    {
	      "Profile": "AACLC",
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}

###Bonne qualité audio AAC

	{
	  "Version": 1.0,
	  "Codecs": [
	    {
	      "Profile": "AACLC",
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 192,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}

##<a id="concatenate"></a>Concaténation de deux fichiers vidéo ou plus

L'exemple suivant décrit comment générer une présélection pour concaténer deux fichiers vidéo ou plus. Le scénario le plus courant consiste à ajouter une amorce de début ou de fin à la vidéo principale. Dans le cadre de l'utilisation prévue, les fichiers vidéo en cours de modification conjointe partagent les mêmes propriétés (résolution vidéo, fréquence d'images, nombre de pistes audio, etc.). Vous devez prendre soin de ne pas mélanger des vidéos de différentes fréquences d’images ou comportant un nombre différent de pistes audio.

###Conditions requises et éléments à prendre en compte

- Les vidéos d'entrée ne doivent avoir qu'une seule piste audio.
- Les vidéos d'entrée doivent avoir la même fréquence d'images.
- Vous devez télécharger vos vidéos dans des éléments multimédias séparés et définir les vidéos comme fichier primaire de chaque élément multimédia.
- Vous devez connaître la durée de vos vidéos.
- Les exemples prédéfinis ci-dessous supposent que toutes les vidéos d'entrée commencent avec un timestamp égal à zéro. Vous devez modifier les valeurs StartTime si les vidéos ont un timestamp de début différent, comme c'est généralement le cas avec les archives en direct.
- La présélection JSON fait des références explicites aux valeurs AssetID des éléments multimédias d’entrée.
- L'exemple de code suppose que la présélection JSON a été enregistrée dans un fichier local, par exemple « C:\\supportFiles\\preset.json ». Il suppose également que les deux éléments multimédias ont été créés en téléchargeant deux fichiers vidéo et que vous connaissez les valeurs AssetID résultantes.
- L'extrait de code et la présélection JSON montrent un exemple de concaténation de deux fichiers vidéo. Vous pouvez l'étendre à plus de deux vidéos en :

	1. Appelant task.InputAssets.Add() à plusieurs reprises pour ajouter d’autres vidéos, dans l'ordre.
	2. Apportant les modifications correspondantes à l'élément « Sources » dans le JSON, en ajoutant d’autres entrées, dans le même ordre. 


###Code .NET

	
	IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
	IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();
	
	// Declare a new job.
	IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
	// Get a media processor reference, and pass to it the name of the 
	// processor to use for the specific task.
	IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
	
	// Load the XML (or JSON) from the local file.
	string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");
	
	// Create a task
	ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
	    processor,
	    configuration,
	    TaskOptions.None);
	
	// Specify the input videos to be concatenated (in order).
	task.InputAssets.Add(asset1);
	task.InputAssets.Add(asset2);
	// Add an output asset to contain the results of the job. 
	// This output is specified as AssetCreationOptions.None, which 
	// means the output asset is not encrypted. 
	task.OutputAssets.AddNew("Output asset",
	    AssetCreationOptions.None);
	
	job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
	job.Submit();
	job.GetExecutionProgressTask(CancellationToken.None).Wait();

###Présélection JSON

Mettez à jour votre présélection personnalisée avec les ID des éléments multimédias à concaténer et le segment de temps approprié pour chaque vidéo.

	{
	  "Version": 1.0,
	  "Sources": [
	    {
	      "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
	      "StartTime": "00:00:01",
	      "Duration": "00:00:15"
	    },
	    {
	      "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
	      "StartTime": "00:00:02",
	      "Duration": "00:00:05"
	    }
	  ],
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "SceneChangeDetection": true,
	      "H264Layers": [
	        {
	          "Level": "auto",
	          "Bitrate": 1800,
	          "MaxBitrate": 1800,
	          "BufferWindow": "00:00:05",
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "Width": "640",
	          "Height": "360",
	          "FrameRate": "0/1"
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}
	

##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Voir aussi 

[Vue d’ensemble de l’encodage de Media Services](media-services-encode-asset.md)

<!---HONumber=AcomDC_0427_2016-->