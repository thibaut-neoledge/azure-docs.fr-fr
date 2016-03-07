<properties 
	pageTitle="Azure App Service Environment" 
	description="Découvrir le fonctionnement d’App Service" 
	keywords="app service environment, azure app service environment"
	services="app-service" 
	documentationCenter="" 
	authors="yochay" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="stefsch"/>

# Vue d’ensemble

Un environnement App Service est une option de plan de service [Premium][PremiumTier] d’Azure App Service qui fournit un environnement totalement isolé et dédié de grande envergure des applications Azure App Service, comme [Web Apps][WebApps], [Mobile Apps][MobileApps] et [API Apps][APIApps].

Les environnements App Service constituent le meilleur choix pour les charges de travail applicatives avec les exigences suivantes :

- Très grande échelle
- Isolation et accès réseau sécurisé

Les clients peuvent créer plusieurs environnements App Service au sein d’une même région Azure, ainsi que dans plusieurs régions Azure. Les environnements App Service sont donc parfaits pour l’évolution horizontale des applications sans état pour la prise en charge de lourdes charges de travail RPS.

Les environnements App Service sont isolés de façon à exécuter les applications d’un seul client et ils sont toujours déployés dans un réseau virtuel. Les clients peuvent contrôler précisément le trafic réseau entrant et sortant des applications à l’aide des [groupes de sécurité réseau][NetworkSecurityGroups]. Les applications peuvent également établir des connexions sécurisées à haute vitesse via des réseaux virtuels aux ressources d’entreprise locales.

Les applications doivent souvent accéder à des ressources d’entreprise telles que des bases de données internes et des services web. Les applications s’exécutant dans des environnements App Service peuvent accéder aux ressources joignables via des connexions VPN [site à site][SiteToSite] et [Azure ExpressRoute][ExpressRoute].

* [Qu'est-ce qu'un environnement App Service ?](../app-service-web/app-service-app-service-environment-intro.md)
* [Création d'un environnement App Service](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Création d'applications dans un environnement App Service](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Configuration d'un environnement App Service](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Mise à l'échelle des applications dans un environnement App Service](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Architecture et sécurité du réseau](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## Procédures

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## Vidéos
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

<!---HONumber=AcomDC_0224_2016-->