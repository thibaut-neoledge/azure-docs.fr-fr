<properties 
	pageTitle="Suivi des dépendances dans Application Insights" 
	description="Analysez l'utilisation, la disponibilité et les performances de votre application web locale ou Microsoft Azure avec Application Insights." 
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
	ms.date="10/08/2015" 
	ms.author="awills"/>


# Configurer Application Insights : diagnostiquer les exceptions

*Application Insights est à l'état de version préliminaire.*

[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) surveille vos applications en direct pour vous aider à [détecter et diagnostiquer les problèmes de performances et les exceptions][detect], mais aussi [découvrir comment votre application est utilisée][knowUsers].


<a name="selector1"></a>

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


En analysant votre application avec [Application Insights de Visual Studio][start], vous pouvez associer les demandes ayant échoué à des exceptions et à d’autres événements sur le client et le serveur, ce qui vous permet de diagnostiquer rapidement les causes.

Pour analyser une application ASP.NET, vous devez [ajouter le Kit de développement logiciel (SDK) Application Insights][greenbrown] à votre application, ou [installer le Status Monitor sur votre serveur IIS][redfield], ou, si votre application est une application web de Azure, ajoutez l’[extension Application Insights][azure].

## Diagnostic des défaillances 

Dans le panneau Vue d'ensemble, la vignette Défaillances vous montre les graphiques des exceptions et des demandes HTTP ayant échoué ainsi qu’une liste des URL demandées qui entraînent les défaillances les plus fréquentes.

![Sélection des défaillances](./media/app-insights-asp-net-exception-mvc/012-start.png)

Cliquez sur l’un des types de demande défaillante dans la liste pour obtenir des occurrences individuelles de cette défaillance. À partir de là, cliquez sur les exceptions ou les données de trace qui y sont associées :

