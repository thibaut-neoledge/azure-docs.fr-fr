<properties 
	pageTitle="Encodage de contenu à la demande avec Azure Media Services" 
	description="Cette rubrique offre une vue d’ensemble de l’encodage de contenu à la demande avec Media Services." 
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
	ms.date="07/23/2015" 
	ms.author="juliako"/>

#Encodage de contenu à la demande avec Azure Media Services

Cette rubrique fait partie de la série [Flux de travail de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md).

##Vue d'ensemble

Media Services prend en charge les encodeurs suivants :

- [Media Encoder Standard](#media_encoder_standard)
- [Encodeur multimédia Azure](#azure_media_encoder)
- [Media Encoder Premium Workflow](#media_encoder_premium_workflow)

La [section suivante](#compare_encoders) compare les fonctionnalités d'encodage des encodeurs pris en charge.

Par défaut, chaque compte Media Services peut avoir une tâche d’encodage active à la fois. Vous pouvez réserver des unités d’encodage qui vous permettent d’exécuter plusieurs tâches d’encodage simultanément, une pour chaque unité réservée d’encodage que vous achetez. Pour plus d’informations sur la mise à l’échelle des unités d’encodage, consultez les rubriques **Portail** et **.NET** suivantes.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="media_encoder_standard"></a>Media Encoder Standard

[Formats pris en charge par Media Encoder Standard](media-services-media-encoder-standard-formats.md) : décrit les formats de fichiers et de flux pris en charge par **Media Encoder Standard**.

**Media Encoder Standard** se configure à l'aide d'une des présélections d'encodeur décrites [ici](http://go.microsoft.com/fwlink/?LinkId=618336) ou de paramètres prédéfinis personnalisés basés sur [ces](http://go.microsoft.com/fwlink/?LinkId=618336) présélections.

Pour plus d'informations, consultez [ce blog](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

##<a id="azure_media_encoder"></a>Encodeur multimédia Azure

[Formats pris en charge par l'encodeur multimédia Azure](media-services-azure-media-encoder-formats.md) : décrit les formats de fichiers et de flux pris en charge par l'**Encodeur multimédia Azure**.

L'**Encodeur multimédia Azure** se configure à l'aide d'une des chaînes de présélection d'encodeur décrites [ici](https://msdn.microsoft.com/library/azure/dn619392.aspx). Vous pouvez également obtenir les fichiers de présélection de l'Encodeur multimédia Azure [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

Procédez à l’encodage à l’aide de l’**Encodeur multimédia Azure** en utilisant le **portail de gestion Azure**, **.NET** ou l’**API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Autres rubriques connexes

[Empaquetage dynamique](https://msdn.microsoft.com/library/azure/jj889436.aspx) : explique comment encoder des fichiers MP4 à débit adaptatif et fournir dynamiquement du contenu aux formats Smooth Streaming, Apple HLS ou MPEG-DASH.

[Contrôle des noms de fichier de sortie de l'encodeur Media Services](https://msdn.microsoft.com/library/azure/dn303341.aspx) : explique les conventions d'affectation de noms de fichiers utilisés par l'Encodeur multimédia Azure et comment modifier les noms de fichier de sortie.

[Encodage de vos médias avec Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md) : explique comment encoder les pistes audio à l'aide de l'encodage Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a>Media Encoder Premium Workflow 

**Remarque** : le processeur multimédia Media Encoder Premium Workflow présenté dans cette rubrique n’est pas disponible en Chine.

[Formats pris en charge par Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md) : présente les formats de fichiers et les codecs pris en charge par **Media Encoder Premium Workflow**.

**Media Encoder Premium Workflow** se configure à l'aide de flux de travail complexes. Les fichiers de flux de travail peuvent être créés à l'aide de l'outil [Concepteur de flux de travail](media-services-workflow-designer.md).

Vous pouvez vous procurer les fichiers de flux de travail par défaut [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Le dossier contient aussi la description de ces fichiers.

Encodez avec **Media Encoder Premium Workflow** à l'aide de **.NET**. Pour plus d'informations, consultez [Encodage avancé avec Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Comparaison des encodeurs

###<a id="billing"></a>Compteur de facturation utilisé par chaque encodeur

Nom du processeur multimédia|Tarification applicable|Remarques
---|---|---
**Encodeur multimédia Windows Azure** |ENCODEUR HÉRITÉ|Les tâches d'encodage seront facturées en fonction de la somme des tailles des actifs d'entrée et de l'actif de sortie, en gigaoctets, à la vitesse spécifiée [ici][1], sous la colonne ENCODEUR HÉRITÉ.
**Encodeur multimédia Azure** |ENCODEUR|Les tâches d'encodage seront facturées en fonction de la taille de l'actif de sortie, en gigaoctets, à la vitesse spécifiée [ici][1], sous la colonne ENCODEUR.
**Media Encoder Standard** |ENCODEUR|Les tâches d'encodage seront facturées en fonction de la taille de l'actif de sortie, en gigaoctets, à la vitesse spécifiée [ici][1], sous la colonne ENCODEUR.
**Media Encoder Premium Workflow** |ENCODEUR PREMIUM|Les tâches d'encodage seront facturées en fonction de la taille de l'actif de sortie, en gigaoctets, à la vitesse spécifiée [ici][1], sous la colonne ENCODEUR PREMIUM.



Cette section compare les fonctionnalités d'encodage de l'**Encodeur multimédia Azure**, **Media Encoder Premium Workflow** et **Media Encoder Standard**.


###Formats d’entrée

Formats de conteneurs/fichiers d’entrée

Formats de conteneurs/fichiers d’entrée|Media Encoder Premium Workflow|Encodeur multimédia Azure|Media Encoder Standard
---|---|---|---
Adobe® Flash® F4V|Oui|Non|Oui
MXF/SMPTE 377M|Oui|Limité|Oui
GXF|Oui|Non|Oui
MPEG-2 Transport Streams|Oui|Oui|Oui
MPEG-2 Program Streams|Oui|Oui|Oui
MPEG-4/MP4|Oui|Oui|Oui
Windows Media/ASF|Oui|Oui|Oui
AVI (8 bits/10 bits non compressé)|Oui|Oui|Oui
3GPP/3GPP2|Non|Oui|Oui
Format de fichier de diffusion en continu lisse (PIFF 1.3)|Non|Oui|Oui
[Enregistrement vidéo numérique Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Non|Non|Oui
Matroska/WebM|Non|Non|Oui

Codecs vidéo d’entrée

Codecs vidéo d’entrée|Media Encoder Premium Workflow|Encodeur multimédia Azure|Media Encoder Standard
---|---|---|---
AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra|Oui|Uniquement 8 bits 4:2:0|8 bits 4:2:0 et 4:2:2
Avid DNxHD (dans MXF)|Oui|Non|Oui
DVCPro/DVCProHD (dans MXF)|Oui|Non|Oui
JPEG2000|Oui|Non|Oui
MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|Oui|Jusqu’à un profil de 422|Jusqu’à un profil de 422
MPEG-1|Oui|Oui|Oui
Windows Media Video/VC-1|Oui|Oui|Oui
Canopus HQ/HQX|Non|Oui|Non
MPEG-4 partie 2|Non|Non|Oui
[Theora](https://en.wikipedia.org/wiki/Theora)|Non|Non|Oui

Codecs audio d’entrée

Codecs audio d’entrée|Media Encoder Premium Workflow|Encodeur multimédia Azure|Media Encoder Standard
---|---|---|---
AES (SMPTE 331M et 302M, AES3-2003)|Oui|Non|Non
Dolby® E|Oui|Non|Non
Dolby® Digital (AC3)|Oui|Oui|Non
Dolby® Digital Plus (E-AC3)|Oui|Non|Non
AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1)|Oui|Oui|Oui
MPEG Layer 2|Oui|Oui|Oui
MP3 (MPEG-1 Audio Layer 3)|Oui|Oui|Oui
Windows Media Audio|Oui|Oui|Oui
WAV/PCM|Oui|Oui|Oui
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Non|Non|Oui
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Non|Non|Oui
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Non|Non|Oui

###Formats de sortie

Formats de conteneurs/fichiers de sortie

Formats de conteneurs/fichiers de sortie|Media Encoder Premium Workflow|Encodeur multimédia Azure|Media Encoder Standard
---|---|---|---
Adobe® Flash® F4V|Oui|Non|Non
MXF (OP1a, XDCAM et AS02)|Oui|Non|Non
DPP (y compris AS11)|Oui|Non|Non
GXF|Oui|Non|Non
MPEG-4/MP4|Oui|Oui|Oui
MPEG-TS|Oui|Non|Oui
Windows Media/ASF|Oui|Oui|Non
AVI (8 bits/10 bits non compressé)|Oui|Non|Non
Format de fichier de diffusion en continu lisse (PIFF 1.3)|Oui|Oui|Non

Codecs vidéo de sortie

Codecs vidéo de sortie|Media Encoder Premium Workflow|Encodeur multimédia Azure|Media Encoder Standard
---|---|---|---
AVC (H.264 ; 8 bits ; jusqu'au profil High, niveau 5.2 ; Ultra HD 4K ; AVC Intra)|Oui|Uniquement 8 bits 4:2:0 jusqu’à 1080p|Uniquement 8 bits 4:2:0
Avid DNxHD (dans MXF)|Oui|Non|Non
DVCPro/DVCProHD (dans MXF)|Oui|Non|Non
MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|Oui|Non|Non
MPEG-1|Oui|Non|Non
Windows Media Video/VC-1|Oui|Oui|Non
Création de miniatures JPEG|Oui|Oui|Non

Codecs audio de sortie

Codecs audio de sortie|Media Encoder Premium Workflow|Encodeur multimédia Azure|Media Encoder Standard
---|---|---|---
AES (SMPTE 331M et 302M, AES3-2003)|Oui|Non|Non
Dolby® Digital (AC3)|Oui|Oui|Non
Dolby® Digital Plus (E-AC3) jusqu'à 7.1|Oui|Jusqu’à 5.1|Non
AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1)|Oui|Oui|Oui
MPEG Layer 2|Oui|Non|Non
MP3 (MPEG-1 Audio Layer 3)|Oui|Non|Non
Windows Media Audio|Oui|Oui|Non

##Articles connexes

- [Présentation de l’encodage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Utilisation de l’encodage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Quotas et limitations](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=August15_HO6-->