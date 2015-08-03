<properties
   pageTitle="Comment les acteurs Fabric utilisent la plateforme Service Fabric"
   description="Cet article explique comment Fabric Actors utilise les fonctionnalités de la plateforme Service Fabric. Il traite les concepts de la plateforme Service Fabric du point de vue des développeurs d'acteur."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/21/2015"
   ms.author="abhisram"/>

# Comment les acteurs Fabric utilisent la plateforme Service Fabric

## Concepts de modèle d'application Service Fabric pour les acteurs
Les acteurs utilisent le modèle d'application Service Fabric pour gérer le cycle de vie de l'application. Chaque type d'acteur est mappé à un [type de Service](service-fabric-application-model.md#describe-a-service) Service Fabric. Le code de l'acteur est [empaqueté](service-fabric-application-model.md#package-an-application) comme une application Service Fabric et [déployé](service-fabric-deploy-remove-applications.md#deploy-an-application) sur le cluster.

Prenons l'exemple d'un projet d'acteur [créé à l'aide de Visual Studio](service-fabric-reliable-actors-get-started.md) afin d'illustrer certains des concepts ci-dessus.

Le manifeste de l'application, le manifeste de service et le fichier de configuration Settings.xml sont inclus dans le projet pour le service de l'acteur lorsque la solution est créée dans Visual Studio. Cette situation est présentée dans la capture d'écran ci-dessous.

![][1]

Le type et la version de l'application dans laquelle l’acteur est empaqueté figurent dans le manifeste de l’application inclus dans le projet pour le service de l'acteur. L'extrait suivant provenant d'un manifeste d'application en est un exemple.

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

Le type de service auquel le type d’acteur est mappé figure dans le manifeste de service inclus dans le projet pour le service de l’acteur. L'extrait suivant provenant d'un manifeste de service en est un exemple.

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

Lorsque le package de l’application est créé à l'aide de Visual Studio, les journaux de la fenêtre Build Output indiquent l'emplacement de ce package. Par exemple :

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

Voici une liste partielle de l'emplacement ci-dessus (liste complète omise par souci de concision) :

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───fr-fr
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───fr-fr
                    System.Fabric.Common.Internal.Strings.resources.dll

La liste ci-dessus indique les assemblys qui implémentent l'acteur VoicemailBox inclus dans le package de code au sein du package de service dans le package de l'application.

La solution Visual Studio inclut les scripts PowerShell utilisés pour déployer l'application et supprimer l'application du cluster. Les scripts sont entourés dans la capture d'écran ci-dessous.

![][2]

Les tâches de gestion suivantes (mises à niveau et suppression éventuelle) de l'application sont également effectuées à l'aide de mécanismes de gestion d’application Service Fabric. Pour plus d'informations, consultez les rubriques sur le [modèle d'application](service-fabric-application-model.md), le [déploiement et la suppression d'application](service-fabric-deploy-remove-applications.md), et la [mise à niveau de l'application](service-fabric-application-upgrade.md).

