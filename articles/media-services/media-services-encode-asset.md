<properties 
	pageTitle="Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure" 
	description="Cette rubrique donne une vue d’ensemble des encodeurs multimédia à la demande Azure et les compare." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015"  
	ms.author="juliako"/>

#Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure

##Vue d’ensemble de l’encodage

Azure Media Services fournit plusieurs options pour l’encodage de fichiers multimédias dans le cloud.

Quand vous commencez à utiliser Media Services, il est important de bien comprendre la différence entre les codecs et les formats de fichiers. Les codecs sont les logiciels qui implémentent les algorithmes de compression/décompression, tandis que les formats de fichiers sont des conteneurs qui contiennent la vidéo compressée.

Media Services fournit l’empaquetage dynamique qui permet de distribuer un contenu en diffusion continue en MP4 ou Smooth Streaming dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sans avoir à recréer de nouveaux packages dans ces formats.

Pour tirer parti de l’[empaquetage dynamique](media-services-dynamic-packaging-overview.md), vous devez effectuer les opérations suivantes :

- encoder votre fichier mezzanine (source) dans un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif (les étapes de codage sont décrites plus loin dans ce didacticiel).
- obtenir au moins une unité de diffusion à la demande pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu. Pour plus d’informations, consultez la page [Extension des unités réservées de diffusion en continu à la demande](media-services-manage-origins.md#scale_streaming_endpoints/).

Media Services prend en charge les éléments suivants sur les encodeurs à la demande décrits dans cet article :

- **Media Encoder Standard**
- **Encodeur multimédia Azure** 
- **Media Encoder Premium Workflow**

Cet article donne un bref aperçu des encodeurs multimédia à la demande et fournit des liens vers des articles fournissant des informations plus détaillées. Cette rubrique compare également les encodeurs.

Remarque : par défaut, chaque compte Media Services peut avoir une tâche d’encodage active à la fois. Vous pouvez réserver des unités d’encodage qui vous permettent d’exécuter plusieurs tâches d’encodage simultanément, une pour chaque unité réservée d’encodage que vous achetez. Pour plus d’informations, consultez [Mise à l’échelle des unités d’encodage](media-services-portal-encoding-units.md).

##Media Encoder Standard

###Vue d’ensemble

Il est recommandé d’utiliser l’encodeur Media Encoder Standard. Toutefois, il ne figure pas actuellement sur le portail Azure.

Par rapport à Azure Media Encoder, cet encodeur prend en charge plusieurs formats d’entrée et de sortie et plusieurs codecs. Autres avantages :

- Plus grande tolérance par rapport au mode de création du fichier d’entrée
- Meilleure qualité de codec H.264 qu’Azure Media Encoder
- Repose sur un pipeline plus flexible et plus récent
- Robustesse/résilience accrues

###Utilisation

[Encodage avec Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

###Formats

[Formats et codecs](media-services-media-encoder-standard-formats.md)

###Présélections

Media Encoder Standard est configuré à l’aide d’une des présélections d’encodeur décrites [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Métadonnées d’entrée et de sortie

Les métadonnées d’entrée des encodeurs sont décrites [ici](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Les métadonnées de sortie des encodeurs sont décrites [ici](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###Miniature

Pour plus d’informations sur la génération de miniatures, consultez [Génération des miniatures à l’aide de Media Encoder Standard](media-services-dotnet-generate-thumbnail-with-mes.md).

###Superpositions audio et/ou vidéo

Fonctionnalité non prise en charge actuellement.

###Voir aussi

[Blog sur Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Encodeur multimédia Azure

###Vue d’ensemble

Azure Media Encoder est l’un des encodeurs pris en charge par Media Services. Depuis juillet 2015, il est recommandé d’utiliser [Media Encoder Standard](media-services-encode-asset.md#media_encoder_standard).

###Utilisation

[Encodage avec Azure Media Encoder](media-services-dotnet-encode-asset.md)

###Formats

[Formats et codecs](media-services-azure-media-encoder-formats.md)

###Présélections

Azure Media Encoder est configuré à l’aide d’une des présélections d’encodeur décrites [ici](https://msdn.microsoft.com/library/azure/dn619392.aspx). Vous pouvez également obtenir les fichiers des présélections d’Azure Media Encoder [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

###Métadonnées d’entrée et de sortie

Les métadonnées d’entrée des encodeurs sont décrites [ici](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Les métadonnées de sortie des encodeurs sont décrites [ici](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###Miniature

[Création d’une miniature](https://msdn.microsoft.com/library/hh973624.aspx)

###Superpositions audio et/ou vidéo

[Création de superpositions](media-services-azure-media-customize-ame-presets.md#creating-overlays).

###Conventions d’affectation de noms

[Modification des noms de fichier de sortie](media-services-azure-media-customize-ame-presets.md#controlling-azure-media-encoder-output-file-names)

###Voir aussi

[Encodage de vos médias avec Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md)

##Media Encoder Premium Workflow

###Vue d'ensemble

[Présentation de l’encodage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

###Utilisation

Media Encoder Premium Workflow se configure à l’aide de flux de travail complexes. Les fichiers de flux de travail peuvent être créés et mis à jour à l’aide de l’outil [Concepteur de flux de travail](media-services-workflow-designer.md).

[Utilisation de l’encodage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

##<a id="compare_encoders"></a>Comparaison des encodeurs

###<a id="billing"></a>Compteur de facturation utilisé par chaque encodeur

Nom du processeur multimédia|Tarification applicable|Remarques
---|---|---
**Media Encoder Standard** |ENCODEUR|Les tâches d’encodage seront facturées en fonction de la taille de la ressource de sortie, en gigaoctets, à la vitesse spécifiée [ici][1], sous la colonne ENCODER.
**Encodeur multimédia Azure** |ENCODEUR|Les tâches d’encodage seront facturées en fonction de la taille de la ressource de sortie, en gigaoctets, à la vitesse spécifiée [ici][1], sous la colonne ENCODER.
**Media Encoder Premium Workflow** |ENCODEUR PREMIUM|Les tâches d’encodage seront facturées en fonction de la taille de la ressource de sortie, en gigaoctets, à la vitesse spécifiée [ici][1], sous la colonne PREMIUM ENCODER.


Cette section compare les fonctionnalités d’encodage de **Media Encoder Standard**, d’**Azure Media Encoder** et de **Media Encoder Premium Worfklow**.


###Formats de conteneurs/fichiers d’entrée

Formats de conteneurs/fichiers d’entrée|Media Encoder Standard|Encodeur multimédia Azure|Media Encoder Premium Workflow
---|---|---|---
Adobe® Flash® F4V |Oui|Non |Oui
MXF/SMPTE 377M |Oui|Limité|Oui
GXF |Oui|Non |Oui
MPEG-2 Transport Streams |Oui|Oui |Oui
MPEG-2 Program Streams |Oui|Oui |Oui
MPEG-4/MP4 |Oui|Oui |Oui
Windows Media/ASF |Oui|Oui |Oui
AVI (8 bits/10 bits non compressé)|Oui|Oui |Oui
3GPP/3GPP2 |Oui|Oui |Non
Format de fichier de diffusion en continu lisse (PIFF 1.3)|Oui|Oui|Non
[Enregistrement vidéo numérique Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Oui|Non|Non
Matroska/WebM |Oui|Non|Non

###Codecs vidéo d’entrée

Codecs vidéo d’entrée|Media Encoder Standard|Encodeur multimédia Azure|Media Encoder Premium Workflow
---|---|---|---
AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra |8 bits 4:2:0 et 4:2:2|Uniquement 8 bits 4:2:0|Oui
Avid DNxHD (dans MXF) |Oui|Non|Oui
DVCPro/DVCProHD (dans MXF) |Oui|Non|Oui
JPEG2000 |Oui|Non|Oui
MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|Jusqu’à un profil de 422|Jusqu’à un profil de 422|Oui
MPEG-1 |Oui|Oui|Oui
Windows Media Video/VC-1 |Oui|Oui|Oui
Canopus HQ/HQX |Non|Oui|Non
MPEG-4 partie 2 |Oui|Non|Non
[Theora](https://en.wikipedia.org/wiki/Theora) |Oui|Non|Non

###Codecs audio d’entrée

Codecs audio d’entrée|Media Encoder Standard|Encodeur multimédia Azure|Media Encoder Premium Workflow
---|---|---|---
AES (SMPTE 331M et 302M, AES3-2003) |Non|Non|Oui
Dolby® E |Non|Non|Oui
Dolby® Digital (AC3) |Non|Oui|Oui
Dolby® Digital Plus (E-AC3) |Non|Non|Oui
AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1)|Oui|Oui|Oui
MPEG Layer 2|Oui|Oui|Oui
MP3 (MPEG-1 Audio Layer 3)|Oui|Oui|Oui
Windows Media Audio|Oui|Oui|Oui
WAV/PCM|Oui|Oui|Oui
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Oui|Non|Non
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Oui|Non|Non
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Oui|Non|Non


###Formats de conteneurs/fichiers de sortie

Formats de conteneurs/fichiers de sortie|Media Encoder Standard|Encodeur multimédia Azure|Media Encoder Premium Workflow
---|---|---|---
Adobe® Flash® F4V|Non|Non|Oui
MXF (OP1a, XDCAM et AS02)|Non|Non|Oui
DPP (y compris AS11)|Non|Non|Oui
GXF|Non|Non|Oui
MPEG-4/MP4|Oui|Oui|Oui
MPEG-TS|Oui|Non|Oui
Windows Media/ASF|Non|Oui|Oui
AVI (8 bits/10 bits non compressé)|Non|Non|Oui
Format de fichier de diffusion en continu lisse (PIFF 1.3)|Non|Oui|Oui

###Codecs vidéo de sortie

Codecs vidéo de sortie|Media Encoder Standard|Encodeur multimédia Azure|Media Encoder Premium Workflow
---|---|---|---
AVC (H.264 ; 8 bits ; jusqu'au profil High, niveau 5.2 ; Ultra HD 4K ; AVC Intra)|Uniquement 8 bits 4:2:0|Uniquement 8 bits 4:2:0 jusqu’à 1080p|Oui
Avid DNxHD (dans MXF)|Non|Non|Oui
DVCPro/DVCProHD (dans MXF)|Non|Non|Oui
MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|Non|Non|Oui
MPEG-1|Non|Non|Oui
Windows Media Video/VC-1|Non|Oui|Oui
Création de miniatures JPEG|Non|Oui|Oui

###Codecs audio de sortie

Codecs audio de sortie|Media Encoder Standard|Encodeur multimédia Azure|Media Encoder Premium Workflow
---|---|---|---
AES (SMPTE 331M et 302M, AES3-2003)|Non|Non|Oui
Dolby® Digital (AC3)|Non|Oui|Oui
Dolby® Digital Plus (E-AC3) jusqu'à 7.1|Non|Jusqu’à 5.1|Oui
AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1)|Oui|Oui|Oui
MPEG Layer 2|Non|Non|Oui
MP3 (MPEG-1 Audio Layer 3)|Non|Non|Oui
Windows Media Audio|Non|Oui|Oui


##Parcours d’apprentissage de Media Services

Vous pouvez afficher les parcours d’apprentissage d’AMS ici :

- [Workflow de vidéo en flux continu AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Workflow de streaming à la demande AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Articles connexes

- [Quotas et limitations](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=Oct15_HO4-->