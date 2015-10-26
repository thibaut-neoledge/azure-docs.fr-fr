<properties
   pageTitle="Cycle de vie des Acteurs fiables"
   description="Explique le cycle de vie et le Garbage Collection pour les Acteurs fiables Service Fabric"
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


# Cycle de vie des acteurs et Garbage Collection
Un acteur est activé quand il est appelé pour la première fois et il est désactivé (fait l'objet d'un Garbage Collection par le runtime Actors) s'il n'est pas utilisé pendant un certain temps. Pour configurer cette période de temps, consultez la section Garbage Collection des acteurs ci-dessous.

Que se passe-t-il lors de l'activation d'un acteur ?

- Quand un appel est émis vers un acteur qui n'est pas actif, un autre acteur est créé.
- Son état est chargé (s'il s'agit d'un acteur avec état)
- La méthode `OnActivateAsync` (qui peut être remplacée dans l'implémentation de l'acteur) est appelée.
- Il est ajouté à une table d'acteurs actifs.

Que se passe-t-il lors de la désactivation d'un acteur ?

- Quand un acteur n'est pas utilisé pendant un certain temps, il est supprimé de la table d'acteurs actifs.
- La méthode `OnDeactivateAsync` (qui peut être remplacée dans l'implémentation de l'acteur) est appelée et efface toutes les minuteries de l'acteur.

> [AZURE.TIP]Le runtime Fabric Actors émet des [événements liés à l'activation et la désactivation des acteurs](service-fabric-reliable-actors-diagnostics.md#actor-activation-and-deactivation-events). Ces derniers sont utiles dans les diagnostics et la surveillance des performances.

## Garbage Collection des acteurs
Le runtime Actors recherche périodiquement les acteurs qui n'ont pas été utilisés depuis un certain temps et les désactive. Une fois qu'ils sont désactivés, ils peuvent faire l'objet d'un Garbage Collection par le CLR.

Que signifie « être utilisé » dans le cadre du Garbage Collection ?

- Réception d'un appel.
- Appel de la méthode `IRemindable.ReceiveReminderAsync` (applicable uniquement si l'acteur utilise des rappels).

Notez que si l'acteur utilise des minuteries et que son rappel de minuterie est appelé, cela ne signifie **pas** qu'il est « utilisé ».

Avant d'aborder les détails du Garbage Collection, il est important de définir les termes suivants :

- *Intervalle d'analyse* : il s'agit de l'intervalle pendant lequel le runtime Actors recherche dans sa table d'acteurs actifs les acteurs qui peuvent faire l'objet d'un Garbage Collection. La valeur par défaut est 1 minute.
- *Délai d'inactivité* : il s'agit de la durée pendant laquelle un acteur reste inutilisé (inactif) avant de faire l'objet d'un Garbage Collection. La valeur par défaut est 60 minutes.

En général, vous n'avez pas besoin de modifier les valeurs par défaut. Toutefois, si nécessaire, ces intervalles peuvent être modifiés au niveau de l'assembly pour tous les types d'acteur dans cet assembly ou au niveau d'un type d'acteur à l'aide de l'attribut `ActorGarbageCollection`. L'exemple ci-dessous montre la modification des intervalles de Garbage Collection pour HelloActor.

```csharp
[ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

Pour modifier la valeur par défaut de l'attribut `ActorGarbageCollection` au niveau de l'assembly, ajoutez l'extrait suivant à `AssemblyInfo.cs`.

```csharp
[assembly: ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
```

Pour chaque acteur de la table d'acteurs actifs, le runtime Actors effectue le suivi de la durée pendant laquelle il a été inactif (c'est-à-dire non utilisé). Le runtime Actors vérifie chacun des acteurs tous les `ScanIntervalInSeconds` pour voir s'il peut faire l'objet d'un Garbage Collection, et le collecte s'il est inactif depuis `IdleTimeoutInSeconds`.

Chaque fois qu'un acteur est utilisé, son délai d'inactivité est réinitialisé à 0. Ensuite, l'acteur peut uniquement faire l'objet d'un Garbage Collection s'il reste encore inactif pendant `IdleTimeoutInSeconds`. N'oubliez pas qu'un acteur est considéré utilisé si une méthode d'interface d'acteur ou un rappel de rappel d'acteur est exécuté. Un acteur n'est **pas** considéré utilisé si son rappel de minuterie est exécuté.

Le diagramme ci-dessous contient un exemple qui illustre ces concepts.

![][1]

L'exemple suppose qu'il n'existe qu'un seul acteur actif dans la table d'acteurs actifs et montre l'impact des appels de méthode d'acteur, des minuteries et des rappels sur la durée de vie de cet acteur. Voici les points importants de l'exemple :

- ScanInterval et IdleTimeout sont définis avec les valeurs 5 et 10 respectivement dans l'exemple (les unités n'ont pas d'importance ici, dans la mesure où notre objectif se borne à illustrer le concept).
- La recherche d'acteurs ayant fait l'objet d'un Garbage Collection s'effectue à T=0,5,10,15,20,25, comme défini par la valeur 5 de ScanInterval.
- Une minuterie périodique se déclenche à T=4,8,12,16,20,24 et son rappel s'exécute. Il n'affecte pas la durée d'inactivité de l'acteur.
- Un appel de méthode d'acteur à T=7 réinitialise la durée d'inactivité à 0 et retarde le Garbage Collection de l'acteur.
- Un rappel de rappel d'acteur s'exécute à T=14 et retarde davantage le Garbage Collection de l'acteur.
- Lors de l'analyse de Garbage Collection à T=25, la durée d'inactivité de l'acteur dépasse la valeur 10 du paramètre IdleTimeout et l'acteur fait l'objet d'un Garbage Collection.

Notez qu'un acteur ne peut jamais faire l'objet d'un Garbage Collection quand il exécute l'une de ses méthodes, quelle que soit la durée d'exécution de cette méthode. Comme mentionné précédemment, l'exécution des méthodes d'interface d'acteur et des rappels de rappel empêche le Garbage Collection en réinitialisant la durée d'inactivité de l'acteur à 0. L'exécution des rappels de minuterie ne réinitialise pas la durée d'inactivité à 0. Toutefois, le Garbage Collection de l'acteur est différé jusqu'à ce que le rappel de minuterie ait terminé son exécution.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

<!---HONumber=Oct15_HO3-->