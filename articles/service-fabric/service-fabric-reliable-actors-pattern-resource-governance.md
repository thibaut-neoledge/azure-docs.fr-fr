<properties
   pageTitle="Modèle de conception de gestion des ressources pour les Acteurs fiables"
   description="Modèle de conception montrant comment utiliser les Acteurs fiables pour modéliser une application à mettre à l'échelle mais avec des ressources limitées"
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


# Modèle de conception Acteurs fiables : gestion des ressources
Ce modèle et les scénarios associés sont facilement identifiables par les développeurs (entreprises ou autres) qui disposent de ressources limitées sur site ou dans le cloud qu'ils ne peuvent pas mettre immédiatement à l'échelle, ou qui souhaitent transférer des applications et des données volumineuses vers le cloud.

Dans l'entreprise, ces ressources limitées, telles que des bases de données, s'exécutent sur du matériel avec une augmentation d'échelle. Toute personne disposant d'une longue expérience en entreprise sait qu'il s'agit d'une situation courante sur site. Même à l'échelle du cloud, nous avons vu cette situation se produire lorsqu'un service cloud a tenté de dépasser la limite de connexions 64K TCP entre un tuple adresse/port, ou lorsque vous tentez de vous connecter à une base de données en cloud qui limite le nombre de connexions simultanées.

En général, dans le passé, ce problème était résolu en utilisant des intergiciels à base de messages ou à l'aide de mécanismes personnalisés de regroupement et en façade. Ces situations sont difficiles à gérer, en particulier lorsque nous devons mettre à l'échelle le niveau intermédiaire tout en conservant le bon nombre de connexions. C'est un système fragile et complexe.

En fait, comme le modèle Smart Cache, ce modèle couvre plusieurs scénarios et s'adresse aux clients qui possèdent déjà de systèmes opérationnels avec des ressources limitées. Ils créent des systèmes où ils doivent faire monter en charge non seulement les services, mais également leur état en mémoire ainsi que l'état persistant dans un stockage stable.

Le diagramme ci-dessous illustre ce scénario :

![][1]

## Modélisation de scénarios de mise en cache avec des acteurs
Essentiellement, nous modélisons l'accès à des ressources avec un ou plusieurs acteurs qui agissent comme des proxies (une connexion, par exemple) vers une ressource ou un groupe de ressources. Vous pouvez ensuite gérer directement la ressource par le biais d'acteurs individuels ou utiliser un acteur de coordination qui gère les acteurs de la ressource. Pour plus de clarté, nous étudierons le besoin fréquent de devoir travailler sur un niveau de stockage partitionné pour des questions de performances et d'évolutivité. Notre première option est plutôt basique : nous pouvons utiliser une fonction statique pour mapper et résoudre nos acteurs aux ressources en aval. Une telle fonction peut retourner, par exemple, une chaîne de connexion avec un résultat donné. Nous pouvons librement choisir comment implémenter cette fonction. Bien entendu, cette approche présente ses propres inconvénients comme une affinité statique qui complique beaucoup le repartitionnement des ressources ou le remappage d'un acteur à des ressources. Voici un exemple très simple dans lequel nous utilisons une arithmétique modulaire pour déterminer le nom de la base de données en utilisant la valeur userID et une région pour identifier le serveur de la base de données.

## Exemple de code de gestion des ressources – résolution statique

```csharp
private static string _connectionString = "none";

private static string ResolveConnectionString(long userId, int region)
{
    if (_connectionString == "none")
    {
        // an example of static mapping
        _connectionString = String.Format("Server=SERVER_{0};Database=DB_{0}", region, userId % 4);
    }
    return _connectionString;
}
```

Simple mais pas très flexible. Examinons maintenant une approche plus avancée et plus utile. Tout d'abord, nous modélisons l'affinité entre des ressources physiques et des acteurs. Cette opération s'effectue via un acteur appelé Résolveur qui comprend le mappage entre les utilisateurs, les partitions logiques et les ressources physiques. Le résolveur conserve ses données dans un magasin persistant, mais elles sont mises en cache pour simplifier la recherche. Comme nous l'avons vu dans l'exemple du taux de change plus tôt dans le modèle Smart Cache, le résolveur peut récupérer de façon proactive les toutes dernières informations à l'aide d'un minuteur. Une fois que l'acteur utilisateur résout la ressource qu'il doit utiliser, il la met en cache dans une variable locale appelée \_resolution et l'utilise pendant sa durée de vie. Nous avons choisi une résolution basée sur la recherche (illustrée ci-dessous) plutôt qu'un hachage simple ou un hachage par plage en raison de la flexibilité qu'elle offre pour des opérations comme la montée/descente en charge ou le déplacement d'un utilisateur d'une ressource à une autre.

![][2]

