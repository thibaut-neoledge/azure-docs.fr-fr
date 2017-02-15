---
title: "Gérer les points de terminaison de streaming avec le Portail Azure | Microsoft Docs"
description: "Cette rubrique montre comment gérer les points de terminaison de streaming avec le Portail Azure."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e0351e286ae4f36c6c6fba25e5eaf4c135b21549


---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gérer les points de terminaison de streaming avec le Portail Azure
## <a name="overview"></a>Vue d'ensemble
> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Dans Microsoft Azure Media Services, un **point de terminaison de diffusion en continu** représente un service de diffusion en continu qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de diffusion de contenu (CDN) pour être redistribué. Media Services fournit également une intégration transparente au CDN Azure. Le flux sortant d’un service StreamingEndpoint peut être un flux dynamique ou un élément multimédia de vidéo à la demande dans votre compte Media Services.

En outre, vous pouvez contrôler la capacité du service de point de terminaison de streaming afin de gérer les besoins croissants en matière de bande passante en ajustant les unités de diffusion en continu. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Les unités de diffusion en continu fournissent à la fois une capacité de sortie dédiée que vous pouvez acquérir par incréments de 200 Mbit/s et des fonctionnalités supplémentaires incluant : [l’empaquetage dynamique](media-services-dynamic-packaging-overview.md), l’intégration au CDN et la configuration avancée.

> [!NOTE]
> Vous êtes facturé uniquement lorsque votre point de terminaison de streaming est en cours d’exécution.
> 
> 

Cette rubrique donne une vue d’ensemble des fonctionnalités principales offertes par les points de terminaison de streaming. Elle explique également comment utiliser le Portail Azure pour gérer les points de terminaison de streaming. Pour plus d’informations sur la mise à l’échelle du point de terminaison de streaming, consultez [cette](media-services-portal-scale-streaming-endpoints.md) rubrique.

