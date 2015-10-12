<properties 
	pageTitle="Présentation de l'environnement App Service" 
	description="Découvrez la fonctionnalité Environnement App Service qui fournit des unités d'échelle sécurisées, dédiées et appartenant à un réseau virtuel pour exécuter toutes vos applications." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2015"
	ms.author="stefsch"/>

# Présentation de l'environnement App Service

## Vue d'ensemble ##
Un environnement App Service est une option de plan de service [Premium][PremiumTier] d’Azure App Service qui fournit un environnement totalement isolé et dédié de grande envergure des applications Azure App Service, comme [Web Apps][WebApps], [Mobile Apps][MobileApps] et [API Apps][APIApps].

Les environnements App Service constituent le meilleur choix pour les charges de travail applicatives avec les exigences suivantes :

- Très grande échelle
- Isolation et accès réseau sécurisé

Les clients peuvent créer plusieurs environnements App Service au sein d’une même région Azure, ainsi que dans plusieurs régions Azure. Les environnements App Service sont donc parfaits pour l’évolution horizontale des applications sans état pour la prise en charge de lourdes charges de travail RPS.

Les environnements App Service sont isolés de façon à exécuter les applications d’un seul client et ils sont toujours déployés dans un réseau virtuel. Les clients ont un contrôle affiné sur le trafic réseau entrant et sortant des applications, et les applications peuvent établir des connexions sécurisées à haut débit sur les réseaux virtuels avec les ressources d’entreprise locales.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Ressources de calcul dédiées ##
Toutes les ressources de calcul d’un environnement App Service sont exclusivement dédiées à un seul abonnement, et il est possible de configurer un environnement App Service avec jusqu’à cinquante (50) ressources de calcul pour une seule application.

Un environnement App Service se compose d’un pool de ressources de calcul frontal, ainsi que de un à trois pools de ressources de calcul de travail.

Le pool frontal contient des ressources de calcul responsables de l'arrêt SSL ainsi que de l'équilibrage de charge automatique des demandes d'application dans un environnement App Service.

Chaque pool de travaux contient des ressources de calcul allouées à des [plans App Service][AppServicePlan], qui, eux-mêmes, contiennent une ou plusieurs applications Azure App Service. Étant donné qu'un environnement App Service peut comprendre jusqu'à trois pools de travaux différents, vous pouvez choisir des ressources de calcul différentes pour chaque pool de travaux.

Cela vous permet, par exemple, de créer un pool de travaux avec des ressources de calcul moins puissantes pour les plans App Service destinés aux applications de développement ou de test. Un deuxième (ou même troisième) pool de travaux peut utiliser des ressources de calcul plus puissantes destinées aux plans App Service exécutant des applications de production.

Pour plus d'informations sur la quantité de ressources de calcul disponibles pour les pools frontaux et de travail, consultez [Comment configurer un environnement App Service][HowToConfigureanAppServiceEnvironment].

Pour plus d'informations sur les tailles de ressources de calcul disponibles prises en charge dans un environnement App Service, consultez la page [Service d'application Tarification][AppServicePricing] et passez en revue les options disponibles pour les environnements App Service dans le niveau de tarification Premium.

## Prise en charge des réseaux virtuels ##
Un environnement App Service peut être créé sur un réseau virtuel régional « v1 » classique préexistant ou sur un nouveau réseau virtuel régional « v1 » classique ([plus d’informations sur les réseaux virtuels][MoreInfoOnVirtualNetworks]). Étant donné qu'il existe toujours un environnement App Service sur un réseau virtuel régional, et plus précisément sur un sous-réseau d'un réseau virtuel régional, vous pouvez exploiter les fonctionnalités de sécurité des réseaux virtuels pour contrôler les communications réseau entrantes et sortantes.

Vous pouvez utiliser des [groupes de sécurité réseau][NetworkSecurityGroups] pour restreindre les communications réseau entrantes vers le sous-réseau sur lequel réside un environnement App Service. Cela vous permet d'exécuter des applications derrière des appareils et services en amont tels des pare-feu d'applications web, ainsi que des fournisseurs SaaS réseau.

Les applications doivent souvent accéder à des ressources d'entreprise telles que des bases de données internes et des services web. Une approche courante consiste à rendre ces points de terminaison disponibles uniquement au trafic réseau interne circulant au sein d'un réseau virtuel Azure. Une fois qu'un environnement App Service est joint au même réseau virtuel que les services internes, les applications s'exécutant dans l'environnement peuvent y accéder, notamment les points de terminaison accessibles via des connexions [site à site][SiteToSite] et [Azure ExpressRoute][ExpressRoute].

Pour plus d’informations sur le fonctionnement des environnements App Service avec les réseaux virtuels et les réseaux locaux, consultez les articles suivants sur l’[architecture réseau][NetworkArchitectureOverview], le [contrôle du trafic entrant][ControllingInboundTraffic] et la [connexion sécurisée aux serveurs principaux][SecurelyConnectingToBackends].

**Remarque :** un environnement App Service ne peut pas être créé sur un réseau virtuel « v2 ».

## Prise en main

Pour prendre en main les environnements App Service, consultez [Comment créer un environnement App Service][HowToCreateAnAppServiceEnvironment].

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service][AzureAppService].

Pour obtenir une vue d’ensemble de l’architecture réseau de l’environnement App Service, consultez l’article [Vue d’ensemble de l’architecture réseau][NetworkArchitectureOverview].

Pour plus d’informations sur l’utilisation d’un environnement App Service avec ExpressRoute, consultez l’article suivant sur [ExpressRoute et environnements App Service][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[Azure preview portal]: http://portal.azure.com
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=Oct15_HO1-->