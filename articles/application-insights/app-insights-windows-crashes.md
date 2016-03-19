<properties 
	pageTitle="Détection et diagnostic des incidents dans les applications Windows Store et Windows Phone avec Application Insights" 
	description="Analysez les problèmes de performances de votre application pour appareil Windows avec Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2015" 
	ms.author="awills"/>

# Détection et diagnostic des incidents dans les applications Windows Store et Windows Phone avec Application Insights

*Application Insights est à l'état de version préliminaire.*

Si vos utilisateurs rencontrent des incidents lors de l’utilisation de votre application, vous devez le savoir rapidement et obtenir le plus d’informations sur les conditions de ces incidents. Avec Application Insights, vous pouvez surveiller la fréquence à laquelle se produisent ces incidents, recevoir des alertes lorsqu’ils surviennent et étudier des rapports les concernant.

Le terme « incident » signifie que l'application s'arrête en raison d'une exception non interceptée. Si votre application intercepte une exception, vous pouvez l’intégrer dans un rapport avec [l’API TrackException][apiexceptions] tout en continuant l’exécution de l’application. Dans ce cas, cette exception ne sera pas enregistrée comme un incident.


## Surveillance de la fréquence d'incident

Si vous ne l'avez pas encore fait, ajoutez [Application Insights à votre projet d’application][windows] et publiez-le de nouveau.

Les incidents sont affichés sur le volet Vue d’ensemble de votre application dans le [portail Application Insights][portal].

![](./media/app-insights-windows-crashes/appinsights-d018-oview.png)

Vous pouvez modifier la période indiquée par le graphique.


## Configuration d’une alerte pour détecter les incidents

![À partir du graphique d’incidents, cliquez sur Règles d'alerte, puis sur Ajouter une alerte](./media/app-insights-windows-crashes/appinsights-d023-alert.png)

## Diagnostic des incidents

Pour savoir si certaines versions de votre application sont plus sujettes aux incidents que d’autres, cliquez sur le graphique des incidents et répartissez les résultats en fonction des versions de l’application :

![](./media/app-insights-windows-crashes/appinsights-d26crashSegment.png)


Pour découvrir les exceptions qui provoquent des incidents, ouvrez Recherche de diagnostic. Si vous souhaitez supprimer les autres types de données de télémétrie pour vous concentrer sur ces exceptions :

![](./media/app-insights-windows-crashes/appinsights-d26crashExceptions.png)

[En savoir plus sur le filtrage dans Recherche de diagnostic][diagnostic]
 

Cliquez sur n'importe quelle exception pour afficher ses détails, notamment son arborescence des appels de procédure et ses propriétés associées.

![](./media/app-insights-windows-crashes/appinsights-d26crash.png)

Consultez les autres exceptions et événements qui se sont produits peu de temps avant ou après cette exception :


![](./media/app-insights-windows-crashes/appinsights-d26crashRelated.png)

## Insertion d’événements et de journaux de suivi

Pour faciliter le diagnostic des problèmes, vous pouvez [insérer un suivi des appels et rechercher les journaux dans Application Insights][diagnostic].

## <a name="debug"></a>Comparaison entre le mode de débogage et le mode de version finale

#### Déboguer

Si vous développez en mode débogage, les événements sont envoyés dès qu'ils sont générés. Si vous êtes déconnecté d’Internet, puis que vous quittez l’application avant de vous reconnecter, la télémétrie hors connexion est ignorée.

#### Version finale

Si vous développez en configuration de version finale, les événements sont stockés dans l’appareil, puis envoyés lors de la reprise de l’application. Les données sont également envoyées lors de la première utilisation de l’application. S'il vous n’êtes pas connecté à Internet lors du démarrage, les données de télémétrie précédentes, tout comme celles du cycle de vie actuel, sont stockées, puis envoyées lors de la prochaine connexion.

## <a name="next"></a>Étapes suivantes

[Problèmes de détection, de tri et de diagnostic avec Application Insights][detect]

[API Application Insights][api]

[Capture des journaux de diagnostic][trace]

[Résolution des problèmes](app-insights-windows-troubleshoot.md)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1203_2015-->