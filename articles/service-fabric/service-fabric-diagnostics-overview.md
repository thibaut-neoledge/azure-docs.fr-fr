---
title: "Présentation de l’analyse et du diagnostic Azure Service Fabric | Microsoft Docs"
description: "Découvrez comment surveiller et diagnostiquer les applications Microsoft Azure Service Fabric hébergées dans Azure, en développement ou en local."
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
ms.date: 02/3/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: fdb15c3af4980e284e1a9effe434b1cab959d24c
ms.openlocfilehash: 826ae11e4826b37379caa53d85d8ec834a5ed0fb


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Surveiller et diagnostiquer les applications Azure Service Fabric

L’analyse, la détection, le diagnostic et la résolution des problèmes permettent aux services de fonctionner avec une interruption minimale de l’expérience utilisateur, et de fournir des connaissances professionnelles, surveiller l’utilisation des ressources, détecter les pannes matérielles et logicielles ou les problèmes de performances, et diagnostiquer les problèmes potentiels de comportement de service. Bien que l’analyse et le diagnostic soient essentiels dans un environnement de production réel déployé, leur efficacité dépend de l’adoption d’un modèle similaire pendant le développement des services pour garantir leur fonctionnement lors du passage à une configuration réelle. Service Fabric facilite pour les développeurs de service l’implémentation de diagnostics qui peuvent fonctionner parfaitement aussi bien sur une configuration de développement d’ordinateur local unique que sur une configuration réelle de cluster de production. 

L’analyse est un terme général qui englobe l’instrumentation du code, la collecte des journaux d’instrumentation, l’analyse des journaux, la visualisation des connaissances basées sur les données de journal, les alertes basées sur les valeurs des journaux et les connaissances, la surveillance de l’infrastructure et la possibilité pour les ingénieurs de détecter et diagnostiquer les problèmes qui affectent leurs clients. Cet article vise à fournir une vue d’ensemble de la surveillance des clusters Service Fabric dans Azure ou en local, déployés sur Windows ou Linux à l’aide de .NET. Commençons par scinder le problème en trois
- Instrumentation du code ou de l’infrastructure
- Collecte d’événements générés
- Stockage, agrégation, visualisation et analyse

Bien que certains produits couvrent ces trois aspects, de nombreux clients choisissent des technologies différentes pour chaque domaine. Les produits doivent s’associer parfaitement afin de fournir une solution de surveillance de bout en bout pour l’application. 

## <a name="monitoring-infrastructure"></a>Surveillance de l’infrastructure

Alors que Service Fabric assure l’exécution de l’application pendant les pannes d’infrastructure, les opérateurs de l’application doivent repérer les erreurs potentielles au sein de l’application ou de l’infrastructure sous-jacente. Il est également nécessaire de surveiller l’infrastructure dans le cadre de la planification de la capacité afin de déterminer quand ajouter ou supprimer l’infrastructure. Il est important de surveiller et de dépanner l’infrastructure et l’application qui composent un déploiement Service Fabric. Tant que l’application est disponible pour les clients, une partie de l’infrastructure peut présenter des problèmes.

### <a name="azure-monitoring"></a>Surveillance Azure

