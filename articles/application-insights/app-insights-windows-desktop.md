<properties 
	pageTitle="Application Insights pour les services et applications de bureau Windows" 
	description="Analysez l’utilisation et les performances de votre application de bureau Windows avec Application Insights." 
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
	ms.date="09/07/2015" 
	ms.author="awills"/>

# Application Insights sur des rôles de travail, des services et des applications de bureau Windows

*Application Insights est à l'état de version préliminaire.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights vous permet d'analyser l’utilisation et les performances de vos applications déployées.

Toutes les applications Windows (y compris les applications de bureau, les services d’arrière-plan et les rôles de travail) peuvent utiliser le Kit de développement logiciel (SDK) principal du logiciel Application Insights pour envoyer des données de télémétrie à Application Insights. Vous pouvez également ajouter un kit de développement logiciel d’Application Insights à un projet de bibliothèque de classes.

La Kit de développement logiciel (SDK) principal fournit simplement une API : contrairement aux Kits de développement logiciel (SDK) web ou pour appareil, il n’inclut pas tous les modules qui collectent les données automatiquement. Vous devez donc écrire du code pour envoyer vos propres données de télémétrie. Parmi les autres packages, par exemple, le collecteur de compteur de performances fonctionnera également dans une application de bureau.


## <a name="add"></a> Création d’une ressource Application Insights


1.  Dans le [portail Azure][portal], créez une ressource Application Insights. Choisissez le type d’application Windows Store. 

    ![Cliquez sur Nouveau > Application Insights](./media/app-insights-windows-desktop/01-new.png)

    Le choix du type d'application définit le contenu du volet Vue d'ensemble et les propriétés disponibles dans [Metrics Explorer][metrics].

