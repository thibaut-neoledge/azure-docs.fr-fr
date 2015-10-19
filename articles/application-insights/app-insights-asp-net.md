<properties 
	pageTitle="Application Insights pour ASP.NET" 
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
	ms.date="10/05/2015" 
	ms.author="awills"/>


# Application Insights pour ASP.NET

*Application Insights est à l'état de version préliminaire.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) surveille votre application en direct pour vous aider à [détecter et diagnostiquer les problèmes de performances et les exceptions][detect], mais aussi à [découvrir comment votre application est utilisée][knowUsers]. Il peut être utilisé avec de nombreux types d’application. Il fonctionne pour les applications hébergées sur vos propres serveurs locaux IIS ou sur les machines virtuelles Azure, ainsi que les applications Web Azure. ([Les applications pour appareils et les serveurs Java sont aussi compris][start].)

![Exemples de graphiques d’analyse des performances](./media/app-insights-asp-net/10-perf.png)


#### Avant de commencer

Ce dont vous avez besoin :

* Un abonnement à [Microsoft Azure](http://azure.com). Si votre équipe ou votre organisation dispose d’un abonnement Azure, le propriétaire peut vous y ajouter à l’aide de votre [compte Microsoft](http://live.com).
* Visual Studio 2013 Update 3 ou version ultérieure.

## <a name="ide"></a> Ajouter Application Insights à votre projet dans Visual Studio

#### S'il s'agit d'un nouveau projet...

Lorsque vous créez un projet dans Visual Studio, assurez-vous que Application Insights est sélectionné.


![Création d'un projet ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### ... ou s'il s'agit d'un projet existant

Cliquez avec le bouton droit de la souris sur le projet dans l'Explorateur de solutions, puis sélectionnez Ajouter Application Insights.

![Sélection de Ajouter Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### Options d'installation

S'il s'agit de votre toute première utilisation, vous êtes invité à vous connecter ou à vous inscrire à Microsoft Azure.

Si cette application fait partie d'une application plus importante, vous pouvez utiliser **Configurer les paramètres** pour la placer dans le même groupe de ressources que les autres composants.


####<a name="land"></a> Quelle est la fonction de la commande « Ajouter Application Insights » ?

Cette commande effectue les opérations suivantes (que vous pouvez [exécuter manuellement](app-insights-start-monitoring-app-health-usage.md) si vous préférez) :

* Elle crée une ressource Application Insights dans [le portail Azure][portal]. Il s’agit de l’endroit où s’afficheront vos données. Elle récupère la *clé d’instrumentation*, qui identifie la ressource.
* Elle ajoute le package NuGet du Kit de développement logiciel (SDK) Web Application Insights à votre projet. Pour le visualiser dans Visual Studio, cliquez avec le bouton droit sur votre projet et choisissez Gérer les packages NuGet.
* Elle place la clé d’instrumentation dans `ApplicationInsights.config`.


## <a name="run"></a> Exécution de votre projet

Exécutez votre application à l'aide de la touche F5 et essayez-la : ouvrez différentes pages pour générer des données de télémétrie.

Un décompte des événements envoyés s'affiche dans Visual Studio.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Ouvrir Application Insights

Ouvrez votre ressource Application Insights dans le [portail Azure][portal].

![Cliquez avec le bouton droit de la souris sur votre projet et ouvrez le portail Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)


Recherchez des données dans les graphiques de présentation. Au début, seuls un ou deux points s'affichent. Par exemple :

![Cliquez sur d'autres éléments pour afficher plus de données](./media/app-insights-asp-net/12-first-perf.png)

Cliquez sur un des graphiques pour afficher des métriques plus détaillées. [En savoir plus sur les métriques.][perf]

À présent, déployez votre application et regardez les données s'accumuler.


Lorsque vous exécutez l'application en mode débogage, la télémétrie est envoyée via le pipeline, ce qui vous permet d'accéder aux données en quelques secondes. Lorsque vous déployez votre application, les données s'accumulent plus lentement.

#### Pas de données ?

* Assurez-vous que vous regardez les bonnes informations. Connectez-vous au [portail Azure](https://portal.azure.com), cliquez sur « Parcourir », « Application Insights », puis sélectionnez votre application.
* Utilisez l'application en ouvrant différentes pages pour générer des données de télémétrie.
* Ouvrez le panneau [Rechercher][diagnostic] pour afficher les événements individuels. Parfois, les événements mettent un peu plus de temps pour parcourir le pipeline de métriques.
* Attendez quelques secondes, puis cliquez sur Actualiser.
* Consultez la rubrique [Résolution des problèmes][qna].

#### Vous rencontrez des problèmes sur votre serveur de builds ?

Consultez cet article de [résolution des problèmes](app-insights-troubleshoot-faq.md#NuGetBuild).


## Ajouter une surveillance du navigateur

La surveillance du navigateur vous fournit des données sur les utilisateurs, les sessions, les pages consultées et les exceptions ou incidents qui se produisent dans le navigateur.

![Cliquez sur Nouveau, Services de développement, Application Insights.](./media/app-insights-asp-net/16-page-views.png)

Vous pourrez également écrire votre propre code pour suivre la façon dont les utilisateurs travaillent avec votre application, même jusqu'au niveau du nombre de clics et de frappes sur les touches.

Ajoutez un extrait de code JavaScript à chaque page. Récupérez le code à partir de votre ressource Application Insights :

![Dans votre application web, ouvrez Démarrage rapide, puis cliquez sur Obtenir le code pour analyser mes pages web.](./media/app-insights-asp-net/02-monitor-web-page.png)

Notez que le code contient la clé d’instrumentation qui identifie la ressource de votre application.

[En savoir plus sur le suivi de page web.](app-insights-web-track-usage.md)


## Suivi de l’utilisation

Après avoir proposé une nouvelle solution à un utilisateur, vous souhaitez savoir dans quelle proportion vos clients l’ont adoptée et s’ils ont atteint leurs objectifs ou rencontrent des difficultés. Bénéficiez d’un aperçu détaillé de l’activité des utilisateurs en insérant un élément TrackEvent() et d’autres appels dans votre code, à la fois côté client et côté serveur.

[Suivi de l’utilisation à l’aide de l’API][api]


## Journaux de diagnostic

[Capturez le suivi du journal][netlogs] dans votre framework de journalisation favori pour faciliter le diagnostic des problèmes. Vos entrées de journal apparaissent dans [Recherche de diagnostic][diagnostic] avec les événements de télémétrie Application Insights.

## Publier votre application

Si vous n’avez pas encore publié votre application (depuis que vous avez ajouté Application Insights), faites-le maintenant. Observez l’évolution des données dans les graphiques à mesure que les personnes utilisent votre application.


#### Les données n’apparaissent pas après leur publication sur votre serveur ?

Ouvrez ces ports pour le trafic sortant dans le pare-feu de votre serveur :

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


## Développement, test et release

Dans le cas d’une application majeure, il est conseillé de transmettre les données de télémétrie de différents horodatages (builds de débogage, test et production) dans des [ressources distinctes](app-insights-separate-resources.md).

## Suivi de la version de l'application

Assurez-vous que `buildinfo.config` est généré par votre processus de génération. Dans votre fichier .csproj, ajoutez :

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Lorsqu'il détient les informations de build, le module Web Application Insights ajoute automatiquement la **version de l'application** en tant que propriété à chaque élément de télémétrie. Cela vous permet de filtrer par version lors de l'exécution de [recherches de diagnostic][diagnostic] ou lors de l’[exploration de mesures][metrics].



## Ajouter un suivi de dépendance et des compteurs de performances système

Les [mesures de dépendance](app-insights-dependencies.md) peuvent vous être très utiles pour diagnostiquer les problèmes de performance. Ils comptabilisent les appels de votre application vers les bases de données, les API REST et d’autres composants externes.

![](./media/app-insights-asp-net/04-dependencies.png)

Cette étape permet aussi d’établir un [rapport des compteurs de performance](app-insights-web-monitor-performance.md#system-performance-counters), tels que le taux d’utilisation du processeur, de la mémoire et du réseau.

#### Si votre application s'exécute sur votre serveur IIS

Connectez-vous à votre serveur avec des droits d’administrateur, puis installez [Application Insights Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).

Vous devez vous assurer que certains [autres ports sont ouverts dans le pare-feu de votre serveur](app-insights-monitor-performance-live-website-now.md#troubleshooting).

#### Si votre application est une application web Azure

Dans le panneau de configuration de votre application web Azure, ajoutez l’extension Application Insights.

![Dans votre application web, Paramètres, Extensions, Ajouter, Application Insights](./media/app-insights-asp-net/05-extend.png)

(Vous pouvez également ajouter l’extension à une application qui est déjà active, même si vous n’avez pas installé le Kit de développement logiciel (SDK) qu’elle contient).

#### Pour surveiller les rôles de services cloud Azure

Il existe une [procédure manuelle pour ajouter le moniteur d’état](app-insights-cloudservices.md).

## Tests web de disponibilité

[Configurez des tests Web][availability] pour vérifier depuis l’extérieur que votre application est opérationnelle et réactive.


![](./media/app-insights-asp-net/appinsights-10webtestresult.png)






## Pour passer aux versions ultérieures du Kit de développement logiciel (SDK)

Pour passer à la [nouvelle version du Kit de développement logiciel (SDK)](app-insights-release-notes-dotnet.md), ouvrez une nouvelle fois le gestionnaire de package NuGet et filtrez les packages qui ont été installés. Sélectionnez Microsoft.ApplicationInsights.Web et choisissez Mettre à niveau.

Si vous avez apporté des personnalisations à ApplicationInsights.config, conservez-en une copie avant d’effectuer la mise à niveau et fusionnez ensuite vos modifications dans la nouvelle version.



## <a name="video"></a>Vidéo

> [AZURE.VIDEO getting-started-with-application-insights]


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
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 

<!---HONumber=Oct15_HO2-->