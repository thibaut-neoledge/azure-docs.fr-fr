<properties 
	pageTitle="Codecs et formats standard de l’encodeur multimédia" 
	description="Cette rubrique fournit une vue d’ensemble des codecs et formats standard de l’encodeur multimédia Azure." 
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
	ms.date="09/16/2015"
	ms.author="juliako"/>

#Codecs et formats standard de l’encodeur multimédia


Ce document contient la liste des formats de fichier d’importation et d’exportation les plus courants que vous pouvez utiliser avec l’encodeur multimédia.


##Formats de conteneurs/fichiers d’entrée

Formats de fichier (extensions de fichier)|Pris en charge
---|---|---|---
FLV (avec les codecs H.264 et AAC) (.flv) |Oui 
MXF (.mxf) |Oui 
GXF (.gxf) |Oui 
MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Oui 
Windows Media Video (WMV)/ASF (.wmv, .asf) |Oui 
AVI (8 bits/10 bits non compressé) (.avi)|Oui 
MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv)|Oui 
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Oui 
Matroska/WebM (.mkv) |Oui 
WAVE/WAV (.wav) |Oui 
QuickTime (.mov) |Oui
 
###Formats audio dans des conteneurs d’entrée 

Media Encoder Standard prend en charge la transmission des formats audio suivants dans des conteneurs d’entrée :

- Fichiers MXF, GXF et QuickTime contenant des pistes audio avec des échantillons Interleaved Stereo ou 5.1

ou

- Fichiers MXF, GXF et QuickTime où l’audio est transmis sous forme de pistes PCM distinctes, mais où le mappage de canaux (vers la stéréo ou 5.1) peut être déduit des métadonnées du fichier

Notez que le mappage de canaux explicite/fourni par l’utilisateur sera pris en charge dans un avenir proche.


##Codecs vidéo d’entrée

Codecs vidéo d’entrée|Pris en charge
---|---|---|---
AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra |8 bits 4:2:0 et 4:2:2 
Avid DNxHD (dans MXF) |Oui 
DVCPro/DVCProHD (dans MXF) |Oui 
JPEG 2000 |Oui 
MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|Jusqu’à un profil de 422 
MPEG-1 |Oui 
VC-1/WMV9 |Oui 
Canopus HQ/HQX |Non 
MPEG-4 partie 2 |Oui 
[Theora](https://en.wikipedia.org/wiki/Theora) |Oui 
YUV420 non compressé ou mezzanine |Oui
Apple ProRes 422 |Oui
Apple ProRes 422 LT |Oui
Apple ProRes 422 HQ |Oui
Apple ProRes Proxy|Oui
Apple ProRes 4444 |Oui
Apple ProRes 4444 XQ |Oui



##Codecs audio d’entrée

Codecs audio d’entrée|Pris en charge
---|---|---|---
AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1)|Oui 
MPEG Layer 2|Oui 
MP3 (MPEG-1 Audio Layer 3)|Oui 
Windows Media Audio|Oui 
WAV/PCM|Oui 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Oui 
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Oui 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Oui 
AMR (adaptive multi-rate)|Oui
AES (SMPTE 331M et 302M, AES3-2003) |Non 
Dolby® E |Non 
Dolby® Digital (AC3) |Non 
Dolby® Digital Plus (E-AC3) |Non 


##Formats de sortie et codecs

Le tableau suivant répertorie les codecs et les formats de fichiers pris en charge pour l'exportation.


Format de fichier|Codec vidéo|Codec audio
---|---|---
MP4 <br/><br/>(y compris les conteneurs MP4 à vitesses de transmission multiples) |H.264 (profils High, Main et Baseline)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (profils High, Main et Baseline)|AAC-LC, HE-AAC v1, HE-AAC v2 


##Parcours d’apprentissage de Media Services

Vous pouvez afficher les parcours d’apprentissage d’AMS ici :

- [Workflow de vidéo en flux continu AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Workflow de streaming à la demande AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Voir aussi

[Encodage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md)

[Encodage avec Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

<!---HONumber=Sept15_HO3-->