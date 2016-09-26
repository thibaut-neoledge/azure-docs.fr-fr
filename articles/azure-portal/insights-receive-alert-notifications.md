<properties
	pageTitle="Réception de notifications d’alerte pour les services Azure | Microsoft Azure"
	description="Soyez averti quand des conditions de règles d'alerte sont remplies."
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
	ms.date="09/08/2015"
	ms.author="robb"/>

# Réception de notifications d'alerte

Vous pouvez recevoir une alerte en fonction de métriques de surveillance pour vos services Azure ou d'événements sur ces derniers.

Une règle d'alerte, définie sur une valeur métrique, s'active et peut envoyer une notification quand la valeur de la métrique spécifiée dépasse un seuil affecté. Une règle d'alerte, définie sur des événements, peut envoyer une notification à *chaque* événement ou uniquement quand un certain nombre d'événements se produisent.

Quand vous créez une règle d'alerte, vous pouvez sélectionner des options pour envoyer une notification par courrier électronique à l'administrateur de services fédérés et ses co-administrateurs ou à un autre administrateur que vous pouvez spécifier. Une notification par courrier électronique est envoyée quand la règle s'active et quand une condition d'alerte est résolue.

Vous pouvez utiliser l'[API REST](https://msdn.microsoft.com/library/azure/dn931945.aspx) ou le [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour configurer et obtenir par programme des informations sur les règles d'alerte.

## Création d'une règle d'alerte

1. Dans le [portail](https://portal.azure.com/), cliquez sur **Parcourir**, puis sur une ressource que vous voulez surveiller.

2. Cliquez sur la vignette **Règles d'alerte** vignette dans le filtre **Opérations**.

3. Cliquez sur la commande **Ajouter une alerte**.

	![Ajouter une alerte](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Vous pouvez nommer votre règle d'alerte et choisir la description à afficher dans le message électronique de notification.

5. Quand vous sélectionnez **Mesures**, vous allez choisir une condition et une valeur de seuil pour la métrique. Il s'agit de la période utilisée par Azure pour surveiller et tracer l'activité d'alerte.

	![Condition et seuil](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. Vous pouvez également choisir **Événements**, puis recevoir une notification quand un certain événement se produit.

	![Événements](./media/insights-receive-alert-notifications/Insights_Events.png)

7. Enfin, vous pouvez choisir d'envoyer la notification par courrier électronique aux administrateurs concernés.

Après avoir cliqué sur **Enregistrer**, vous êtes informé dans les minutes qui suivent de tout dépassement du seuil par la métrique sélectionnée.

## Gestion de vos règles d'alerte

Une fois que vous avez créé une règle d'alerte, vous pouvez afficher un aperçu de votre seuil d'alerte par rapport à la métrique du jour précédent.

![Événements](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Vous pouvez bien entendu modifier cette règle d'alerte, la **désactiver** ou l'**activer** si vous voulez temporairement arrêter de recevoir des notifications à son sujet.

## Étapes suivantes

* [Configurer des webhooks sur vos alertes](insights-webhooks-alerts.md) pour acheminer des notifications vers différents canaux
* [Surveillance des mesures de service](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
* [Activation de la surveillance et des diagnostics](insights-how-to-use-diagnostics.md) pour collecter des mesures détaillées à fréquence élevée sur votre service.
* [Surveillance de la disponibilité et de la réactivité des pages web](../application-insights/app-insights-monitor-web-app-availability.md) avec Application Insights pour déterminer si vos pages sont inactives.
* [Surveillance des performances d'une application](../application-insights/app-insights-azure-web-apps.md) si vous voulez comprendre exactement comment votre code s'exécute dans le cloud.
* [Affichage des événements et journaux d'audit](insights-debugging-with-events.md) pour découvrir tout ce qui s'est produit dans votre service.
* [Suivi de l'intégrité du service](insights-service-health.md) pour déterminer à quel moment Azure a rencontré des interruptions de service ou une dégradation des performances.

<!---HONumber=AcomDC_0914_2016-->