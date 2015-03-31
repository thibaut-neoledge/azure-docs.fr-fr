<properties 
	pageTitle="Lecture du contenu" 
	description="Cette rubrique décrit comment lire votre contenu." 
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
	ms.date="02/16/2015" 
	ms.author="juliako"/>


#Lecture du contenu

Azure Media Services prend en charge de nombreux formats de diffusion en continu populaires, tels que la diffusion en continu lisse, la diffusion en continu HTTP et MPEG-Dash. Cette rubrique vous oriente vers les lecteurs existants que vous pouvez utiliser pour tester votre flux de données.  

##Lecture de votre contenu à l'aide des lecteurs existants

Cette rubrique présente les lecteurs existants avec lesquels vous pouvez lire votre contenu.

>[AZURE.NOTE]Pour lire du contenu empaqueté ou chiffré dynamiquement, veillez à obtenir au moins une unité de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de remettre votre contenu. Pour plus d'informations sur la mise à l'échelle des unités de diffusion en continu, consultez : [Mise à l'échelle des unités de diffusion en continu](../media-services-manage-origins#scale_streaming_endpoints).


###Lecteur de contenu Media Services sur le portail de gestion Azure

Le **portail de gestion Azure** propose un lecteur de contenu que vous pouvez utiliser pour tester vos vidéos.

Cliquez sur la vidéo souhaitée (qui doit avoir été préalablement [publiée](../media-services-manage-content#publish)), puis sur le bouton **Lire** au bas du portail. 
 
Certaines considérations s'appliquent :

- Le **Lecteur de contenu des Services de média** assure la lecture depuis le point de terminaison de diffusion en continu par défaut. Si vous souhaitez lire à partir d'un autre point de terminaison de diffusion en continu que celui par défaut, utilisez un autre lecteur, par exemple, [le lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

###Lecteur Azure Media Services

Utilisez le [lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html) pour lire votre contenu.


##Développement de lecteurs vidéo

Pour plus d'informations sur le développement de vos propres lecteurs, consultez [Développement de lecteurs vidéo](../media-services-develop-video-players)
 
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png
<!--HONumber=47-->
