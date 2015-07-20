<properties
   pageTitle="Vue d'ensemble d'Azure Service Fabric Actors"
   description="Présentation du modèle de programmation Azure Service Fabric Actors"
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
   ms.date="03/02/2015"
   ms.author="claudioc"/>

# Présentation d'Azure Service Fabric Actors
Azure Service Fabric Actors est un modèle de programmation d'acteur pour [Service Fabric](service-fabric-technical-overview.md). Service Fabric est une plateforme permettant de créer, à la fois pour le cloud et localement, des applications évolutives, extrêmement fiables et faciles à développer et à gérer.

Fabric Actors fournit un modèle d'acteur asynchrone, monothread. Un acteur représente une unité d'état et de calcul. Les acteurs sont distribués sur le cluster pour obtenir une extensibilité élevée. Quand un processus qui héberge l'acteur échoue, le système recrée l'acteur dans un autre processus. De même, quand le nœud sur lequel s'exécute le processus hôte de l'acteur échoue, le système recrée l'acteur dans un processus hôte sur un autre nœud. Le runtime Actors s'appuie sur le magasin distribué fourni par la plateforme Service Fabric sous-jacente pour permettre une gestion d'état hautement disponible et cohérente pour les acteurs. De cette façon, le développement et la maintenance des applications cloud distribuées basées sur les acteurs devient extrêmement simple.

## Acteurs
Les acteurs sont des composants isolés monothread qui encapsulent l'état et le comportement. Ils sont semblables aux objets .NET et, par conséquent, fournissent un modèle de programmation naturel aux développeurs. Chaque acteur est une instance d'un type d'acteur, de la même façon qu'un objet .NET est une instance d'un type .NET. Par exemple, un type d'acteur peut implémenter les fonctionnalités d'une calculatrice, et plusieurs acteurs de ce type peuvent être distribués sur différents nœuds d'un cluster. Chaque acteur de ce type est identifié de façon unique par un ID d'acteur.

Les acteurs interagissent avec le reste du système, y compris les autres acteurs, en transmettant des messages asynchrones sur le modèle demande-réponse. Ces interactions sont définies dans une interface en tant que méthodes asynchrones. Par exemple, l'interface pour un type d'acteur qui implémente les fonctionnalités d'une calculatrice peut être défini comme suit.

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

