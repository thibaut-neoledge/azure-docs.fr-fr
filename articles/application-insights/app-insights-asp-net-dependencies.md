<properties 
	pageTitle="Suivi des dépendances dans Application Insights" 
	description="Analysez l'utilisation, la disponibilité et les performances de votre application web locale ou Microsoft Azure avec Application Insights." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/05/2015" 
	ms.author="awills"/>


# Configurer Application Insights : suivi des dépendances

*Application Insights est à l'état de version préliminaire.*


[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) surveille vos applications en direct pour vous aider à [détecter et diagnostiquer les problèmes de performances et les exceptions][detect], mais aussi [découvrir comment votre application est utilisée][knowUsers].


<a name="selector1"></a>

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]



Un *dépendance* est un composant externe qui est appelé par votre application. Il s’agit habituellement d’un service appelé à l’aide de HTTP, d’une base de données ou d’un système de fichiers. Visual Studio Application Insights vous permet de voir facilement combien de temps votre application attend les dépendances et la fréquence à laquelle un appel de dépendance échoue.

![Exemples de graphiques](./media/app-insights-asp-net-dependencies/10-intro.png)

Le moniteur de dépendance prêt à l’emploi signale les appels aux types de dépendances suivants :

* ASP.NET
 * Bases de données SQL
 * Services web et WCF d’ASP.NET qui utilisent des liaisons HTTP
 * Appels HTTP locaux ou distants
 * Azure DocumentDb, table, stockage d’objets blob et file d’attente
* Java
 * Appels effectués vers une base de données par le biais d’un pilote [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), comme MySQL, SQL Server, PostgreSQL ou SQLite.

Vous pouvez écrire vos propres appels de Kit de développement logiciel (SDK) pour surveiller d'autres dépendances à l'aide de l’[API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


## Pour configurer la surveillance des dépendances

Cette opération nécessite un abonnement [Microsoft Azure](http://azure.com).

### Si votre application s’exécute sur votre serveur IIS

1. Sur votre serveur web IIS, connectez-vous avec vos informations d’identification d’administrateur.
2. Téléchargez et exécutez le [programme d’installation Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).
4. Dans l'Assistant Installation, connectez-vous à Microsoft Azure.

    ![Connectez-vous à Azure avec les informations d’identification de votre compte Microsoft.](./media/app-insights-asp-net-dependencies/appinsights-035-signin.png)

    *Erreurs de connexion Consultez la rubrique [Résolution des problèmes](#troubleshooting).*

5. Sélectionnez l’application web installée ou le site web à surveiller, puis configurez la ressource dans laquelle vous voulez afficher les résultats dans le portail Application Insights.

    ![Choisissez une application et une ressource.](./media/app-insights-asp-net-dependencies/appinsights-036-configAIC.png)

    Il est probable que vous choisirez de configurer une nouvelle ressource et un nouveau [groupe de ressources][roles].

    Vous pouvez aussi utiliser une ressource existante si vous avez déjà configuré des [tests web][availability] pour votre site ou une [surveillance du client web][client].

6. Redémarrez IIS.

    ![Cliquez sur Redémarrer en haut de la boîte de dialogue.](./media/app-insights-asp-net-dependencies/appinsights-036-restart.png)

    Votre service web sera interrompu pendant une courte période.

6. Notez que ApplicationInsights.config est inséré dans les applications web que voulez surveiller.

    ![Recherchez le fichier .config et les fichiers de code de l’application web.](./media/app-insights-asp-net-dependencies/appinsights-034-aiconfig.png)

   web.config a également été légèrement modifié.

#### Vous voulez (re)configurer plus tard ?

Lorsque l'Assistant est terminé, vous pouvez reconfigurer l'agent à tout moment. Vous pouvez également reconfigurer si vous avez installé l'agent et avez rencontré des problèmes lors de la configuration initiale.

![Click the Application Insights icon on the task bar](./media/app-insights-asp-net-dependencies/appinsights-033-aicRunning.png)


### Si votre application s’exécute en tant qu’application web Azure

Dans le panneau de configuration de votre application web Azure, ajoutez l’extension Application Insights.

![Dans votre application web, Paramètres, Extensions, Ajouter, Application Insights](./media/app-insights-asp-net-dependencies/05-extend.png)


### S’il s’agit d’un projet services cloud Azure

[Ajoutez des scripts aux rôles web et de travail](app-insights-cloudservices.md).

## <a name="diagnosis"></a> Diagnostic des problèmes de performances liés aux dépendances

Pour évaluer les performances des demandes au niveau de votre serveur :

![Dans Application Insights, dans la page Vue d’ensemble de votre application, cliquez sur la vignette de performances](./media/app-insights-asp-net-dependencies/01-performance.png)

Faites défiler vers le bas pour accéder à la grille des demandes :

![Liste de demandes avec moyennes et nombres](./media/app-insights-asp-net-dependencies/02-reqs.png)

La durée de la première est très longue. Examinons-la pour en savoir plus.

Cliquez sur cette ligne pour afficher les événements de la demande :


![Liste des occurrences de demande](./media/app-insights-asp-net-dependencies/03-instances.png)

Cliquez sur n’importe quelle instance présentant une longue durée d’exécution pour l’examiner de plus près.

> [AZURE.NOTE]Faites défiler vers le bas pour choisir une instance. Une latence dans le pipeline peut expliquer que les données des premières instances soient incomplètes.

Faites défiler vers le bas pour atteindre les appels de dépendance à distance liés à cette demande :

![Rechercher des appels de dépendances distantes, identifier une durée anormale](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Il semble que la plupart du temps passé au traitement de cette demande ait été consacré à l’appel d’un service local.

Sélectionnez cette ligne pour obtenir plus d’informations :


![Cliquez sur cette dépendance distante pour identifier la cause](./media/app-insights-asp-net-dependencies/05-detail.png)

Les informations contenues dans le détail sont suffisantes pour diagnostiquer le problème.



## Échecs

Si des demandes ont échoué, cliquez sur le graphique.

![Cliquez sur le graphique des demandes ayant échoué](./media/app-insights-asp-net-dependencies/06-fail.png)

Cliquez sur un type de demande et une instance de demande pour rechercher un appel à une dépendance distante.


![Cliquez sur un type de demande, cliquez sur l’instance pour obtenir une vue différente de la même instance, cliquez dessus pour obtenir des informations relatives à l’exception.](./media/app-insights-asp-net-dependencies/07-faildetail.png)


## Suivi personnalisé des dépendances

Le module de suivi des dépendances standard découvre automatiquement les dépendances externes, telles que des bases de données et des API REST. Mais vous souhaiterez peut-être traiter d’autres composants de la même façon.

Vous pouvez écrire du code qui envoie des informations de dépendance, en utilisant la même [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency) que celle utilisée par les modules standard.

Par exemple, si vous générez votre code avec un assembly que vous n’avez pas écrit vous-même, vous pouvez minuter tous les appels vers cet assembly afin de déterminer sa contribution dans votre temps de réponse. Pour afficher ces données dans les graphiques de dépendance d’Application Insights, envoyez-les en utilisant `TrackDependency`.

```C#

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

## Étapes suivantes

- [Exceptions](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [Données utilisateur et de page](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-asp-net-dependencies.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Nov15_HO3-->