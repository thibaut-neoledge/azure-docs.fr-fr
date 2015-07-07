<properties 
	pageTitle="Diagnostic des problèmes liés aux dépendances dans Application Insights" 
	description="Rechercher les échecs et les faibles performances causées par les dépendances" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="awills"/>
 
# Diagnostic des problèmes liés aux dépendances dans Application Insights


Un *dépendance* est un composant externe qui est appelé par votre application. Il s’agit habituellement d’un service appelé à l’aide de HTTP, d’une base de données ou d’un système de fichiers. Visual Studio Application Insights vous permet de voir facilement combien de temps votre application attend les dépendances et la fréquence à laquelle un appel de dépendance échoue.

## Où vous pouvez l’utiliser

Une surveillance des dépendances est actuellement disponible en standard pour :

* les applications et les services web ASP.NET qui s’exécutent sur un serveur IIS ou sur Azure

Pour les autres types, tels que les applications web Java ou les applications d’appareil, vous pouvez créer votre propre surveillance à l’aide de l’API TrackDependency.

Le moniteur de dépendance prêt à l’emploi signale les appels aux types de dépendances suivants :

* Bases de données SQL
* Services web et wcf d’ASP.NET
* Appels HTTP locaux ou distants
* Azure DocumentDb, table, stockage d’objets blob et file d’attente

Là encore, vous pouvez écrire vos propres appels au kit de développement logiciel (SDK) pour surveiller d’autres dépendances.

## Configuration de la surveillance des dépendances

Pour obtenir la surveillance des dépendances, vous devez :

* Utiliser [Status Monitor](app-insights-monitor-performance-live-website-now.md) sur votre serveur IIS et l’utiliser pour activer la surveillance
* Ajouter l’[extension Application Insights](../insights-perf-analytics.md) à votre application web ou votre machine virtuelle Azure.

(Pour une machine virtuelle Azure, vous pouvez soit installer l’extension à partir du panneau de configuration Azure, ou installer Status Monitor comme vous le feriez sur n’importe quelle machine.)

Vous pouvez effectuer les étapes ci-dessus pour une application web déjà déployée. Pour obtenir la surveillance des dépendances standard, vous n’êtes pas obligé d’ajouter Application Insights à votre projet source.

## Diagnostic des problèmes de performances liés aux dépendances

Pour évaluer les performances des demandes au niveau de votre serveur :

![Dans Application Insights, dans la page Vue d’ensemble de votre application, cliquez sur la vignette de performances](./media/app-insights-dependencies/01-performance.png)

Faites défiler vers le bas pour accéder à la grille des demandes :

![Liste de demandes avec moyennes et nombres](./media/app-insights-dependencies/02-reqs.png)

La durée de la première est très longue. Examinons-la pour en savoir plus.

Cliquez sur cette ligne pour afficher les événements de la demande :


![Liste des occurrences de demande](./media/app-insights-dependencies/03-instances.png)

Cliquez sur n’importe quelle instance présentant une longue durée d’exécution pour l’examiner de plus près.

> [AZURE.NOTE]Faites défiler vers le bas pour choisir une instance. Une latence dans le pipeline peut expliquer que les données des premières instances soient incomplètes.

Faites défiler vers le bas pour atteindre les appels de dépendance à distance liés à cette demande :

![Rechercher des appels de dépendances distantes, identifier une durée anormale](./media/app-insights-dependencies/04-dependencies.png)

Il semble que la plupart du temps passé au traitement de cette demande ait été consacré à l’appel d’un service local.

Sélectionnez cette ligne pour obtenir plus d’informations :


![Cliquez sur cette dépendance distante pour identifier la cause](./media/app-insights-dependencies/05-detail.png)

Les informations contenues dans le détail sont suffisantes pour diagnostiquer le problème.



## Échecs

Si des demandes ont échoué, cliquez sur le graphique.

![Cliquez sur le graphique des demandes ayant échoué](./media/app-insights-dependencies/06-fail.png)

Cliquez sur un type de demande et une instance de demande pour rechercher un appel à une dépendance distante.


![Cliquez sur un type de demande, cliquez sur l’instance pour obtenir une vue différente de la même instance, cliquez dessus pour obtenir des informations relatives à l’exception.](./media/app-insights-dependencies/07-faildetail.png)


<!--Link references-->

<!---HONumber=62-->