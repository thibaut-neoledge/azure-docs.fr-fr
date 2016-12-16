---
title: "Réentrance Reliable Actors | Microsoft Docs"
description: "Présentation de la réentrance pour Service Fabric Reliable Actors"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/19/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1d71949426637b520b8fed0b0fe64e4afcbfd077


---
# <a name="reliable-actors-reentrancy"></a>Réentrance Reliable Actors
Par défaut, le runtime Reliable Actors autorise la réentrance des appels logiques selon le contexte. Cela permet de réentrer des acteurs s'ils se trouvent dans la même chaîne de contexte d'appel. Par exemple, un acteur A envoie un message à un acteur B qui envoie le message à un acteur C. Dans le cadre du traitement du message, si l’acteur C appelle l’acteur A, le message est réentrant et donc autorisé. Tout autre message faisant partie d’un contexte d’appel différent est bloqué au niveau de l’acteur A jusqu’à ce qu’il termine le traitement.

Deux options disponibles pour la réentrance des acteurs sont définies dans l’enum `ActorReentrancyMode` :

* `LogicalCallContext` (comportement par défaut)
* `Disallowed` : désactive la réentrance

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

Vous pouvez configurer la réentrance dans les paramètres d’un `ActorService`lors de l’inscription. Le paramètre s’applique à toutes les instances d’acteur créées dans le service d’acteur.

L’exemple suivant montre un service d’acteur qui affecte la valeur `ActorReentrancyMode.Disallowed`au mode de réentrance. Dans ce cas, si un acteur envoie un message réentrant à un autre acteur, une exception de type `FabricException` est levée.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context, 
                    actorType, () => new Actor1(), 
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Diagnostics et surveillance des performances d’acteur](service-fabric-reliable-actors-diagnostics.md)
* [Documentation de référence de l’API d’acteur](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exemple de code](https://github.com/Azure/servicefabric-samples)




<!--HONumber=Nov16_HO3-->


