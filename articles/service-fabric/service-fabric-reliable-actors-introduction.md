<properties
   pageTitle="Vue d'ensemble des Acteurs fiables Service Fabric"
   description="Présentation du modèle de programmation Acteurs fiables Service Fabric"
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
   ms.author="claudioc"/>


# Présentation des Acteurs fiables Service Fabric
L’API avec acteurs fiables constitue l’une des deux infrastructures générales fournies par [Service Fabric](service-fabric-technical-overview.md), en plus de l’[API avec services fiables](service-fabric-reliable-services-introduction.md).

Selon le modèle d'acteur, l'API avec acteurs fiables fournit un modèle de programmation monothread asynchrone qui simplifie votre code tout en bénéficiant des garanties de fiabilité et d’évolutivité offertes par Service Fabric.

## Acteurs
Les acteurs sont des composants monothread isolés qui encapsulent l'état et le comportement. Ils sont semblables aux objets .NET et, par conséquent, fournissent un modèle de programmation naturel. Chaque acteur est une instance d'un type d'acteur, de la même façon qu'un objet .NET est une instance d'un type .NET. Par exemple, un type d'acteur peut implémenter les fonctionnalités d'une calculatrice, et plusieurs acteurs de ce type peuvent être distribués sur différents nœuds d'un cluster. Chaque acteur de ce type est identifié de façon unique par un ID d'acteur.

## Définition et implémentation d'interfaces d’acteur

Les acteurs interagissent avec le reste du système, y compris les autres acteurs, en transmettant des messages asynchrones sur le modèle demande-réponse. Ces interactions sont définies dans une interface en tant que méthodes asynchrones. Par exemple, l'interface pour un type d'acteur qui implémente les fonctionnalités d'une calculatrice peut être défini comme suit :

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

Un type d'acteur peut implémenter l'interface ci-dessus comme suit :

