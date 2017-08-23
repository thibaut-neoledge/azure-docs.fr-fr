---
title: "Liaisons de stockage de file d’attente d’Azure Functions | Microsoft Docs"
description: "Découvrez comment utiliser des déclencheurs et des liaisons Azure Storage dans Azure Functions."
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 94d1d4c243bede354ae3deba7fbf5da0652567cb
ms.openlocfilehash: f39f674bf576a2661a0e03710b9005b0515b3aa5
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---
# <a name="azure-functions-queue-storage-bindings"></a>Liaisons de stockage de file d’attente d’Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons Stockage File d’attente Azure dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur et de sortie pour les files d’attente Azure. Pour les fonctionnalités qui sont disponibles dans toutes les liaisons, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>Déclencheur de stockage de file d’attente
Le déclencheur de Stockage File d’attente Azure vous permet de surveiller les nouveaux messages d’un stockage de file d’attente et d’agir en conséquence. 

Définissez un déclencheur de file d’attente en utilisant l’onglet **Intégrer** dans le portail Functions. Le portail crée la définition suivante dans la section **bindings** de *function.json* :

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* La propriété `connection` doit contenir le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. Dans le portail Azure, l’éditeur standard de l’onglet **Intégrer** configure ce paramètre d’application pour vous quand vous sélectionnez un compte de stockage.

Vous pouvez fournir des paramètres supplémentaires dans un [fichier host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) pour optimiser les déclencheurs de stockage de file d’attente. Par exemple, vous pouvez changer l’intervalle d’interrogation de la file d’attente dans host.json.

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>Utilisation d’un déclencheur de file d’attente
Dans les fonctions Node.js, accédez aux données de la file d’attente en utilisant `context.bindings.<name>`.


Dans les fonctions .NET, accédez à la charge utile de la file d’attente en utilisant un paramètre de méthode comme `CloudQueueMessage paramName`. Ici, `paramName` est la valeur que vous avez spécifiée dans la [configuration du déclencheur](#trigger). Le message de file d’attente peut être désérialisé vers l’un des types suivants :

* Objet POCO. À utiliser si la charge utile de la file d’attente est un objet JSON. Le runtime Functions désérialise la charge utile dans l’objet POCO. 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Métadonnées de déclencheur de file d’attente
Le déclencheur de file d’attente fournit plusieurs propriétés de métadonnées. Ces propriétés peuvent être utilisées dans les expressions de liaison dans d’autres liaisons ou en tant que paramètres dans votre code. Les valeurs ont la même sémantique que [`CloudQueueMessage`].

* **QueueTrigger** : charge utile de la file d’attente (s’il s’agit d’une chaîne valide)
* **DequeueCount** : type `int`. Nombre de fois que ce message a été enlevé de la file d’attente.
* **ExpirationTime** : type `DateTimeOffset?`. Heure à laquelle le message expire.
* **Id**:type `string`. ID de message de la file d’attente.
* **InsertionTime** : type `DateTimeOffset?`. Heure à laquelle le message a été ajouté à la file d’attente.
* **NextVisibleTime** : tapez `DateTimeOffset?`. Heure à laquelle le message sera de nouveau visible.
* **PopReceipt** : type `string`. Réception pop du message.

Découvrez comment utiliser les métadonnées de file d’attente dans [l’exemple de déclencheur](#triggersample).

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Exemple de déclencheur
Supposons le function.json suivant, qui définit un déclencheur de file d’attente :

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

Consultez l’exemple dans le langage de votre choix pour voir comment récupérer et enregistrer les métadonnées de file d’attente.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemple de déclencheur en C# #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemple de déclencheur en Node.js

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>Gestion des messages de file d’attente incohérents
En cas d’échec d’une fonction de déclenchement de file d’attente, Azure Functions réessaie cette fonction jusqu’à cinq fois (première tentative comprise) pour un message de file d’attente donné. Si les cinq tentatives échouent, le runtime Functions ajoute un message à une file d’attente de stockage nommée *&lt;nom_file_attente_origine>-poison*. Vous pouvez écrire une fonction pour traiter les messages de la file d’attente de messages incohérents en les consignant dans un journal ou en envoyant une notification signalant qu’une attention manuelle est nécessaire. 

Pour gérer manuellement les messages incohérents, vérifiez le `dequeueCount` du message de la file d’attente (voir [Métadonnées du déclencheur de file d’attente](#meta)).

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>Liaison de sortie de stockage de file d’attente
La liaison de sortie de stockage de file d’attente vous permet d’écrire des messages dans une file d’attente. 

Définissez une liaison de sortie de file d’attente en utilisant l’onglet **Intégrer** dans le portail Functions. Le portail crée la définition suivante dans la section **bindings** de *function.json* :

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* La propriété `connection` doit contenir le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. Dans le portail Azure, l’éditeur standard de l’onglet **Intégrer** configure ce paramètre d’application pour vous quand vous sélectionnez un compte de stockage.

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>Utilisation d’une liaison de sortie de file d’attente
Dans les fonctions Node.js, vous accédez à la file d’attente de sortie en utilisant `context.bindings.<name>`.

Dans les fonctions .NET, vous pouvez définir une sortie vers les types suivants. Quand il existe un paramètre de type `T`, `T` doit être un des types de sortie pris en charge, comme `string` ou un objet POCO.

* `out T` (sérialisé au format JSON)
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

Vous pouvez également utiliser le type de retour de la méthode comme liaison de sortie.

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>Exemple de sortie de file d’attente
Le *function.json* suivant définit un déclencheur HTTP avec une liaison de sortie de file d’attente :

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

Consultez l’exemple spécifique au langage qui génère un message de file d’attente avec la charge utile HTTP entrante.

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>Exemple de sortie de file d’attente en C# #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Pour envoyer plusieurs messages, utilisez un `ICollector` :

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Exemple de sortie de file d’attente en Node.js

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Ou, pour envoyer plusieurs messages,

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un exemple de fonction utilisant des déclencheurs et de liaisons de file d’attente de stockage, consultez [Créer une fonction Azure connectée à un service Azure](functions-create-an-azure-connected-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

[« CloudQueueMessage »]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage

