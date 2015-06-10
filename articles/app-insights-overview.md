<properties 
	pageTitle="Présentation d’Application Insights" 
	description="Suivez l’utilisation et les performances de votre application web en ligne ou de votre application Smart Device. Détection, tri et diagnostic des problèmes Surveillez et améliorez le succès auprès de vos utilisateurs en continu." 
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
	ms.date="04/22/2015" 
	ms.author="awills"/>
 
# Présentation d’Application Insights

Visual Studio Application Insights vous permet de suivre les performances et l’utilisation de votre site Web en ligne ou d’une application Smart Device.

* [Détectez, triez et diagnostiquez][detect] les problèmes de performances et corrigez-les avant que la plupart de vos utilisateurs ne s’en rende compte.
 *  Alertes sur les changements de performances ou les incidents.
 *  Métriques permettant de diagnostiquer des problèmes de performances, comme les temps de réponse, l’utilisation du processeur et le suivi des dépendances.
 *  Tests de disponibilité pour les applications web.
 *  Alertes et rapports d’incidents et d’exceptions
 *  Puissante recherche des journaux de diagnostic (y compris le suivi des journaux à partir de vos frameworks de journalisation préférés).
* [Améliorez en permanence votre application][knowUsers] en comprenant comment les utilisateurs s’en servent. 
 * Nombre d’affichages de pages, utilisateurs nouveaux et qui reviennent, ainsi que d’autres analyses d’utilisation principales
 * Effectuez le suivi de vos propres événements pour évaluer les tendances d’utilisation et le succès de chaque fonctionnalité.

## Comment cela fonctionne-t-il ?

Vous installez un petit Kit de développement logiciel (SDK) dans votre application et configurez un compte dans le portail Application Insights. Le Kit de développement logiciel (SDK) surveille votre application et envoie les données de télémétrie au portail. Le portail vous montre des graphiques statistiques et fournit de puissants outils de recherche pour vous aider à diagnostiquer les problèmes.

![Le Kit de développement logiciel (SDK) Application Insights dans votre application envoie la télémétrie aux ressources Application Insights dans le portail Azure.](./media/app-insights-overview/01-scheme.png)

Le Kit de développement logiciel (SDK) dispose de plusieurs modules qui recueillent les données de télémétrie, pour compter les utilisateurs, les sessions et les performances, par exemple. Vous pouvez également écrire votre propre code personnalisé pour envoyer des données de télémétrie au portail. La télémétrie personnalisée est particulièrement utile pour suivre les récits utilisateur : vous pouvez compter des événements comme les clics des boutons, la réalisation d’objectifs spécifiques ou les erreurs des utilisateurs.

Pour les serveurs ASP.NET et les applications web Azure, vous pouvez également installer [Status Monitor][redfield], qui a deux utilisations. Il vous permet de :

* surveiller une application web sans la régénérer ou la réinstaller.
* suivre les appels aux modules dépendants.

## Avec quels types d’applications fonctionne-t-il ?

Il existe actuellement des Kits de développement logiciel (SDK) pour :

* les applications web
 * [ASP.NET][greenbrown] sur Azure ou votre serveur IIS
 * [Java][java] sur JRE 
 * [les pages Web][client] : HTML + JavaScript
* les applications Smart Device
 * [Windows][windows]
 * [iOS][ios]
 * [Android][android]
 * Cordova
 * [autres plateformes][platforms]


## Comment l’utiliser ?

* [Détection, tri et diagnostic des problèmes][detect]
* [Analyse de l’utilisation de votre application][knowUsers]


## De quoi ai-je besoin pour l’utiliser ?

* Un abonnement à Microsoft Azure. Application Insights est un des nombreux services cloud d’Azure, qui incluent également des sites web, des bases de données, des machines virtuelles et plus encore. (Mais vous pouvez utiliser Application Insights pour surveiller n’importe quelle application, votre application n’a pas à s’exécuter dans Azure.) 

 * Votre organisation peut avoir un compte.


## Comment faire pour démarrer ?

Choisissez votre plateforme à partir du menu Démarrer sur la gauche.

Dans tous les cas, la procédure de base est la suivante :

1. Créez une ressource Application Insights dans [Azure][portal] (et obtenez sa clé d’instrumentation).
2. Instrumentez votre application avec le Kit de développement logiciel (SDK) approprié (et configurez-la avec la clé d’instrumentation).
3. Exécutez votre application en mode débogage ou en direct.
4. Affichez les résultats dans votre ressource dans [Azure][portal].

Dans certains cas, un plug-in est disponible pour votre IDE (par exemple, Visual Studio ou Eclipse). Il effectue les deux premières étapes pour vous. Toutefois, vous pouvez toujours effectuer la procédure manuellement.

Si votre application est un site Web ou un service, il existe certains ajouts facultatifs et des variations de la procédure de base :

* Ajoutez un kit de développement logiciel (SDK) à l’application côté serveur et à l’[appareil][windows] client ou à la [page web][client]. Les données de télémétrie sont fusionnées dans le portail de sorte que vous pouvez mettre en corrélation les événements aux deux extrémités.
* Configurez des tests web pour surveiller la disponibilité de votre site à partir de points dans le monde entier.
* Instrumentez une application côté serveur déjà en ligne, sans la régénérer ou la redéployer. Cette option est disponible pour les [serveurs IIS][redfield] et les [applications web Azure][azure].
* Analysez les appels de dépendance que votre application effectue vers d’autres composants comme des bases de données ou via les API REST. Disponible pour les [serveurs IIS][redfield] et les [applications web Azure][azure].


<!--Link references-->

[android]: app-insights-android.md
[azure]: insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[ios]: app-insights-ios.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

<!---HONumber=58-->