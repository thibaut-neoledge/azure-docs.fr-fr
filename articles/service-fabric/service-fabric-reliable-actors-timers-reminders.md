<properties
   pageTitle="Minuteries et rappels pour Reliable Actors | Microsoft Azure"
   description="Présentation des minuteries et rappels pour Service Fabric Reliable Actors."
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


# Minuteries et rappels d’acteur
Les acteurs peuvent planifier un travail régulier par eux-mêmes en inscrivant des minuteries ou des rappels. Cet article montre comment utiliser des minuteries et des rappels, puis explique les différences entre les deux.

## Minuteries des acteurs
Les minuteries des acteurs fournissent un wrapper simple autour de minuteries .NET pour veiller à ce que les méthodes de rappel respectent les garanties d’un accès concurrentiel en alternance fournies par le runtime Actors.

Les acteurs peuvent utiliser les méthodes `RegisterTimer` et `UnregisterTimer` sur leur classe de base pour inscrire et désinscrire leurs minuteries. L'exemple ci-dessous montre l'utilisation des API de minuterie. Les API sont très similaires à la minuterie .NET. Dans cet exemple, quand la minuterie arrive à son terme, le runtime Actors appelle la méthode `MoveObject`. Le respect par la méthode de l’accès concurrentiel en alternance est garanti. Aucune autre méthode d’acteur ou aucun autre rappel de minuterie n’est donc en cours d’exécution avant la fin de ce rappel.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
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
        return Task.FromResult(true);
    }
}
```

La période suivante de la minuterie démarre après la fin du rappel. Cela implique que la minuterie s’arrête pendant l’exécution du rappel, puis démarre quand le rappel se termine.

Le runtime Actors enregistre les modifications apportées au Gestionnaire d’état de l’acteur à la fin du rappel. Si une erreur se produit lors de l'enregistrement de l'état, cet objet acteur sera désactivé et une nouvelle instance sera activée.

Toutes les minuteries sont arrêtées quand l’acteur est désactivé dans le cadre du nettoyage de la mémoire. Aucun rappel de minuterie n’est appelé après cela. En outre, le runtime Actors ne conserve aucune information concernant les minuteries qui étaient exécutées avant la désactivation. C'est à l'acteur d'inscrire toutes les minuteries dont il a besoin lors d'une réactivation ultérieure. Pour plus d’informations, consultez la section sur le [nettoyage de la mémoire d’acteur](service-fabric-reliable-actors-lifecycle.md).

## Rappels d’acteur
Les rappels sont un mécanisme permettant de déclencher des rappels persistants sur un acteur à certaines heures. Leur fonctionnalité est semblable à celle des minuteries. Toutefois, contrairement aux minuteries, les rappels sont déclenchés en toutes circonstances jusqu’à ce que l’acteur les désinscrive ou qu’il soit supprimé explicitement. Plus précisément, les rappels sont déclenchés lors des désactivations d'acteur et des basculements car le runtime Actors conserve des informations sur les rappels de l'acteur.

Pour inscrire un rappel, un acteur appelle la méthode `RegisterReminderAsync` fournie dans la classe de base, comme illustré dans l’exemple suivant :

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),
        TimeSpan.FromDays(1));
}
```

Dans cet exemple, `"Pay cell phone bill"` est le nom du rappel. Il s’agit d’une chaîne que l’acteur utilise pour identifier de façon unique un rappel. `BitConverter.GetBytes(amountInDollars)` est le contexte associé au rappel. Il sera renvoyé à l'acteur en tant qu'argument vers le rappel, soit `IRemindable.ReceiveReminderAsync`.

Les acteurs qui utilisent des rappels doivent implémenter l’interface `IRemindable`, comme illustré dans l’exemple ci-dessous.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
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

Quand un rappel est déclenché, le runtime Reliable Actors appelle la méthode `ReceiveReminderAsync` sur l’acteur. Un acteur peut inscrire plusieurs rappels, et la méthode `ReceiveReminderAsync` est appelée chaque fois qu’un de ces rappels est déclenché. L'acteur peut ensuite utiliser le nom du rappel qui est envoyé à la méthode `ReceiveReminderAsync` pour identifier le rappel qui a été déclenché.

Le runtime Actors enregistre l’état de l’acteur à la fin de l’appel `ReceiveReminderAsync`. Si une erreur se produit lors de l'enregistrement de l'état, cet objet acteur sera désactivé et une nouvelle instance sera activée. Pour spécifier que l'état ne doit pas être enregistré à la fin du rappel, l'indicateur `ActorReminderAttributes.ReadOnly` peut être défini dans le paramètre `attributes` lorsque la méthode `RegisterReminder` est appelée pour créer le rappel.

Pour désinscrire un rappel, un acteur appelle la méthode `UnregisterReminder`, comme illustré dans l’exemple ci-dessous.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Comme indiqué ci-dessus, la méthode `UnregisterReminder` accepte une interface `IActorReminder`. La classe de base de l'acteur prend en charge une méthode `GetReminder` qui peut être utilisée pour récupérer l'interface `IActorReminder` en passant le nom du rappel. C'est pratique car l'acteur n'a pas besoin de conserver l'interface `IActorReminder` renvoyée par l'appel de la méthode `RegisterReminder`.

## Étapes suivantes
 - [Événements d’acteurs](service-fabric-reliable-actors-events.md)
 - [Réentrance des acteurs](service-fabric-reliable-actors-reentrancy.md)
 - [Diagnostics et surveillance des performances d’acteur](service-fabric-reliable-actors-diagnostics.md)
 - [Documentation de référence de l’API d’acteur](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemple de code](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0406_2016-->