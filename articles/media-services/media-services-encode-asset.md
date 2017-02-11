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
ms.sourcegitcommit: ff663f40507547ba561053b5c9a7a8ce93fbf213
ms.openlocfilehash: ea3d67f7e73f9e1fb716e9c5cdd4873b0b7dddd4


---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure
## <a name="encoding-overview"></a>Vue d’ensemble de l’encodage
Azure Media Services fournit plusieurs options pour l’encodage de fichiers multimédias dans le cloud.

Quand vous commencez à utiliser Media Services, il est important de bien comprendre la différence entre les codecs et les formats de fichiers.
Les codecs sont les logiciels qui implémentent les algorithmes de compression/décompression, tandis que les formats de fichiers sont des conteneurs qui contiennent la vidéo compressée.

Media Services fournit l’empaquetage dynamique qui permet de distribuer un contenu en diffusion continue en MP4 ou Smooth Streaming dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming) sans avoir à recréer de nouveaux packages dans ces formats.

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
Pour plus d’informations, consultez [Génération de miniatures à l’aide de Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Découpage de vidéos (extrait)
Pour plus d’informations, consultez [Découpage de vidéos à l’aide de Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Création de superpositions
Pour plus d’informations, consultez [Création de superpositions à l’aide de Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

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


## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articles connexes
* [Exécution de tâches d’encodage avancées via la personnalisation des présélections Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas et limitations](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/



<!--HONumber=Dec16_HO2-->


