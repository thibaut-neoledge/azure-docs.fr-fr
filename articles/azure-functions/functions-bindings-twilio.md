---
title: Liaison Twilio Azure Functions| Microsoft Docs
description: "Découvrez comment utiliser les liaisons Twilio dans Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: a60263aa-3de9-4e1b-a2bb-0b52e70d559b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/20/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: 29fa4620885ec787f362966f732510fded600d53


---
# <a name="azure-functions-twilio-output-binding"></a>Liaisons de sortie Azure Functions Twilio
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article vous explique comment configurer et utiliser les liaisons Twilio avec Azure Functions. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Functions prend en charge les liaisons de sortie Twilio, afin de permettre à vos fonctions d’envoyer des SMS avec quelques lignes de codes et un compte [Twilio](https://www.twilio.com/). 

## <a name="functionjson-for-azure-notification-hub-output-binding"></a>function.json pour les liaisons de sortie Azure Notification Hubs
Le fichier function.json spécifie les propriétés suivantes :

* `name` : nom de la variable utilisée dans le code de fonction pour le SMS Twilio.
* `type` : doit être définie sur *"twilioSms"*.
* `accountSid` : cette valeur doit être définie sur le nom d’un paramètre d’application hébergeant le SID de votre compte Twilio.
* `authToken` : cette valeur doit être définie sur le nom d’un paramètre d’application hébergeant le jeton d’authentification Twilio.
* `to` : cette valeur est définie sur le numéro de téléphone sur lequel est envoyé le SMS.
* `from` : cette valeur est définie sur le numéro de téléphone à partir duquel est envoyé le SMS.
* `direction` : doit être défini sur *out*.
* `body` : cette valeur peut être utilisée pour coder en dur le SMS, si vous n’avez pas besoin de procéder à une définition dynamique dans le code associé à votre fonction. 

Exemple de fichier function.json :

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Exemple de déclencheur de file d’attente C# avec liaison de sortie Twilio
#### <a name="synchronous"></a>Synchrone
Cet exemple de code asynchrone pour un déclencheur de file d’attente Azure Storage utilise un paramètre sortant pour envoyer un SMS à un client qui a passé une commande.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

#### <a name="asynchronous"></a>Asynchrone
Cet exemple de code asynchrone pour un déclencheur de file d’attente Azure Storage envoie un SMS à un client qui a passé une commande.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Exemple de déclencheur de file d’attente Node.js avec liaison de sortie Twilio
Cet exemple Node.js envoie un SMS à un client qui a passé une commande.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


