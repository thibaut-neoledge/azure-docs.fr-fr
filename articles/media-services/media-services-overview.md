<properties 
	pageTitle="Vue d’ensemble d’Azure Media Services et scénarios courants" 
	description="Cette rubrique offre une vue d'ensemble d'Azure Media Services" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015"
	ms.author="juliako"/>

#Vue d’ensemble d’Azure Media Services et scénarios courants

Microsoft Azure Media Services est une plateforme extensible basée sur le cloud qui permet aux développeurs de créer des applications évolutives de gestion et de diffusion de médias. Media Services est basé sur les API REST qui permettent de télécharger, stocker, encoder et empaqueter en toute sécurité du contenu vidéo ou audio destiné à être diffusé à la demande ou en direct sur différents clients (par exemple, téléviseurs, PC et appareils mobiles).

Vous pouvez créer des flux de travail de bout en bout en utilisant uniquement Media Services. Vous pouvez aussi choisir d'utiliser des composants tiers pour certaines parties de votre flux de travail (par exemple, en encodant avec un encodeur tiers). Le contenu est ensuite téléchargé, protégé, empaqueté et remis à l'aide de Media Services.

Vous pouvez choisir de diffuser votre contenu en direct ou de distribuer du contenu à la demande. Cette rubrique décrit les scénarios courants pour distribuer vos contenus [en direct](media-services-overview.md#live_scenarios) ou [à la demande](media-services-overview.md#vod_scenarios). La rubrique contient également des liens vers d’autres rubriques pertinentes.

## Kits de développement logiciel (SDK) et outils 

Pour créer des solutions Media Services, vous pouvez utiliser les composants suivants :

- [API REST Media Services](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Un des SDK clients disponibles : [SDK Azure Media Services pour .NET](https://github.com/Azure/azure-sdk-for-media-services), [SDK Azure pour Java](https://github.com/Azure/azure-sdk-for-java), [Azure Media Services pour Node.js](https://github.com/fritzy/node-azure-media), [SDK Azure PHP](https://github.com/Azure/azure-sdk-for-php)
- Outils existants : [Portail de gestion Azure](http://manage.windowsazure.com/) ou [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).


##Parcours d’apprentissage de Media Services

Vous pouvez afficher les parcours d’apprentissage d’AMS ici :

- [Workflow de vidéo en flux continu AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Workflow de streaming à la demande AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Affiche


Vous pouvez [ici](http://azure.microsoft.com/documentation/infographics/media-services/) consulter l'affiche Azure Media Services qui présente les flux de travail AMS, de la création des médias à leur consommation.

##Composants requis

Pour commencer à utiliser Azure Media Services, vous devez disposer des éléments suivants :
 
3. Un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](azure.microsoft.com).
2. Un compte Azure Media Services. Utilisez le portail de gestion Azure, .NET ou l’API REST pour créer un compte Azure Media Services. Pour plus d’informations, consultez [Créer un compte](media-services-create-account.md).
3. (Facultatif) Un environnement de développement configuré. Choisissez .NET ou API REST comme environnement de développement. Pour plus d’informations, consultez [Configuration d'environnement](media-services-dotnet-how-to-use.md). 

	En outre, découvrez comment [vous connecter](media-services-dotnet-connect_programmatically.md) par programmation.
4. (Recommandé) Allocation d’une ou de plusieurs unités d’échelle. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Pour plus d’informations, consultez [Gestion des points de terminaison de diffusion en continu](media-services-manage-origins.md).

##Concepts

Pour plus d’informations, consultez [Concepts](media-services-concepts.md).


##<a id="vod_scenarios"></a>Diffusion multimédia à la demande avec Azure Media Services : tâches et scénarios courants

Cette section décrit les scénarios courants et fournit des liens vers des rubriques pertinentes. Le diagramme suivant présente les principaux composants de la plateforme Media Services impliqués dans la distribution de contenu à la demande.

![Flux de travail VOD][vod-overview]


###Protéger le contenu stocké et assurer une diffusion multimédia en continu en clair (sans chiffrement)

1. Téléchargez un fichier mezzanine de haute qualité dans une ressource.
	
	Il est recommandé d’appliquer une option de chiffrement de stockage à votre ressource afin de protéger votre contenu lors du téléchargement et lorsqu’il est au repos, lors du stockage.
 
1. Encoder en un ensemble de fichiers MP4 à débit adaptatif.

	Il est recommandé d’appliquer une option de chiffrement de stockage à la ressource de sortie afin de protéger votre contenu lorsqu’il est au repos.
	
1. Configurez la stratégie de remise de ressources (utilisée par l’empaquetage dynamique).
	
	Si votre ressource est stockée sous forme chiffrée, vous **devez** configurer une stratégie de remise de ressources.

1. Publiez la ressource en créant un localisateur à la demande.

	Assurez-vous d’avoir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de diffuser votre contenu.

1. Diffusez le contenu publié.

###Protéger le contenu stocké et diffuser du contenu multimédia chiffré dynamiquement en continu  

Pour pouvoir utiliser le chiffrement dynamique, vous devez obtenir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de diffuser votre contenu chiffré.

1. Téléchargez un fichier mezzanine de haute qualité dans une ressource. Appliquez l’option de chiffrement de stockage à la ressource.
1. Encoder en un ensemble de fichiers MP4 à débit adaptatif. Appliquez l’option de chiffrement de stockage à la ressource de sortie.
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
1. Encoder en un fichier MP4 unique.
1. Publiez la ressource en créant un localisateur à la demande ou de signature d’accès partagé (SAS, Shared Access Signature).

	Si vous utilisez un localisateur à la demande, assurez-vous d’avoir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de télécharger progressivement le contenu.

	Si vous utilisez un localisateur SAS, le contenu est téléchargé depuis le stockage d’objets blob Azure. Dans ce cas, il n’est pas nécessaire de disposer d’unités réservées de diffusion en continu.
  
1. Téléchargez le contenu de manière progressive.

###Voir aussi

- [Téléchargement de contenu](media-services-manage-content.md#upload)
- [Obtention d’un processeur multimédia](media-services-get-media-processor.md)
- [Encodage de contenu](media-services-manage-content.md#encode)
- [Surveillance de tâches](media-services-portal-check-job-progress.md)
- [Indexation de contenu](media-services-manage-content.md#index)
- [Protection de contenu](media-services-manage-content.md#encrypt)
- [Protection de publication](media-services-manage-content.md#publish)
- [Mise à l’échelle de l’encodage](media-services-portal-encoding-units.md)

##<a id="live_scenarios"></a>Diffusion d’événements en direct en continu avec Azure Media Services

Lorsque vous utilisez la vidéo en flux continu, les composants suivants sont généralement impliqués :

- Une caméra utilisée pour diffuser un événement.
- Un encodeur vidéo dynamique qui convertit les signaux de la caméra en flux de données qui sont envoyés vers un service de vidéo en flux continu. 
  
	Éventuellement, plusieurs encodeurs dynamiques. Pour certains événements en direct critiques qui exigent une disponibilité et une qualité d’expérience très élevées, il est recommandé d’utiliser des encodeurs redondants en mode actif-actif pour obtenir un basculement transparent sans perte de données.
- Service de vidéo en flux continu qui vous permet d’effectuer les opérations suivantes : 
	- Recevoir du contenu en direct à l’aide de différents protocoles de diffusion de vidéo en flux continu (par exemple RTMP ou Smooth Streaming), 
	- Encoder votre flux en flux à débit adaptatif
	- Afficher un aperçu de votre flux en direct
	- Stocker le contenu reçu pour le diffuser ultérieurement (vidéo à la demande)
	- Fournir le contenu via des protocoles de diffusion communs (par exemple, MPEG DASH, Smooth, TLS, HDS) directement à vos clients ou à un réseau de distribution de contenu (CDN) pour une distribution supplémentaire 
	
		
**Microsoft Azure Media Services** (AMS) offre la possibilité de recevoir, d’encoder, d’afficher, de stocker et de distribuer votre contenu vidéo en flux continu.

Quand vous distribuez votre contenu aux clients, votre objectif est de distribuer une vidéo de haute qualité à divers appareils dans des conditions de réseau différentes. Pour prendre en charge les conditions de qualité et de réseau, utilisez des encodeurs dynamiques pour encoder votre flux dans un flux vidéo à débit binaire multiple (débit binaire adaptatif). Pour prendre en charge la diffusion en continu sur différents appareils, utilisez l’[empaquetage dynamique](media-services-dynamic-packaging-overview.md) Media Services pour empaqueter de manière dynamique votre flux dans différents protocoles. Media Services prend en charge la distribution des technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement).

Dans Azure Media Sercices, les **canaux**, les **programmes** et le **point de terminaison de diffusion en continu** gèrent toutes les fonctionnalités vidéo en flux continu, notamment la réception, le formatage, le DVR, la sécurité, l’extensibilité et la redondance.

Un **canal** représente un pipeline de traitement du contenu vidéo en flux continu. Actuellement, un canal peut recevoir des flux d’entrée dynamiques de l’une des manières suivantes :


- Un encodeur dynamique envoie un flux à vitesse de transmission unique vers le canal activé pour effectuer un encodage en direct avec Media Services dans l’un des formats suivants : RTP (MPEG-TS), RTMP ou Smooth Streaming (MP4 fragmenté). Le canal procède ensuite à l’encodage en temps réel du flux à débit binaire unique entrant en flux vidéo à débit binaire multiple (adaptatif). Lorsqu’il y est invité, Media Services fournit le flux aux clients.

	L’encodage d’un flux en direct avec Media Services est en **version préliminaire**.
- Un encodeur en direct local envoie au canal un paquet **RTMP** ou **Smooth Streaming** (MP4 fragmenté) à débit binaire multiple. Vous pouvez utiliser les encodeurs dynamiques suivants qui produisent un flux Smooth Streaming à débit binaire multiple : Elemental, Envivio, Cisco. Les encodeurs dynamiques suivants produisent un flux au format RTMP : Adobe Flash Live, Telestream Wirecast et transcodeurs Tricaster. Les flux reçus transitent par les **canaux** sans traitement supplémentaire. Votre encodeur dynamique peut également envoyer un flux à débit binaire unique vers un canal qui n’est pas activé pour le codage en direct, mais ce n’est pas recommandé. Lorsqu’il y est invité, Media Services fournit le flux aux clients.


###Utilisation de canaux activés pour effectuer un encodage en temps réel avec Azure Media Services


Le schéma suivant illustre les principales parties de la plateforme AMS impliquées dans le flux de travail de vidéo en flux continu où un canal est activé pour effectuer un encodage live avec Media Services.

![Flux de travail en direct][live-overview1]

Pour plus d’informations, consultez [Utilisation de canaux activés pour effectuer un encodage en temps réel avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).


###Utilisation des canaux recevant un flux dynamique à débit binaire multiple provenant d’encodeurs locaux


Le diagramme suivant présente les principaux composants de la plateforme AMS impliqués dans ce flux de travail de vidéo en flux continu.

![Flux de travail en direct][live-overview2]

Pour plus d’informations, consultez [Utilisation des canaux recevant un flux dynamique à débit binaire multiple provenant d’encodeurs locaux](media-services-manage-channels-overview.md).

##Utilisation de contenu

Azure Media Services fournit les outils dont vous avez besoin pour créer des applications de lecteur clientes riches et dynamiques pour la plupart des plateformes, notamment : appareils iOS, Android, Windows, Windows Phone, Xbox et décodeurs. La rubrique suivante fournit des liens vers les Kits de développement logiciel (SDK) et les infrastructures de lecteur que vous pouvez utiliser pour développer vos propres applications clientes pour utiliser la diffusion en continu de médias à partir de Media Services.

[Développement d'applications de lecteur vidéo](media-services-develop-video-players.md)

##Activation du CDN Azure

Media Services prend en charge l’intégration avec le CDN d’Azure. Pour plus d’informations sur l’activation du CDN Azure, voir [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-manage-origins.md#enable_cdn).

##Mise à l’échelle d’un compte Media Services

Vous pouvez mettre à l’échelle **Media Services** en spécifiant le nombre d’**unités réservées de diffusion en continu** et d’**unités réservées d’encodage** que vous voulez attribuer à votre compte.

Vous pouvez aussi mettre à l’échelle votre compte Media Services en lui ajoutant des comptes de stockage. Chaque compte de stockage est limité à 500 To. Pour développer votre capacité stockage au-delà des limites par défaut, vous pouvez choisir de rattacher plusieurs comptes de stockage à un même compte Media Services.

[Cette](media-services-how-to-scale.md) rubrique offre des liens vers des rubriques connexes.

##Support

Le [support Azure](http://azure.microsoft.com/support/options/) propose des options de support pour Azure, y compris Media Services.

##Modèles et pratiques recommandées

[Modèles et pratiques recommandées](https://wamsg.codeplex.com/) [Documentation en ligne](https://msdn.microsoft.com/library/dn735912.aspx) [Livre électronique téléchargeable](https://www.microsoft.com/download/details.aspx?id=42629)


##Contrat de Niveau de Service (SLA)

- Pour Media Services, nous garantissons une disponibilité de 99,9 % des transactions d'API REST.
- Pour la diffusion en continu, nous traiterons avec succès les demandes de service avec une garantie de disponibilité de 99,9 % pour le contenu multimédia existant quand au moins une unité de diffusion en continu est achetée.
- Pour les Canaux en Direct, nous garantissons que les canaux en cours d’exécution auront une connectivité externe au moins 99,9 % du temps.
- Pour la Protection de Contenu, nous garantissons que nous serons en mesure de traiter les demandes de clé au moins 99,9 % du temps.
- Pour l’Indexeur, nous serons en mesure d’assurer les demandes de tâche d’indexation traitées avec une unité réservée d’encodage 99,9 % du temps.

	Pour plus d’informations, consultez le [contrat SLA Microsoft Azure](http://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=Oct15_HO4-->