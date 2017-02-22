---
title: "Liaisons de file d’attente Stockage Azure Functions | Microsoft Docs"
description: "Découvrez comment utiliser des déclencheurs et des liaisons Azure Storage dans Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
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
ms.date: 01/18/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 770cac8809ab9f3d6261140333ec789ee1390daf
ms.openlocfilehash: bf9bd2a1b5acdf5a4a4f862bef693f8c60c63a33


---
# <a name="azure-functions-storage-queue-bindings"></a>Liaisons de file d’attente Stockage Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons de file d’attente Stockage Azure dans Azure Functions. Azure Functions prend en charge des liaisons de déclencheur et de sortie pour les files d’attente Stockage Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="storage-queue-trigger"></a>Déclencheur de file d’attente Stockage
Le déclencheur de file d’attente Stockage Azure vous permet de surveiller les nouveaux messages dans une file d’attente et de réagir à ces derniers. 

Le déclencheur de file d’attente Stockage d’une fonction utilise les objets JSON suivants dans le tableau `bindings` de function.json :

```json
{
    "name": "<Name of input parameter in function signature>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>",
    "type": "queueTrigger",
    "direction": "in"
}
```

`connection` doit contenir le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. Dans le Portail Azure, vous pouvez configurer ce paramètre d’application sous l’onglet **Intégrer** quand vous créez un compte de stockage ou en sélectionnez un. Pour créer manuellement ce paramètre d’application, consultez [Manage App Service settings](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings) (Gestion des paramètres App Service).

Vous pouvez fournir des [paramètres supplémentaires](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) dans un fichier host.json pour affiner les déclencheurs de file d’attente de stockage.  

### <a name="handling-poison-queue-messages"></a>Gestion des messages de file d’attente incohérents
En cas d’échec d’une fonction de déclenchement de file d’attente, Azure Functions réessaie cette fonction jusqu’à cinq fois (première tentative comprise) pour un message de file d’attente donné. Si les cinq tentatives échouent, Functions ajoute un message à une file d’attente Stockage nommée *&lt;originalqueuename>-poison*. Vous pouvez écrire une fonction pour traiter les messages de la file d’attente de messages incohérents en les consignant dans un journal ou en envoyant une notification signalant qu’une attention manuelle est nécessaire. 

Pour gérer les messages incohérents manuellement, vous pouvez obtenir le nombre de fois où un message a été récupéré pour traitement en consultant `dequeueCount` (voir [Métadonnées de déclencheur de file d’attente](#meta)).

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utilisation du déclencheur
Dans les fonctions C#, vous effectuez une liaison au message d’entrée à l’aide d’un paramètre nommé dans la signature de votre fonction, comme `<T> <name>`.
Où `T` est le type de données dans lequel vous souhaitez désérialiser les données et `paramName` le nom que vous avez spécifié dans la [liaison de déclencheur](#trigger). Dans les fonctions Node.js, vous accédez aux données blob d’entrée en utilisant `context.bindings.<name>`.

Le message de file d’attente peut être désérialisé vers l’un des types suivants :

* [Objet](https://msdn.microsoft.com/library/system.object.aspx) : utilisé pour les messages sérialisés en JSON. Lorsque vous déclarez un type d’entrée personnalisé, le runtime tente de désérialiser l’objet JSON. 
* String
* Tableau d’octets
* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx) (C# uniquement)

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Métadonnées de déclencheur de file d’attente
Vous pouvez obtenir les métadonnées de file d’attente dans votre fonction en utilisant les noms de variable suivants :

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (autre méthode pour récupérer le texte du message de file d’attente sous la forme d’une chaîne)

Découvrez comment utiliser les métadonnées de file d’attente avec [l’exemple de déclencheur](#triggersample).

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Exemple de déclencheur
Supposons le code function.json suivant, qui définit un déclencheur de file d’attente Stockage :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
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
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
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

<a name="output"></a>

## <a name="storage-queue-output-binding"></a>Liaison de sortie de file d’attente Stockage
La liaison de sortie de file d’attente Stockage Azure vous permet d’écrire des messages dans une file d’attente Stockage de votre fonction. 

La sortie de file d’attente Stockage pour une fonction utilise les objets JSON suivants dans le tableau `bindings` de function.json :

```json
{
  "name": "<Name of output parameter in function signature>",
    "queueName": "<Name of queue to write to>",
    "connection":"<Name of app setting - see below>",
  "type": "queue",
  "direction": "out"
}
```

`connection` doit contenir le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. Dans le Portail Azure, l’éditeur standard sous l’onglet **Intégrer** configure ce paramètre d’application pour vous quand vous créez un compte de stockage ou en sélectionnez un. Pour créer manuellement ce paramètre d’application, consultez [Manage App Service settings](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings) (Gestion des paramètres App Service).

<a name="outputusage"></a>

## <a name="output-usage"></a>Utilisation en sortie
Dans les fonctions C#, vous écrivez un message de file d’attente à l’aide du paramètre `out` nommé dans la signature de votre fonction, comme `out <T> <name>`. Dans ce cas, `T` est le type de données dans lequel vous souhaitez sérialiser le message et `paramName` le nom que vous avez spécifié dans la [liaison de sortie](#output). Dans les fonctions Node.js, vous accédez à la sortie en utilisant `context.bindings.<name>`.

Vous pouvez sortir un message de file d’attente en utilisant n’importe quel type de données dans votre code :

* N’importe quel [objet](https://msdn.microsoft.com/library/system.object.aspx) :`out MyCustomType paramName`  
Utilisé pour la sérialisation JSON.  Lorsque vous déclarez un type de sortie personnalisé, le runtime tente de sérialiser l’objet en JSON. Si le paramètre de sortie est null quand la fonction s’arrête, le runtime crée un message de file d’attente en tant qu’objet null.
* Chaîne : `out string paramName`  
Utilisé pour les messages de test. Le runtime crée un message uniquement si le paramètre de chaîne n’est pas null quand la fonction s’arrête.
* Tableau d’octets : `out byte[]` 

Ces types de sortie supplémentaires sont pris en charge par une fonction C# :

* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx) : `out CloudQueueMessage` 
* `ICollector<T>` ou `IAsyncCollector<T>` où `T` est l’un des types pris en charge.

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemple de sortie
Supposons le code function.json suivant, qui définit un [déclencheur de file d’attente Stockage](functions-bindings-storage-queue.md), une entrée d’objet blob Stockage et une sortie d’objet blob Stockage :

Exemple *function.json* pour une liaison de sortie de file d’attente de stockage qui utilise un déclencheur manuel et écrit l’entrée dans un message de file d’attente :

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "queue",
      "name": "myQueueItem",
      "queueName": "myqueue",
      "connection": "my_storage_connection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Consultez l’exemple dans le langage de votre choix pour voir comment écrire un message de file d’attente de sortie pour chaque message de file d’attente d’entrée.

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemple de sortie en C# #

```cs
public static void Run(string input, out string myQueueItem, TraceWriter log)
{
    myQueueItem = "New message: " + input;
}
```

Ou, pour envoyer plusieurs messages,

```cs
public static void Run(string input, ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Message 1: " + input);
    myQueueItem.Add("Message 2: " + "Some other message.");
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Exemple de sortie en Node.js

```javascript
module.exports = function(context) {
    // Define a new message for the myQueueItem output binding.
    context.bindings.myQueueItem = "new message";
    context.done();
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

Pour obtenir un exemple de fonction utilisant des liaisons et des déclencheurs de file d’attente Stockage, consultez [Créer une fonction Azure connectée à un service Azure](functions-create-an-azure-connected-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Jan17_HO3-->