Pour les clusters déployés sur Azure, la [surveillance Azure](../monitoring-and-diagnostics/toc.md) permet de surveiller un grand nombre de ressources Azure sur lesquelles est basé un cluster Service Fabric. Un ensemble de mesures est automatiquement collecté et affiché dans le portail Azure pour le [groupe de machines virtuelles identiques (VMSS)](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) et les [machines virtuelles](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuelles. Vous pouvez afficher ces informations dans le portail Azure en sélectionnant le groupe de ressources du cluster Service Fabric et en choisissant le VMSS à afficher. Sélectionnez ensuite Mesures dans la section de navigation de surveillance pour afficher les valeurs sous forme de graphique

![Vue des informations de mesure collectées sur le portail Azure](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Les graphiques peuvent être personnalisés conformément aux instructions fournies dans l’article [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Les alertes peuvent également être créées en fonction de ces mesures comme indiqué dans l’article [Utiliser le Portail Azure pour créer des alertes pour les services Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Des alertes peuvent être envoyées à un service de notification à l’aide de Webhooks, comme décrit dans l’article [Configurer un webhook sur une alerte de métrique Azure (../monitoring-and-diagnostics/insights-webhooks-alerts.md). La surveillance Azure prend en charge un seul abonnement. Si la prise en charge de plusieurs abonnements ou si d’autres fonctions sont requises, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), composant d’[Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), offre une solution de gestion informatique globale aussi bien pour les infrastructures sur site que pour les solutions basées sur le cloud. Les données de surveillance Azure peuvent être acheminées directement dans Log Analytics afin que vous puissiez voir les mesures et journaux pour l’ensemble de votre environnement en un seul endroit.

[Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) est la méthode recommandée pour l’analyse de votre infrastructure sur site, mais une solution déjà mise en œuvre par votre entreprise pour la surveillance de l’infrastructure peut également être utilisée.

### <a name="service-fabric-support-logs"></a>Journaux de support Service Fabric

Si vous devez contacter le support technique Microsoft pour obtenir de l’aide sur votre cluster Azure Service Fabric, les journaux de support sont presque toujours requis. Si votre cluster est hébergé sur Azure, ces journaux sont automatiquement configurés et collectés lors de la création du cluster. Les journaux sont stockés dans un compte de stockage dédié, qui peut être consulté dans le groupe de ressources de votre cluster. Aucun nom fixe n’est défini pour le compte de stockage, mais le compte recense des conteneurs d’objets blob et des tables commençant par « fabric ». Si votre cluster est un cluster autonome, vous devez configurer la collecte de ces journaux conformément aux recommandations indiquées dans [Créer et gérer un cluster Azure Service Fabric autonome](service-fabric-cluster-creation-for-windows-server.md) et [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md). Pour un Service Fabric autonome, les journaux doivent être envoyés vers un partage de fichiers local. Vous **devez** communiquer ces journaux à l’équipe de support, qui ne diffusera en aucun cas ces documents à d’autres fins. Mon pouvoir de Jedi me dit que ce ne sont pas les journaux qui vous intéressent…

## <a name="instrument-your-code"></a>instrumentalisation de votre code

L’instrumentation du code est à la base de la plupart des autres aspects de la surveillance de vos services. Les gens sont souvent surpris de voir à quel point cette instrumentation est nécessaire, mais ils ne devraient pas compte tenu du fait que cette instrumentation est le seul moyen de détecter les problèmes et de diagnostiquer ce qui doit être corrigé. Bien qu’il soit techniquement possible de le faire, un débogueur est rarement relié à un service de production. Il est donc crucial de disposer de données d’instrumentation détaillées. Lors de la production de ce volume d’informations, l’expédition de tous les événements en dehors du nœud local peut se révéler coûteuse. De nombreux services utilisent une stratégie en deux parties pour traiter le volume de données d’instrumentation :
* Tous les événements sont conservés dans des fichiers journaux déployés en local pendant quelques jours et collectés uniquement si ces données sont nécessaires pour le débogage. En général, les événements requis pour le diagnostic détaillé sont conservés sur le nœud afin de réduire les coûts et l’utilisation des ressources
* Tous les événements indiquant l’état du service, comme les événements d’erreur, les événements de pulsation ou les événements de performances, sont envoyés vers un référentiel central où ils peuvent être utilisés pour émettre des alertes sur un service défectueux.

Certaines solutions instrumentent automatiquement votre code. Bien que ces produits soient très efficaces, une instrumentation manuelle est presque toujours nécessaire. Au final, vous devez disposer d’assez d’informations pour déboguer l’application de manière approfondie. Les sections suivantes décrivent les différentes approches d’instrumentation de votre code et vous éclairent dans votre choix.

### <a name="eventsource"></a>EventSource

Lorsque vous créez une solution Azure Service Fabric à partir d’un modèle dans Visual Studio, une classe dérivée EventSource (*ServiceEventSource* ou *ActorEventSource*) est générée. Vous obtenez ainsi un modèle auquel vous pouvez ajouter des événements supplémentaires appropriés pour votre application ou votre service. Le nom de la classe dérivée EventSource **doit** être unique et créé à partir de la chaîne de départ « MyCompany-&lt;solution&gt;-&lt;projet&gt; ». Un même nom attribué à plusieurs définitions EventSource peut causer des problèmes lors de l’exécution. Chaque événement défini doit avoir un identificateur unique. Si l’identificateur n’est pas unique, l’exécution échoue. Des plages de valeurs sont souvent préaffectées aux identificateurs afin d’éviter les conflits entre les équipes de développement. Pour plus d’informations sur EventSource, consultez le [blog de Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou la [documentation MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

#### <a name="using-structured-eventsource-events"></a>Utilisation d’événements EventSource structurés

Chacun des événements suivants est défini pour un cas spécifique, par exemple pour l’enregistrement d’un type de service. Cette définition des messages permet d’inclure les données avec le texte de l’erreur. Cela optimise la recherche et le filtrage en fonction des noms ou des valeurs des propriétés spécifiées. La structuration de la sortie d’instrumentation facilite l’utilisation, mais elle nécessite plus de réflexion et de temps pour la définition d’un nouvel événement à chaque cas d’utilisation. Certaines définitions d’événement peuvent être partagées dans toute l’application. Par exemple, un événement de commencement ou d’arrêt de méthode peut être réutilisé dans plusieurs services d’une même application. Un service spécifique à un domaine, comme un système de commande, peut est associé à un événement CreateOrder, qui aura son propre événement unique. Cette approche génère souvent de nombreux événements et peut requérir la coordination des identificateurs entre les équipes de projet. Pour obtenir un exemple complet de structure EventSource dans Service Fabric, observez PartyCluster.ApplicationDeployService dans l’exemple Cluster d’essai.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
        private ServiceEventSource() : base() { }

        ...

        // ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```
#### <a name="using-eventsource-generically"></a>Utilisation générale d’EventSource

Étant donné la complexité de la définition d’événements spécifiques, de nombreuses personnes définissent peu d’événements avec un ensemble commun de paramètres qui sortent généralement les informations sous forme de chaîne. Une grande partie de la structure est perdue, ce qui complique la recherche et le filtrage des résultats. Avec cette approche, quelques événements correspondant généralement aux niveaux de journalisation sont définis. L’extrait de code suivant définit un message d’erreur et de débogage.
```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
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
Une approche hybride d’instrumentation structurée et générique peut aussi fonctionner. Lorsque les rapports d’erreurs et de mesures ont recours à l’instrumentation structurée, des événements génériques peuvent être employés pour la journalisation détaillée utilisée par les ingénieurs lors du dépannage.

### <a name="aspnet-core-logging"></a>Journalisation ASP.NET Core

Le choix de l’instrumentation de votre code peut être difficile. Si vous vous trompez et devez procéder à une réinstrumentation, vous devez réexaminer et potentiellement déstabiliser votre base de code. Afin de réduire le risque, les développeurs peuvent choisir une bibliothèque d’instrumentation telle que [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), fournie par ASP.NET Core. Une interface [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) vous permet alors d’utiliser le fournisseur de votre choix tout en réduisant l’impact sur le code existant. Autre aspect intéressant : le code peut être utilisé dans .NET Core sur Windows et Linux, mais aussi dans la version complète du framework, vous donnant ainsi la possibilité de normaliser votre code d’instrumentation dans .NET et .NET Core.

#### <a name="how-to-use-microsoftextensionslogging-within-service-fabric"></a>Utilisation de Microsoft.Extensions.Logging dans Service Fabric

1. Ajoutez le package NuGet **Microsoft.Extensions.Logging** au projet à instrumenter. Vous souhaiterez peut-être aussi ajouter des packages de fournisseurs. Nous aborderons ce point avec un package tiers ultérieurement. Consultez la page [Logging in ASP.NET Core (Journalisation dans ASP.NET Core)](https://docs.microsoft.com/aspnet/core/fundamentals/logging) pour plus d’informations
2. Ajoutez une directive pour « Microsoft.Extensions.Logging » à votre fichier de service
3. Définissez une variable privée dans votre classe de service
```csharp
        private ILogger _logger = null;
```
4. Dans le constructeur de votre classe de service, ajoutez
```csharp
        _logger = new LoggerFactory().CreateLogger<Stateless>();
```
5. Démarrez l’instrumentation de votre code dans vos méthodes. Voici quelques exemples
```csharp
        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");

        // In this variant, we're adding structured properties RequestName and Duration that has values MyRequest and the duration of the request.
        // More on why you'll want to do this later.
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
```

#### <a name="using-other-logging-providers"></a>Utilisation d’autres fournisseurs de journalisation

Parmi les fournisseurs tiers fonctionnant avec cette approche, on peut citer [SeriLog](https://serilog.net/), [NLog](http://nlog-project.org/) et [loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Chacun d’entre eux peut être associé à la journalisation ASP.Net Core et être utilisé séparément. SeriLog possède une fonctionnalité qui permet d’enrichir tous les messages envoyés à partir d’un enregistreur, ce qui peut être utile pour générer le nom du service, le type et les informations de partition. Pour utiliser cette fonctionnalité dans l’infrastructure ASP.NET Core, effectuez les opérations suivantes

1. Ajoutez les packages NuGet **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Observable** dans le projet. Ajoutez également **SeriLog.Sinks.Literate** pour cet exemple. Une approche plus adaptée est présentée dans la suite de cet article
2. Créez un objet LoggerConfiguration et l’instance d’enregistreur dans SeriLog
```csharp
    Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
```
3. Ajoutez un argument SeriLog.ILogger au constructeur de service et exécutez l’enregistreur d’événements nouvellement créé
```csharp
    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
```
4. Dans le constructeur de service, ajoutez le code suivant afin de créer des enrichisseurs de propriétés pour les propriétés ServiceTypeName, ServiceName, PartitionId et InstanceId du service. Cela affecte également la fabrique de journalisation ASP.NET Core pour que Microsoft.Extensions.Logging.ILogger soit utilisable dans votre code.
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
5. Instrumentez le code de la même manière que lorsque vous utilisez ASP.NET Core sans SeriLog.

> [!NOTE] 
> Il n’est pas recommandé d’utiliser le Log.Logger statique avec cette approche, car Service Fabric peut héberger plusieurs instances du même type de service dans un seul processus. Autrement dit, les valeurs du dernier enregistreur des enrichisseurs de propriétés seraient affichées pour toutes les instances en cours d’exécution. C’est l’une des raisons pour lesquelles la variable _logger est une variable membre privée de la classe de service. Cela signifie également que la variable _logger doit être mise à la disposition du code commun susceptible d’être utilisé dans les services. 

### <a name="which-one-should-i-use"></a>Que dois-je utiliser ?

Si votre application accorde une grande importance aux performances, EventSource constitue la meilleure approche, car cette classe utilise **généralement** moins de ressources et offre de meilleures performances que la journalisation ASP.NET Core ou une solution tierce.  Cela ne représente pas un problème pour de nombreux services, mais si votre service est axé sur les performances, le recours à EventSource peut être plus judicieux. Pour obtenir les mêmes avantages que la journalisation structurée, EventSource nécessite un investissement important de la part de l’équipe d’ingénierie. Le meilleur moyen de déterminer la solution adaptée à votre projet consiste à réaliser un prototype rapide de ce que vous feriez pour chaque approche, puis à choisir l’approche qui convient le mieux à vos besoins.

## <a name="event-and-log-collection"></a>Collecte d’événements et de journaux

### <a name="azure-diagnostics"></a>Azure Diagnostics

Outre ce que la surveillance Azure fournit déjà, Azure offre également un moyen de collecter les événements à partir de chacun des services vers un emplacement central. Deux articles expliquent comment configurer la collecte d’événements pour [Windows](service-fabric-diagnostics-how-to-setup-wad.md) et [Linux](service-fabric-diagnostics-how-to-setup-lad.md). Ils montrent comment collecter les données d’événement et comment les envoyer vers le stockage Azure. Cette opération simple peut se faire sur le portail ou dans le modèle Resource Manager grâce à l’activation des diagnostics. Cette fonctionnalité permet de collecter des sources d’événements générées automatiquement par Service Fabric :

- Événements EventSource et compteurs de performances lors de l’utilisation du modèle de programmation Reliable Actors. Les événements sont répertoriés dans l’article intitulé [Diagnostics et surveillance des performances pour Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
- Événements EventSource lors de l’utilisation du modèle de programmation Reliable Services. Les événements sont répertoriés dans l’article intitulé [Fonctionnalité de diagnostic pour Reliable Services avec état](service-fabric-reliable-services-diagnostics.md)
- Les événements système sont émis en tant qu’événements ETW. De nombreux événements sont émis à partir de Service Fabric. Dans le cadre de cette catégorie, ils incluent le positionnement de service et les événements de démarrage et d’arrêt. La meilleure façon de voir les événements émis consiste à utiliser la [visionneuse de diagnostics Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) lors de l’exécution sur votre ordinateur local. Comme ces événements sont des événements ETW natifs, leur collecte est soumise à certaines restrictions
- Depuis la version 5.4 de Service Fabric, les événements liés aux mesures d’intégrité et de charge sont exposés. Il est ainsi possible de collecter ces événements pour créer des rapports historiques et des alertes. Ces événements sont également des événements ETW natifs et leur mode de collecte est soumis à certaines restrictions

Lors de la configuration, ces événements apparaissent dans l’un des comptes de stockage Azure créés en même temps que le cluster, à condition que le diagnostic ait été activé. Les tables sont nommées *WADServiceFabricReliableActorEventTable*, *WADServiceFabricReliableServiceEventTable* et *WADServiceFabricSystemEventTable*. Les événements d’intégrité ne sont pas ajoutés par défaut et nécessitent la modification du modèle Resource Manager. Consultez l’article [Collecte des journaux avec Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md) pour plus d’informations.

Ces articles expliquent également comment obtenir des événements personnalisés dans le stockage Azure. Les articles dédiés au diagnostic Azure relatif à la configuration des compteurs de performances ou d’autres informations de surveillance d’une machine virtuelle sur les diagnostics Azure s’appliquent également aux clusters Service Fabric. Par exemple, si vous ne souhaitez pas définir Azure Stockage Table comme destination, consultez l’article intitulé [Diffusion des données d’Azure Diagnostics dans le chemin réactif à l’aide d’Event Hubs](../event-hubs/event-hubs-streaming-azure-diags-data.md). Une fois que les événements apparaissent dans Event Hubs, ils peuvent être lus et envoyés à l’emplacement de votre choix. Un article est également dédié à l’intégration des [informations de diagnostic Azure avec Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)

Néanmoins, l’utilisation des diagnostics Azure comporte quelques inconvénients. Par exemple, la configuration s’effectue à l’aide d’un modèle Resource Manager et se produit donc au niveau du VMSS uniquement. Un VMSS correspond à un type de nœud dans Service Fabric. Autrement dit, vous devez configurer chaque type de nœud pour toutes les applications et tous les services susceptibles de s’exécuter sur un nœud de ce type. Cela peut représenter un grand nombre d’événements EventSource selon le nombre d’applications et de services configurés. En outre, Resource Manager doit être déployé à chaque modification de la configuration d’une application. Dans l’idéal, la configuration de la surveillance s’accompagne de la configuration de service.

Les diagnostics Azure fonctionnent uniquement pour les clusters Service Fabric déployés sur Azure, ainsi que pour les clusters Windows et Linux.

### <a name="eventflow"></a>EventFlow

[EventFlow a été publiée par l’équipe Visual Studio](service-fabric-diagnostic-collect-logs-without-an-agent.md) et fournit un mécanisme de routage des événements à partir d’un nœud vers une ou plusieurs destinations de surveillance. Étant donné qu’elle est incluse en tant que package NuGet dans votre projet de service, le code et la configuration d’EventFlow accompagne le service, éliminant ainsi le problème de configuration par nœud mentionné à propos des diagnostics Azure. EventFlow s’exécute au sein de votre processus de service et se connecte directement aux sorties configurées. En raison de cette connexion directe, EventFlow fonctionne pour les déploiements d’un service sur Azure, dans un conteneur ou en local. L’exécution dans des scénarios de haute densité comme un conteneur nécessite une attention toute particulière, car chaque pipeline EventFlow établit une connexion externe, et si vous hébergez un grand nombre de processus, vous récupérez de nombreuses connexions sortantes. Ce n’est pas un gros problème pour les applications Service Fabric, car tous les réplicas d’un événement ServiceType s’exécutent dans le même processus, ce qui limite le nombre de connexions sortantes. EventFlow propose également le filtrage des événements. Ainsi, seuls les événements correspondant au filtre spécifié sont envoyés. Pour plus d’informations sur l’utilisation d’EventFlow avec Service Fabric, consultez [Collecter les journaux directement à partir d’un processus de service Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)

> [!NOTE]
> Dans une prochaine version de Service Fabric, une application hôte EventSource sera disponible afin de permettre l’écoute des entrées basées sur ETW, la collecte de mesures au niveau du nœud et la prise en charge du déploiement des fichiers journaux.

L’utilisation d’EventFlow est assez simple
1. Ajoutez le package NuGet à votre projet de service
2. Dans la fonction **Main** du service, créez le pipeline EventFlow et configurez les sorties. Nous allons ici utiliser SeriLog en tant que sortie
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
                    IObserver<LogEvent> serilogInput = pipeline.Inputs.OfType<SerilogInput>().First();
                    Log.Logger = new LoggerConfiguration().WriteTo.Observers(events => events.Subscribe(serilogInput)).CreateLogger();

                    // The ServiceManifest.XML file defines one or more service type names.
                    // Registering a service maps a service type name to a .NET type.
                    // When Service Fabric creates an instance of this service type,
                    // an instance of the class is created in this host process.

                    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);
                    
                    // Prevents this host process from terminating so services keep running.
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
3. Créez un fichier nommé *eventFlowConfig.json* dans le dossier PackageRoot | Config du service. Dans le fichier, la configuration ressemble à ce qui suit
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
Dans la configuration, deux entrées sont définies : les deux sources basées sur EventSource et créées par Service Fabric, et EventSource pour le service. Le niveau du système et les événements d’intégrité qui utilisent ETW ne sont pas disponibles pour EventFlow. En effet, un privilège de haut niveau est requis pour l’écoute d’une source ETW, et les services ne s’exécutent jamais sans privilèges de ce niveau. L’autre entrée est SeriLog : sa configuration est indiquée dans la méthode **Main**.  Des filtres sont également appliqués. Le premier demande à EventFlow de supprimer tous les événements qui correspondent à un niveau d’événement détaillé. Nous reviendrons un peu plus tard à la définition de l’autre filtre. Deux sorties sont également configurées. La sortie standard écrit dans la fenêtre de sortie de Visual Studio. L’autre sortie est Application Insights. Veillez à bien ajouter votre clé d’instrumentation.

4. La dernière étape consiste à instrumenter le code. Dans cet exemple, nous allons instrumenter RunAsync de différentes manières. Dans le code ci-dessous, nous utilisons toujours SeriLog, et une partie de la syntaxe utilisée est caractéristique de SeriLog. N’oubliez pas les capacités spécifiques de la solution de journalisation que vous avez choisie. Trois événements sont générés : un événement de niveau débogage et deux événements de niveau informationnel, dont le second effectue le suivi de la durée de la demande. Avec la configuration d’EventFlow ci-dessus, l’événement de niveau débogage ne doit pas être diffusé en flux vers la sortie.

```csharp
    Stopwatch sw = Stopwatch.StartNew();

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        sw.Restart();

        // Delay a random interval to provide a more interesting request duration.
        await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
    }
```

Pour afficher les événements dans Application Insights, ouvrez le portail Azure et accédez à votre ressource Application Insights. Cliquez ensuite sur Rechercher en haut à gauche pour afficher les événements.

![Vue de recherche des événements dans Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.png)

Les traces se trouvent en bas de l’image. Vous pouvez constater que nous ne disposons que de deux événements. L’événement de niveau débogage a été supprimé par EventFlow. À quoi l’entrée de demande au-dessus de la trace correspond-elle ? Il s’agit de la troisième ligne d’instrumentation « _logger » qui indique que l’événement a été converti en mesure de demande dans Application Insights. Revenons à la définition du filtre, dont le type est « metadata ». Cela indique qu’un événement affiche une propriété « RequestName » avec la valeur « MyRequest », et qu’une autre propriété, « Durée », contient la durée de la demande en millisecondes. Voici ce que vous voyez dans l’événement de demande au sein d’Application Insights. La même approche fonctionne pour toutes les entrées EventFlow prises en charge, y compris EventSource.

Si le cluster est un cluster autonome qui ne peut pas être connecté à une solution basée sur le cloud pour des raisons stratégiques, EventFlow prend en charge la recherche élastique en tant que sortie, mais d’autres sorties peuvent être écrites et les demandes d’extraction sont encouragées. Certains fournisseurs tiers de journalisation ASP.NET Core proposent également des solutions prenant en charge les installations locales.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Rapports d’intégrité et de charge Azure Service Fabric

Service Fabric a son propre modèle de contrôle d’intégrité, détaillé dans plusieurs articles
- [Présentation du contrôle d’intégrité de Service Fabric](service-fabric-health-introduction.md)
- [Signaler et contrôler l’intégrité du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Ajout de rapports d’intégrité Service Fabric personnalisés](service-fabric-report-health.md)
- [Affichage rapports d’intégrité de Service Fabric](service-fabric-view-entities-aggregated-health.md)

Le contrôle d’intégrité est essentiel à plusieurs aspects de l’exploitation d’un service. Surtout, lorsque Service Fabric effectue la mise à niveau d’une application nommée, une fois que chaque domaine de mise à niveau du service est mis à niveau et est accessible à vos clients, les contrôles d’intégrité doivent être réussis avant la mise à niveau de domaine suivante. S’il est impossible de proposer une intégrité correcte, le déploiement est restauré afin de laisser l’application dans un état correct connu. Bien que certains clients aient été affectés avant la restauration des services, la plupart des clients n’ont rencontré aucun problème. En outre, la résolution s’est effectuée assez rapidement, sans nécessiter d’intervention humaine. Plus les contrôles d’intégrité sont incorporés dans le code, plus le service est résilient face aux problèmes de déploiement.

L’intégrité du service inclut un autre aspect : le rapport de mesures à partir du service. Les mesures sont importantes dans Service Fabric, car elles permettent d’équilibrer l’utilisation des ressources et peuvent servir d’indicateur d’intégrité du système. Supposons que votre application contienne de nombreux services et que chaque instance rapporte une mesure de demandes par seconde (RPS). Si l’un des services utilise plus de ressources qu’un autre service, Service Fabric déplace les instances de service autour du cluster afin de maintenir une utilisation homogène des ressources. L’article [Gestion de la consommation des ressources et des charges dans Service Fabric à l’aide de mesures](service-fabric-cluster-resource-manager-metrics.md) explique ce fonctionnement en détail.

Les mesures donnent également des informations sur les performances de votre service et, au fil du temps, elles peuvent être utilisées pour vérifier le bon fonctionnement du service selon les paramètres prévus. Par exemple, si les tendances indiquent que le nombre moyen de demandes par seconde s’élève à 1 000 le lundi à 9h00, vous pouvez configurer un rapport d’intégrité qui envoie une alerte si le RPS est inférieur à 500 ou supérieur à 1 500. Tout peut se passer parfaitement bien, mais il peut être intéressant de vérifier la qualité de l’expérience dont bénéficient vos clients. Votre service a la possibilité de définir un ensemble de mesures pouvant être signalées dans le cadre de contrôles d’intégrité, mais cela n’affecte pas l’équilibre des ressources du cluster, le poids de mesure étant fixé à zéro. Nous vous recommandons de démarrer toutes les mesures avec un poids égal à zéro et de ne pas augmenter cette valeur avant d’être certain de bien comprendre l’impact sur l’équilibrage des ressources de votre cluster.

> [!TIP]
> Veillez à ne pas avoir trop de mesures pondérées. Il peut être difficile de comprendre pourquoi les instances de service sont déplacées, et quelques mesures peuvent jouer un grand rôle !

Les rapports de mesures et d’intégrité peuvent prendre en considération tout ce qui peut donner des informations sur l’intégrité et les performances de votre application. Un compteur de performances du processeur peut vous indiquer le degré d’intégrité de votre nœud, mais il ne précise pas véritablement si un service particulier est intègre ou non, car plusieurs services peuvent s’exécuter sur un seul nœud. En revanche, une mesure comme la valeur RPS, les éléments traités ou la latence des demandes peut donner des informations sur l’intégrité d’un service spécifique.

Pour effectuer un rapport d’intégrité, ajoutez un code du type

```csharp
 if (!result.HasValue)
 {
     HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
     this.Partition.ReportInstanceHealth(healthInformation);
 }
```

Pour rapporter une mesure, ajoutez un code semblable à ce qui suit dans votre service

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

## <a name="watchdogs"></a>Agents de surveillance

Un agent de surveillance est un service distinct qui peut surveiller l’intégrité et la charge des services, et effectuer un rapport d’intégrité pour n’importe quel élément de la hiérarchie du modèle d’intégrité. Cela permet d’éviter des erreurs qui ne seraient pas détectées à partir de l’affichage d’un service unique. Les agents de surveillance sont également un bon moyen d’héberger le code capable d’effectuer des actions de correction dans des conditions connues et sans intervention humaine.

## <a name="visualization-analysis-and-alerting"></a>Visualisation, analyse et alerte

La dernière partie de la surveillance consiste en la visualisation du flux d’événements, la création de rapports sur les performances de service et à la génération d’alertes en cas de détection d’un problème. Plusieurs solutions sont utilisées à cet égard. Application Insights et OMS peuvent servir aux alertes basées sur le flux d’événements. PowerBI ou une solution tierce comme [Kibana](https://www.elastic.co/products/kibana) ou [Splunk](https://www.splunk.com/) peuvent servir à la visualisation des données.

## <a name="next-steps"></a>Étapes suivantes

* [Collecte des journaux avec Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md)
* [Collecter les journaux directement à partir d’un processus de service Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Gestion de la consommation des ressources et des charges dans Service Fabric à l’aide de mesures](service-fabric-cluster-resource-manager-metrics.md)




<!--HONumber=Feb17_HO1-->


