<properties 
	pageTitle="Application Insights dans Microsoft Azure" 
	description="Détectez, triez et diagnostiquez des problèmes dans votre application Web ou un périphérique live. Surveillez et améliorez le succès auprès de vos utilisateurs en continu." 
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
	ms.date="03/31/2016" 
	ms.author="awills"/>
 
# Visual Studio Application Insights

Application Insights est un service d’analyse extensible qui surveille votre application en direct. Il vous permet de détecter et diagnostiquer les problèmes de performances et de comprendre ce que font les utilisateurs avec votre application. Il est conçu pour les développeurs, pour vous aider à améliorer en permanence les performances et la convivialité de votre application.

![Créez un graphique des statistiques d'activité de l’utilisateur ou explorez des événements spécifiques.](./media/app-service-app-insights-get-started/00-sample.png)

Il fonctionne avec les applications web et les applications autonomes sur un large éventail de plates-formes : .NET ou J2EE, hébergées sur site ou dans le cloud.

Application Insights est destiné à l'équipe de développement. Avec lui, vous pouvez :

* [Analyser les modèles d'utilisation][knowUsers] pour mieux comprendre vos utilisateurs et améliorer sans cesse votre application.
 * Nombre de pages consultées, les utilisateurs nouveaux et qui reviennent, la géolocalisation, les plateformes ainsi que d’autres analyses d’utilisation principales
 * Suivre les chemins d'utilisation pour évaluer la réussite de chaque fonctionnalité.
* [Détectez, triez et diagnostiquez][detect] les problèmes de performances et corrigez-les avant que la plupart de vos utilisateurs ne s’en rende compte.
 *  Alertes sur les changements de performances ou les incidents.
 *  Métriques permettant de diagnostiquer des problèmes de performances, comme les temps de réponse, l’utilisation du processeur et le suivi des dépendances.
 *  Tests de disponibilité pour les applications web.
 *  Alertes et rapports d’incidents et d’exceptions
 *  Puissante recherche des journaux de diagnostic (y compris le suivi des journaux à partir de vos frameworks de journalisation préférés).

Le Kit de développement logiciel (SDK) de chaque plateforme inclut une gamme de modules qui surveillent d’emblée l'application. En outre, vous pouvez coder vos propres mesures de télémétrie pour obtenir des analyses plus détaillées et personnalisées.

Les données de télémétrie recueillies à partir de votre application sont stockées et analysées dans le portail Azure, où des vues intuitives et de puissants outils accélèrent le diagnostic et l’analyse.



Vous voulez bénéficier d’une analyse encore plus approfondie ? [Exportez](app-insights-export-telemetry.md) vos données [vers SQL](app-insights-code-sample-export-telemetry-sql-database.md)[vers Power BI](app-insights-export-power-bi.md) ou vers vos propres outils.

![Affichage des données dans Power BI](./media/app-service-app-insights-get-started/210.png)

## Plateformes et langages

Il existe des Kits de développement logiciel pour un nombre croissant de plateformes. Actuellement, la liste comprend :

 * [Serveurs ASP.NET][greenbrown] sur Azure ou votre serveur IIS
 * [Azure Cloud Services](app-insights-cloudservices.md)
 * [les serveurs J2EE][java]
 * [les pages Web][client] : HTML + JavaScript
 * [services Windows, rôles de travail et applications de bureau][desktop]
 * [Autres plateformes][platforms] : Node.js, PHP, Python, Ruby, Joomla, SharePoint, WordPress

Application Insights peut également obtenir les mesures de télémétrie à partir des applications web ASP.NET existantes sur IIS, sans les recréer.

Si votre application comporte un client, un serveur et d’autres composants, vous pouvez tous les instrumenter. Les données seront intégrées au portail Application Insights pour vous permettre, par exemple, d’associer des événements au niveau du client à des événements au niveau du serveur.


## Fonctionnement

Vous installez un petit Kit de développement logiciel (SDK) dans votre application et configurez un compte dans le portail Application Insights. Le Kit de développement logiciel (SDK) surveille votre application et envoie les données de télémétrie au portail. Le portail vous montre des graphiques statistiques et fournit de puissants outils de recherche pour vous aider à diagnostiquer les problèmes.

![Le Kit de développement logiciel (SDK) Application Insights dans votre application envoie la télémétrie aux ressources Application Insights dans le portail Azure.](./media/app-service-app-insights-get-started/01-scheme.png)

Le Kit de développement logiciel (SDK) dispose de plusieurs modules qui recueillent les données de télémétrie, pour compter les utilisateurs, les sessions et les performances, par exemple. Vous pouvez également écrire votre propre code personnalisé pour envoyer des données de télémétrie au portail. La télémétrie personnalisée est particulièrement utile pour suivre les récits utilisateur : vous pouvez compter des événements comme les clics des boutons, la réalisation d’objectifs spécifiques ou les erreurs des utilisateurs.

Pour les serveurs ASP.NET et les applications web Azure, vous pouvez également installer [Status Monitor][redfield], qui a deux utilisations. Il vous permet de :

* surveiller une application web sans la régénérer ou la réinstaller.
* suivre les appels aux modules dépendants.



### Quelle est la surcharge ?

L'impact sur les performances est très faible. Le suivi des appels n’entraîne aucun blocage, et les appels sont regroupés par lots et envoyés dans un thread séparé.



## Pour commencer

1. Vous devrez vous abonner à [Microsoft Azure](http://azure.com). L'inscription est gratuite et vous pouvez choisir le [niveau de tarification](https://azure.microsoft.com/pricing/details/application-insights/) gratuit d'Application Insights.

2. Si vous utilisez Visual Studio :

 * Nouveau projet : activez la case à cocher **Ajouter Application Insights**.
 * Projet existant : cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter Application Insights**.

Si vous n'utilisez pas Visual Studio, ou si ces options ne sont pas disponibles pour votre projet, [recherchez votre plate-forme ici](app-insights-platforms.md).

3. Exécutez votre application (en mode de développement, ou publiez-la) et regardez les données s'accumuler dans le [portail Azure](https://portal.azure.com).

## Code


[Exemples et procédures pas à pas](app-insights-code-samples.md)

[SDK Labs](https://www.myget.org/gallery/applicationinsights-sdk-labs) : packages NuGet que vous pouvez installer (et désinstaller) sous forme d'ajouts au Kit de développement logiciel (SDK) Application Insights. Essayez-les et envoyez-nous vos commentaires !


## Support et commentaires

* Questions et problèmes :
 * [Résolution des problèmes][qna]
 * [Forum MSDN](https://social.msdn.microsoft.com/Forums/vstudio/fr-FR/home?forum=ApplicationInsights)
 * [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Bogues :
 * [Connect](https://connect.microsoft.com/VisualStudio/Feedback/LoadSubmitFeedbackForm?FormID=6076)
* Suggestions
 * [UserVoice](https://visualstudio.uservoice.com/forums/357324)


## Vidéos


> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]


<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0810_2016-->