```csharp
public class CalculatorActor : Actor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

Comme les appels de méthode et leurs réponses aboutissent à des demandes réseau sur le cluster, les arguments et le type de résultat de la tâche renvoyés doivent être sérialisables par la plateforme. En particulier, ils doivent être [sérialisables en contrat de données](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [AZURE.TIP]Le runtime Fabric Actors émet des [événements et compteurs de performances liés aux méthodes d'acteur](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Ces derniers sont utiles dans les diagnostics et la surveillance des performances.

Voici les règles importantes en matière de méthodes d'interface d’acteur : -Les méthodes d'interface d’acteur ne peuvent pas être surchargées. - Les méthodes d’interface d’acteur ne doivent pas avoir de paramètres de sortie, de référence ou facultatifs.

## Communication d'acteur
### Le proxy d'acteur
L'API du client Actors assure la communication entre une instance d'acteur et un client d'acteur. Pour communiquer avec un acteur, un client crée un objet proxy d'acteur qui implémente l'interface d'acteur. Le client interagit avec l'acteur en appelant des méthodes sur l'objet proxy. Le proxy d'acteur peut être utilisé aussi bien pour la communication client-acteur que la communication acteur-acteur. Dans le cadre de notre exemple de calculatrice, le code client pour un acteur calculatrice peut être écrit comme ci-dessous :

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Notez les deux informations utilisées pour créer l'objet proxy d'acteur : l'ID d'acteur et le nom d'application. L'ID d'acteur est un identificateur qui identifie de façon unique l'acteur, tandis que le nom d'application identifie l’[application Fabric Service](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) dans laquelle l'acteur est déployé.

### Durée de vie de l’acteur

Les acteurs Service Fabric sont virtuels, ce qui signifie que leur durée de vie n'est pas liée à leur représentation en mémoire. En conséquence, ils n’ont pas besoin d’être explicitement créés ou détruits. Le runtime Actors active automatiquement un acteur la première fois qu'il reçoit une demande pour cet acteur. Si un acteur n'est pas utilisé pendant un certain temps, le runtime Actors nettoiera l'objet en mémoire en tenant compte de l'existence de l'acteur si celui-ci doit être réactivé ultérieurement. Pour plus de détails, consultez la rubrique [Cycle de vie des acteurs et Garbage Collection](service-fabric-reliable-actors-lifecycle.md).

### Transparence des emplacements et basculement automatique

Pour garantir une fiabilité et une évolutivité élevées, Service Fabric distribue les acteurs dans l'ensemble du cluster et les migre automatiquement à partir des nœuds ayant échoué vers des nœuds sains selon les besoins. La classe `ActorProxy` côté client effectue la résolution nécessaire pour localiser l'acteur par ID [partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) et ouvrir un canal de communication avec lui. La classe `ActorProxy` retente également la communication en cas d'échec et pendant les basculements. Cela garantit que les messages seront remis correctement malgré la présence d'erreurs, mais cela signifie également qu’une implémentation de l'acteur peut recevoir des messages en double du même client.

## Accès concurrentiel
### Accès concurrentiel en alternance

Le runtime Actors fournit un simple accès concurrentiel en alternance pour les méthodes d'acteur. Cela signifie qu'un seul thread peut être actif à l'intérieur du code d'acteur à tout moment.

Un tour consiste en l'exécution complète d'une méthode d'acteur en réponse à la demande d'autres acteurs ou clients, ou l'exécution complète d'un rappel de [minuterie/rappel](service-fabric-reliable-actors-timers-reminders.md). Bien que ces méthodes et ces rappels soient asynchrones, le runtime Actors ne les entremêle pas. Un tour doit être totalement terminé avant qu'un nouveau tour soit autorisé. En d'autres termes, une méthode d'acteur ou un rappel de minuterie/rappel en cours d'exécution doit être totalement terminé avant qu'un nouvel appel à une méthode ou qu'un rappel soit autorisé. Une méthode ou un rappel est considéré terminé si l'exécution a été retournée depuis la méthode ou le rappel et que la tâche retournée par la méthode ou le rappel est terminée. Il est important de souligner que cet accès concurrentiel en alternance est respecté même dans les différents rappels, minuteries et méthodes.

Le runtime Actors applique un accès concurrentiel en alternance en acquérant un verrou par acteur au début d'un tour et en le relâchant à la fin du tour. Par conséquent, l'accès concurrentiel en alternance est appliqué sur une base par acteur et non entre acteurs. Les méthodes d'acteur et les rappels de minuterie/rappel peuvent s'exécuter simultanément pour le compte de différents acteurs.

L'exemple suivant illustre les concepts ci-dessus : Prenons l'exemple d'un type d'acteur qui implémente deux méthodes asynchrones (par exemple, *Method1* et *Method2*), une minuterie et un rappel. Le diagramme suivant montre un exemple de chronologie de l'exécution de ces méthodes et rappels pour le compte de deux acteurs, *ActorId1* et *ActorId2*, qui appartiennent à ce type d'acteur.

![][1]

Les conventions suivies par le diagramme ci-dessus sont les suivantes :

- Chaque ligne verticale indique le flux logique d'exécution d'une méthode ou d'un rappel pour le compte d'un acteur spécifique.
- Les événements marqués sur chaque ligne verticale se produisent dans l'ordre chronologique, les nouveaux événements se trouvant en dessous des plus anciens.
- Différentes couleurs sont utilisées pour les chronologies des différents acteurs.
- La mise en surbrillance est utilisée pour indiquer la durée pendant laquelle le verrou par acteur est maintenu pour le compte d'une méthode ou d'un rappel.

Voici les points importants du diagramme ci-dessus :

- Quand *Method1* s'exécute pour le compte d'*ActorId2* en réponse à la demande du client *xyz789*, une autre demande du client *abc123* arrive et requiert également que *Method1* soit exécutée par *ActorId2*. Toutefois, la dernière exécution de *Method1* ne commence pas tant que l'exécution précédente n'est pas terminée. De même, un rappel enregistré par *ActorId2* se déclenche lors de l'exécution de *Method1* en réponse à la demande du client *xyz789*. Le rappel de rappel est exécuté uniquement une fois que les deux exécutions de *Method1* sont terminées. Cela s'explique par l'application de l'accès concurrentiel en alternance à *ActorId2*.
- De même, l'accès concurrentiel en alternance est également appliqué à *ActorId1*, comme illustré par l'exécution en série de *Method1*, *Method2* et du rappel de minuterie pour le compte d'*ActorId1*.
- L'exécution de *Method1* pour le compte d'*ActorId1* se chevauche avec son exécution pour le compte d'*ActorId2*. En effet, l'accès concurrentiel en alternance est appliqué uniquement au sein d'un acteur et non entre les acteurs.
- Dans certaines exécutions de méthode/rappel, le `Task` retourné par la méthode/le rappel se termine après le retour de la méthode. Dans d'autres exécutions, le `Task` est déjà terminé au moment du retour de la méthode/du rappel. Dans les deux cas, le verrou par acteur n'est relâché qu'après le retour de la méthode/du rappel et la fin de `Task`.

### Réentrance

Le runtime Actors autorise la réentrance par défaut. Cela signifie que si une méthode de l'acteur A appelle une méthode sur l'acteur B, qui appelle à son tour une autre méthode sur l'acteur A, cette méthode peut s'exécuter, car elle fait partie du même contexte de chaîne d'appel logique. Tous les appels du minuteur et du rappel démarrent avec le nouveau contexte d'appel logique. Pour plus d'informations, consultez la section [Réentrance](service-fabric-reliable-actors-reentrancy.md).

### Étendue des garanties d'accès concurrentiel

Le runtime Actors fournit ces garanties d'accès concurrentiel dans les situations où il contrôle l'appel de ces méthodes. Par exemple, il fournit ces garanties pour les appels de méthode effectués en réponse à la réception d'une demande du client et pour les rappels de minuterie et de rappel. Toutefois, si le code de l'acteur appelle directement ces méthodes en dehors des mécanismes fournis par le runtime Actors, celui-ci ne peut pas fournir de garanties d'accès concurrentiel. Par exemple, si la méthode est appelée dans le contexte d'une tâche qui n'est pas associée à la tâche retournée par les méthodes d'acteur, ou si elle est appelée à partir d'un thread créé par l'acteur de son côté, le runtime ne peut pas fournir de garantie d'accès concurrentiel. Par conséquent, pour effectuer des opérations d'arrière-plan, les acteurs doivent utiliser les [minuteurs d'acteur ou les rappels d'acteur](service-fabric-reliable-actors-timers-reminders.md), qui respectent l'accès concurrentiel en alternance.

> [AZURE.TIP]Le runtime Fabric Actors émet des [événements et compteurs de performances liés à l'accès concurrentiel](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters). Ces derniers sont utiles dans les diagnostics et la surveillance des performances.

## Gestion des états d'acteur
Fabric Actors vous permet de créer des acteurs avec ou sans état.

### Acteurs sans état
Les acteurs sans état, dérivés de la classe de base ``Actor``, n'ont pas d'état et sont gérés par le runtime Actors. Leurs variables de membres sont conservées tout au long de leur durée de vie en mémoire, comme tout autre type .NET. Toutefois, lorsqu'elles sont nettoyées après une période d'inactivité, leur état est perdu. De même, un état peut être perdu en raison des basculements qui se produisent pendant les mises à niveau, les opérations d'équilibrage des ressources ou à la suite d'erreurs dans le processus de l'acteur ou de son nœud hôte.

Voici un exemple d'acteur sans état :

```csharp
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### Acteurs avec état
Les acteurs avec état ont un état qui doit être conservé dans les opérations de basculement et de Garbage Collection. Ils dérivent de la classe de base `Actor<TState>`, où `TState` est le type de l'état qui doit être conservé. L'état est accessible dans les méthodes d'acteur via la propriété `State` sur la classe de base.

