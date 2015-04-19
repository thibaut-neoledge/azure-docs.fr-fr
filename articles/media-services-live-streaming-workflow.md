<properties 
	pageTitle="Workflow en flux continu Media Services" 
	description="Cette rubrique décrit les étapes d'un workflow en diffusion en continu Media Services standard." 
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
	ms.date="02/18/2015" 
	ms.author="juliako"/>


#Workflow en diffusion en continu Media Services

##Vue d'ensemble

Cette rubrique décrit les étapes d'un workflow en diffusion en continu Azure Media Services (AMS) standard. Chaque étape dirige vers des rubriques pertinentes. Pour les tâches pouvant être effectuées en utilisant différentes technologies, des boutons vous envoient à la technologie de votre choix (par exemple, .NET ou REST).   

Notez que vous pouvez intégrer Media Services à vos outils et processus existants. Par exemple, encodez un contenu local, puis téléchargez-le dans Media Services pour un transcodage en différents formats et une diffusion via le réseau de distribution de contenu (CDN) Azure ou via un CDN tiers. 

Le diagramme suivant présente les principaux composants de la plate-forme Media Services impliqués dans le workflow de vidéo à la demande.
![Live workflow][live-overview]


Pour plus d'informations, consultez [Vue d'ensemble de Media Services](media-services-overview.md).

##Création d'un compte Media Services

Utilisez le **portail de gestion Azure** pour la [Création d'un compte Azure Media Services](media-services-create-account.md).

##Configuration des points de terminaison de diffusion en continue

[travail en cours]

##Configuration d'un environnement de développement  

Choisissez **.NET** ou **API REST** comme environnement de développement.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##Connexion par programme  

Choisissez **.NET** ou **API REST** pour vous connecter par programme à Azure Media Services.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##Utilisation de transcodeurs dynamiques

Pour plus d'informations, consultez [Utilisation d'encodeurs tiers en temps réel avec Azure Media Services](https://msdn.microsoft.com/library/azure/dn783464.aspx).

##Gestion des canaux, des programmes, des ressources

Pour plus d'informations, consultez [Diffusion en direct](https://msdn.microsoft.com/library/azure/dn783466.aspx).

##Configurer la stratégie de protection de contenu et d'autorisation de clé de contenu 

Configurez la stratégie d'autorisation de clé à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##Configuration de la stratégie de remise de ressources

Configurez la stratégie de remise de ressources à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##Publication de ressources

Publiez des ressources (en créant des localisateurs) à l'aide du **portail de gestion Azure** ou de **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


##Mise à l'échelle d'un compte Media Services

Vous pouvez mettre à l'échelle **Media Services** en spécifiant le nombre d'**Unités réservées de diffusion en continu** avec lesquelles vous voulez que votre compte soit approvisionné. 

Pour plus d'informations sur la mise à l'échelle d'unités de diffusion en continu, consultez : [Mise à l'échelle des unités de diffusion en continu](media-services-manage-origins#scale_streaming_endpoints.md).



[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png
<!--HONumber=47-->
