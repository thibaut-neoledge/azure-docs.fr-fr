<properties
   pageTitle="Événements Reliable Actors | Microsoft Azure"
   description="Présentation des événements pour Reliable Actors Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/19/2016"
   ms.author="amanbha"/>


# Événements d’acteurs
Les événements d’acteur sont un moyen d’envoyer des notifications Meilleur effort de l’acteur aux clients. Les événements d’acteur sont conçus pour la communication acteur-client et ne doivent pas être utilisés pour la communication acteur-acteur.

L’extrait de code suivant montre comment utiliser les événements d’acteur dans votre application.

Définissez une interface qui décrit les événements publiés par l'acteur. Cette interface doit être dérivée de l'interface `IActorEvents`. Les arguments des méthodes doivent être [sérialisables en contrat de données](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Les méthodes doivent retourner une valeur nulle, car les notifications d’événement sont unidirectionnelles et Meilleur effort.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Déclarez les événements publiés par l'acteur dans l'interface d'acteur.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    [Readonly]
    Task<string> GetGameScore();
}
```

Côté client, implémentez le gestionnaire d'événements.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

Sur le client, créez un proxy pour l'acteur qui publie l'événement et s'abonne à ses événements.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);
proxy.SubscribeAsync(new GameEventsHandler()).Wait();
```

En cas de basculement, l’acteur peut basculer sur un nœud ou processus différent. Le proxy de l'acteur gère les abonnements actifs et s'y réabonne automatiquement. Vous pouvez contrôler l'intervalle de réabonnement via l'API `ActorProxyEventExtensions.SubscribeAsync<TEvent>`. Pour vous désabonner, utilisez l’API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>`.

Sur l'acteur, publiez simplement les événements à mesure qu'ils se produisent. Le runtime Actors envoie la notification aux abonnés à l’événement, le cas échéant.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), State.Status.Score);
```

<!---HONumber=AcomDC_0224_2016-->