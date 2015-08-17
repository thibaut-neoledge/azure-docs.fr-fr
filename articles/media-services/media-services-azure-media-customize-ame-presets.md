<properties 
	pageTitle="Manipuler les tâches d’encodage en personnalisant les présélections de tâches" 
	description="L’Encodeur Azure Media Services permet de transmettre des fichiers de présélections personnalisés à l’Encodeur multimédia Azure. Cette rubrique présente la personnalisation des fichiers de présélection pour réaliser les tâches suivantes : superposer une image sur une vidéo existante, contrôler les noms de fichiers de sortie produits par l’encodeur, assembler des vidéos." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#Manipuler les tâches d’encodage en personnalisant les présélections de tâches 

L’Encodeur Azure Media Services permet de transmettre des fichiers de présélections personnalisés à l’Encodeur multimédia Azure. Cette rubrique présente la personnalisation des fichiers de présélection pour réaliser les tâches suivantes :

- Superposer une image sur une vidéo existante 
- Contrôler les noms du fichier de sortie produits par l’encodeur 
- Assembler des vidéos
- Encoder des présentations contenant essentiellement des paroles

##Contrôle des noms de fichier de sortie de l’Encodeur multimédia Azure 

Par défaut, l’Encodeur multimédia Azure crée des noms de fichier de sortie en combinant différents attributs de la ressource d’entrée et du processus d’encodage. Chaque attribut est identifié à l’aide d’une macro, comme indiqué ci-dessous.

Voici une liste complète des macros disponibles pour la dénomination des fichiers de sortie : Débit audio : le débit utilisé pour l’encodage de l’audio, spécifié en kbit/s

- Codec audio : le codec utilisé pour l’encodage audio, les valeurs valides sont : AAC, WMA et DDP
- Nombre de canaux : nombre de canaux audio encodés, les valeurs valides sont : 1, 2 ou 6
- Extension par défaut : l’extension de fichier par défaut 
- Langage : le code de langage BCP-47 représentant le langage utilisé dans l’audio. La valeur actuelle par défaut est « und ». 
- Nom du fichier d’origine : le nom du fichier téléchargé dans Azure Storage
- ID de flux : l’ID de flux défini par l’attribut streamID de l’élément <StreamInfo> dans le fichier de présélection 
- Codec vidéo : le codec utilisé pour l’encodage, les valeurs valides sont : H264 et VC1
- Débit vidéo : le débit utilisé pour l’encodage de la vidéo, spécifié en kbit/s

Ces macros peuvent être combinées dans n’importe quel ordre pour contrôler le nom des fichiers générés par Media Services Encoder. Par exemple, la convention d’affectation de noms par défaut est :

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

