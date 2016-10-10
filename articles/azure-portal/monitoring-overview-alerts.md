<properties
	pageTitle="Vue d’ensemble des alertes dans Microsoft Azure | Microsoft Azure"
	description="Les alertes vous permettent d’analyser des mesures de ressources, événements ou journaux Azure, puis d’envoyer des alertes lorsqu’une condition spécifiée est remplie."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/24/2016"
	ms.author="robb"/>

# Vue d’ensemble des alertes dans Microsoft Azure 


Cet article décrit les alertes Microsoft Azure ainsi que leurs avantages, et comment commencer à les utiliser.

## Que sont les alertes ?
Les alertes sont une méthode d’analyse des mesures des ressources événements ou journaux Azure, puis d’avertissement lorsqu’une condition spécifiée est remplie.

Vous pouvez recevoir des alertes sur la base de :
    
- Valeurs de mesure - L’alerte se déclenche lorsque la valeur d’une mesure spécifiée dépasse un seuil que vous attribuez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois lorsque la condition est remplie et par la suite une fois que la condition n’est plus remplie.
- Événements de journal d’activité : une alerte peut se déclencher sur chaque événement ou, uniquement lorsqu’un certain nombre d’événements se produisent.

## Alertes dans différents services Azure
 
Les alertes sont disponibles dans différents services

- **Application Insights** - Pour les tests web et alertes de mesures. Consultez la page [Définir les alertes dans Application Insights](../application-insights/app-insights-alerts.md) et [Analyse de la disponibilité et de la réactivité d’un site web](../application-insights/app-insights-monitor-web-app-availability.md).
- **OMS Log Analytics** - Vous pouvez acheminer les journaux de diagnostic vers Log Analytics. OMS autorise les mesures, journaux et autres types d’alerte. Pour plus d’informations, consultez [Alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md).
- **Azure Monitor** - Azure Monitor permet d’envoyer des alertes sur la base de valeurs de mesures et d’événements de journal d’activité. Azure inclut les [API REST d’Azure Insights](https://msdn.microsoft.com/library/dn931943.aspx). Consultez également [Utilisation du portail Azure, de PowerShell ou l’Interface de ligne de commande pour créer des alertes](insights-alerts-portal.md)

## Actions d’alerte
Vous pouvez configurer une alerte pour effectuer les opérations suivantes lorsque son déclenchement :

- envoyer des notifications par courrier électronique à l’administrateur de service, aux coadministrateurs et/ou aux adresses e-mail supplémentaires que vous spécifiez.
- appeler un webhook, qui permet de lancer des actions d’automatisation supplémentaires

 ![Alertes expliquées.](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)


## Étapes suivantes

obtenir des informations sur les règles d’alerte et les configurer avec

- [Le portail Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [Interface de ligne de commande (CLI)](insights-alerts-command-line-interface.md)
- [L’API REST Azure Insights](https://msdn.microsoft.com/library/azure/dn931945.aspx)

<!---HONumber=AcomDC_0928_2016-->