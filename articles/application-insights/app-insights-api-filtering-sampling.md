<properties 
	pageTitle="Échantillonnage, filtrage et pré-traitement dans le Kit de développement logiciel (SDK) Application Insights" 
	description="Écrire des plug-ins que le Kit de développement logiciel (SDK) doit filtrer ou ajouter des propriétés aux données avant que la télémétrie ne soit envoyée au portail Application Insights." 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="10/22/2015" 
	ms.author="awills"/>

# Échantillonnage, filtrage et pré-traitement de la télémétrie dans le Kit de développement logiciel (SDK) Application Insights

*Application Insights est à l'état de version préliminaire.*

Vous pouvez écrire et configurer des plug-ins pour le Kit de développement logiciel (SDK) Application Insights afin de personnaliser la capture et le traitement des données de télémétrie avant leur envoi au service Application Insights.

Actuellement, ces fonctionnalités sont disponibles pour le Kit de développement logiciel (SDK) ASP.NET.

* L'[échantillonnage](#sampling) réduit le volume des données de télémétrie sans affecter les statistiques. Il maintient ensemble les points de données liés de sorte que vous pouvez naviguer entre eux pour diagnostiquer un problème. Dans le portail, les nombres totaux sont multipliés pour compenser l'échantillonnage.
* Le [filtrage](#filtering) vous permet de sélectionner ou de modifier la télémétrie dans le Kit de développement logiciel (SDK) avant l'envoi au serveur. Par exemple, vous pouvez réduire le volume de la télémétrie en excluant les demandes émanant de robots. Il s'agit d'une approche plus simple que l'échantillonnage pour réduire le trafic. Cela vous permet de mieux contrôler ce qui est transmis, mais vous devez être conscient que cela affectera vos statistiques ; par exemple, si vous filtrez toutes les demandes réussies.
* [Ajoutez des propriétés](#add-properties) à n'importe quelle télémétrie envoyée à partir de votre application, notamment les données de télémétrie fournies par les modules standard. Par exemple, vous pouvez ajouter des valeurs calculées ou des numéros de version permettant de filtrer les données dans le portail.
* [L'API SDK](app-insights-api-custom-events-metrics.md) est utilisée pour envoyer des événements et des mesures personnalisés.

Avant de commencer :

* Installez le [Kit de développement logiciel (SDK) Application Insights](app-insights-start-monitoring-app-health-usage.md) dans votre application. Installez manuellement les packages NuGet et sélectionnez la dernière version *préliminaire*.
* Essayez l'[API Application Insights](app-insights-api-custom-events-metrics.md) 


## Échantillonnage

*Cette fonctionnalité est en version bêta.*

La méthode recommandée pour réduire le trafic tout en conservant des statistiques précises. Le filtre sélectionne les éléments associés afin que vous puissiez naviguer entre les éléments en cours de diagnostic. Le nombre d’événements est ajusté dans Metrics Explorer pour compenser les éléments filtrés.

1. Mettez à jour les packages NuGet de votre projet vers la dernière version *préliminaire* d'Application Insights. Cliquez avec le bouton droit sur le projet dans l'Explorateur de solutions, sélectionnez Gérer les packages NuGet, cochez **Inclure la version préliminaire** et recherchez Microsoft.ApplicationInsights.Web. 

2. Ajoutez cet extrait de code à ApplicationInsights.config :

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 10, 1 (=100/100), 0.1 (=100/1000) ... -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```


Pour obtenir un échantillonnage sur les données à partir des pages web, ajoutez une ligne supplémentaire dans l'[extrait de code Application Insights](app-insights-javascript.md) que vous avez inséré (généralement dans une page maître comme \_Layout.cshtml) :

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Définissez un pourcentage (10 dans ces exemples) qui est égal à 100/N, où N est un entier ; par exemple 50 (= 100/2), 33,33 (= 100/3), 25 (= 100/4), 10 (= 100/5). 
* Si vous avez beaucoup de données, vous pouvez utiliser des taux d'échantillonnage très faibles, comme 0,1.
* Si vous définissez l’échantillonnage sur la page web et sur le serveur, veillez à définir le même pourcentage d’échantillonnage des deux côtés.
* Les côtés client et serveur s’accorderont pour sélectionner des éléments associés.

[En savoir plus sur l'échantillonnage](app-insights-sampling.md).

## Filtrage

Cette technique vous offre un contrôle plus direct sur ce qui est inclus ou exclu du flux de télémétrie. Vous pouvez l'utiliser conjointement avec l'échantillonnage, ou séparément.

Pour filtrer la télémétrie, vous écrivez un processeur de télémétrie et l'enregistrez avec le Kit de développement logiciel (SDK). Toute la télémétrie passe par votre processeur et vous pouvez choisir de la supprimer du flux ou d'ajouter des propriétés. Cela inclut les données de télémétrie fournies par les modules standard tels que le collecteur de demandes HTTP et le collecteur de dépendances, ainsi que les données de télémétrie que vous avez écrites vous-même. Vous pouvez, par exemple, exclure la télémétrie concernant les demandes émanant de robots ou les appels de dépendance réussis.

> [AZURE.WARNING]Filtrer la télémétrie envoyée depuis le Kit de développement logiciel (SDK) à l’aide de processeurs peut fausser les statistiques que vous voyez dans le portail et rendre difficile le suivi des éléments associés.
> 
> Envisagez plutôt d'utiliser l'[échantillonnage](#sampling).

### Créer un processeur de télémétrie

1. Pour créer un filtre, déployez ITelemetryProcessor. Il s’agit d’un autre point d’extensibilité, à l’instar d’un module de télémétrie, d’un initialiseur de télémétrie et d’un canal de télémétrie. 

    Notez que les processeurs de télémétrie construisent une chaîne de traitement. Lorsque vous instanciez un processeur de télémétrie, vous transmettez un lien au processeur suivant dans la chaîne. Lorsqu’un point de données de télémétrie est transmis à la méthode de traitement, il effectue son travail, puis appelle le processeur de télémétrie suivant dans la chaîne.

    ``` C#

    namespace FilteringTelemetryProcessor
    {
      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.DataContracts;

      class UnauthorizedRequestFilteringProcessor : ITelemetryProcessor
      {
        public UnauthorizedRequestFilteringProcessor(ITelemetryProcessor next)
		//Initialization will fail without this constructor. Link processors to each other
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
        }      private ITelemetryProcessor Next { get; set; }
      }
    }

    ```
2. Dans une classe d’initialisation appropriée (par exemple, AppStart dans Global.asax.cs), insérez votre processeur dans la chaîne :

    ```C#

    var builder = new TelemetryChannelBuilder();
    builder.Use((next) => new UnauthorizedRequestFilteringProcessor(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    TelemetryConfiguration.Active.TelemetryChannel = builder.Build();

    ```

    Les clients de télémétrie créés après ce point utiliseront vos processeurs.

### Exemples de filtres

#### Demandes synthétiques

Excluez les robots et les tests web. Bien que Metrics Explorer vous donne la possibilité d’exclure les sources synthétiques, cette option réduit le trafic en les filtrant au niveau du Kit de développement (SDK).

``` C#

public void Process(ITelemetry item)
{
    if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource))
    { return; }

    this.Next.Process(item);
}

```

#### Échec d’authentification

Excluez les demandes avec des réponses de type « 401 ».

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

> [AZURE.NOTE]Cela faussera les statistiques que vous voyez dans le portail. Le graphique de dépendance s’affichera comme si les appels de dépendance avaient tous échoué.

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


## Ajout de propriétés

Utilisez des initialiseurs de télémétrie pour définir des propriétés globales qui sont envoyées avec toute la télémétrie ; et pour substituer le comportement sélectionné des modules standard de télémétrie.

Par exemple, le package Application Insights pour le Web collecte la télémétrie sur les requêtes HTTP. Il indique par défaut l’échec de toute requête à l’aide d’un code de réponse supérieur ou égal à 400. Toutefois, si 400 vous convient, vous pouvez fournir un initialiseur de télémétrie qui définit la propriété Success.

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

Dans ApplicationInsights.config :

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

Vous pouvez *également* instancier l'initialiseur dans le code, par exemple dans Global.aspx.cs :


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

Insérer un initialiseur de télémétrie immédiatement après le code d’initialisation obtenu à partir du portail :

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

Pour obtenir un résumé des propriétés non personnalisées disponibles sur le telemetryItem, consultez le [modèle de données](app-insights-export-data-model.md/#lttelemetrytypegt).

Vous pouvez ajouter autant d'initialiseurs que vous le souhaitez.


## Documents de référence

* [Présentation de l’API](app-insights-api-custom-events-metrics.md)

* [Référence ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Référence Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Référence JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Kit de développement logiciel Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [Kit de développement logiciel (SDK) iOS](https://github.com/Microsoft/ApplicationInsights-iOS)


## Code du Kit de développement logiciel (SDK)

* [Kit de développement logiciel (SDK) principal ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Kit de développement logiciel Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [Kit de développement logiciel (SDK) Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Kit de développement logiciel (SDK) JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Kit de développement logiciel (SDK) iOS](https://github.com/Microsoft/ApplicationInsights-iOS)
* [Toutes les plateformes](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## Questions

* *Quelles exceptions peuvent être lancées par les appels Track\_() ?*
    
    Aucun. Vous n’avez pas besoin de les inclure dans des clauses try-catch. Si le Kit de développement logiciel (SDK) rencontre des problèmes, il enregistrera des messages que vous pourrez consulter dans la sortie de la console de débogage et, si les messages aboutissent, dans la recherche de diagnostic.



* *Existe-t-il une API REST ?*

    Oui, mais nous ne l’avons pas encore publiée.

## <a name="next"></a>Étapes suivantes


[Recherche d'événements et de journaux][diagnostic]

[Exemples et procédures pas à pas](app-insights-code-samples.md)

[Résolution des problèmes][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Nov15_HO1-->