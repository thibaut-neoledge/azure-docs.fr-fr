---
title: "Création de filtres avec le Kit de développement logiciel (SDK) .NET Azure Media Services"
description: "Cette rubrique décrit comment créer des filtres pour que votre client puisse les utiliser pour diffuser des sections spécifiques d&quot;un flux. Media Services crée des manifestes dynamiques pour obtenir cette diffusion sélective."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/18/2016
ms.author: juliako;cenkdin
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 07239a392438afd9807ce69fba657c27e853a219


---
# <a name="creating-filters-with-azure-media-services-net-sdk"></a>Création de filtres avec le Kit de développement logiciel (SDK) .NET Azure Media Services
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

À partir de la version 2.11, Media Services vous permet de définir des filtres pour vos éléments multimédias. Ces filtres sont des règles côté serveur qui permettent à vos clients de choisir d'effectuer des opérations comme les suivantes : lecture d'une section d'une vidéo uniquement (au lieu de la vidéo entière), spécification d'un seul sous-ensemble de rendus audio et vidéo pouvant être gérés par l'appareil de votre client (au lieu de tous les rendus associés à l'élément multimédia). Ce filtrage de vos éléments multimédias est obtenu via des **manifestes dynamiques**créés à la demande de votre client pour diffuser une vidéo selon des filtres spécifiés.

Pour plus d'informations sur les filtres et le manifeste dynamique, consultez [Vue d'ensemble des manifestes dynamiques](media-services-dynamic-manifest-overview.md).

Cette rubrique montre comment utiliser le SDK .NET de Media Services pour créer, mettre à jour et supprimer des filtres. 

Remarque : si vous mettez à jour un filtre, il peut falloir jusqu’à 2 minutes pour que le point de terminaison de diffusion en continu actualise les règles. Si le contenu a été servi à l'aide de ce filtre (puis mis en cache dans des proxys et des caches CDN), la mise à jour de ce filtre peut entraîner des défaillances du lecteur. Il est recommandé d'effacer le cache après la mise à jour du filtre. Si cette option n'est pas possible, envisagez d'utiliser un filtre différent. 

## <a name="types-used-to-create-filters"></a>Types utilisés pour créer des filtres
Les types suivants sont utilisés lors de la création de filtres : 

* **IStreamingFilter**.  Ce type est basé sur l’API REST suivante : [Filter](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Ce type est basé sur l’API REST suivante : [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Ce type est basé sur l’API REST suivante : [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** et **IFilterTrackPropertyCondition**. Ces types sont basés sur les API REST suivantes [FilterTrackSelect et FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Création/Mise à jour/Lecture/Suppression de filtres globaux
Le code suivant montre comment utiliser .NET pour créer, mettre à jour, lire et supprimer des filtres d’éléments multimédia.

    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


## <a name="createupdatereaddelete-asset-filters"></a>Création/Mise à jour/Lecture/Suppression de filtres d’éléments multimédia
Le code suivant montre comment utiliser .NET pour créer, mettre à jour, lire et supprimer des filtres d’éléments multimédia.

    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();




## <a name="build-streaming-urls-that-use-filters"></a>Création d'URL de diffusion qui utilisent des filtres
Pour plus d'informations sur la publication et la distribution de vos éléments multimédias, consultez [Vue d'ensemble de la distribution de contenu aux clients](media-services-deliver-content-overview.md).

Les exemples suivants montrent comment ajouter des filtres à vos URL de diffusion.

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Voir aussi
[Vue d'ensemble des manifestes dynamiques](media-services-dynamic-manifest-overview.md)




<!--HONumber=Feb17_HO3-->


