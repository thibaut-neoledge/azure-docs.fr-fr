<properties 
	pageTitle="Utilisation de partenaires pour fournir des licences Widevine à Azure Media Services" 
	description="Cet article décrit comment vous pouvez utiliser Azure Media Services (AMS) pour fournir un flux chiffré dynamiquement par AMS avec des DRM PlayReady et Widevine. La licence PlayReady provient du serveur de licences Media Services PlayReady et la licence Widevine est délivrée par le serveur de licences castLabs." 
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
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Utilisation de partenaires pour fournir des licences Widevine à Azure Media Services

##Vue d'ensemble

Microsoft Azure Media Services vous permet de diffuser du contenu MPEG-DASH protégé par Widevine DRM, qui est chiffré conformément à la spécification du chiffrement commun (CENC).

En commençant par le kit de développement logiciel (SDK) Media Services .NET version 3.5.2, Media Services vous permet de configurer le modèle de licence Widevine et d’obtenir des licences Widevine. Vous pouvez également utiliser les partenaires AMS suivants pour vous aider à distribuer des licences Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##castLabs

Vous pouvez utiliser [castLabs](http://castlabs.com/company/partners/azure/) pour fournir des licences Widevine. Pour plus d’informations, consultez [Utilisation de castLabs pour fournir des licences DRM à Azure Media Services](media-services-castlabs-integration.md).

##Axinom

Vous pouvez utiliser [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) pour fournir des licences Widevine. Pour plus d’informations, consultez [Utilisation d’Axinom pour fournir des licences DRM à Azure Media Services](media-services-axinom-integration.md).


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Voir aussi

[Utilisation du chiffrement commun dynamique PlayReady et/ou Widevine](media-services-protect-with-drm.md)

[Blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!---HONumber=AcomDC_0211_2016-->