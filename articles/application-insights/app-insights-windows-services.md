<properties 
	pageTitle="Application Insights pour les services Windows" 
	description="Analysez l’utilisation et les performances des services en arrière-plan Windows avec Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2016" 
	ms.author="awills"/>

# Application Insights sur des services en arrière-plan Windows

*Application Insights est à l'état de version préliminaire.*

[Visual Studio Application Insights](app-insights-get-started.md) vous permet d’analyser vos applications déployées en termes d’utilisation et de performances.

Toutes les applications Windows (y compris les services en arrière-plan et les rôles de travail) peuvent utiliser le Kit de développement logiciel (SDK) du logiciel Application Insights pour envoyer des données de télémétrie à Application Insights. Vous pouvez également ajouter le SDK à un projet de bibliothèque de classes.

Vous pouvez choisir les collecteurs de données standard que vous souhaitez utiliser (par exemple, pour surveiller les compteurs de performances ou les appels de dépendance) ou simplement utiliser l'API de base et écrire votre propre télémétrie.

Vérifiez tout d’abord si vous travaillez avec un autre type d’application Windows :

* Applications web : accédez à [ASP.NET 4](app-insights-asp-net.md), [ASP.NET 5](app-insights-asp-net-five.md).
* [Azure Cloud Services](app-insights-cloudservices.md)
* Applications de bureau : nous vous recommandons [HockeyApp](https://hockeyapp.net). Avec HockeyApp, vous pouvez gérer la distribution, le test direct et les commentaires des utilisateurs, de même que surveiller l’utilisation et les rapports d’incident. Vous pouvez également [envoyer les données de télémétrie à Application Insights à partir d’une application de bureau](app-insights-windows-desktop.md). 


## <a name="add"></a> Création d’une ressource Application Insights


1.  Dans le [portail Azure][portal], créez une ressource Application Insights. Choisissez ASP.NET comme type d’application. 

    ![Cliquez sur Nouveau > Application Insights](./media/app-insights-windows-services/01-new.png)


2.  Copiez la clé d'instrumentation. Recherchez la clé dans la liste déroulante Essentials de la nouvelle ressource que vous venez de créer. Fermez le plan de l’application ou faites défiler vers la gauche vers le volet Vue d’ensemble de la ressource.

    ![Cliquez sur Essentials, sélectionnez la clé et appuyez sur Ctrl + C](./media/app-insights-windows-services/10.png)

## <a name="sdk"></a>Installation du Kit de développement logiciel (SDK) dans votre application


1. Dans Visual Studio, modifiez les packages NuGet de votre projet d’application Windows.

    ![Cliquez avec le bouton droit sur le projet et sélectionnez Gérer les packages NuGet](./media/app-insights-windows-services/03-nuget.png)

2. Installez le package Application Insights de Windows Server : Microsoft.ApplicationInsights.WindowsServer

    ![Recherchez « Application Insights »](./media/app-insights-windows-services/04-ai-nuget.png)

    *Puis-je utiliser d’autres packages ?*

    Oui. Choisissez l’API de base (Microsoft.ApplicationInsights) si vous souhaitez uniquement utiliser l’API pour envoyer vos données de télémétrie. Le package Windows Server inclut automatiquement l'API de base ainsi qu’un certain nombre d’autres packages, tels que les packages de collecte des compteurs de performances et d’analyse de dépendance.


3. Définissez votre clé d’instrumentation.

    * Si vous avez installé uniquement le package d’API de base Microsoft.ApplicationInsights, vous devez définir la clé dans le code, par exemple dans main() : 

    `TelemetryConfiguration.Active.InstrumentationKey = "` *votre clé* `";`

    Si vous avez installé un des autres packages, vous pouvez définir la clé à l’aide de code ou la définir dans le fichier ApplicationInsights.config :
 
    `<InstrumentationKey>`*votre clé*`</InstrumentationKey>`

    Si vous utilisez ApplicationInsights.config, assurez-vous que ses propriétés dans l’Explorateur de solutions sont définies sur **Build Action = Content, Copy to Output Directory = Copy**.

## <a name="telemetry"></a>Insertion d’appels de télémétrie

Utilisez l’une des [API Application Insights][api] pour envoyer des données de télémétrie. Si vous utilisez l’API de base, aucune donnée de télémétrie n’est envoyée automatiquement. En général, vous allez utiliser :

* `TrackPageView(pageName)` lors du changement de formulaires, de pages ou d’onglets
* `TrackEvent(eventName)` pour d’autres actions utilisateur
* `TrackMetric(name, value)` dans une tâche en arrière-plan pour envoyer des rapports de métriques réguliers non liés à des événements spécifiques.
* `TrackTrace(logEvent)` pour la [journalisation de diagnostic][diagnostic]
* `TrackException(exception)` dans les clauses catch
* `Flush()` pour vous assurer que toutes les données de télémétrie sont envoyées avant la fermeture de l’application Utiliser uniquement si vous utilisez l’API de base (Microsoft.ApplicationInsights). Les Kits de développement logiciel (SDK) web implémentent automatiquement ce comportement. (Si votre application s’exécute dans des contextes où Internet n’est pas toujours disponible, consultez aussi [Canal de persistance](#persistence-channel).)


#### Initialiseurs de télémétrie

Pour afficher le nombre d'utilisateurs et de sessions, vous pouvez définir les valeurs de chaque instance `TelemetryClient`. Vous pouvez également utiliser un initialiseur de télémétrie afin d’effectuer cet ajout pour tous les clients :

```C#

    class UserSessionInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            telemetry.Context.User.Id = Environment.UserName;
            telemetry.Context.Session.Id = Guid.NewGuid().ToString();
        }
        
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>Exécution de votre projet

[Exécutez votre application en appuyant sur F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) et utilisez-la pour générer des données de télémétrie.

Un décompte des événements envoyés s'affiche dans Visual Studio.

![](./media/app-insights-windows-services/appinsights-09eventcount.png)

Les événements s’affichent également dans les fenêtres de diagnostics et de sortie.

## <a name="monitor"></a>Affichage des données d’analyse

Revenez au volet de votre application dans le portail Azure.

Les premiers événements seront affichés dans la fenêtre de flux dynamique.


## Canal de persistance 

Si votre application s’exécute à l’endroit où une connexion n’est pas toujours disponible ou est très lente, prévoyez d’utiliser la couche de persistance et le canal mémoire interne par défaut.

Le canal de mémoire par défaut perd toute télémétrie qui n’a pas été envoyée au moment où l’application se ferme. Bien que vous puissiez utiliser `Flush()` pour tenter d’envoyer les données restantes dans la mémoire tampon, il perdra tout de même les données si aucune connexion Internet n’est disponible ou si l’application s’arrête avant la fin de la transmission.

En revanche, le canal de persistance met en mémoire tampon la télémétrie dans un fichier, avant de l’envoyer au portail. `Flush()` garantit que les données sont stockées dans le fichier. Si aucune donnée n’est envoyée au moment où l’application se ferme, elle reste dans le fichier. Lorsque l’application redémarre, les données sont envoyées ensuite s’il existe une connexion internet. Les données s’accumuleront dans le fichier aussi longtemps que nécessaire, jusqu’à ce qu’une connexion est disponible.

### Pour utiliser le canal de persistance

1. Importez le package NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Incluez ce code dans votre application, dans un emplacement d’initialisation approprié :
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Utilisez `telemetryClient.Flush()` avant que votre application ferme, pour vous assurer que les données sont envoyées au portail ou enregistrées dans votre fichier.

    Notez que Flush() est synchrone pour le canal de persistance, mais asynchrone pour les autres canaux.

 
Le canal de persistance est optimisé pour les scénarios d’appareils, où le nombre d’événements générés par l’application est relativement faible et la connexion est souvent peu fiable. Ce canal écrira les événements sur le disque dans un espace de stockage fiable tout d’abord, puis essaiera de les envoyer.

#### Exemple

Disons que vous souhaitez surveiller les exceptions non traitées. Commencez par vous abonner à l'événement `UnhandledException`. Dans le rappel, incluez un appel de vidage pour vous assurer que la télémétrie est bien persistante.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Une fois que l’application s’arrête, vous voyez un fichier contenant les événements compressés dans `%LocalAppData%\Microsoft\ApplicationInsights`.
 
Lors du prochain que démarrage de cette application, le canal récupérera le fichier et transmettra la télémétrie à Application Insights si possible.

#### Exemple de test

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


Le code du canal de persistance est disponible sur [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel).


## <a name="usage"></a>Étapes suivantes

[Suivi de l'utilisation de votre application][knowUsers]

[Capture et recherche des journaux de diagnostic][diagnostic]

[Résolution des problèmes][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=AcomDC_0518_2016-->