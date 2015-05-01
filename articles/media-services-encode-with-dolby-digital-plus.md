<properties 
	pageTitle="Encodage de vos médias avec Dolby Digital Plus" 
	description="Cette rubrique explique comment encoder vos médias avec Dolby Digital Plus." 
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
	ms.date="03/01/2015" 
	ms.author="juliako"/>

# Encodage de vos médias avec Dolby Digital Plus

L'Encodeur multimédia Azure prend en charge l'encodage **Dolby(r) Digital Plus**. Dolby(r) Digital Plus, ou E-AC-3 (Enhanced AC-3), est un codec audio à effet surround avancé qui a été spécialement conçu pour des médias en constante évolution. Sur votre home cinéma, votre ordinateur et votre téléphone mobile ou encore dans le cadre d'une diffusion en continu en ligne, Dolby Digital Plus offre un son haute-fidélité. Vous bénéficiez ainsi sur tous vos appareils de l'expérience cinématographique Dolby. Dolby Digital Plus repose sur les principales technologies Dolby Digital, norme établie de l'effet surround pour le cinéma, la radio ou télé diffusion et le home cinéma. Face à la prolifération des appareils mobiles, Dolby Digital Plus est aussi en passe de devenir la norme en matière de divertissement mobile. Ses nouvelles technologies avancées en matière d'amélioration audio garantissent un son d'une qualité accrue et une meilleure gestion de la bande passante. Vous profitez de contenus d'exception avec moins d'interruptions, même quand la bande passante est limitée.


## Configurer l'Encodeur multimédia Azure pour un encodage avec Dolby Digital Plus

### Se procurer le processeur de l'Encodeur multimédia Azure 

Dolby Digital Plus est pris en charge par l'Encodeur multimédia Azure. Pour obtenir une référence sur l'**Encodeur multimédia Azure**, consultez la rubrique [Se procurer des processeurs multimédias](media-services-get-media-processor.md).

### <a id="configure_preset"></a>Configurer les paramètres de l'Encodeur multimédia Azure

Pendant la configuration des paramètres d'encodage à utiliser avec l'Encodeur multimédia Azure, certaines présélections prédéfinies étaient représentées par des chaînes faciles à retenir. L'encodeur Dolby Digital Plus fournit un large éventail de contrôles. Pour plus d'informations, consultez [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx). Il n'existe donc aucune présélection de chaîne préintégrée qui utilise ce codec. Vous devez spécifier les paramètres d'encodage souhaités dans un fichier XML et soumettre ces données avec votre tâche, comme illustré dans l'exemple de code suivant :
	
	string configuration = File.ReadAllText(pathToXMLConfigFile));

    ITask task = job.Tasks.AddNew("My Dolby Digital Plus Encode Task",
        processor,
        configuration,
        _clearConfig);

Cette rubrique contient plusieurs exemples de présélections XML qui permettent de configurer les paramètres d'encodage. L'élément utilisé pour configurer l'encodage Dolby Digital Plus est [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx) qui se présente sous la forme d'un nœud enfant de l'élément <AudioProfile> dans une présélection XML de l'Encodeur multimédia Azure. Cet élément XML contient plusieurs attributs qui contrôlent les différents éléments de l'encodage.

## Encodage en Dolby Digital Plus 5.1 multicanal

Pour encoder en Dolby Digital Plus 5.1 multicanal, définissez les attributs Codec et EncoderMode avec la valeur " DolbyDigitalPlus ". Le nombre de canaux encodés est spécifié à l'aide de l'attribut AudioCodingMode de l'élément <DolbyDigitalPlusAudioProfile>. Pour un encodage multicanal 5.1, attribuez à AudioCodingMode la valeur " Mode32 ". 

La présélection XML suivante contient une présélection XML complète de l'Encodeur multimédia Azure qui génère un fichier MP4 avec une vidéo à 1080p et large bande H264 et un son Dolby Digital Plus 5.1 multicanal. Cette présélection spécifie aussi l'encodage d'un canal LFE (Low-Frequency Effects) à l'aide de l'attribut LFEOn, qui est défini avec la valeur true. Les attributs non spécifiés conservent leurs valeurs par défaut.

