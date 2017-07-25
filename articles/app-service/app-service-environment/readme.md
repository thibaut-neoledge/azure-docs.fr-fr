---
title: "Ressources relatives à l’environnement Azure App Service Environment"
description: "Répertorie la documentation consacrée à l’environnement Azure App Service Environment."
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 77452413-5193-4762-8b3d-5fa8e4edf1ca
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8b401036d4fa8e6c4e6430433b1641f5cb4ae010
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017

---

# <a name="app-service-environment-documentation"></a>Documentation sur l’environnement App Service
L’environnement App Service Environment est une fonctionnalité d’Azure App Service qui fournit un environnement totalement isolé et dédié pour l’exécution sécurisée de vos applications Azure App Service à grande échelle. Il permet d’héberger les éléments créés avec [Web Apps][webapps], [Mobile Apps][mobileapps], [API Apps][APIApps] et [Functions][Functions].

Les environnements App Service Environment constituent le meilleur choix pour les charges de travail applicatives présentant les exigences suivantes :

* Très grande échelle
* Isolation et accès réseau sécurisé

Les clients peuvent créer plusieurs environnements App Service au sein d’une même région Azure, ainsi que dans plusieurs régions Azure. Les environnements App Service sont donc parfaits pour l’évolution horizontale des applications sans état pour la prise en charge de lourdes charges de travail RPS.

Les environnements App Service sont isolés de façon à exécuter les applications d’un seul client et ils sont toujours déployés dans un réseau virtuel. Les clients peuvent contrôler précisément le trafic réseau entrant et sortant des applications à l’aide des [groupes de sécurité réseau][NSGs]. Les applications peuvent également établir des connexions sécurisées à haute vitesse via des réseaux virtuels aux ressources d’entreprise locales.

Les applications doivent souvent accéder à des ressources d’entreprise telles que des bases de données internes et des services web. Les applications s’exécutant dans des environnements App Service peuvent accéder aux ressources joignables via des connexions VPN [site à site][SiteToSite] et [Azure ExpressRoute][ExpressRoute].

* [Qu’est-ce qu’un environnement App Service Environment ?][Intro]
* [Création d’un environnement App Service Environment][MakeExternalASE]
* [Création d’un environnement App Service Environment avec équilibreur de charge interne][MakeILBASE]
* [Utilisation d’un environnement App Service Environment][UsingASE]
* [Considérations relatives à la mise en réseau pour un environnement App Service Environment][ASENetwork]
* [Création d’un environnement App Service Environment à partir d’un modèle][MakeASEfromTemplate]


## <a name="videos"></a>Vidéos
Maîtriser le PaaS moderne pour l’entreprise avec Azure App Service
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

Déploiement d’applications hautement évolutives et sécurisées
>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

Exécution d’applications web et mobiles d’entreprise sur Azure App Service
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]

## <a name="asev1"></a>ASEv1 ##
Il existe deux versions de l’environnement App Service Environment : ASEv1 et ASEv2. Pour plus d’informations sur ASEv1, consultez la [documentation sur l’environnement App Service Environment v1][ASEv1README].


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[ASEv1README]: ../app-service-app-service-environments-readme.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-site-to-site-create.md
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

