<properties
	pageTitle="Analyse développeur"
	description="DevOps avec Visual Studio, Application Insights et HockeyApp"
	authors="alancameronwills"
	services="application-insights"
    documentationCenter=""
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="05/18/2016"
	ms.author="awills"/>

# Analyse développeur avec Application Insights et HockeyApp

*Application Insights est à l'état de version préliminaire.*

De nombreux projets suivent un cycle [DevOps](https://en.wikipedia.org/wiki/DevOps) rapide. Ils créent et distribuent leurs applications, obtiennent des commentaires sur son fonctionnement et sur ce que les utilisateurs font avec elles, puis ils utilisent ces connaissances pour planifier des cycles de développement ultérieurs.

Pour surveiller les performances et l’utilisation, il est important d’avoir des données de télémétrie de l’application active, ainsi que des commentaires des utilisateurs.

De nombreux systèmes sont conçus à partir de plusieurs composants : un service web, des processeurs ou magasins de données principaux, et des logiciels clients qui s’exécutent dans le navigateur de l’utilisateur ou en tant qu’application sur un téléphone ou un autre appareil. Les données de télémétrie de ces différents composants doivent être combinées.

Certaines versions ont une distribution limitée vers des testeurs désignés. Nous avons également organisé la distribution de version d’évaluation (tests de nouvelles fonctionnalités à un public bien précis) et des test A| B (tests parallèles d’autres interfaces utilisateurs).

La gestion des distributions et l’intégration de la surveillance sur plusieurs composants clients et serveurs n’est pas une tâche aisée. Ce processus est une partie essentielle de l’architecture de l’application : nous ne pouvons pas créer un système de ce genre sans cycle de développement itératif et sans outils de surveillance performants.

Dans cet article, nous allons étudier comment les aspects du cycle devOps liés à la surveillance s’intègrent aux autres parties du processus.

Si vous souhaitez examiner un exemple spécifique, il existe [une étude de cas intéressante](http://aka.ms/mydrivingdocs) qui compte plusieurs composants clients et serveurs.

## Cycle DevOps

Visual Studio et les outils d’Analyse développeur offrent une expérience devOps bien intégrée. Par exemple, voici un cycle classique pour une application web (qui peut être Java, Node.js ou ASP.NET) :

![Cycle devops d’application web](./media/app-insights-developer-analytics/040.png)

* Un développeur archive dans le dépôt de code ou fusionne avec la branche principale. Le dépôt est Git dans cette illustration, mais il peut également s’agir de [Team Foundation Version Control](https://www.visualstudio.com/docs/tfvc/overview).
* Les modifications déclenchent une build et un test unitaire. Le service de build peut être dans [Visual Studio Team Services ou son équivalent local, Team Foundation Server](https://www.visualstudio.com/docs/vsts-tfs-overview). 
* Un test unitaire et une build couronnés de succès peuvent [déclencher un déploiement automatique](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition). L’hôte de l’application web peut être votre propre serveur web ou Microsoft Azure. 
* Les données de télémétrie de l’application en ligne sont envoyées vers [Application Insights](app-insights-overview.md), à la fois à partir du serveur et [à partir des navigateurs clients](app-insights-javascript.md). Vous pouvez alors analyser les performances de l’application et les modèles d’utilisation. De puissants [outils de recherche](app-insights-analytics.md) vous aident à diagnostiquer les éventuels problèmes. Des [alertes](app-insights-alerts.md) vous permettent d’être informé des problèmes dès qu’ils surgissent. 
* Votre prochain cycle de développement est informé par votre analyse des données de télémétrie actives.

### Applications de bureau et pour appareils

Pour les applications de bureau et pour appareils, la partie distribution du cycle est légèrement différente, car nous ne chargeons pas simplement vers un ou deux serveurs. Au lieu de cela, un test unitaire et une build couronnés de succès peuvent [déclencher le chargement vers HockeyApp](https://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs). HockeyApp supervise la distribution à votre équipe d’utilisateurs de test (ou au grand public, si vous préférez).


![Cycle devops pour appareil](./media/app-insights-developer-analytics/030.png)

HockeyApp recueille également des données de performances et d’utilisation, sous la forme de :

* Commentaires utilisateur textuels avec captures d’écran
* Rapports d’incidents
* Télémétrie personnalisée encodée par vous-même.

Là encore, le cycle devOps est terminé car vous dressez vos plans de développement ultérieurs en fonction des commentaires reçus.


## Configuration de l’Analyse développeur

Pour chaque composant de votre application (mobile, web ou bureau), les étapes sont les mêmes. Pour de nombreux types d’applications, Visual Studio exécute automatiquement certaines de ces étapes.

1. Ajoutez le SDK approprié à votre application. Pour les applications pour appareils il s’agit de HockeyApp, et pour les services web il s’agit d’Application Insights. Chacun possède plusieurs variantes pour différentes plateformes. (Vous pouvez aussi utiliser l’un ou l’autre SDK pour les applications de bureau, mais nous vous recommandons d’utiliser HockeyApp.)
2. Inscrivez votre application avec le portail Application Insights ou HockeyApp, en fonction du SDK que vous avez utilisé. C’est là que seront affichées les données d’analyse de votre application active. Vous obtenez une clé d’instrumentation ou un ID que vous configurez dans votre application pour que le SDK sache où envoyer ses données de télémétrie.
3. Ajoutez du code personnalisé (le cas échéant) pour consigner des événements ou des mesures, pour faciliter les diagnostics ou pour analyser les performances ou l’utilisation. Comme il existe de nombreuses fonctionnalités de surveillance intégrées, ceci ne sera pas nécessaire lors de votre premier cycle.
3. Pour les applications pour appareils :
 * Chargez une build de débogage vers HockeyApp. À partir de là, vous pouvez la distribuer à une équipe d’utilisateurs de test. Chaque fois que vous chargez des builds ultérieures, l’équipe est informée.
 * Quand vous configurez votre service de build continu, créez une définition de version qui utilise l’étape de plug-in pour charger vers HockeyApp.
 
Voici les instructions détaillées pour différents types d’applications :

* [Application web ASP.NET](app-insights-asp-net.md) 
* [Applications web Java](app-insights-java-get-started.md)
* [Application web Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Application iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Application Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Application Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Application Windows universelle](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Application Windows Phone 8 et 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Application Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

<!---HONumber=AcomDC_0525_2016-->