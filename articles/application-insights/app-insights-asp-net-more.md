<properties 
	pageTitle="Tirez davantage d’Application Insights | Microsoft Azure" 
	description="Après avoir pris en main Application Insights, voici un résumé des fonctionnalités que vous pouvez explorer." 
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
	ms.date="08/30/2016" 
	ms.author="awills"/>

# Plus de télémétrie dans Application Insights

Une fois que vous avez [ajouté Application Insights à votre code ASP.NET](app-insights-asp-net.md), vous pouvez encore suivre quelques étapes supplémentaires pour obtenir davantage de données de télémétrie.

## Si votre application s’exécute sur votre serveur IIS...

Si votre application est hébergée sur des serveurs IIS que vous-même contrôlez, installez Application Insights Status Monitor sur ces serveurs. Si le programme est déjà installé, vous n’avez rien à faire.

1. Sur chaque serveur web IIS, connectez-vous avec vos informations d’identification d’administrateur.
2. Téléchargez et exécutez le [programme d’installation Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).
3. Dans l'Assistant Installation, connectez-vous à Microsoft Azure.

Vous n’avez rien d’autre à faire, mais vous pouvez vérifier que l’analyse est bien activée pour votre application.

![Extension dans Azure](./media/app-insights-asp-net-more/025.png)

(Vous pouvez également utiliser Status Monitor pour [activer l’analyse lors de l’exécution](app-insights-monitor-performance-live-website-now.md), même si vous n’avez pas instrumenté vos applications dans Visual Studio.)

### Résultat ?

Si Status Monitor est installé sur vos machines serveur, vous obtenez des données de télémétrie supplémentaires :

* Télémétrie de dépendance (appels SQL et appels REST effectuées par votre application) pour les applications .NET 4.5. (Pour les versions ultérieures de .NET, Status Monitor n’est pas requis pour la télémétrie de dépendance.)
* Traces de pile d’exception affichant davantage de détails.
* Compteurs de performance. Dans Application Insights, ces compteurs apparaissent dans le panneau Serveurs.

![Extension dans Azure](./media/app-insights-asp-net-more/070.png)

