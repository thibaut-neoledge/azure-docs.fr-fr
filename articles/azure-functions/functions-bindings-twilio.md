---
title: Liaison Twilio Azure Functions| Microsoft Docs
description: "Découvrez comment utiliser les liaisons Twilio dans Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
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
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: e8c5e8f2dfedae26486e1c8afbe0cec3f3228e86
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="send-sms-messages-from-azure-functions-using-the-twilio-output-binding"></a>Envoi de messages SMS depuis Azure Functions à l’aide de la liaison de sortie Twilio
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article vous explique comment configurer et utiliser les liaisons Twilio avec Azure Functions. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Functions prend en charge les liaisons de sortie Twilio, afin de permettre à vos fonctions d’envoyer des SMS avec quelques lignes de codes et un compte [Twilio](https://www.twilio.com/). 

## <a name="functionjson-for-the-twilio-output-binding"></a>function.json pour la liaison de sortie Twilio
Le fichier function.json spécifie les propriétés suivantes :

|Propriété  |Description  |
|---------|---------|
|**name**| Nom de la variable utilisée dans le code de fonction pour le SMS Twilio. |
|**type**| Cette propriété doit être définie sur `twilioSms`.|
|**accountSid**| Cette valeur doit être définie sur le nom d’un paramètre d’application hébergeant le SID de votre compte Twilio.|
|**authToken**| Cette valeur doit être définie sur le nom d’un paramètre d’application hébergeant le jeton d’authentification Twilio.|
|**to**| Cette valeur est définie sur le numéro de téléphone sur lequel est envoyé le SMS.|
|**from**| Cette valeur est définie sur le numéro de téléphone à partir duquel est envoyé le SMS.|
|**direction**| Cette propriété doit être définie sur `out`.|
|**body**| Cette valeur peut être utilisée pour coder en dur le SMS, si vous n’avez pas besoin de procéder à une définition dynamique dans le code associé à votre fonction. |

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


