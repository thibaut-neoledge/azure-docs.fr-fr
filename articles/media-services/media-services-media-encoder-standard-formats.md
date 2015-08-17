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
	ms.date="07/08/2015" 
	ms.author="juliako"/>

#Codecs et formats standard de l’encodeur multimédia


Ce document contient la liste des formats de fichier d’importation et d’exportation les plus courants que vous pouvez utiliser avec l’encodeur multimédia.


[Formats d’importation de l’encodeur multimédia ](#import_formats)

[Formats d’exportation Media Encoder](#export_formats)


##<a id="import_formats"></a>Formats d’importation de l’encodeur multimédia 

La section suivante répertorie les codecs et les formats de fichiers pris en charge pour l'importation.


##Codecs vidéo :

- MPEG-2
- H.264
- YUV420 non compressé ou mezzanine
- DNxHD
- VC-1/WMV9
- MPEG-4 Part 2
- JPEG 2000
- Theora

###Codecs audio

- PCM
- AAC (AAC-LC, AAC-HE et AAC-HEv2)
- WMA9/Pro
- MP3 (MPEG-1 Audio Layer 3)
- FLAC
- Opus
- Vorbis
 
###Formats

Format de fichier|Extensions de fichiers
---|---
FLV (avec les codecs H.264 et AAC) |.flv
MP4/ISMV|*.ismv
MPEG2-PS, MPEG2-TS, 3GP|.ts, .ps, .3gp
MXF|.mxf
WMV/ASF|.mwv, .asf
DVR-MS|.dvr-ms 
AVI|.avi
Matroska|.mkv
GXF|.gxf
WAVE/WAV |.wav


##<a id="export_formats"></a>Formats d’exportation de l’encodeur multimédia

Le tableau suivant répertorie les codecs et les formats de fichiers pris en charge pour l'exportation.


Format de fichier|Codec vidéo|Codec audio
---|---|---
MP4 (* .mp4)<br/><br/>(y compris les conteneurs MP4 à vitesses de transmission multiples) |H.264 (profils High, Main et Baseline)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (profils High, Main et Baseline)|AAC-LC, HE-AAC v1, HE-AAC v2 

##Voir aussi

[Encodage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md)

<!---HONumber=August15_HO6-->