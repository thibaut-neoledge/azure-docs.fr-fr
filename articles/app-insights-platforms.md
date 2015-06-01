<properties 
	pageTitle="Application Insights : plateformes" 
	description="Puis-je utiliser Application Insights avec... ?" 
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
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# Application Insights : plateformes

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

#### Puis-je utiliser Application Insights avec... ?


## Langages :

+ [C#, VB](app-insights-start-monitoring-app-health-usage.md)
+ [Pages web JavaScript](app-insights-web-track-usage.md)
+ [Applications JavaScript Windows Store](#cordova)
+ [Java](app-insights-java.md)
+ [Ruby](https://rubygems.org/gems/application_insights) 
+ [PHP](https://github.com/Microsoft/AppInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)

## Plateformes

+ [ASP.NET](app-insights-start-monitoring-app-health-usage.md)
+ [Applications web Azure et machines virtuelles](insights-perf-analytics.md)
+ [Android](https://github.com/Microsoft/AppInsights-Android)
+ [iOS](https://github.com/Microsoft/AppInsights-iOS)
+ [Cordova](#cordova)
+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)
+ [Ordinateurs Windows](app-insights-windows-desktop.md)


## Frameworks de journalisation

+	[Log4Net, NLog ou System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java, Log4J ou Logback](app-insights-java-trace-logs.md)


## Projets

Consultez également le projet du [Kit de développement logiciel (SDK) Application Insights sur GitHub](https://github.com/Microsoft/AppInsights-Home)


### <a name="cordova"></a>Applications JavaScript Cordova et Windows Store

Dans Visual Studio, cliquez avec le bouton droit sur votre projet et choisissez **Gérer les packages NuGet**.

Sélectionnez **En ligne** et recherchez Application Insights.

Installez l’**API Application Insights pour les applications JavaScript**.

Utilisez le [script d’application web](app-insights-web-track-usage.md) côté client, mais avec une modification.

Lorsque vous obtenez le script dans le portail Application Insights, insérez une ligne après la clé d’instrumentation :

    ...{
        instrumentationKey:"00000000-662d-4479-0000-40c89770e67c",
        endpointUrl:"https://dc.services.visualstudio.com/v2/track"
    } ...

[Cordova](http://cordova.apache.org/)

[Applications Windows Store en JavaScript](https://msdn.microsoft.com/library/windows/apps/br211385.aspx)

<!--Link references-->



<!--HONumber=54-->