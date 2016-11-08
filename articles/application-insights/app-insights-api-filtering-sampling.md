---
title: Filtrage et pré-traitement dans le Kit de développement logiciel (SDK) Application Insights | Microsoft Docs
description: Écrivez des processeurs de télémétrie et des initialiseurs de télémétrie que le Kit de développement logiciel (SDK) doit filtrer ou ajoutez des propriétés aux données avant que la télémétrie ne soit envoyée au portail Application Insights.
services: application-insights
documentationcenter: ''
author: beckylino
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 08/30/2016
ms.author: borooji

---
# Filtrage et pré-traitement de la télémétrie dans le Kit de développement logiciel (SDK) Application Insights
*Application Insights est à l'état de version préliminaire.*

Vous pouvez écrire et configurer des plug-ins pour le Kit de développement logiciel (SDK) Application Insights afin de personnaliser la capture et le traitement des données de télémétrie avant leur envoi au service Application Insights.

Actuellement, ces fonctionnalités sont disponibles pour le Kit de développement logiciel (SDK) ASP.NET.

* L'[échantillonnage](app-insights-sampling.md) réduit le volume des données de télémétrie sans affecter les statistiques. Il maintient ensemble les points de données liés de sorte que vous pouvez naviguer entre eux pour diagnostiquer un problème. Dans le portail, les nombres totaux sont multipliés pour compenser l'échantillonnage.
* Le [filtrage avec des processeurs de télémétrie](#filtering) vous permet de sélectionner ou de modifier la télémétrie dans le Kit de développement logiciel (SDK) avant l’envoi au serveur. Par exemple, vous pouvez réduire le volume de la télémétrie en excluant les demandes émanant de robots. Mais le filtrage est une approche plus simple que l’échantillonnage pour réduire le trafic. Cela vous permet de mieux contrôler ce qui est transmis, mais vous devez être conscient que cela affecte vos statistiques ; par exemple, si vous filtrez toutes les demandes réussies.
* [Les initialiseurs de télémétrie ajoutent des propriétés](#add-properties) à n’importe quelle télémétrie envoyée à partir de votre application, notamment les données de télémétrie fournies par les modules standard. Par exemple, vous pouvez ajouter des valeurs calculées ou des numéros de version permettant de filtrer les données dans le portail.
* [L'API SDK](app-insights-api-custom-events-metrics.md) est utilisée pour envoyer des événements et des mesures personnalisés.

Avant de commencer :

* Installez le [SDK Application Insights pour ASP.NET v2](app-insights-asp-net.md) dans votre application.

<a name="filtering"></a>

## Filtrage : ITelemetryProcessor
Cette technique vous offre un contrôle plus direct sur ce qui est inclus ou exclu du flux de télémétrie. Vous pouvez l'utiliser conjointement avec l'échantillonnage, ou séparément.

Pour filtrer la télémétrie, vous écrivez un processeur de télémétrie et l'enregistrez avec le Kit de développement logiciel (SDK). Toute la télémétrie passe par votre processeur et vous pouvez choisir de la supprimer du flux ou d'ajouter des propriétés. Cela inclut les données de télémétrie fournies par les modules standard tels que le collecteur de demandes HTTP et le collecteur de dépendances, ainsi que les données de télémétrie que vous avez écrites vous-même. Vous pouvez, par exemple, exclure la télémétrie concernant les demandes émanant de robots ou les appels de dépendance réussis.

> [!WARNING]
> Filtrer la télémétrie envoyée depuis le Kit de développement logiciel (SDK) à l’aide de processeurs peut fausser les statistiques que vous voyez dans le portail et rendre difficile le suivi des éléments associés.
> 
> Envisagez plutôt d'utiliser l'[échantillonnage](app-insights-sampling.md).
> 
> 

### Créer un processeur de télémétrie
1. Vérifiez que la version du SDK Application Insights dans votre projet est la version 2.0.0 ou ultérieure. Cliquez avec le bouton droit sur votre projet dans l'explorateur de solutions Visual Studio, puis sélectionnez Gérer les packages NuGet. Dans le Gestionnaire de package NuGet, cochez Microsoft.ApplicationInsights.Web.
2. Pour créer un filtre, déployez ITelemetryProcessor. Il s’agit d’un autre point d’extensibilité, à l’instar d’un module de télémétrie, d’un initialiseur de télémétrie et d’un canal de télémétrie.
   
    Notez que les processeurs de télémétrie construisent une chaîne de traitement. Lorsque vous instanciez un processeur de télémétrie, vous transmettez un lien au processeur suivant dans la chaîne. Lorsqu’un point de données de télémétrie est transmis à la méthode de traitement, il effectue son travail, puis appelle le processeur de télémétrie suivant dans la chaîne.
   
    ``` C#
   
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;
   
    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
   
        private ITelemetryProcessor Next { get; set; }
   
        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }
   
        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);
   
            this.Next.Process(item);
        }
   
        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;
   
            return dependency.Success != true;
        }
   
        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
1. Insérez-la dans ApplicationInsights.config :

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Il s’agit de la section dans laquelle vous initialisez un filtre d’échantillonnage.)

Vous pouvez transférer des valeurs de chaîne depuis le fichier .config en fournissant des propriétés publiques nommées dans votre classe.

> [!WARNING]
> Veillez à faire correspondre le nom de type et les noms de propriété dans le fichier .config aux noms de classe et de propriété dans le code. Si le fichier .config fait référence à un type ou à une propriété qui n'existe pas, le kit de développement peut échouer lors de l'envoi d'une télémétrie quelconque.
> 
> 

Vous pouvez **également** initialiser le filtre dans le code. Dans une classe d’initialisation appropriée (par exemple, AppStart dans Global.asax.cs), insérez votre processeur dans la chaîne :

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Les clients de télémétrie créés après ce point utiliseront vos processeurs.

### Exemples de filtres
#### Demandes synthétiques
Excluez les robots et les tests web. Bien que Metrics Explorer vous donne la possibilité d’exclure les sources synthétiques, cette option réduit le trafic en les filtrant au niveau du Kit de développement (SDK).

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### Échec d’authentification
Excluez les demandes avec une réponse de type « 401 ».

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### Excluez les appels de dépendance à distance rapides.
Si vous souhaitez uniquement diagnostiquer les appels lents, excluez les appels rapides.

> [!NOTE]
> Cela faussera les statistiques que vous voyez dans le portail. Le graphique de dépendance s’affichera comme si les appels de dépendance avaient tous échoué.
> 
> 

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### Diagnostiquer les problèmes de dépendance
[Ce blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) décrit un projet pour diagnostiquer les problèmes de dépendance en envoyant automatiquement des requêtes ping régulières aux dépendances.

<a name="add-properties"></a>

## Ajouter des propriétés : ITelemetryInitializer
Utilisez des initialiseurs de télémétrie pour définir des propriétés globales qui sont envoyées avec toute la télémétrie ; et pour substituer le comportement sélectionné des modules standard de télémétrie.

Par exemple, le package Application Insights pour le Web collecte la télémétrie sur les requêtes HTTP. Il indique par défaut l’échec de toute requête à l’aide d’un code de réponse supérieur ou égal à 400. Toutefois, si 400 vous convient, vous pouvez fournir un initialiseur de télémétrie qui définit la propriété Success.

Si vous fournissez un initialiseur de télémétrie, celui-ci est appelé chaque fois qu'une des méthodes Track*() est appelée. Cela inclut les méthodes appelées par les modules de télémétrie standard. Par convention, ces modules ne définissent aucune propriété déjà définie par un initialiseur.

**Définir votre initialiseur**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Charger votre initialiseur**

Dans ApplicationInsights.config :

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

Vous pouvez *également* instancier l'initialiseur dans le code, par exemple dans Global.aspx.cs :

```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Voir l’intégralité de cet exemple.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### Initialiseurs de télémétrie JavaScript
*JavaScript*

Insérer un initialiseur de télémétrie immédiatement après le code d’initialisation obtenu à partir du portail :

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Pour obtenir un résumé des propriétés non personnalisées disponibles dans le telemetryItem, consultez le [modèle de données](app-insights-export-data-model.md#lttelemetrytypegt).

Vous pouvez ajouter autant d'initialiseurs que vous le souhaitez.

## ITelemetryProcessor et ITelemetryInitializer
Quelle est la différence entre les processeurs de télémétrie et les initialiseurs de télémétrie ?

* Leurs utilisations se recoupent partiellement : les deux peuvent être utilisés pour ajouter des propriétés à la télémétrie.
* Les TelemetryInitializers sont toujours exécutés avant les TelemetryProcessors.
* Les TelemetryProcessors permettent de remplacer ou supprimer complètement un élément de télémétrie.
* Les TelemetryProcessors ne traitent pas la télémétrie du compteur de performances.

## Canal de persistance
Si votre application s’exécute à l’endroit où une connexion n’est pas toujours disponible ou est très lente, prévoyez d’utiliser la couche de persistance et le canal mémoire interne par défaut.

Le canal de mémoire par défaut perd toute télémétrie qui n’a pas été envoyée au moment où l’application se ferme. Bien que vous puissiez utiliser `Flush()` pour tenter d’envoyer les données restantes dans la mémoire tampon, il perd tout de même les données si aucune connexion Internet n’est disponible ou si l’application s’arrête avant la fin de la transmission.

En revanche, le canal de persistance met en mémoire tampon la télémétrie dans un fichier, avant de l’envoyer au portail. `Flush()` garantit que les données sont stockées dans le fichier. Si aucune donnée n’est envoyée au moment où l’application se ferme, elle reste dans le fichier. Lorsque l’application redémarre, les données sont envoyées ensuite s’il existe une connexion internet. Les données s’accumulent dans le fichier aussi longtemps que nécessaire, jusqu’à ce qu’une connexion soit disponible.

### Pour utiliser le canal de persistance
1. Importez le package NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Incluez ce code dans votre application, dans un emplacement d’initialisation approprié :
   
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

## Documents de référence
* [Présentation de l’API](app-insights-api-custom-events-metrics.md)
* [Référence ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## Code du Kit de développement logiciel (SDK)
* [Kit de développement logiciel (SDK) principal ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Kit de développement logiciel (SDK) JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Étapes suivantes
* [Recherche d'événements et de journaux][diagnostic]
* [Échantillonnage](app-insights-sampling.md)
* [Résolution des problèmes][qna]

<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md



<!---HONumber=AcomDC_0907_2016-->