---
title: "Encoder un élément multimédia à l’aide de Media Encoder Standard avec le Portail Azure | Microsoft Docs"
description: "Ce didacticiel vous guide à travers les étapes d’encodage d’un élément multimédia à l’aide de Media Encoder Standard avec le Portail Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: SyntaxC4
editor: 
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: efe7db8a36273b4755dd057139bb1c673af868d3
ms.contentlocale: fr-fr
ms.lasthandoff: 08/08/2017

---
# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Encoder un élément multimédia à l’aide de Media Encoder Standard avec le Portail Azure
> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Lorsque vous travaillez avec Azure Media Services, un des scénarios les plus courants est la diffusion de contenu à débit adaptatif à vos clients. Media Services prend en charge les technologies de streaming à débit adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH. Pour préparer vos vidéos au streaming à débit adaptatif, vous devez encoder votre vidéo source en fichiers à débit binaire multiple. Vous devez utiliser **Media Encoder Standard** pour encoder vos vidéos.  

Media Services assure également l’empaquetage dynamique qui vous permet de diffuser des fichiers MP4 multidébit dans les formats de diffusion en continu MPEG DASH, HLS ou Smooth Streaming, sans avoir à effectuer de ré-empaquetage dans ces formats. Avec l’empaquetage dynamique, vous devez stocker et payer les fichiers dans un seul format de stockage. Ensuite, Media Services crée et fournit la réponse appropriée en fonction des demandes des clients.

Pour tirer parti de l’empaquetage dynamique, vous devez encoder votre fichier source dans un ensemble de fichiers MP4 multidébit (les étapes de l’encodage sont décrites plus loin dans cette section).

Pour mettre à l’échelle le traitement multimédia, consultez [cette](media-services-portal-scale-media-processing.md) rubrique.

## <a name="encode-with-the-azure-portal"></a>Encoder à l’aide du Portail Azure
Cette section décrit les étapes à suivre pour encoder votre contenu avec Media Encoder Standard.

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **Paramètres**, sélectionnez **Éléments multimédias**.  
3. Dans la fenêtre **Éléments multimédias** , sélectionnez l’élément que vous souhaitez encoder.
4. Appuyez sur le bouton **Encoder** .
5. Dans la fenêtre **Encoder un élément multimédia**, sélectionnez le processeur Media Encoder Standard et choisissez une présélection. Pour plus d’informations sur les présélections, consultez [Utilisation d’Azure Media Encoder Standard pour générer automatiquement une échelle des vitesses de transmission](media-services-autogen-bitrate-ladder-with-mes.md) et [Présélections de travaux pour MES (Media Encoder Standard)](media-services-mes-presets-overview.md). Si vous envisagez de contrôler quelle préselection d’encodage est utilisée, n’oubliez pas qu’il est important de sélectionner la préselection qui convient le mieux à votre entrée vidéo. Par exemple, si vous savez que votre vidéo d’entrée possède une résolution de 1920 x 1080 pixels, vous pouvez utiliser la présélection « H264 Multiple Bitrate 1080p ». Si vous avez une vidéo de basse résolution (640 x 360), il est préférable de ne pas utiliser la présélection « H264 Multiple Bitrate1080p ».
   
   Pour des questions pratiques, vous avez la possibilité de modifier le nom de l’élément multimédia de sortie ainsi que le nom de la tâche.
   
   ![Encoder des éléments multimédias](./media/media-services-portal-vod-get-started/media-services-encode1.png)
6. Appuyez sur **Créer**.

## <a name="next-step"></a>Étape suivante
Vous pouvez surveiller la progression du travail d’encodage avec le Portail Azure, comme le décrit [cet](media-services-portal-check-job-progress.md) article.  

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


