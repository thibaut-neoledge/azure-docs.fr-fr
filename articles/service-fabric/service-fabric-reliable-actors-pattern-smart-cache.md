<properties
   pageTitle="Modèle de conception Smart Cache pour les Acteurs fiables"
   description="Modèle de conception sur l'utilisation des Acteurs fiables comme infrastructure de mise en cache dans les applications Web"
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
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Modèle de conception Acteurs fiables : Smart Cache
La combinaison d'un niveau web, d'un niveau de mise en cache, d'un niveau de stockage et parfois d'un niveau travailleur constitue les éléments standard des applications actuelles. Le niveau de mise en cache est généralement essentiel pour les performances et peut, en fait, être lui-même composé de plusieurs niveaux. De nombreux caches sont de simples paires clé-valeur tandis que d'autres systèmes comme [Redis](http://redis.io) utilisés comme caches offrent une sémantique plus riche. Cependant, tout niveau particulier de mise en cache sera limité en termes de sémantique et plus important encore, il constituera un autre niveau à gérer. Que se passe-t-il si au lieu de cela, les objets conservaient simplement l'état dans des variables locales et qu'on puisse en faire des instantanés ou les conserver automatiquement dans un magasin durable ? En outre, de collections riches, telles que des listes, des ensembles triés, des files d'attente et tout autre type personnalisé sont simplement modélisés comme des variables et des méthodes membres.

![][1]

## L'exemple du classement
Prenons comme exemples des classements : un objet Classement doit conserver une liste triée des joueurs et de leurs scores pour nous permettre de lancer des recherches. Par exemple, pour afficher les « 100 premiers joueurs » ou pour situer un joueur dans le classement par rapport aux +- N joueurs avant et après lui. Une solution standard avec des outils traditionnels nécessiterait une commande 'GET' pour obtenir l'objet Classement (une collection qui prend en charge l'insertion d'un nouveau tuple<Player  Points> nommé Score), un tri de l'objet, puis une commande 'PUT' pour replacer l'objet dans le cache. Nous verrouillerions (LOCK) probablement l'objet Classement (GETLOCK, PUTLOCK) pour garantir la cohérence. Examinons une solution basée sur un acteur où l'état et le comportement sont combinés. Nous avons deux options :

* Implémenter la collection Classement comme un composant de l'acteur,
* Ou utiliser l'acteur comme une interface vers la collection que nous pouvons conserver dans une variable membre. Voyons tout d'abord à quoi ressemblerait cette interface :

## Exemple de code de Smart Cache – Interface du classement

```
public interface ILeaderboard : IActor
{
    // Updates the leaderboard with the score - player, points
    Task UpdateLeaderboard(Score score);

    // Returns the Top [count] from the leaderboard e.g., Top 10
    Task<List<Score>> GetLeaderboard(int count);

    // Returns the specific position of the player relative to other players
    Task<List<Score>> GetPosition(long player, int range);
}

```

Ensuite, nous implémentons cette interface puis utilisons cette dernière option et encapsulons le comportement de cette collection dans l'acteur :

## Exemple de code de Smart Cache – Acteur du classement

```
public class Leaderboard : Actor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
    }

    public Task<List<Score>> GetLeaderboard(int count)
    {
        // Return top N from Leaderboard
        return Task.FromResult(State.GetLeaderboard(count));
    }

    public Task<List<Score>> GetPosition(long player, int range)
    {
        // Return player position and other players in range from Leaderboard
        return Task.FromResult(State.FindPosition(player, range));
    }
}

```

Le membre état de la classe fournit l'état de l'acteur, et dans l'exemple de code ci-dessus, il fournit également des méthodes de lecture et d'écriture des données.

## Exemple de code de Smart Cache – LeaderboardCollection

```
[DataContract]
public class LeaderboardCollection
{
    // Specialised collection, could be part of the actor
    [DataMember]
    Private List<score> _leaderboard = new List<score>();

    public void UpdateLeaderboard(Score score)
    {
        _leaderboard.add(score);
    }

    public List<Score> GetLeaderboard(int count)
    {
        …
    }

    public List<Score> GetPosition(long player, int range)
    {
        …
    }
}

```

Aucun envoi de données, aucun verrou, simplement une manipulation d'objets distants dans un runtime distribué et la gestion de plusieurs clients comme s'il s'agissait d'objets uniques d'une même application ne traitant qu'un seul client. Voici l'exemple de client :

## Exemple de code de Smart Cache – Appel de l'acteur du classement

```
// Get reference to Leaderboard
const string appName = "fabric:/FunnyGameApp";
var leaderboard = ActorProxy.Create<ILeaderboard>(1001, appName);

// Update Leaderboard with dummy players and scores
await leaderboard.UpdateLeaderboard(new Score() { Player = 1, Points = 500 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 2, Points = 100 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 3, Points = 1500 });

// Finally, Get the Leaderboard. 0 represents ALL, any other number > 0 represents TOP N
var result = await leaderboard.GetLeaderboard(0);
```

Le résultat se présente ainsi :

```
Player = 3 Points = 1500
Player = 1 Points = 500
Player = 2 Points = 100
```

