<properties 
	pageTitle="Vue d'ensemble d'Azure CDN" 
	description="Découvrez le réseau de distribution de contenu (CDN) Azure et comment l'utiliser pour diffuser du contenu haut débit en mettant en cache les objets blob et le contenu statique." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>

# Vue d'ensemble du réseau de distribution de contenu (CDN) Azure

Le réseau de distribution de contenu (CDN) Azure met en cache les objets blob Azure et le contenu statique utilisés par les services cloud à des emplacements stratégiques afin de fournir une bande passante maximale pour la distribution de contenu aux utilisateurs.

Si vous êtes un client CDN existant, vous pouvez désormais gérer vos points de terminaison CDN via le [portail Microsoft Azure](https://portal.azure.com).


Le CDN offre aux développeurs une solution globale pour la distribution de contenu haut débit en mettant en cache le contenu sur des nœuds physiques dans le monde entier. Pour obtenir la liste actuelle des emplacements des nœuds CDN, consultez la page [Emplacements des points de présence (POP) du réseau de distribution de contenu (CDN) Azure](cdn-pop-locations.md).

Les avantages de l'utilisation du CDN pour mettre en cache des données Azure sont notamment les suivants :

- De meilleures performances et une expérience utilisateur plus agréable pour les utilisateurs finaux qui se trouvent éloignés d'une source de contenu et qui utilisent des applications ayant recours à de nombreux « trajets Internet » pour charger le contenu
- Une diffusion à grande échelle pour améliorer la gestion instantanée des charges importantes, par exemple au début de l'événement du lancement d'un produit. 


>[AZURE.IMPORTANT] Quand vous créez ou activez un point de terminaison CDN, sa propagation dans le monde entier peut prendre jusqu’à 90 minutes.
 
Quand une demande d’objet est effectuée pour la première fois au CDN, l’objet est récupéré directement de son emplacement d’origine source. Cette origine peut être un compte de stockage Azure, une application web, un service cloud ou toute origine personnalisée qui accepte les demandes web publiques. Quand une demande est effectuée à l'aide de la syntaxe CDN, elle est redirigée vers le point de terminaison CDN le plus proche de l'emplacement à partir duquel elle a été faite pour fournir l'accès à l'objet. Si l'objet est introuvable sur ce point de terminaison, il est récupéré du service et mis en cache au point de terminaison, où un paramètre de durée de vie est géré pour l'objet mis en cache.

## Fonctionnalités Standard

Le niveau Standard de CDN inclut les fonctionnalités suivantes :

- Intégration simplifiée avec les services Azure tels que [Storage](cdn-create-a-storage-account-with-cdn.md), Web Apps et Media Services
- [Mise en cache des chaînes de requête](cdn-query-string.md)
- [Prise en charge du nom de domaine personnalisé](cdn-map-content-to-custom-domain.md)
- [Filtrage par pays](cdn-restrict-access-by-country.md)
- [Analyse principale](cdn-analyze-usage-patterns.md)
- [Origines de contenu personnalisées](cdn-how-to-use-cdn.md#caching-content-from-custom-origins)
- [Prise en charge de HTTPS](cdn-how-to-use-cdn.md#accessing-cached-content-over-https)
- Équilibrage de la charge
- Protection DDOS
- [Purge rapide](cdn-purge-endpoint.md)
- [Préchargement de ressources](cdn-preload-endpoint.md)
- [Gestion via l’API REST](https://msdn.microsoft.com/library/mt634456.aspx)


## Fonctionnalités Premium

Le niveau Premium de CDN comprend toutes les fonctionnalités du niveau Standard, ainsi que les fonctionnalités suivantes :

- [Moteur de distribution de contenu personnalisable et basé sur des règles](cdn-rules-engine.md)
- [Rapports HTTP avancés](cdn-advanced-http-reports.md)
- [Statistiques en temps réel](cdn-real-time-stats.md)

<!---HONumber=AcomDC_0204_2016-->