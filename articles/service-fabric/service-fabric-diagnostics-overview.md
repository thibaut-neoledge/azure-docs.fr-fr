---
title: "Présentation de l’analyse et du diagnostic Azure Service Fabric | Microsoft Docs"
description: "Découvrez comment surveiller et diagnostiquer les applications Microsoft Azure Service Fabric hébergées dans Azure, dans un environnement de développement ou en local."
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: ebbb29ee031fb477ce284f7a0d27c1522317f4f0
ms.openlocfilehash: 46a35fa4ec341561ab234f7ec19fb20658fcb2c4
ms.lasthandoff: 02/27/2017


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Surveiller et diagnostiquer les applications Azure Service Fabric

L’analyse et le diagnostic sont essentiels dans un environnement de production actif. Azure Service Fabric vous permet de mettre en œuvre une analyse et un diagnostic pendant le développement de votre service afin de vous assurer que ce dernier fonctionne parfaitement aussi bien dans un environnement de développement local à une seule machine que sur une configuration de cluster de production réel.

L’analyse et le diagnostic pendant le développement de vos services vous aident à :
* limiter les perturbations pour vos clients ;
* fournir des renseignements sur l’activité ;
* surveiller l’utilisation des ressources ;
* détecter les défaillances matérielles et logicielles ou les problèmes de performances ;
* diagnostiquer les problèmes potentiels de service.

L’analyse est un terme général qui englobe les tâches suivantes :
* Instrumentation du code
* Collecte des journaux d’instrumentation
* Analyse des journaux
* Visualisation des renseignements tirés des données des journaux
* Configuration d’alertes basées sur les valeurs et les renseignements des journaux
* Surveillance de l’infrastructure
* Détection et diagnostic des problèmes qui affectent vos clients

Cet article propose une vue d’ensemble de la surveillance des clusters Service Fabric hébergés dans Azure ou en local, déployés sur Windows ou Linux, ou à l’aide de Microsoft .NET Framework. Il traite plus particulièrement de trois aspects importants de l’analyse et du diagnostic :
- Instrumentation du code ou de l’infrastructure
- Collecte des événements générés
- Stockage, agrégation, visualisation et analyse

Bien que plusieurs produits couvrant l’ensemble de ces trois domaines soient disponibles, de nombreux clients choisissent des technologies différentes pour chaque aspect de l’analyse. Il est important que les composants fonctionnent ensemble afin de fournir une solution d’analyse de bout en bout pour l’application.

## <a name="monitoring-infrastructure"></a>Surveillance de l’infrastructure

Service Fabric peut garantir l’exécution continue d’une application pendant les pannes d’infrastructure, mais vous devez déterminer si une erreur se produit dans l’application ou l’infrastructure sous-jacente. Vous devez également surveiller l’infrastructure dans le cadre de la planification de la capacité afin de savoir quand ajouter ou supprimer l’infrastructure. Il est important de surveiller et de dépanner l’infrastructure et l’application qui composent un déploiement Service Fabric. Même si une application est disponible pour les clients, il se peut que l’infrastructure rencontre toujours des problèmes.

### <a name="azure-monitor"></a>Azure Monitor

