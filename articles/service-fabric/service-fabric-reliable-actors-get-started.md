<properties
   pageTitle="Prise en main des acteurs fiables | Microsoft Azure"
   description="Ce didacticiel vous guide dans les étapes de création, de débogage et de déploiement d'un service HelloWorld canonique à l'aide des acteurs fiables Service Fabric."
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
   ms.date="08/05/2015"
   ms.author="claudioc"/>


# Acteurs fiables : scénario canonique HelloWorld étape par étape
Cet article explique les notions de base des acteurs fiables Service Fabric et vous présente la création, le débogage et le déploiement d'une application HelloWorld simple dans Visual Studio.

## Installation et configuration
Avant de commencer, assurez-vous d'avoir configuré l'environnement de développement Service Fabric sur votre ordinateur. Des instructions détaillées sur la configuration de l'environnement de développement sont disponibles [ici](service-fabric-get-started.md).

## Concepts de base
Pour prendre en main les acteurs fiables, il vous suffit de comprendre 4 concepts de base :

* **Service d'acteur**. Les acteurs fiables sont empaquetés dans des services qui peuvent être déployés dans l'infrastructure Service Fabric. Un service peut héberger un ou plusieurs acteurs. Nous verrons plus tard plus en détail les avantages d'avoir un ou plusieurs acteurs par service. Pour l'instant, supposons que nous devons implémenter un seul acteur.
* **Interface d'acteur**. L'interface d'acteur est utilisée pour définir l'interface publique d'un acteur. Dans la terminologie des modèles d'acteur, elle définit le type de messages dont l'acteur est en mesure de comprendre le processus. L'interface d'acteur est utilisée par d'autres acteurs ou applications clientes pour « envoyer » (de façon asynchrone) des messages à l'acteur. Les acteurs fiables peuvent implémenter plusieurs interfaces, comme nous le verrons, un acteur HelloWorld peut implémenter l'interface IHelloWorld, mais également une interface ILogging qui définit différents messages/fonctionnalités.
* **Enregistrement d'acteur**. Dans le service d'acteur, le type d'acteur doit être enregistré pour que Fabric Service ait connaissance du nouveau type et puisse l'utiliser pour créer de nouveaux acteurs.
* **Classe ActorProxy**. La classe ActorProxy est utilisée pour effectuer une liaison à un acteur et appeler les méthodes exposées via ses interfaces. La classe ActorProxy fournit deux fonctionnalités importantes :
	* Résolution de noms : elle est en mesure de localiser l'acteur dans le cluster (rechercher le nœud du cluster dans lequel il est hébergé).
	* Gérer les échecs : elle peut retenter les appels de méthode et redéterminer l'emplacement de l'acteur après une défaillance qui requiert le déplacement de l'acteur vers un autre nœud du cluster, par exemple.

## Création d'un projet dans Visual Studio
Après avoir installé Service Fabric Tools pour Visual Studio, vous pouvez créer des types de projet. Les nouveaux types de projet se trouvent sous la catégorie « Cloud » de la boîte de dialogue Nouveau projet


![Outils Service Fabric pour VS - Nouveau projet][1]

Dans la boîte de dialogue suivante, vous pouvez choisir le type de projet que vous souhaitez créer.

![Modèles de projets Service Fabric][5]

Pour le projet HelloWorld, nous allons utiliser le service Service Fabric Actor.

Une fois la solution créée, la structure suivante doit s'afficher :

![Structure d’un projet Service Fabric][2]

## Blocs de construction de base des acteurs fiables

Une solution standard d'acteurs fiables est composée de trois projets :

* Le projet d'application (HelloWorldApplication). Il s'agit du projet qui regroupe tous les services pour le déploiement. Il contient ApplicationManifest.xml et les scripts PowerShell pour gérer l'application.

* Le projet d'interface (HelloWorld.Interfaces). Il s'agit du projet qui contient la définition d'interface de l'acteur. Dans le projet d'interface, vous pouvez définir les interfaces qui seront utilisées par les acteurs de la solution.

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld.Interfaces
{
    public interface IHelloWorld : IActor
    {
        Task<string> SayHello(string greeting);
    }
}

```

* Le projet de service (HelloWorld). Il s'agit du projet utilisé pour définir le service Service Fabric qui va héberger l'acteur. Il contient du code réutilisable qui n'a pas besoin d'être modifié dans la plupart des cas (ServiceHost.cs) ainsi que l'implémentation de l'acteur. L'implémentation de l'acteur implique l'implémentation d'une classe qui dérive d'un type de base (acteur) et implémente les interfaces définies dans le projet .Interfaces.

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using HelloWorld.Interfaces;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld
{
    public class HelloWorld : Actor, IHelloWorld
    {
        public Task<string> SayHello(string greeting)
        {
            return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
        }
    }
}

```

Le projet de service d'acteur contient le code pour créer un service Service Fabric, dans la définition de service, les types d'acteur sont enregistrés pour permettre d'instancier les nouveaux acteurs.

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(HelloWorld));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
}  

```

Si vous démarrez à partir d'un nouveau projet dans Visual Studio et que vous n'avez qu'une seule définition d'acteur, l'enregistrement est inclus par défaut dans le code généré par Visual Studio. Si vous définissez d'autres acteurs dans le service, vous devez ajouter l'enregistrement des acteurs à l'aide de ce qui suit :

```csharp

fabricRuntime.RegisterActor(typeof(MyNewActor));


```

## Débogage

Service Fabric Tools pour Visual Studio prend en charge le débogage sur l'ordinateur local. Vous pouvez démarrer une session de débogage en appuyant sur F5. Visual Studio génère (si nécessaire) des packages, déploie l'application sur le cluster Service Fabric local et attache le débogueur. Le processus est le même que pour déboguer une application ASP.NET. Pendant le processus de déploiement, vous pouvez afficher la progression dans la fenêtre Sortie

![Fenêtre de sortie de débogage Service Fabric][3]


## Étapes suivantes

[Présentation des Acteurs fiables Service Fabric](service-fabric-reliable-actors-introduction.md) [Documentation de référence sur les API avec acteurs](https://msdn.microsoft.com/library/azure/dn971626.aspx) [Exemple de code](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=August15_HO6-->