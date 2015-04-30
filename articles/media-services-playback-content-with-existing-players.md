<properties 
	pageTitle="Lecture du contenu" 
	description="Cette rubrique répertorie les lecteurs existants que vous pouvez utiliser pour lire votre contenu." 
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
	ms.date="04/08/2015" 
	ms.author="juliako"/>


# Lecture de votre contenu à l'aide des lecteurs existants

Azure Media Services prend en charge de nombreux formats de diffusion en continu populaires, tels que la diffusion en continu lisse, la diffusion en continu HTTP et MPEG-Dash. Cette rubrique vous oriente vers les lecteurs existants que vous pouvez utiliser pour tester votre flux de données.  

>[AZURE.NOTE]Pour lire du contenu empaqueté ou chiffré dynamiquement, veillez à obtenir au moins une unité de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de remettre votre contenu. Pour plus d'informations sur la mise à l'échelle des unités de diffusion en continu, consultez : [Mise à l'échelle des unités de diffusion en continu](media-services-manage-origins.md#scale_streaming_endpoints).

### Lecteur de contenu Media Services sur le portail de gestion Azure

Le **portail de gestion Azure** propose un lecteur de contenu que vous pouvez utiliser pour tester vos vidéos.

Cliquez sur la vidéo de votre choix (assurez-vous qu'elle a été [publiée](media-services-manage-content.md#publish)), puis sur le bouton **Lire** situé en bas du portail. 
 
Certaines considérations s'appliquent :

- Le **Lecteur de contenu des Services de média** assure la lecture depuis le point de terminaison de diffusion en continu par défaut. Si vous souhaitez lire à partir d'un autre point de terminaison de diffusion en continu que celui par défaut, utilisez un autre lecteur, par exemple, [le lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

### Lecteur Azure Media Services

Utilisez le [lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html) pour lire votre contenu (protégé ou non) dans un des formats suivants :

- Smooth Streaming
- MPEG DASH
- HLS
- MP4 progressif


### Flash Player

#### Chiffrement AES avec jeton 

[http://aestoken.azurewebsites.net]("http://aestoken.azurewebsites.net)

### Lecteurs Silverlight

#### Surveillance

[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### PlayReady avec jeton

[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### Lecteurs DASH

[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### Autres

Pour tester les URL HLS, vous pouvez également utiliser :

- **Safari** sur un appareil iOS ou
- **3ivx HLS Player** sous Windows.

## Développement de lecteurs vidéo

Pour plus d'informations sur le développement de vos propres lecteurs, consultez la page [Développement de lecteurs vidéo](media-services-develop-video-players.md)
 
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png

<!--HONumber=52-->