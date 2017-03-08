---
title: "Fonctionnement d’Azure App Service"
description: "Découvrir le fonctionnement d’App Service"
keywords: "app service, azure app service, mise à l&quot;échelle, évolutif, plan app service, coût d&quot;app service"
services: app-service
documentationcenter: 
author: yochay
manager: erikre
editor: 
ms.assetid: ae74fc32-969e-4580-8d61-02c922f1f184
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/23/2017
ms.author: yochayk
translationtype: Human Translation
ms.sourcegitcommit: edb3325414adf876548181243ddfa2d515aeb0b8
ms.openlocfilehash: 2d830963d3d2adba71a6ca99f79eac0fc8cbfb12
ms.lasthandoff: 02/24/2017


---
# <a name="how-app-service-works"></a>Fonctionnement d’App Service
Azure App Service est un service cloud conçu pour résoudre les problèmes pratiques auxquels les ingénieurs sont confrontés.
App Service est destiné à fournir une productivité supérieure en développement sans transiger sur la nécessité de fournir des applications à l’échelle du cloud. 

App Service fournit également les fonctionnalités et les infrastructures qui sont nécessaires à la création d’applications métier d’entreprise. App Service vous permet de développer des applications dans les langages de développement les plus utilisés, notamment Java, PHP, Node.js, Python et les langages .NET de Microsoft. Avec App Service, vous pouvez :

* générer des applications web hautement évolutives.
* Générer rapidement des infrastructures principales d’application mobile avec un ensemble de fonctionnalités mobiles simples d’utilisation, par exemple des infrastructures principales de données, l’authentification utilisateur et les notifications Push.
* Implémenter, déployer et publier des API avec API Apps.
* lier entre elles les applications métier dans des flux de travail et transformer les données avec Logic Apps.

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

Tous les types d’application s’appuient sur la plateforme Web App évolutive et flexible, qui permet aux développeurs de bénéficier d’une expérience optimisée du cycle de vie complet allant de la conception d’application à la maintenance d’application. Les fonctionnalités de cycle de vie permettent ce qui suit :

* **Création d’application rapide**. Commencer à partir de zéro ou sélectionner un package de système de soutien opérationnel (OSS) dans Azure Marketplace.
* **Déploiement continu**. Déployer automatiquement un nouveau code à partir de solutions de contrôle de code source populaires comme TFS, GitHub et BitBucket, et synchroniser le contenu des services de stockage en ligne que sont OneDrive et DropBox.
* **Tester en production**. Sans difficulté, créer des environnements de préproduction et gérer la quantité de trafic entrant associée. Procéder à un débogage dans le cloud au besoin et à une restauration lorsque des problèmes sont trouvés.
* **Exécution de traitements par lots et de tâches asynchrones**. Exécuter du code dans un processus en arrière-plan ou activer votre code en fonction des événements (par exemple, arrivée des messages dans Azure Storage Queue) et des heures planifiées (CRON).
* **Mise à l’échelle de l’application**. Utiliser l’une des nombreuses options pour mettre à l’échelle automatiquement votre service horizontalement et verticalement en fonction du trafic et de l’utilisation des ressources. Configurer des environnements privés dédiés à vos applications.   
* **Maintenance de l’application**. Tirer parti des nombreuses fonctionnalités de débogage et de diagnostic pour anticiper les problèmes et pour les résoudre efficacement en temps réel (avec des fonctionnalités telles que la réparation automatique et le débogage dynamique) ou après les faits en analysant les journaux et les images mémoire.

Ensemble, les fonctionnalités d’App Service permettent aux développeurs de se concentrer sur leur code et d’atteindre rapidement un état de production stable et hautement évolutif. Avec les fonctionnalités d’API Apps et de Logic Apps, les développeurs peuvent créer des applications d’entreprise réelles rapprochant les solutions d’entreprise et sur site de l’intégration au cloud. 

## <a name="videos"></a>Vidéos
* [Architecture Azure App Service](https://azure.microsoft.com/documentation/videos/why-azure-web-sites-plus-architecture/)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur App Service, consultez l’une des rubriques suivantes :

* [Qu'est-ce qu'Azure App Service ?](app-service-value-prop-what-is.md)
  * [Application web](../app-service-web/app-service-web-overview.md)
  * [Application mobile](../app-service-mobile/app-service-mobile-value-prop.md)
  * [Application API](../app-service-api/app-service-api-apps-why-best-platform.md)
* [Architecture Azure App Service (présentation)](http://www.slideshare.net/maartenba/windows-azure-web-sites-things-they-dont-teach-kids-in-school-comunity-day-2013)
* [Comparaison entre Azure App Service, Azure Cloud Services et Azure Virtual Machines](../app-service-web/choose-web-site-cloud-service-vm.md)
* [Présentation des plans App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md)
* [Présentation de l'environnement App Service](../app-service-web/app-service-app-service-environment-intro.md)
  * [Exercice : création d’un environnement App Service](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Prise en charge des piles de développement Azure App Service](https://azure.microsoft.com/blog/windows-azure-websites-development-stacks-support/)




