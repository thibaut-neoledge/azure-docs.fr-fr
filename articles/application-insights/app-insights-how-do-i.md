---
title: "Comment ... dans Application Insights | Microsoft Docs"
description: FAQ dans Application Insights
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/05/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 9a3df0ad2483471023ebb954d613bc5cad8fb7bf
ms.openlocfilehash: 9e54ee2d67a8dfb5b480db01219e128607e26f51


---
# <a name="how-do-i--in-application-insights"></a>Comment ... dans Application Insights ?
## <a name="get-an-email-when-"></a>Recevoir un message électronique quand...
### <a name="email-if-my-site-goes-down"></a>Envoyer un message électronique si mon site tombe en panne
Définissez un [test web de disponibilité](app-insights-monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Envoyer un message électronique si mon site est surchargé
Définissez une [alerte](app-insights-alerts.md) sur le **Temps de réponse du serveur**. Un seuil compris entre 1 et 2 secondes doit fonctionner.

![](./media/app-insights-how-do-i/030-server.png)

Votre application peut également indiquer des signes de surcharge en retournant des codes d’erreur. Définissez une alerte sur les **Demandes ayant échoué**.

Si vous voulez définir une alerte sur les **Exceptions du serveur**, vous devrez peut-être effectuer une [installation supplémentaire](app-insights-asp-net-exceptions.md) pour afficher les données.

### <a name="email-on-exceptions"></a>Envoyer un message électronique en cas d’exceptions
1. [Configurer la surveillance des exceptions](app-insights-asp-net-exceptions.md)
2. [Définir une alerte](app-insights-alerts.md) sur la métrique Nombre d’exceptions

### <a name="email-on-an-event-in-my-app"></a>Envoyer un message électronique en réponse à un événement dans mon application
Supposons que vous vouliez recevoir un e-mail quand un événement spécifique se produit. Application Insights ne fournit pas directement cette fonctionnalité, mais peut [envoyer une alerte quand une métrique dépasse un seuil](app-insights-alerts.md).

Les alertes peuvent être définies sur des [métriques personnalisées](app-insights-api-custom-events-metrics.md#trackmetric), et non sur des événements personnalisés. Écrivez du code pour augmenter une métrique quand l’événement se produit :

    telemetry.TrackMetric("Alarm", 10);

ou :

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Les alertes ayant deux états, vous devez envoyer une valeur faible quand vous considérez que l’alerte est terminée :

    telemetry.TrackMetric("Alarm", 0.5);

Créez un graphique dans [Metrics Explorer](app-insights-metrics-explorer.md) pour afficher votre alarme :

![](./media/app-insights-how-do-i/010-alarm.png)

Maintenant, définissez une alerte qui se déclenche quand la métrique dépasse une valeur moyenne pendant une courte période :

![](./media/app-insights-how-do-i/020-threshold.png)

Définissez la durée moyenne sur la valeur minimale.

Vous recevrez des messages électroniques quand la métrique est supérieure et inférieure au seuil.

Éléments à prendre en considération :

* Une alerte possède deux états (« alerte » et « intègre »). L’état est évalué uniquement quand une métrique est reçue.
* Un message électronique est envoyé uniquement quand l’état change. C’est pourquoi vous devez envoyer à la fois des métriques de valeur haute et faible.
* Pour évaluer l’alerte, la moyenne est calculée à partir des valeurs reçues pendant la période précédente. Comme cela se produit chaque fois qu’une métrique est reçue, des messages électroniques peuvent être envoyés plus fréquemment que la période que vous avez définie.
* Étant donné que les messages électroniques sont envoyés à la fois pour les états « alerte » et « intègre », vous devrez peut-être remplacer votre événement à déclenchement unique par une condition à deux états. Par exemple, au lieu d’un événement « tâche terminée », définissez une condition « tâche en cours », pour laquelle vous obtenez des messages électroniques au début et à la fin d’une tâche.

### <a name="set-up-alerts-automatically"></a>Configurer automatiquement des alertes
[Utiliser PowerShell pour créer des alertes](app-insights-alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Utiliser PowerShell pour gérer Application Insights
* [Créer des ressources](app-insights-powershell-script-create-resource.md)
* [Créer des alertes](app-insights-alerts.md#automation)

## <a name="application-versions-and-stamps"></a>Horodatages et versions d’application
### <a name="separate-the-results-from-dev-test-and-prod"></a>Séparer les résultats de développement, de test et de production
* Pour différents environnements, configurez différents ikeys
* Pour différents horodatages (développement, test, production), marquez la télémétrie avec différentes valeurs de propriété

[En savoir plus](app-insights-separate-resources.md)

### <a name="filter-on-build-number"></a>Filtrer sur le numéro de build
Quand vous publiez une nouvelle version de votre application, vous voulez pouvoir distinguer la télémétrie des différentes builds.

Vous pouvez définir la propriété Version de l’application pour filtrer les résultats de [recherche](app-insights-diagnostic-search.md) et de [Metrics Explorer](app-insights-metrics-explorer.md).

![](./media/app-insights-how-do-i/050-filter.png)

Il existe plusieurs méthodes de définition de la propriété Version de l’application.

* Définissez directement :

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Encapsulez cette ligne dans un [initialiseur de télémétrie](app-insights-api-custom-events-metrics.md#defaults) pour vous assurer que toutes les instances de TelemetryClient sont définies de manière cohérente.
* [ASP.NET] Définissez la version dans `BuildInfo.config`. Le module web sélectionnera la version dans le nœud BuildLabel. Incluez ce fichier dans votre projet et n’oubliez pas de définir la propriété Toujours copier dans l’Explorateur de solutions.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Générez automatiquement BuildInfo.config dans MSBuild. Pour ce faire, ajoutez quelques lignes à votre fichier .csproj :

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Cela génère un fichier appelé *Votre_nom_de_projet*.BuildInfo.config. Le processus de publication le renomme en BuildInfo.config.

    L’étiquette de build contient un espace réservé (AutoGen_...) quand vous effectuez la génération avec Visual Studio. Mais quand vous utilisez MSBuild, l’espace réservé est remplacé par le numéro de version correct.

    Pour permettre à MSBuild de générer des numéros de version, définissez la version comme `1.0.*` dans AssemblyReference.cs

## <a name="monitor-backend-servers-and-desktop-apps"></a>Surveiller les serveurs principaux et les applications de bureau
[Utilisez le module du Kit de développement logiciel (SDK) Windows Server](app-insights-windows-desktop.md).

## <a name="visualize-data"></a>Visualiser les données
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Tableau de bord avec des métriques de plusieurs applications
* Dans [Metrics Explorer](app-insights-metrics-explorer.md), personnalisez votre graphique et enregistrez-le en tant que favori. Épinglez-le au tableau de bord Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Tableau de bord avec des données provenant d’autres sources et d’Application Insights
* [Exportez la télémétrie dans Power BI](app-insights-export-power-bi.md).

Ou

* Utilisez SharePoint comme tableau de bord, pour afficher les données des composants WebPart de SharePoint. [Utilisez l’exportation continue et Stream Analytics pour exporter vers SQL](app-insights-code-sample-export-sql-stream-analytics.md).  Utilisez PowerView pour examiner la base de données et créer un composant WebPart de SharePoint pour PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrer les utilisateurs authentifiés ou anonymes
Si vos utilisateurs se connectent, vous pouvez définir l’ [ID d’utilisateur authentifié](app-insights-api-custom-events-metrics.md#authenticated-users). (Cela n’est pas automatique.)

Vous pouvez ensuite effectuer les opérations suivantes :

* Rechercher des ID d’utilisateur spécifiques

![](./media/app-insights-how-do-i/110-search.png)

* Filtrer des métriques sur les utilisateurs anonymes ou authentifiés

![](./media/app-insights-how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modification de noms ou de valeurs de propriété
Créez un [filtre](app-insights-api-filtering-sampling.md#filtering). Cela vous permet de modifier ou de filtrer la télémétrie avant son envoi depuis votre application vers Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Répertorier les utilisateurs spécifiques et leur utilisation
Si vous voulez simplement [rechercher des utilisateurs spécifiques](#search-specific-users), vous pouvez définir [l’identifiant utilisateur authentifié](app-insights-api-custom-events-metrics.md#authenticated-users).

Si vous voulez une liste des utilisateurs avec des données telles que les pages qu’ils ont consultées ou leur fréquence de connexion, deux options s’offrent à vous :

* [Définissez l’identifiant utilisateur authentifié](app-insights-api-custom-events-metrics.md#authenticated-users), [exportez vers une base de données](app-insights-code-sample-export-sql-stream-analytics.md) et utilisez des outils pour analyser vos données utilisateur.
* Si vous ne disposez que d’un petit nombre d’utilisateurs, envoyez des événements ou des métriques personnalisés, en utilisant les données d’intérêt comme le nom de l’événement ou la valeur de la métrique et en définissant l’id d’utilisateur en tant que propriété. Pour analyser les vues de page, remplacez l’appel standard de trackPageView JavaScript. Pour analyser la télémétrie côté serveur, utilisez un initialiseur de télémétrie pour ajouter l’id d’utilisateur à toute la télémétrie de serveur. Vous pouvez ensuite filtrer et segmenter les métriques et les recherches sur l’id d’utilisateur.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Réduire le trafic de mon application vers Application Insights
* Dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), désactivez tous les modules dont vous n’avez pas besoin, comme le collecteur de compteurs de performances.
* Utilisez [Échantillonnage et filtrage](app-insights-api-filtering-sampling.md) dans le Kit de développement logiciel (SDK).
* Dans vos pages web, limitez le nombre d'appels Ajax signalés pour chaque affichage de page. Dans l(extrait de script après `instrumentationKey:...`, insérez : `,maxAjaxCallsPerView:3` (ou un nombre approprié).
* Si vous utilisez [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric), calculez l’agrégat des lots de valeurs de métriques avant d’envoyer le résultat. Il existe une surcharge de TrackMetric() qui se charge de cette tâche.

En savoir plus sur la [tarification et les quotas](app-insights-pricing.md).

## <a name="disable-telemetry"></a>Désactiver la télémétrie
Pour **arrêter et démarrer dynamiquement** la collecte et la transmission de la télémétrie à partir du serveur :

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



Pour **désactiver les collecteurs standard sélectionnés** (par exemple, les compteurs de performances, les requêtes HTTP ou les dépendances), supprimez ou commentez les lignes correspondantes dans [ApplicationInsights.config](app-insights-api-custom-events-metrics.md). Par exemple, vous pouvez faire cela si vous souhaitez envoyer vos propres données TrackRequest.

## <a name="view-system-performance-counters"></a>Afficher les compteurs de performances système
Parmi les métriques que vous pouvez afficher dans Metrics Explorer, il existe un ensemble de compteurs de performances système. Un panneau prédéfini intitulé **Serveurs** affiche plusieurs d'entre eux.

![Ouvrez votre ressource Application Insights et cliquez sur Serveurs.](./media/app-insights-how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Si aucune donnée de compteur de performances ne s’affiche
* **Serveur IIS** sur votre propre ordinateur ou sur une machine virtuelle. [Installer Status Monitor](app-insights-monitor-performance-live-website-now.md).
* **Site Web Azure** : nous ne prenons pas encore en charge les compteurs de performances. Il existe plusieurs métriques que vous pouvez obtenir de manière standard dans le panneau de configuration des sites web Azure.
* **Serveur Unix** - [installer collectd](app-insights-java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Pour afficher davantage de compteurs de performances
* Tout d’abord, [ajoutez un nouveau graphique](app-insights-metrics-explorer.md) et vérifiez si le compteur se trouve dans le jeu de base que nous offrons.
* Dans le cas contraire, [ajoutez le compteur au jeu collecté par le module de compteur de performances](app-insights-performance-counters.md).



<!--HONumber=Feb17_HO1-->


