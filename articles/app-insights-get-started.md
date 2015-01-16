<properties title="Application Insights" pageTitle="Application Insights : analysez l'intégrité et l'utilisation de votre application" description="Analysez l'utilisation, la disponibilité et les performances de votre application web locale ou Microsoft Azure avec Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Application Insights : analysez l'intégrité et l'utilisation de votre application

*Application Insights est à l'état de version préliminaire.*

Application Insights vous permet d'analyser les éléments suivants de votre application en direct :

* **Disponibilité** - Nous testons vos URL à des intervalles de quelques minutes dans le monde entier.
* **Performances** - Détectez et diagnostiquez les problèmes et exceptions de performances.
* **Utilisation** - Découvrez ce que les utilisateurs font avec votre application afin de l'améliorer et mieux les satisfaire.

La configuration est très facile, et les résultats sont visibles après quelques minutes. Nous prenons actuellement en charge les applications Web ASP.NET (sur vos propres serveurs ou sur Azure).


## Prise en main

Commencez par choisir des points d'entrée sur la gauche de ce graphique, dans l'ordre de votre choix. Choisissez les itinéraires qui répondent à vos besoins. Si vous développez une application web ASP.NET, commencez par ajouter Application Insights à votre projet web. Vous n'aurez aucune difficulté à ajouter les autres composants ultérieurement.

Vous devez posséder un compte dans [Microsoft Azure](http://azure.com) (sauf si vous utilisez la version de Visual Studio Online).

<table >
<tr valign="top"><th>Ce dont vous avez besoin</th><th colspan="2">Ce que vous devez faire</th><th>Ce que vous obtenez</th></tr>
<tr valign="top"><td>Obtenir une analyse des performances et de l'utilisation de mon application ASP.NET</td><td colspan="2"><a href="../app-insights-start-monitoring-app-health-usage/">Ajouter Application Insights à votre projet web</a></td><td>Mesures de performances : nombre de charges, temps de réponse...</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Envoyer des événements et des mesures à partir de votre code serveur</a></td><td>Analyse marketing personnalisée</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Envoyer des données de télémétrie concernant le suivi et les exceptions à partir de votre serveur ou capturer les données de journalisation tierces.</td><td>Diagnostic des applications serveur. Rechercher et filtrer les données de journalisation.</a></td></tr>
<tr valign="top"><td>Obtenir une analyse de l'utilisation de mes pages web (sur toute plateforme)</td><td colspan="2"><a href="../app-insights-web-track-usage/">Insérer le script d'intelligence artificielle dans vos pages web</a></td><td>Analyse de l'utilisation : nombre de pages vues, utilisateurs inscrits, nombre de sessions</td></tr>
<tr valign="top"><td></td><td>&nbsp;&nbsp;</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Écrire des appels d'événement et de mesure dans les scripts de vos pages web</a></td><td>Analyse de l'expérience utilisateur personnalisée</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Écrire des appels de suivi et de diagnostic dans les scripts de vos pages web</a></td><td>Rechercher et filtrer les données de journalisation.</td></tr>
<tr valign="top"><td>Diagnostiquer les problèmes dans une application ASP.NET en cours d'exécution sur mon serveur web</td><td colspan="2"><a href="../app-insights-monitor-performance-live-website-now/">Installer un moniteur d'état sur votre serveur web</a></td><td>Durée et nombre des appels de dépendance ; compteurs de processeur, mémoire et réseau ; nombre de charges, temps de réponse</td></tr>
<tr valign="top"><td>Analyser la disponibilité de toutes les pages web</td><td colspan="2"><a href="../app-insights-monitor-web-app-availability/">Configurer des tests web sur Application Insights</a></td><td>Analyse de la disponibilité et alertes</td></tr>
<tr valign="top"><td>Obtenir une analyse des performances et de l'utilisation des applications Windows Phone, des applications Windows Store ou des sites web Java</td><td colspan="2"><a href="http://msdn.microsoft.com/library/dn481095.aspx">Pour l'instant, utiliser l'ancienne version de Visual Studio Online</a></td><td>Analyse de l'utilisation et des performances. <a href="http://msdn.microsoft.com/library/dn793604.aspx">Nous générons progressivement des fonctionnalités dans la version Azure.</a></td></tr>
</table>


## <a name="video"></a>Vidéos

#### Introduction

> [AZURE.VIDEO application-insights-introduction]

#### Prise en main

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]


