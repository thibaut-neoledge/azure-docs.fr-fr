---
title: "Présentation de Traffic Manager | Microsoft Docs"
description: "Cet article vous aidera à comprendre le fonctionnement de Traffic Manager, et de déterminer s’il est le choix de routage du trafic adapté à votre application"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 79c14e713fd58d3f69f5978e550a78e8e84d286d

---

# <a name="overview-of-traffic-manager"></a>Vue d’ensemble de Traffic Manager

Microsoft Azure Traffic Manager vous permet de contrôler la répartition du trafic utilisateur pour les points de terminaison de service dans différents centres de données. Les points de terminaison de service pris en charge par Traffic Manager incluent des machines virtuelles Azure, des applications web et des services cloud. Vous pouvez également utiliser Traffic Manager avec des points de terminaison externes non-Azure.

Traffic Manager utilise le DNS (Domain Name System) pour diriger les demandes des clients vers le point de terminaison approprié en fonction de la [méthode de routage du trafic](traffic-manager-routing-methods.md) et de l’intégrité des points de terminaison. Traffic Manager fournit un large éventail de méthodes de routage du trafic pour répondre à différents besoins d’application, la [surveillance](traffic-manager-monitoring.md) de l’intégrité des points de terminaison et le basculement automatique. Traffic Manager est résilient aux défaillances, notamment à l’échec d’une région Azure entière.

## <a name="traffic-manager-benefits"></a>Avantages de Traffic Manager

Traffic Manager peut vous aider à atteindre les objectifs suivants :

* **Améliorer la disponibilité des applications critiques**

    Traffic Manager vous permet de garantir une haute disponibilité de vos applications en surveillant vos points de terminaison et en fournissant un basculement automatique en cas de panne d’un point de terminaison.

* **Améliorer la réactivité des applications haute performance**

    Azure vous permet d'exécuter des sites web ou des services cloud dans des centres de données situés dans le monde entier. Traffic Manager améliore la réactivité de vos applications en dirigeant le trafic vers le point de terminaison affichant la latence réseau la plus faible pour le client.

* **Gérer les services sans les interrompre**

    Vous pouvez effectuer les opérations de maintenance planifiée sur vos applications sans temps d’arrêt. Traffic Manager dirige le trafic vers les autres points de terminaison pendant la maintenance.

* **Combiner des applications cloud et locales**

    Traffic Manager prend en charge des points de terminaison externes non Azure, ce qui permet de l’utiliser dans des déploiements de cloud hybride et locaux, notamment des scénarios tels que le « burst-to-cloud » la « migration vers le cloud » et le « basculement vers le cloud ».

* **Distribuer le trafic pour des déploiements vastes et complexes**

    En utilisant les [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md), il est possible de combiner des méthodes de routage du trafic pour créer des règles flexibles et sophistiquées afin de répondre aux besoins des déploiements plus vastes et plus complexes.

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md).
* Découvrez comment développer des applications à haute disponibilité à l’aide de la [surveillance de points de terminaison Traffic Manager](traffic-manager-monitoring.md).
* En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) prises en charge par Traffic Manager.
* [Créer un profil Traffic Manager](traffic-manager-manage-profiles.md).



<!--HONumber=Nov16_HO3-->


