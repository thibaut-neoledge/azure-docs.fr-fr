---
title: "Remarques sur Reliable Actors pour la sérialisation de type d’acteur | Documents Microsoft"
description: "Traite des exigences de base pour la définition des classes sérialisables pouvant servir à définir les interfaces et les états Service Fabric Reliable Actors"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: f08fc1df10506dead5d049fb2c6cdc29c8f89d90


---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Remarques sur la sérialisation de type Reliable Actors Service Fabric
Les arguments de toutes les méthodes, les types de résultats des tâches retournées par chaque méthode dans une interface d’acteur et les objets stockés dans le Gestionnaire d’état d’un acteur doivent être [sérialisables en contrat de données](https://msdn.microsoft.com/library/ms731923.aspx). Cela s’applique également aux arguments des méthodes définies dans les [interfaces d’événement d’acteur](service-fabric-reliable-actors-events.md). (Les méthodes d’interface d’événement d’acteur retournent toujours la valeur nulle.)

## <a name="custom-data-types"></a>Types de données personnalisés
Dans cet exemple, l’interface d’acteur suivante définit une méthode qui retourne un type de données personnalisé appelé `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

L’interface est implémentée par un acteur, qui utilise le Gestionnaire d’état pour stocker un objet `VoicemailBox` :

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

Dans cet exemple, l’objet `VoicemailBox` est sérialisé quand :

* L’objet est transmis entre une instance d’acteur et un appelant.
* L’objet est enregistré dans le Gestionnaire d’état quand il est enregistré sur disque et répliqué vers d’autres nœuds.

L’infrastructure Reliable Actor utilise la sérialisation DataContract. Par conséquent, les objets de données personnalisés et leurs membres doivent être annotés avec les attributs **DataContract** et **DataMember**, respectivement

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Cycle de vie des acteurs et Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Minuteries et rappels d’acteur](service-fabric-reliable-actors-timers-reminders.md)
* [Événements d’acteurs](service-fabric-reliable-actors-events.md)
* [Réentrance des acteurs](service-fabric-reliable-actors-reentrancy.md)
* [Polymorphisme des acteurs et modèles de conception orientée objet](service-fabric-reliable-actors-polymorphism.md)
* [Diagnostics et surveillance des performances d’acteur](service-fabric-reliable-actors-diagnostics.md)



<!--HONumber=Nov16_HO3-->