La convention d’affectation de noms de fichier est spécifiée à l’aide de l’attribut DefaultMediaOutputFileName de l’élément [Preset](https://msdn.microsoft.com/library/azure/dn554334.aspx). Par exemple :

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

L’encodeur insère des traits de soulignement entre chaque macro, par exemple, la configuration ci-dessus donnerait un nom de fichier du type : MyVideo\_H264\_4500kpbs\_AAC\_und\_ch2\_128kbps.mp4.


##Création de superpositions

L’Encodeur Azure Media Services vous permet de superposer une image (jpg, bmp, gif, tif), une vidéo ou une piste audio (*.wma, .mp3, .wav) sur une vidéo existante. Cette fonctionnalité est similaire à celle d’Expression Encoder 4 (Service Pack 2).

###Superpositions avec l’Encodeur Media Services

Vous pouvez spécifier le moment où la superposition est présentée, la durée de la superposition et, pour les superpositions d’image/de vidéo, là où la superposition s’affiche à l’écran. Vous pouvez également faire apparaître ou disparaître les superpositions en fondu. Les fichiers audio et vidéo à superposer peuvent être contenus dans un ou plusieurs éléments multimédias. Les superpositions sont contrôlées par la présélection XML transmise à l’encodeur. Pour une description complète du schéma des présélections, consultez la page Schémas de l’Encodeur multimédia Azure. Les superpositions sont spécifiées dans l’élément <MediaFile>, comme indiqué dans l’extrait de présélection suivant :

	<MediaFile
	    ...
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="WholeSequence"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">
	    ...
	</MediaFile>

###Présélections pour les superpositions de vidéos ou d’images

Les superpositions peuvent provenir d’un seul ou de plusieurs éléments multimédias. Lorsque vous créez des superpositions vidéo avec plusieurs éléments multimédias, le nom du fichier de superposition est spécifié dans l’attribut OverlayFileName à l’aide de la syntaxe %n%, où n est l’index de base zéro des éléments multimédias d’entrée de la tâche d’encodage. Lorsque vous créez des superpositions vidéo avec un seul élément multimédia, le nom du fichier de superposition est spécifié directement dans l’attribut OverlayFileName, comme présenté dans les extraits de code de présélections suivants :

Exemple 1 :

	<!-- Multiple Assets -->
	<MediaFile
		...
		OverlayFileName="%1%"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

Exemple 2 :

	<!-- Single Asset -->
	<MediaFile
		...
		OverlayFileName="videoOverlay.mp4"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

L’emplacement et la taille de la superposition vidéo sont contrôlés par l’attribut OverlayRect. Le contenu à superposer est redimensionné pour tenir dans ce rectangle. L’opacité est contrôlée par l’attribut OverlayOpacity. Les valeurs valides sont 0,0 – 1,0, où 1,0 est 100 % opaque. La superposition s’affiche au moment spécifié par l’attribut OverlayStartTime et se termine au moment spécifié par l’attribut OverlayEndTime. Les attributs OverlayStartTime et OverlayEndTime doivent se situer dans la chronologie de la vidéo source. Pour plus d’informations sur chaque attribut spécifique à la superposition, consultez la page Schémas de l’Encodeur multimédia Azure.

###Présélections pour les superpositions audio

Les superpositions audio peuvent utiliser n’importe quel format de fichier audio pris en charge, par exemple. Pour obtenir la liste des formats de fichier audio pris en charge, consultez la page Formats pris en charge par l’encodeur Media Services. Les superpositions audio sont elles aussi spécifiées dans l’élément <MediaFile>, comme indiqué dans l’extrait de présélection suivant :

	<MediaFile
		...
		AudioOverlayFileName="%1%" <!-- or AudioOverlayFileName=”audioOverlay.mp3” for overlays from a single asset -->
		AudioOverlayLoop="True"
		AudioOverlayLoopingGap="00:00:00"
		AudioOverlayLayoutMode="Custom"
		AudioOverlayStartTime="00:05:00"
		AudioOverlayEndTime="00:10:00"
		AudioOverlayGainLevel="2.2"
		AudioOverlayFadeInDuration="00:00:00"
		AudioOverlayFadeOutDuration="00:00:00">

Lorsque vous créez des superpositions audio à partir de plusieurs éléments multimédias, le nom du fichier de superposition audio est spécifié dans l’attribut AudioOverlayFileName à l’aide de la syntaxe %n%, où n est l’index de base zéro de la collection des éléments multimédias d’entrée de la tâche d’encodage. Pour les superpositions audio stockées dans un seul élément multimédia, le nom du fichier de superposition est spécifié directement dans l’attribut AudioOverlayFileName. L’attribut AudioOverlayLayoutMode détermine quand la superposition audio est présentée. Lorsque cet attribut a la valeur « WholeSequence », la piste audio est présentée pendant toute la durée de la vidéo. Lorsqu’il a la valeur « Custom », les attributs AudioOverlayStartTime et AudioOverlayEndTime déterminent quand la superposition audio commence et se termine. Les attributs OverlayStartTime et OverlayEndTime doivent se situer dans la chronologie de la vidéo source. Pour plus d’informations sur tous les attributs de superposition audio, consultez la page Schémas de l’Encodeur multimédia Azure. Les superpositions audio peuvent être combinées avec des superpositions vidéos, comme indiqué dans l’extrait de présélection suivant :
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch"
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="Custom"
	    AudioOverlayStartTime="00:05:00"
	    AudioOverlayEndTime="00:10:00"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">


###Envoi de tâches avec des superpositions

Une fois que vous avez créé un fichier de présélection, effectuez les opérations suivantes :

- Chargement de vos éléments multimédias
- Chargement de la configuration de présélection (Remarque : le code ci-dessous suppose la présélection ci-dessus)
- Création d’un travail
- Obtention d’une référence pour l’Encodeur Media Services
- Création d’une tâche spécifiant la collection des éléments multimédias d’entrée, la configuration de la présélection, l’encodeur multimédia et l’élément multimédia de sortie
- Envoi du travail

L’extrait de code qui suit montre comment effectuer ces étapes :

	static public void CreateOverlayJob()
	{
	_context = new CloudMediaContext(_accountName, _accountKey);
	
	       // Upload assets to overlay
	       IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4); // this is the input mezzanine
	       IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);// this will be used as a video overlay
	       IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv); // this will be used as an audio overlay
	
	       // Load the preset configuration
	       string presetFileName = "OverlayPreset.xml";
	       string configuration = File.ReadAllText(presetFileName);
	
	       // Create a job
	       IJob job = _context.Jobs.Create("A WAME overlay job, using " + presetFileName);
	                 
	// Get a reference to the media services encoder   
	       IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	            
	        // Create a task    
	       ITask task = job.Tasks.AddNew("Encode Task for overlay, using " + presetFileName, processor, configuration, TaskOptions.None);
	
	    // Add the input assets
	           task.InputAssets.Add(inputAsset1);
	           task.InputAssets.Add(inputAsset2);
	           task.InputAssets.Add(inputAsset3);
	
	     // Add the output asset
	            task.OutputAssets.AddNew("Result of an overlay job, using " + presetFileName, AssetCreationOptions.None);
	
	     // Submit the job
	            job.Submit();
	}



