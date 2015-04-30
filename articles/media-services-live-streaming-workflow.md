<properties 
	pageTitle="Diffusion vidéo en flux continu avec Azure Media Services" 
	description="Cette rubrique décrit les étapes d'un workflow en diffusion en continu Media Services standard." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2015" 
	ms.author="juliako"/>


# Diffusion vidéo en flux continu avec Azure Media Services

## Vue d'ensemble

Cette rubrique décrit les étapes d'un workflow en diffusion en continu Azure Media Services (AMS) standard. Chaque étape dirige vers des rubriques pertinentes. Pour les tâches pouvant être effectuées en utilisant différentes technologies, des boutons vous envoient à la technologie de votre choix (par exemple, .NET ou REST).   

Notez que vous pouvez intégrer Media Services à vos outils et processus existants. Par exemple, encodez un contenu local, puis téléchargez-le dans Media Services pour un transcodage en différents formats et une diffusion via le réseau de distribution de contenu (CDN) Azure ou via un CDN tiers. 

Le diagramme suivant présente les principaux composants de la plateforme Media Services impliqués dans le workflow de vidéo en flux continu.

![Live workflow][live-overview]

Cette rubrique décrit les concepts liés à la vidéo en flux continu et offre des liens vers des rubriques expliquant comment effectuer les tâches de diffusion vidéo en flux continu.

## Concepts

Pour les concepts liés à la vidéo en flux continu, consultez la page [Concepts Media Services](media-services-concepts.md).

## Création d'un compte Media Services

Utilisez le **portail de gestion Azure** pour [créer un compte Azure Media Services](media-services-create-account.md).

## Configuration de points de terminaison de diffusion en continu

Pour plus d'informations sur les points de terminaison de diffusion en continu et leur gestion, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-manage-origins.md)

## Configuration d'un environnement de développement  

Choisissez **.NET** ou **API REST** comme environnement de développement.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

## Connexion par programme  

Choisissez **.NET** ou **API REST** pour vous connecter par programme à Azure Media Services.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]


## Utilisation d'encodeurs dynamiques locaux pour envoyer un flux à débit binaire multiple vers un canal

## Utilisation de transcodeurs dynamiques tiers

Pour plus d'informations, consultez [Utilisation d'encodeurs tiers en temps réel avec Azure Media Services](https://msdn.microsoft.com/library/azure/dn783464.aspx).

## Gestion des canaux, des programmes, des ressources

**Vue d'ensemble** : [Vue d'ensemble de la gestion des canaux et des programmes](media-services-manage-channels-overview.md).

Choisissez **Portail**, **.NET**, **API REST** pour voir des exemples.

[AZURE.INCLUDE [media-services-selector-manage-channels](../includes/media-services-selector-manage-channels.md)]

## Configuration de la stratégie de remise de ressources

Configurez la stratégie de remise de ressources à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

## Création d'une clé de contenu

Créez une clé de contenu avec laquelle chiffrer votre ressource à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

## Configuration de la stratégie d'autorisation de clé de contenu 

Configurez la stratégie de protection de contenu et d'autorisation de clé à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]


## Publication et distribution d'éléments multimédias

**Vue d'ensemble** : [Vue d'ensemble de la distribution de contenu](media-services-deliver-content-overview.md)

Publiez des ressources (en créant des localisateurs) à l'aide du **portail de gestion Azure** ou de **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


## Activation du CDN Azure

Media Services prend en charge l'intégration au CDN Azure. Pour plus d'informations sur l'activation du CDN Azure, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-manage-origins.md#enable_cdn).

## Mise à l'échelle d'un compte Media Services

Vous pouvez mettre à l'échelle **Media Services** en spécifiant le nombre d'**Unités réservées de diffusion en continu** avec lesquelles vous voulez que votre compte soit approvisionné. 

Pour plus d'informations sur la mise à l'échelle des unités de diffusion en continu, consultez : [Mise à l'échelle des unités de diffusion en continu](media-services-manage-origins.md#scale_streaming_endpoints.md).




[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png

<!--HONumber=52-->