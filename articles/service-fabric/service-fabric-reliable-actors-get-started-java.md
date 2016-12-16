---
title: Prise en main de Service Fabric Reliable Actors | Microsoft Docs
description: "Ce didacticiel vous guide dans les étapes de création, de débogage et de déploiement d’un service simple basé sur acteur à l’aide de Service Fabric Reliable Actors."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d31dc8ab-9760-4619-a641-facb8324c759
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3e349d0c76078889b6e41340ee8bb2f599819ba3


---
# <a name="getting-started-with-reliable-actors"></a>Prise en main de Reliable Actors
> [!div class="op_single_selector"]
> * [C# sur Windows](service-fabric-reliable-actors-get-started.md)
> * [Java sur Linux](service-fabric-reliable-actors-get-started-java.md)
> 
> 

Cet article explique les notions de base d’Azure Service Fabric Reliable Actors et vous présente la création et le déploiement d’une application Reliable Actor simple dans Java.

## <a name="installation-and-setup"></a>Installation et configuration
Avant de commencer, assurez-vous que l’environnement de développement Service Fabric est configuré sur votre ordinateur.
Si vous devez le configurer, référez-vous à l’article sur la [prise en main sur Mac](service-fabric-get-started-mac.md) ou sur la [prise en main sur Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Concepts de base
Pour prendre en main Reliable Actors, il vous suffit de comprendre quelques concepts de base :

* **Service d’acteur**. Les entités Reliable Actors sont empaquetées dans des Reliable Services qui peuvent être déployés dans l’infrastructure Service Fabric. Les instances d’acteur sont activées dans une instance de service nommée.
* **Enregistrement d’acteur**. Comme avec les Reliable Services, un service Reliable Actor doit être enregistré avec le runtime Service Fabric. En outre, le type d’acteur doit être enregistré auprès du runtime de l’acteur.
* **Interface d’acteur**. L’interface d’acteur est utilisée pour définir une interface publique fortement typée d’un acteur. Dans la terminologie du modèle Reliable Actor, l’interface d’acteur définit les types de messages que l’acteur peut comprendre et traiter. L’interface d’acteur est utilisée par d’autres acteurs et applications clientes pour « envoyer » (de façon asynchrone) des messages à l’acteur. Reliable Actors peut implémenter plusieurs interfaces.
* **Classe ActorProxy**. La classe ActorProxy est utilisée par des applications clientes pour appeler les méthodes exposées par le biais de l’interface d’acteur. La classe ActorProxy fournit deux fonctionnalités importantes :
  
  * Résolution de noms : elle est en mesure de localiser l’acteur dans le cluster (en recherchant le nœud du cluster dans lequel il est hébergé).
  * Gestion des défaillances : elle peut retenter les appels de méthode et déterminer de nouveau l’emplacement de l’acteur après une défaillance qui, par exemple, nécessite le déplacement de l’acteur vers un autre nœud du cluster.

Les règles suivantes, qui se rapportent aux interfaces d’acteur, sont importantes :

* Les méthodes d'interface d'acteur ne peuvent pas être surchargées.
* Les méthodes d’interface d'acteur ne doivent pas avoir de paramètres de sortie, de paramètres de référence ou de paramètres facultatifs.
* Les interfaces génériques ne sont pas prises en charge.

## <a name="create-an-actor-service"></a>Création d’un service d’acteur
Commencez par créer une application Service Fabric. Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur Yeoman qui assure la génération de modèles automatique pour une application Service Fabric avec un service sans état. Commencez par exécuter la commande Yeoman suivante :

```bash
$ yo azuresfjava
```

Suivez les instructions pour créer un **service Reliable Actor**. Pour ce didacticiel, nommez l’application « HelloWorldActorApplication » et l’acteur « HelloWorldActor ». La structure suivante sera créée :

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Blocs de construction de base des acteurs fiables
Les concepts de base décrits précédemment se traduisent par les blocs de construction de base d’un service Reliable Actor.

### <a name="actor-interface"></a>Interface d’acteur
Elle contient la définition d'interface de l'acteur. Cette interface définit le contrat de l’acteur qui est partagé par l’implémentation de l’acteur et les clients appelant l’acteur, donc il est généralement justifié de le définir dans un endroit distinct de l’implémentation de l’acteur et qui peut être partagé par plusieurs autres services ou application clientes.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Service d’acteur
Il contient votre implémentation d’acteur et le code d’inscription de l’acteur. La classe d’acteur implémente l’interface d’acteur. Il s’agit de l’endroit dans quelquel votre acteur effectue son travail.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Enregistrement d’acteur
Le service de l’acteur doit être enregistré avec un type de service dans le runtime Service Fabric. Afin que le service de l’acteur exécute vos instances d’acteur, le type d’acteur doit également être enregistré auprès du Service de l’acteur. La méthode d’inscription `ActorRuntime` effectue ce travail pour les acteurs.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Test client
Il s’agit d’une application cliente de test simple que vous pouvez exécuter séparément à partir de l’application Service Fabric pour tester votre service d’acteur. Voici un exemple d’endroit où l’ActorProxy peut être utilisé pour activer et communiquer avec des instances d’acteur. Il n’est pas déployé avec votre service.

### <a name="the-application"></a>L'application
Enfin, les packages d’applications, le service d’acteur et tout autre service, que vous pouvez ajouter ensemble à l’avenir pour le déploiement. Elle contient le fichier *ApplicationManifest.xml* et des espaces réservés pour le package de service d’acteur.

## <a name="run-the-application"></a>Exécution de l'application
La structure Yeoman inclut un script Gradle pour générer l’application et des scripts Bash pour déployer l’application et annuler son déploiement. Pour exécuter l’application, commencez par créer l’application avec Gradle :

```bash
$ gradle
```

Cela génère un package d’application Service Fabric qui peut être déployé à l’aide de l’interface de ligne de commande Azure Service Fabric. Le script install.sh contient les commandes d’interface de ligne de commande Azure nécessaires pour déployer le package d’application. Exécutez simplement le script install.sh à déployer :

```bask
$ ./install.sh
```



<!--HONumber=Nov16_HO3-->