>[AZURE.NOTE]Cet extrait charge chaque élément multimédia l’un après l’autre par souci de simplicité. En production, les éléments multimédias seraient chargés en bloc. Pour plus d’informations sur le chargement d’éléments multimédias en bloc, consultez la page [Réception d’éléments multimédias en bloc avec le Kit de développement logiciel (SDK) Media Services pour .NET](media-services-dotnet-upload-files.md#ingest_in_bulk).

Pour obtenir un exemple complet de code, consultez la page [Création de superpositions avec Media Services Encoder](https://code.msdn.microsoft.com/Creating-Audio-and-Video-c2942c47).

###Conditions d’erreur

Lorsque vous modifiez la chaîne de présélection, vous devez vérifier les points suivants :

- Pour les superpositions de vidéo ou d’images, le rectangle de superposition doit tenir entièrement dans la vidéo source.
- Le début et la fin des superpositions doivent se trouver dans la chronologie de la vidéo source.
- Si la présélection XML contient une référence à ?OverlayFileName=”%n%”, la collection InputAssets pour les tâches doit contenir au moins n+1 éléments multimédias



##Assemblage de séquences vidéo

L’Encodeur Media services prend en charge l’assemblage d’un jeu de vidéos. Les vidéos peuvent être assemblées ensemble bout à bout ou vous pouvez spécifier les parties d’une ou de deux vidéos à assembler. Le résultat de l’assemblage est un élément unique qui contient la vidéo spécifiée à partir des éléments multimédias d’entrée. Les fichiers vidéos à assembler peuvent se trouver dans plusieurs éléments multimédias ou dans un seul. L’assemblage est contrôlé par la présélection XML transmise à l’encodeur. Pour une description complète du schéma des présélections, consultez la page [Schémas de l’Encodeur multimédia Azure](https://msdn.microsoft.com/library/azure/dn584702.aspx).

###Assemblage avec l’Encodeur Media Services

L’assemblage est contrôlé dans l’élément <MediaFile>, comme illustré dans la présélection suivante :
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Pour que chaque vidéo soit assemblée, un élément <Source> est ajouté à l’élément <Sources>. Chaque élément <Source> contient un élément <Clips>. Chaque élément <Clips> contient un ou plusieurs éléments <Clip> spécifiant la quantité de vidéo à assembler dans l’élément multimédia de sortie, avec une heure de début et de fin. L’élément <Source> fait référence à l’élément multimédia sur lequel il agit. Le format de la référence varie selon que les vidéos à assembler se trouvent dans des éléments multimédias séparés ou dans un seul élément. Si vous voulez assembler toute une vidéo, omettez simplement l’élément <Clips>.

###Assemblage de vidéos à partir de plusieurs éléments multimédias

Lors de l’assemblage de vidéos à partir de plusieurs éléments multimédias, un index de base zéro est utilisé pour l’attribut MediaFile de l’élément <Source> pour identifier à quel élément multimédia correspond l’élément <Source>. L’index de base zéro n’est pas spécifié, l’élément <Source> qui ne spécifie pas d’attribut MediaFile référence le premier élément multimédia d’entrée. Tous les autres éléments <Source> doivent spécifier l’index de base zéro de l’élément multimédia d’entrée auquel il fait référence à l’aide de la syntaxe %n%, où n est l’index de base zéro de l’élément multimédia d’entrée. Dans l’exemple précédent, le premier élément <Source> spécifie le premier élément d’entrée multimédia, le deuxième élément <Source> spécifie le deuxième élément multimédia d’entrée, et ainsi de suite. Il n’est pas obligatoire que les éléments multimédias d’entrée soient référencés dans l’ordre, par exemple :
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip EndTime="00:00:05" 
	                StartTime="00:00:00" />
	        </Clips>
	                  
	        </Source>
	      <Source
	       AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Cet exemple assemble des parties du deuxième élément, le premier élément et le troisième élément multimédia d’entrée. Notez que puisque chaque vidéo est référencée par un index de base zéro, il est possible d’assembler deux vidéos qui portent le même nom. Une fois que vous avez créé un fichier de présélection, effectuez les opérations suivantes :
 
- Chargement de vos éléments multimédias
- Chargement de la configuration de présélection
- Création d’un travail
- Obtention d’une référence pour l’Encodeur Media Services
- Création d’une tâche spécifiant les éléments multimédias d’entrée, la configuration de la présélection, l’encodeur multimédia et l’élément multimédia de sortie
- Envoi du travail

L’extrait de code qui suit montre comment effectuer ces étapes :
	
	static public void StitchWithMultipleAssets()
	{
    		_context = new CloudMediaContext(_accountName, _accountKey);
	
	       // Upload assets to stitch
	       IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4);
	       IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);
	       IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv);
	
	       // Load the preset configuration
	       string presetFileName = "StitchingWithMultipleAssets.xml";
	       string configuration = File.ReadAllText(presetFileName);
	
	       // Create a job
	       IJob job = _context.Jobs.Create("A WAME stitching job, using " + presetFileName);
	                 
	// Get a reference to the media services encoder   
	       IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	            
	        // Create a task    
	       ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
	
			// Add the input assets
	       task.InputAssets.Add(inputAsset1);
	       task.InputAssets.Add(inputAsset2);
	       task.InputAssets.Add(inputAsset3);
	
	       // Add the output asset
	        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);
	
	       // Submit the job
	        job.Submit();
	} 


