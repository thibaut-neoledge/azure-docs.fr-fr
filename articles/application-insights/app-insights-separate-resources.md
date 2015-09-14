<properties 
	pageTitle="Ressources Application Insights distinctes pour le développement, le test et la production"
	description="Surveillez les performances et l'utilisation de votre application à différentes étapes du développement"
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
	ms.date="09/02/2015"
	ms.author="awills"/>

# Ressources Application Insights distinctes pour le développement, le test et la production


Pour éviter le mélange des valeurs de télémétrie des versions de débogage, de test et de production de votre application, créez des ressources [Application Insights][start] distinctes pour recevoir les données à partir de chaque version.

Les données reçues de votre application sont stockées et traitées par Application Insights dans une *ressource* Microsoft Azure. Chaque ressource est identifiée par une *clé d’instrumentation*. Dans votre application, la clé est fournie au SDK Application Insights afin qu'il puisse envoyer les données collectées à la ressource appropriée. La clé peut être fournie dans le code ou dans ApplicationInsights.config. En modifiant la clé dans le SDK, vous pouvez diriger les données vers différentes ressources.


## Création d’une ressource Application Insights dans Azure
  

Dans le portail [portal.azure.com](https://portal.azure.com), ajoutez une ressource Application Insights :

![Cliquez sur Nouveau > Application Insights](./media/app-insights-create-new-resource/01-new.png)


* Le **type d’application** définit le contenu du panneau de présentation et les propriétés disponibles dans [Metrics Explorer][metrics]. Si vous ne voyez pas votre type d’application, choisissez un des types web pour les pages web et un des types de téléphone pour les autres appareils.
* **Groupe de ressources** facilite la gestion des propriétés telles que le [contrôle d’accès](app-insights-resources-roles-access-control.md). Vous pouvez utiliser des groupes de ressources distincts pour le développement, le test et la production.
* **Abonnement** est votre compte de paiement dans Azure.
* **Emplacement** correspond à l’endroit où nous conservons vos données. Actuellement, il n’est pas possible de le modifier.
* **Ajouter au tableau d’accueil** place une vignette d’accès rapide à votre ressource sur votre page d’accueil Azure. 

La création de la ressource prend quelques secondes. Une alerte vous prévient lorsque l’opération est terminée.

(Vous pouvez écrire un [script PowerShell](app-insights-powershell-script-create-resource.md) pour créer automatiquement une ressource.)


## Copie de la clé d’instrumentation

La clé d'instrumentation identifie la ressource que vous avez créée.

![Cliquez sur Essentials, sur la clé d'instrumentation, puis appuyez sur CTRL+C](./media/app-insights-create-new-resource/02-props.png)

Vous aurez besoin des clés d'instrumentation de toutes les ressources auxquelles votre application envoie des données.


## <a name="dynamic-ikey"></a>Clé d'instrumentation dynamique

Le Kit de développement logiciel (SDK) obtient généralement l'iKey du fichier ApplicationInsights.config. Facilitez plutôt la modification de cette clé en la définissant dans votre code.

Définissez la clé dans une méthode d'initialisation, par exemple global.aspx.cs dans un service ASP.NET :

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Dans cet exemple, les ikeys des différentes ressources sont placées dans différentes versions du fichier de configuration web. L’échange du fichier de configuration web échangera la ressource cible.

#### Pages web

L'iKey est également utilisée dans les pages web de votre application, dans le [script que vous avez obtenu à partir du panneau de démarrage rapide](app-insights-javascript.md). Au lieu de la coder littéralement dans le script, vous devez la générer à partir de l'état du serveur. Par exemple, dans une application ASP.NET :

*JavaScript dans Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...





<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-get-started.md

 

<!---HONumber=September15_HO1-->