2.  Copiez la clé d'instrumentation.

    ![Cliquez sur Propriétés, sélectionnez la clé et appuyez sur ctrl + C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Installation du Kit de développement logiciel (SDK) dans votre application


1. Dans Visual Studio, modifiez les packages NuGet de votre projet d’application de bureau.

    ![Cliquez avec le bouton droit sur le projet et sélectionnez Gérer les packages NuGet](./media/app-insights-windows-desktop/03-nuget.png)

2. Installez le package d’API Core Application Insights : Microsoft.ApplicationInsights.

    ![Recherchez « Application Insights »](./media/app-insights-windows-desktop/04-core-nuget.png)

    *Puis-je utiliser d’autres packages ?*

    Vous pouvez installer d’autres packages tels que le compteur de performances ou des packages de collecteur de dépendances si vous souhaitez utiliser leurs modules. Microsoft.ApplicationInsights.Web contient plusieurs de ces packages. Si vous souhaitez utiliser les [packages de collecteurs de journaux ou de traces](app-insights-asp-net-trace-logs.md), démarrez avec le package de serveur web.

    (Mais n’utilisez pas Microsoft.ApplicationInsights.Window : cela concerne les applications Windows Store.)

3. Définissez votre clé d’instrumentation.

    * Si vous avez installé uniquement le package d’API de base Microsoft.ApplicationInsights, vous devez définir la clé dans le code, par exemple dans main() : 

     `TelemetryConfiguration.Active.InstrumentationKey = "`* votre clé *`";`

    * Si vous avez installé un des autres packages, vous pouvez définir la clé à l’aide de code ou la définir dans le fichier ApplicationInsights.config :
 
     `<InstrumentationKey>`* votre clé *`</InstrumentationKey>`



## <a name="telemetry"></a>Insertion d’appels de télémétrie

Créez une instance de `TelemetryClient` puis [utilisez-la pour envoyer des données de télémétrie][api].


Par exemple, dans une application Windows Forms, vous pouvez écrire :

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.GetUserName();
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

Utilisez l’une des [API Application Insights][api] pour envoyer des données de télémétrie. Dans les applications de bureau Windows, aucune donnée de télémétrie n'est envoyée automatiquement. En général, vous allez utiliser :

* `TrackPageView(pageName)` sur des formulaires, des pages ou des onglets de basculement
* `TrackEvent(eventName)` pour d’autres actions utilisateur
* `TrackMetric(name, value)` dans une tâche en arrière-plan pour envoyer des rapports de métriques réguliers qui non liés à des événements spécifiques.
* `TrackTrace(logEvent)` pour la [journalisation de diagnostic][diagnostic]
* `TrackException(exception)` dans les clauses catch
* `Flush()` pour vous assurer que toutes les données de télémétrie sont envoyées avant la fermeture de l’application Utiliser uniquement si vous utilisez l’API de base (Microsoft.ApplicationInsights). Les Kits de développement logiciel (SDK) et le web mettent automatiquement en œuvre ce comportement. (Si votre application s’exécute dans les contextes où internet n’est pas toujours disponible, voir aussi [Canal de persistance](#persistence-channel).)


#### Initialiseurs de contexte

Pour afficher le nombre d'utilisateurs et de sessions, vous pouvez définir les valeurs de chaque instance `TelemetryClient`. Vous pouvez également utiliser un initialiseur de contexte afin d’effectuer cet ajout pour tous les clients :

```C#

    class UserSessionInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = Guid.NewGuid().ToString();
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>Exécution de votre projet

[Exécutez votre application en appuyant sur F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) et utilisez-la pour générer des données de télémétrie.

Un décompte des événements envoyés s'affiche dans Visual Studio.

![](./media/app-insights-windows-desktop/appinsights-09eventcount.png)



## <a name="monitor"></a>Affichage des données d’analyse

Revenez au volet de votre application dans le portail Azure.

Les premiers événements s’affichent dans [Recherche de diagnostic](app-insights-diagnostic-search.md).

Après quelques secondes, cliquez sur Actualiser pour obtenir des données supplémentaires.

Si vous avez utilisé TrackMetric ou le paramètre de mesures de TrackEvent, ouvrez [Metrics Explorer][metrics] et le panneau Filtres. Vos mesures devraient apparaître ici, mais elles peuvent quelquefois prendre un moment pour traverser le pipeline, et vous devez donc fermer le panneau Filtres, attendre un peu, puis actualiser.



## Canal de persistance 

Si votre application s’exécute à l’endroit où une connexion n’est pas toujours disponible ou est très lente, prévoyez d’utiliser la couche de persistance et le canal mémoire interne par défaut.

Le canal de mémoire par défaut perd toute télémétrie qui n’a pas été envoyée au moment où l’application se ferme. Bien que vous puissiez utiliser `Flush()` pour tenter d'envoyer les données restantes dans la mémoire tampon, il perdra tout de même les données si aucune connexion Internet n'est disponible, ou si l'application s'arrête avant la fin de la transmission.

En revanche, le canal de persistance met en mémoire tampon la télémétrie dans un fichier, avant de l’envoyer au portail. `Flush()` garantit que les données sont stockées dans le fichier. Si aucune donnée n’est envoyée au moment où l’application se ferme, elle reste dans le fichier. Lorsque l’application redémarre, les données sont envoyées ensuite s’il existe une connexion internet. Les données s’accumuleront dans le fichier aussi longtemps que nécessaire, jusqu’à ce qu’une connexion est disponible.

### Pour utiliser le canal de persistance

1. Importer le package NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel).
2. Incluez ce code dans votre application, dans un emplacement d’initialisation approprié :
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Utilisez `telemetryClient.Flush()` avant que votre application ferme, pour vous assurer que les données sont soit envoyées au portail, soit enregistrées dans votre fichier.

 
Le canal de persistance est optimisé pour les scénarios d’appareils, où le nombre d’événements générés par l’application est relativement faible et la connexion est souvent peu fiable. Ce canal écrira les événements sur le disque dans un espace de stockage fiable tout d’abord, puis essaiera de les envoyer.

#### Exemple

Disons que vous souhaitez surveiller les exceptions non traitées. Commencez par vous abonner à l’événement `UnhandledException`. Dans le rappel, incluez un appel de vidage pour vous assurer que la télémétrie est bien persistante.
 
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


Le code du canal de persistance est sur [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/master/src/TelemetryChannels/PersistenceChannel).


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
 

<!---HONumber=Sept15_HO4-->