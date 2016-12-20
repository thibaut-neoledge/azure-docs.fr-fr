---
title: "Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure | Microsoft Docs"
description: "Cette rubrique donne une vue d’ensemble des encodeurs multimédia à la demande Azure et les compare."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: dc153e837a20f475c0e0b03ac698f9f84a045364


---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure
## <a name="encoding-overview"></a>Vue d’ensemble de l’encodage
Azure Media Services fournit plusieurs options pour l’encodage de fichiers multimédias dans le cloud.

Quand vous commencez à utiliser Media Services, il est important de bien comprendre la différence entre les codecs et les formats de fichiers.
Les codecs sont les logiciels qui implémentent les algorithmes de compression/décompression, tandis que les formats de fichiers sont des conteneurs qui contiennent la vidéo compressée.

Media Services fournit l’empaquetage dynamique qui permet de distribuer un contenu en diffusion continue en MP4 ou Smooth Streaming dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sans avoir à recréer de nouveaux packages dans ces formats.

Pour tirer parti de l’ [empaquetage dynamique](media-services-dynamic-packaging-overview.md), vous devez effectuer les opérations suivantes :

* encoder votre fichier mezzanine (source) dans un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif (les étapes de codage sont décrites plus loin dans ce didacticiel).
* obtenir au moins une unité de diffusion à la demande pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu. Pour plus d’informations, consultez la page [Extension des unités réservées de diffusion en continu à la demande](media-services-portal-manage-streaming-endpoints.md).