Cet extrait charge chaque élément multimédia l’un après l’autre par souci de simplicité. En production, les éléments multimédias seraient chargés en bloc. Pour plus d’informations sur le chargement d’éléments multimédias en bloc, consultez la page [Réception d’éléments multimédias en bloc avec le Kit de développement logiciel (SDK) Media Services pour .NET](media-services-dotnet-upload-files.md#ingest_in_bulk). Pour obtenir un exemple complet de code, consultez la page [Assemblage avec Media Services Encoder](https://code.msdn.microsoft.com/Stitching-with-Media-8fd5f203).

###Assemblage de vidéos à partir d’un seul élément multimédia

Lors de l’assemblage de vidéos à partir d’un seul élément multimédia, chaque vidéo doit avoir un nom unique. Les vidéos sont spécifiées à l’aide de l’attribut MediaFile avec le nom de fichier en tant que valeur de l’attribut. Par exemple :
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="video1.mp4">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	       AudioStreamIndex="0"
	       MediaFile="video2.wmv">
	
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="video3.wmv">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Cette présélection assemble des parties de video1.mp4, video2.wmv et video3.wmv dans l’élément multimédia de sortie. La création de travaux et de tâches est identique à l’assemblage de vidéos à partir de plusieurs éléments multimédias. Vous ne devez charger qu’un seul élément multimédia, comme illustré dans le code suivant :

	// Creates a stitching job that uses a single asset 
    static public void StitchWithASingleAsset()
    {
        string presetFileName = "StitchingWithASingleAsset.xml";
        string configuration = File.ReadAllText(presetFileName);
        _context = new CloudMediaContext(_accountName, _accountKey);

        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
        IJob job = _context.Jobs.Create("A WAME stitching job, using " + presetFileName);
        IAsset asset = CreateAssetAndUploadMultipleFiles(AssetCreationOptions.None, _stitchingFiles);

        ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);

        job.Submit();
    }