Dans l'illustration ci-dessus, nous constatons que l'acteur B23 résout tout d'abord sa ressource (également appelée resolution) — DB1 puis la met en cache. Les opérations suivantes peuvent maintenant utiliser la résolution mise en cache pour accéder à la ressource limitée. Comme les acteurs prennent en charge une exécution dans un seul thread, les développeurs n'ont plus à se soucier de l'accès simultané à la ressource. Les acteurs User et Resolver ressemblent à ceci :

## Exemple de code de gestion des ressources – Résolveur

```csharp
public interface IUser : IActor
{
    Task UpdateProfile(string name, string country, int age);
}

[DataContract]
public class UserState
{
    [DataMember]
    private long _userId;
    [DataMember]
    private string _name;
    [DataMember]
    private string _country;
    [DataMember]
    private int _age;
    [DataMember]
    private Resolution _resolution;
}


public class User : Actor<UserState>, IUser
{
    public override async Task ActivateAsync()
    {
        State._userId = this.GetPrimaryKeyLong();
        var resolver = ActorProxy.Create<IResolver>(0);
        State._resolution = await resolver.ResolveAsync(State._userId);
        await base.ActivateAsync();
    }

    public Task UpdateProfile(string name, string country, int age)
    {
        Console.WriteLine("Using {0}", State._resolution.Resource.ConnectionString);
        // this is where we use the resource...
        return TaskDone.Done;
    }
}
```

Gestion des ressources – exemple de résolveur

```csharp
public interface IResolver : IActor
{
    Task<Resolution> ResolveAsync(long entity);
}

[DataContract]
public class ResolverState
{
    ...
}

public class Resolver : Actor<ResolverState>, IResolver
{
    ...

    public Task<Resolution> ResolveAsync(long entityKey)
    {
        if (State._resolutionCache.ContainsKey(entityKey))
            return Task.FromResult(_resolutionCache[entityKey]); // return from cache

        var partitionKey = State._entityPartitions[entityKey]; // resolve partition;
        var resourceKey = State._partitionResources[partitionKey]; // resolve resource;
        var resolution =
            new Resolution()
            {
                Entity = State._entities[entityKey],
                Partition = State._partitions[partitionKey],
                Resource = State._resources[resourceKey]
            }; // create resolution

        State._resolutionCache.Add(entityKey, resolution); // cache the resolution

        return Task.FromResult(resolution);
    }

    ...
}
```

## Accès aux ressources avec une capacité limitée
Examinons maintenant un autre exemple ; l'accès exclusif à des ressources précieuses comme des bases de données, des comptes de stockage et des systèmes de fichiers avec une capacité de débit limitée. Notre scénario est le suivant : nous aimerions traiter des événements à l'aide d'un acteur appelé EventProcessor, qui est responsable du traitement et de la persistance de l'événement, dans ce cas un fichier .CSV pour plus de simplicité. Même si nous pouvons suivre l'approche de partitionnement décrite ci-dessus pour monter en charge nos ressources, nous devons toujours traiter les problèmes de concurrence. C'est pourquoi nous avons choisi un exemple basé sur un fichier pour illustrer ce point particulier car l'écriture dans un fichier unique à partir de plusieurs acteurs entraîne des problèmes d'accès simultané. Pour résoudre le problème, nous avons introduit un autre acteur appelé EventWriter qui est propriétaire exclusif des ressources limitées. Voici une illustration de ce scénario :

![][3]

Nous définissons les acteurs EventProcessor comme « Travailleurs sans état », ce qui permet au runtime de les faire évoluer dans le cluster en fonction des besoins. Par conséquent, nous n'avons pas utilisé tous les identificateurs dans l'illustration ci-dessus pour ces acteurs. En d'autres termes, les acteurs sans état constituent un pool de travailleurs géré par le runtime. Dans l'exemple de code ci-dessous, l'acteur EventProcessor effectue deux opérations : il détermine tout d'abord l'élément EventWriter (et par conséquent la ressource) à utiliser et appelle l'acteur choisi pour écrire l'événement traité. Pour plus de simplicité, nous choisissons Type d'événement comme identificateur de l'acteur EventWriter. En d'autres termes, il n'y aura qu'un seul EventWriter pour ce type d'événement fournissant un accès à thread unique et exclusif à la ressource.

## Exemple de code de gestion des ressources – Event Processor

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : Actor, IEventProcessor
{
    public Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        // This where we write to constrained resource...
        var eventWriterKey = ResolveWriter(eventType, eventTime);
        var eventWriter = ActorProxy.Create<IEventWriter>(eventWriterKey);

        return eventWriter.WriteEventAsync(eventId, eventType, eventTime, payload);
    }

    private long ResolveWriter(long eventType, DateTime eventTime)
    {
        // To simplify, we are returning event type as to identify the event writer actor.
        return eventType;
    }
}
```

Examinons maintenant l'acteur EventWriter. Il ne fait pas véritablement grand-chose en dehors de contrôler l'accès exclusif à la ressource limitée, le fichier dans ce cas, et d'écrire les événements.
## Exemple de code de gestion des ressources – Event Writer

```csharp
public interface IEventWriter : IActor
{
    Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
    Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

[DataContract]
public class EventWriterState
{
    [DataMember]
    public string _filename;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
    {
        _writer.Close();
        return base.OnDeactivateAsync();
    }

