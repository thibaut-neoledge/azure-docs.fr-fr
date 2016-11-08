---
title: Vue d’ensemble des alertes dans Microsoft Azure | Microsoft Docs
description: Les alertes vous permettent d’analyser des mesures de ressources, événements ou journaux Azure, puis d’envoyer des alertes quand une condition spécifiée est remplie.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: robb

---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Vue d’ensemble des alertes dans Microsoft Azure
Cet article décrit les alertes Microsoft Azure ainsi que leurs avantages, et comment commencer à les utiliser.  

## <a name="what-are-alerts?"></a>Que sont les alertes ?
Les alertes sont une méthode d’analyse des mesures des ressources événements ou journaux Azure, puis d’avertissement quand une condition spécifiée est remplie.

Vous pouvez recevoir des alertes sur la base de :

* **Valeurs de mesure** : l’alerte se déclenche quand la valeur d’une mesure spécifiée dépasse un seuil que vous attribuez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois quand la condition est remplie et par la suite une fois que la condition n’est plus remplie.
* **Événements de journal d’activité** : cette alerte peut se déclencher sur chaque événement ou, uniquement quand un certain nombre d’événements se produisent.

## <a name="alerts-in-different-azure-services"></a>Alertes dans différents services Azure
Les alertes sont disponibles dans différents services, notamment les suivants :

* **Application Insights** : prend en charge les tests web et les alertes de mesure. Consultez [Définir des alertes dans Application Insights](../application-insights/app-insights-alerts.md) et [Analyser la disponibilité et la réactivité d’un site web](../application-insights/app-insights-monitor-web-app-availability.md).
* **Log Analytics (Operations Management Suite)** : prend en charge le routage des journaux de diagnostic vers Log Analytics. Operations Management Suite autorise les alertes de mesure, de journal, etc. Pour plus d’informations, consultez [Alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md).   
* **Azure Monitor** : prend en charge les alertes sur la base de valeurs de mesures et d’événements de journal d’activité. Azure inclut les [API REST d’Azure Insights](https://msdn.microsoft.com/library/dn931943.aspx).  Pour plus d’informations, consultez [Utilisation du portail Azure, de PowerShell ou de l’interface de ligne de commande pour créer des alertes](insights-alerts-portal.md).

## <a name="alert-actions"></a>Actions d’alerte
Vous pouvez configurer une alerte pour effectuer les opérations suivantes :

* envoyer des notifications par courrier électronique à l’administrateur de service, aux coadministrateurs ou aux adresses e-mail supplémentaires que vous spécifiez ;
* appeler un webhook, qui permet de lancer des actions d’automatisation supplémentaires.
  
  ![Alertes expliquées](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez obtenir des informations sur les règles d’alerte et leur configuration avec :

* [Portail Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Interface de ligne de commande (CLI)](../azure-portal/insights-alerts-command-line-interface.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

<!--HONumber=Oct16_HO2-->


