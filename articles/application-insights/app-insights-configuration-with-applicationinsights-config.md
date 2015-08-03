<properties 
	pageTitle="Configuration du kit de développement logiciel (SDK) Application Insights à l’aide du fichier ApplicationInsights.config ou .xml" 
	description="Activer ou désactiver les modules de collecte de données et ajouter des compteurs de performances et d’autres paramètres" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="meravd"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="awills"/>

# Configuration du kit de développement logiciel (SDK) Application Insights à l’aide du fichier ApplicationInsights.config ou .xml

Le kit de développement logiciel (SDK) .NET Application Insights se compose d’un certain nombre de packages NuGet. Le [package principal](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fournit l'API pour l'envoi des données télémétriques à Application Insights. Des [packages supplémentaires](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fournissent les _modules_ et les _initialiseurs_ de télémétrie pour le suivi télémétrique automatique de votre application et de son contexte. La modification du fichier de configuration permet d’activer ou de désactiver les modules et initialiseurs de télémétrie, et de définir les paramètres pour certains d’entre eux.

Le fichier de configuration est nommé `ApplicationInsights.config` ou `ApplicationInsights.xml`, selon le type de votre application. Il est automatiquement ajouté à votre projet lorsque vous [installez certaines versions du Kit de développement logiciel (SDK)][start]. Il est également ajouté à une application web par [Status Monitor sur un serveur IIS][redfield], ou lorsque vous [sélectionnez l’extension Appplication Insights pour un site web ou une machine virtuelle Azure][azure].

Il n’existe pas de fichier équivalent permettant le contrôle du [kit de développement logiciel (SDK) dans une page web][client].

Il existe un nœud dans le fichier de configuration pour chaque module. Pour désactiver un module, supprimez le nœud ou commentez-le.

## Package NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights)

Le package NuGet `Microsoft.ApplicationInsights` fournit les modules de télémétrie suivants dans `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights" />
  </TelemetryModules>
</ApplicationInsights>
```
Le `DiagnosticsTelemetryModule` répertorie les erreurs dans le code d'instrumentation Application Insights lui-même. Par exemple, si le code ne peut pas accéder aux compteurs de performances ou si un `ITelemetryInitializer` renvoie une exception. La télémétrie de trace suivie par ce module s'affiche dans la [Recherche de diagnostic][diagnostic].

## Package NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector)

Le package NuGet `Microsoft.ApplicationInsights.DependencyCollector` fournit les modules de télémétrie suivants dans `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.DependencyCollector" />
  </TelemetryModules>
</ApplicationInsights>
```
Le `DependencyTrackingTelemetryModule` suit la télémétrie concernant les appels vers des dépendances externes, effectués par votre application, tels que les demandes HTTP et les requêtes SQL. Pour permettre à ce module travailler dans un serveur IIS, vous devez [installer Status Monitor][redfield]. Pour l’utiliser dans des applications web ou des machines virtuelles Azure, [sélectionnez l’extension Application Insights][azure].

Vous pouvez également écrire votre propre code de suivi des dépendances à l'aide de l’[API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).

## Package NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

Le package NuGet `Microsoft.ApplicationInsights.Web` fournit les modules et initialiseurs de télémétrie suivants dans `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SyntheticTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ClientIpHeaderTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserAgentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationNameTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationIdTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SessionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.AzureRoleEnvironmentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DomainNameRoleInstanceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeviceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.RequestTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ExceptionTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
  </TelemetryModules>
</ApplicationInsights>
```

**Initialiseurs**

* `SyntheticTelemetryInitializer` met à jour les propriétés de contexte pour `User`, `Session` et `Operation` de tous les éléments de télémétrie suivis lors du traitement d'une demande émanant d'une source synthétique, comme un test de disponibilité. Le portail Application Insights n'affiche aucune télémétrie synthétique par défaut.
* `ClientIpHeaderTelemetryInitializer` met à jour la propriété `Ip` du contexte `Location` de tous les éléments de télémétrie à partir de l’en-tête HTTP `X-Forwarded-For` de la demande.
* `UserAgentTelemetryInitializer` met à jour la propriété `UserAgent` du contexte `User` de tous les éléments de télémétrie à partir de l’en-tête HTTP `User-Agent` de la demande.
* `OperationNameTelemetryInitializer` met à jour la propriété `Name` de `RequestTelemetry` et la propriété `Name` du contexte `Operation` de tous les éléments de télémétrie à partir de la méthode HTTP, ainsi que les noms du contrôleur ASP.NET MVC et de l’action appelée pour traiter la demande.
* `OperationNameTelemetryInitializer` met à jour l’élément 'Operation.Id` context property of all telemetry items tracked while 
handling a request with the automatically generated `RequestTelemetry.Id`.
* `UserTelemetryInitializer` met à jour les propriétés `Id` et `AcquisitionDate` du contexte `User` pour tous les éléments de télémétrie avec les valeurs extraites du cookie `ai_user` généré par le code d’instrumentation JavaScript Application Insights en cours d'exécution dans le navigateur de l'utilisateur.
* `SessionTelemetryInitializer` met à jour la propriété `Id` du contexte `Session` pour tous les éléments de télémétrie avec la valeur extraite du cookie `ai_session` généré par le code d’instrumentation JavaScript Application Insights en cours d'exécution dans le navigateur de l'utilisateur. 
* `AzureRoleEnvironmentTelemetryInitializer` met à jour les propriétés `RoleName` et `RoleInstance` du contexte `Device` pour tous les éléments de télémétrie avec des informations extraites de l'environnement d’exécution Azure.
* `DomainNameRoleInstanceTelemetryInitializer` met à jour la propriété `RoleInstance` du contexte `Device` pour tous les éléments de télémétrie avec le nom de domaine de l'ordinateur sur lequel l'application web est exécutée.
* `BuildInfoConfigComponentVersionTelemetryInitializer` met à jour la propriété `Version` du contexte `Component` pour tous les éléments de télémétrie avec la valeur extraite du fichier `BuildInfo.config` produit par la build TFS.
* `DeviceTelemetryInitializer` met à jour les propriétés suivantes du contexte `Device` pour tous les éléments de télémétrie.
 - `Type` est défini sur « PC »
 - `Id` est défini sur le nom de domaine de l'ordinateur sur lequel l'application web est exécutée.
 - `OemName` est défini sur la valeur extraite du champ `Win32_ComputerSystem.Manufacturer` à l'aide de WMI.
 - `Model` est défini sur la valeur extraite du champ `Win32_ComputerSystem.Model` à l'aide de WMI.
 - `NetworkType` est défini sur la valeur extraite de l’`NetworkInterface`.
 - `Language` est défini sur le nom de la `CurrentCulture`.

**Modules**

* `RequestTrackingTelemetryModule` comptabilise le nombre de demandes reçues par votre application web et mesure les temps de réponse.
* `ExceptionTrackingTelemetryModule` comptabilise le nombre d’exceptions non traitées dans votre application web. Voir [Échecs et exceptions][exceptions].
* `DeveloperModeWithDebuggerAttachedTelemetryModule` force Application Insights`TelemetryChannel` à envoyer des données immédiatement, un élément de télémétrie à la fois, lorsqu’un débogueur est associé au processus d'application. Cela réduit le délai entre le moment où votre application effectue le suivi de télémétrie et celui où les données apparaissent sur le portail Application Insights, au prix d’une surcharge importante au niveau du processeur et de la bande passante réseau.

## Package NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)

Le package NuGet `Microsoft.ApplicationInsights.PerfCounterCollector` ajoute les modules de télémétrie suivants à l’élément `ApplicationInsights.config` par défaut.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector" />
  </TelemetryModules>
</ApplicationInsights>
```

### PerformanceCollectorModule

`PerformanceCollectorModule` comptabilise le nombre de compteurs de performances Windows. Vous pouvez afficher ces compteurs en cliquant sur un graphique dans Metric Explorer pour ouvrir son panneau de détails.

Vous pouvez surveiller des compteurs de performances supplémentaires : les compteurs Windows standard et tout autre compteur ajouté.
      
Pour collecter des compteurs de performances supplémentaires, utilisez la syntaxe suivante :

```
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector">
  <Counters>
    <Add PerformanceCounter="\MyCategory\MyCounter" />
    <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
    <!-- ... -->
  </Counters>
</Add>
```      
      
`PerformanceCounter` doit être `\CategoryName(InstanceName)\CounterName` ou `\CategoryName\CounterName`
      
Si vous fournissez un attribut `ReportAs`, il sera utilisé comme nom affiché dans Application Insights.

À des fins de reporting dans Application Insights, les noms de compteurs doivent inclure uniquement : des lettres, des parenthèses, des barres obliques, des tirets, des traits de soulignement, des espaces et des points.

Vous devez utiliser ReportAs si le compteur que vous souhaitez surveiller contient des caractères non valides tels que « # » ou des chiffres.
      
Les espaces réservés suivants sont pris en charge en tant que `InstanceName` :

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## Paramètres de canal (Java)

Ces paramètres ont un effet sur la manière dont le kit de développement logiciel (SDK) doit stocker et vider les données de télémétrie qu’il collecte.

#### MaxTelemetryBufferCapacity

Nombre d’éléments de télémétrie qui peuvent être stockés dans la mémoire du kit de développement logiciel (SDK). Une fois ce nombre atteint, la mémoire tampon de télémétrie est vidée : les éléments de télémétrie sont envoyés au serveur Application Insights.

-	Min : 1
-	Max : 1000
-	Valeur par défaut : 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

Détermine la fréquence à laquelle les données stockées en mémoire doivent être vidées (envoyées à Application Insights).

-	Min : 1
-	Max : 300
-	Valeur par défaut : 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

Détermine la taille maximale en Mo allouée au stockage persistant sur le disque local. Ce stockage est utilisé pour les éléments de télémétrie persistants qui n’ont pas pu être transmis au point de terminaison Application Insights. Une fois la capacité de stockage atteinte, les nouveaux éléments de télémétrie sont ignorés.

-	Min : 1
-	Max : 100
-	Valeur par défaut : 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

## Initialiseurs personnalisés


Si les initialiseurs standard ne conviennent pas à votre application, vous pouvez créer les vôtres.

Les initialiseurs de contexte permettent de définir les valeurs qui seront utilisées pour initialiser chaque client de télémétrie. Par exemple, si vous avez publié plusieurs versions de votre application, vous pouvez vous assurer de la séparation des données en filtrant sur une propriété personnalisée :

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

Utilisez les initialiseurs de télémétrie pour ajouter le traitement à chaque événement. Par exemple, le kit de développement logiciel (SDK) web indique l’échec de toute demande dont le code de réponse est supérieur ou égal à 400. Vous pourriez ignorer ce comportement :

    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                requestTelemetry.Success = true;
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }            
        }
    }
 
Pour installer vos initialiseurs, ajoutez des lignes dans ApplicationInsights.config :

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


Vous pouvez également écrire du code pour installer l’initialiseur au tout début de l’exécution de votre application. Par exemple :


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




## InstrumentationKey

Ce paramètre détermine la ressource Application Insights dans laquelle vos données s’affichent. En général, vous créez une ressource séparée, avec une clé distincte, pour chacune de vos applications.

Si vous souhaitez définir la clé de manière dynamique, par exemple si vous souhaitez transmettre des résultats de votre application vers différentes ressources, vous pouvez omettre la clé du fichier de configuration, et la définir plutôt dans le code.

Définissez la clé dans TelemetryConfiguration.Active pour toutes les instances de TelemetryClient, y compris les modules standard de télémétrie. Définissez-la dans une méthode d’initialisation, telle que global.aspx.cs dans un service ASP.NET :

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Si vous souhaitez simplement envoyer un ensemble spécifique d’événements à une autre ressource, vous pouvez définir la clé pour un TelemetryClient spécifique :

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[En savoir plus sur l’API][api]

Pour obtenir une nouvelle clé, [créez une nouvelle ressource dans le portail Application Insights][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=July15_HO4-->