---
title: "Surveillance au niveau de l’application Azure Service Fabric | Microsoft Docs"
description: "Découvrez les événements et journaux de niveau application et service utilisés pour surveiller et diagnostiquer les clusters Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3c472904641108b7383cd0f1416c47460f8de11a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---

# <a name="application-and-service-level-event-and-log-generation"></a>Génération de journaux et d’événements de niveau application et service

## <a name="instrumenting-the-code-with-custom-events"></a>Instrumentation du code avec des événements personnalisés

L’instrumentation du code est à la base de la plupart des autres aspects de la surveillance de vos services. L’instrumentation est le seul moyen de détecter les problèmes et de diagnostiquer ce qui doit être résolu. Bien qu’il soit techniquement possible de connecter un débogueur à un service de production, cette pratique n’est pas courante. Par conséquent, il est important de disposer de données d’instrumentation détaillées.

Certains produits instrumentent automatiquement votre code. Bien que ces solutions soient très efficaces, une instrumentation manuelle est presque toujours nécessaire. Au final, vous devez disposer d’assez d’informations pour déboguer l’application de manière approfondie. Ce document décrit les différentes approches d’instrumentation de votre code et explique quand privilégier telle ou telle approche.

## <a name="eventsource"></a>EventSource
Lorsque vous créez une solution Service Fabric à partir d’un modèle dans Visual Studio, une classe dérivée **EventSource** (**ServiceEventSource** ou **ActorEventSource**) est générée. Un modèle dans lequel vous pouvez ajouter des événements pour votre application ou votre service est créé. Le nom de la classe dérivée **EventSource** **doit** être unique et créé à partir de la chaîne de modèle par défaut MyCompany-&lt;solution&gt;-&lt;project&gt;. Un même nom attribué à plusieurs définitions **EventSource** peut causer des problèmes lors de l’exécution. Chaque événement défini doit avoir un identificateur unique. Si l’identificateur n’est pas unique, l’exécution échoue. Certaines organisations préaffectent des valeurs aux identificateurs afin d’éviter les conflits entre les équipes de développement. Pour plus d’informations, consultez le [blog de Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou la [documentation MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

### <a name="using-structured-eventsource-events"></a>Utilisation d’événements EventSource structurés

Chacun des événements des exemples de code de cette section est défini pour un cas de figure spécifique, par exemple pour l’enregistrement d’un type de service. Lorsque vous définissez des messages par cas d’utilisation, les données peuvent être fournies avec le texte de l’erreur, et vous pouvez plus facilement effectuer des recherches et filtrer les résultats en fonction des noms ou des valeurs des propriétés spécifiées. La structuration de la sortie d’instrumentation facilite l’utilisation, mais elle nécessite plus de réflexion et de temps pour la définition d’un nouvel événement à chaque cas d’utilisation. Certaines définitions d’événement peuvent être partagées dans toute l’application. Par exemple, un événement de commencement ou d’arrêt de méthode peut être réutilisé dans plusieurs services d’une même application. Un service spécifique d’un domaine, comme un système de commande, peut est associé à un événement **CreateOrder**, qui présente son propre événement unique. Cette approche est susceptible de générer de nombreux événements et peut requérir la coordination des identificateurs entre les équipes de projet. 

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The instance constructor is private to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```

### <a name="using-eventsource-generically"></a>Utilisation générale d’EventSource

Étant donné la complexité de la définition d’événements spécifiques, de nombreuses personnes définissent quelques événements avec un ensemble commun de paramètres qui sortent généralement les informations sous forme de chaîne. Une grande partie de la structure est perdue, ce qui complique les recherches et le filtrage des résultats. Dans le cadre de cette approche, quelques événements correspondant généralement aux niveaux de journalisation sont définis. L’extrait de code suivant définit un message d’erreur et de débogage :

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The Instance constructor is private, to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        private const int DebugEventId = 10;
        [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
        public void Debug(string msg)
        {
            WriteEvent(DebugEventId, msg);
        }

        private const int ErrorEventId = 11;
        [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
        public void Error(string error, string msg)
        {
            WriteEvent(ErrorEventId, error, msg);
        }
```

Une approche hybride d’instrumentation structurée et générique peut aussi fonctionner. L’instrumentation structurée sert à créer des rapports sur les erreurs et les mesures. Des événements génériques peuvent être employés pour la journalisation détaillée utilisée par les ingénieurs lors de la résolution des problèmes.

## <a name="aspnet-core-logging"></a>Journalisation ASP.NET Core

Il est important de planifier avec soin la manière dont vous allez instrumenter votre code. Avec le plan d’instrumentation adéquat, vous pouvez éviter de déstabiliser votre base de code et d’avoir alors à réinstrumenter le code. Afin de réduire les risques, vous pouvez choisir une bibliothèque d’instrumentation telle que [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), qui fait partie de Microsoft ASP.NET Core. ASP.NET Core présente une interface [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) que vous pouvez utiliser avec le fournisseur de votre choix, tout en limitant les répercussions sur le code existant. Vous pouvez utiliser le code dans ASP.NET Core sur Windows et Linux, ainsi que dans la version complète de .NET Framework. Par conséquent, votre code d’instrumentation est normalisé. Cette opération est examinée plus en détail ci-dessous :

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Utilisation de Microsoft.Extensions.Logging dans Service Fabric

1. Ajoutez le package NuGet Microsoft.Extensions.Logging au projet à instrumenter. Ajoutez également les éventuels packages de fournisseurs (pour un package tiers, consultez l’exemple suivant). Pour plus d’informations, consultez l’article [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Journalisation dans ASP.NET Core).
2. Ajoutez une directive **using** pour Microsoft.Extensions.Logging à votre fichier de service.
3. Définissez une variable privée dans votre classe de service.

  ```csharp
  private ILogger _logger = null;

  ```
4. Dans le constructeur de votre classe de service, ajoutez le code suivant :

  ```csharp
  _logger = new LoggerFactory().CreateLogger<Stateless>();

  ```
5. Démarrez l’instrumentation de votre code dans vos méthodes. Voici quelques exemples :

  ```csharp
  _logger.LogDebug("Debug-level event from Microsoft.Logging");
  _logger.LogInformation("Informational-level event from Microsoft.Logging");

  // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
  // Later in the article, we discuss why this step is useful.
  _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

  ```

## <a name="using-other-logging-providers"></a>Utilisation d’autres fournisseurs de journalisation

Certains fournisseurs tiers utilisent l’approche décrite dans la section précédente, notamment [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/) et [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Vous pouvez connecter chacun d’eux à la journalisation ASP.NET Core ou les utiliser séparément. Serilog offre une fonctionnalité qui enrichit tous les messages envoyés par un enregistreur d’événements. Cette fonctionnalité peut être utile pour obtenir le nom, le type et les informations de partition du service. Pour utiliser cette fonctionnalité dans l’infrastructure ASP.NET Core, procédez comme suit :

1. Ajoutez les packages NuGet Serilog, Serilog.Extensions.Logging et Serilog.Sinks.Observable au projet. Pour l’exemple ci-après, ajoutez également Serilog.Sinks.Literate. Une meilleure approche est présentée plus loin dans cet article.
2. Dans Serilog, créez un objet LoggerConfiguration et l’instance d’enregistreur d’événements.

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Ajoutez un argument Serilog.ILogger au constructeur de service et exécutez l’enregistreur d’événements nouvellement créé.

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. Dans le constructeur de service, ajoutez le code suivant afin de créer des enrichisseurs de propriétés pour les propriétés **ServiceTypeName**, **ServiceName**, **PartitionId** et **InstanceId** du service. Il ajoute également un enrichisseur de propriétés à la fabrique de journalisation ASP.NET Core, ce qui vous permet d’utiliser Microsoft.Extensions.Logging.ILogger dans votre code.

  ```csharp
  public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
      : base(context)
  {
      PropertyEnricher[] properties = new PropertyEnricher[]
      {
          new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
          new PropertyEnricher("ServiceName", context.ServiceName),
          new PropertyEnricher("PartitionId", context.PartitionId),
          new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
      };

      serilog.ForContext(properties);

      _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
  }
  ```

5. Instrumentez le code de la même manière que si vous utilisiez ASP.NET Core sans Serilog.

  >[!NOTE]
  >Nous vous déconseillons d’utiliser le Log.Logger statique avec l’exemple précédent. En effet, Service Fabric peut héberger plusieurs instances du même type de service dans un seul processus. Si vous utilisez le Log.Logger statique, les valeurs du dernier enregistreur des enrichisseurs de propriétés seront affichées pour toutes les instances en cours d’exécution. C’est l’une des raisons pour lesquelles la variable _logger est une variable membre privée de la classe de service. En outre, vous devez mettre la variable _logger à disposition du code commun, qui est susceptible d’être utilisé pour différents services.

## <a name="choosing-a-logging-provider"></a>Choix d’un fournisseur de journalisation

Si votre application repose sur des performances élevées, **EventSource** constitue généralement une bonne approche. En effet, **EventSource** utilise *généralement* moins de ressources et offre de meilleures performances que la journalisation ASP.NET Core ou que les solutions tierces disponibles.  Cela ne représente pas un problème pour de nombreux services, mais si votre service est axé sur les performances, le recours à **EventSource** peut être plus judicieux. Toutefois, pour obtenir ces avantages de journalisation structurée, **EventSource** nécessite un investissement plus important de la part de votre équipe d’ingénierie. Si possible, effectuez un prototype rapide de quelques options de journalisation, puis choisissez celui qui répond le mieux à vos besoins.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre fournisseur de journalisation choisi pour l’instrumentation de vos applications et services, vos journaux et événements doivent être agrégés pour pouvoir être envoyés à une plateforme d’analyse. Pour mieux comprendre certaines des options recommandées, consultez les informations relatives à [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) et [WAD](service-fabric-diagnostics-event-aggregation-wad.md).

