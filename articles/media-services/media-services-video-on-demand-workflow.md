<properties 
	pageTitle="Diffusion multimédia à la demande avec Azure Media Services" 
	description="Cette rubrique traite des scénarios courants de diffusion multimédia à la demande avec Azure Media Services." 
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


#Diffusion multimédia à la demande avec Azure Media Services

##Vue d’ensemble

Cette rubrique décrit les étapes d’un workflow type de vidéo à la demande Azure Media Services (AMS). Chaque étape dirige vers des rubriques pertinentes. Pour les tâches pouvant être effectuées en utilisant différentes technologies, des boutons vous envoient à la technologie de votre choix (par exemple, .NET ou REST).

Notez que vous pouvez intégrer Media Services à vos outils et processus existants. Par exemple, encodez un contenu local, puis téléchargez-le dans Media Services pour un transcodage en différents formats et une diffusion via le réseau de distribution de contenu (CDN) Azure ou via un CDN tiers.

Le diagramme suivant présente les principaux composants de la plateforme Media Services impliqués dans le workflow de vidéo à la demande. ![Flux de travail VOD][vod-overview]

##<a id="vod_scenarios"></a>Scénarios courants : diffusion multimédia à la demande.

###Protéger le contenu stocké et assurer une diffusion multimédia en continu en clair (sans chiffrement)

1. Téléchargez un fichier mezzanine de haute qualité dans une ressource.
	
	Il est recommandé d’appliquer une option de chiffrement de stockage à votre ressource afin de protéger votre contenu lors du téléchargement et lorsqu’il est au repos, lors du stockage. 
1. Encodez-la sous forme de jeu de fichiers MP4 à débit adaptatif. 

	Il est recommandé d’appliquer une option de chiffrement de stockage à la ressource de sortie afin de protéger votre contenu lorsqu’il est au repos.
	
1. Configurez la stratégie de remise de ressources (utilisée par l’empaquetage dynamique).
	
	Si votre ressource est stockée sous forme chiffrée, vous **devez** configurer une stratégie de remise de ressources.

1. Publiez la ressource en créant un localisateur à la demande.

	Assurez-vous d’avoir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de diffuser votre contenu.

1. Diffusez le contenu publié.

###Protéger le contenu stocké et diffuser du contenu multimédia chiffré dynamiquement en continu  

Pour pouvoir utiliser le chiffrement dynamique, vous devez obtenir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de diffuser votre contenu chiffré.

1. Téléchargez un fichier mezzanine de haute qualité dans une ressource. Appliquez l’option de chiffrement de stockage à la ressource.
1. Encodez-la sous forme de jeu de fichiers MP4 à débit adaptatif. Appliquez l’option de chiffrement de stockage à la ressource de sortie.
1. Créez la clé de contenu de chiffrement pour la ressource que vous souhaitez chiffrer dynamiquement pendant la lecture.
2. Configurez la stratégie d’autorisation de clé de contenu.
1. Configurez la stratégie de remise de ressources (utilisée par l’empaquetage dynamique et le chiffrement dynamique).
1. Publiez la ressource en créant un localisateur à la demande.
1. Diffusez le contenu publié. 

###Indexer le contenu

1. Téléchargez un fichier mezzanine de haute qualité dans une ressource.
1. Indexez le contenu.

	La tâche d’indexation génère des fichiers qui peuvent être utilisés comme fichiers de sous-titres pour la lecture vidéo. Elle génère également des fichiers qui vous permettent d’effectuer une recherche dans la vidéo et d’accéder à un emplacement spécifique de la vidéo.

1. Consommez le contenu indexé.


###Remettre le téléchargement progressif 

1. Téléchargez un fichier mezzanine de haute qualité dans une ressource.
1. Encodez-la sous forme de jeu de fichiers MP4 à débit adaptatif ou de fichier MP4 unique.
1. Publiez la ressource en créant un localisateur à la demande ou de signature d’accès partagé (SAS, Shared Access Signature).

	Si vous utilisez un localisateur à la demande, assurez-vous d’avoir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de télécharger progressivement le contenu.

	Si vous utilisez un localisateur SAS, le contenu est téléchargé depuis le stockage d’objets blob Azure. Dans ce cas, il n’est pas nécessaire de disposer d’unités réservées de diffusion en continu.
  
1. Téléchargez le contenu de manière progressive.

Cet article contient des liens vers des rubriques expliquant comment configurer votre environnement de développement et effectuer les tâches mentionnées ci-dessus.


##Concepts

Pour connaître les concepts liés à la diffusion de votre contenu à la demande, consultez la rubrique [Concepts Media Services](media-services-concepts.md) (players.md).

##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=AcomDC_0211_2016-->