---
title: "Scénarios Microsoft Azure Media Services et disponibilité des fonctionnalités dans les centres de données | Microsoft Docs"
description: "Cette rubrique offre une vue d’ensemble des scénarios Microsoft Azure Media Services et de la disponibilité des fonctionnalités et services dans les centres de données."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/10/2017
ms.author: juliako;anilmur
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 5cdfd4dd90f5073d03071ef77c062d450210f5ea
ms.contentlocale: fr-fr
ms.lasthandoff: 07/12/2017

---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Scénarios et disponibilité des fonctionnalités Media Services dans les centres de données

Microsoft Azure Media Services (AMS) vous permet de charger, de stocker, d’encoder et d’empaqueter en toute sécurité du contenu vidéo ou audio destiné à être diffusé à la demande ou en streaming en direct sur différents clients (par exemple, téléviseurs, PC et appareils mobiles).

AMS fonctionne dans plusieurs centres de données à travers le monde. Ces centres de données sont regroupés en régions géographiques, ce qui vous donne une certaine flexibilité dans le choix de l’emplacement où vous créez vos applications. Vous pouvez consulter la [liste de ces régions et leurs emplacements](https://azure.microsoft.com/regions/). 

Cette rubrique décrit les scénarios courants pour distribuer votre contenu [en direct](#live_scenarios) ou [à la demande](#vod_scenarios). Cette rubrique présente également des informations sur la disponibilité des fonctionnalités et services Media Services dans les centres de données.

## <a name="overview"></a>Vue d'ensemble

### <a name="prerequisites"></a>Composants requis

Pour commencer à utiliser Azure Media Services, vous devez disposer des éléments suivants :

* Un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com).
* Un compte Azure Media Services. Pour plus d’informations, consultez [Créer un compte](media-services-portal-create-account.md).
* Le point de terminaison à partir duquel vous souhaitez diffuser du contenu doit se trouver dans l’état **En cours d’exécution**.

    Une fois votre compte AMS créé, un point de terminaison de streaming **par défaut** est ajouté à votre compte à l’état **Arrêté**. Pour démarrer le streaming de votre contenu et tirer parti de l’empaquetage et du chiffrement dynamiques, le point de terminaison de streaming doit se trouver à l’état **En cours d’exécution**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Objets couramment utilisés lors du développement par rapport au modèle AMS OData

L’image suivante illustre certains des objets couramment utilisés lors du développement par rapport au modèle OData de Media Services.

Cliquez sur l’image pour l’afficher en plein écran.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

Vous pouvez afficher l’ensemble du modèle [ici](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Protéger le contenu stocké et assurer une diffusion multimédia en continu en clair (sans chiffrement)

![Flux de travail VOD](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Chargez un fichier multimédia de haute qualité dans une ressource.

    Il est recommandé d’appliquer une option de chiffrement de stockage à votre ressource afin de protéger votre contenu lors du téléchargement et lorsqu’il est au repos, lors du stockage.
2. Encoder en un ensemble de fichiers MP4 à débit adaptatif.

    Il est recommandé d’appliquer une option de chiffrement de stockage à la ressource de sortie afin de protéger votre contenu lorsqu’il est au repos.
3. Configurez la stratégie de remise de ressources (utilisée par l’empaquetage dynamique).

    Si votre ressource est stockée sous forme chiffrée, vous **devez** configurer une stratégie de remise de ressources.
4. Publiez la ressource en créant un localisateur à la demande.
5. Diffusez le contenu publié.

Pour plus d’informations sur la disponibilité dans les centres de données, consultez la section [Disponibilité](#availability).

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Protéger le contenu stocké et diffuser du contenu multimédia chiffré dynamiquement en continu

![Protéger avec PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Chargez un fichier multimédia de haute qualité dans une ressource. Appliquez l’option de chiffrement de stockage à la ressource.
2. Encoder en un ensemble de fichiers MP4 à débit adaptatif. Appliquez l’option de chiffrement de stockage à la ressource de sortie.
3. Créez la clé de contenu de chiffrement pour la ressource que vous souhaitez chiffrer dynamiquement pendant la lecture.
4. Configurez la stratégie d’autorisation de clé de contenu.
5. Configurez la stratégie de remise de ressources (utilisée par l’empaquetage dynamique et le chiffrement dynamique).
6. Publiez la ressource en créant un localisateur à la demande.
7. Diffusez le contenu publié.

Pour plus d’informations sur la disponibilité dans les centres de données, consultez la section [Disponibilité](#availability).

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Utiliser Media Analytics pour extraire des connaissances exploitables de vos vidéos

Media Analytics est une collection de composants visuels et vocaux qui aident les organisations et les entreprises à extraire des connaissances exploitables de leurs fichiers vidéo. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Media Analytics](media-services-analytics-overview.md).

1. Chargez un fichier multimédia de haute qualité dans une ressource.
2. Traitez vos vidéos avec l’un des services Media Analytics décrits dans la section [Vue d’ensemble de Media Analytics](media-services-analytics-overview.md).
3. Les processeurs multimédias Media Analytics créent des fichiers MP4 ou JSON. Si un processeur multimédia a produit un fichier MP4, vous pouvez télécharger ce dernier progressivement. Si un processeur multimédia a produit un fichier JSON, vous pouvez télécharger ce dernier à partir d’Azure Blob Storage.

Pour plus d’informations sur la disponibilité dans les centres de données, consultez la section [Disponibilité](#availability).

## <a name="deliver-progressive-download"></a>Remettre le téléchargement progressif

1. Chargez un fichier multimédia de haute qualité dans une ressource.
2. Encoder en un fichier MP4 unique.
3. Publiez la ressource en créant un localisateur à la demande ou de signature d’accès partagé (SAS, Shared Access Signature).

    Si vous utilisez un localisateur SAS, le contenu est téléchargé depuis le stockage d’objets blob Azure. Dans ce cas, il n’est pas nécessaire de disposer de points de terminaison de streaming à l’état Démarré.
4. Téléchargez le contenu de manière progressive.

## <a id="live_scenarios"></a>Diffusion d’événements en streaming en direct 

1. Ingérer du contenu en direct à l’aide de différents protocoles de streaming en direct (par exemple RTMP ou Smooth Streaming)
2. Encoder votre flux en flux à débit adaptatif (facultatif)
3. Afficher un aperçu de votre flux en direct
4. Fournir le contenu par le biais des protocoles de streaming courants (par exemple, MPEG DASH, Smooth, HLS) directement à vos clients ou à un réseau de distribution de contenu (CDN) pour une distribution supplémentaire.

    -ou-

    Enregistrer et stocker le contenu ingéré pour le diffuser ultérieurement (vidéo à la demande)

Lors d’un streaming en direct, vous pouvez choisir l’une des deux options suivantes :

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Utilisation des canaux recevant un flux dynamique à débit binaire multiple provenant d’encodeurs locaux (méthode directe)

Le diagramme suivant présente les principaux composants de la plateforme AMS impliqués dans ce flux de travail de **méthode directe** .

![Flux de travail live](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Pour plus d’informations, consultez [Utilisation des canaux recevant un flux dynamique à débit binaire multiple provenant d’encodeurs locaux](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Utilisation de canaux activés pour effectuer un encodage en temps réel avec Azure Media Services

Le schéma suivant illustre les principales parties de la plateforme AMS impliquées dans le flux de travail de vidéo en flux continu où un canal est activé pour effectuer un encodage live avec Media Services.

![Flux de travail live](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Pour plus d’informations, consultez [Utilisation de canaux activés pour effectuer un encodage en direct avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Pour plus d’informations sur la disponibilité dans les centres de données, consultez la section [Disponibilité](#availability).

## <a name="consuming-content"></a>Utilisation de contenu

Azure Media Services fournit les outils dont vous avez besoin pour créer des applications de lecteur clientes riches et dynamiques pour la plupart des plateformes, notamment : appareils iOS, Android, Windows, Windows Phone, Xbox et décodeurs. La rubrique suivante fournit des liens vers les Kits de développement logiciel (SDK) et les infrastructures de lecteur que vous pouvez utiliser pour développer vos propres applications clientes pour utiliser la diffusion en continu de médias à partir de Media Services. Pour plus d’informations, consultez [Développement d’applications de lecteur vidéo](media-services-develop-video-players.md).

## <a name="enabling-azure-cdn"></a>Activation du CDN Azure

Media Services prend en charge l’intégration avec le CDN d’Azure. Pour plus d’informations sur l’activation du CDN Azure, voir [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-portal-manage-streaming-endpoints.md).

## <a id="scaling"></a>Scalabilité d’un compte Media Services

Les clients AMS peuvent mettre à l’échelle les points de terminaison de streaming, le traitement multimédia et le stockage dans leurs comptes AMS.

* En fonction de leurs besoins, les clients Media Services peuvent choisir un point de terminaison de streaming **Standard** ou **Premium**. Le point de terminaison de streaming **Standard** convient à la plupart des charges de travail de streaming. Il inclut les mêmes fonctionnalités que les points de terminaison de streaming **Premium** et met automatiquement à l’échelle la bande passante sortante. 

    Les points de terminaison de streaming **Premium** sont conçus pour les charges de travail avancées et fournissent une capacité de bande passante dédiée et scalable. Les clients disposant d’un point de terminaison de streaming **Premium** ont par défaut une unité de streaming. Le point de terminaison de streaming peut être adapté avec l’ajout d’unités de streaming. Chaque unité de streaming fournit une capacité de bande passante supplémentaire à l’application. Pour plus d’informations sur la scalabilité des points de terminaison de streaming **Premium**, consultez la rubrique [Mettre à l’échelle des points de terminaison de streaming](media-services-portal-scale-streaming-endpoints.md).

* Un compte Media Services est associé à un Type d'unité réservé qui détermine la vitesse à laquelle vos tâches de traitement multimédia sont traitées. Vous pouvez choisir entre les types d’unités réservées suivantes : **S1**, **S2** ou **S3**. Par exemple, un même travail d’encodage s’exécute plus rapidement quand vous utilisez le type d’unité réservée **S2** que le type **S1**.

    En plus de spécifier le type d’unité réservée, vous pouvez approvisionner votre compte avec des **unités réservées**. Le nombre d’unités réservées approvisionnées détermine le nombre de tâches multimédias qui peuvent être traitées simultanément dans un compte donné.

    >[!NOTE]
    >Les unités réservées fonctionnent pour la mise en parallèle de tout le traitement multimédia, notamment les travaux d’indexation qui utilisent Azure Media Indexer. Toutefois, contrairement à l’encodage, l’indexation des travaux n’est pas plus rapide avec des unités réservées plus rapides.

    Pour plus d’informations, consultez [Mise à l’échelle du traitement multimédia](media-services-portal-scale-media-processing.md).
* Vous pouvez aussi mettre à l’échelle votre compte Media Services en lui ajoutant des comptes de stockage. Chaque compte de stockage est limité à 500 To. Pour développer votre capacité stockage au-delà des limites par défaut, vous pouvez choisir de rattacher plusieurs comptes de stockage à un même compte Media Services. Pour plus d’informations, consultez [Gérer les comptes de stockage](meda-services-managing-multiple-storage-accounts.md).

##<a id="availability"></a> Disponibilité des fonctionnalités Media Services dans les centres de données

Cette section présente des informations sur la disponibilité des fonctionnalités Media Services dans les centres de données.

### <a name="ams-accounts"></a>Comptes AMS

#### <a name="availability"></a>Availability

Vous pouvez créer des comptes Media Services dans les régions suivantes : Europe du Nord, Europe de l’Ouest, Ouest des États-Unis, Est des États-Unis, Sud-Est de l’Asie, Est de l’Asie, Japon de l’Ouest, Japon de l’Est, Sud du Brésil, Inde-Ouest, Inde-Sud et Inde-Centre. 

### <a name="streaming-endpoints"></a>Points de terminaison de streaming 

En fonction de leurs besoins, les clients Media Services peuvent choisir un point de terminaison de streaming **Standard** ou **Premium**. Pour plus d’informations, consultez la section sur la [mise à l’échelle](#scaling).

#### <a name="availability"></a>Availability

|Nom|État|Centres de données
|---|---|---|
|Standard|GA|Tout|
|Premium|GA|Tout|

### <a name="live-encoding"></a>Live Encoding

#### <a name="availability"></a>Availability

Disponible dans tous les centres de données, à l’exception des régions suivantes : Allemagne, Sud du Brésil, Inde-Ouest, Inde-Sud et Inde-Centre. 

### <a name="encoding-media-processors"></a>Processeurs multimédia d’encodage

AMS offre deux encodeurs à la demande : **Media Encoder Standard** et **Media Encoder Premium Workflow**. Pour plus d’informations, consultez [Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure](media-services-encode-asset.md). 

#### <a name="availability"></a>Availability

|Nom du processeur multimédia|État|Centres de données
|---|---|---|
|Media Encoder Standard|GA|Tout|
|Media Encoder Premium Workflow|GA|Tout sauf la Chine|

### <a name="analytics-media-processors"></a>Processeurs multimédias Analytics

Media Analytics est une collection de composants visuels et vocaux qui aident les organisations et les entreprises à extraire des connaissances exploitables de leurs fichiers vidéo. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Media Analytics](media-services-analytics-overview.md).

#### <a name="availability"></a>Availability

|Nom du processeur multimédia|État|Centres de données
|---|---|---|
|Détecteur de visage Azure Media|VERSION PRÉLIMINAIRE|Tout|
|Azure Media Hyperlapse|VERSION PRÉLIMINAIRE|Tout|
|Azure Media Indexer|GA|Tout|
|Détecteur de mouvement Azure Media|VERSION PRÉLIMINAIRE|Tout|
|Azure Media OCR|VERSION PRÉLIMINAIRE|Tout|
|Azure Media Redactor|VERSION PRÉLIMINAIRE|Tout|
|Azure Media Stabilizer|VERSION PRÉLIMINAIRE|Tout|
|Miniatures vidéo Azure Media|VERSION PRÉLIMINAIRE|Tout|
|Azure Media Indexer 2|VERSION PRÉLIMINAIRE|Tout sauf la région de Chine et du gouvernement fédéral|

### <a name="protection"></a>Protection

Microsoft Azure Media Services vous permet de sécuriser votre contenu multimédia dès lors qu’il quitte votre ordinateur via le stockage, le traitement et la remise. Pour plus d’informations, consultez [Protection du contenu AMS](media-services-content-protection-overview.md).

#### <a name="availability"></a>Availability

|Chiffrement|État|Centres de données|
|---|---|---| 
|Storage|GA|Tout|
|Clés AES-128|GA|Tout|
|Fairplay|GA|Tout|
|PlayReady|GA|Tout|
|Widevine|GA|Tous, sauf les régions Allemagne, Gouvernement fédéral et Chine.

### <a name="reserved-units-rus"></a>Unités réservées

Le nombre d’unités réservées approvisionnées détermine le nombre de tâches de média qui peuvent être traitées simultanément dans un compte donné. 

Pour plus d’informations, consultez la section sur la [mise à l’échelle](#scaling).

#### <a name="availability"></a>Availability

Disponible dans tous les centres de données.

### <a name="reserved-unit-ru-type"></a>Type d’unité réservée

Un compte Media Services est associé à un type d’unité réservée qui détermine la vitesse à laquelle vos tâches de traitement multimédia sont traitées. Vous pouvez choisir entre les types d’unités réservées suivants : S1, S2 ou S3.

Pour plus d’informations, consultez la section sur la [mise à l’échelle](#scaling).

#### <a name="availability"></a>Availability

|Nom du type d’unité réservée|État|Centres de données
|---|---|---|
|S1|GA|Tout|
|S2|GA|Tous, à l’exception des régions Sud du Brésil et Inde-Ouest|
|S3|GA|Tous, à l’exception de la région Inde-Ouest|

## <a name="next-steps"></a>Étapes suivantes

Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