##Encodage de présentations ou de flux audio avec essentiellement des paroles
 
Lors de l’encodage de vidéos dont la piste audio contient essentiellement des paroles, les présélections d’encodeur par défaut peuvent créer un bruit de fond amplifié dans l’élément multimédia encodé. Ce comportement est dû au fait que l’attribut NormalizeAudio est défini sur true.

###Encodage de présentations contenant essentiellement des paroles

Pour empêcher l’amplification du bruit de fond, procédez comme suit :

1. Copiez le contenu de la présélection de l’encodeur que vous utilisez dans un fichier XML. Les présélections d’encodeur sont disponibles à la page Schémas de l’Encodeur multimédia Azure
1. Supprimez l’attribut NormalizeAudio, qui se trouve en haut du fichier de présélection, sous l’élément <MediaFile> :
	
	<MediaFile
	     DeinterlaceMode="AutoPixelAdaptive"
	     ResizeQuality="Super"
	     NormalizeAudio="True"
	     AudioGainLevel="1"
	     VideoResizeMode="Stretch">

1. Enregistrez le fichier de présélection modifié sur votre disque dur local et utilisez un code comme celui qui suit pour encoder avec la présélection personnalisée :
	
	// Upload file and create asset IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, @"C:\\TEMP\\Original.mp4");
	 
	string inputPresetFile = @"C:\\TEMP\\H264 Broadband 720p NoAudioNorm.xml"; string presetName = Path.GetFileNameWithoutExtension(inputPresetFile);
	 
	IJob job = \_context.Jobs.Create("Encode Job for " + asset.Name + ", encoded using " + presetName);
	
	Console.WriteLine("Encode Job for " + asset.Name + ", encoded using " + presetName);
	
	// Get a media processor reference, and pass to it the name of the processor to use for the specific task. IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); Console.WriteLine("Got MP " + processor.Name + ", ID : " + processor.Id + ", version: " + processor.Version);
	 
	// Read the configuration data into a string. string configuration = File.ReadAllText(inputPresetFile);
	 
	// Créez une tâche avec les détails de l’encodage, à l’aide d’une chaîne de présélection. ITask task = job.Tasks.AddNew("Encode Task for " + asset.Name + ", encoded using " + presetName, processor, configuration, Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
	 
	// Specify the input asset to be encoded. task.InputAssets.Add(asset);
	 
	// Add an output asset to contain the results of the job. task.OutputAssets.AddNew("Output asset for " + asset.Name + ", encoded using " + presetName, AssetCreationOptions.None);
	 
	// Launch the job. job.Submit();

##Voir aussi

[Schéma XML de l’Encodeur multimédia Azure](https://msdn.microsoft.com/library/azure/dn584702.aspx)

<!---HONumber=August15_HO6-->