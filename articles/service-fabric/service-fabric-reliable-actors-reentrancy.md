<properties
   pageTitle="Réentrance Reliable Actors | Microsoft Azure"
   description="Présentation de la réentrance pour Service Fabric Reliable Actors"
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


# Réentrance Reliable Actors
Par défaut, le runtime Fabric Actors autorise la réentrance des appels logiques selon le contexte. Cela permet de réentrer des acteurs s'ils se trouvent dans la même chaîne de contexte d'appel. Par exemple, un acteur A envoie un message à un acteur B qui envoie le message à un acteur C. Dans le cadre du traitement du message, si l’acteur C appelle l’acteur A, le message est réentrant et donc autorisé. Tout autre message faisant partie d’un contexte d’appel différent est bloqué au niveau de l’acteur A jusqu’à ce qu’il termine le traitement.

Les acteurs qui souhaitent désactiver la réentrance des appels logiques selon le contexte peuvent le faire en affectant l'attribut `ReentrantAttribute(ReentrancyMode.Disallowed)` à la classe d'acteur.

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

Le code suivant montre une classe d’acteur qui affecte au mode de réentrance la valeur `ReentrancyMode.Disallowed`. Dans ce cas, si un acteur envoie un message réentrant à un autre acteur, une exception de type `FabricException` est levée.

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```

<!---HONumber=AcomDC_0224_2016-->