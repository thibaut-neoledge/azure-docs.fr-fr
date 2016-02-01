<properties
   pageTitle="Prise en main de Reliable Actors | Microsoft Azure"
   description="Ce didacticiel vous guide dans les étapes de création, de débogage et de déploiement d'un service HelloWorld canonique à l'aide des acteurs fiables Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Acteurs fiables : scénario canonique HelloWorld étape par étape
Cet article explique les notions de base d’Azure Service Fabric Reliable Actors et vous présente la création, le débogage et le déploiement d’une application HelloWorld simple dans Visual Studio.

## Installation et configuration
Avant de commencer, assurez-vous d’avoir configuré l’environnement de développement Service Fabric sur votre ordinateur. Si vous devez le configurer, consultez les instructions détaillées sur la [configuration de l’environnement de développement](service-fabric-get-started.md).

## Concepts de base
Pour prendre en main Reliable Actors, vous devez comprendre seulement quatre concepts de base :

* **Service d’acteur**. Les entités Reliable Actors sont empaquetées dans des services qui peuvent être déployés dans l’infrastructure Service Fabric. Un service peut héberger un ou plusieurs acteurs. Nous verrons plus loin plus en détail les avantages d’avoir un ou plusieurs acteurs par service. Pour l’instant, supposons que nous devons implémenter un seul acteur.
* **Interface d’acteur**. L’interface d’acteur est utilisée pour définir l’interface publique d’un acteur. Dans la terminologie du modèle Reliable Actor, l’interface d’acteur définit les types de messages que l’acteur peut comprendre et traiter. L’interface d’acteur est utilisée par d’autres acteurs et applications clientes pour « envoyer » (de façon asynchrone) des messages à l’acteur. Reliable Actors peut implémenter plusieurs interfaces. Comme nous le verrons, un acteur HelloWorld peut implémenter l’interface IHelloWorld, mais également une interface ILogging qui définit différents messages et/ou fonctionnalités.
* **Enregistrement d’acteur**. Dans le service Reliable Actors, le type d’acteur doit être enregistré. De cette façon, Service Fabric est conscient du nouveau type et peut l’utiliser pour créer des acteurs.
* **Classe ActorProxy**. La classe ActorProxy est utilisée pour effectuer une liaison à un acteur et appeler les méthodes exposées par le biais de ses interfaces. La classe ActorProxy fournit deux fonctionnalités importantes :
	* Elle résout les noms. Elle est en mesure de localiser l’acteur dans le cluster (rechercher le nœud du cluster dans lequel il est hébergé).
	* Elle gère les échecs. Elle peut retenter les appels de méthode et déterminer de nouveau l’emplacement de l’acteur après une défaillance qui requiert le déplacement de l’acteur vers un autre nœud du cluster, par exemple.

## Création d'un projet dans Visual Studio
Après avoir installé Service Fabric Tools pour Visual Studio, vous pouvez créer des types de projet. Les nouveaux types de projet se trouvent sous la catégorie **Cloud** de la boîte de dialogue **Nouveau projet**.


![Outils Service Fabric pour Visual Studio - nouveau projet][1]

Dans la boîte de dialogue suivante, vous pouvez choisir le type de projet que vous souhaitez créer.

![Modèles de projets Service Fabric][5]

Pour le projet HelloWorld, nous allons utiliser le service Service Fabric Reliable Actors.

Une fois que vous avez créé la solution, la structure suivante doit s’afficher :

![Structure d’un projet Service Fabric][2]

## Blocs de construction de base des acteurs fiables

Une solution Reliable Actors standard est composée de trois projets :

* **Le projet d’application (HelloWorldApplication)**. Il s’agit du projet qui regroupe tous les services pour le déploiement. Il contient **ApplicationManifest.xml** et les scripts PowerShell pour gérer l’application.

* **Le projet d’interface (HelloWorld.Interfaces)**. Il s'agit du projet qui contient la définition d'interface de l'acteur. Dans le projet HelloWorld.Interfaces, vous pouvez définir les interfaces qui seront utilisées par les acteurs de la solution.

```csharp

namespace MyActor.Interfaces
{
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;

    public interface IMyActor : IActor
    {
        Task<string> HelloWorld();
    }
}

```

* **Le projet de service (HelloWorld)**. Il s'agit du projet utilisé pour définir le service Service Fabric qui va héberger l'acteur. Il contient du code réutilisable qui n’a pas besoin d’être modifié dans la plupart des cas (ServiceHost.cs), ainsi que l’implémentation de l’acteur. L’implémentation de l’acteur implique l’implémentation d’une classe qui dérive d’un type de base (Acteur). Il implémente également les interfaces qui sont définies dans le projet HelloWorld.Interfaces.

```csharp

namespace MyActor
{
    using System;
    using System.Threading.Tasks;
    using Interfaces;
    using Microsoft.ServiceFabric.Actors;

    internal class MyActor : StatelessActor, IMyActor
    {
        public Task<string> HelloWorld()
        {
            throw new NotImplementedException();
        }
    }
}

```

Le projet de service Reliable Actors contient le code pour créer un service Service Fabric. Dans la définition de service, le type ou les types d’acteur sont inscrits et peuvent ainsi servir à instancier de nouveaux acteurs.

```csharp

namespace MyActor
{
    using System;
    using System.Fabric;
    using System.Threading;
    using Microsoft.ServiceFabric.Actors;

    internal static class Program
    {
        private static void Main()
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {
                    fabricRuntime.RegisterActor<MyActor>();

                    Thread.Sleep(Timeout.Infinite);  // Prevents this host process from terminating so services keeps running.
                }
            }
            catch (Exception e)
            {
                ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}

```

Si vous démarrez à partir d’un nouveau projet dans Visual Studio et que vous n’avez qu’une seule définition d’acteur, l’enregistrement est inclus par défaut dans le code généré par Visual Studio. Si vous définissez d’autres acteurs dans le service, vous devez ajouter l’enregistrement des acteurs à l’aide de ce qui suit :

```csharp

fabricRuntime.RegisterActor<MyActor>();


```

## Débogage

Service Fabric Tools pour Visual Studio prend en charge le débogage sur votre ordinateur local. Vous pouvez démarrer une session de débogage en appuyant sur la touche F5. Visual Studio génère (si nécessaire) des packages. En outre, il déploie l’application sur le cluster Service Fabric local et attache le débogueur. Le processus est le même que pour déboguer une application ASP.NET.

Pendant le processus de déploiement, vous pouvez afficher la progression dans la fenêtre **Sortie**.

![Fenêtre de sortie de débogage Service Fabric][3]


## Étapes suivantes

- [Présentation des Acteurs fiables Service Fabric](service-fabric-reliable-actors-introduction.md)
- [Documentation de référence sur les API avec acteurs](https://msdn.microsoft.com/library/azure/dn971626.aspx)
- [Exemple de code](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=AcomDC_0121_2016-->