Vous pouvez utiliser [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) pour surveiller un grand nombre des ressources Azure sur lesquelles repose un cluster Service Fabric. Un ensemble de mesures pour le [groupe de machines virtuelles identiques](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) et les [machines virtuelles](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuelles est automatiquement collecté et affiché dans le portail Azure. Pour consulter les informations collectées, dans le portail Azure, sélectionnez le groupe de ressources qui contient le cluster Service Fabric. Ensuite, sélectionnez le groupe de machines virtuelles identiques à afficher. Dans la section **Surveillance**, sélectionnez **Mesures** pour afficher un graphique des valeurs.

![Vue des informations de mesure collectées sur le portail Azure](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.PNG)

Pour personnaliser les graphiques, suivez les instructions présentées dans la [vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Vous pouvez également créer des alertes basées sur ces mesures, comme décrit dans l’article [Créer des alertes dans Azure Monitor pour les services Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Vous pouvez envoyer des alertes à un service de notification à l’aide de webhooks, comme décrit dans l’article [Configurer un webhook sur une alerte de métrique Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure Monitor ne prend en charge qu’un seul abonnement. Si vous avez besoin de la prise en charge de surveiller plusieurs abonnements, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), composant de Microsoft Operations Management Suite, offre une solution de gestion informatique globale aussi bien pour les infrastructures locales que pour les solutions basées sur le cloud. Vous pouvez acheminer les données d’Azure Monitor directement dans Log Analytics afin d’afficher les mesures et les journaux pour l’ensemble de votre environnement dans un seul et même emplacement.

Nous vous recommandons d’utiliser Operations Management Suite pour surveiller votre infrastructure locale, mais vous pouvez utiliser une solution déjà mise en œuvre par votre organisation pour la surveillance de l’infrastructure.

### <a name="service-fabric-support-logs"></a>Journaux de support Service Fabric

Si vous devez contacter le support technique Microsoft pour obtenir de l’aide sur votre cluster Azure Service Fabric, les journaux de support sont presque toujours requis. Si votre cluster est hébergé dans Azure, ces journaux sont automatiquement configurés et collectés lors de la création du cluster. Les journaux sont stockés dans un compte de stockage dédié dans le groupe de ressources de votre cluster. Le compte de stockage ne présente pas de nom fixe, mais le compte recense des conteneurs d’objets blob et des tables dont le nom commence par *fabric*. Pour plus d’informations sur la configuration de la collecte de journaux pour un cluster autonome, consultez [Créer et gérer un cluster Azure Service Fabric autonome](service-fabric-cluster-creation-for-windows-server.md) et [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md). Pour les instances Service Fabric autonomes, les journaux doivent être envoyés vers un partage de fichiers local. Vous **devez** communiquer ces journaux à l’équipe de support, qui ne diffuse en aucun cas ces documents à d’autres fins.

## <a name="instrument-your-code"></a>instrumentalisation de votre code

L’instrumentation du code est à la base de la plupart des autres aspects de la surveillance de vos services. L’instrumentation est le seul moyen de détecter les problèmes et de diagnostiquer ce qui doit être résolu. Bien qu’il soit techniquement possible de connecter un débogueur à un service de production, cette pratique n’est pas courante. Par conséquent, il est important de disposer de données d’instrumentation détaillées. Lorsque vous produisez un tel volume d’informations, l’expédition de tous les événements en dehors du nœud local peut se révéler coûteuse. De nombreux services utilisent une stratégie en deux parties pour traiter le volume de données d’instrumentation :
1.  Tous les événements sont conservés dans un fichier journal déployé en local pendant une courte période et sont collectés uniquement si ces données sont nécessaires pour le débogage. En général, les événements requis pour le diagnostic détaillé sont conservés sur le nœud afin de réduire les coûts et l’utilisation des ressources.
2.  Tous les événements qui sont un indicateur de l’intégrité des services sont envoyés à un référentiel central, où ils peuvent être utilisés pour déclencher des alertes en cas de service défectueux. Les événements d’intégrité des services incluent les événements d’erreur, les événements de pulsation et les événements de performances.

Certains produits instrumentent automatiquement votre code. Bien que ces solutions soient très efficaces, une instrumentation manuelle est presque toujours nécessaire. Au final, vous devez disposer d’assez d’informations pour déboguer l’application de manière approfondie. Les sections suivantes décrivent les différentes approches d’instrumentation de votre code et expliquent quand privilégier telle ou telle approche.

### <a name="eventsource"></a>EventSource

Lorsque vous créez une solution Service Fabric à partir d’un modèle dans Visual Studio, une classe dérivée **EventSource** (**ServiceEventSource** ou **ActorEventSource**) est générée. Un modèle dans lequel vous pouvez ajouter des événements pour votre application ou votre service est créé. Le nom de la classe dérivée **EventSource** **doit** être unique et créé à partir de la chaîne de modèle par défaut MyCompany-&lt;solution&gt;-&lt;project&gt;. Un même nom attribué à plusieurs définitions **EventSource** peut causer des problèmes lors de l’exécution. Chaque événement défini doit avoir un identificateur unique. Si l’identificateur n’est pas unique, l’exécution échoue. Certaines organisations préaffectent des valeurs aux identificateurs afin d’éviter les conflits entre les équipes de développement. Pour plus d’informations, consultez le [blog de Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou la [documentation MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

#### <a name="using-structured-eventsource-events"></a>Utilisation d’événements EventSource structurés

Chacun des événements des exemples de code de cette section est défini pour un cas de figure spécifique, par exemple pour l’enregistrement d’un type de service. Lorsque vous définissez des messages par cas d’utilisation, les données peuvent être fournies avec le texte de l’erreur, et vous pouvez plus facilement effectuer des recherches et filtrer les résultats en fonction des noms ou des valeurs des propriétés spécifiées. La structuration de la sortie d’instrumentation facilite l’utilisation, mais elle nécessite plus de réflexion et de temps pour la définition d’un nouvel événement à chaque cas d’utilisation. Certaines définitions d’événement peuvent être partagées dans toute l’application. Par exemple, un événement de commencement ou d’arrêt de méthode peut être réutilisé dans plusieurs services d’une même application. Un service spécifique d’un domaine, comme un système de commande, peut est associé à un événement **CreateOrder**, qui présente son propre événement unique. Cette approche est susceptible de générer de nombreux événements et peut requérir la coordination des identificateurs entre les équipes de projet. Pour obtenir un exemple complet d’événements **EventSource** de structure dans Service Fabric, examinez l’événement **PartyCluster.ApplicationDeployService** dans l’exemple Cluster d’essai.

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

#### <a name="using-eventsource-generically"></a>Utilisation générale d’EventSource

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

### <a name="aspnet-core-logging"></a>Journalisation ASP.NET Core

Il est important de planifier avec soin la manière dont vous allez instrumenter votre code. Avec le plan d’instrumentation adéquat, vous pouvez éviter de déstabiliser votre base de code et d’avoir alors à réinstrumenter le code. Afin de réduire les risques, vous pouvez choisir une bibliothèque d’instrumentation telle que [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), qui fait partie de Microsoft ASP.NET Core. ASP.NET Core présente une interface [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) que vous pouvez utiliser avec le fournisseur de votre choix, tout en limitant les répercussions sur le code existant. Vous pouvez utiliser le code dans ASP.NET Core sur Windows et Linux, ainsi que dans la version complète de .NET Framework. Par conséquent, votre code d’instrumentation est normalisé.

#### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Utilisation de Microsoft.Extensions.Logging dans Service Fabric

1. Ajoutez le package NuGet Microsoft.Extensions.Logging au projet à instrumenter. Ajoutez également les éventuels packages de fournisseurs (pour un package tiers, consultez l’exemple suivant). Pour plus d’informations, consultez l’article [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Journalisation dans ASP.NET Core).
2. Ajoutez une directive **using** pour Microsoft.Extensions.Logging à votre fichier de service.
3. Définissez une variable privée dans votre classe de service.

  ```csharp
  private ILogger _logger = null;

  ```
4. Dans le constructeur de votre classe de service, ajoutez ce code.

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

#### <a name="using-other-logging-providers"></a>Utilisation d’autres fournisseurs de journalisation

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

4. Dans le constructeur de service, ajoutez le code suivant. Ce code crée des enrichisseurs de propriétés pour les propriétés **ServiceTypeName**, **ServiceName**, **PartitionId** et **InstanceId** du service. Il ajoute également un enrichisseur de propriétés à la fabrique de journalisation ASP.NET Core, ce qui vous permet d’utiliser Microsoft.Extensions.Logging.ILogger dans votre code.

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

  > [!NOTE]
  > Nous vous déconseillons d’utiliser le Log.Logger statique avec l’exemple précédent. En effet, Service Fabric peut héberger plusieurs instances du même type de service dans un seul processus. Si vous utilisez le Log.Logger statique, les valeurs du dernier enregistreur des enrichisseurs de propriétés seront affichées pour toutes les instances en cours d’exécution. C’est l’une des raisons pour lesquelles la variable _logger est une variable membre privée de la classe de service. En outre, vous devez mettre la variable _logger à disposition du code commun, qui est susceptible d’être utilisé pour différents services.

### <a name="choosing-a-logging-provider"></a>Choix d’un fournisseur de journalisation

Si votre application repose sur des performances élevées, **EventSource** constitue la meilleure approche. En effet, **EventSource** utilise *généralement* moins de ressources et offre de meilleures performances que la journalisation ASP.NET Core ou que les solutions tierces disponibles.  Cela ne représente pas un problème pour de nombreux services, mais si votre service est axé sur les performances, le recours à **EventSource** peut être plus judicieux. Pour obtenir les mêmes avantages que la journalisation structurée, **EventSource** nécessite un investissement important de la part de votre équipe d’ingénierie. Pour déterminer l’approche adaptée à votre projet, réalisez un prototype rapide de ce que chaque option implique, puis choisissez celui qui répond le mieux à vos besoins.

## <a name="event-and-log-collection"></a>Collecte d’événements et de journaux

### <a name="azure-diagnostics"></a>Azure Diagnostics

En plus des informations offertes par Azure Monitor, Azure collecte les événements de chaque service dans un emplacement central. Pour plus d’informations, découvrez comment configurer la collecte d’événements pour [Windows](service-fabric-diagnostics-how-to-setup-wad.md) et [Linux](service-fabric-diagnostics-how-to-setup-lad.md). Ces deux articles expliquent comment collecter les données d’événement et les envoyer vers le stockage Azure. Vous pouvez le faire dans le portail Azure ou dans votre modèle Azure Resource Manager en activant les diagnostics. La fonctionnalité de diagnostics Azure permet de collecter des sources d’événements générées automatiquement par Service Fabric :

- Événements **EventSource** et compteurs de performances lors de l’utilisation du modèle de programmation Reliable Actors. Les événements sont répertoriés dans [Diagnostics et surveillance des performances pour Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
- Événements **EventSource** lors de l’utilisation du modèle de programmation Reliable Services. Les événements sont répertoriés dans [Fonctionnalité de diagnostic pour Reliable Services avec état](service-fabric-reliable-services-diagnostics.md).
- Les événements du système sont émis en tant qu’événements du suivi d’événements pour Windows (ETW). De nombreux événements émis à partir de Service Fabric entrent dans cette catégorie, notamment les événements de positionnement de service et les événements de démarrage et d’arrêt. La meilleure façon de voir les événements émis consiste à utiliser la [visionneuse d’événements de diagnostic de Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) sur votre ordinateur local. Comme ces événements sont des événements ETW natifs, leur collecte est soumise à certaines restrictions.
- Depuis la version 5.4 de Service Fabric, les événements liés aux mesures d’intégrité et de charge sont exposés. De cette façon, vous pouvez utiliser la collecte d’événements pour créer des rapports historiques et des alertes. Ces événements sont également des événements ETW natifs et leur collecte est soumise à certaines restrictions.

Lorsqu’ils sont configurés, les événements s’affichent dans un compte de stockage Azure créé au moment de la création du cluster, en supposant que vous ayez activé les diagnostics. Les tables sont nommées WADServiceFabricReliableActorEventTable, WADServiceFabricReliableServiceEventTable et WADServiceFabricSystemEventTable. Les événements d’intégrité ne sont pas ajoutés par défaut. Vous devez modifier le modèle Resource Manager pour les ajouter. Pour plus d’informations, consultez [Collecte des journaux avec Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md).

Les articles répertoriés dans cette section expliquent également comment obtenir des événements personnalisés dans le stockage Azure. D’autres articles sur les diagnostics Azure concernant la configuration des compteurs de performances ou des articles contenant d’autres informations au sujet de la surveillance des machines virtuelles sur les diagnostics Azure s’appliquent également aux clusters Service Fabric. Par exemple, si vous ne souhaitez pas utiliser le stockage Table Azure comme destination, consultez [Diffusion des données d’Azure Diagnostics dans le chemin réactif à l’aide d’Event Hubs](../event-hubs/event-hubs-streaming-azure-diags-data.md). Lorsque les événements se trouvent dans Azure Event Hubs, vous pouvez les lire et les envoyer dans l’emplacement de votre choix. Pour en savoir plus sur l’intégration des informations de diagnostic Azure avec Application Insights, consultez [cet article](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/).

Un inconvénient de la fonctionnalité de diagnostics Azure réside dans le fait qu’elle se configure à l’aide d’un modèle Resource Manager. Les diagnostics ne s’appliquent donc qu’au niveau du groupe de machines virtuelles identiques. Un groupe de machines virtuelles identiques correspond à un type de nœud dans Service Fabric. Vous devez configurer chaque type de nœud pour toutes les applications et tous les services susceptibles de s’exécuter sur un nœud de ce type. Cela peut représenter un grand nombre d’événements **EventSource** selon le nombre d’applications et de services configurés. Vous devez également déployer Resource Manager à chaque fois que la configuration d’une application change. Dans l’idéal, la configuration de la surveillance s’accompagne de la configuration de service.

La fonctionnalité de diagnostics Azure prend uniquement en charge les clusters Service Fabric déployés sur Azure. Elle fonctionne à la fois pour les clusters Windows et Linux.

### <a name="eventflow"></a>EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) peut acheminer les événements d’un nœud vers une ou plusieurs destinations de surveillance. Étant donné que cet outil est inclus en tant que package NuGet dans votre projet de service, le code et la configuration d’EventFlow accompagnent le service, éliminant ainsi le problème de configuration par nœud mentionné plus haut à propos des diagnostics Azure. EventFlow s’exécute au sein de votre processus de service et se connecte directement aux sorties configurées. En raison de cette connexion directe, EventFlow fonctionne pour les déploiements d’un service sur Azure, dans un conteneur et en local. Faites preuve de prudence si vous exécutez EventFlow dans des scénarios de haute densité, par exemple dans un conteneur, car chaque pipeline EventFlow établit une connexion externe. Si vous hébergez un grand nombre de processus, cela donne lieu à un grand nombre de connexions sortantes. Ce n’est pas vraiment une préoccupation pour les applications Service Fabric, car tous les réplicas d’un événement `ServiceType` s’exécutent dans le même processus, ce qui limite le nombre de connexions sortantes. EventFlow propose également le filtrage des événements. Ainsi, seuls les événements correspondant au filtre spécifié sont envoyés. Pour plus d’informations sur l’utilisation d’EventFlow avec Service Fabric, consultez [Collecter les journaux directement à partir d’un processus de service Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md).

Pour utiliser EventFlow :

1. Ajoutez le package NuGet à votre projet de service.
2. Dans la fonction **Main** du service, créez le pipeline EventFlow, puis configurez les sorties. Dans l’exemple ci-après, nous utilisons Serilog en tant que sortie.

  ```csharp
  internal static class Program
  {
      /// <summary>
      /// This is the entry point of the service host process.
      /// </summary>
      private static void Main()
      {
          try
          {
              using (var pipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MonitoringE2E-Stateless-Pipeline"))
              {
                  Log.Logger = new LoggerConfiguration().WriteTo.EventFlow(pipeline).CreateLogger();

                  // The ServiceManifest.xml file defines one or more service type names.
                  // Registering a service maps a service type name to a .NET type.
                  // When Service Fabric creates an instance of this service type,
                  // an instance of the class is created in this host process.

                  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                  ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);

                  // Prevents this host process from terminating, so services keep running.
                  Thread.Sleep(Timeout.Infinite);
              }
          }
          catch (Exception e)
          {
              ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
              throw;
          }
      }
  }
  ```

3. Créez un fichier nommé eventFlowConfig.json dans le dossier \\PackageRoot\\Config du service. Dans le fichier, la configuration ressemble à ceci :

  ```json
      {
      "inputs": [
          {
          "type": "EventSource",
          "sources": [
              { "providerName": "Microsoft-ServiceFabric-Services" },
              { "providerName": "Microsoft-ServiceFabric-Actors" },
              { "providerName": "MyCompany-MonitoringE2E-Stateless" }
          ]
          },
          {
          "type": "Serilog"
          }
      ],
      "filters": [
          {
          "type": "drop",
          "include": "Level == Verbose"
          },
          {
          "type": "metadata",
          "metadata": "request",
          "requestNameProperty": "RequestName",
          "include":  "RequestName==MyRequest",
          "durationProperty": "Duration",
          "durationUnit": "milliseconds"
          }
      ],
      "outputs": [
          {
          "type": "StdOutput"
          },
          {
          "type": "ApplicationInsights",
          "instrumentationKey": "== instrumentation key here =="
          }
      ],
      "schemaVersion": "2016-08-11",
      "extensions": []
      }
  ```
    Dans la configuration, deux entrées sont définies : les deux sources basées sur **EventSource** que Service Fabric crée, et **EventSource** pour le service. Les événements d’intégrité et système qui utilisent ETW ne sont pas disponibles pour EventFlow. En effet, un privilège de haut niveau est requis pour l’écoute d’une source ETW, et les services ne s’exécutent jamais sans privilèges de ce niveau. L’autre entrée est Serilog. Sa configuration est indiquée dans la méthode **Main**.  Certains filtres sont appliqués. Le premier filtre indique à EventFlow de supprimer tous les événements qui présentent un niveau d’événement détaillé. Deux sorties sont configurées : la sortie standard, qui s’affiche dans la fenêtre de sortie de Visual Studio, et ApplicationInsights. Veillez à ajouter votre clé d’instrumentation.

4. Instrumentez le code. Dans l’exemple suivant, nous instrumentons `RunAsync` de différentes façons à des fins d’illustration. Dans le code suivant, nous utilisons toujours Serilog. Une partie de la syntaxe utilisée est propre à Serilog. Prenez en compte les capacités spécifiques de la solution de journalisation que vous avez choisie. Trois événements sont générés : un événement de débogage et deux événements d’information. Le deuxième événement d’information suit la durée des demandes. Dans la configuration d’EventFlow décrite plus haut, l’événement de débogage ne doit pas être diffusé en flux vers la sortie.

  ```csharp
      Stopwatch sw = Stopwatch.StartNew();

      while (true)
      {
          cancellationToken.ThrowIfCancellationRequested();

          sw.Restart();

          // Delay a random interval, to provide a more interesting request duration.
          await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

          ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
          _logger.LogDebug("Debug level event from Microsoft.Logging");
          _logger.LogInformation("Informational level event from Microsoft.Logging");
          _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
      }
  ```

Pour afficher les événements dans Azure Application Insights, dans le portail Azure, accédez à votre ressource Application Insights. Pour afficher les événements, sélectionnez la zone **Rechercher**.

![Vue de recherche des événements dans Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.PNG)

Vous pouvez voir les traces en bas de la capture d’écran précédente. Seulement deux événements sont affichés, l’événement de débogage ayant été supprimé par EventFlow. L’entrée de demande au-dessus de la trace correspond à la troisième ligne d’instrumentation `_logger`. Cette ligne montre que l’événement a été converti en mesure de demande dans Application Insights.

Dans la définition du filtre, le type est **metadata**. Cela indique qu’un événement présente une propriété `RequestName` avec la valeur `MyRequest` et qu’une autre propriété, `Duration`, contient la durée de la demande, en millisecondes. Voici ce que vous voyez dans l’événement de demande au sein d’Application Insights. La même approche fonctionne pour toutes les entrées EventFlow prises en charge, y compris **EventSource**.

Si vous avez un cluster autonome qui ne peut pas être connecté à une solution basée sur le cloud pour des raisons de stratégie, vous pouvez utiliser Elasticsearch en tant que sortie. Cependant, d’autres sorties peuvent être affichées, et les demandes d’extraction sont encouragées. Certains fournisseurs tiers de journalisation ASP.NET Core proposent également des solutions prenant en charge les installations locales.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Rapports d’intégrité et de charge Azure Service Fabric

Service Fabric présente son propre modèle de contrôle d’intégrité, qui est détaillé dans les articles suivants :
- [Présentation du contrôle d’intégrité de Service Fabric](service-fabric-health-introduction.md)
- [Signaler et contrôler l’intégrité du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Ajout de rapports d’intégrité Service Fabric personnalisés](service-fabric-report-health.md)
- [Affichage rapports d’intégrité de Service Fabric](service-fabric-view-entities-aggregated-health.md)

Le contrôle d’intégrité est essentiel à plusieurs aspects de l’exploitation d’un service. Le contrôle d’intégrité est particulièrement important lorsque Service Fabric effectue la mise à niveau d’une application nommée. À chaque fois qu’un domaine de mise à niveau du service est mis à niveau et devient accessible à vos clients, il doit passer des contrôles d’intégrité avec succès avant que le déploiement puisse passer au domaine de mise à niveau suivant. S’il est impossible d’obtenir un état d’intégrité correct, le déploiement est restauré afin de laisser l’application dans un état correct connu. Bien que certains clients puissent être affectés avant la restauration des services, la plupart des clients ne rencontreront aucun problème. En outre, la résolution s’effectue assez rapidement, sans nécessiter d’intervention humaine. Plus votre code contient de contrôles d’intégrité, plus votre service est résilient face aux problèmes de déploiement.

L’intégrité du service inclut un autre aspect : le rapport de mesures à partir du service. Les mesures sont importantes dans Service Fabric, car elles permettent d’équilibrer l’utilisation des ressources. Les mesures peuvent aussi servir d’indicateur de l’intégrité du système. Par exemple, vous pouvez avoir une application qui comporte de nombreux services, chaque instance envoyant des rapports sur une mesure de demandes par seconde (RPS). Si un service utilise plus de ressources qu’un autre, Service Fabric déplace les instances de service autour du cluster afin de maintenir une utilisation homogène des ressources. Pour une explication plus détaillée du fonctionnement de l’utilisation des ressources, consultez [Gestion de la consommation des ressources et des charges dans Service Fabric à l’aide de mesures](service-fabric-cluster-resource-manager-metrics.md).

Les mesures peuvent également vous renseigner sur les performances de votre service. Au fil du temps, vous pouvez utiliser des mesures pour vérifier que le service fonctionne selon les paramètres prévus. Par exemple, si les tendances indiquent que le nombre moyen de demandes par seconde (RPS) s’élève à 1 000 le lundi à 9 h 00, vous pouvez configurer un rapport d’intégrité qui vous alerte lorsque le RPS est inférieur à 500 ou supérieur à 1 500. Tout peut se passer parfaitement bien, mais il peut être intéressant de vérifier la qualité de l’expérience dont bénéficient vos clients. Votre service offre la possibilité de définir un ensemble de mesures pouvant faire l’objet de rapports dans le cadre de contrôles d’intégrité, mais qui n’affectent pas l’équilibre des ressources du cluster. Pour ce faire, définissez le poids de mesure sur zéro. Nous vous recommandons de démarrer toutes les mesures avec un poids égal à zéro et de ne pas augmenter cette valeur avant d’être certain de bien comprendre l’impact de la pondération des mesures sur l’équilibrage des ressources de votre cluster.

> [!TIP]
> N’utilisez pas un trop grand nombre de mesures pondérées. Il peut être difficile de comprendre pourquoi les instances de service sont déplacées à des fins d’équilibrage. Quelques mesures peuvent en dire long !

Toute information qui peut être un indicateur de l’intégrité et des performances de votre application est appropriée pour les rapports de mesure et d’intégrité. Un compteur de performances du processeur peut vous renseigner sur l’utilisation de votre nœud, mais il ne vous permet pas de savoir si un service particulier est intègre, car plusieurs services peuvent s’exécuter sur un seul nœud. Cependant, des mesures telles que les demandes par seconde (RPS), les éléments traités et la latence des demandes peuvent toutes être un indicateur de l’intégrité d’un service spécifique.

Pour obtenir des rapports d’intégrité, utilisez un code similaire à ceci :

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

Pour obtenir des rapports sur une mesure, utilisez un code similaire à ceci :

  ```csharp
    this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

## <a name="watchdogs"></a>Agents de surveillance

Un agent de surveillance est un service distinct qui peut surveiller l’intégrité et la charge des services, et créer des rapports d’intégrité pour n’importe quel élément de la hiérarchie du modèle d’intégrité. Cela permet d’éviter des erreurs qui ne seraient pas détectées à partir de l’affichage d’un service unique. Les agents de surveillance sont également un bon moyen d’héberger du code capable d’effectuer des actions de correction dans des conditions connues, sans nécessiter l’intervention d’un utilisateur.

## <a name="visualization-analysis-and-alerts"></a>Visualisation, analyse et alertes

La dernière partie de la surveillance consiste en la visualisation du flux d’événements, la création de rapports sur les performances de service et la génération d’alertes en cas de détection d’un problème. Vous pouvez utiliser différentes solutions pour cet aspect de la surveillance. Vous pouvez utiliser Azure Application Insights et Operations Management Suite pour créer des alertes basées sur le flux d’événements. Vous pouvez utiliser Microsoft Power BI ou une solution tierce telle que [Kibana](https://www.elastic.co/products/kibana) ou [Splunk](https://www.splunk.com/) pour visualiser les données.

## <a name="next-steps"></a>Étapes suivantes

* [Collecte des journaux avec Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md)
* [Collecter les journaux directement à partir d’un processus de service Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Gestion de la consommation des ressources et des charges dans Service Fabric à l’aide de mesures](service-fabric-cluster-resource-manager-metrics.md)

