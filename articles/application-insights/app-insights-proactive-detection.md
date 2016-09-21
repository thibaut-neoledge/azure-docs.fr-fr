<properties 
	pageTitle="Détection proactive dans Application Insights | Microsoft Azure" 
	description="Application Insights réalise une analyse télémétrique approfondie automatique de votre application et vous avertit des éventuels problèmes de performances." 
	services="application-insights" 
    documentationCenter="windows"
	authors="rakefetj" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="awills"/>

#  Détection proactive dans Application Insights

 La détection proactive vous informe automatiquement des éventuels problèmes de performances dans votre application web. Ils effectuent des analyses intelligentes des données de télémétrie que votre application envoie à [Visual Studio Application Insights](app-insights-overview.md). S’ils détectent une augmentation soudaine du taux d’échec, ou des modèles anormaux de performances client ou serveur, vous recevez une alerte. Cette fonctionnalité ne nécessite aucune configuration. Elle fonctionne si votre application envoie suffisamment de données de télémétrie.

Vous pouvez accéder aux alertes de détection proactive via les courriers électroniques que vous recevez et à partir du panneau de détection proactive.



## Consulter vos détections proactives

Vous pouvez découvrir des détections de deux manières :

* **Vous recevez un message électronique** de la part d’Application Insights. Voici un exemple typique :

    ![Alerte par courrier électronique](./media/app-insights-proactive-detection/03.png)

    Cliquez sur le grand bouton pour afficher plus de détails dans le portail.

* La **mosaïque Détection proactive** figurant sur le panneau d’aperçu de votre application indique le nombre d’alertes récentes. Cliquez sur la mosaïque pour afficher la liste des alertes récentes.

![Afficher les détections récentes](./media/app-insights-proactive-detection/04.png)

Sélectionnez une alerte pour afficher les détails la concernant.


## Quels sont les problèmes détectés ?

Il existe trois types de détection :

* [Alertes d’échec en temps quasi-réel](app-insights-proactive-failure-diagnostics.md). Nous utilisons l’apprentissage automatique pour définir la fréquence attendue des demandes entraînant un échec pour votre application, en la mettant en corrélation avec la charge et d’autres facteurs. Si le taux d’échec est situé en dehors de la plage attendue, nous envoyons une alerte.
* [Diagnostics d’anomalies](app-insights-proactive-anomaly-diagnostics.md). Nous recherchons des modèles anormaux en matière de temps de réponse et de taux d’échec tous les jours. Nous mettons en corrélation ces problèmes avec des propriétés telles que l’emplacement, le navigateur, le système d’exploitation client, l’instance de serveur et le moment de la journée.
* [Azure Cloud Services](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Vous recevez des alertes si votre application est hébergée dans Azure Cloud Services et qu’une instance de rôle présente des échecs de démarrage, un recyclage fréquent ou des erreurs d’exécution.

(Les liens d’aide dans chaque notification vous renvoient vers les articles pertinents.)


## Étapes suivantes

Ces outils de diagnostic vous aident à inspecter les données de télémétrie à partir de votre application :

* [Metrics Explorer](app-insights-metrics-explorer.md)
* [Navigateur de recherche](app-insights-diagnostic-search.md)
* [Analytics : un puissant langage de requête](app-insights-analytics-tour.md)

Les détections proactives sont entièrement automatiques. Mais vous souhaitez peut-être configurer des alertes supplémentaires ?

* [Alertes de mesures configurées manuellement](app-insights-alerts.md)
* [Tests web de disponibilité](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->