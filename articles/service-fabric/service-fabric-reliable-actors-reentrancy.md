<properties
   pageTitle="Réentrance des Acteurs fiables"
   description="Présentation de la réentrance pour les Acteurs fiables Service Fabric"
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
   ms.author="amanbha"/>



# Réentrance des Acteurs fiables
Par défaut, Fabric Actors autorise une réentrance des appels logiques selon le contexte. Cela permet de réentrer des acteurs s'ils se trouvent dans la même chaîne de contexte d'appel. Par exemple, si un acteur A envoie un message à un acteur B qui envoie le message à un acteur C. Dans le cadre du traitement du message, si l'acteur C appelle l'acteur A, le message est réentrant et donc autorisé. Tout autre message faisant partie d'un contexte d'appel différent sera bloqué au niveau de l'acteur A jusqu'à ce qu'il termine le traitement.

Les acteurs qui souhaitent désactiver la réentrance des appels logiques selon le contexte peuvent le faire en affectant l'attribut `ReentrantAttribute(ReentrancyMode.Disallowed)` à la classe d'acteur.

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

Le code suivant montre la classe d'acteur qui définit le mode de réentrance sur `ReentrancyMode.Disallowed`. Dans ce cas, si un acteur envoie un message réentrant à un autre acteur, une exception de type `FabricException` se produira.

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```

<!---HONumber=August15_HO6-->