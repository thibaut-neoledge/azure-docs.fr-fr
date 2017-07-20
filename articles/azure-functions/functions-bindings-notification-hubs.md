---
title: Liaison Notification Hubs Azure Functions| Microsoft Docs
description: "Découvrez comment utiliser les liaisons Azure Notification Hubs dans Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: fa3d37b963c1bb6b58127b9180cd657d7b1dabcc
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017


---
# <a name="azure-functions-notification-hub-output-binding"></a>Liaison de sortie Notification Hubs Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons Notification Hubs dans Azure Functions. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Vos fonctions peuvent envoyer des notifications Push à l’aide d’un Azure Notification Hub configuré avec quelques lignes de code. Toutefois, l’Azure Notification Hub doit être configuré pour l’infrastructure Platform Notification System (PNS) que vous souhaitez utiliser. Pour plus d’informations sur la configuration d’un Azure Notification Hub et sur le développement d’applications clientes qui s’inscrivent pour recevoir des notifications, voir [Prise en main de Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) en cliquant sur votre plateforme cliente cible au début de l’article.

Les notifications que vous envoyez peuvent être des notifications natives ou des notifications de modèle. Les notifications natives ciblent une plateforme de notification spécifique telle que configurée dans la propriété `platform` de la liaison de sortie. Un modèle de notification peut servir à plusieurs plateformes cibles.   

## <a name="notification-hub-output-binding-properties"></a>Propriété de liaison de sortie du hub de notification
Le fichier function.json spécifie les propriétés suivantes :

* `name` : nom de variable utilisé dans le code de fonction pour le message du hub de notification.
* `type` : doit être défini sur *"notificationHub"*.
* `tagExpression` : expressions de balise vous permettant de demander que les notifications soient remises à un ensemble d’appareils qui se sont inscrits pour la réception de notifications correspondant à l’expression de balise.  Pour plus d’informations, voir [Routage et expressions de balise](../notification-hubs/notification-hubs-tags-segment-push-message.md).
* `hubName` : nom de la ressource de hub de notification dans le portail Azure.
* `connection` : cette chaîne de connexion doit correspondre à une chaîne de connexion **Paramètre d’application** définie sur la valeur *DefaultFullSharedAccessSignature* de votre hub de notification.
* `direction` : doit être défini sur *out*. 
* `platform` : la propriété de la plateforme indique la plateforme de notification ciblée par votre notification. Il doit s’agir de l’une des valeurs suivantes : 
  * Par défaut, si la propriété de la plateforme est omise dans la liaison de sortie, les notifications de modèle peuvent être utilisées pour cibler n’importe quelle plateforme configurée sur Azure Notification Hub. Pour en savoir plus sur l’utilisation de modèles en général pour envoyer entre des notifications entre plusieurs plateformes avec un Azure Notification Hub, consultez la rubrique [Modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).
  * `apns` : Apple Push Notification Service (APNS) Pour en savoir plus sur la configuration du hub de notification pour APNS et la réception de notifications dans une application cliente, consultez [Envoi de notifications Push vers iOS avec Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
  * `adm` : [Amazon Device Messaging](https://developer.amazon.com/device-messaging). Pour en savoir plus sur la configuration du hub de notification pour ADM et la réception de notifications dans une application Kindle, consultez [Prise en main de Notification Hubs pour les applications Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
  * `gcm` : [Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging, qui est la nouvelle version de GCM, est également pris en charge. Pour en savoir plus sur la configuration du hub de notification pour GCM/FCM et la réception de notifications dans une application cliente Android, consultez [Envoi de notifications Push vers Android avec Azure Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
  * `wns` : [services de notification push Windows](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) ciblant les plateformes Windows. Windows Phone 8.1 et les versions ultérieures sont est également prises en charge par WNS. Pour en savoir plus sur la configuration du hub de notification pour WNS et la réception de notifications dans une application Universal Windows Platform (UWP), consultez [Prise en main de Notification Hubs pour les applications de plateforme Windows universelle](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
  * `mpns` : [service de notifications Push Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Cette plateforme prend en charge Windows Phone 8 et les plateformes antérieures de Windows Phone. Pour en savoir plus sur la configuration du hub de notification pour MPNS et la réception de notifications dans une application Windows Phone, consultez [Envoi de notifications Push avec Azure Notification Hubs sur Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)

Exemple de fichier function.json :

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>Configuration de la chaîne de connexion du hub de notification
Pour utiliser une liaison de sortie de hub de notification, vous devez configurer la chaîne de connexion pour le hub. Vous pouvez effectuer cette opération dans l’onglet *Intégrer*. Pour cela, sélectionnez votre hub de notification ou d’en créer un. 

Vous pouvez également ajouter manuellement une chaîne de connexion pour un hub existant en ajoutant cette chaîne pour *DefaultFullSharedAccessSignature* à votre hub de notification. Cette chaîne de connexion fournit l’autorisation d’accès de votre fonction pour l’envoi des messages de notification. La valeur de la chaîne de connexion *DefaultFullSharedAccessSignature* est accessible à partir du bouton **clés** dans le panneau principal de votre ressource Notification Hub, dans le portail Azure. Pour ajouter manuellement une chaîne de connexion pour votre hub, procédez comme suit : 

1. Dans le panneau **Function App** du portail Azure, cliquez sur **Paramètres Function App > Accéder aux paramètres App Service**.
2. Dans le panneau **Paramètres**, cliquez sur **Paramètres de l’application**.
3. Faites défiler les éléments vers le bas jusqu’à la section **Paramètres de l’application**, puis ajoutez une entrée nommée pour la valeur *DefaultFullSharedAccessSignature* pour votre hub de notification.
4. Référencez votre nom de chaîne de paramètre de l’application dans les liaisons de sortie. Cette chaîne sera semblable à **MyHubConnectionString** utilisé dans l’exemple ci-dessus.

## <a name="apns-native-notifications-with-c-queue-triggers"></a>Notifications natives APNS avec déclencheurs de file d’attente C#
Cet exemple indique comment utiliser les types définis dans la [bibliothèque Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) pour envoyer une notification APNS native. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>Notifications natives GCM avec déclencheurs de file d’attente C#
Cet exemple indique comment utiliser les types définis dans la [bibliothèque Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) pour envoyer une notification GCM native. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>Notifications natives WNS avec déclencheurs de file d’attente C#
Cet exemple indique comment utiliser les types définis dans la [bibliothèque Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) pour envoyer une notification toast WNS native. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Exemple de modèle pour les déclencheurs de minuteur Node.js
Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenant `location` et `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="template-example-for-f-timer-triggers"></a>Exemple de modèle pour les déclencheurs de minuteur F#
Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenant `location` et `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>Exemple de modèle utilisant un paramètre de sortie
Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenant un emplacement réservé `message` dans le modèle.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

## <a name="template-example-with-asynchronous-function"></a>Exemple de modèle avec fonction asynchrone
Si vous utilisez du code asynchrone, les paramètres de sortie ne sont pas autorisés. Dans ce cas, utilisez `IAsyncCollector` pour renvoyer votre notification modèle. Le code suivant est un exemple de code asynchrone ci-dessus. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>Exemple de modèle utilisant JSON
Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) contenant un emplacement réservé `message` dans le modèle à l’aide d’une chaîne JSON valide.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>Exemple de modèle utilisant des types de bibliothèques de Notification Hubs
Cet exemple indique comment utiliser le type défini dans la [bibliothèque Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


