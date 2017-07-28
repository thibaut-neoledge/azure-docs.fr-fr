---
title: "Présentation de l’environnement Azure App Service Environment"
description: "Brève vue d’ensemble de l’environnement Azure App Service Environment"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0d078aefbf73a45298f397d02ab24b2c8232ecef
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017

---
# <a name="introduction-to-the-app-service-environment"></a>Présentation de l’environnement App Service Environment #
 
## <a name="overview"></a>Vue d'ensemble ##

L’environnement App Service Environment est une fonctionnalité d’Azure App Service qui fournit un environnement totalement isolé et dédié pour l’exécution sécurisée de vos applications Azure App Service à grande échelle. Il permet d’héberger les éléments créés avec [Web Apps][webapps], [Mobile Apps][mobileapps], [API Apps][APIapps] et [Functions][Functions].

Les environnements App Service Environment constituent le meilleur choix pour les charges de travail applicatives présentant les exigences suivantes :

- Très grande échelle
- Isolation et accès réseau sécurisé
- Utilisation important de la mémoire

Les clients peuvent créer plusieurs environnements App Service au sein d’une même région Azure, ainsi que dans plusieurs régions Azure. Les environnements App Service sont donc parfaits pour l’évolution horizontale des applications sans état pour la prise en charge de lourdes charges de travail RPS.

Les environnements App Service sont isolés de façon à exécuter les applications d’un seul client et ils sont toujours déployés dans un réseau virtuel. Les clients ont un contrôle affiné sur le trafic réseau entrant et sortant des applications, et les applications peuvent établir des connexions sécurisées à haut débit sur les réseaux privés virtuels avec les ressources d’entreprise locales.

Tous les articles et procédures concernant les environnements App Service sont disponibles dans le [fichier Lisez-moi des environnements App Service][ASEReadme].

Pour avoir une idée générale de la façon dont les environnements App Service permettent l’accès réseau sécurisé à grande échelle, voir la vidéo [AzureCon Deep Dive](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) sur les environnements App Service Environments (ASE).

Pour une exploration approfondie de la mise à l’échelle horizontale à l’aide de plusieurs environnements App Service, voir l’article sur la configuration d’une [empreinte d’application géo-distribuée](https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/).

Pour afficher la configuration de l’architecture de sécurité illustrée dans AzureCon Deep Dive, consultez l’article sur l’implémentation d’une [architecture de sécurité en couches](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-layered-security) avec les environnements App Service.

L’accès aux applications qui s’exécutent sur des environnements App Service peut être contrôlé par des appareils en amont tels que les pare-feu d’applications web (WAF). Ce scénario est traité dans l’article sur la [configuration d’un pare-feu d’application web pour les environnements App Service](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-web-application-firewall) .

## <a name="dedicated-environment"></a>Environnement dédié ##

Un environnement App Service est dédié exclusivement à un abonnement et peut héberger 100 instances. Il peut s’agir de 100 instances d’un même plan App Service ou de 100 plans App Service à instance unique, ou de toutes les combinaisons possibles des deux.

Un environnement App Service Environment se compose de serveurs frontaux et de workers. Les serveurs frontaux sont responsables de l’arrêt du protocole HTTP/HTTPS ainsi que de l’équilibrage de charge automatique des demandes d’application dans un environnement App Service Environment. Des serveurs frontaux sont automatiquement ajoutés à mesure que les plans App Service dans l’environnement App Service Environment montent en charge.

Les workers sont des rôles qui hébergent des applications clientes. Les workers sont disponibles dans des 3 tailles fixes :
* 1 cœur/3,5 Go de RAM
* 2 cœurs/7 Go de RAM
* 4 cœurs/14 Go de RAM

Les clients n’ont pas besoin de gérer les serveurs frontaux et les workers. Toute infrastructure est automatiquement ajoutée à mesure que les clients augmentent l’échelle de leurs plans App Service. À mesure que des plans App Service sont créés ou mis à l’échelle dans un environnement App Service Environment, l’infrastructure requise est augmentée ou réduite en conséquence.

Il existe un taux mensuel fixe pour un environnement App Service Environment, qui couvre l’infrastructure et ne change pas avec la taille de l’environnement App Service Environment. En plus de cela, il existe un coût par cœur de plan App Service. Toutes les applications hébergées dans un environnement App Service Environment sont dans la référence (SKU) de tarification isolée. Pour plus d’informations sur la tarification d’un environnement App Service Environment, visitez la page [Tarification d’App Service][Pricing], et passez en revue les options disponibles pour les environnements App Service Environment.

## <a name="virtual-network-support"></a>Prise en charge des réseaux virtuels ##

Un environnement App Service Environment peut être créé uniquement dans un réseau virtuel Azure Resource Manager. Pour en savoir plus sur les réseaux virtuels Azure, voir [FAQ sur les réseaux virtuels Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Étant donné qu’il existe toujours un environnement App Service Environment sur un réseau virtuel, et plus précisément sur un sous-réseau d’un réseau virtuel, vous pouvez exploiter les fonctionnalités de sécurité des réseaux virtuels pour contrôler les communications réseau entrantes et sortantes pour vos applications.

Un environnement App Service peut être soit accessible sur Internet avec une adresse IP publique, soit accessible en interne avec uniquement une adresse d’Équilibreur de charge interne (ILB) Azure.

Vous pouvez utiliser des [groupes de sécurité réseau][NSGs] pour restreindre les communications réseau entrantes vers le sous-réseau sur lequel réside un environnement App Service. Cela vous permet d'exécuter des applications derrière des appareils et services en amont tels des pare-feu d'applications web, ainsi que des fournisseurs SaaS réseau.

Les applications doivent souvent accéder à des ressources d'entreprise telles que des bases de données internes et des services web. Si l’environnement App Service Environment est déployé dans un réseau virtuel Azure qui a une connexion VPN au réseau local, les applications dans l’environnement App Service Environment peuvent accéder aux ressources locales. Cela est vrai même que le réseau privé virtuel soit un VPN [de site à site](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) ou un VPN [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/).

Pour plus d’informations sur le fonctionnement des environnements App Service Environment avec des réseaux virtuels et des réseaux locaux, voir [Considérations en matière de réseau pour un environnement App Service Environment][ASENetwork].

## <a name="asev1"></a>ASEv1 ##

L’environnement App Service Environment est disponible en deux versions : ASEv1 et ASEv2. Les informations précédentes sont centrées sur ASEv2. Cette section montre les différences entre ASEv1 et ASEv2. 

Dans ASEv1, vous devez gérer toutes les ressources manuellement. Celles-ci incluent les serveurs frontaux, les rôles de travail et les adresses IP utilisées pour le protocole SSL basé sur IP. Pour pouvoir augmenter l’échelle de votre plan App Service, vous devez d’abord augmenter celle du pool de workers dans lequel vous voulez héberger le plan.

Les versions ASEv1 et ASEv2 utilisent un modèle de tarification différent. Dans ASEv1, vous devez payer pour chaque cœur alloué. Cela inclut les cœurs utilisés pour les serveurs frontaux ou les workers qui n’hébergent pas de charges de travail. Dans la version ASEv1, la taille d’échelle maximale par défaut d’un environnement App Service Environment correspond à un total de 55 hôtes, dont les workers et les serveurs frontaux. L’un des avantages d’un environnement ASEv1 est qu’il peut être déployé dans un réseau virtuel classique ainsi que dans un réseau virtuel Resource Manager. Pour plus d’informations sur la version ASEv1, voir [Présentation de l’environnement App Service v1][ASEv1Intro].

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

