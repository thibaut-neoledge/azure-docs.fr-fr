---
title: "Forum Aux Questions (FAQ) | Microsoft Docs"
description: Forum Aux Questions (FAQ)
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 5374f7f4-c189-43ef-8b7f-f2f4141e2748
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 6972550d74965fa6a7c69a5903b3287d5d219851


---
# <a name="frequently-asked-questions"></a>Forum Aux Questions
## <a name="general-ams-faqs"></a>Forum Aux Questions - Généralités AMS
Q : comment mettre à l’échelle l’indexation ?

R : les unités réservées sont les mêmes pour les tâches d’encodage et d’indexation. Suivez les instructions de la page [Comment mettre à l’échelle des unités réservées mise à l’échelle des unités réservées d’encodage](media-services-scale-media-processing-overview.md). **Remarque** : le fonctionnement de l’indexeur n’est pas affecté par le type d’unité réservée.

Q : j’ai chargé, encodé et publié une vidéo. Pourquoi la vidéo n’est-elle pas lue lorsque j’essaie de la diffuser en continu ?

R : une des raisons les plus courantes est que vous n’avez pas au moins une unité réservée de diffusion en continu allouée sur le point de terminaison de diffusion en continu à partir duquel vous essayez de lire la vidéo.  Suivez les instructions de la page [Mise à l’échelle des unités réservées de diffusion en continu](media-services-portal-scale-streaming-endpoints.md).

Q : la composition d’un flux dynamique est-elle possible ?

R : la composition de flux dynamiques n’est pas disponible pour le moment dans Azure Media Services. Vous devez donc effectuer la composition au préalable sur votre ordinateur.

Q : puis-je utiliser le CDN Azure avec la vidéo en flux continu ?

R : Media Services prend en charge l’intégration au CDN Azure (pour plus d’informations, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-portal-manage-streaming-endpoints.md)).  Vous pouvez utiliser la vidéo en flux continu avec le CDN. Azure Media Services fournit des sorties aux formats Smooth Streaming, HLS et MPEG-DASH. Tous ces formats utilisent le protocole HTTP pour transférer les données et bénéficient des avantages de la mise en cache HTTP. Lors d’une diffusion vidéo en flux continu, les données vidéo/audio sont divisées en fragments individuels qui sont mis en cache dans le CDN. Les seules données devant être actualisées sont les données de manifeste. CDN actualise régulièrement les données de manifeste.

Q : le stockage des images est-il pris en charge par Azure Media Services ?

R : si vous cherchez uniquement à stocker des images JPEG ou PNG, nous vous recommandons de les conserver dans le stockage d’objets blob Azure. Il n’y a aucun avantage à les placer dans votre compte Media Services, à moins que vous souhaitiez qu’elles restent associées à vos ressources vidéo ou audio Ou, si vous avez besoin d'utiliser les images sous forme de superpositions dans l'encodeur vidéo, Media Encoder Standard prend en charge la superposition d’images sur les vidéos, ce qui explique pourquoi JPEG et PNG figurent parmi les formats d’entrée pris en charge. Pour plus d’informations, consultez la page [Création de superpositions](media-services-advanced-encoding-with-mes.md#overlay).

Q : comment puis-je copier des éléments multimédias d’un compte Media Services vers un autre ?

R : pour copier des éléments multimédias d’un compte Media Services vers un autre à l’aide de .NET, utilisez la méthode d’extension [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponible dans le référentiel [Extensions du Kit de développement logiciel (SDK) Azure Media Services](https://github.com/Azure/azure-sdk-for-media-services-extensions/). Pour plus d’informations, consultez [cette publication de forum](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) .

Q: quels sont les caractères pris en charge pour les noms des fichiers en utilisant AMS ?

R : Media Services utilise la valeur de la propriété IAssetFile.Name lors de la génération d’URL pour le contenu de diffusion en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Pour cette raison, l’encodage par pourcentage n’est pas autorisé. La valeur de la propriété **Name** ne peut pas comporter les [caractères réservés à l’encodage en pourcentage](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) suivants : !*'();:@&=+$,/?%#[]". En outre, il ne peut y avoir qu’un « . » pour l’extension de nom de fichier.

Q: comment se connecter avec REST ?

R: Après vous être connecté à https://media.windows.net, vous recevrez une redirection 301 spécifiant un autre URI Media Services. Vous devez effectuer les appels suivants au nouvel URI comme décrit dans [Connexion à Media Services à l'aide de l'API REST](media-services-rest-connect-programmatically.md).

Q : comment faire pivoter une vidéo au cours du processus d’encodage ?

R : [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) prend en charge trois angles de rotation (90, 180 et 270). Le comportement par défaut est « Auto », ce qui signifie qu’il tente de détecter les métadonnées de rotation dans le fichier MP4/MOV entrant et de les compenser. Incluez l’élément **Sources** suivant dans l’une des présélections json définies [ici](http://msdn.microsoft.com/library/azure/mt269960.aspx):

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...




## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=Dec16_HO2-->


