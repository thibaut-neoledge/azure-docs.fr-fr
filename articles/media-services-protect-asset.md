<properties 
	pageTitle="Chiffrement des éléments multimédias dans Media Services - Azure" 
	description="Apprenez à utiliser Microsoft PlayReady Protection pour chiffrer un élément multimédia dans Media Services. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# Procédure : Chiffrement dynamique d'une ressource avec PlayReady ou AES-128

Cet article fait partie des séries [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md) et [workflow de vidéo en flux continu Media Services](media-services-live-streaming-workflow.md).
  
## Vue d'ensemble

Microsoft Azure Media Services permet de transmettre un contenu chiffré (dynamiquement) avec la norme AES (Advanced Encryption Standard) (à l'aide de clés de chiffrement 128 bits) et le DRM PlayReady. Media Services fournit également un service de distribution de clés et licences PlayReady aux clients autorisés. Pour fournir du contenu protégé, vous devez configurer la stratégie d'autorisation de clé de contenu et les stratégies de remise de ressources.

## Configuration

Pour plus d'informations sur la configuration de la stratégie d'autorisation de clé de contenu 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

Configuration de stratégies de remise de ressources
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]
 

<!--HONumber=52-->