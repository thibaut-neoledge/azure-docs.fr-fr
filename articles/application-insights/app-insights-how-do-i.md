<properties 
	pageTitle="Comment ... dans Application Insights" 
	description="FAQ dans Application Insights" 
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
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Comment ... dans Application Insights ?

## Recevoir un message électronique quand...

### Envoyer un message électronique si mon site tombe en panne

Définissez un [test web de disponibilité](app-insights-monitor-web-app-availability.md).

### Envoyer un message électronique si mon site est surchargé

Définissez une [alerte](app-insights-alerts.md) sur le **Temps de réponse du serveur**. Un seuil compris entre 1 et 2 secondes doit fonctionner.

![](./media/app-insights-how-do-i/030-server.png)

Votre application peut également indiquer des signes de surcharge en retournant des codes d’erreur. Définissez une alerte sur les **Demandes ayant échoué**.

Si vous voulez définir une alerte sur les **Exceptions du serveur**, vous devrez peut-être effectuer une [installation supplémentaire](app-insights-asp-net-exceptions.md) pour afficher les données.


### Envoyer un message électronique en réponse à un événement dans mon application

Supposons que vous vouliez recevoir un e-mail quand un événement spécifique se produit. Application Insights ne fournit pas directement cette fonctionnalité, mais peut [envoyer une alerte quand une métrique dépasse un seuil](app-insights-alerts.md).

Les alertes peuvent être définies sur des [métriques personnalisées](app-insights-api-custom-events-metrics.md#track-metric), et non sur des événements personnalisés. Écrivez du code pour augmenter une métrique quand l’événement se produit :

    telemetry.TrackMetric("Alarm", 10);

ou :

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Les alertes ayant deux états, vous devez envoyer une valeur faible quand vous considérez que l’alerte est terminée :

    telemetry.TrackMetric("Alarm", 0.5);

Créez un graphique dans [Metrics Explorer](app-insights-metric-explorer.md) pour afficher votre alarme :

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
 
## Filtre de version d’application

Quand vous publiez une nouvelle version de votre application, vous voulez pouvoir distinguer la télémétrie des différentes versions.

Vous pouvez définir la propriété Version de l’application pour filtrer les résultats de [recherche](app-insights-diagnostic-search.md) et de [Metrics Explorer](app-insights-metrics-explorer.md).


![](./media/app-insights-how-do-i/050-filter.png)

Il existe plusieurs méthodes de définition de la propriété Version de l’application.

* Définissez directement :

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`

* Encapsulez cette ligne dans un [initialiseur de télémétrie](app-insights-api-custom-events-metrics.md#telemetry-initializers) pour vous assurer que toutes les instances de TelemetryClient sont définies de manière cohérente.

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

    Cela génère un fichier appelé *Votre\_nom\_de\_projet*.BuildInfo.config. Le processus de publication le renomme en BuildInfo.config.

    L’étiquette de build contient un espace réservé (AutoGen\_...) quand vous effectuez la génération avec Visual Studio. Mais quand vous utilisez MSBuild, l’espace réservé est remplacé par le numéro de version correct.

    Pour permettre à MSBuild de générer des numéros de version, définissez la version comme `1.0.*` dans AssemblyReference.cs

<!---HONumber=Oct15_HO1-->