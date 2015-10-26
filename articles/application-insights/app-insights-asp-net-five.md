<properties 
	pageTitle="Application Insights pour ASP.NET 5" 
	description="Surveiller la disponibilité, les performances et l’utilisation.des applications Web." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="awills"/>

# Application Insights pour ASP.NET 5

Visual Studio Application Insights permet de surveiller la disponibilité, les performances et l’utilisation de votre application Web. Avec les retours que vous obtenez sur les performances et l’efficacité de votre application dans la nature, vous pouvez prendre des décisions avisées sur la direction de la conception de chaque cycle de développement.

![exemples](./media/app-insights-asp-net-five/sample.png)

Tout d’abord, vous avez besoin d’un abonnement à [Microsoft Azure](http://azure.com). Connectez-vous avec un compte Microsoft, que vous pouvez avoir pour Windows, XBox Live ou d’autres services cloud de Microsoft.

## Création d’un projet ASP.NET 5

... si vous ne l’avez pas déjà fait.

Utilisez le modèle de projet standard ASP.NET 5 dans Visual Studio 2015.


## Création d’une ressource Application Insights dans Azure

Dans le [portail Azure][portal], créez une ressource Application Insights. Choisissez l’option ASP.NET.

![Cliquez sur Nouveau, Services de développement, Application Insights](./media/app-insights-asp-net-five/01-new-asp.png)

Dans le panneau de [ressources][roles] qui s’ouvre, vous trouverez des données relatives à l’utilisation et aux performances de votre application. Vous devriez trouver une vignette sur l’écran d’accueil pour accéder à ces informations, la prochaine fois que vous vous connecterez à Azure. Sinon, cliquez sur Parcourir.

Le choix du type d’application définit le contenu par défaut des panneaux de ressource et les propriétés visibles dans [Metrics Explorer][metrics].

##  Configurez votre projet avec la clé d’instrumentation.

Copiez la clé à partir de votre ressource Application Insights :

![Cliquez sur Propriétés, sélectionnez la clé et appuyez sur ctrl + C](./media/app-insights-asp-net-five/02-props-asp.png)

Dans votre projet ASP.NET 5, collez-la dans `config.json` :

    {
      "ApplicationInsights": {
        "InstrumentationKey": "11111111-2222-3333-4444-555555555555"
      }
    }

Ou, si vous préférez une configuration dynamique, vous pouvez ajouter ce code à la classe de démarrage de l’application :

    configuration.AddApplicationInsightsSettings(
      instrumentationKey: "11111111-2222-3333-4444-555555555555");


## Ajout de Application Insights à votre projet


#### Référencez le package NuGet

Recherchez le [numéro de version le plus récent](https://github.com/Microsoft/ApplicationInsights-aspnet5/releases) du package NuGet.

Ouvrez `project.json` et modifiez la section `dependencies` :

    {
      "dependencies": {
        // Replace 0.* with a specific version:
        "Microsoft.ApplicationInsights.AspNet": "0.*",

       // Add these if they aren't already there:
       "Microsoft.Framework.ConfigurationModel.Interfaces": "1.0.0-beta4",
       "Microsoft.Framework.ConfigurationModel.Json":  "1.0.0-beta4"
      }
    }

#### Analyse du fichier de configuration

Dans `startup.cs` :

    using Microsoft.ApplicationInsights.AspNet;

    public IConfiguration Configuration { get; set; }

Dans la méthode `Startup` :

    public Startup(IHostingEnvironment env, IApplicationEnvironment appEnv)
    {
    	// Setup configuration sources.
    	var builder = new ConfigurationBuilder(appEnv.ApplicationBasePath)
	   		.AddJsonFile("config.json")
	   		.AddJsonFile($"config.{env.EnvironmentName}.json", optional: true);
    	builder.AddEnvironmentVariables();

    	if (env.IsEnvironment("Development"))
    	{
	    	builder.AddApplicationInsightsSettings(developerMode: true);
    	}
    
    	Configuration = builder.build();
    }

Dans la méthode `ConfigurationServices` :

    services.AddApplicationInsightsTelemetry(Configuration);

Dans la méthode `Configure` :

    // Add Application Insights monitoring to the request pipeline as a very first middleware.
    app.UseApplicationInsightsRequestTelemetry();

    // Any other error handling middleware goes here.

    // Add Application Insights exceptions handling to the request pipeline.
    app.UseApplicationInsightsExceptionTelemetry();

## Ajout de l’instrumentation de client JavaScript

Si vous avez un fichier \_Layout.cshtml, insérez le code suivant ici. Dans le cas contraire, placez le code dans une page que vous souhaitez analyser.

Définissez l’injection dans la partie supérieure du fichier :

    @inject Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration TelemetryConfiguration

Insérez l’application auxiliaire Html avant la balise `</head>` et avant tout autre script. N’importe quelle télémétrie JavaScript personnalisée que vous souhaitez signaler à partir de la page doit être injectée après cet extrait de code :

    <head> 

      @Html.ApplicationInsightsJavaScript(TelemetryConfiguration) 

      <!-- other scripts -->
    </head>

## Exécutez l'application.

Déboguez votre application dans Visual Studio, ou publiez-la sur votre serveur Web.

## Affichage des données relatives à votre application

Revenez au [portail Azure][portal] et accédez à votre ressource Application Insights. S’il n’existe aucune donnée dans le panneau Vue d’ensemble, attendez une minute ou deux et cliquez sur Actualiser.

* [Suivi de l'utilisation de votre application][usage]
* [Diagnostic des problèmes de performances][detect]
* [Configuration de tests Web pour surveiller la disponibilité][availability]



## Open source

[Lire et contribuer au code](https://github.com/Microsoft/ApplicationInsights-aspnet5)


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
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=Oct15_HO3-->