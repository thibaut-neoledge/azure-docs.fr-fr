<properties 
	pageTitle="Encodage de contenu à la demande avec Azure Media Services" 
	description="Cette rubrique offre une vue d'ensemble de l'encodage de contenu à la demande avec Media Services." 
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
	ms.date="03/05/2015" 
	ms.author="juliako"/>

# Encodage de contenu à la demande avec Azure Media Services

Cette rubrique fait partie de la série [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md).

## Vue d'ensemble

Media Services prend en charge les encodeurs suivants :

- [Encodeur multimédia Azure](#azure_media_encoder)
- [Media Encoder Premium Workflow](#media_encoder_premium_workflow) (version préliminaire publique)

La [section suivante](#compare_encoders) compare les fonctionnalités d'encodage de ces deux encodeurs.

Par défaut, chaque compte Media Services peut avoir une tâche d'encodage active à la fois. Vous pouvez réserver des unités d'encodage qui vous permettent d'exécuter plusieurs tâches d'encodage simultanément, une pour chaque unité réservée d'encodage que vous achetez. Pour plus d'informations sur la mise à l'échelle des unités d'encodage, consultez les rubriques **Portail** et **.NET** suivantes.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../includes/media-services-selector-scale-encoding-units.md)]

## <a id="azure_media_encoder"></a>Encodeur multimédia Azure

[Formats pris en charge par l'encodeur Media Services](media-services-azure-media-encoder-formats.md) : décrit les formats de fichiers et de flux pris en charge par l'**Encodeur multimédia Azure**.

L'**Encodeur multimédia Azure** se configure à l'aide d'une des chaînes de présélection d'encodeur décrites [ici](https://msdn.microsoft.com/library/azure/dn619392.aspx). Vous pouvez également obtenir les fichiers de présélection de l'Encodeur multimédia Azure [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

Encodez à l'aide de l'**Encodeur multimédia Azure** en utilisant le **portail de gestion Azure**, **.NET** ou l'**API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

#### Autres rubriques connexes

[Empaquetage dynamique](https://msdn.microsoft.com/library/azure/jj889436.aspx) : explique comment encoder des fichiers MP4 à débit adaptatif et fournir dynamiquement du contenu aux formats Smooth Streaming, Apple HLS ou MPEG-DASH.

[Contrôle des noms de fichier de sortie de l'encodeur Media Services](https://msdn.microsoft.com/library/azure/dn303341.aspx) : explique les conventions d'affectation de noms de fichiers utilisées par l'Encodeur multimédia Azure et comment modifier les noms de fichier de sortie.

[Encodage de vos médias avec Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md) : explique comment encoder les pistes audio à l'aide de l'encodage Dolby Digital Plus.


## <a id="media_encoder_premium_wokrflow"></a>Media Encoder Premium Workflow (version préliminaire publique)

**Remarque** : le processeur multimédia Media Encoder Premium Workflow présenté dans cette rubrique n'est pas disponible en Chine.

[Formats pris en charge par Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md) : passe en revue les formats de fichiers et les codecs pris en charge par **Media Encoder Premium Workflow**.

**Media Encoder Premium Workflow** se configure à l'aide de flux de travail complexes. Les fichiers de flux de travail peuvent être créés à l'aide de l'outil [Concepteur de flux de travail](media-services-workflow-designer.md). 

Vous pouvez vous procurer les fichiers de flux de travail par défaut [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Le dossier contient aussi la description de ces fichiers.

Encodez avec **Media Encoder Premium Workflow** à l'aide de **.NET**. Pour plus d'informations, consultez la page [Encodage avancé avec Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).
 

## <a id="compare_encoders"></a>Comparaison des encodeurs

Cette section compare les fonctionnalités d'encodage de l'**Encodeur multimédia Azure** et de **Media Encoder Premium Workflow**.

### Formats d'entrée

Formats de conteneurs/fichiers d'entrée

<table border="1">
<tr><th>Input Container/File Formats</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe(r) Flash(r) F4V</td><td>Yes</td><td>No</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Yes</td><td>Limited</td></tr>
<tr><td>GXF</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 Transport Streams</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG-2 Program Streams</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG-4/MP4</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media/ASF</td><td>Yes</td><td>Yes</td></tr>
<tr><td>AVI (Uncompressed 8bit/10bit)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>3GPP/3GPP2</td><td>No</td><td>Yes</td></tr>
<tr><td>Smooth Streaming File Format (PIFF 1.3)</td><td>No</td><td>Yes</td></tr>
</table>

Codecs vidéo d'entrée

<table border="1">
<tr><th>Input Video Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC 8-bit/10-bit, up to 4:2:2, including AVCIntra</td><td>Yes</td><td>Only 8bit 4:2:0</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>JPEG2000</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 (up to 422 Profile and High Level; including variants such as XDCAM, XDCAM HD, XDCAM IMX, CableLabs(r) and D10)</td><td>Yes</td><td>Up to 422 Profile</td></tr>
<tr><td>MPEG-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Canopus HQ/HQX</td><td>No</td><td>Yes</td></tr>
</table>

Codecs audio d'entrée

<table border="1">
<tr><th>Input Audio Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M and 302M, AES3-2003)</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) E</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) Digital (AC3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Dolby(r) Digital Plus (E-AC3)</td><td>Yes</td><td>No</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE, and AAC-HEv2; up to 5.1)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG Layer 2</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media Audio</td><td>Yes</td><td>Yes</td></tr>
<tr><td>WAV/PCM</td><td>Yes</td><td>Yes</td></tr>
</table>

### Formats de sortie

Formats de conteneurs/fichiers de sortie

<table border="1">
<tr><th>Output Container/File Formats</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe(r) Flash(r) F4V</td><td>Yes</td><td>No</td></tr>
<tr><td>MXF (OP1a, XDCAM and AS02)</td><td>Yes</td><td>No</td></tr>
<tr><td>DPP (including AS11)</td><td>Yes</td><td>No</td></tr>
<tr><td>GXF</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-4/MP4</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media/ASF</td><td>Yes</td><td>Yes</td></tr>
<tr><td>AVI (Uncompressed 8bit/10bit)</td><td>Yes</td><td>No</td></tr>
<tr><td>Smooth Streaming File Format (PIFF 1.3)</td><td>Yes</td><td>Yes</td></tr>
</table>

Codecs vidéo de sortie

<table border="1">
<tr><th>Output Video Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC (H.264; 8-bit; up to High Profile, Level 5.2; 4K Ultra HD; AVC Intra)</td><td>Yes</td><td>Only 8bit 4:2:0 up to 1080p</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 (up to 422 Profile and High Level; including variants such as XDCAM, XDCAM HD, XDCAM IMX, CableLabs(r) and D10)</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-1</td><td>Yes</td><td>No</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>JPEG thumbnail creation</td><td>Yes</td><td>Yes</td></tr>
</table>

Codecs audio de sortie

<table border="1">
<tr><th>Output Audio Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M and 302M, AES3-2003)</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) Digital (AC3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Dolby(r) Digital Plus (E-AC3) up to 7.1</td><td>Yes</td><td>Up to 5.1</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE, and AAC-HEv2; up to 5.1)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG Layer 2</td><td>Yes</td><td>No</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Yes</td><td>No</td></tr>
<tr><td>Windows Media Audio</td><td>Yes</td><td>Yes</td></tr>
</table>
## Articles connexes

- [Présentation de l'encodage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Utilisation de l'encodage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Quotas et limitations](media-services-quotas-and-limitations.md)


<!--HONumber=52-->