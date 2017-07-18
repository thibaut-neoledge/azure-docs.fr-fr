---
title: "Vue d’ensemble des alertes dans Microsoft Azure et Azure Monitor | Microsoft Docs"
description: "Les alertes vous permettent d’analyser des mesures de ressources, événements ou journaux Azure, puis d’envoyer des alertes quand une condition spécifiée est remplie."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 76c8feb077cca27dc96f43e708cdef4fbb0f824c
ms.contentlocale: fr-fr
ms.lasthandoff: 03/31/2017

---
# <a name="what-are-alerts-in-microsoft-azure"></a>Que sont les alertes dans Microsoft Azure ?
Cet article décrit les alertes Microsoft Azure ainsi que leurs avantages, et comment commencer à les utiliser. Cela s’applique plus particulièrement à Azure Monitor, mais propose des références vers d’autres services.  

Les alertes sont une méthode d’analyse des mesures des ressources événements ou journaux Azure, puis d’avertissement quand une condition spécifiée est remplie.  

## <a name="alerts-in-different-azure-services"></a>Alertes dans différents services Azure
Les alertes sont disponibles dans différents services, notamment les suivants :

* **Application Insights** : prend en charge les tests web et les alertes de mesure. Consultez [Définir des alertes dans Application Insights](../application-insights/app-insights-alerts.md) et [Analyser la disponibilité et la réactivité d’un site web](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics (Operations Management Suite)** : prend en charge le routage des journaux de diagnostic et d’activité vers Log Analytics. Operations Management Suite autorise les alertes de mesure, de journal, etc. Pour plus d’informations, consultez [Alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md).  
* **Azure Monitor** : prend en charge les alertes sur la base de valeurs de mesures et d’événements de journal d’activité. Vous pouvez utiliser [l’API REST Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx) pour gérer les alertes.  Pour plus d’informations, consultez [Utilisation du portail Azure, de PowerShell ou de l’interface de ligne de commande pour créer des alertes](insights-alerts-portal.md).

## <a name="visual-summary"></a>Résumé visuel
Le diagramme suivant récapitule les alertes et ce que vous pouvez en faire dans Azure Monitor. D’autres actions peuvent être disponibles pour les services répertoriés précédemment. Par exemple, seules les alertes sur les journaux de diagnostic sont disponibles actuellement dans Log Analytics.

![Alertes expliquées](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="what-can-trigger-alerts-in-azure-monitor"></a>Quel élément peut déclencher les alertes dans Azure Monitor ?

Vous pouvez recevoir des alertes sur la base de :

* **Valeurs de mesure** : l’alerte se déclenche quand la valeur d’une mesure spécifiée dépasse un seuil que vous attribuez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois quand la condition est remplie et par la suite une fois que la condition n’est plus remplie. Pour obtenir la liste croissante des mesures disponibles prises en charge par Azure Monitor, voir [Liste des mesures prises en charge sur Azure Monitor](monitoring-supported-metrics.md).
* **Événements de journal d’activité** : cette alerte peut se déclencher lorsqu’un événement particulier se produit sur une ressource ou lorsqu’une notification de service est publiée sur votre abonnement.

## <a name="what-can-metric-alerts-do"></a>À quoi servent les alertes métriques ?
Vous pouvez configurer une alerte pour effectuer les actions suivantes :

* envoyer des notifications par courrier électronique à l’administrateur de service, aux coadministrateurs ou aux adresses e-mail supplémentaires que vous spécifiez ;
* appeler un webhook, qui permet de lancer des actions d’automatisation supplémentaires. Voici quelques exemples d’appel :
    - Runbook Azure Automation
    - Fonction Azure
    - Application logique Azure
    - un service tiers

## <a name="what-can-activity-log-alerts-do"></a>À quoi servent les alertes de journal d’activité ?
Vous pouvez configurer une alerte pour effectuer les actions suivantes :
* déclencher l’alerte lorsqu’un événement spécifique se produit sur une des ressources sous votre abonnement ;
* déclencher l’alerte chaque fois qu’une notification de service est publiée dans votre abonnement ;
* alerter les membres d’un groupe d’actions via
    * sms
    * Email
    * webhook

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez obtenir des informations sur les règles d’alerte et leur configuration avec :

* En savoir plus sur les [métriques](monitoring-overview-metrics.md)
* Configurer les [alertes métriques par le biais du portail Azure](insights-alerts-portal.md)
* Configurer les [alertes métriques dans PowerShell](insights-alerts-powershell.md)
* Configurer l’[interface de ligne de commande des alertes métriques](insights-alerts-command-line-interface.md)
* Configurer les [alertes métriques au moyen de l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* En savoir plus sur les [journaux d’activité](monitoring-overview-activity-logs.md)
* Configurer les [alertes de journal d’activité par le biais du portail Azure](monitoring-activity-log-alerts.md)
* Configurer les [alertes de journal d’activité au moyen de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Consulter le [schéma de webhook d’alerte de journal d’activité](monitoring-activity-log-alerts-webhook.md)
* Apprenez-en plus sur les [notifications de service](monitoring-service-notifications.md)
* En savoir plus sur les [groupes d’actions](monitoring-action-groups.md)

