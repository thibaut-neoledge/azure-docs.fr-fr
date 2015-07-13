<properties 
	pageTitle="Mise à niveau à partir de l'ancienne version de Visual Studio Online d'Application Insights" 
	description="Mise à niveau des projets existants"
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
	ms.date="06/19/2015" 
	ms.author="awills"/>
 
# Mise à niveau à partir de l'ancienne version de Visual Studio Online d'Application Insights

Ce document est susceptible de vous intéresser uniquement si vous avez un projet qui utilise encore l'ancienne version d'Application Insights, qui faisait partie de Visual Studio Online. Cette version sera désactivée en temps voulu, et par conséquent nous vous encourageons effectuer une mise à niveau vers la nouvelle version, qui est un service dans Microsoft Azure.

## Quelle est la version que j'utilise ?

Si vous avez ajouté Application Insights à votre projet avec Visual Studio 2013 Update 3 ou une version ultérieure, il utilise probablement la nouvelle version d'Azure.

Ouvrez ApplicationInsights.config. Si les nœuds `ActiveProfile` et `Profiles` sont présents, il s'agit de l'ancienne version et vous devez la mettre à niveau.

Sinon, examinez votre projet dans l'Explorateur de solutions Visual Studio et sous Références, sélectionnez Microsoft.ApplicationInsights. Dans la fenêtre Propriétés, recherchez la version. Si elle est inférieure à 0,12, vous devez effectuer une mise à niveau.

## Si vous avez un projet Visual Studio...

1. Ouvrez le projet dans Visual Studio 2013 Update 3 ou version ultérieure.
2. Supprimez ApplicationInsights.config. 
3. Supprimez les packages Application Insights NuGet du projet. Cliquez avec le bouton droit sur votre projet dans l'Explorateur de solutions, puis sélectionnez Gérer les packages NuGet.

    ![](./media/app-insights-upgrade-vso-azure/nuget.png)
4. Supprimez le dossier AppInsightsAgent et les fichiers qu'il contient.

    ![](./media/app-insights-upgrade-vso-azure/folder.png)

5. Supprimez tous les noms de paramètres Microsoft.AppInsights et les valeurs de ServiceDefinition.csdef et ServiceConfiguration.csfg

    ![](./media/app-insights-upgrade-vso-azure/csdef.png)
4. SDK : cliquez avec le bouton droit et [sélectionnez Ajouter Application Insights][greenbrown]. Cette opération ajoute le Kit de développement logiciel (SDK) à votre projet et crée également une ressource Application Insights dans Azure.
5. Journalisation : si votre code comprend des appels à l'ancienne API, comme LogEvent(), vous les découvrirez lorsque vous essayez de générer la solution. Mettez-le à jour pour pouvoir [utiliser la nouvelle API][track].
6. Pages web : si votre projet comprend des pages web, remplacez les scripts dans les sections <head>. En général, il n'y a qu'une seule copie de la page maître, comme Views\Shared_Layout.cshtml. [Obtenir le nouveau script à partir du panneau de démarrage rapide dans vos ressources Application Insights dans Azure][usage]. Si vos pages web incluent des appels de télémétrie, comme logEvent ou logPage, [mettez-les à jour pour qu'ils utilisent la nouvelle API][api].
7. Surveillance du serveur : si votre application est un service s'exécutant sur IIS, désinstallez l'Agent de surveillance Microsoft du serveur, puis [installez Application Insights Status Monitor][redfield].
8. Tests web : si vous utilisiez des tests de disponibilité du site web, [recréez-les sur le nouveau portail][availability], avec leurs alertes.
9. Alertes : configurer des [alertes sur des mesures][alerts] dans le portail Azure.


## Si vous disposez d'un service web Java...

1. Sur votre serveur, désactivez l'ancien agent en supprimant les références à l'agent APM dans le fichier de démarrage du service web. Sur les serveurs TomCat, modifiez Catalina.bat. Sur les serveurs JBoss, modifiez Run.bat. 
2. Redémarrez le service web.
3. Dans le portail Microsoft Azure, [ajoutez une nouvelle ressource Application Insights][java]. Sur votre ordinateur de développement, ajoutez le [kit de développement logiciel (SDK) Java][java] à votre projet web.
4. Remplacez les scripts dans les sections <head> de vos pages web. (Il peut y avoir une seule copie côté serveur.) [Obtenir le nouveau script à partir du panneau de démarrage rapide dans vos nouvelles ressources Application Insights dans Azure][usage]. Si vos pages web incluent des appels de télémétrie, comme logEvent ou logPage, [mettez-les à jour pour qu'ils utilisent la nouvelle API][track].
8. Tests web : si vous utilisiez des tests de disponibilité du site web, [recréez-les sur le nouveau portail][availability], avec leurs alertes.
9. Alertes : configurer des [alertes sur des mesures][alerts] dans le portail Azure.



<!--Link references-->

[alerts]: app-insights-alerts.md
[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->