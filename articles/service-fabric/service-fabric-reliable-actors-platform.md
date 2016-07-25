<properties
   pageTitle="Reliable Actors dans Service Fabric | Microsoft Azure"
   description="Décrit comment les Reliable Actors sont superposés en couches sur des Reliable Services et utilisent les fonctionnalités de la plateforme Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="amanbha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# Comment le service Reliable Actors utilise la plateforme Service Fabric

Cet article décrit le fonctionnement du service Reliable Actors sur la plateforme Service Fabric. La solution Reliable Actors s’exécute dans une infrastructure hébergée dans une implémentation d’un service fiable avec état nommé *Service d’acteur*. Le service d’acteur contient tous les composants nécessaires pour gérer le cycle de vie et la distribution des messages destinés à votre acteurs :

 - Le runtime de l’acteur gère le cycle de vie et le nettoyage de la mémoire, et applique une accès monothread.
 - Un écouteur de communication à distance du service d’acteur accepte les appels d’accès à distance adressés aux acteurs, et les transmet à un répartiteur qui les route vers l’instance d’acteur appropriée.
 - Le fournisseur d’état de l’acteur encapsule des fournisseurs d’état (par exemple, le fournisseur d’état Collections fiables), et fournit un adaptateur pour la gestion de l’état de l’acteur.

Ces composants forment ensemble l’infrastructure d’acteur fiable.

## Couches de service

Étant donné que le service d’acteur est un service fiable, l’ensemble des concepts de services fiables (Reliable Services) relatifs au [modèle d’application](service-fabric-application-model.md), au cycle de vie, à l’[empaquetage](service-fabric-application-model.md#package-an-application), au [déploiement]((service-fabric-deploy-remove-applications.md#deploy-an-application), à la mise à niveau et à la mise à l’échelle s’appliquent aux services d’acteur.

![Superposition de service d’acteur][1]

Le diagramme ci-dessus montre la relation entre les infrastructures d’application de Service Fabric et le code utilisateur. Les éléments en bleu représentent l’infrastructure d’application de Reliable Services, ceux en orange l’infrastructure de Reliable Actors, et ceux en vert le code utilisateur.


Dans Reliable Services, votre service hérite de la classe `StatefulService`, elle-même dérivée de `StatefulServiceBase`. (ou `StatelessService` pour les services sans état). Dans Reliable Actors, vous utilisez le service d’acteur qui est une implémentation différente de la classe `StatefulServiceBase` qui implémente le modèle d’acteur dans lequel vos acteurs exécutent. Étant donné que le service d’acteur proprement dit est simplement une implémentation de `StatefulServiceBase`, vous pouvez écrire votre propre service qui dérive de `ActorService`, et implémenter des fonctionnalités au niveau du service de la même façon que si vous héritiez de `StatefulService`, par exemple :

 - sauvegarde et restauration du service ;
 - fonctionnalités partagées par tous les acteurs, tel un disjoncteur ;
 - communication à distance avec les appels de procédure sur le service d’acteur proprement dit, ainsi que sur chaque acteur.

### Utilisation du service d’acteur

Les instances d’acteur ont accès au service d’acteur dans lequel elles s’exécutent. Via le service d’acteur, les instances d’acteur peuvent obtenir par programme le contexte de service qui comprend l’ID de partition, le nom du service, le nom de l’application et d’autres informations spécifiques de la plateforme Service Fabric :

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```

Comme tous les services fiables, le service d’acteur doit être inscrit avec un type de service dans le runtime de Service Fabric. Afin que le service d’acteur exécute vos instances d’acteur, votre type d’acteur doit également être inscrit auprès du service d’acteur. La méthode d’inscription `ActorRuntime` effectue ce travail pour les acteurs. Dans le cas le plus simple, vous pouvez simplement enregistrer votre type d’acteur. Le service d’acteur avec les paramètres par défaut est alors utilisé de façon implicite :

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```  

Vous pouvez également utiliser une expression lambda fournie par la méthode d’inscription pour construire vous-même le service d’acteur. Cela vous permet de configurer le service d’acteur ainsi que de construire explicitement vos instances d’acteur, en y injectant des dépendances à votre acteur via son constructeur :

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

### Méthodes du service d’acteur

Le service d’acteur implémente `IActorService` qui implémente à son tour `IService`. Il s’agit de l’interface qu’utilise la communication à distance de Reliable Services, qui autorise des appels de procédure distante sur les méthodes de service. Elle contient les méthodes de niveau de service qui peuvent être appelées à distance à l’aide d’une communication à distance avec le service.


#### Énumération des acteurs

Le service d’acteur permet à un client d’énumérer des métadonnées sur les acteurs hébergés par le service. Étant donné que le service d’acteur est un service avec état partitionné, l’énumération est effectuée par partition. Étant donné que chaque partition peut contenir un grand nombre d’acteurs, l’énumération est renvoyée sous la forme d’un ensemble de résultats paginés. Les pages sont traitées en boucle jusqu’à ce qu’elles aient toutes été lues. L’exemple suivant montre comment créer une liste de tous les acteurs actifs dans une partition d’un service d’acteur :

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);
                
    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

