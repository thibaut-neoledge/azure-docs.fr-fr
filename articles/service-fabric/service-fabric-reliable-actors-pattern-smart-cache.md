<properties
   pageTitle="Modèle de conception Smart Cache | Microsoft Azure"
   description="Modèle de conception sur l'utilisation du modèle de programmation Reliable Actors de Service Fabric pour créer une infrastructure de mise en cache pour les applications web."
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

# Modèle de conception Reliable Actors : Smart Cache

La combinaison d’un niveau web, d’un niveau de mise en cache, d’un niveau de stockage et (parfois) d’un niveau worker constitue les éléments standard de nombreuses applications actuelles. Le niveau de mise en cache est généralement essentiel pour les performances et peut être lui-même composé de plusieurs niveaux. De nombreux caches sont de simples paires clé-valeur. D’autres systèmes, comme [Redis](http://redis.io), sont utilisés comme caches, mails ils offrent une sémantique plus riche. Cependant, tout niveau particulier de mise en cache est limité en termes de sémantique. En outre, il constitue un autre niveau à gérer.

Au lieu de cela, les objets peuvent conserver l’état dans des variables locales et être convertis en instantanés ou conservés automatiquement dans un magasin durable. En outre, les collections riches, telles que des listes, des ensembles triés, des files d’attente et autres types personnalisés, peuvent être simplement modélisées comme des variables et des méthodes membres.

![Acteurs et mise en cache][1]

## Explorer l’exemple du classement

Prenons comme exemples des classements. Un objet classement doit conserver une liste triée des joueurs et de leurs scores, pour que la liste puisse être interrogée. Une requête peut rechercher les 100 premiers joueurs. Elle peut également trouver la place qu’occupe un joueur dans le classement par rapport à un certain nombre de joueurs au-dessus et au-dessous de lui. Une solution traditionnelle nécessiterait d’obtenir l’objet classement (collection qui prend en charge l’insertion d’un nouveau tuple `<Player, Points>` nommé **Score**) au moyen d’une commande GET, de trier l’objet, puis de le replacer dans le cache avec une commande PUT. Vous verrouillerez probablement l’objet classement (GETLOCK, PUTLOCK) pour garantir la cohérence. Examinons une solution basée sur un acteur où l’état et le comportement sont combinés. Nous avons deux options :

* Implémenter la collection classement comme un composant de l’acteur.
* Utiliser l’acteur comme une interface vers la collection que nous pouvons conserver dans une variable membre.

L’exemple de code suivant illustre l’interface.

### Exemple de code de Smart Cache : interface du classement

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

Ensuite, vous pouvez implémenter cette interface en utilisant la dernière option et en encapsulant le comportement de la collection dans l’acteur :

### Exemple de code de Smart Cache : acteur du classement

```
public class Leaderboard : StatefulActor<LeaderboardCollection>, ILeaderboard
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

Le membre état de la classe fournit l’état de l’acteur. Dans l’exemple de code ci-dessus, il fournit également des méthodes de lecture et d’écriture des données.

### Exemple de code de Smart Cache : LeaderboardCollection

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

Cette approche ne fait appel à aucun verrou ou transfert de données. Elle consiste simplement à manipuler des objets distants dans un runtime distribué, qui gère plusieurs clients comme s’il s’agissait d’objets uniques d’une même application ne traitant qu’un seul client. L’exemple de code suivant se concentre sur l’exemple de client.

### Exemple de code de Smart Cache : appel de l’acteur du classement

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

## Mettre l’architecture à l’échelle
Il peut sembler que l’exemple ci-dessus risque de créer un goulet d’étranglement dans l’instance classement. Par exemple, que se passe-t-il si vous envisagez de prendre en charge des milliers de joueurs ? Une façon de régler ce problème peut consister à introduire des agrégations sans état qui agiraient comme un tampon. Ces agrégations conserveraient les scores partiels (sous-totaux), puis les enverraient périodiquement à l’acteur classement afin de gérer le classement final. Nous étudierons cette technique plus en détail ultérieurement. En outre, nous n’avons pas à prendre en compte les mutex, les sémaphores ou autres constructions concurrentes traditionnellement requises par les programmes concurrents qui s’exécutent correctement.

Voici un autre exemple de cache qui montre la sémantique riche que vous pouvez implémenter avec les acteurs. Cette fois, nous implémentons la logique d’une file d’attente de priorité (plus le chiffre est bas, plus la priorité est élevée) dans le cadre de l’implémentation de l’acteur. L’exemple de code suivant présente l’interface de **IJobQueue**.

### Exemple de code de Smart Cache : interface de file d’attente des travaux

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

Nous devons également définir l’élément **Job** :

### Exemple de code de Smart Cache : Job

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

Enfin, nous implémentons l'interface IJobQueue dans l’acteur. Notez que nous avons omis ici les détails d'implémentation de la file d'attente de priorité par souci de clarté. Les exemples qui accompagnent ce document donnent un aperçu de l’implémentation.

### Exemple de code de Smart Cache : file d’attente des travaux

```
public class JobQueue : StatefulActor<List<Jobs>>, IJobQueue
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

## Utiliser des acteurs pour fournir la flexibilité
Dans les exemples ci-dessus du classement et de la file d’attente des travaux, nous avons utilisé deux techniques différentes :

* Dans l’exemple du classement, nous avons encapsulé un objet classement comme variable membre privée dans l’acteur. Nous avons ensuite simplement proposé une interface vers cet objet, à la fois vers son état et sa fonctionnalité.

* Dans l’exemple de la file d’attente des travaux, nous avons, à la place, implémenté l’acteur comme une file d’attente de priorité, plutôt qu’en faisant référence à un autre objet défini ailleurs.

Les acteurs fournissent au développeur la flexibilité de définir des structures d’objet riches dans le cadre des acteurs ou de référencer des graphiques d’objets en dehors des acteurs. En termes de mise en cache, les acteurs peuvent effectuer des opérations d’écriture différées ou de double écriture, ou ils peuvent utiliser différentes techniques au niveau de granularité des variables membres. Vous avez un contrôle total sur les éléments à conserver et le moment de le faire. Vous n’avez pas besoin de conserver un état transitoire ou un état que vous pouvez créer à partir d’un état enregistré.

Comment les caches de ces acteurs sont-ils remplis ? Il existe plusieurs moyens d'y parvenir. Les acteurs fournissent les méthodes virtuelles **OnActivateAsync()** et **OnDeactivateAsync()** qui vous avertissent quand une instance d’acteur est activée et désactivée. Notez que l’acteur est activé à la demande dès qu’il reçoit une requête.

Vous pouvez utiliser OnActivateAsync() pour remplir l’état à la demande, comme dans l’opération de double lecture, par exemple à partir d’un magasin externe stable. Vous pouvez également remplir l’état d’un minuteur, par exemple en utilisant un acteur taux de change qui fournit une fonction de conversion à partir des derniers taux de change. Un acteur de ce type peut remplir régulièrement son état à partir d’un service externe (par exemple, toutes les cinq secondes) et utiliser l’état pour la fonction de conversion. L’exemple de code suivant montre comment procéder.

### Exemple de code de Smart Cache : convertisseur de taux

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

Essentiellement, l’approche Smart Cache présente les caractéristiques suivantes :

* Débit élevé/faible latence par des demandes de service à partir de la mémoire
* Routage d’instance unique et sérialisation à thread unique des demandes vers un élément, sans contention au niveau d’un magasin persistant
* Opérations de sémantiques, par exemple, **file d’attente (élément Job)**
* Opérations de double écriture et d’écriture différée faciles à implémenter
* Éviction automatique des derniers éléments utilisés (LRU) (gestion des ressources)
* Élasticité et fiabilité automatiques


## Étapes suivantes

[Modèle : réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modèle : gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modèle : composition d’un service avec état](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modèle : Internet des objets](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modèle : calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

[Présentation des Acteurs fiables Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=AcomDC_0121_2016-->