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
	ms.date="05/04/2016" 
	ms.author="awills"/>

# Séparer des ressources Application Insights

Les données de télémétrie de différents composants et différentes versions de votre application doivent-elles être envoyées à différentes ressources Application Insights, ou combinées en une seule ? Cet article décrit les meilleures pratiques et les techniques nécessaires.

Tout d’abord, essayons de comprendre la question. Les données reçues de votre application sont stockées et traitées par Application Insights dans une *ressource* Microsoft Azure. Chaque ressource est identifiée par une *clé d’instrumentation* (iKey). Dans votre application, la clé est fournie au SDK Application Insights afin qu'il puisse envoyer les données collectées à la ressource appropriée. La clé peut être fournie dans le code ou dans ApplicationInsights.config. En modifiant la clé dans le SDK, vous pouvez diriger les données vers différentes ressources.

Dans un cas simple, lorsque vous créez le code d’une nouvelle application, vous créez également une ressource dans Application Insights. Dans Visual Studio, la boîte de dialogue *Nouveau projet* le fait pour vous.

S’il s’agit d’un site web volumineux, il peut être déployé sur plusieurs instances de serveur.

Dans des scénarios plus complexes, vous disposez d’un système constitué de plusieurs composants : par exemple, un site web et un processeur back end.

## Quand utiliser des iKeys distinctes

Voici quelques recommandations générales à prendre en compte :

* Lorsque vous avez une unité d’application pouvant être déployée indépendamment qui s’exécute sur un ensemble d’instances de serveur capables de monter/descendre en puissance indépendamment des autres composants, il est généralement conseillé de la mapper à une seule ressource - autrement dit, elle aura une clé d’instrumentation (iKey) unique.
* En revanche, voici quelques-unes des raisons d’utiliser des iKeys distinctes :
 - Lire facilement des métriques distinctes de composants distincts.
 - Séparer la télémétrie de faibles volumes de la télémétrie de gros volumes, afin que la limitation, les quotas et l’échantillonnage sur un flux n’affectent pas l’autre.
 - Alertes, exportation et configurations des éléments de travail distinctes.
 - Répartir les [limites](app-insights-pricing.md#limits-summary), notamment le nombre de tests web, la limitation et le quota de télémétrie.
 - Le code en cours de développement et de test doit envoyer à une autre iKey que l’horodatage de production.  

De nombreuses expériences du portail Application Insights sont conçues dans l’esprit de ces recommandations. Par exemple, les serveurs affichent des segments sur l’instance de serveur, ce qui suppose que les données de télémétrie d’un même composant logique peuvent provenir de plusieurs instances de serveur.

## iKey unique

Voici les cas où vous envoyez les données de télémétrie de plusieurs composants à une seule iKey :

* Ajouter une propriété à toutes les données de télémétrie pour segmenter et filtrer sur l’identité du composant. Cela se produit automatiquement avec des instances de rôle de serveur mais, dans d’autres cas, vous pouvez utiliser un [initialiseur de télémétrie](app-insights-api-filtering-sampling.md#add-properties) pour ajouter la propriété.
* Mettre à jour les Kits de développement logiciel (SDK) Application Insights dans les différents composants en même temps. La télémétrie d’une iKey doit provenir de la même version du SDK.

## iKeys distinctes

Voici les cas où vous avez plusieurs iKeys pour différents composants d’applications :

* Créer un [tableau de bord](app-insights-dashboards.md) pour afficher la télémétrie clé de votre application logique, à partir d’une combinaison des différents composants de l’application. Les tableaux de bord peuvent être partagés : un affichage unique du système logique peut donc être utilisé par différentes équipes.
* Organiser les [groupes de ressources](app-insights-resources-roles-access-control.md) au niveau de l’équipe. Les autorisations d’accès sont attribuées par groupe de ressources et comprennent notamment des autorisations pour configurer les alertes. 
* Utiliser les [modèles Azure Resource Manager et PowerShell](app-insights-powershell.md) pour gérer les artefacts tels que les règles d’alerte et les tests web.



## iKeys distinctes pour le développement/test et la production

Pour faciliter le changement automatique de clé lorsque votre application est publiée, définissez l’iKey dans le code plutôt que dans ApplicationInsights.config.

### <a name="dynamic-ikey"></a>Clé d'instrumentation dynamique

Définissez la clé dans une méthode d'initialisation, par exemple global.aspx.cs dans un service ASP.NET :

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Dans cet exemple, les ikeys des différentes ressources sont placées dans différentes versions du fichier de configuration web. Le remplacement du fichier de configuration web, que vous pouvez effectuer dans le cadre du script de lancement, remplacera la ressource cible.

### Pages web

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


## Création d’une ressource Application Insights supplémentaire
  
Si vous décidez de séparer la télémétrie de différents composants d’applications ou de différents horodatages (développement/test/production) du même composant, vous devez créer une ressource Application Insights.

Dans le portail [portal.azure.com](https://portal.azure.com), ajoutez une ressource Application Insights :

![Cliquez sur Nouveau > Application Insights](./media/app-insights-separate-resources/01-new.png)


* Le **type d’application** définit le contenu du panneau de présentation et les propriétés disponibles dans [Metrics Explorer](app-insights-metrics-explorer.md). Si vous ne voyez pas votre type d’application, choisissez un des types web pour les pages web.
* **Groupe de ressources** facilite la gestion des propriétés telles que le [contrôle d’accès](app-insights-resources-roles-access-control.md). Vous pouvez utiliser des groupes de ressources distincts pour le développement, le test et la production.
* **Abonnement** est votre compte de paiement dans Azure.
* **Emplacement** correspond à l’endroit où nous conservons vos données. Actuellement, il n’est pas possible de le modifier. 
* **Ajouter au tableau de bord** place une vignette d’accès rapide à votre ressource sur votre page d’accueil Azure. 

La création de la ressource prend quelques secondes. Une alerte vous prévient lorsque l’opération est terminée.

(Vous pouvez écrire un [script PowerShell](app-insights-powershell-script-create-resource.md) pour créer automatiquement une ressource.)


## Récupération de la clé d’instrumentation

La clé d'instrumentation identifie la ressource que vous avez créée.

![Cliquez sur Essentials, sur la clé d'instrumentation, puis appuyez sur CTRL+C](./media/app-insights-separate-resources/02-props.png)

Vous aurez besoin des clés d'instrumentation de toutes les ressources auxquelles votre application envoie des données.


 

<!---HONumber=AcomDC_0518_2016-->