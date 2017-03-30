---
title: "Utiliser des déclencheurs et des liaisons dans Azure Functions | Microsoft Docs"
description: "Découvrez comment utiliser des déclencheurs et des liaisons dans Azure Functions pour connecter l’exécution de votre code aux événements en ligne et aux services cloud."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a56d71d437814ed08b2e0a05d9acc8448f6b9ae5
ms.lasthandoff: 03/17/2017


---

# <a name="learn-how-to-work-with-triggers-and-bindings-in-azure-functions"></a>Découvrez comment utiliser des déclencheurs et des liaisons dans Azure Functions 
Cette rubrique montre comment utiliser des déclencheurs et des liaisons dans Azure Functions pour connecter votre code à divers déclencheurs, services Azure et autres services cloud. Elle comprend certaines des fonctionnalités avancées de liaison, ainsi que la syntaxe prise en charge par tous les types de liaisons.  

Pour plus d’informations sur l’utilisation d’un type spécifique de déclencheur ou liaison, consultez une des rubriques de référence suivantes :

| | | | |  
| --- | --- | --- | --- |  
| [HTTP/webhook](functions-bindings-http-webhook.md) | [Minuteur](functions-bindings-timer.md) | [Mobile Apps](functions-bindings-mobile-apps.md) | [Service Bus](functions-bindings-service-bus.md)  |  
| [Base de données de documents](functions-bindings-documentdb.md) |  [Storage Blob](functions-bindings-storage-blob.md) | [File d’attente de stockage](functions-bindings-storage-queue.md) |  [Table de stockage](functions-bindings-storage-table.md) |  
| [Hubs d'événements](functions-bindings-event-hubs.md) | [Notification Hubs](functions-bindings-notification-hubs.md) | [SendGrid](functions-bindings-sendgrid.md) | [Twilio](functions-bindings-twilio.md) |   
| | | | |  

Ces articles supposent que vous ayez lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md), ainsi que les articles de référence pour les développeurs en [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) ou [Node.js](functions-reference-node.md).

## <a name="overview"></a>Vue d'ensemble
Les déclencheurs sont les réponses aux événements utilisées pour déclencher votre code personnalisé. Ils vous permettent de répondre aux événements sur la plateforme Azure et sur site. Les liaisons représentent les métadonnées nécessaires pour connecter votre code au déclencheur de votre choix ou aux données d’entrée ou de sortie associées. Le fichier *function.json* de chaque fonction contient tous les liaisons associées. Il n’existe aucune limite au nombre de liaisons d’entrée et de sortie d’une fonction. Toutefois, une seule liaison de déclencheur est prise en charge pour chaque fonction.  

Pour mieux connaître les différentes liaisons que vous pouvez intégrer à votre application Azure Functions, consultez le tableau suivant.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Pour mieux comprendre les déclencheurs et les liaisons en général, supposons que vous souhaitez exécuter du code pour traiter un nouvel élément déposé dans une file d’attente de Stockage Azure. Azure Functions fournit un déclencheur de file d’attente Azure pour le prendre en charge. Vous aurez besoin des informations suivantes pour contrôler la file d’attente :

* le compte de stockage où se trouve la file d’attente ;
* le nom de la file d’attente ;
* un nom de variable que votre code pourra utiliser pour faire référence au nouvel élément déposé dans la file d’attente.  

Une liaison de déclencheurs de file d’attente contient ces informations pour une fonction Azure. Voici un exemple de fichier *function.json* contenant une liaison de déclencheurs de file d’attente. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ],
  "disabled": false
}
```

Votre code peut envoyer différents types de sorties selon de la manière dont est traité le nouvel élément de file d’attente. Par exemple, si vous souhaitez écrire un nouvel enregistrement dans une table de Stockage Azure,  vous pouvez créer une liaison de sortie vers une table de stockage Azure. Voici un exemple de fichier *function.json* qui inclut une liaison de sortie de table de stockage utilisable avec un déclencheur de file d’attente. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "name": "myNewUserTableBinding",
      "tableName": "newUserTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La fonction C# suivante répond à l’ajout d’un nouvel élément dans la file d’attente et écrit une nouvelle entrée d’utilisateur dans une table de Stockage Azure.

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;

public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                TraceWriter log)
{
    // In this example the queue item is a JSON string representing an order that contains the name, 
    // address and mobile number of the new customer.
    dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);

    await myNewUserTableBinding.AddAsync(
        new Person() { 
            PartitionKey = "Test", 
            RowKey = Guid.NewGuid().ToString(), 
            Name = order.name,
            Address = order.address,
            MobileNumber = order.mobileNumber }
        );
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string MobileNumber { get; set; }
}
```

