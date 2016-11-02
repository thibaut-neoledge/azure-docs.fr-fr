<properties
	pageTitle="Encoder un élément multimédia à l’aide de Media Encoder Standard avec le Portail Azure | Microsoft Azure"
	description="Ce didacticiel vous guide à travers les étapes d’encodage d’un élément multimédia à l’aide de Media Encoder Standard avec le Portail Azure."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="juliako"/>


# Encoder un élément multimédia à l’aide de Media Encoder Standard avec le Portail Azure

> [AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

Lorsque vous travaillez avec Azure Media Services, un des scénarios les plus courants est la diffusion de contenu à débit adaptatif à vos clients. Media Services prend en charge les technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement). Pour préparer vos vidéos au streaming à débit adaptatif, vous devez encoder votre vidéo source en fichiers à débit binaire multiple. Vous devez utiliser **Media Encoder Standard** pour encoder vos vidéos.

Media Services fournit également l’empaquetage dynamique qui vous permet de distribuer des fichiers MP4 à débit binaire multiple dans les formats MPEG DASH, HLS, Smooth Streaming ou HDS, sans avoir à recréer de nouveaux packages dans ces formats. Avec l’empaquetage dynamique, vous devez stocker et payer les fichiers dans un seul format de stockage. Ensuite, Media Services crée et fournit la réponse appropriée en fonction des demandes des clients.

Pour tirer parti de l’empaquetage dynamique, vous devez effectuer les opérations suivantes :

- Encoder votre fichier source dans un ensemble de fichiers MP4 à débit binaire multiple (les étapes de codage sont décrites plus loin dans cette section).
- Obtenir au moins une unité de diffusion pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu. Pour plus d’informations, consultez la section [Configuration des points de terminaison de diffusion en continu](media-services-portal-vod-get-started.md#configure-streaming-endpoints).

Pour mettre à l’échelle le traitement multimédia, consultez [cette](media-services-portal-scale-media-processing.md) rubrique.

## Encoder à l’aide du Portail Azure

Cette section décrit les étapes à suivre pour encoder votre contenu avec Media Encoder Standard.

1.  Dans la fenêtre **Paramètres**, sélectionnez **Éléments multimédias**.
2.  Dans la fenêtre **Éléments multimédias**, sélectionnez l’élément que vous souhaitez encoder.
3.  Appuyez sur le bouton **Encoder**.
4.  Dans la fenêtre **Encode an asset** (Encoder un élément multimédia), sélectionnez le processeur Media Encoder Standard et choisissez une présélection. Par exemple, si vous savez que votre vidéo d’entrée possède une résolution de 1920 x 1080 pixels, vous pouvez utiliser la présélection « H264 Multiple Bitrate 1080p ». Pour plus d’informations sur les présélections, consultez [cet article](https://msdn.microsoft.com/library/azure/mt269960.aspx). Il est important de choisir la présélection qui convient le mieux à votre vidéo. Si vous avez une vidéo de basse résolution (640 x 360), il est préférable de ne pas utiliser la présélection par défaut « H264 Multiple Bitrate1080p ».
	
	Pour des questions pratiques, vous avez la possibilité de modifier le nom de l’élément multimédia de sortie ainsi que le nom de la tâche.
		
	![Encoder des éléments multimédias](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Appuyez sur **Créer**.


##Étape suivante

Vous pouvez surveiller la progression du travail d’encodage avec le Portail Azure, comme le décrit [cet](media-services-portal-check-job-progress.md) article.

##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->