## <a name="start-managing-streaming-endpoints"></a>Commencer à gérer des points de terminaison de streaming
Pour commencer à gérer les points de terminaison de streaming de votre compte, procédez comme suit.

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **Paramètres**, sélectionnez **Points de terminaison de diffusion en continu**.
   
    ![point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

## <a name="adddelete-a-streaming-endpoint"></a>Ajouter/supprimer un point de terminaison de streaming
Pour ajouter/supprimer un point de terminaison de streaming à l’aide du Portail Azure, procédez comme suit :

1. Pour ajouter un point de terminaison de streaming, cliquez sur le **+ Point de terminaison** en haut de la page. 
2. Pour supprimer un point de terminaison de streaming, appuyez sur le bouton **Supprimer** . 
   
    Il n’est pas possible de supprimer le point de terminaison de streaming par défaut.
3. Cliquez sur le bouton **Démarrer** pour démarrer le point de terminaison de streaming.
   
    ![point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

Par défaut, vous bénéficiez au maximum de deux points de terminaison de diffusion en continu. Si vous avez besoin de points de terminaisons supplémentaires, voir [Quotas et limitations](media-services-quotas-and-limitations.md).

## <a name="a-idconfigurestreamingendpointsaconfiguring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Configuration du point de terminaison de diffusion en continu
Lorsque vous disposez d’au moins une unité d’échelle, le point de terminaison de diffusion en continu vous permet de configurer les propriétés suivantes : 

* Contrôle d’accès
* Contrôle de cache
* Stratégies d’accès intersite

Pour plus d’informations sur ces propriétés, consultez la rubrique [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Vous pouvez configurer le point de terminaison de streaming en procédant comme suit :

1. Sélectionnez le point de terminaison de diffusion en continu que vous souhaitez configurer.
2. Cliquez sur **Paramètres**.

Vous trouverez une brève description des champs ci-dessous.

![point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Stratégie de cache maximale : permet de configurer la durée de vie du cache pour les ressources traitées par le biais de ce point de terminaison de streaming. Si aucune valeur n’est définie, la valeur par défaut est utilisée. Les valeurs par défaut peuvent également être définies directement dans Azure Storage. Si Azure CDN est activé pour le point de terminaison de streaming, ne définissez pas une valeur de stratégie de cache inférieure à 600 secondes.  
2. Adresses IP autorisées : permet de définir les adresses IP autorisées à se connecter au point de terminaison de streaming publié. Si aucune adresse IP n'est spécifiée, toutes les adresses IP peuvent se connecter. Les adresses IP peuvent être définies sous forme d’adresse IP unique (par exemple, « 10.0.0.1 »), de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau CIDR (par exemple, « 10.0.0.1/22 ») ou de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau au format décimal séparé par des points (par exemple, « 10.0.0.1(255.255.255.0) »).
3. Configuration de l’authentification de l’en-tête de signature Akamai : permet de spécifier la configuration de la demande d’authentification de l’en-tête de signature à partir de serveurs Akamai. L’expiration est au format UTC.

## <a name="a-idenablecdnaenable-azure-cdn-integration"></a><a id="enable_cdn"></a>Activer l’intégration au CDN Azure
Vous pouvez choisir d’activer l’intégration au CDN Azure pour un point de terminaison de diffusion en continu (cette option est désactivée par défaut).

Pour activer l’intégration au CDN Azure :

* Le point de terminaison de streaming doit avoir au moins une unité de diffusion en continu. Si par la suite, vous souhaitez définir le nombre d’unités d’échelle sur 0, vous devez au préalable désactiver l’intégration au CDN. Par défaut, lorsque vous créez un nouveau point de terminaison de diffusion en continu, une unité de diffusion en continu est définie automatiquement.
* Le point de terminaison de diffusion en continu doit être à l’état arrêté. Une fois le CDN activé, vous pouvez démarrer le point de terminaison de diffusion en continu. 

L’activation de l’intégration au CDN Azure peut prendre jusqu’à 90 minutes.  Il peut s’écouler jusqu’à deux heures avant que les modifications ne soient actives sur tous les comptes POP CDN.

L’intégration de CDN est activée dans les centres de données Azure : Ouest des États-Unis, Est des États-Unis, Europe du Nord, Europe de l’Ouest, Japon de l’Ouest, Japon de l’Est, Asie du Sud-Est et Asie de l’Est.

Une fois qu’elle est activée, la configuration **Access Control** est désactivée.

![point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

> [!IMPORTANT]
> L’intégration d’Azure Media Services au CDN Azure est implémentée sur le **CDN Azure fourni par Verizon**.  Si vous souhaitez utiliser le **CDN Azure fourni par Akamai** pour Azure Media Services, vous devez [configurer le point de terminaison manuellement](../cdn/cdn-create-new-endpoint.md).  Pour plus d’informations sur les fonctionnalités du CDN Azure, consultez [Vue d’ensemble du réseau de distribution de contenu (CDN)](../cdn/cdn-overview.md).
> 
> 

### <a name="additional-considerations"></a>Considérations supplémentaires
* Lorsque le CDN est activé pour un point de terminaison de diffusion en continu, les clients ne peuvent pas demander directement le contenu à partir de l’origine. Si vous avez besoin de tester votre contenu avec ou sans CDN, vous pouvez créer un autre point de terminaison de streaming qui n’est pas activé pour le CDN.
* Le nom d’hôte de votre point de terminaison reste le même une fois le CDN activé. Vous n’avez pas besoin d’apporter de modifications à votre flux de travail Media Services une fois le CDN activé. Par exemple, si le nom d’hôte de votre point de terminaison en continu est strasbourg.streaming.mediaservices.windows.net, après avoir activé le CDN, le même nom d’hôte est utilisé.
* Pour les nouveaux points de terminaison de streaming, vous pouvez simplement activer le CDN en créant un nouveau point de terminaison. Pour les points de terminaison de streaming existants, vous devez d’abord arrêter le point de terminaison, puis activer le CDN.

Pour plus d’informations, consultez le blog [Announcing Azure Media Services integration with Azure CDN (Content Delivery Network)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/)(Annonce de l’intégration d’Azure Media Services avec Azure CDN).

## <a name="next-steps"></a>Étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