#### Suppression d’acteurs

Le service d’acteur fournit également une fonction permettant de supprimer des acteurs :

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);
            
await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```

Pour plus d’informations sur la suppression d’acteurs et de leur état, voir la [documentation sur le cycle de vie des acteurs](service-fabric-reliable-actors-lifecycle.md).

### Service d’acteur personnalisé

À l’aide de l’expression lambda d’inscription de l’acteur, vous pouvez également inscrire votre propre service d’acteur personnalisé dérivé de `ActorService`, où vous pouvez implémenter vos propres fonctionnalités de niveau de service. Pour ce faire, vous devez écrire une classe de service qui hérite de `ActorService`. Un service d’acteur personnalisé hérite de toutes les fonctionnalités de runtime d’acteur d’`ActorService`. Vous pouvez l’utiliser pour implémenter vos propres méthodes de service.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```


#### Implémentation d’une sauvegarde et restauration d’acteur

 Dans l’exemple suivant, le service d’acteur personnalisé expose une méthode pour sauvegarder des données d’acteur en tirant parti de l’écouteur de communication à distance déjà présent dans `ActorService` :

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }
    
    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```

Dans cet exemple, `IMyActorService` est un contrat de communication à distance qui implémente `IService` et est ensuite implémenté par `MyActorService`. Suite à l’ajout de ce contrat de communication à distance, vous pouvez rendre les méthodes sur `IMyActorService` également disponibles pour un client en créant un proxy de communication à distance avec `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```


## Modèle d'application

Les services d’acteur étant des Reliable Services, le modèle d’application est le même. Toutefois, les outils de génération d’infrastructure d’acteur produisent bon nombre des fichiers de modèle d’application pour vous.

### Manifeste de service
 
Le contenu du fichier ServiceManifest.xml de votre service d’acteur est généré automatiquement par les outils de génération d’infrastructure d’acteur. notamment :

 - le type de service d’acteur ; Le nom du type est basé sur le nom de votre projet d’acteur. En fonction de l’attribut de persistance sur votre acteur, l’indicateur HasPersistedState est également défini en conséquence.
 - le package de code ;
 - le package de configuration ;
 - les ressources et points de terminaison.

### Manifeste d’application

Les outils de génération d’infrastructure d’acteur créent automatiquement une définition de service par défaut pour votre service d’acteur. Les propriétés de service par défaut sont définies par les outils de génération :

 - Le nombre de jeux de réplicas est déterminé par l’attribut de persistance sur votre acteur. À chaque modification de l’attribut de persistance sur votre acteur, le nombre de jeux de réplicas dans la définition de service par défaut est réinitialisé en conséquence.
 - La plage et le schéma de partition ont une valeur Int64 uniforme avec la plage complète de clés Int64.

## Concepts de partition Service Fabric pour les acteurs

Les services d’acteur sont des services partitionnés avec état. Chaque partition d’un service d’acteur contient un ensemble d’acteurs. Les partitions de service sont automatiquement distribuées sur plusieurs nœuds dans Service Fabric. Par conséquent, les instances d’acteur sont distribuées.

![Partitionnement et distribution d’acteur][5]

Vous pouvez créer des Reliable Services avec différents schémas de partition et plages de clés de partition. Le service d’acteur utilise le schéma de partitionnement Int64 avec la plage complète de clés Int64 pour mapper des acteurs à des partitions.

### ID d’acteur

Chaque acteur créé dans le service possède un ID unique associé, représenté par la classe `ActorId`. L’`ActorId` est une valeur d’ID opaque qui peut être utilisée pour une distribution uniforme des acteurs sur les partitions de service en générant des identifiants aléatoires :

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```

Chaque `ActorId` étant haché en valeur Int64, le service d’acteur doit utiliser un schéma de partitionnement Int64 avec la plage complète de clés Int64. Toutefois, vous pouvez utiliser des valeurs d’ID personnalisées pour un `ActorID`, dont des GUID, des chaînes et des valeurs Int64.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```

Lorsque vous utilisez des chaînes et des GUID, les valeurs sont hachées en Int64. Toutefois, lorsque vous fournissez explicitement une valeur Int64 à un `ActorId`, la valeur Int64 mappe directement à une partition sans hachage supplémentaire. Cela permet de contrôler les acteurs de partition qui sont placés.

## Étapes suivantes
 - [Gestion des états d’acteur](service-fabric-reliable-actors-state-management.md)
 - [Cycle de vie des acteurs et Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
 - [Documentation de référence de l’API Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemple de code](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)

 
<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

<!---HONumber=AcomDC_0713_2016-->