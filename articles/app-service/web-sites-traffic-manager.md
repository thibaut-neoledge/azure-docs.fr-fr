---
title: "Contrôle du trafic des applications web Azure avec Azure Traffic Manager"
description: Cet article fournit des informations globales sur Azure Traffic Manager et son rapport aux applications web Azure.
services: app-service\web
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: 93645aa5765d533b45fe2266f061ad61c0bf45d7
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Contrôle du trafic des applications web Azure avec Azure Traffic Manager
> [!NOTE]
> Cet article présente des informations récapitulatives sur Microsoft Azure Traffic Manager et son rapport à Azure Web App. Vous trouverez d'autres informations sur Azure Traffic Manager en suivant les liens fournis à la fin de cet article.
> 
> 

## <a name="introduction"></a>Introduction
Vous pouvez utiliser Azure Traffic Manager pour contrôler la façon dont les requêtes des clients web sont distribuées aux applications web dans Azure App Service. Lorsque des points de terminaison d’applications web sont ajoutés à un profil Azure Traffic Manager, ce dernier conserve une trace du statut de vos applications web (en cours d’exécution, arrêtées ou supprimées) pour pouvoir décider du point de terminaison qui doit recevoir le trafic.

## <a name="routing-methods"></a>Méthodes de routage
Azure Traffic Manager utilise trois méthodes de routage. Ces méthodes sont décrites dans la liste suivante, car elles se rapportent aux applications web Azure.

* **[Priorité](#priority) :** utilisez une application web principale pour tout le trafic, et fournissez des sauvegardes au cas où celle-ci ou les applications web de sauvegarde ne seraient pas disponibles.
* **[Pondération](#weighted) :** répartissez le trafic sur un ensemble d’applications web, uniformément ou en fonction du poids que vous définissez.
* **[Performances](#performance) :** lorsque vous avez des applications web à différents emplacements géographiques, utilisez l’application web « la plus proche » en termes de latence réseau faible.
* **[Géographique](#geographic) :** orientez les utilisateurs vers des applications web spécifiques en fonction de l’emplacement géographique d’où provient leur requête DNS. 

Pour plus d’informations, voir [Méthodes de routage de Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="web-apps-and-traffic-manager-profiles"></a>Applications web et profils dans Traffic Manager
Pour configurer le contrôle du trafic des applications web, vous pouvez créer un profil dans Azure Traffic Manager, qui utilise l’une des trois méthodes d’équilibrage de charge décrites précédemment, puis ajouter au profil les points de terminaison (ici, des applications web) dont vous souhaitez contrôler le trafic. Le statut de votre application web (en cours d’exécution, arrêtée ou supprimée) est régulièrement communiqué au profil, ce qui permet à Azure Traffic Manager d’orienter le trafic en conséquence.

Lorsque vous utilisez Azure Traffic Manager avec Azure, tenez compte des points suivants :

* Pour les déploiements des applications web dans une même région uniquement, Web Apps fournit déjà les fonctionnalités de basculement et de tourniquet (round-robin) et ce, quel que soit le mode des applications web.
* Pour les déploiements dans une même région qui utilisent Web Apps conjointement avec un autre service cloud Azure, vous pouvez combiner les deux types de point de terminaison pour autoriser les scénarios hybrides.
* Vous pouvez spécifier un seul point de terminaison d’application web par région dans un profil. Lorsque vous sélectionnez une application web comme point de terminaison pour une région, les autres applications web de cette région ne peuvent plus être sélectionnées pour ce profil.
* Le point de terminaison d’application web, que vous spécifiez dans un profil Azure Traffic Manager, s’affiche à la section **Noms de domaine** de la page Configurer pour l’application web dans le profil, mais il n’est pas configurable ici.
* Lorsque vous avez ajouté une application web à un profil, l’**URL du site** dans le tableau de bord de l’application web sur le portail affiche l’URL du domaine personnalisé de l’application web (s’il est défini). Sinon, l’URL du profil Traffic Manager (par exemple, `contoso.trafficmgr.com`) s’affiche. Le nom de domaine direct de l’application web et l’URL de Traffic Manager sont tous deux affichés dans la page Configurer de l’application web, à la section **Noms de domaine**.
* Vos noms de domaines personnalisés fonctionnent comme prévu, mais en plus de les ajouter à vos applications web, vous devez configurer votre carte DNS pour qu’elle pointe vers l’URL de Traffic Manager. Pour plus d’informations sur la configuration d’un domaine personnalisé pour une application web Azure, consultez [Configuration d’un nom de domaine personnalisé pour un site web Azure](app-service-web-tutorial-custom-domain.md).
* Vous ne pouvez ajouter que des applications web en mode standard ou Premium à un profil Azure Traffic Manager.

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d'ensemble conceptuelle et technique d'Azure Traffic Manager, consultez la rubrique [Vue d'ensemble de Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Pour plus d’informations sur l’utilisation de Traffic Manager avec Web Apps, consultez les billets de blog [Utilisation de Microsoft Azure Traffic Manager avec WAWS](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) et [Azure Traffic Manager peut désormais s’intégrer dans les sites web Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

