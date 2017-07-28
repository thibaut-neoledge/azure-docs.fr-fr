---
title: "Créer votre premier microservice Azure fiable en Java | Microsoft Docs"
description: "Introduction à la création d'une application Microsoft Azure Service Fabric avec des services avec et sans état."
services: service-fabric
documentationcenter: java
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 35d7f6d655f53891c00ca6c736c8c12b99fdc5ef
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017


---
# <a name="get-started-with-reliable-services"></a>Prise en main de Reliable Services
> [!div class="op_single_selector"]
> * [C# sur Windows](service-fabric-reliable-services-quick-start.md)
> * [Java sur Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Cet article explique les notions de base d’Azure Service Fabric Reliable Services et vous guide pas à pas dans la création et le déploiement d’une application Reliable Service simple écrite en Java. Cette vidéo Microsoft Virtual Academy vous montre également comment créer un service Reliable sans état :<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>Installation et configuration
Avant de commencer, assurez-vous que l’environnement de développement Service Fabric est configuré sur votre ordinateur.
Si vous devez le configurer, référez-vous à l’article sur la [prise en main sur Mac](service-fabric-get-started-mac.md) ou sur la [prise en main sur Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Concepts de base
Pour prendre en main Reliable Services, il vous suffit de comprendre quelques concepts de base :

* **Type de service** : il s’agit de l’implémentation de votre service. Elle est définie par la classe que vous écrivez qui étend `StatelessService` et tout autre code ou dépendances utilisés ici, ainsi qu’un nom et un numéro de version.
* **Instance de service nommée** : pour exécuter votre service, vous créez des instances nommées de votre type de service, de la même manière que vous créez des instances d’objet d’un type de classe. Les instances de service sont en fait des instanciations d’objet de votre classe de service que vous écrivez.
* **Hôte de service** : les instances de service nommées que vous créez doivent s’exécuter au sein d’un ordinateur hôte. L’hôte de service est simplement un processus dans lequel les instances de votre service peuvent s’exécuter.
* **Inscription du service** : l’inscription rassemble tous les éléments. Le type de service doit être inscrit auprès du runtime Service Fabric dans un hôte de service pour autoriser Service Fabric à créer des instances de ce type à exécuter.  

## <a name="create-a-stateless-service"></a>Création d'un service sans état
Commencez par créer une application Service Fabric. Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur Yeoman qui assure la génération de modèles automatique pour une application Service Fabric avec un service sans état. Commencez par exécuter la commande Yeoman suivante :

```bash
$ yo azuresfjava
```

Suivez les instructions pour créer un **service sans état fiable**. Pour ce didacticiel, nommez l’application « HelloWorldApplication » et le service « HelloWorld ». Le résultat comprend des répertoires pour `HelloWorldApplication` et `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Mettre en œuvre le service
Ouvrez **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Cette classe définit le type de service et peut exécuter n’importe quel code. L'API de service fournit deux points d'entrée pour votre code :

* Une méthode de point d’entrée de durée indéterminée, appelée `runAsync()`, avec laquelle vous pouvez commencer l’exécution de toute charge de travail, y compris les charges de travail de calcul de longue durée.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Un point d’entrée de communication où vous pouvez connecter la pile de communication de votre choix. C’est là que vous pouvez commencer à recevoir des demandes des utilisateurs et des autres services.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Dans ce didacticiel, nous nous concentrons sur la méthode de point d’entrée `runAsync()`. C’est là que vous pouvez commencer immédiatement à exécuter votre code.

### <a name="runasync"></a>RunAsync
La plateforme appelle cette méthode quand une instance d’un service est placée et prête à être exécutée. Pour un service sans état, cela signifie simplement le moment où l’instance de service est ouverte. Un jeton d'annulation est fourni pour coordonner lorsque l'instance de service doit être fermée. Dans Service Fabric, ce cycle d’ouverture/fermeture d’une instance de service peut se produire plusieurs fois au cours de la durée de vie de votre service dans son ensemble. Il existe diverses raisons à cela, notamment :

* Le système déplace vos instances de service à des fins d’équilibrage des ressources.
* Des erreurs surviennent dans votre code.
* L’application ou le système sont mis à niveau.
* Le matériel sous-jacent tombe en panne.

Cette orchestration est gérée par Service Fabric afin de maintenir une haute disponibilité et un équilibrage correct pour votre service.

`runAsync()` ne doit pas se bloquer de façon synchrone. Votre implémentation de runAsync doit renvoyer un objet CompletableFuture pour permettre au runtime de continuer si votre charge de travail a besoin d’implémenter une tâche longue qui doit être effectuée à l’intérieur de l’objet CompletableFuture.

#### <a name="cancellation"></a>Annulation
L'annulation de votre charge de travail est un effort conjoint orchestré par le jeton d'annulation fourni. Le système attend la fin de la tâche (suite à sa réussite, à son annulation ou à une défaillance) avant de poursuivre. Il est important de respecter le jeton d’annulation, de terminer le travail et de quitter `runAsync()` aussi rapidement que possible quand le système demande une annulation. L’exemple suivant montre comment gérer un événement d’annulation :

```java
    @Override
    protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

        // TODO: Replace the following sample code with your own logic
        // or remove this runAsync override if it's not needed in your service.

        CompletableFuture.runAsync(() -> {
          long iterations = 0;
          while(true)
          {
            cancellationToken.throwIfCancellationRequested();
            logger.log(Level.INFO, "Working-{0}", ++iterations);

            try
            {
              Thread.sleep(1000);
            }
            catch (IOException ex) {}
          }
        });
    }
```

### <a name="service-registration"></a>Inscription du service
Les types de service doivent être inscrits auprès du runtime Service Fabric. Le type de service est défini dans le fichier `ServiceManifest.xml` et votre classe de service qui implémente `StatelessService`. L’inscription du service est réalisée dans le point d’entrée principal du processus. Dans cet exemple, le point d’entrée principal du processus est `HelloWorldServiceHost.java` :

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Exécution de l'application

La génération de modèles automatique Yeoman inclut un script Gradle permettant de créer l’application et des scripts bash permettant le déploiement et la suppression de l’application. Pour exécuter l’application, commencez par créer l’application avec Gradle :

```bash
$ gradle
```

Cela génère un package d’application Service Fabric qui peut être déployé à l’aide de l’interface de ligne de commande Azure Service Fabric.

### <a name="deploy-with-xplat-cli"></a>Déploiement avec l’interface de ligne de commande XPlat

Si vous utilisez la CLI XPlat, le script install.sh contient les commandes Azure CLI nécessaires pour déployer le package d’application. Exécutez le script install.sh pour déployer l’application.

```bash
$ ./install.sh
```

### <a name="deploy-with-azure-cli-20"></a>Déploiement avec Azure CLI 2.0

Si vous utilisez Azure CLI 2.0, consultez la documentation de référence sur la gestion d’un [cycle de vie des applications à l’aide d’Azure CLI 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md).

## <a name="related-articles"></a>Articles connexes

* [Prise en main de Service Fabric et d’Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Prise en main de l’interface de ligne de commande Service Fabric XPlat](service-fabric-azure-cli.md)