    public async Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var text = string.Format("{0}, {1}, {2}, {3}", eventId, eventType, eventTime, payload);
        await _writer.WriteLineAsync(text);
        await _writer.FlushAsync();
    }
 }
```

N'avoir qu'un seul acteur responsable de la ressource nous permet d'ajouter des fonctionnalités telles que la mise en mémoire tampon. Nous pouvons mettre en mémoire tampon les événements entrants et écrire ces événements régulièrement à l'aide d'un minuteur ou lorsque la mémoire tampon est saturée. Voici un exemple simple basé sur un minuteur :
## Exemple de code de gestion des ressources – Event Writer

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (State._buffer.Count == 0)
            return;

        while (State._buffer.Count > 0)
        {
            var customEvent = State._buffer.Dequeue();
            await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
        }
    }

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        return TaskDone.Done;
    }
}
```

Bien que le code ci-dessus fonctionne correctement, les clients ne savent pas si leur événement est bien arrivé au magasin sous-jacent. Pour autoriser la mise en mémoire tampon et permettre aux clients de suivre l'état de leur requête, nous proposons l'approche suivante qui indique aux clients d'attendre que l'événement soit écrit dans le fichier .CSV :

## Exemple de code de gestion des ressources – Traitement par lots asynchrone

```csharp
public class AsyncBatchExecutor
{
    private readonly List<TaskCompletionSource<bool>> actionPromises;

    public AsyncBatchExecutor()
    {
        this.actionPromises = new List<TaskCompletionSource<bool>>();
    }

    public int Count
    {
        get
        {
            return actionPromises.Count;
        }
    }

    public Task SubmitNext()
    {
        var resolver = new TaskCompletionSource<bool>();
        actionPromises.Add(resolver);
        return resolver.Task;
    }

    public void Flush()
    {
        foreach (var tcs in actionPromises)
        {
            tcs.TrySetResult(true);

        }
        actionPromises.Clear();
    }
}
```

Nous utiliserons cette classe pour créer et gérer une liste de tâches non terminées (pour bloquer des clients) et les finaliser en une seule fois après avoir écrit les événements mis en mémoire tampon dans le stockage. Dans la classe EventWriter, nous devons faire trois choses : marquer la classe acteur comme Reentrant, renvoyer le résultat de SubmitNext() et réinitialiser notre minuterie. Le code modifié se présente comme suit :

## Exemple de code de gestion des ressources – Mise en mémoire tampon avec traitement par lots asynchrone

```csharp
public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.GetPrimaryKeyLong());
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();
        _batchExecuter = new AsyncBatchExecutor();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (_batchExecuter.Count > 0)
        {
            // take snapshot of the batch tasks
            var batchSnapshot = _batchExecuter;
            _batchExecuter = new AsyncBatchExecutor();

            if (State._buffer.Count == 0)
                return;

            while (State._buffer.Count > 0)
            {
                var customEvent = State._buffer.Dequeue();
                await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
            }

            _batchExecuter.Flush();
        }
    }
    ...

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        // we are adding an incomplete task to batch executer and returning this task.
        // this will block until task is completed when we call Flush();
        return _batchExecuter.SubmitNext();
    }
}
```

Cela semble facile ? C'est le cas. Mais cette facilité cache la puissance de l'entreprise. Avec cette architecture, nous obtenons :

* Un adressage des ressources indépendant de l'emplacement.
* Une taille de pool ajustable simplement basée sur la modification du nombre d'acteurs qui agissent au nom d'une ressource.
* Une utilisation du pool coordonnée côté client (voir illustration) ou côté serveur (imaginez un acteur unique devant chacun des pools présentés dans l'image).
* Un ajout évolutif de pools (il suffit d'ajouter les acteurs qui représentent la nouvelle ressource).
* Un acteur (comme nous l'avons démontré plus haut) peut mettre en cache les résultats à partir d'une ressource principale à la demande ou effectuer une pré-mise en cache à l'aide d'un minuteur sans que cela n’affecte la ressource principale.
* Une distribution asynchrone efficace.
* Un environnement de codage familier à tout développeur et pas seulement aux spécialistes en intergiciels.

Ce modèle est très courant dans les scénarios où des développeurs doivent travailler avec des ressources limitées ou bâtir de vastes systèmes avec montée en charge.


## Étapes suivantes
[Modèle : Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modèle : Réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modèle : Composition d'un service avec état](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modèle : Internet des objets](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modèle : Calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

[Introduction à Service Fabric Actors](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=August15_HO6-->