## Évolutivité des services d'acteur
Les administrateurs de cluster peuvent créer un ou plusieurs services d'acteur pour chaque type de service du cluster. Chacun de ces services d'acteur peut avoir une ou plusieurs partitions (similaires aux autres services d'infrastructure Service Fabric). La possibilité de créer plusieurs services d'un type de service (mappé à un type d'acteur) et la possibilité de créer plusieurs partitions pour un service permettent de faire évoluer l'application de l'acteur. Consultez l'article sur l'[évolutivité](service-fabric-concepts-scalability.md) pour plus d'informations.

> [AZURE.NOTE]Les services d’acteur sans état doivent avoir un nombre d’[instances](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) égal à 1. La présence de plus d’une instance d’un service d’acteur sans état dans une partition n’est pas prise en charge. Par conséquent, il n’est pas possible pour les services d’acteur sans état d’augmenter le nombre d'instances pour optimiser l’extensibilité. Ils doivent utiliser les options d'extensibilité décrites dans l’[article sur l’extensibilité](service-fabric-concepts-scalability.md).

## Concepts de partition Service Fabric pour les acteurs
L'ID acteur d'un acteur est mappé à une partition d'un service d'acteur. L'acteur est créé dans la partition à laquelle l'ID acteur est mappé. Lorsqu'un acteur est créé, le runtime Actors écrit un [événement EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) qui indique dans quelle partition cet acteur est créé. Voici un exemple de cet événement qui indique qu'un acteur avec l'ID `-5349766044453424161` a été créé dans la partition `0583c745-1bed-43b2-9545-29d7e3448156` du service `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService`, de l'application `fabric:/VoicemailBoxAdvancedApplication`.

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "0583c745-1bed-43b2-9545-29d7e3448156",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

Un autre acteur avec l'ID `-4952641569324299627` a été créé dans une autre partition `c146fe53-16d7-4d96-bac6-ef54613808ff` du même service, comme indiqué par l'événement ci-dessous.

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "c146fe53-16d7-4d96-bac6-ef54613808ff",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

*Remarque :* certains champs des événements ci-dessus sont omis par souci de concision.

L'ID de partition peut être utilisé pour obtenir d'autres informations relatives à la partition. Par exemple, l'outil [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) peut être utilisé pour afficher des informations sur la partition, le service et l'application auxquels il appartient. La capture d'écran suivante affiche des informations sur la partition `c146fe53-16d7-4d96-bac6-ef54613808ff`, qui contenait l'acteur avec l'ID `-4952641569324299627` dans l'exemple ci-dessus.

![][3]

Les acteurs peuvent obtenir de façon programmée l'ID partition, le nom du service, le nom de l'application et d'autres informations spécifiques à la plateforme Fabric Service via les membres `Host.ActivationContext` et `Host.StatelessServiceInitialization` ou `Host.StatefulServiceInitializationParameters` de la classe de base dont le type d'acteur est dérivé. L'extrait de code suivant montre un exemple :

```csharp
public void ActorMessage<TState>(Actor<TState> actor, string message, params object[] args)
{
    string finalMessage = string.Format(message, args);
    this.ActorMessage(
        actor.GetType().ToString(),
        actor.Id.ToString(),
        actor.Host.ActivationContext.ApplicationTypeName,
        actor.Host.ActivationContext.ApplicationName,
        actor.Host.StatefulServiceInitializationParameters.ServiceTypeName,
        actor.Host.StatefulServiceInitializationParameters.ServiceName.ToString(),
        actor.Host.StatefulServiceInitializationParameters.PartitionId,
        actor.Host.StatefulServiceInitializationParameters.ReplicaId,
        FabricRuntime.GetNodeContext().NodeName,
        finalMessage);
}
```

### Concepts de partition Service Fabric pour les acteurs sans état
Les acteurs sans état sont créés dans une partition d'un service sans état Service Fabric. L'ID acteur détermine dans quelle partition l'acteur est créé. Le nombre d’[instances](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) pour un service d’acteur sans état doit être égal à 1. La modification du nombre d'instances n'est pas prise en charge. Par conséquent, l'acteur est créé dans l'unique instance de service au sein de la partition.

> [AZURE.TIP]Le runtime Service Fabric émet certains [événements liés aux instances d'acteur sans état](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances). Ces événements sont utiles dans les diagnostics et la surveillance des performances.

Lors de la création d'un acteur sans état, le runtime Actors écrit un [événement EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) qui indique dans quelles partition et instance l'acteur est créé. Voici un exemple de cet événement qui indique qu'un acteur avec l'ID `abc` a été créé dans l'instance `130745709600495974` de la partition `8c828833-ccf1-4e21-b99d-03b14d4face3`, du service `fabric:/HelloWorldApplication/HelloWorldActorService`, de l'application `fabric:/HelloWorldApplication`.

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

*Remarque :* certains champs de l’événement ci-dessus sont omis par souci de concision.

### Concepts de partition Service Fabric pour les acteurs avec état
Les acteurs avec état sont créés dans une partition du service avec état Service Fabric. L'ID acteur détermine dans quelle partition l'acteur est créé. Chaque partition du service peut avoir un ou plusieurs [réplicas](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) placés sur différents nœuds du cluster. Avoir plusieurs réplicas garantit la fiabilité de l'état de l'acteur. Le gestionnaire de ressources optimise le positionnement en fonction des domaines de défaillance et de mise à niveau disponibles dans le cluster. Deux réplicas de la même partition ne sont jamais placés sur le même nœud. Les acteurs sont toujours créés dans le réplica principal de la partition à laquelle leur ID acteur est mappé.

> [AZURE.TIP]Le runtime Fabric Actors émet certains [événements liés aux réplicas d'acteur avec état](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas). Ces événements sont utiles dans les diagnostics et la surveillance des performances.

N'oubliez pas que dans l'[exemple VoiceMailBoxActor présenté précédemment](#service-fabric-partition-concepts-for-actors), l'acteur avec l'ID `-4952641569324299627` a été créé dans la partition `c146fe53-16d7-4d96-bac6-ef54613808ff`. L'événement EventSource tiré de cet exemple indique également que l'intervenant a été créé dans le réplica `130745418574851853` de cette partition. Il s'agissait du réplica principal de cette partition au moment de la création de l'acteur. La capture d'écran Service Fabric Explorer ci-dessous confirme ce point.

![][4]

## Choix du fournisseur de l'état d'acteur
Certains fournisseurs d'état d'acteur par défaut sont inclus dans le runtime Actors. Afin de choisir un fournisseur d'état approprié pour un service de l'acteur, il est nécessaire de comprendre comment les fournisseurs d'état utilisent les fonctionnalités de la plateforme Service Fabric sous-jacente pour rendre l'état d'acteur hautement disponible.

Par défaut, un acteur avec état utilise le fournisseur d'état de l'acteur du stockage clé-valeur. Ce fournisseur d'état repose sur le stockage clé-valeur distribué fourni par la plateforme Service Fabric. L'état est enregistré durablement sur le disque local du nœud qui héberge le [réplica](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) principal, et il est répliqué et enregistré durablement sur les disques locaux des nœuds hébergent les réplicas secondaires. L'état est enregistré uniquement lorsqu'un quorum de réplicas a validé l'état sur les disques locaux. Le stockage clé-valeur offre des fonctionnalités avancées de détection des incohérences comme une mauvaise progression, et les corrige automatiquement.

Le runtime Actors inclut également un `VolatileActorStateProvider`. Ce fournisseur d'état réplique l'état dans les réplicas, mais l'état reste en mémoire sur le réplica. Si un réplica s'arrête puis redevient opérationnel, son état est reconstitué à partir de l'autre réplica. Mais si tous les réplicas (copie de l'état) s'arrêtent simultanément, les données de l'état seront perdues. Par conséquent, ce fournisseur d'état est adapté aux applications où les données peuvent survivre à des échecs de quelques réplicas et survivre aux basculements planifiés, comme les mises à niveau. Si tous les réplicas (copies) sont perdus, les données doivent être recréées à l'aide de mécanismes externes à Service Fabric. Vous pouvez configurer votre acteur avec état pour utiliser un fournisseur d'état d'acteur volatile en ajoutant l'attribut `VolatileActorStateProvider` à la classe d'acteur ou un attribut de niveau assembly.

L'extrait de code suivant montre comment modifier tous les acteurs dans l'assembly qui ne dispose pas d'un attribut de fournisseur d'état explicite pour utiliser `VolatileActorStateProvider`.

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

L'extrait de code suivant montre comment modifier le fournisseur d'état pour un type d'acteur particulier, `VoicemailBox` devient `VolatileActorStateProvider`.

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Veuillez noter que la modification du fournisseur d'état nécessite la recréation du service d'acteur. Les fournisseurs d'état ne peuvent pas être modifiés dans le cadre de la mise à niveau de l'application.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png

<!---HONumber=July15_HO4-->