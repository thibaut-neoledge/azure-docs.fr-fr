<properties 
	pageTitle="Diagnostic des problèmes liés aux dépendances dans Application Insights" 
	description="Rechercher les échecs et les faibles performances causées par les dépendances" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2016" 
	ms.author="awills"/>
 
# Diagnostic des problèmes liés aux dépendances dans Application Insights


Un *dépendance* est un composant externe qui est appelé par votre application. Il s’agit habituellement d’un service appelé à l’aide de HTTP, d’une base de données ou d’un système de fichiers. Ou, dans le script de votre page web, il peut s’agir d’un renvoi d’appel AJAX vers le serveur. Visual Studio Application Insights vous permet de voir facilement combien de temps votre application attend les dépendances et la fréquence à laquelle un appel de dépendance échoue.

## Où vous pouvez l’utiliser

Une surveillance des dépendances est actuellement disponible en standard pour :

* les applications et les services web ASP.NET qui s’exécutent sur un serveur IIS ou sur Azure
* [Applications web Java](app-insights-java-agent.md)
* [Pages web](https://azure.microsoft.com/blog/ajax-collection-in-application-insights/)

Pour les autres types, tels que les applications pour appareil, vous pouvez créer votre propre surveillance à l’aide de l’API [TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).

Le moniteur de dépendance prêt à l’emploi signale les appels aux types de dépendances suivants :

* ASP.NET
 * Bases de données SQL
 * Services web et WCF d’ASP.NET qui utilisent des liaisons HTTP
 * Appels HTTP locaux ou distants
 * Azure DocumentDb, table, stockage d’objets blob et file d’attente
* Java
 * Appels effectués vers une base de données par le biais d’un pilote [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), comme MySQL, SQL Server, PostgreSQL ou SQLite.
* Pages web
 * [Appels AJAX](app-insights-javascript.md)

Là encore, vous pouvez écrire vos propres appels au kit de développement logiciel (SDK) pour surveiller d’autres dépendances.

## Pour configurer la surveillance des dépendances

Installez l’agent approprié pour le serveur hôte.

Plateforme | Installer
---|---
Serveur IIS | [Installez Status Monitor sur votre serveur](app-insights-monitor-performance-live-website-now.md) ou [mettez à niveau votre application vers .NET Framework version 4.6 ou ultérieure](http://go.microsoft.com/fwlink/?LinkId=528259) et installez le [Kit de développement logiciel (SDK) Application Insights](app-insights-asp-net.md) dans votre application.
Application web Azure | [Extension Application Insights](app-insights-azure-web-apps.md)
Serveur web Java | [Applications web Java](app-insights-java-agent.md)
Pages web | [Moniteur JavaScript](app-insights-javascript.md) (sans configuration supplémentaire en dehors de la surveillance de pages web)
Azure Cloud Services | [Utilisez une tâche de démarrage](app-insights-cloudservices.md#dependencies) ou [installez .NET Framework version 4.6 ou ultérieure](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Status Monitor pour les serveurs IIS n’a pas besoin de régénérer votre projet source avec le Kit de développement logiciel (SDK) Application Insights.

## Plan de l’application

Le plan de l’application fonctionne comme une aide visuelle pour découvrir les dépendances entre les composants de votre application.

![Cliquez sur Paramètres, Plan de l’application](./media/app-insights-dependencies/08.png)

À partir des zones, vous pouvez accéder aux graphiques pertinents de dépendance et autres.

Cliquez sur la petite [x] pour réduire une sous-arborescence.

Épinglez la mise en correspondance sur le [tableau de bord](app-insights-dashboards.md), où elle sera entièrement opérationnelle.

[En savoir plus](app-insights-app-map.md).

## <a name="diagnosis"></a> Diagnostic des problèmes de performances liés aux dépendances sur le serveur web

Pour évaluer les performances des demandes au niveau de votre serveur :

![Dans Application Insights, dans la page Vue d’ensemble de votre application, cliquez sur la vignette de performances](./media/app-insights-dependencies/01-performance.png)

Faites défiler vers le bas pour accéder à la grille des demandes :

![Liste de demandes avec moyennes et nombres](./media/app-insights-dependencies/02-reqs.png)

La durée de la première est très longue. Examinons-la pour en savoir plus.

Cliquez sur cette ligne pour afficher les événements de la demande :


![Liste des occurrences de demande](./media/app-insights-dependencies/03-instances.png)

Cliquez sur n’importe quelle instance présentant une longue durée d’exécution pour l’examiner de plus près.

> [AZURE.NOTE] Faites défiler vers le bas pour choisir une instance. Une latence dans le pipeline peut expliquer que les données des premières instances soient incomplètes.

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


## Suivi personnalisé des dépendances

Le module de suivi des dépendances standard découvre automatiquement les dépendances externes, telles que des bases de données et des API REST. Mais vous souhaiterez peut-être traiter d’autres composants de la même façon.

Vous pouvez écrire du code qui envoie des informations de dépendance, en utilisant la même [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency) que celle utilisée par les modules standard.

Par exemple, si vous générez votre code avec un assembly que vous n’avez pas écrit vous-même, vous pouvez minuter tous les appels vers cet assembly afin de déterminer sa contribution dans votre temps de réponse. Pour afficher ces données dans les graphiques de dépendance d’Application Insights, envoyez-les en utilisant `TrackDependency`.

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Si vous souhaitez désactiver le module de suivi des dépendances standard, supprimez la référence à DependencyTrackingTelemetryModule dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).


## Ajax

Voir [Pages web](app-insights-javascript.md).


 

<!---HONumber=AcomDC_0914_2016-->