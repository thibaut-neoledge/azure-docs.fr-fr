<properties 
	pageTitle="Vue d'ensemble de la distribution de contenu aux clients" 
	description="Cette rubrique donne une vue d'ensemble de ce qu'implique la distribution de votre contenu avec Azure Media Services." 
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


# Vue d'ensemble de la distribution de contenu aux clients

## Vue d'ensemble

Lorsque vous travaillez avec Media Services, un des scénarios les plus courants est :

1. Charger un fichier d'entrée (appelé fichier mezzanine) dans une ressource. Par exemple, H.264, MP4 ou WMV.
1. Encoder la ressource en un ensemble de fichiers MP4 à débit adaptatif.
1. Publier la ressource. 
2. Utiliser l'[empaquetage dynamique](http://msdn.microsoft.com/library/azure/jj889436.aspx) pour distribuer le contenu à vos clients dans un des formats suivants : MPEG DASH, Apple HLS ou Smooth Streaming. 

Cette rubrique offre une vue d'ensemble des [concepts](media-services-deliver-content.md#concepts) principaux, ainsi que des liens vers des rubriques expliquant comment effectuer les [tâches liées à la distribution de contenu](media-services-deliver-content.md#tasks).

## <a id="concepts"></a>Concepts

La liste suivante décrit les termes et concepts utiles dans le cadre de la distribution de médias.

### Localisateurs

Pour fournir aux utilisateurs une URL pouvant être utilisée pour diffuser en continu ou télécharger votre contenu, vous devez d'abord " publier " votre élément multimédia en créant un localisateur.  Les localisateurs fournissent un point d'entrée pour accéder aux fichiers contenus dans une ressource. Media Services prend en charge deux types de localisateurs : 

- les localisateurs **OnDemandOrigin**, utilisés pour diffuser du contenu multimédia (par exemple, MPEG DASH, HLS ou Smooth Streaming) ou télécharger progressivement des fichiers ;
-  les localisateurs d'URL **SAP** (signature d'accès partagé), utilisés pour télécharger des fichiers multimédias sur un ordinateur local.

Une **stratégie d'accès** est utilisée pour définir les autorisations (écriture, lecture, énumération, etc.) et la durée pendant laquelle un client a accès à une ressource donnée. Notez que l'autorisation de liste (AccessPermissions.List) ne doit pas être utilisée lors de la création d'un localisateur OrDemandOrigin.

Les localisateurs ont une date d'expiration. Lorsque vous utilisez le portail pour publier vos ressources, des localisateurs présentant une date d'expiration de 100 ans sont créés. 

>[AZURE.NOTE] Si vous avez utilisé le portail pour créer des localisateurs avant mars 2015, des localisateurs présentant une date d'expiration de deux ans ont été créés.  

Pour mettre à jour la date d'expiration d'un localisateur, utilisez les API [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Notez que lorsque vous mettez à jour la date d'expiration d'un localisateur SAP, l'URL est modifiée. 
 
Les localisateurs ne sont pas conçus pour gérer le contrôle d'accès par utilisateur. Pour accorder différents droits d'accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM). Pour plus d'informations, consultez la page [Sécurisation des médias](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Notez que lorsque vous créez un localisateur, il peut y avoir un délai de 30 secondes dû au processus de stockage et de propagation requis dans Azure Storage.


### Diffusion en continu adaptative 

Les technologies à débit adaptatif permettent aux applications de lecteur vidéo de déterminer les conditions réseau et de choisir entre plusieurs débits. Si la communication réseau se dégrade, le client peut sélectionner un débit inférieur, ce qui permet au lecteur de continuer à lire la vidéo, au détriment cependant de la qualité vidéo. Dès que les conditions réseau s'améliorent, le client peut passer à un débit binaire supérieur pour une meilleure qualité vidéo. Azure Media Services prend en charge les technologies à débit adaptatif suivantes : diffusion en continu HTTP(HLS), diffusion en continu lisse (Smooth Streaming), MPEG DASH et HDS.

Pour fournir aux utilisateurs des URL de diffusion en continu, vous devez d'abord créer un localisateur OnDemandOrigin. La création du localisateur vous donne le chemin d'accès de base à l'élément multimédia qui contient le contenu que vous souhaitez diffuser. Toutefois, pour pouvoir diffuser ce contenu vous devez modifier ce chemin d'accès. Pour construire une URL complète vers le fichier manifeste de diffusion en continu, vous devez concaténer la valeur de chemin d'accès du localisateur et le nom du fichier manifeste (nom_fichier.ISM). Ensuite, ajoutez /Manifest et un format approprié (si nécessaire) au chemin d'accès du localisateur. 

Vous pouvez aussi diffuser votre contenu via une connexion SSL. Pour ce faire, assurez-vous que votre URL de diffusion commence par HTTPS. 

Notez que vous pouvez uniquement transmettre en continu via le protocole SSL si le point de terminaison à partir duquel vous distribuez votre contenu a été créé après le 10 septembre 2014. Si vos URL de diffusion sont basées sur des points de terminaison créés après le 10 septembre, l'URL contient " streaming.mediaservices.windows.net " (le nouveau format). Les URL de diffusion qui contiennent " origin.mediaservices.windows.net " (ancien format) ne sont pas compatibles avec le protocole SSL. Si votre URL suit l'ancien format et que vous souhaitez être en mesure de diffuser via le protocole SSL, créez un point de terminaison. L'utilisation d'URL créées à partir du nouveau point de terminaison permet de diffuser votre contenu via le protocole SSL. 