Pour afficher plus ou moins de compteurs, vous pouvez [modifier les graphiques](app-insights-metrics-explorer.md). Si le compteur de performances que vous souhaitez ne se trouve pas dans l’ensemble disponible, vous pouvez [l’ajouter à l’ensemble collecté par le module du compteur de performances](app-insights-web-monitor-performance.md#system-performance-counters).

## S’il ne s’agit pas d’une application web Azure...

Si votre application s’exécute en tant qu’application web Azure, accédez au panneau de commande Azure correspondant à l’application ou à la machine virtuelle, puis ajoutez l’extension Application Insights. Dans **Outils**, ouvrez **Analyse des performances** et configurez **Application Insights**. Lorsque vous y êtes invité, choisissez la ressource Application Insights que vous avez déjà créée.

![Extension dans Azure](./media/app-insights-asp-net-more/05-extend.png)

### Résultat ?

* Traces de pile d’exception affichant davantage de détails.
* Télémétrie de dépendance (appels SQL et appels REST effectuées par votre application) pour les applications .NET 4.5. (Pour les versions ultérieures de .NET, l’extension n’est pas requise pour la télémétrie de dépendance.)

![Extension dans Azure](./media/app-insights-asp-net-more/080.png)

(Vous pouvez également utiliser cette méthode pour [activer l’analyse des performances lors de l’exécution](app-insights-monitor-performance-live-website-now.md), même si vous n’avez pas instrumenté votre application dans Visual Studio.)

## Surveillance côté client

Vous avez installé le kit de développement logiciel (SDK) qui envoie des données de télémétrie depuis le serveur (principal) de votre application. Vous pouvez maintenant ajouter une surveillance côté client. Cette surveillance vous fournit des données sur les utilisateurs, les sessions, les pages consultées et les exceptions ou incidents qui se produisent dans le navigateur. Vous pourrez également écrire votre propre code pour suivre la façon dont les utilisateurs travaillent avec votre application, même jusqu'au niveau du nombre de clics et de frappes sur les touches.

Ajoutez l’extrait de code JavaScript Application Insights à chaque page web pour obtenir des données de télémétrie à partir de navigateurs clients.

1. Dans Azure, ouvrez la ressource Application Insights correspondant à votre application.
2. Accédez à Démarrage rapide, Analyse côté client et copiez l’extrait de code.
3. Copiez-le de manière à le faire apparaître dans l’entête de chaque page web. Pour cela, il suffit généralement de coller le contenu dans la page de mise en forme principale.

![Extension dans Azure](./media/app-insights-asp-net-more/100.png)

Notez que le code contient la clé d’instrumentation qui identifie la ressource de votre application.

### Résultat ?

* Vous pouvez écrire du code JavaScript pour envoyer une [télémétrie personnalisée à partir de vos pages web](app-insights-api-custom-events-metrics.md), par exemple pour effectuer un suivi des clics.
* Dans [Analytique](app-insights-analytics.md), données dans `pageViews` et données AJAX dans `dependencies`.
* [Données de performances et d’utilisation du client](app-insights-javascript.md) dans le panneau Navigateurs.

![Extension dans Azure](./media/app-insights-asp-net-more/090.png)


[En savoir plus sur le suivi de page web.](app-insights-web-track-usage.md)



## Suivi de la version de l'application

Assurez-vous que `buildinfo.config` est généré par votre processus MSBuild. Dans votre fichier .csproj, ajoutez :

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Quand il détient les informations de version, le module web Application Insights ajoute automatiquement la **version de l’application** en tant que propriété à chaque élément de télémétrie. Cela vous permet de filtrer par version pendant l’exécution de [recherches de diagnostic](app-insights-diagnostic-search.md) ou l’[exploration de métriques](app-insights-metrics-explorer.md).

Toutefois, notez que le numéro de version de build est uniquement généré par MS Build, et non par la build de développement dans Visual Studio.


## Tests web de disponibilité

Envoyez des demandes HTTP à votre application web à intervalles réguliers à partir de divers emplacements dans le monde. Nous vous alertons si la réponse est lente ou peu fiable.

Dans la ressource Application Insights correspondant à votre application, cliquez sur la mosaïque Disponibilité pour ajouter, modifier et afficher les tests web.

Vous pouvez ajouter plusieurs tests à plusieurs emplacements.

![Extension dans Azure](./media/app-insights-asp-net-more/110.png)

[En savoir plus](app-insights-monitor-web-app-availability.md)

## Télémétrie personnalisée et journalisation

Les packages Application Insights que vous avez ajoutés à votre code fournissent une API que vous pouvez appeler à partir de votre application.

* [Générez vos propres événements et mesures](app-insights-api-custom-events-metrics.md), par exemple pour dénombrer des événements commerciaux ou surveiller les performances.
* [Capturez des suivis de journaux](app-insights-asp-net-trace-logs.md) à partir de Log4Net, NLog ou System.Diagnostics.Trace.
* [Filtrez, modifiez ou augmentez](app-insights-api-filtering-sampling.md) la télémétrie standard envoyée à partir de votre application en écrivant des processeurs de télémétrie.


## Analyse puissante et présentation

Il existe de nombreuses manières d’explorer vos données. Si vous venez de découvrir Application Insights, consultez les articles suivants :

||
|---|---
|[**Recherche de diagnostic pour les données d’instance**](app-insights-visual-studio.md)<br/>Cherchez et filtrez les événements, comme les requêtes, les exceptions, les appels de dépendance, les suivis de journaux et les affichages de pages. Dans Visual Studio, accédez au code à partir de l’arborescence des appels de procédure.|![Visual Studio](./media/app-insights-asp-net/61.png)
|[**Metrics Explorer pour les données agrégées**](app-insights-metrics-explorer.md)<br/>Explorez, filtrez et segmentez des données agrégées, comme les taux de demandes, d’échecs et d’exceptions, les temps de réponse et les durées de chargement des pages.|![Visual Studio](./media/app-insights-asp-net-more/060.png)
|[**Tableaux de bord**](app-insights-dashboards.md#dashboards)<br/>Combinez des données de plusieurs sources et partagez-les avec d’autres. Idéal pour les applications à composants multiples et pour un affichage en continu dans la salle de l’équipe. |![Exemple de tableaux de bord](./media/app-insights-asp-net/62.png)
|[**Live Metrics Stream**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>Lorsque vous déployez une nouvelle version, observez ces indicateurs de performance quasiment en temps réel pour vous assurer que tout fonctionne comme prévu.|![Exemple d’analyse](./media/app-insights-asp-net-more/050.png)
|[**Analyses**](app-insights-analytics.md)<br/>Répondez à des questions difficiles sur les performances et l’utilisation de votre application avec ce langage de requêtes puissant.|![Exemple d’analyse](./media/app-insights-asp-net-more/010.png)
|[**Alertes automatiques et manuelles**](app-insights-alerts.md)<br/>Les alertes automatiques s’adaptent aux habitudes télémétriques normales de votre application et se déclenchent lorsqu’un comportement inhabituel est détecté. Vous pouvez également définir des alertes sur des niveaux particuliers de mesures personnalisées ou standard.|![Exemple d’alerte](./media/app-insights-asp-net-more/020.png)

## Gestion des données

|||
|---|---|
|[**Exportation continue**](app-insights-export-telemetry.md)<br/>Copiez toutes vos données télémétriques dans le stockage pour pouvoir les analyser de la façon de votre choix.|
|**API d’accès aux données**<br/>Prochainement disponible.|
|[**Échantillonnage**](app-insights-sampling.md)<br/>Réduit le débit de données et vous permet de rester dans la limite de votre niveau tarifaire.|![Mosaïque d’échantillonnage](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0907_2016-->