## Mise à l'échelle de l'architecture
Il peut sembler que l'exemple ci-dessus risque de créer un goulet d'étranglement dans l'instance Classement. Que se passe-t-il si, par exemple, nous avons l'intention de prendre en charge des centaines de milliers de joueurs ? Une façon de régler ce problème peut consister à introduire des agrégations qui agiraient comme un tampon, en conservant les scores partiels (par exemple les sous-totaux) puis en les envoyant périodiquement à l'acteur Classement afin de gérer le classement final. Nous étudierons cette technique d'« agrégation » plus en détail ultérieurement. En outre, nous n'avez pas à prendre en compte les mutex, les sémaphores ou autres constructions concurrentes traditionnellement requises par les programmes concurrents qui s'exécutent correctement. Voici un autre exemple de cache qui montre la sémantique riche qu'il est possible d'implémenter avec les acteurs. Cette fois, nous implémentons la logique de la file d'attente de priorité (plus le chiffre est bas, plus la priorité est élevée) dans le cadre de l'implémentation de l'acteur. L'interface de IJobQueue se présente ainsi :

## Exemple de code de Smart Cache – Interface de file d'attente de tâches

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

Nous devons également définir l'élément Tâche :

## Exemple de code Smart Cache – Tâche

```
public class Job : IComparable<Job>
{
    public double Priority { get; set; }
    public string Name { get; set; }

    public override string ToString()
    {
        return string.Format("Job = {0} Priority = {1}", Name, Priority);
    }

    public int CompareTo(Job other)
    {
        return Priority.CompareTo(other.Priority);
    }
}
```

Enfin, nous implémentons précisément l'interface IJobQueue. Notez que nous avons omis ici les détails d'implémentation de la file d'attente de priorité par souci de clarté. Vous trouverez un exemple d'implémentation dans les exemples qui accompagnent ce document.

## Exemple de code Smart Cache – File d'attente des tâches

```
public class JobQueue : Actor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

    }

    public Task<Job> Dequeue()
    {
        // this is where we remove from the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<Job> Peek()
    {
        // this is where we peek at the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<int> GetCount()
    {

        // this is where we return the number of items in the queue

        return Task.FromResult(data.Count);
    }
}

```

Le résultat se présente ainsi :

```
Job = 2 Priority = 0.0323341116459733
Job = 3 Priority = 0.125596747792138
Job = 4 Priority = 0.208425460480352
Job = 0 Priority = 0.304352047063574
Job = 8 Priority = 0.415597594070992
Job = 7 Priority = 0.477669881413537
Job = 5 Priority = 0.525898784178262
Job = 9 Priority = 0.921959541701693
Job = 6 Priority = 0.962653734238191
Job = 1 Priority = 0.97444181375878
```

## Les acteurs garantissent la flexibilité
Dans les exemples ci-dessus, Classement et File d'attente de tâches, nous avons utilisé deux techniques différentes :

* Dans l'exemple Classement, nous avons encapsulé un objet Classement comme variable membre privée dans l'acteur et simplement proposé une interface vers cet objet – à la fois vers son état et sa fonctionnalité.

* En revanche, dans l'exemple File d'attente de tâches, nous avons implémenté l'acteur comme une file d'attente de priorité plutôt qu'en faisant référence à un autre objet défini ailleurs.

Les acteurs fournissent au développeur la flexibilité de définir des structures d'objet riches dans le cadre des acteurs ou de référencer des graphiques d'objets en dehors des acteurs. En termes de mise en cache, les acteurs peuvent effectuer des opérations d'écriture différées ou de double écriture, ou nous pouvons utiliser différentes techniques à un niveau de granularité de variable membre. En d'autres termes, nous avons un contrôle total sur les éléments à conserver et le moment de le faire. Nous n'avons pas besoin de conserver un état transitoire ou un état que nous pouvons créer à partir d'un état enregistré. Et qu'en est-il du remplissage des caches de ces acteurs ? Il existe plusieurs moyens d'y parvenir. Les acteurs fournissent des méthodes virtuelles appelées OnActivateAsync() et OnDectivateAsync() qui nous avertissent lorsqu'une instance d'acteur est activée et désactivée. Notez que l'acteur est activé à la demande lorsqu'il reçoit la première requête. Nous pouvons utiliser OnActivateAsync() pour remplir l'état à la demande comme dans l'opération de double lecture, peut-être à partir d'un magasin externe stable. Ou nous pouvons remplir l'état d'un minuteur, par exemple un acteur Taux de change qui fournit la fonction de conversion à partir des derniers taux de change. Cet acteur peut remplir régulièrement son état à partir d'un service externe, par exemple toutes les 5 secondes, et utiliser l'état pour la fonction de conversion. Prenons l'exemple suivant :

## Exemple de code Smart Cache – Convertisseur de taux

```
...

private List<ExchangeRate> _rates;
private IActorTimer _timer;

public Task Activate()
{
    // registering a timer that will live as long as the actor...
    _timer = this.RegisterTimer((obj) =>
    {
        Console.WriteLine("Refreshing rates...");
        return this.RefreshRates(); // call to external service/source to retrieve exchange rates
    },
    null,
    TimeSpan.FromSeconds(0), // start immediately
    TimeSpan.FromSeconds(5)); // refresh every 5 seconds

}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
}

```

Essentiellement, Smart Cache fournit :

* Débit élevé/faible latence par des demandes de service à partir de la mémoire.
* Routage d'instance unique et sérialisation à thread unique des demandes vers un élément, sans contention au niveau du magasin persistant.
* Opérations de sémantiques, par exemple, file d'attente (élément Tâche).
* Opérations de double écriture ou d'écriture différée faciles à implémenter
* Éviction automatique des LRU (derniers éléments utilisés) (gestion des ressources).
* Élasticité et fiabilité automatiques.


## Étapes suivantes
[Modèle : réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modèle : Gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modèle : composition d'un service avec état](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modèle : Internet des objets](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modèle : Calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

[Introduction à Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=Oct15_HO3-->