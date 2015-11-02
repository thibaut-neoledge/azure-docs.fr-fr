<properties
   pageTitle="Modèle de conception de réseaux distribués et graphiques Service Fabric Actors"
   description="Modèle de conception décrivant comment utiliser Service Fabric Actors pour modéliser une application sous forme de réseaux distribués et de graphiques"
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
   ms.date="09/29/2015"
   ms.author="claudioc"/>

# Modèle de conception Acteurs fiables : réseaux distribués et graphiques
Les Acteurs fiables Service Fabric constituent une solution naturelle pour la modélisation de solutions complexes impliquant des relations et la modélisation de ces relations sous forme d'objets.

![][1]

Comme le montre le diagramme, il est facile de modéliser un utilisateur en tant qu'instance d'acteur (nœud dans le réseau). Par exemple, le « flux des amis » (parfois appelé problème de « l'abonné ») permet aux utilisateurs d'afficher les mises à jour du statut des personnes auxquelles est sont connectées, comme dans Facebook et Twitter. Le modèle Actor permet d'aborder le problème de la matérialisation. Nous pouvons alimenter le flux des amis au moment de l'événement, en mettant à jour le flux des amis au moment où une mise à jour est publiée, comme illustré ci-dessous :

![][2]


## Exemple de code Smart Cache – Flux d'amis de réseau social (heure de l'événement)

Exemple de code alimentant le flux des amis :

```csharp
public interface ISocialPerson : IActor
{
    Task AddFriend(long person);
    Task RemoveFriend(long person);
    Task<List<SocialStatus>> GetFriendsFeed();
    Task<SocialStatus> GetStatus();
    Task<List<SocialStatus>> GetMyFeed();
    Task UpdateStatus(string status);
    Task UpdateFriendFeedAsync(SocialStatus status);
}

[DataContract]
Public class SocialPersonState
{
    [DataMember]
    public string _name; // my name
    [DataMember]
    public List<long> _friends; // list of my friends' IDs
    [DataMember]
    public List<SocialStatus> _friendsFeed; // my friends feeds
    [DataMember]
    public List<SocialStatus> _myFeed; // this is my feed, all my status updates
    [DataMember]
    public SocialStatus _lastStatus; // this is my last update
}

public class SocialPerson : Actor<SocialPersonState>, ISocialPerson
{
    public override Task ActivateAsync()
    {
        CreateOrRestoreState();
        return base.ActivateAsync();
    }

    public Task AddFriend(long person)
    {
        State._friends.Add(person);
        return TaskDone.Done;
    }

    public Task RemoveFriend(long person)
    {
        State._friends.Remove(person);
        return TaskDone.Done;
    }

    public Task<List<SocialStatus>> GetFriendsFeed()
    {
        return Task.FromResult(State._friendsFeed);
    }

    public Task UpdateStatus(string status)
    {
        State._lastStatus = new SocialStatus()
        {
            Name = _name,
            Status = status,
            Timestamp = DateTime.UtcNow
        };
        State._myFeed.Add(_lastStatus);

        var taskList = new List<Task>();

        foreach(var friendId in _friends)
        {
            var friend = ActorProxy.Create<ISocialPerson>(friendId);
            taskList.Add(friend.UpdateFriendFeedAsync(_lastStatus));
        }

        return Task.WhenAll(taskList);
    }

    public Task UpdateFriendFeed(SocialStatus status)
    {
        State._friendsFeed.Add(status);

        return TaskDone.Done;
    }

    public Task<SocialStatus> GetStatus()
    {
        return Task.FromResult(State._lastStatus);
    }

    public Task<List<SocialStatus>> GetMyFeed()
    {
        return Task.FromResult(State._myFeed);
    }
}
```

Nous pouvons également modéliser nos acteurs afin de compiler le flux d'amis au niveau de la minuterie de la requête, en d'autres termes lorsque l'utilisateur demande le flux de ses amis. Une autre méthode consiste à matérialiser le flux d'amis sur une minuterie, par exemple, toutes les 5 minutes. Ou bien, nous pouvons optimiser le modèle et combiner le traitement de l'heure de l'événement et de l'heure de la requête grâce à un modèle basé sur un minuteur en fonction des habitudes de l'utilisateur, par exemple la fréquence à laquelle il se connecte ou publie une mise à jour. Lors de la modélisation d'un acteur dans un réseau social, il faut également tenir compte des « super utilisateurs », les utilisateurs comptant des millions d'abonnés. Les développeurs doivent modéliser l'état et le comportement de ces utilisateurs différemment pour répondre à la demande. De même, vous pouvez également modéliser une activité qui connecte de nombreux acteurs de l'utilisateur à un seul acteur d'activité (hub and spoke). Les discussions de groupe ou l'hébergement d'un jeu en sont deux exemples. Prenons l'exemple du groupe de discussion ; plusieurs participants créer un acteur de groupe de discussion capable de distribuer les messages d'un participant au groupe, comme dans l'exemple ci-dessous :

## Exemple de code Smart Cache – GroupChat

```csharp
public interface IGroupChat : IActor
{
    Task PublishMessageAsync(long participantId, string message);
    Task<List<GroupChatMessage>> GetMessagesAsync();
    Task AddParticipantAsync(long participantId);
    Task RemoveParticipantAsync(long partitipantId);
}

[DataContract]
public class GroupChatParticipantState
{
    [DataMember]
    Public long _groupChatId;
    [DataMember]
    public List<GroupChatMessage> _messages;
}

public class GroupChatParticipant : Actor<GroupChatParticipantState>, IGroupParticipant
{
    public Task SendMessageAsync(string message)
    {
        if (State._groupChatId != -1)
        {
            var groupChat = ActorProxy.Create<IGroupChat>(State._groupChatId);
            return groupChat.PublishMessageAsync(this.Id, message);
        }

        return TaskDone.Done;
    }

    ...
}

[DataContract]
public class GroupChatState
{
    [DataMember]
    Public List<long> _participants;
    [DataMember]
    Public List<GroupChatMessage> _messages;
}


public class GroupChat : Actor<GroupChatState>, IGroupChat
{

public Task PublishMessageAsync(long participantId, string message)
{
    var chatMessage = new GroupChatMessage()
    {
        ParticipantId = participantId,
        Message = message,
        Timestamp = DateTime.Now
    };

    State._messages.Add(chatMessage);

    var taskList = new List<Task>();

    foreach(var id in State._participants)
    {
        if (id != participantId)
        {
            var participant = ActorProxy.Create<IGroupParticipant>.Create(id);
            taskList.Add(participant.ReceiveMessageAsync(chatMessage));
        }
    }
    return Task.WhenAll(taskList);
}

...

}
```

Ce code ne fait qu'exploiter la capacité Reliable Actors pour permettre à n'importe quel acteur de contacter n'importe quel autre acteur du cluster par ID et de communiquer avec lui sans avoir à se soucier du positionnement, de l'adressage, de la mise en cache, de la messagerie, de la sérialisation ou du routage.

## Étapes suivantes
[Modèle : Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modèle : Gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modèle : composition d'un service avec état](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modèle : Internet des objets](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modèle : Calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

[Introduction à Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=Oct15_HO4-->