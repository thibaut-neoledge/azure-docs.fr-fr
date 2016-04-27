<properties
	pageTitle="Vue d'ensemble d'Azure CDN"
	description="Découvrez le réseau de distribution de contenu (CDN) Azure et comment l'utiliser pour diffuser du contenu haut débit en mettant en cache les objets blob et le contenu statique."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/15/2016" 
	ms.author="casoper"/>

# Vue d'ensemble du réseau de distribution de contenu (CDN) Azure

Le réseau de distribution de contenu (CDN) Azure met en cache les objets blob Azure et le contenu statique utilisés par les sites web à des emplacements stratégiques afin de fournir un débit maximal pour la distribution de contenu aux utilisateurs. Le CDN offre aux développeurs une solution globale pour la distribution de contenu haut débit en mettant en cache le contenu sur des nœuds physiques dans le monde entier. Pour obtenir la liste actuelle des emplacements de nœuds CDN, consultez la page [Emplacements des points de présence CDN Azure](cdn-pop-locations.md).

Les avantages de l'utilisation du CDN pour mettre en cache des données Azure sont notamment les suivants :

- De meilleures performances et une expérience enrichie pour les utilisateurs finaux, en particulier ceux qui utilisent des applications ayant recours à de nombreux allers-retours pour charger le contenu.
- Une mise à grande échelle pour améliorer la gestion instantanée des charges importantes, par exemple au début de l'événement de lancement d'un produit.
- Si l’on répartit les requêtes utilisateur et que l’on diffuse du contenu à partir des serveurs Edge, le trafic transmis à l’origine est moins important.


## Fonctionnement

![Présentation du CDN](./media/cdn-overview/cdn-overview.png)

1. Un utilisateur (Alice) demande un fichier (également appelé ressource) à l'aide d'une URL avec un nom de domaine spécial, par exemple `<endpointname>.azureedge.net`. Le DNS achemine la demande à l’emplacement du point de présence (POP) le plus performant. Il s’agit en général du point du présence le plus proche géographiquement de l'utilisateur.

2. Si les serveurs Edge du point de présence ne disposent pas du fichier dans leur cache, le serveur Edge demande le fichier à l'origine. L'origine peut être une application web Azure, un service cloud Azure, un compte de stockage Azure ou n'importe quel serveur web accessible publiquement.

3. L'origine renvoie les fichiers sur le serveur Edge, notamment les en-têtes HTTP facultatifs décrivant la durée de vie du fichier.

4. Le serveur Edge met en cache le fichier et le renvoie au demandeur d'origine (Alice). Le fichier reste en cache sur le serveur Edge jusqu'à la fin de la durée de vie. Si l'origine n'a pas spécifié de durée de vie, elle est par défaut de 7 jours.

5. Des utilisateurs supplémentaires (par exemple, Jean) peuvent demander le même fichier à l'aide de la même URL et peuvent également être dirigés vers ce même point de présence.

6. Si la durée de vie du fichier n'a pas expiré, le serveur Edge renvoie le fichier à partir du cache. L’expérience utilisateur est en conséquence plus rapide et plus réactive.


## Fonctionnalités Standard

Le niveau Standard de CDN inclut les fonctionnalités suivantes :

- Intégration simplifiée avec les services Azure tels que [Storage](cdn-create-a-storage-account-with-cdn.md), [Cloud Services](cdn-cloud-service-with-cdn.md), Web Apps et [Media Services](../media-services/media-services-manage-origins.md#enable_cdn)
- Prise en charge de HTTPS
- Équilibrage de la charge
- Protection DDOS
- [Mise en cache des chaînes de requête](cdn-query-string.md)
- [Prise en charge du nom de domaine personnalisé](cdn-map-content-to-custom-domain.md)
- [Filtrage par pays](cdn-restrict-access-by-country.md)
- [Analyse principale](cdn-analyze-usage-patterns.md)
- [Purge rapide](cdn-purge-endpoint.md)
- [Préchargement de ressources](cdn-preload-endpoint.md)
- [Gestion via l’API REST](https://msdn.microsoft.com/library/mt634456.aspx)


## Fonctionnalités Premium

Le niveau Premium de CDN comprend toutes les fonctionnalités du niveau Standard, ainsi que les fonctionnalités suivantes :

- [Moteur de distribution de contenu personnalisable et basé sur des règles](cdn-rules-engine.md)
- [Rapports HTTP avancés](cdn-advanced-http-reports.md)
- [Statistiques en temps réel](cdn-real-time-stats.md)

## Étapes suivantes

Pour commencer avec le CDN, consultez [Utilisation du CDN Azure](./cdn-create-new-endpoint.md).

Si vous êtes un client CDN existant, vous pouvez désormais gérer vos points de terminaison CDN via le [portail Microsoft Azure](https://portal.azure.com).

<!---HONumber=AcomDC_0420_2016-->