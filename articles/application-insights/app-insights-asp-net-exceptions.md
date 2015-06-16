<properties 
	pageTitle="Diagnostic des défaillances et des exceptions dans les applications ASP.NET avec Application Insights" 
	description="Configurez votre application pour obtenir une expérience de diagnostic intéressante en capturant les exceptions ainsi que la télémétrie de demande." 
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
	ms.date="04/26/2015" 
	ms.author="awills"/>
 
# Diagnostic des défaillances et des exceptions dans les applications ASP.NET avec Application Insights  

En analysant votre application avec [Application Insights de Visual Studio][start], vous pouvez associer les demandes ayant échoué à des exceptions et à d’autres événements sur le client et le serveur, ce qui vous permet de diagnostiquer rapidement les causes.

Pour analyser une application ASP.NET, vous devez [ajouter le Kit de développement logiciel (SDK) Application Insights][greenbrown] à votre application, ou [installer le Status Monitor sur votre serveur IIS][redfield], ou, si votre application est une application web de Azure, ajoutez l’[extension Application Insights][azure].

## Diagnostic des défaillances 

Dans le panneau Vue d'ensemble, la vignette Défaillances vous montre les graphiques des exceptions et des demandes HTTP ayant échoué ainsi qu’une liste des URL demandées qui entraînent les défaillances les plus fréquentes.

![Sélection des défaillances](./media/app-insights-asp-net-exceptions/012-start.png)

Cliquez sur l’un des types de demande défaillante dans la liste pour obtenir des occurrences individuelles de cette défaillance. À partir de là, cliquez sur les exceptions ou les données de trace qui y sont associées :

![Sélectionnez l’instance d'une demande ayant échoué et, sous Détails de l'exception, accédez aux instances de l'exception.](./media/app-insights-asp-net-exceptions/030-req-drill.png)

*Aucune exception ne s’affiche ? Consulter [Capture des exceptions](#exceptions).*

Autrement, vous pouvez commencer depuis la liste des exceptions que vous trouverez plus bas dans le panneau Défaillances. Cliquez jusqu'à arriver aux exceptions individuelles.


![Extraire](./media/app-insights-asp-net-exceptions/040-exception-drill.png)

*Aucune exception ne s’affiche ? Consulter [Capture des exceptions](#exceptions).*

À partir de là, vous pouvez consulter l’arborescence des appels de procédure et les propriétés détaillées de chaque exception et trouver le suivi du journal associé ou d’autres événements.


![Extraire](./media/app-insights-asp-net-exceptions/050-exception-properties.png)

[En savoir plus sur la recherche de diagnostic][diagnostic]

## Défaillances de dépendance

Une *dépendance* est un service que votre application appelle, généralement via une API REST ou une connexion à une base de données. [Application Insights Status Monitor][redfield] analyse automatiquement différents types d'appel de dépendance en déterminant la durée et la réussite ou la défaillance de l’appel.

Pour obtenir des données de dépendance, vous devez [installer Status Monitor][redfield] sur votre serveur IIS ou, si votre application est une application web d’Azure, utilisez l’[extension Application Insights][azure]. Pour cela, vous pouvez

Les appels vers les dépendances ayant échoué sont répertoriés dans le panneau Défaillance et vous pouvez également les trouver sous Éléments associés dans les détails de la demande et les détails de l'exception.

*Aucune défaillance de dépendance ? Parfait ! Mais pour vérifier que vous obtenez des données de dépendance, ouvrez le panneau Performances et examinez le graphique de la durée de la dépendance.*

## Affichage d’une demande POST et d’autres données du journal

Les détails de la demande n'incluent pas les données envoyées à votre application dans un appel POST. Pour que ces données soient signalées :

* [Installez le Kit de développement logiciel (SDK)][greenbrown] dans votre projet d’application.
* Insérez du code dans votre application pour appeler [Microsoft.ApplicationInsights.TrackTrace()][api]. Envoyez les données POST dans le paramètre du message. Il existe une limite à la taille autorisée. Vous pouvez donc essayer d'envoyer uniquement les données essentielles.
* Lorsque vous examinez une demande ayant échoué, recherchez les traces associées.  

![Extraire](./media/app-insights-asp-net-exceptions/060-req-related.png)

Si vous utilisez déjà un framework de journalisation tel que Log4Net ou NLog, vous pouvez [capturer ces journaux][netlogs] et les afficher de la même façon.

Les [événements personnalisés][api] sont généralement utilisés pour le suivi d'utilisation mais vous pouvez également les trouver sous « Toute la télémétrie pour cette demande ».


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

Mais si vous avez des redirections actives, ajoutez les lignes suivantes à la fonction Application_Error dans Global.asax.cs. (Ajouter un fichier Global.asax si vous n'en avez pas déjà).

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
        [ServiceContract]
        [AiLogException]
        public interface IService1
        {
     ...

[Exemple](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[netlogs]: app-insights-asp-net-trace-logs.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=58--> 