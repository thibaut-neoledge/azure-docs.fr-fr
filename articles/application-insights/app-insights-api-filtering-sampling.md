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
	ms.date="11/04/2015" 
	ms.author="awills"/>

# Échantillonnage, filtrage et pré-traitement de la télémétrie dans le Kit de développement logiciel (SDK) Application Insights

*Application Insights est à l'état de version préliminaire.*

Vous pouvez écrire et configurer des plug-ins pour le Kit de développement logiciel (SDK) Application Insights afin de personnaliser la capture et le traitement des données de télémétrie avant leur envoi au service Application Insights.

Actuellement, ces fonctionnalités sont disponibles pour le Kit de développement logiciel (SDK) ASP.NET.

* L'[échantillonnage](#sampling) réduit le volume des données de télémétrie sans affecter les statistiques. Il maintient ensemble les points de données liés de sorte que vous pouvez naviguer entre eux pour diagnostiquer un problème. Dans le portail, les nombres totaux sont multipliés pour compenser l'échantillonnage.
 * L'échantillonnage à débit fixe vous permet de déterminer le pourcentage d'événements qui sont transmis.
 * L'échantillonnage adaptatif (la valeur par défaut pour ASP.NET SDK de 2.0.0-beta3) ajuste automatiquement le débit d'échantillonnage en fonction du volume de vos données de télémétrie. Vous pouvez définir un volume cible.
