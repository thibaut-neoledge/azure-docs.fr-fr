<properties
   pageTitle="Minuteries et rappels pour les Acteurs fiables"
   description="Présentation des minuteries et rappels pour les Acteurs fiables Service Fabric."
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


# Minuteries des acteurs
Les minuteries des acteurs fournissent un wrapper simple autour de minuteries .NET de sorte que les méthodes de rappel respectent les garanties d’un accès concurrentiel en alternance fournies par le runtime Actors.

Les acteurs peuvent utiliser les méthodes `RegisterTimer` et `UnregisterTimer` sur leur classe de base pour inscrire et désinscrire leurs minuteries. L'exemple ci-dessous montre l'utilisation des API de minuterie. Les API sont très similaires à la minuterie .NET. Dans l'exemple ci-dessous, lorsque la minuterie arrive à son terme, la méthode `MoveObject` sera appelée par le runtime Actors pour garantir l’accès concurrentiel en alternance, ce qui signifie qu'aucune autre méthode d’acteur ou rappels de minuterie ne sera en cours avant que ce rappel ne soit terminé.

```csharp
class VisualObjectActor : Actor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    public override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // callback method
            state,                          // state to pass to callback method
            TimeSpan.FromMilliseconds(15),  // amount of time to delay before callback is invoked
            TimeSpan.FromMilliseconds(15)); // time interval between invocation of the callback method

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return TaskDone.Done;
    }
}
```

La période suivante de la minuterie démarre après la fin du rappel. Cela implique que la minuterie s’arrête pendant l’exécution du rappel puis démarre lorsque le rappel est terminé.

Le runtime Actors enregistre l'état de l’acteur lorsque le rappel se termine si l'acteur est un acteur avec état, comme dans l'exemple ci-dessus. Si une erreur se produit lors de l'enregistrement de l'état, cet objet acteur sera désactivé et une nouvelle instance sera activée. Une méthode de rappel qui ne modifie pas l'état de l'acteur peut être enregistrée comme un rappel de minuterie en lecture seule en spécifiant l'attribut Readonly sur le rappel de la minuterie, comme décrit dans la section sur les [méthodes readonly](service-fabric-reliable-actors-introduction.md#readonly-methods).

Toutes les minuteries sont arrêtées lorsque l'acteur est désactivé dans le cadre du nettoyage de la mémoire, et aucun rappel de minuterie n'est appelé après cela. En outre, le runtime Actors ne conserve aucune information concernant les minuteries qui étaient exécutées avant la désactivation. C'est à l'acteur d'inscrire toutes les minuteries dont il a besoin lors d'une réactivation ultérieure. Pour plus d'informations, consultez la section sur le [nettoyage de la mémoire d'acteur](service-fabric-reliable-actors-lifecycle.md).

## Rappels de l'acteur
Les rappels sont un mécanisme permettant de déclencher des rappels persistants sur un acteur à certaines heures. Leur fonctionnalité est semblable aux minuteries, mais contrairement aux minuteries, les rappels sont déclenchés en toutes circonstances jusqu'à ce que le rappel soit explicitement désinscrit par l'acteur. Plus précisément, les rappels sont déclenchés lors des désactivations d'acteur et des basculements car le runtime Actors conserve des informations sur les rappels de l'acteur.

Les rappels sont uniquement pris en charge pour les acteurs avec état. Les acteurs sans état ne peuvent pas utiliser les rappels. Les fournisseurs d'état d'acteur sont responsables du stockage des informations concernant les rappels qui ont été enregistrées par les acteurs.

Pour enregistrer un rappel, un acteur appelle la méthode `RegisterReminder` fournie dans la classe de base, comme illustré dans l'exemple ci-dessous.

```csharp
string task = "Pay cell phone bill";
int amountInDollars = 100;
Task<IActorReminder> reminderRegistration = RegisterReminder(
                                                task,
                                                BitConverter.GetBytes(amountInDollars),
                                                TimeSpan.FromDays(3),
                                                TimeSpan.FromDays(1),
                                                ActorReminderAttributes.None);
```

Dans l'exemple ci-dessus, `"Pay cell phone bill"` est le nom du rappel, qui est une chaîne que l'acteur utilise pour identifier de façon unique un rappel. `BitConverter.GetBytes(amountInDollars)` est le contexte associé au rappel. Il sera renvoyé à l'acteur en tant qu'argument vers le rappel, soit `IRemindable.ReceiveReminderAsync`.

Les acteurs qui utilisent des rappels doivent implémenter l'interface `IRemindable`, comme indiqué dans l'exemple ci-dessous.

```csharp
public class ToDoListActor : Actor<ToDoList>, IToDoListActor, IRemindable
{
    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```

Lorsqu'un rappel est déclenché, le runtime Fabric Actors appellera la méthode `ReceiveReminderAsync` sur l'acteur. Un acteur peut enregistrer plusieurs rappels et la méthode `ReceiveReminderAsync` est appelée à chaque fois qu'un de ces rappels est déclenché. L'acteur peut ensuite utiliser le nom du rappel qui est envoyé à la méthode `ReceiveReminderAsync` pour identifier le rappel qui a été déclenché.

Le runtime Actors enregistre l'état de l'acteur à la fin de l'appel `ReceiveReminderAsync`. Si une erreur se produit lors de l'enregistrement de l'état, cet objet acteur sera désactivé et une nouvelle instance sera activée. Pour spécifier que l'état ne doit pas être enregistré à la fin du rappel, l'indicateur `ActorReminderAttributes.ReadOnly` peut être défini dans le paramètre `attributes` lorsque la méthode `RegisterReminder` est appelée pour créer le rappel.

Pour désinscrire un rappel, la méthode `UnregisterReminder` doit être appelée, comme illustré dans l'exemple ci-dessous.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Comme indiqué ci-dessus, la méthode `UnregisterReminder` accepte une interface `IActorReminder`. La classe de base de l'acteur prend en charge une méthode `GetReminder` qui peut être utilisée pour récupérer l'interface `IActorReminder` en passant le nom du rappel. C'est pratique car l'acteur n'a pas besoin de conserver l'interface `IActorReminder` renvoyée par l'appel de la méthode `RegisterReminder`.

<!---HONumber=Oct15_HO3-->