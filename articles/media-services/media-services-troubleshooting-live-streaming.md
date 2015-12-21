<properties 
	pageTitle="Guide de dépannage de la vidéo en flux continu" 
	description="Cette rubrique fournit des suggestions sur la façon de résoudre les problèmes de vidéo en flux continu." 
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
	ms.date="12/05/2015"    
	ms.author="juliako"/>

#Guide de dépannage de la vidéo en flux continu

Cette rubrique fournit des suggestions sur la façon de résoudre certains problèmes de vidéo en flux continu.

## Problèmes liés aux encodeurs locaux 

Cette section fournit des suggestions sur la façon de résoudre les problèmes liés aux encodeurs locaux qui sont configurés pour envoyer un flux à débit binaire unique à des canaux AMS activés pour l’encodage live.

###Problème : il n’existe aucune option pour générer un flux progressif

- **Problème potentiel** : l’encodeur utilisé n’effectue pas de désentrelacement automatique. 

	**Étapes de dépannage** : recherchez une option de désentrelacement dans l’interface de l’encodeur. Une fois le désentrelacement activé, revérifiez les paramètres de sortie progressive.
 
###Problème : vous avez essayé plusieurs paramètres de sortie d’encodeur et la connexion échoue encore. 

- **Problème potentiel** : le canal d’encodage Azure n’a pas été réinitialisé correctement. 

	**Étapes de dépannage** : assurez-vous que l’encodeur ne transmet plus les données à AMS, arrêtez puis réinitialisez le canal. Une fois le canal redémarré, essayez de connecter votre encodeur avec les nouveaux paramètres. Si cela ne résout toujours pas le problème, essayez de créer un canal. Parfois, les canaux peuvent être endommagés après plusieurs tentatives infructueuses.

- **Problème potentiel** : la taille de GOP ou les paramètres d’image clé ne sont pas optimaux.

	**Étapes de dépannage** : la taille de GOP ou l’intervalle d’image clé recommandé(e) est de deux secondes. Certains encodeurs calculent ce paramètre en nombre d’images, tandis que d’autres utilisent des secondes. Par exemple : lors de la sortie de 30 i/s, la taille de GOP serait de 60 images, ce qui équivaut à deux secondes.
	 
- **Problème potentiel** : des ports fermés bloquent le flux de données.

	**Étapes de dépannage** : lors de la diffusion en flux continu via RTMP, vérifiez les paramètres de pare-feu et/ou de proxy pour confirmer que les ports sortants 1935 et 1936 sont ouverts. Lorsque vous utilisez la diffusion en flux continu RTP, vérifiez que le port sortant 2010 est ouvert.


###Problème : lors de la configuration de l’encodeur pour la diffusion avec le protocole RTP, il n’y a aucun emplacement où entrer un nom d’hôte. 

- **Problème potentiel** : de nombreux encodeurs RTP n’autorisent pas les noms d’hôtes et une adresse IP doit être acquise.  

	**Étapes de dépannage** : pour trouver l’adresse IP, ouvrez une invite de commandes sur n’importe quel ordinateur. Pour ce faire, dans Windows, ouvrez le lanceur Exécuter (touche Windows + R) et tapez « cmd ».

	Une fois l’invite de commandes ouverte, tapez « Ping [nom\_hôte\_AMS] ».

	Vous pouvez obtenir le nom d’hôte en omettant le numéro de port de l’URL de réception Azure, comme illustré dans l’exemple suivant :

	rtp://test2-amstest009.rtp.channel.mediaservices.windows.net:2010/

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###Problème : impossible de lire le flux publié.
 
- **Problème potentiel** : aucun point de terminaison de diffusion en continu n’est en cours d’exécution ou aucune unité de diffusion en continu (unité d’échelle) n’a été allouée. 

	**Étapes de dépannage** : accédez à l’onglet « Point de terminaison de diffusion en continu » dans l’outil AMSE et vérifiez qu’un point de terminaison de diffusion en continu est en cours d’exécution avec une unité de diffusion en continu.
	
>[AZURE.NOTE]Si, après avoir suivi la procédure de dépannage, vous ne pouvez toujours pas diffuser en flux continu, envoyez un ticket de support à l’aide du portail Azure Classic.

##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1210_2015-->