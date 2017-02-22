---
title: Environnement App Service| Microsoft Docs
description: "Qu’est-ce qu’un environnement Azure App Service ? Présentation de l’environnement App Service."
keywords: "environnement azure app service, réseau virtuel, sécurisation des réseaux"
services: app-service
documentationcenter: 
author: stefsch
manager: wpickett
editor: 
ms.assetid: 1db5c057-3c56-4537-b580-cdd21fe3f3a7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: df2fa1d0790f8942a0350f9734232cc4b25cadc1
ms.openlocfilehash: a54f559801e1a4f4b752bc2268ea8d6cb20b1267


---
# <a name="app-service-environment-documentation"></a>Documentation sur l’environnement App Service
Un environnement App Service est une option de plan de service [Premium][PremiumTier] d’Azure App Service qui fournit un environnement totalement isolé et dédié pour exécuter en toute sécurité des applications Azure App Service de grande envergure, comme [Web Apps][WebApps], [Mobile Apps][MobileApps] et [API Apps][APIApps].  

Les environnements App Service constituent le meilleur choix pour les charges de travail applicatives avec les exigences suivantes :

* Très grande échelle
* Isolation et accès réseau sécurisé

Les clients peuvent créer plusieurs environnements App Service au sein d’une même région Azure, ainsi que dans plusieurs régions Azure.  Les environnements App Service sont donc parfaits pour l’évolution horizontale des applications sans état pour la prise en charge de lourdes charges de travail RPS.

Les environnements App Service sont isolés de façon à exécuter les applications d’un seul client et ils sont toujours déployés dans un réseau virtuel.  Les clients peuvent contrôler précisément le trafic réseau entrant et sortant des applications à l’aide des [groupes de sécurité réseau][NetworkSecurityGroups].  Les applications peuvent également établir des connexions sécurisées à haute vitesse via des réseaux virtuels aux ressources d’entreprise locales.

Les applications doivent souvent accéder à des ressources d’entreprise telles que des bases de données internes et des services web.  Les applications s’exécutant dans des environnements App Service peuvent accéder aux ressources joignables via des connexions VPN [site à site][SiteToSite] et [Azure ExpressRoute][ExpressRoute].

* [Qu'est-ce qu'un environnement App Service ?](../app-service-web/app-service-app-service-environment-intro.md)
* [Création d'un environnement App Service](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Création d'applications dans un environnement App Service](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Création et utilisation d’un équilibreur de charge interne avec des environnements App Service](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configuration d'un environnement App Service](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Mise à l'échelle des applications dans un environnement App Service](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Architecture et sécurité du réseau](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Conseils pratiques
[!INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

## <a name="videos"></a>vidéos
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]



<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/



<!--HONumber=Dec16_HO1-->