![Sélectionnez l’instance d'une demande ayant échoué et, sous Détails de l'exception, accédez aux instances de l'exception.](./media/app-insights-asp-net-exception-mvc/030-req-drill.png)


**Autrement**, vous pouvez commencer à partir de la liste des exceptions que vous trouverez plus bas dans le panneau Défaillances. Cliquez jusqu'à arriver aux exceptions individuelles.


![Extraire](./media/app-insights-asp-net-exception-mvc/040-exception-drill.png)

*Aucune exception ne s’affiche ? Consulter [Capture des exceptions](#exceptions).*

À partir de là, vous pouvez consulter l’arborescence des appels de procédure et les propriétés détaillées de chaque exception et trouver le suivi du journal associé ou d’autres événements.


![Extraire](./media/app-insights-asp-net-exception-mvc/050-exception-properties.png)

[En savoir plus sur la recherche de diagnostic][diagnostic]

## Défaillances de dépendance

Une *dépendance* est un service que votre application appelle, généralement via une API REST ou une connexion à une base de données. [Application Insights Status Monitor][redfield] analyse automatiquement différents types d'appel de dépendance en déterminant la durée et la réussite ou la défaillance de l’appel.

Pour obtenir des données de dépendance, vous devez [installer Status Monitor][redfield] sur votre serveur IIS ou, si votre application est une application web d’Azure, utilisez l’[extension Application Insights][azure].

Les appels vers les dépendances ayant échoué sont répertoriés dans le panneau Défaillance et vous pouvez également les trouver sous Éléments associés dans les détails de la demande et les détails de l'exception.

*Aucune défaillance de dépendance ? Parfait ! Mais pour vérifier que vous obtenez des données de dépendance, ouvrez le panneau Performances et examinez le graphique de la durée de la dépendance.*

 

## Suivi personnalisé et données du journal

Pour obtenir des données de diagnostic propres à votre application, vous pouvez insérer le code pour envoyer vos propres données de télémétrie. Ces informations apparaissent dans Recherche de diagnostic avec la demande, une vue de la page et d’autres données automatiquement collectées.

Vous disposez de plusieurs options :

* [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) sert généralement à surveiller les modèles d’utilisation, mais les données qu’il envoie apparaissent également sous Événements personnalisés dans Recherche de diagnostic. Les événements sont nommés et peuvent contenir des propriétés de type chaîne et des métriques numériques sur lesquels vous pouvez [filtrer vos recherches de diagnostic][diagnostic].
* [TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) vous permet d’envoyer des données plus longues telles que des informations POST.
* [TrackException()](#exceptions) envoie des arborescences des appels de procédure. [Plus d’informations sur les exceptions](#exceptions).
* Si vous utilisez déjà un framework de journalisation comme Log4Net ou NLog, vous pouvez [capturer ces journaux][netlogs] et les visualiser dans Recherche de diagnostic avec les données sur les demandes et les exceptions.

Pour afficher ces événements, ouvrez [Recherche][diagnostic], ouvrez Filtre, puis choisissez Événement personnalisé, Trace ou Exception.


![Extraire](./media/app-insights-asp-net-exception-mvc/viewCustomEvents.png)

### Affichage des données POST de la demande

Les détails de la demande n'incluent pas les données envoyées à votre application dans un appel POST. Pour que ces données soient signalées :

* [Installez le Kit de développement logiciel (SDK)][greenbrown] dans votre projet d’application.
* Insérez du code dans votre application pour appeler [Microsoft.ApplicationInsights.TrackTrace()][api]. Envoyez les données POST dans le paramètre du message. Il existe une limite à la taille autorisée. Vous pouvez donc essayer d'envoyer uniquement les données essentielles.
* Lorsque vous examinez une demande ayant échoué, recherchez les traces associées.  

![Extraire](./media/app-insights-asp-net-exception-mvc/060-req-related.png)


## <a name="exceptions"></a> Capture des exceptions et des données de diagnostic connexes

Dans un premier temps, vous ne verrez pas dans le portail toutes les exceptions qui entraînent des défaillances dans votre application. Vous verrez les exceptions du navigateur (si vous utilisez le [Kit de développement logiciel (SDK) JavaScript][client] dans vos pages web). Mais la plupart des exceptions de serveur sont interceptées par IIS et vous devez écrire un peu de code afin de les afficher.

Vous pouvez :

* **Enregistrer explicitement des exceptions** en insérant le code dans les gestionnaires d'exceptions pour signaler ces exceptions.
* **Capturer automatiquement des exceptions** en configurant votre infrastructure ASP.NET. Les ajouts nécessaires sont différents selon les différents types d’infrastructure.

## Signalisation explicite des exceptions

La façon la plus simple consiste à insérer un appel à TrackException() dans un gestionnaire d'exceptions.

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Les paramètres de propriétés et de mesures sont facultatifs mais ils sont utiles pour [filtrer et ajouter][diagnostic] des informations supplémentaires. Par exemple, si vous avez une application qui peut exécuter plusieurs jeux, vous pouvez rechercher tous les rapports d'exception liés à un jeu particulier. Vous pouvez ajouter autant d'éléments que vous le souhaitez à chaque dictionnaire.

## Exceptions du navigateur

La plupart des exceptions de navigateur sont signalées.

Si votre page web inclut des fichiers de script à partir de réseaux de distribution de contenu ou d'autres domaines, vérifiez que votre balise de script possède l'attribut ```crossorigin="anonymous"``` et que le serveur envoie des [en-têtes CORS](http://enable-cors.org/). Cela vous permettra d'obtenir une arborescence des appels de procédure et les détails des exceptions JavaScript non gérées à partir de ces ressources.

## Formulaires web

Pour les formulaires web, le module HTTP pourra collecter les exceptions si aucune redirection n’est configurée avec CustomErrors.

Mais si vous avez des redirections actives, ajoutez les lignes suivantes à la fonction Application\_Error dans Global.asax.cs. (Ajouter un fichier Global.asax si vous n'en avez pas déjà).

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## MVC

Si la configuration de [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) est `Off`, les exceptions seront alors disponibles pour être collectées par le [module HTTP](https://msdn.microsoft.com/library/ms178468.aspx). Toutefois, si elle est `RemoteOnly` (valeur par défaut), ou `On`, l'exception ne sera alors pas disponible pour être collectée automatiquement par Application Insights. Vous pouvez corriger cela en remplaçant la [classe System.Web.Mvc.HandleErrorAttribute](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx) et en appliquant la classe remplacée comme indiqué pour les différentes versions MVC ci-dessous ([source github](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)) :

    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                } 
            }
            base.OnException(filterContext);
        }
      }
    }

#### MVC 2

Remplacez l'attribut HandleError par votre nouvel attribut dans vos contrôleurs.

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[Exemple](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### MVC 3

Enregistrez `AiHandleErrorAttribute` en tant que filtre global dans Global.asax.cs :

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[Exemple](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)



#### MVC 4, MVC 5

Enregistrez AiHandleErrorAttribute en tant que filtre global dans FilterConfig.cs :

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[Exemple](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## API Web 1.x


Remplacez System.Web.Http.Filters.ExceptionFilterAttribute :

    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }

Vous pouvez ajouter cet attribut remplacé à des contrôleurs spécifiques ou l’ajouter à la configuration du filtre global dans la classe WebApiConfig :

    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }

[Exemple](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

Il existe un certain nombre de cas que les filtres d'exception ne peuvent pas gérer. Par exemple :

* Les exceptions lancées à partir des constructeurs de contrôleur. 
* Les exceptions lancées à partir des gestionnaires de messages. 
* Les exceptions lancées pendant le routage. 
* Les exceptions lancées pendant la sérialisation du contenu de réponse. 

## API Web 2.x

Ajoutez d'une implémentation de IExceptionLogger :

    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }

Ajoutez cela aux services dans WebApiConfig :

    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger()); 
        }
      }
  }

[Exemple](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Alternativement, vous pouvez :

2. Remplacer le seul gestionnaire d’exceptions avec une implémentation personnalisée de IExceptionHandler. Celle-ci est appelée uniquement lorsque l'infrastructure est toujours en mesure de choisir le message de réponse à envoyer (mais pas lorsque la connexion est abandonnée par exemple) 
3. Les filtres d'exception (comme décrit dans la section sur les contrôleurs API Web 1.x ci-dessus) ne sont pas appelés dans tous les cas.


## WCF

Ajoutez une classe qui étend l'attribut et implémente IErrorHandler et IServiceBehavior.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above 
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error, 
            System.ServiceModel.Channels.MessageVersion version, 
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Ajoutez l'attribut aux implémentations de service :

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1 
        { 
         ...

[Exemple](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## Compteurs de performance des exceptions

Si vous avez [installé Status Monitor][redfield] sur votre serveur, vous pouvez obtenir un graphique du taux d’exceptions, mesuré par .NET. Celui-ci comprend les exceptions .NET gérées et non gérées.

Ouvrez un panneau Metric Explorer, ajoutez un nouveau graphique, puis sélectionnez **Taux d’exception** sous Compteurs de performance.

.NET Framework calcule le taux en comptant le nombre d’exceptions sur un intervalle et en divisant ce nombre par la longueur de l’intervalle.

Notez que ce chiffre sera différent du nombre d’« exceptions » calculé par le portail Application Insights, qui est basé sur les rapports TrackException. Les intervalles d’échantillonnage sont différents et le Kit de développement logiciel (SDK) n’envoie pas de rapports TrackException pour toutes les exceptions gérées et non gérées.

## Étapes suivantes

- [Dépendances](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [Données utilisateur et de page](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO3-->