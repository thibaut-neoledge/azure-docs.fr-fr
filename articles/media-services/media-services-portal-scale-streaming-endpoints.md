---
title: " Mettre à l’échelle des points de terminaison de streaming avec le Portail Azure | Microsoft Docs"
description: "Ce didacticiel vous guide à travers les étapes de mise à l’échelle des points de terminaison de streaming avec le Portail Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: ff663f40507547ba561053b5c9a7a8ce93fbf213
ms.openlocfilehash: f9f7872eab6b61afcbdc2d8eb23f1dc8ada82829


---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Mettre à l’échelle des points de terminaison de streaming avec le Portail Azure
## <a name="overview"></a>Vue d'ensemble
> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Lorsque vous utilisez Azure Media Services, la diffusion à vos clients de vidéos en continu à débit binaire adaptatif constitue l’un des scénarios les plus courants. Media Services prend en charge les technologies de streaming à débit adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

Media Services assure l’empaquetage dynamique, qui vous permet de distribuer juste-à-temps un contenu encodé en MP4 à débit adaptatif dans un format de diffusion en continu pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming), sans qu’il vous soit nécessaire de stocker des versions pré-empaquetées de chacun de ces formats.

Pour tirer parti de l’empaquetage dynamique, vous devez effectuer les opérations suivantes :

* Encoder votre fichier mezzanine (source) dans un ensemble de fichiers MP4 à débit adaptatif (les étapes de codage sont décrites plus loin dans ce didacticiel).  
* Créez au moins une unité de diffusion pour le *point de terminaison de diffusion en continu* à partir duquel vous envisagez de distribuer votre contenu. La procédure ci-dessous explique comment modifier le nombre d’unités de diffusion en continu.

Avec l’empaquetage dynamique, vous devez stocker et payer les fichiers dans un seul format de stockage. Ensuite, Media Services crée et fournit la réponse appropriée en fonction des demandes des clients.

En outre, vous pouvez contrôler la capacité du service de point de terminaison de streaming afin de gérer les besoins croissants en matière de bande passante en ajustant les unités de diffusion en continu. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Les unités de diffusion en continu fournissent à la fois une capacité de sortie dédiée que vous pouvez acquérir par incréments de 200 Mbit/s et des fonctionnalités supplémentaires incluant : [l’empaquetage dynamique](media-services-dynamic-packaging-overview.md), l’intégration au CDN et la configuration avancée. Pour plus d’informations, consultez [Gérer les points de terminaison de streaming avec le Portail Azure](media-services-portal-manage-streaming-endpoints.md).

## <a name="scale-streaming-endpoints"></a>Mettre à l’échelle les points de terminaison de streaming
Pour créer et modifier le nombre d’unités réservées de diffusion en continu, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **Paramètres**, sélectionnez **Points de terminaison de diffusion en continu**.
3. Cliquez sur le point de terminaison de streaming que vous souhaitez mettre à l’échelle. 
4. Déplacez le curseur pour spécifier le nombre d’unités de diffusion en continu

![point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

Les considérations suivantes s'appliquent :

* L’allocation de nouvelles unités de diffusion en continu peut prendre environ 20 minutes. 
* Actuellement, le fait de passer d’une valeur positive à zéro pour le nombre d’unités de diffusion en continu peut désactiver la diffusion en continu pendant une heure.
* C’est le plus grand nombre d’unités spécifiées sur 24 heures qui est utilisé pour calculer le coût. Pour des informations détaillées sur la tarification, consultez la page [Détails de la tarification des services de média](http://go.microsoft.com/fwlink/?LinkId=275107).

## <a name="next-steps"></a>Étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Dec16_HO2-->


