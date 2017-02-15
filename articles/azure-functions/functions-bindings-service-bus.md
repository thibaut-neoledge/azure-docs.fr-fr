---
title: "Déclencheurs et liaisons Service Bus Azure Functions | Microsoft Docs"
description: "Découvrez comment utiliser des déclencheurs et des liaisons Azure Service Bus dans Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: 7ff4286d6006eb362b6fba705e2afca1fd872f72
ms.openlocfilehash: afefa826577999897a537add7a6c6301144fa38c


---
# <a name="azure-functions-service-bus-bindings"></a>Liaisons Service Bus Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons Azure Service Bus dans Azure Functions. Azure Functions prend en charge des liaisons de déclencheur et de sortie pour les files d’attente et les rubriques Notification Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>Déclencheur Service Bus
Utilisez le déclencheur Service Bus pour répondre aux messages provenant d'une file d’attente ou d'une rubrique Service Bus. 

Les déclencheurs de file d’attente et de rubrique Notification Hubs utilisent les objets JSON suivants dans le tableau `bindings` de function.json :

* Déclencheur de *file d’attente* :

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* Déclencheur de *rubrique* :

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

Notez les points suivants :

* Pour `connection`, [créez un paramètre d’application dans votre application de fonction]() qui contient la chaîne de connexion à votre espace de noms Service Hub, puis spécifiez le nom du paramètre d’application dans la propriété `connection` de votre déclencheur. Vous obtenez la chaîne de connexion en suivant les étapes indiquées à la section [Obtenir les informations d’identification de gestion](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  La chaîne de connexion doit être destinée à un espace de noms Service Bus, et non limitée à une file d’attente ou une rubrique spécifique.
  Si vous laissez `connection` vide, le déclencheur suppose qu’une chaîne de connexion Service Bus par défaut est spécifiée dans le paramètre d’application nommé `AzureWebJobsServiceBus`.
* Pour `accessRights`, les valeurs disponibles sont `manage` et `listen`. La valeur par défaut est `manage`, ce qui indique que `connection` a l'autorisation **Gérer**. Si vous utilisez une chaîne de connexion qui n’a pas l'autorisation **Gérer**, définissez `accessRights` sur `listen`. Sinon, le runtime Functions pourrait essayer et faire échouer les opérations qui nécessitent des droits de gestion.

## <a name="trigger-behavior"></a>Comportement du déclencheur
* **Thread unique** - Par défaut, le runtime Functions traite plusieurs messages simultanément. Pour que le runtime ne traite qu’un message de file d’attente ou de rubrique à la fois, définissez `serviceBus.maxConcurrrentCalls` sur 1 dans *host.json*. 
  Pour plus d’informations sur *host.json*, consultez [Structure des dossiers](functions-reference.md#folder-structure) et [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).
* **Gestion des messages incohérents** - Service Bus assure sa propre gestion des messages incohérents. Il est impossible de la contrôler ou de la configurer dans la configuration ou le code d’Azure Functions. 
* **Comportement de PeekLock** - Le runtime Functions reçoit un message en mode [`PeekLock`](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode) et appelle `Complete` sur le message si la fonction se termine correctement. Si la fonction échoue, il appelle `Abandon`. 
  Si la fonction s’exécute au-delà du délai imparti à `PeekLock`, le verrou est automatiquement renouvelé.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utilisation du déclencheur
Cette section vous montre comment utiliser votre déclencheur Service Hub dans le code de votre fonction. 

Dans C# and F#, le message du déclencheur Service Bus peut être désérialisé vers l’un des types d'entrée suivants :

* `string` - utile pour les messages de type chaîne
* `byte[]` - utile pour les données binaires
* N’importe quel [objet](https://msdn.microsoft.com/library/system.object.aspx) : utile pour les données JSON sérialisées.
  Si vous déclarez un type d’entrée personnalisé (par exemple, `FooType`), Azure Functions tente de désérialiser les données JSON dans le type spécifié.
* `BrokeredMessage` - vous donne le message désérialisé avec la méthode [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx).

Dans Node.js, le message du déclencheur Service Bus est transmis à la fonction en tant que chaîne ou, dans le cas d'un message JSON, en tant qu'objet JavaScript.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Exemple de déclencheur
Supposons que vous avez le code function.json suivant :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Consultez l'exemple de code spécifique au langage qui traite un message de file d’attente Service Bus.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemple de déclencheur en C# #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Exemple de déclencheur en F# #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemple de déclencheur en Node.js

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>Liaison de sortie Service Bus
La sortie de file d’attente et de rubrique Notification Hubs utilise les objets JSON suivants dans le tableau `bindings` de function.json :

* Sortie de *file d’attente* :

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>"
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* Sortie de *rubrique* :

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>"
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

Notez les points suivants :

* Pour `connection`, [créez un paramètre d’application dans votre application de fonction]() qui contient la chaîne de connexion à votre espace de noms Service Hub, puis spécifiez le nom du paramètre d’application dans la propriété `connection` de votre liaison de sortie. Vous obtenez la chaîne de connexion en suivant les étapes indiquées à la section [Obtenir les informations d’identification de gestion](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  La chaîne de connexion doit être destinée à un espace de noms Service Bus, et non limitée à une file d’attente ou une rubrique spécifique.
  Si vous laissez `connection` vide, la liaison de sortie suppose qu’une chaîne de connexion Service Bus par défaut est spécifiée dans le paramètre d’application nommé `AzureWebJobsServiceBus`.
* Pour `accessRights`, les valeurs disponibles sont `manage` et `listen`. La valeur par défaut est `manage`, ce qui indique que `connection` a l'autorisation **Gérer**. Si vous utilisez une chaîne de connexion qui n’a pas l'autorisation **Gérer**, définissez `accessRights` sur `listen`. Sinon, le runtime Functions pourrait essayer et faire échouer les opérations qui nécessitent des droits de gestion.

<a name="outputusage"></a>

## <a name="output-usage"></a>Utilisation en sortie
Dans C# et F#, Azure Functions peut créer un message de file d’attente Service Bus à partir d’un des types suivants :

* N’importe quel [objet](https://msdn.microsoft.com/library/system.object.aspx) - la définition du paramètre ressemble à `out T paramName` (C#).
  Functions désérialise l’objet dans un message JSON. Si la valeur de sortie est null lorsque la fonction se termine, Functions crée le message avec un objet null.
* `string` - La définition du paramètre ressemble à `out string paraName` (C#). Si la valeur du paramètre n'est pas null lorsque la fonction se termine, Functions crée un message.
* `byte[]` - La définition du paramètre ressemble à `out byte[] paraName` (C#). Si la valeur du paramètre n'est pas null lorsque la fonction se termine, Functions crée un message.
* `BrokeredMessage` - La définition du paramètre ressemble à `out BrokeredMessage paraName` (C#). Si la valeur du paramètre n'est pas null lorsque la fonction se termine, Functions crée un message.

Pour créer plusieurs messages dans une fonction C#, vous pouvez utiliser `ICollector<T>` ou `IAsyncCollector<T>`. Un message est créé quand vous appelez la méthode `Add` .

Dans Node.js, vous pouvez affecter une chaîne, un tableau d’octets ou un objet Javascript (désérialisé dans JSON) à `context.binding.<paramName>`.

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemple de sortie
Supposons le code function.json suivant, qui définit une sortie de file d'attente Service Bus :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Consultez l'exemple spécifique au langage qui envoie un message à la file d’attente Service Bus.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemple de sortie en C# #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Ou, pour créer plusieurs messages :

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Exemple de sortie en F# #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Exemple de sortie en Node.js

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Ou, pour créer plusieurs messages :

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Feb17_HO2-->