Pour voir d’autres exemples de code et des informations plus précises sur les types de Stockage Azure pris en charge, consultez [Déclencheurs et liaisons Azure Functions pour le Stockage Azure](functions-bindings-storage.md).

Pour utiliser les fonctionnalités de liaison plus avancées du Portail Azure, cliquez sur l’option **Éditeur avancé** dans l’onglet **Intégrer** de votre fonction. L’éditeur avancé vous permet de modifier le fichier *function.json* directement sur le portail.

## <a name="random-guids"></a>GUID aléatoires
Azure Functions fournit une syntaxe permettant de générer des GUID aléatoires avec vos liaisons. La syntaxe de liaison suivante écrit la sortie dans un nouvel objet BLOB avec un nom unique dans un conteneur de stockage : 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```


## <a name="returning-a-single-output"></a>Renvoi d’une seule sortie
Dans le cas où le code de votre fonction retourne une sortie unique, vous pouvez utiliser une liaison de sortie nommée `$return` pour conserver une signature de fonction plus naturelle dans votre code. Cette spécificité n’est utilisable qu’avec les langages qui prennent en charge une valeur de retour (C#, Node.js, F#). La liaison serait similaire à la liaison de sortie blob suivante, utilisée avec un déclencheur de file d’attente.

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "name": "input",
      "direction": "in",
      "queueName": "test-input-node"
    },
    {
      "type": "blob",
      "name": "$return",
      "direction": "out",
      "path": "test-output-node/{id}"
    }
  ]
}
```

Le code C# suivant retourne la sortie plus naturellement sans utiliser de paramètre `out` dans la signature de fonction.

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

Exemple async :

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```


Cette approche est illustrée comme suit avec Node.js :

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

Vous trouverez ci-dessous un exemple de F# :

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

Cette méthode peut également être utilisée dans le cas de paramètres de sortie multiples, en désignant une sortie unique avec `$return`.

## <a name="resolving-app-settings"></a>Résolution des paramètres de l’application
Il est recommandé de stocker les informations sensibles dans le cadre de l’environnement d’exécution à l’aide des paramètres d’application. En conservant les informations sensibles hors des fichiers de configuration de votre application, vous limitez leur exposition lorsqu’un référentiel public est utilisé pour stocker les fichiers d’application.  

L’environnement d’exécution d’Azure Functions résout les paramètres d’application en valeurs lorsque le nom de paramètre d’application est placé entre des signes de pourcentage : `%your app setting%`. La [liaison Twilio](functions-bindings-twilio.md) suivante utilise un paramètre d’application nommé `TWILIO_ACCT_PHONE` pour le champ `from` de la liaison. 

```json
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received Node.js",
  "direction": "out"
},
```



## <a name="parameter-binding"></a>Liaison de paramètres
Au lieu de définir une configuration statique pour les propriétés de votre liaison de sortie, vous pouvez configurer des paramètres liés de façon dynamique aux données qui appartiennent à la liaison d’entrée de votre déclencheur. Imaginez un scénario où les nouvelles commandes sont traitées à l’aide d’une file d’attente de Stockage Azure. Chaque nouvel élément de file d’attente est une chaîne JSON contenant au moins les propriétés suivantes :

```json
{
  "name" : "Customer Name",
  "address" : "Customer's Address",
  "mobileNumber" : "Customer's mobile number in the format - +1XXXYYYZZZZ."
}
```

Vous pouvez par exemple envoyer au client un SMS avec votre compte Twilio pour l’informer que la commande a bien été reçue.  Vous pouvez configurer ainsi les champs `body` et `to` de votre liaison de sortie Twilio pour qu’elle soit liée dynamiquement aux `name` et `mobileNumber` qui faisaient partie de l’entrée.

```json
{
  "name": "myNewOrderItem",
  "type": "queueTrigger",
  "direction": "in",
  "queueName": "queue-newOrders",
  "connection": "orders_STORAGE"
},
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received",
  "direction": "out"
},
```

Le code de votre fonction n’a plus qu’à initialiser le paramètre de sortie de la façon suivante. Pendant l’exécution, les propriétés de sortie seront liées aux données d’entrée souhaitées.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using System.Threading.Tasks;
using Newtonsoft.Json;
using Twilio;

public static async Task<SMSMessage> Run(string myNewOrderItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myNewOrderItem}");

    dynamic order = JsonConvert.DeserializeObject(myNewOrderItem);    

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // The following isn't needed since we use parameter binding for this
    //string msg = "Hello " + order.name + ", thank you for your order.";
    //smsText.Body = msg;
    //smsText.To = order.mobileNumber;

    return smsText;
}
```

Node.js :

```javascript
module.exports = function (context, myNewOrderItem) {    
    context.log('Node.js queue trigger function processed work item', myNewOrderItem);    

    // No need to set the properties of the text, we use parameters in the binding. We do need to 
    // initialize the object.
    var smsText = {};    

    context.done(null, smsText);
}
```

## <a name="advanced-binding-at-runtime-imperative-binding"></a>Liaison avancée lors de l’exécution (liaison impérative)

Le modèle de liaison d’entrée et de sortie standard utilisant *function.json* est nommé liaison [*déclarative*](https://en.wikipedia.org/wiki/Declarative_programming). La liaison est définie par la déclaration JSON. Toutefois, vous pouvez utiliser une liaison [impérative](https://en.wikipedia.org/wiki/Imperative_programming). Avec ce modèle, vous pouvez effectuer une liaison avec n’importe quel nombre de liaisons d’entrée et de sortie prises en charge à la volée dans le code de votre fonction.
Vous aurez peut-être besoin d’une liaison impérative pour les cas où le calcul du chemin de liaison ou d’autres entrées doit se produire au moment de l’exécution dans votre fonction et non au moment de la conception. 

Définissez une liaison impérative comme suit :

- **N’incluez pas** d’entrée dans *function.json* pour les liaisons impératives souhaitées.
- Transmettez un paramètre d’entrée [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs). 
- Utilisez le modèle en C# suivant pour effectuer la liaison de données.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

où `BindingTypeAttribute` est l’attribut .NET qui définit votre liaison et `T` est le type d’entrée ou de sortie pris en charge par ce type de liaison. `T` ne peut pas être un type de paramètre `out` (comme `out JObject`). Par exemple, la liaison de sortie de la table Mobile Apps prend en charge [six types de sortie](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mais vous pouvez utiliser uniquement [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) pour `T`.
    
L’exemple de code suivant crée une [liaison de sortie d’objet blob de stockage](functions-bindings-storage-blob.md#storage-blob-output-binding) avec un chemin d’objet blob défini au moment de l’exécution, puis écrit une chaîne vers l’objet blob.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) définit la liaison d’entrée ou de sortie de [l’objet blob de stockage](functions-bindings-storage-blob.md), et [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) est un type de liaison de sortie pris en charge.
Le code tel quel obtient le paramètre d’application par défaut pour la chaîne de connexion de compte de stockage (c’est-à-dire `AzureWebJobsStorage`). Vous pouvez spécifier un paramètre d’application personnalisé à utiliser en ajoutant l’attribut [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) et en transmettant le tableau d’attributs dans `BindAsync<T>()`. Par exemple,

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

Le tableau suivant présente l’attribut .NET à utiliser pour chaque type de liaison, ainsi que le package auquel il convient de faire référence.

> [!div class="mx-codeBreakAll"]
| Liaison | Attribut | Ajouter la référence |
|------|------|------|
| Base de données de documents | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| File d’attente de stockage | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Objet blob de stockage | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Table de stockage | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |



## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Test d’une fonction](functions-test-a-function.md)
* [Mettre à l’échelle une fonction](functions-scale.md)