#### Formats d'URL de diffusion en continu :

**Format Smooth Streaming**

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID_de_localisateur}/{nom_de_fichier}.ISM/Manifest

Exemple :

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


**Format MPEG DASH**

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID_de_localisateur}/{nom_de_fichier}.ISM/Manifest(format=mpd-time-csf) 

Exemple

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

**Format Apple HTTP Live Streaming (HLS) V4**

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID_de_localisateur}/{nom_de_fichier}.ISM/Manifest(format=m3u8-aapl)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

**Format Apple HTTP Live Streaming (HLS) V3**

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID_de_localisateur}/{nom_de_fichier}.ISM/Manifest(format=m3u8-aapl-v3)
	
	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

**HDS (pour les titulaires de licences Adobe PrimeTime/Access uniquement)**

{nom du point de terminaison de diffusion en continu-nom du compte media services}.streaming.mediaservices.windows.net/{ID_de_localisateur}/{nom_de_fichier}.ISM/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


### Empaquetage dynamique

Media Services fournit l'empaquetage dynamique qui permet de distribuer un contenu en diffusion continue en MP4 ou Smooth Streaming dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sans avoir à créer de nouveaux packages dans ces formats. 

Pour tirer parti de l'empaquetage dynamique, vous devez effectuer les opérations suivantes :

- encoder votre fichier mezzanine (source) en un ensemble de fichiers MP4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif ;
- obtenir au moins une unité de diffusion à la demande pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu. Pour plus d'informations, consultez la page [Extension des unités réservées de diffusion en continu à la demande](media-services-manage-origins.md#scale_streaming_endpoints/).

Avec l'empaquetage dynamique, vous devez stocker et payer les fichiers dans un seul format de stockage. Ensuite, Media Services crée et fournit la réponse appropriée en fonction des demandes des clients. 

Notez qu'en plus d'utiliser les fonctionnalités d'empaquetage dynamique, les unités réservées de diffusion en continu à la demande vous offrent une capacité de sortie dédiée qui peut être achetée par incréments de 200 Mbit/s. Par défaut, la diffusion en continu à la demande est configurée dans un modèle d'instance partagée, pour lequel les ressources du serveur (calcul, sortie, capacité, etc.) sont partagées avec tous les autres utilisateurs. Afin d'améliorer la vitesse de diffusion en continu à la demande, il est recommandé d'acheter des unités réservées de diffusion en continu à la demande.

### Téléchargement progressif 

Le téléchargement progressif vous permet de commencer la lecture multimédia avant que l'intégralité du fichier ait été téléchargée. Vous ne pouvez télécharger progressivement les fichiers .ism* (.ismv, .isma, .ismt, .ismc). 

Pour télécharger progressivement du contenu, utilisez le type de localisateur OnDemandOrigin. L'exemple suivant illustre l'URL basée sur le type de localisateur OnDemandOrigin :

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

La considération suivante s'applique :

- Vous devez déchiffrer les ressources à chiffrement de stockage que vous souhaitez diffuser en continu depuis le service d'origine pour permettre le téléchargement progressif.


### Télécharger

Pour télécharger votre contenu sur un périphérique client, vous devez créer un localisateur SAP. Le localisateur SAP vous donne accès au conteneur Azure Storage où votre fichier est stocké. Pour créer l'URL de téléchargement, vous devez inclure le nom du fichier entre l'hôte et la signature SAP. 

L'exemple suivant illustre l'URL basée sur le localisateur SAP :

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Les considérations suivantes s'appliquent :

- Vous devez déchiffrer les ressources à chiffrement de stockage que vous souhaitez diffuser en continu depuis le service d'origine pour permettre le téléchargement progressif.
- Si le téléchargement n'est pas terminé au bout de 12 heures, il échoue.



### Points de terminaison de diffusion en continu

Un **point de terminaison de diffusion en continu** représente un service de diffusion en continu qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de distribution de contenu (CDN) pour être redistribué. Le flux sortant d'un service de point de terminaison de diffusion en continu peut être un flux dynamique ou une ressource de vidéo à la demande dans votre compte Media Services. En outre, vous pouvez contrôler la capacité du service de point de terminaison de diffusion en continu afin de gérer les besoins croissants en matière de bande passante en ajustant les unités réservées de diffusion en continu. Vous devez allouer au moins une unité réservée pour les applications au sein d'un environnement de production. Pour plus d'informations, consultez la page [Mise à l'échelle d'un service de média](media-services-manage-origins.md#scale_streaming_endpoints).

## <a id="tasks"></a>Tâches liées à la distribution de ressources

### Configuration de la stratégie de remise de ressources

Configurez la stratégie de remise de ressources à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

### Publication de ressources

Publiez des ressources (en créant des localisateurs) à l'aide du **portail de gestion Azure** ou de **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


## Rubriques connexes

[Mettre à jour les localisateurs de Media Services après le déploiement des clés de stockage](media-services-roll-storage-access-keys.md)


<!--HONumber=52-->