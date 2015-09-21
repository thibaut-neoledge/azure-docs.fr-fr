<properties 
	pageTitle="Codecs et formats de Media Encoder Premium Workflow" 
	description="Cette rubrique offre une vue d'ensemble des codecs et formats de Media Encoder Premium Workflow" 
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
	ms.date="09/07/2015" 
	ms.author="juliako"/>

#Codecs et formats de Media Encoder Premium Workflow


**Remarque** : le processeur multimédia Media Encoder Premium Workflow présenté dans cette rubrique n’est pas disponible en Chine.

Ce document contient la liste des codecs et formats de fichiers d’entrée et de sortie pris en charge par la version préliminaire publique de l’encodeur **Media Encoder Premium Workflow**.

[Codecs et formats d’entrée de Media Encoder Premium Workflow](#input_formats)

[Codecs et formats de sortie de Media Encoder Premium Workflow](#output_formats)

**Media Encoder Premium Workflow** prend en charge le sous-titrage décrit dans [cette](#closed_captioning) section


##<a id="input_formats"></a>Codecs et formats d’entrée de Media Encoder Premium Workflow

La section suivante répertorie les codecs et les formats de fichiers que ce processeur multimédia prend en charge en entrée.

###Formats de conteneurs/fichiers d’entrée

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- MPEG-2 Transport Streams
- MPEG-2 Program Streams
- MPEG-4/MP4
- Windows Media/ASF
- AVI (8 bits/10 bits non compressé)

###Codecs vidéo d’entrée

- AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra
- Avid DNxHD (dans MXF)
- DVCPro/DVCProHD (dans MXF)
- JPEG2000
- MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)
- MPEG-1
- Windows Media Video/VC-1

###Codecs audio d’entrée

- AES (SMPTE 331M et 302M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio
- WAV/PCM
 
##<a id="output_format"></a>Codecs et formats de sortie de Media Encoder Premium Workflow

La section suivante répertorie les codecs et les formats de fichiers pris en charge en sortie avec ce processeur multimédia.

###Formats de conteneurs/fichiers de sortie

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM et AS02)
- DPP (y compris AS11)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (8 bits/10 bits non compressé)
- Format de fichier de diffusion en continu lisse (PIFF 1.3)
- MPEG-TS 


###Codecs vidéo de sortie

- AVC (H.264 ; 8 bits ; jusqu'au profil High, niveau 5.2 ; Ultra HD 4K ; AVC Intra)
- Avid DNxHD (dans MXF)
- DVCPro/DVCProHD (dans MXF)
- MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)
- MPEG-1
- Windows Media Video/VC-1
- Création de miniatures JPEG

###Codecs audio de sortie

- AES (SMPTE 331M et 302M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) jusqu'à 7.1
- AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio

##<a id="closed_captioning"></a>Prise en charge du sous-titrage

En entrée, **Media Encoder Premium Workflow** prend en charge :

1. Fichiers sous contrôle de code source (SCC)
1. Fichiers SMPTE-TT
1. CEA-608/CEA-708 : transporté comme données utilisateur (messages SEI de flux élémentaires H.264, ATSC/53, SCTE20) ou comme données connexes dans des fichiers MXF/GXF
1. Fichiers de sous-titres STL

En sortie, les options suivantes sont disponibles :

1. conversion de CEA-608 vers CEA-708
1. Transition CEA-608/CEA-708 (incorporé dans des messages SEI de flux élémentaires H.264 ou transporté comme données connexes dans des fichiers MXF)
1. SCC
1. SMPTE Timed Text (de la source CEA-608 par SMPTE RP2052 ; création de fichiers DFXP incluse)
1. Fichier de sous-titre SRT
1. Flux de sous-titres DVB

Remarque : tous les formats de sortie mentionnés ci-dessus ne sont pas pris en charge dans le cadre d'une remise diffusée en continu dans Azure Media Services.

##Problèmes connus

Si votre vidéo d’entrée ne contient pas de sous-titres, l’élément multimédia de sortie actif comportera toujours un fichier TTML vide.


##Parcours d’apprentissage de Media Services

Vous pouvez afficher les parcours d’apprentissage d’AMS ici :

- [Workflow en flux continu AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Workflow de streaming à la demande AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Sept15_HO2-->