Pour chaque méthode d'interface, les arguments et le type de résultat de la tâche qu'elle retourne doivent être [sérialisables en contrat de données](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

L'implémentation de ces méthodes n'implique pas de gérer le verrouillage ou d'autres problèmes d'accès concurrentiel, car le runtime Actors fournit un accès concurrentiel en alternance pour les méthodes d'acteur. Pour plus d'informations, consultez la section [Accès concurrentiel](#concurrency).

> [AZURE.TIP]Le runtime Fabric Actors émet des [événements et compteurs de performances liés aux méthodes d'acteur](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Ces derniers sont utiles dans les diagnostics et la surveillance des performances.

Voici les règles importantes en matière de méthodes d'interface d’acteur : -Les méthodes d'interface d’acteur ne peuvent pas être surchargées. - Les méthodes d’interface d’acteur ne doivent pas avoir de paramètres de sortie, de référence ou facultatifs.

## Communication d'acteur
L'API du client Actors assure la communication entre une instance d'acteur et un client d'acteur. Pour communiquer avec un acteur, un client crée un objet proxy d'acteur qui implémente l'interface d'acteur. Le client interagit avec l'acteur en appelant des méthodes sur l'objet proxy. Le proxy d'acteur peut être utilisé aussi bien pour la communication client-acteur que la communication acteur-acteur. Dans le cadre de notre exemple de calculatrice, le code client pour un acteur calculatrice peut être écrit comme ci-dessous :

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Comme indiqué dans l'exemple ci-dessus, deux informations ont été utilisées pour créer l'objet proxy d'acteur : l'ID d'acteur et le nom d'application. L'ID d'acteur est un identificateur qui identifie l'acteur de façon unique. Le nom d'application est le nom de l'[application Service Fabric](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors), qui est la forme sous laquelle l'acteur est déployé.

Les acteurs sont virtuels, ce qui signifie qu'ils existent toujours. Vous n'avez pas besoin de les créer ou les détruire explicitement. Le runtime Actors active automatiquement un acteur la première fois qu'il reçoit une demande pour cet acteur. Si un acteur n'est pas utilisé pendant un certain temps, le runtime Actors le soumet à un Garbage Collection et l'active ultérieurement si nécessaire. Pour plus d'informations, consultez la section [Cycle de vide des acteurs et Garbage Collection](service-fabric-reliable-actors-lifecycle.md).

L'API du client Actors fournit également la transparence de l'emplacement et le basculement. La classe `ActorProxy` côté client effectue la résolution nécessaire et localise la [partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) du service d'acteur où l'acteur avec l'ID spécifié est hébergé, puis ouvre une communication de canal avec elle. La classe `ActorProxy` retente la communication en cas d'échec et pendant les basculements. Cela signifie qu'il est possible pour une implémentation d'acteur d'obtenir des messages en double du même client.

## Accès concurrentiel
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

Le runtime Actors autorise la réentrance par défaut. Cela signifie que si une méthode de l'acteur A appelle une méthode sur l'acteur B, qui appelle à son tour une autre méthode sur l'acteur A, cette méthode peut s'exécuter, car elle fait partie du même contexte de chaîne d'appel logique. Tous les appels du minuteur et du rappel démarrent avec le nouveau contexte d'appel logique. Pour plus d'informations, consultez la section [Réentrance](service-fabric-reliable-actors-reentrancy.md).

Le runtime Actors fournit ces garanties d'accès concurrentiel dans les situations où il contrôle l'appel de ces méthodes. Par exemple, il fournit ces garanties pour les appels de méthode effectués en réponse à la réception d'une demande du client et pour les rappels de minuterie et de rappel. Toutefois, si le code de l'acteur appelle directement ces méthodes en dehors des mécanismes fournis par le runtime Actors, celui-ci ne peut pas fournir de garanties d'accès concurrentiel. Par exemple, si la méthode est appelée dans le contexte d'une tâche qui n'est pas associée à la tâche retournée par les méthodes d'acteur, ou si elle est appelée à partir d'un thread créé par l'acteur de son côté, le runtime ne peut pas fournir de garantie d'accès concurrentiel. Par conséquent, pour effectuer des opérations d'arrière-plan, les acteurs doivent utiliser les [minuteurs d'acteur ou les rappels d'acteur](service-fabric-reliable-actors-timers-reminders.md), qui respectent l'accès concurrentiel en alternance.

> [AZURE.TIP]Le runtime Fabric Actors émet des [événements et compteurs de performances liés à l'accès concurrentiel](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters). Ces derniers sont utiles dans les diagnostics et la surveillance des performances.

## Gestion des états d'acteur
Fabric Actors vous permet de créer des acteurs avec ou sans état.

### Acteurs sans état
Les acteurs sans état, comme leur nom l'indique, n'ont pas d'état et sont gérés par le runtime Actors. Les acteurs sans état dérivent de la classe de base Actor. Ils peuvent avoir des variables membres, qui sont conservées pendant toute la durée de vie de l'acteur, comme tous les autres types .NET. Toutefois, si cette instance d'acteur fait l'objet d'un Garbage Collection après avoir été inactive pendant un certain temps, elle perd l'état stocké dans ses variables membres. De même, l'état peut être perdu en cas de basculement, ce qui peut se produire dans les situations telles que les mises à niveau, les opérations d'équilibrage des ressources, ou l'échec d'un processus d'acteur ou du nœud hébergeant le processus d'acteur.

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
Les acteurs avec état ont un état qui doit être conservé dans les opérations de basculement et de Garbage Collection d'acteur. Les acteurs avec état dérivent de la classe de base `Actor<TState>`, où `TState` est le type de l'état qui doit être conservé dans les opérations de Garbage Collection et de basculement. L'état est accessible dans les méthodes d'acteur via la propriété `State` sur la classe de base `Actor<TState>`. Voici un exemple d'un acteur avec état accédant à l'état.

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

Le type d'état de l'acteur doit être [sérialisable en contrat de données](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [Remarque] : reportez-vous à l'article [Remarques des acteurs fiables sur la sérialisation](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) pour plus d'informations sur la façon dont les interfaces et les types d'état d'acteur doivent être définis.

#### Fournisseurs d'état d'acteur
Le stockage et la récupération de l'état sont proposés par un fournisseur d'état d'acteur. Le fournisseur d'état peut être configuré par acteur ou pour tous les acteurs au sein d'un assembly par l'attribut spécifique du fournisseur d'état. Certains fournisseurs d'état d'acteur par défaut sont inclus dans le runtime Actors. La durabilité et la fiabilité de l'état sont déterminées par les garanties offertes par le fournisseur d'état. Quand un acteur est activé, son état est chargé en mémoire. Quand une méthode d'acteur se termine, l'état modifié est automatiquement enregistré par le runtime Actors en appelant une méthode sur le fournisseur d'état. En cas d'erreur pendant l'enregistrement de l'état, le runtime Actors recycle cette instance d'acteur. Une nouvelle instance d'acteur est créée et chargée avec le dernier état cohérent du fournisseur d'état.

Par défaut, un acteur avec état utilise un fournisseur d'état d'acteur du stockage clé-valeur. Ce fournisseur d'état repose sur le stockage clé-valeur distribué fourni par la plateforme Service Fabric. Pour plus d'informations, consultez la rubrique sur [le choix du fournisseur d'état](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP]Le runtime Fabric Actors émet des [événements et compteurs de performances liés à la gestion des états d'acteur](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters). Ces derniers sont utiles dans les diagnostics et la surveillance des performances.

#### Méthodes en lecture seule
Par défaut, le runtime Actors enregistre l'état d'acteur à la fin de l'appel d'une méthode d'acteur, d'un rappel de rappel et d'un rappel de minuterie. Aucun autre appel d'acteur n'est autorisé tant que l'enregistrement de l'état n'est pas terminé. Selon le fournisseur d'état, l'enregistrement de l'état peut prendre un certain temps et aucun autre appel d'acteur n'est autorisé dans l'acteur pendant cette période.

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

[Configuration de l'acteur KVSActorStateProvider](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[Diagnostics et surveillance des performances d'acteur](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
 

<!---HONumber=July15_HO2-->