* Le [filtrage](#filtering) vous permet de sélectionner ou de modifier la télémétrie dans le Kit de développement logiciel (SDK) avant son envoi au serveur. Par exemple, vous pouvez réduire le volume de la télémétrie en excluant les demandes émanant de robots. Il s'agit d'une approche plus simple que l'échantillonnage pour réduire le trafic. Cela vous permet de mieux contrôler ce qui est transmis, mais vous devez être conscient que cela affectera vos statistiques ; par exemple, si vous filtrez toutes les demandes réussies.
* [Ajoutez des propriétés](#add-properties) à n'importe quelle télémétrie envoyée à partir de votre application, notamment les données de télémétrie fournies par les modules standard. Par exemple, vous pouvez ajouter des valeurs calculées ou des numéros de version permettant de filtrer les données dans le portail.
* [L'API SDK](app-insights-api-custom-events-metrics.md) est utilisée pour envoyer des événements et des mesures personnalisés.

Avant de commencer :

* Installez le [Kit de développement logiciel (SDK) Application Insights](app-insights-start-monitoring-app-health-usage.md) dans votre application. Installez manuellement les packages NuGet et sélectionnez la dernière version *préliminaire*.
* Essayez l'[API Application Insights](app-insights-api-custom-events-metrics.md) 


## Échantillonnage

*Cette fonctionnalité est en version bêta.*

L'[échantillonnage](app-insights-sampling.md) est la méthode recommandée pour réduire le trafic tout en conservant la précision des statistiques. Le filtre sélectionne les éléments associés afin que vous puissiez naviguer entre les éléments en cours de diagnostic. Le nombre d’événements est ajusté dans Metrics Explorer pour compenser les éléments filtrés.

* L'échantillonnage adaptatif est recommandé. Il ajuste automatiquement le pourcentage d'échantillonnage de façon à obtenir un volume spécifique de demandes. Actuellement uniquement disponible pour la télémétrie ASP.NET côté serveur.  
* Une option d'échantillonnage à débit fixe est également disponible. Vous spécifiez le pourcentage d'échantillonnage. Disponible pour le code d'application web ASP.NET et les pages Web JavaScript. Le client et le serveur synchronisent leur échantillonnage de façon à ce que vous puissiez naviguer entre les demandes et les affichages de pages associés dans Search.

### Pour activer l'échantillonnage

**Mettez à jour les packages NuGet** de votre projet vers la version *préliminaire* d'Application Insights la plus récente : cliquez avec le bouton droit sur le projet dans l'Explorateur de solutions, sélectionnez Gérer les packages NuGet, cochez **Inclure la version préliminaire** et recherchez Microsoft.ApplicationInsights.Web.

Dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), vous pouvez ajuster le taux maximal de télémétrie défini comme objectif de l'algorithme adaptatif :

    <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>

### Échantillonnage côté client

Pour obtenir un échantillonnage à débit fixe de données de pages Web, ajoutez une ligne supplémentaire dans l'[extrait de code Application Insights](app-insights-javascript.md) que vous avez inséré (généralement dans une page maître telle que \_Layout.cshtml) :

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Définissez un pourcentage (10 dans cet exemple) qui est égal à 100/N, où N est un entier. Par exemple 50 (= 100/2), 33,33 (= 100/3), 25 (= 100/4) ou 10 (= 100/10). 
* Si vous avez activé l'[échantillonnage à débit fixe](app-insights-sampling.md) côté serveur, les clients et le serveur synchronisent leur échantillonnage de façon à ce que vous puissiez naviguer entre les demandes et les affichages de pages associés dans Search.

[En savoir plus sur l'échantillonnage](app-insights-sampling.md).

## Filtrage

Cette technique vous offre un contrôle plus direct sur ce qui est inclus ou exclu du flux de télémétrie. Vous pouvez l'utiliser conjointement avec l'échantillonnage, ou séparément.

Pour filtrer la télémétrie, vous écrivez un processeur de télémétrie et l'enregistrez avec le Kit de développement logiciel (SDK). Toute la télémétrie passe par votre processeur et vous pouvez choisir de la supprimer du flux ou d'ajouter des propriétés. Cela inclut les données de télémétrie fournies par les modules standard tels que le collecteur de demandes HTTP et le collecteur de dépendances, ainsi que les données de télémétrie que vous avez écrites vous-même. Vous pouvez, par exemple, exclure la télémétrie concernant les demandes émanant de robots ou les appels de dépendance réussis.

> [AZURE.WARNING]Filtrer la télémétrie envoyée depuis le Kit de développement logiciel (SDK) à l’aide de processeurs peut fausser les statistiques que vous voyez dans le portail et rendre difficile le suivi des éléments associés.
> 
> Envisagez plutôt d'utiliser l'[échantillonnage](#sampling).

### Créer un processeur de télémétrie

1. Mettez à jour le kit de développement logiciel Application Insights à la dernière version (2.0.0-beta2 ou version ultérieure). Cliquez avec le bouton droit sur votre projet dans l'explorateur de solutions Visual Studio, puis sélectionnez Gérer les packages NuGet. Dans le gestionnaire de packages NuGet, activez **Inclure la version préliminaire** et recherchez Microsoft.ApplicationInsights.Web.

1. Pour créer un filtre, déployez ITelemetryProcessor. Il s’agit d’un autre point d’extensibilité, à l’instar d’un module de télémétrie, d’un initialiseur de télémétrie et d’un canal de télémétrie.

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
2. Insérez-la dans ApplicationInsights.config : 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Notez qu'il s'agit de la section dans laquelle vous initialisez un filtre d'échantillonnage).

Vous pouvez transférer des valeurs de chaîne depuis le fichier .config en fournissant des propriétés publiques nommées dans votre classe.

> [AZURE.WARNING]Veillez à faire correspondre le nom de type et les noms de propriété dans le fichier .config aux noms de classe et de propriété dans le code. Si le fichier .config fait référence à un type ou à une propriété qui n'existe pas, le kit de développement peut échouer lors de l'envoi d'une télémétrie quelconque.

 
Vous pouvez **également** initialiser le filtre dans le code. Dans une classe d’initialisation appropriée (par exemple, AppStart dans Global.asax.cs), insérez votre processeur dans la chaîne :

```C#

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
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

Excluez les demandes avec une réponse de type « 401 ».

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

Pour obtenir un résumé des propriétés non personnalisées disponibles dans le telemetryItem, consultez le [modèle de données](app-insights-export-data-model.md/#lttelemetrytypegt).

Vous pouvez ajouter autant d'initialiseurs que vous le souhaitez.


## Documents de référence

* [Présentation de l’API](app-insights-api-custom-events-metrics.md)

* [Référence ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## Code du Kit de développement logiciel (SDK)

* [Kit de développement logiciel (SDK) principal ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Kit de développement logiciel (SDK) JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


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

 

<!---HONumber=AcomDC_1125_2015-->