Voici un exemple d'un acteur avec état accédant à l'état :

```csharp
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

L’état de l'acteur est conservé entre les opérations Garbage Collection et les basculements en le gardant persistant sur le disque et en le répliquant entre plusieurs nœuds du cluster. Cela signifie que, comme les arguments de méthode et les valeurs de retour, le type de l'état de l'acteur doit être [sérialisable en contrat de données](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [AZURE.NOTE]Reportez-vous à l'article [Remarques des acteurs fiables sur la sérialisation](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) pour plus d'informations sur la façon dont les interfaces et les types d'état d'acteur doivent être définis.

#### Fournisseurs d'état d'acteur
Le stockage et la récupération de l'état sont proposés par un fournisseur d'état d'acteur. Les fournisseurs d'état peuvent être configurés par acteur ou pour tous les acteurs au sein d'un assembly par l'attribut spécifique du fournisseur d'état. Quand un acteur est activé, son état est chargé en mémoire. Quand une méthode d'acteur se termine, l'état modifié est automatiquement enregistré par le runtime Actors en appelant une méthode sur le fournisseur d'état. Si une erreur se produit pendant l'opération d’enregistrement, le runtime Actors crée une instance de l'acteur et charge le dernier état cohérent à partir du fournisseur d'état.

Par défaut, les acteurs avec état utilisent le fournisseur d'état de l’acteur du stockage clé-valeur, qui repose sur le stockage clé-valeur distribué fourni par la plateforme Service Fabric. Pour plus d'informations, consultez la rubrique sur [le choix du fournisseur d'état](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP]Le runtime Actors émet des [événements et compteurs de performances liés à la gestion des états d'acteur](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters). Ces événements sont utiles dans les diagnostics et la surveillance des performances.

#### Méthodes en lecture seule
Par défaut, le runtime Actors enregistre l'état d'acteur à la fin de l'appel d'une méthode d'acteur, d'un rappel de rappel ou d'un rappel de minuterie. Aucun autre appel d'acteur n'est autorisé tant que l'enregistrement de l'état n'est pas terminé.

Certaines méthodes d'acteur peuvent ne pas modifier l'état, dans ce cas, la durée d'enregistrement de l'état supplémentaire peut affecter le débit général du système. Pour éviter ce problème, vous pouvez marquer en lecture seule les méthodes et les rappels de minuterie qui ne modifient pas l'état.

L'exemple ci-dessous montre comment marquer une méthode d'acteur en lecture seule à l'aide de l'attribut `Readonly`.

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

Les rappels de minuterie peuvent être marqués avec l'attribut `Readonly` de la même façon. Pour les rappels, l'indicateur de lecture seule est transmis en tant qu'argument à la méthode `RegisterReminder` appelée pour enregistrer le rappel.

## Étapes suivantes
### Concepts
[Cycle de vie des acteurs et Garbage Collection](service-fabric-reliable-actors-lifecycle.md)

[Minuteries et rappels d'acteur](service-fabric-reliable-actors-timers-reminders.md)

[Événements des acteurs](service-fabric-reliable-actors-events.md)

[Réentrance des acteurs](service-fabric-reliable-actors-reentrancy.md)

[Comment Fabric Actors utilise la plateforme Service Fabric](service-fabric-reliable-actors-platform.md)

[Configuration de l'acteur KVSActorStateProvider](service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[Diagnostics et surveillance des performances d'acteur](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png

<!---HONumber=August15_HO6-->