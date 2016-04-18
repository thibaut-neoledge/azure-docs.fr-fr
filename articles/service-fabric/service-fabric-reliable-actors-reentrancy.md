<properties
   pageTitle="Réentrance Reliable Actors | Microsoft Azure"
   description="Présentation de la réentrance pour Service Fabric Reliable Actors"
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
   ms.date="03/25/2016"
   ms.author="vturecek"/>


# Réentrance Reliable Actors
Par défaut, le runtime Reliable Actors autorise la réentrance des appels logiques selon le contexte. Cela permet de réentrer des acteurs s'ils se trouvent dans la même chaîne de contexte d'appel. Par exemple, un acteur A envoie un message à un acteur B qui envoie le message à un acteur C. Dans le cadre du traitement du message, si l’acteur C appelle l’acteur A, le message est réentrant et donc autorisé. Tout autre message faisant partie d’un contexte d’appel différent est bloqué au niveau de l’acteur A jusqu’à ce qu’il termine le traitement.

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
class MyActor : Actor, IMyActor
{
    ...
}
```

## Étapes suivantes
 - [Diagnostics et surveillance des performances d’acteur](service-fabric-reliable-actors-diagnostics.md)
 - [Documentation de référence de l’API d’acteur](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemple de code](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0406_2016-->