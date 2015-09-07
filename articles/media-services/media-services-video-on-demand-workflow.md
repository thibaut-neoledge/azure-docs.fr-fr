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
	ms.date="08/11/2015"
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

Pour connaître les concepts liés à la diffusion de votre contenu à la demande, consultez la rubrique [Concepts Media Services](media-services-concepts.md).

##Tâches courantes : diffusion multimédia à la demande

###Création d’un compte Media Services

Utilisez le **portail de gestion Azure** pour la [création d’un compte Azure Media Services](media-services-create-account.md).

###Configuration d’un environnement de développement  

Choisissez **.NET** ou **API REST** comme environnement de développement.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

###Connexion par programme  

Choisissez **.NET** ou **API REST** pour vous connecter par programme à Azure Media Services.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]


###Configuration de points de terminaison de diffusion en continu

Pour une présentation des points de terminaison de diffusion en continu et pour obtenir des informations sur leur gestion, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-manage-origins.md).

###Téléchargement de médias 

Chargez vos fichiers à l’aide du **portail de gestion Azure**, de **.NET** ou de l’**API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

###Création de travaux et de tâches

Un travail est une entité qui contient des métadonnées sur un ensemble de tâches (par exemple, l’encodage ou l’indexation). Chaque tâche effectue une opération atomique sur la ou les ressources d’entrée. Pour obtenir un exemple sur la façon de créer des travaux d’encodage, consultez :

Pour une vue d’ensemble, consultez la rubrique [Utilisation des travaux Azure Media Services](media-services-jobs.md).

Procurez-vous un processeur multimédia adapté à votre tâche avec **.NET** ou l’**API REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

Les exemples suivants montrent comment créer des travaux d’encodage avec le **portail de gestion Azure**, **.NET** ou l’**API REST**.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Indexation

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

####Encodage 

**Vue d’ensemble** :

- [Vue d’ensemble de l’empaquetage dynamique](media-services-dynamic-packaging-overview.md)
- [Encodage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md)

Procédez à l’encodage à l’aide de l’**Encodeur multimédia Azure** en utilisant le **portail de gestion Azure**, **.NET** ou l’**API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

Encodage avancé avec **Media Encoder Premium Workflow** et **.NET**.

[AZURE.INCLUDE [media-services-selector-advanced-encoding](../../includes/media-services-selector-advanced-encoding.md)]

####Surveillance de la progression des travaux

Vous pouvez surveiller la progression des travaux à l’aide du **portail de gestion Azure**, de **.NET** ou de l’**API REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

###Protection du contenu 

**Vue d’ensemble** :

[Vue d’ensemble de la protection du contenu](media-services-content-protection-overview.md)

Si vous souhaitez chiffrer une ressource avec la norme AES (Advanced Encryption Standard) (à l’aide de clés de chiffrement 128 bits) ou avec PlayReady DRM, vous devez créer une clé de contenu.

Utilisez **.NET** ou l’**API REST** pour créer des clés.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

Une fois que vous avez créé la clé de contenu, vous pouvez configurer la stratégie d’autorisation des clés à l’aide de **.NET** ou de l’**API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


Configurez la stratégie de remise de ressources à l’aide de **.NET** ou de l’**API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]


####Intégration avec des partenaires

[Utilisation de castLabs pour fournir des licences DRM à Azure Media Services](media-services-castlabs-integration.md)

###Publication et distribution de ressources

Vue d’ensemble de l’empaquetage dynamique

> [AZURE.SELECTOR]
- [Overview](media-services-dynamic-packaging-overview.md)


Vue d’ensemble de la distribution de contenu

> [AZURE.SELECTOR]
- [Overview](media-services-deliver-content-overview.md)

Publiez des ressources (en créant des localisateurs) à l’aide du **portail de gestion Azure**, de **.NET** ou des **API REST**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]

###Activation du CDN Azure

Media Services prend en charge l’intégration avec le CDN d’Azure. Pour plus d’informations sur l’activation du CDN Azure, voir [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-manage-origins.md#enable_cdn).

###Mise à l’échelle d’un compte Media Services

Vous pouvez mettre à l’échelle **Media Services** en spécifiant le nombre d’**unités réservées de diffusion en continu** et d’**unités réservées d’encodage** que vous voulez attribuer à votre compte.

Vous pouvez aussi mettre à l’échelle votre compte Media Services en lui ajoutant des comptes de stockage. Chaque compte de stockage est limité à 500 To. Pour développer votre capacité stockage au-delà des limites par défaut, vous pouvez choisir de rattacher plusieurs comptes de stockage à un même compte Media Services.

[Cette](media-services-how-to-scale.md) rubrique offre des liens vers des rubriques connexes.

###Lecture de votre contenu avec les lecteurs existants

Pour plus d’informations, consultez [Lecture de votre contenu avec les lecteurs existants](media-services-playback-content-with-existing-players.md).


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=August15_HO9-->