<properties 
	pageTitle="Configuration du kit de développement logiciel (SDK) Application Insights à l’aide du fichier ApplicationInsights.config ou .xml" 
	description="Activer ou désactiver les modules de collecte de données et ajouter des compteurs de performances et d’autres paramètres" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/05/2015" 
	ms.author="awills"/>

# Configuration du kit de développement logiciel (SDK) Application Insights à l’aide du fichier ApplicationInsights.config ou .xml

Le kit de développement logiciel (SDK) .NET Application Insights se compose d’un certain nombre de packages NuGet. Le [package principal](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fournit l'API pour l'envoi des données télémétriques à Application Insights. Des [packages supplémentaires](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fournissent les _modules_ et les _initialiseurs_ de télémétrie pour le suivi télémétrique automatique de votre application et de son contexte. La modification du fichier de configuration permet d’activer ou de désactiver les modules et initialiseurs de télémétrie, et de définir les paramètres pour certains d’entre eux.

Le fichier de configuration est nommé `ApplicationInsights.config` ou `ApplicationInsights.xml`, selon le type de votre application. Il est automatiquement ajouté à votre projet lorsque vous [installez la plupart des versions du Kit de développement logiciel (SDK)][start]. Il est également ajouté à une application web par [Status Monitor sur un serveur IIS][redfield], ou lorsque vous [sélectionnez l’extension Appplication Insights pour un site web ou une machine virtuelle Azure][azure].

Il n’existe pas de fichier équivalent permettant le contrôle du [kit de développement logiciel (SDK) dans une page web][client].

Ce document décrit les sections du fichier de configuration, la façon dont ils contrôlent les composants du Kit de développement logiciel (SDK) et les packages NuGet qui chargent ces composants.

## Modules de télémétrie (ASP.NET)

Chaque module de télémétrie collecte un type de données précis et utilise l'API de base pour envoyer les données. Les modules sont installés par différents packages NuGet, qui ajoutent également les lignes requises dans le fichier .config.

Il existe un nœud dans le fichier de configuration pour chaque module. Pour désactiver un module, supprimez le nœud ou commentez-le.



### Suivi des dépendances

Le [Suivi des dépendances](app-insights-dependencies.md) collecte la télémétrie des appels de votre application aux bases de données et aux services et bases de données externes. Pour permettre à ce module travailler dans un serveur IIS, vous devez [installer Status Monitor][redfield]. Pour l’utiliser dans des applications web ou des machines virtuelles Azure, [sélectionnez l’extension Application Insights][azure].

Vous pouvez également écrire votre propre code de suivi des dépendances à l'aide de l’[API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Package NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector).

### Collecteur de performances

[Collecte les compteurs de performances système](app-insights-web-monitor-performance.md#system-performance-counters), notamment l'UC, la mémoire et la charge réseau, à partir des installations IIS. Vous pouvez spécifier les compteurs à collecter, y compris les compteurs de performances que vous avez configurés vous-même.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Package NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector).


### Télémétrie des diagnostics Application Insights

Le `DiagnosticsTelemetryModule` répertorie les erreurs dans le code d'instrumentation Application Insights lui-même. Par exemple, si le code ne peut pas accéder aux compteurs de performances ou si un `ITelemetryInitializer` renvoie une exception. La télémétrie de trace suivie par ce module s'affiche dans la [Recherche de diagnostic][diagnostic].
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* Package NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights). Si vous installez simplement ce package, le fichier ApplicationInsights.config n'est pas créé automatiquement. 

### Mode développeur

`DeveloperModeWithDebuggerAttachedTelemetryModule` force Application Insights`TelemetryChannel` à envoyer des données immédiatement, un élément de télémétrie à la fois, lorsqu’un débogueur est associé au processus d'application. Cela réduit le délai entre le moment où votre application effectue le suivi de télémétrie et celui où les données apparaissent sur le portail Application Insights. Cela cause une surcharge importante au niveau de l'UC et de la bande passante réseau.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* Package NuGet [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)

### Suivi des requêtes web

Indique le [temps de réponse et le code résultant](app-insights-start-monitoring-app-health-usage.md) des requêtes HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Package NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### Suivi des exceptions

`ExceptionTrackingTelemetryModule` comptabilise le nombre d’exceptions non traitées dans votre application web. Voir [Échecs et exceptions][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Package NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` - suit [les exceptions de tâche non prises en charge](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - suit les exceptions non gérées pour les rôles de travail, les services Windows et les applications de console.
* Package NuGet [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

### API de base

Le package de base fournit l'[API de base](https://msdn.microsoft.com/library/mt420197.aspx) du Kit de développement logiciel (SDK). Les autres modules de télémétrie l'utilisent. Vous pouvez également [l'utiliser pour définir votre propre télémétrie](app-insights-api-custom-events-metrics.md).

* Aucune entrée dans ApplicationInsights.config.
* Package NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights). Si vous installez simplement ce package NuGet, aucun fichier .config n'est créé.

## Canal de télémétrie

Le canal de télémétrie gère la mise en mémoire tampon et la transmission de la télémétrie au service Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` est le canal par défaut des services. Il met les données en mémoire.
* `Microsoft.ApplicationInsights.PersistenceChannel` est une alternative pour les applications de console. Il peut enregistrer les données non vidées dans un stockage permanent à la fermeture de votre application et les envoyer au redémarrage de l'application.


## Initialiseurs de télémétrie (ASP.NET)

Les initialiseurs de télémétrie définissent les propriétés de contexte envoyées avec chaque élément de télémétrie.

Vous pouvez [écrire vos propres initialiseurs](app-insights-api-filtering-sampling.md#add-properties) pour définir les propriétés de contexte.

Les initialiseurs standard sont tous définis par les packages NuGet web ou WindowsServer :


* `AccountIdTelemetryInitializer` définit la propriété AccountId.
* `OperationNameTelemetryInitializer` met à jour la propriété de contexte `Operation.Id` de tous les éléments de télémétrie suivis pendant le traitement d'une demande avec l'élément `RequestTelemetry.Id` généré automatiquement.
* `AzureRoleEnvironmentTelemetryInitializer` met à jour les propriétés `RoleName` et `RoleInstance` du contexte `Device` pour tous les éléments de télémétrie avec des informations extraites de l'environnement d’exécution Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` met à jour la propriété `Version` du contexte `Component` pour tous les éléments de télémétrie avec la valeur extraite du fichier `BuildInfo.config` produit par MSBuild.
* `ClientIpHeaderTelemetryInitializer` met à jour la propriété `Ip` du contexte `Location` de tous les éléments de télémétrie à partir de l’en-tête HTTP `X-Forwarded-For` de la demande.
* `DeviceTelemetryInitializer` met à jour les propriétés suivantes du contexte `Device` pour tous les éléments de télémétrie.
 - `Type` est défini sur « PC »
 - `Id` est défini sur le nom de domaine de l'ordinateur sur lequel l'application web est exécutée.
 - `OemName` est défini sur la valeur extraite du champ `Win32_ComputerSystem.Manufacturer` à l'aide de WMI.
 - `Model` est défini sur la valeur extraite du champ `Win32_ComputerSystem.Model` à l'aide de WMI.
 - `NetworkType` est défini sur la valeur extraite de l’`NetworkInterface`.
 - `Language` est défini sur le nom de la `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` met à jour la propriété `RoleInstance` du contexte `Device` pour tous les éléments de télémétrie avec le nom de domaine de l'ordinateur sur lequel l'application web est exécutée.
* `OperationNameTelemetryInitializer` met à jour la propriété `Name` de `RequestTelemetry` et la propriété `Name` du contexte `Operation` de tous les éléments de télémétrie à partir de la méthode HTTP, ainsi que les noms du contrôleur ASP.NET MVC et de l’action appelée pour traiter la demande.
* `OperationIdTelemetryInitializer` met à jour la propriété de contexte `Operation.Id` de tous les éléments de télémétrie suivis lors du traitement d'une demande avec le `RequestTelemetry.Id` généré automatiquement.
* `SessionTelemetryInitializer` met à jour la propriété `Id` du contexte `Session` pour tous les éléments de télémétrie avec la valeur extraite du cookie `ai_session` généré par le code d’instrumentation JavaScript Application Insights en cours d'exécution dans le navigateur de l'utilisateur. 
* `SyntheticTelemetryInitializer` met à jour les propriétés de contexte `User`, `Session` et `Operation` de tous les éléments de télémétrie suivis lors du traitement d'une requête émanant d'une source synthétique, comme un test de disponibilité ou un robot de moteur de recherche. Par défaut, [Metrics Explorer](app-insights-metrics-explorer.md) n'affiche pas la télémétrie synthétique.
* `UserAgentTelemetryInitializer` met à jour la propriété `UserAgent` du contexte `User` de tous les éléments de télémétrie à partir de l’en-tête HTTP `User-Agent` de la demande.
* `UserTelemetryInitializer` met à jour les propriétés `Id` et `AcquisitionDate` du contexte `User` pour tous les éléments de télémétrie avec les valeurs extraites du cookie `ai_user` généré par le code d’instrumentation JavaScript Application Insights en cours d'exécution dans le navigateur de l'utilisateur.


## Processeurs de télémétrie (ASP.NET)

Les processeurs de télémétrie peuvent filtrer et modifier chaque élément de télémétrie avant son envoi au portail à partir du Kit de développement logiciel (SDK).

Vous pouvez [écrire vos propres processeurs de télémétrie](app-insights-api-filtering-sampling.md#filtering).

Il y a un seul processeur standard (de 2.0.1) :

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor` - L'[échantillonnage](app-insights-api-filtering-sampling.md#sampling) réduit le volume de télémétrie tout en vous permettant de naviguer entre les éléments de télémétrie connexes à des fins de diagnostic.



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
[start]: app-insights-overview.md

<!---HONumber=Nov15_HO3-->