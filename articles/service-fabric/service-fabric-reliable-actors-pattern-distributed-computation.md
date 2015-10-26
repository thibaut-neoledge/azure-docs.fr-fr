<properties
   pageTitle="Modèle de calcul distribué Acteurs fiables"
   description="Les Acteurs fiables Service Fabric constituent une solution parfaitement adaptée grâce à la messagerie asynchrone parallèle, l'état distribué facile à gérer et le calcul parallèle."
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
   ms.date="09/08/2015"
   ms.author="vturecek"/>

# Modèle de conception Acteurs fiables : calcul distribué
Ce modèle nous a été inspiré en partie par une situation réelle dans laquelle un client avait réussi à effectuer un calcul financier dans Acteurs fiables Service Fabric en un temps record. Pour être plus précis, il s'agissait d'une simulation de calcul de risque de type Monte Carlo.

Au premier abord, en particulier pour ceux qui ne maîtrisent pas ce domaine spécifique, la gestion de ce type de charge de travail par Azure Service Fabric peut ne pas sembler évidente par rapport à des approches plus traditionnelles comme Map/Reduce ou MPI.

Mais il s'avère qu'Azure Service Fabric est une solution parfaitement adaptée grâce à sa messagerie asynchrone parallèle, son état distribué facile à gérer et son calcul parallèle, comme le montre le diagramme suivant :

![][1]

Dans l'exemple suivant, nous calculons simplement la valeur Pi à l'aide d'une simulation de Monte Carlo. vous disposez des acteurs suivants :

* Processor, responsable du calcul de Pi à l'aide de PoolTask Actors.

* PoolTask, responsable de la simulation de Monte Carlo et de l'envoi des résultats vers l'agrégation.

* Agrégation, responsable de l'agrégation des résultats et de l'envoi au finaliseur.

* Finaliseur, responsable du calcul du résultat final et de l'impression à l'écran.

## Exemple de code de calcul distribué : simulation de Monte Carlo

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : Actor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        ActorId aggregatorId = null;
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(ActorId.NewId()); // stateless
            if (i % 2 == 0) // new aggregator for every 2 pooled actors
               aggregatorId = ActorId.NewId();
            tasks.Add(task.CalculateAsync(tries, seed, aggregatorId));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed, ActorId aggregatorId);
}

public class PooledTask : Actor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed, ActorId aggregatorId)
    {
        var pi = new Pi()
        {
            InCircle = 0,
            Tries = tries
        };

        var random = new Random(seed);
        double x, y;
        for (int i = 0; i < tries; i++)
        {
            x = random.NextDouble();
            y = random.NextDouble();
            if (Math.Sqrt(x * x + y * y) <= 1)
                pi.InCircle++;
        }

        var agg = ActorProxy.Create<IAggregator>(aggregatorId);
        return agg.AggregateAsync(pi);
    }
}
```

Un moyen courant d'agréger les résultats dans Azure Service Fabric consiste à utiliser des minuteries. Nous utilisons des acteurs sans état pour deux raisons principales : le runtime décidera du nombre d'agrégateurs nécessaires dynamiquement, et nous fournira par conséquent une échelle à la demande ; et il instanciera ces acteurs « localement » – en d'autres termes, dans le même silo de l'acteur appelant, ce qui réduit les tronçons réseau. Voici à quoi ressemblent l'agrégation et le finaliseur :

## Exemple de code de calcul distribué : agrégation

```csharp
public interface IAggregator : IActor
{
    Task AggregateAsync(Pi pi);
}

[DataContract]
class AggregatorState
{
    [DataMember]
    public Pi _pi;
    [DataMember]
    public bool _pending;
}

public class Aggregator : Actor<AggregatorState>, IAggregator
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi() { InCircle = 0, Tries = 0 };
        State._pending = false;

        this.RegisterTimer(
            ProcessAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessAsync(object obj)
    {
        if (false == _pending)
            return;

        var finaliser = ActorProxy.Create<IFinaliser>(0);
        await finaliser.FinaliseAsync(_pi);
        State._pending = false;
        State._pi.InCircle = 0;
        State._pi.Tries = 0;
    }

    public Task AggregateAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        State._pending = true;
        return TaskDone.Done;
    }
}

public interface IFinaliser : IActor
{
    Task FinaliseAsync(Pi pi);
}

[DataContract]
class FinalizerState
{
    [DataMember]
    public Pi _pi;
}

public class Finaliser : Actor<FinalizerState>, IFinaliser
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi()
        {
            InCircle = 0,
            Tries = 0
        };

        return base.OnActivateAsync();
    }

    public Task FinaliseAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        Console.WriteLine(" Pi = {0:N9}  T = {1:N0}, {2}",(double)State._pi.InCircle / (double)State._pi.Tries * 4.0, State._pi.Tries, State._pi.InCircle);

        return TaskDone.Done;
    }
}
```

À ce stade, nous devrions avoir une idée précise de la façon d'améliorer l'exemple du classement avec une agrégation en termes d'échelle et de performances.

Nous n'affirmons aucunement qu'Azure Service Fabric est la solution de remplacement idéale pour d'autres calculs distribués de structures de données massives ou pour le calcul hautes performances. Il est juste capable de gérer certaines opérations mieux que d'autres. Toutefois, il est possible de modéliser des flux de travail et un calcul parallèle distribué dans Azure Service Fabric en profitant des mêmes avantages en termes de simplicité.

## Étapes suivantes
[Modèle : Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modèle : Réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modèle : Gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modèle : composition d'un service avec état](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modèle : Internet des Objets](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

[Introduction à Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=Oct15_HO3-->