Cette présélection XML doit être transmise à l'**Encodeur multimédia Azure** pour créer une tâche d'encodage, comme décrit dans [cette rubrique](media-services-dotnet-Encoder-asset.md) (sauf qu'au lieu d'une chaîne de présélection prédéfinie, vous transmettrez la présélection XML entière, comme décrit [ici](#configure_preset)).


	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode32"
	              LFEOn="True"
	              SamplesPerSecond="48000"
	              BandwidthLimitingLowpassFilter="True"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="512"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="3"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Balanced"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="64"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	                <StreamInfo
	                  Size="1920, 1080">
	                  <Bitrate>
	                    <ConstantBitrate
	                      Bitrate="6000"
	                      IsTwoPass="False"
	                      BufferWindow="00:00:05" />
	                  </Bitrate>
	                </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

## Encodage en Dolby Digital Plus Stereo

Pour encoder en Dolby Digital Plus Stereo, définissez les attributs Codec et EncoderMode avec la valeur " DolbyDigitalPlus ". Le nombre de canaux encodés est spécifié à l'aide de l'attribut AudioCodingMode. Pour un encodage stéréo, attribuez à AudioCodingMode la valeur " Mode20 ". L'exemple de présélection XML suivant illustre le paramètre <DolbyDigitalPlusAudioProfile> utilisé pour encoder au format audio 5.1. Les attributs non spécifiés conservent leurs valeurs par défaut.

Cette présélection XML doit être transmise à l'**Encodeur multimédia Azure** pour créer une tâche d'encodage, comme décrit dans [cette rubrique](media-services-dotnet-Encoder-asset.md) (sauf qu'au lieu d'une chaîne de présélection prédéfinie, vous transmettrez la présélection XML entière, comme décrit [ici](#configure_preset)).

	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode20"
	              LFEOn="False"
	              SamplesPerSecond="48000"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="128"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="1"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Speed"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="32"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	              <StreamInfo
	                Size="852, 480">
	                <Bitrate>
	                  <ConstantBitrate
	                    Bitrate="2200"
	                    IsTwoPass="False"
	                    BufferWindow="00:00:05" />
	                </Bitrate>
	              </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

## Encodage vers plusieurs fichiers MP4 

Vous pouvez encoder vers plusieurs fichiers MP4 dans une même présélection XML. Pour chaque fichier MP4 à générer, ajoutez un élément <Preset> dans la configuration. Chaque élément <Preset> peut contenir des informations de configuration pour la vidéo, le son ou les deux.

### Configuration

La configuration suivante génère les sorties suivantes :

- 8 fichiers MP4 vidéo uniquement
	- Vidéo 1080p @ 6 000 Kbits/s
	- Vidéo 1080p @ 4 700 Kbits/s
	- Vidéo 720p @ 3 400 Kbits/s
	- Vidéo 960 x 540 @ 2 250 Kbits/s
	- Vidéo 960 x 540 @ 1 500 Kbits/s
	- Vidéo 640 x 380 @ 1 000 Kbits/s
	- Vidéo 640 x 380 @ 650 Kbits/s
	- Vidéo 320 x 180 @ 400 Kbits/s

- 5 fichiers MP4 audio uniquement
	- AAC Audio Stereo @ 128 Kbits/s
	- AAC Audio 5.1 @ 512 Kbits/s
	- Dolby Digital Plus Stereo @ 128 Kbits/s
	- Dolby Digital Plus 5.1 multicanal @ 512 Kbits/s
	- AAC Stereo @ 56 Kbits/s
- Un manifeste A .ism
- Un fichier XML répertoriant les propriétés des fichiers MP4 générés.
		
		<?xml version="1.0" encoding="utf-16"?>
		<!--Created for Azure Media Encoder, May 16 2013 -->
		<Presets>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"   
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <VideoProfile Condition="SourceContainsVideo">
		            <HighH264VideoProfile
		              BFrameCount="3"
		              EntropyMode="Cabac"
		              RDOptimizationMode="Speed"
		              HadamardTransform="False"
		              SubBlockMotionSearchMode="Speed"
		              MultiReferenceMotionSearchMode="Balanced"
		              ReferenceBFrames="False"
		              AdaptiveBFrames="True"
		              SceneChangeDetector="True"
		              FastIntraDecisions="False"
		              FastInterDecisions="False"
		              SubPixelMode="Quarter"
		              SliceCount="0"
		              KeyFrameDistance="00:00:05"
		              InLoopFilter="True"
		              MEPartitionLevel="EightByEight"
		              ReferenceFrames="4"
		              SearchRange="64"
		              AutoFit="True"
		              Force16Pixels="False"
		              FrameRate="0"
		              SeparateFilesPerStream="True"
		              SmoothStreaming="False"
		              NumberOfEncoderThreads="0">
		              <Streams
		                AutoSize="False"
		                FreezeSort="False">
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="6000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="4700"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1280, 720">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="3400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="2250"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1500"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="650"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="320, 180">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		              </Streams>
		            </HighH264VideoProfile>
		          </VideoProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="PropagateSourceTimeStamps">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="6"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="2"
		              AudioCodingMode="Mode20"
		              LFEOn="False"
		              NinetyDegreePhaseShiftSurrounds="False"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="False"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile Condition="SourceContainsAudio">
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="6"
		              AudioCodingMode="Mode32"
		              LFEOn="True"
		              NinetyDegreePhaseShiftSurrounds="True"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="True"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="56"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		</Presets>

## Création de services d'encodage commerciaux

Certains clients peuvent souhaiter créer un service d'encodage commercial basé sur Azure Media Services. Si vous décidez de créer un tel service, il est important que tous les paramètres d'encodage Dolby Digital Plus soient disponibles. Assurez-vous que tous les paramètres contenus dans la balise <DolbyDigitalPlusAudioProfile> sont affichés et configurables par l'utilisateur final. Pour savoir comment rendre ces paramètres disponibles, contactez prolicensingsupport@dolby.com.

## Utilisation du support DPLM (Dolby Professional Loudness Metering)

L'Encodeur mutimédia Azure peut utiliser le Kit de développement logiciel (SDK) DPLM pour mesurer le volume sonore des dialogues dans l'entrée audio et définir la valeur correcte de DialogNormalization. Cette fonctionnalité est activée uniquement si l'encodage audio est effectué en Dolby Digital Plus. DPLM est configuré dans un fichier de configuration prédéfini à l'aide de l'élément <LoudnessMetering>, qui est un enfant de l'élément <DolbyDigitalPlusAudioProfile>. L'exemple de présélection suivant montre comment configurer DPLM :
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset
	  Version="5.0">
	  <Job />
	  <MediaFile>
	    <OutputFormat>
	      <MP4OutputFormat
	        StreamCompatibility="Standard">
	    <AudioProfile>
	             <DolbyDigitalPlusAudioProfile
	               Codec="DolbyDigitalPlus"
	               EncoderMode="DolbyDigitalPlus"
	               DolbySurroundMode="NotIndicated"
	               StereoDownmixPreference="NotIndicated"
	               SamplesPerSecond="48000"
	               AudioCodingMode="Mode20"
	               Channels="2"
	               BitsPerSample="24">
	               <LoudnessMetering
	                 Mode= "ITU_R_BS_1770_2_DI"
	                 SpeechThreshold="20"
	                 TruePeakEmphasis="false"
	                 TruePeakDCBlock="false" />
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="320"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	     </DolbyDigitalPlusAudioProfile>
	    </AudioProfile>
	      </MP4OutputFormat>
	    </OutputFormat>
	  </MediaFile>
	</Preset>

L'élément <LoudnessMetering> peut uniquement être spécifié dans un élément <DolbyDigitalPlusAudioProfile>. Si l'élément <LoudnessMetering> est utilisé, l'attribut DialogNormalization ne doit pas être utilisé. L'encodeur génère une erreur si l'élément <LoudnessMetering> et l'attribut DialogNormalization sont tous deux utilisés. Tous les attributs de LoudnessMetering sont facultatifs, et l'encodeur applique par défaut les valeurs recommandées par Dolby Laboratories, Inc.

Chaque attribut est décrit dans les sections suivantes.

### Attribut Mode

Cet attribut détermine le mode de mesure du niveau sonore. Les valeurs autorisées sont les suivantes :

 
**ITU_R_BS_1770_2_DI** (par défaut) : indique ITU-R BS.1770-2 plus Dialogue Intelligence

**ITU_R_BS_1770_1_DI** : indique ITU-R BS.1770-1 plus Dialogue Intelligence

**ITU_R_BS_1770_2** : indique ITU-R BS.1770-2

**LEQA_DI** : indique Leq(A) plus Dialogue Intelligence

**Remarque :**

Le mode**EBU R128** peut être obtenu avec **ITU_R_BS_1770_2_DI**

**Leq(A)** est inclus uniquement pour des raisons liées au système antérieur et ne doit être utilisé que dans des flux de travail hérités spécifiques

L'**ITU** a récemment proposé une mise à jour intitulée BS.1770-3, équivalente à BS.1770-2 avec les propriétés TruePeakDCBlock et TruePeakEmphasis définies avec la valeur false

### Attribut SpeechThreshold

Spécifie un seuil vocal utilisé par DPLM pour produire un résultat de niveau sonore intégré (sélection entre régulation vocale, régulation de niveau et pas de régulation, par exemple). La plage des valeurs de seuil vocal est comprise entre 0 % et 100 %, par incréments de 1 %. Ce paramètre produit un effet uniquement quand DPLM est configuré dans un mode qui utilise Dialogue Intelligence, ce qui signifie qu'il peut uniquement être spécifié si Mode est défini selon ITU_R_BS_1770_2_DI ou ITU_R_BS_1770_1_DI. Quand Mode correspond à ITU_R_BS_1770_2_DI ou ITU_R_BS_1770_1_DI, la valeur par défaut est 20 %. Les valeurs de cet attribut doivent se situer dans la plage 0, 1 - 100.

### Attribut TruePeakDCBlock

Ce paramètre d'entrée précise si le bloc DC au sein de la mesure true‐peak est activé (true) ou désactivé (false). Pour plus d'informations sur le bloc DC, reportez-vous à ITU‐R BS.1770‐2. La valeur par défaut est false.

### Attribut TruePeakEmphasis

Précise si le filtre d'accentuation dans la mesure true‐peak est activé (true) ou désactivé (false). Pour plus d'informations sur le filtre d'accentuation, reportez-vous à ITU‐R BS.1770‐2. La valeur par défaut est false.


### Résultats DPLM

Quand une tâche d'encodage spécifie l'utilisation de DPLM, les résultats de la mesure du niveau sonore sont inclus dans le XML des métadonnées de l'élément de sortie. Vous trouverez ci-dessous un exemple.
	
	<LoudnessMeteringResultParameters 
	     DPLMVersionInformation="Dolby Professional Loudness Metering Development Kit Version 1.0"
	     DialogNormalization="-15" 
	     IntegratedLoudness="-14.8487606" 
	     IntegratedLoudnessGatingMethod="2" 
	     IntegratedLoudnessSpeechPercentage="11.673481" 
	     SamplePeak="-0.7028221" 
	     TruePeak="0.705999851" />

Chaque attribut est décrit ci-dessous.

**DPLMVersionInformation** : chaîne représentant la version du Kit de développement logiciel (SDK) DPLM utilisé.

**DialogNormalization** : valeur de DialNorm mesurée, en décibels, à partir de l'entrée audio est incorporée dans le flux DD+ de sortie, dans la plage {-31, -30, ..., -1} dB.

**IntegratedLoudness** : volume intégré tel que mesuré par DPLM, dans la plage -70 à +10 LKFS/dBFS (dBFS est utilisé uniquement quand Mode est défini sur LEQA_DI).

**IntegratedLoudnessGatingMethod** : les valeurs valides sont les suivantes : 0 - Aucun/Pas de fenêtre de mesure (None/Ungated) ; 1 - Signaux vocaux sur intervalle (Speech Gated) ; 2 - Niveau mesuré sur l'intervalle (Level Gated).

**IntegratedLoudnessSpeechPercentage** : ce résultat contient le pourcentage de la chronologie du média d'entrée quand des signaux vocaux sont détectés. Plage de valeurs comprise entre 0 % et 100 %.

**SamplePeak** : ce résultat contient la valeur d'échantillonnage absolue la plus élevée dans tout canal depuis la réinitialisation de la mesure, et se situe entre -70 et +10 dBFS.

**TruePeak** : ce résultat contient la valeur true‐peak absolue la plus élevée dans tout canal depuis la réinitialisation de la mesure. Pour obtenir la description du niveau true peak, consultez ITU‐R BS.1770‐2. La plage de valeurs est comprise entre -70 et 12,04 dBTP.


<!--HONumber=52-->