Media Services prend en charge les éléments suivants sur les encodeurs à la demande décrits dans cet article :

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium Workflow](media-services-encode-asset.md#media-encoder-premium-workflow)

Cet article donne un bref aperçu des encodeurs multimédia à la demande et fournit des liens vers des articles fournissant des informations plus détaillées. Cette rubrique compare également les encodeurs.

Remarque : par défaut, chaque compte Media Services peut avoir une tâche d’encodage active à la fois. Vous pouvez réserver des unités d’encodage qui vous permettent d’exécuter plusieurs tâches d’encodage simultanément, une pour chaque unité réservée d’encodage que vous achetez. Pour plus d’informations, consultez [Mise à l’échelle des unités d’encodage](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>Utilisation
[Encodage avec Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formats
[Formats et codecs](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Présélections
Media Encoder Standard est configuré à l’aide d’une des présélections d’encodeur décrites [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Métadonnées d’entrée et de sortie
Les métadonnées d’entrée des encodeurs sont décrites [ici](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Les métadonnées de sortie des encodeurs sont décrites [ici](http://msdn.microsoft.com/library/azure/dn783217.aspx).

### <a name="generate-thumbnails"></a>Génération de miniatures
Pour plus d’informations, consultez [Génération de miniatures à l’aide de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

### <a name="trim-videos-clipping"></a>Découpage de vidéos (extrait)
Pour plus d’informations, consultez [Découpage de vidéos à l’aide de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#trim_video).

### <a name="create-overlays"></a>Création de superpositions
Pour plus d’informations, consultez [Création de superpositions à l’aide de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#overlay).

### <a name="see-also"></a>Voir aussi
[Blog sur Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Media Encoder Premium Workflow
### <a name="overview"></a>Vue d'ensemble
[Présentation de l’encodage Premium dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Utilisation
Media Encoder Premium Workflow se configure à l’aide de flux de travail complexes. Les fichiers de flux de travail peuvent être créés et mis à jour à l’aide de l’outil [Concepteur de flux de travail](media-services-workflow-designer.md) .

[Utilisation de l’encodage Premium dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Problèmes connus
Si votre vidéo d’entrée ne contient pas de sous-titres, l’élément multimédia de sortie actif comportera toujours un fichier TTML vide. 

## <a name="a-idcompareencodersacompare-encoders"></a><a id="compare_encoders"></a>Comparaison des encodeurs
### <a name="a-idbillingabilling-meter-used-by-each-encoder"></a><a id="billing"></a>Compteur de facturation utilisé par chaque encodeur
| Nom du processeur multimédia | Tarification applicable | Remarques |
| --- | --- | --- |
| **Media Encoder Standard** |ENCODEUR |Les tâches d’encodage seront facturées en fonction de la taille de l’élément multimédia de sortie, en gigaoctets, à la vitesse spécifiée [ici][1], sous la colonne ENCODEUR. |
| **Media Encoder Premium Workflow** |ENCODEUR PREMIUM |Les tâches d’encodage seront facturées en fonction de la taille de la ressource de sortie, en gigaoctets, à la vitesse spécifiée [ici][1], sous la colonne PREMIUM ENCODER. |

Cette section compare les fonctionnalités d’encodage de **Media Encoder Standard** et de **Media Encoder Premium Workflow**.

### <a name="input-containerfile-formats"></a>Formats de conteneurs/fichiers d’entrée
| Formats de conteneurs/fichiers d’entrée | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe® Flash® F4V |OUI |OUI |
| MXF/SMPTE 377M |OUI |OUI |
| GXF |OUI |OUI |
| MPEG-2 Transport Streams |OUI |OUI |
| MPEG-2 Program Streams |OUI |OUI |
| MPEG-4/MP4 |OUI |OUI |
| Windows Media/ASF |OUI |OUI |
| AVI (8 bits/10 bits non compressé) |OUI |OUI |
| 3GPP/3GPP2 |OUI |Non |
| Format de fichier de diffusion en continu lisse (PIFF 1.3) |OUI |Non |
| [Enregistrement vidéo numérique Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |OUI |Non |
| Matroska/WebM |OUI |Non |
| QuickTime (.mov) |OUI |Non |

### <a name="input-video-codecs"></a>Codecs vidéo d’entrée
| Codecs vidéo d’entrée | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra |8 bits 4:2:0 et 4:2:2 |OUI |
| Avid DNxHD (dans MXF) |OUI |OUI |
| DVCPro/DVCProHD (dans MXF) |OUI |OUI |
| JPEG2000 |OUI |OUI |
| MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10) |Jusqu’à un profil de 422 |OUI |
| MPEG-1 |OUI |OUI |
| Windows Media Video/VC-1 |OUI |OUI |
| Canopus HQ/HQX |Non |Non |
| MPEG-4 partie 2 |OUI |Non |
| [Theora](https://en.wikipedia.org/wiki/Theora) |OUI |Non |
| Apple ProRes 422 |OUI |Non |
| Apple ProRes 422 LT |OUI |Non |
| Apple ProRes 422 HQ |OUI |Non |
| Apple ProRes Proxy |OUI |Non |
| Apple ProRes 4444 |OUI |Non |
| Apple ProRes 4444 XQ |OUI |Non |

### <a name="input-audio-codecs"></a>Codecs audio d’entrée
| Codecs audio d’entrée | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M et 302M, AES3-2003) |Non |OUI |
| Dolby® E |Non |OUI |
| Dolby® Digital (AC3) |Non |OUI |
| Dolby® Digital Plus (E-AC3) |Non |OUI |
| AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1) |OUI |OUI |
| MPEG Layer 2 |OUI |OUI |
| MP3 (MPEG-1 Audio Layer 3) |OUI |OUI |
| Windows Media Audio |OUI |OUI |
| WAV/PCM |OUI |OUI |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |OUI |Non |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |OUI |Non |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |OUI |Non |

### <a name="output-containerfile-formats"></a>Formats de conteneurs/fichiers de sortie
| Formats de conteneurs/fichiers de sortie | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| Adobe® Flash® F4V |Non |OUI |
| MXF (OP1a, XDCAM et AS02) |Non |OUI |
| DPP (y compris AS11) |Non |OUI |
| GXF |Non |OUI |
| MPEG-4/MP4 |OUI |OUI |
| MPEG-TS |OUI |OUI |
| Windows Media/ASF |Non |OUI |
| AVI (8 bits/10 bits non compressé) |Non |OUI |
| Format de fichier de diffusion en continu lisse (PIFF 1.3) |Non |OUI |

### <a name="output-video-codecs"></a>Codecs vidéo de sortie
| Codecs vidéo de sortie | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AVC (H.264 ; 8 bits ; jusqu'au profil High, niveau 5.2 ; Ultra HD 4K ; AVC Intra) |Uniquement 8 bits 4:2:0 |OUI |
| Avid DNxHD (dans MXF) |Non |OUI |
| MPEG-2 (jusqu’au profil 422 et haut niveau ; y compris les variantes telles que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10) |Non |OUI |
| MPEG-1 |Non |OUI |
| Windows Media Video/VC-1 |Non |OUI |
| Création de miniatures JPEG |Non |OUI |

### <a name="output-audio-codecs"></a>Codecs audio de sortie
| Codecs audio de sortie | Media Encoder Standard | Media Encoder Premium Workflow |
| --- | --- | --- |
| AES (SMPTE 331M et 302M, AES3-2003) |Non |OUI |
| Dolby® Digital (AC3) |Non |OUI |
| Dolby® Digital Plus (E-AC3) jusqu'à 7.1 |Non |OUI |
| AAC (AAC-LC, AAC-HE et AAC-HEv2 ; jusqu’à 5.1) |OUI |OUI |
| MPEG Layer 2 |Non |OUI |
| MP3 (MPEG-1 Audio Layer 3) |Non |OUI |
| Windows Media Audio |Non |OUI |

## <a name="error-codes"></a>Codes d’erreur
Le tableau suivant répertorie les codes d’erreur susceptibles d’être renvoyés à la suite d’une erreur d’exécution d’une tâche de codage.  Pour obtenir les détails de l’erreur dans votre code .NET, utilisez la classe [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . Pour obtenir les détails de l’erreur dans votre code REST, utilisez l’API REST [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) .

| ErrorDetail.Code | Causes possibles de l’erreur |
| --- | --- |
| Unknown |Erreur inconnue lors de l’exécution de la tâche. |
| ErrorDownloadingInputAssetMalformedContent |Catégorie d’erreurs se produisant lors du téléchargement d’éléments multimédias d’entrée : noms de fichier incorrects, fichiers de longueur nulle, formats incorrects, etc. |
| ErrorDownloadingInputAssetServiceFailure |Catégorie d’erreurs regroupant les problèmes côté service, par exemple les erreurs réseau ou de stockage lors du téléchargement. |
| ErrorParsingConfiguration |Catégorie d’erreurs se produisant quand la tâche <see cref="MediaTask.PrivateData"/> (configuration) n’est pas valide. Par exemple, la configuration n’est pas une valeur système prédéfinie valide ou contient du code XML non valide. |
| ErrorExecutingTaskMalformedContent |Catégorie d’erreurs se produisant pendant l’exécution de la tâche. Des problèmes dans les fichiers multimédias d’entrée provoquent une défaillance. |
| ErrorExecutingTaskUnsupportedFormat |Catégorie d’erreurs se produisant quand le processeur multimédia ne peut pas traiter les fichiers fournis. Le format de média n’est pas pris en charge ou ne correspond pas à la configuration. Cela peut arriver si vous tentez de générer une sortie audio uniquement à partir d’un élément multimédia qui contient seulement des données vidéo. |
| ErrorProcessingTask |Catégorie d’erreurs qui ne sont pas liées au contenu que le processeur multimédia rencontre pendant le traitement de la tâche. |
| ErrorUploadingOutputAsset |Catégorie d’erreurs se produisant pendant le chargement de l’élément multimédia de sortie. |
| ErrorCancelingTask |Catégorie d’erreurs se produisant à la suite d’échecs d’annulation de la tâche. |
| TransientError |Catégorie d’erreurs pour couvrir les problèmes temporaires (par ex. problèmes réseau temporaires avec Azure Storage) |

Pour obtenir de l’aide de la part de l’équipe **Media Services** , ouvrez un [ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articles connexes
* [Exécution de tâches d’encodage avancées via la personnalisation des présélections Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas et limitations](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/



<!--HONumber=Nov16_HO3-->


