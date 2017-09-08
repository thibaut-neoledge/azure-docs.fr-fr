---
title: Liaison Notification Hubs Azure Functions| Microsoft Docs
description: "Découvrez comment utiliser les liaisons Azure Notification Hubs dans Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 02d01d0f6e945ed54dbe766aec2a0fd7c17c510f
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-notification-hub-output-binding"></a>Liaison de sortie Notification Hubs Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons Notification Hubs dans Azure Functions. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Vos fonctions peuvent envoyer des notifications Push à l’aide d’un Azure Notification Hub configuré avec quelques lignes de code. Toutefois, l’Azure Notification Hub doit être configuré pour l’infrastructure Platform Notification System (PNS) que vous souhaitez utiliser. Pour plus d’informations sur la configuration d’un Azure Notification Hub et sur le développement d’applications clientes qui s’inscrivent pour recevoir des notifications, voir [Prise en main de Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) en cliquant sur votre plateforme cliente cible au début de l’article.

Les notifications que vous envoyez peuvent être des notifications natives ou des notifications de modèle. Les notifications natives ciblent une plateforme de notification spécifique telle que configurée dans la propriété `platform` de la liaison de sortie. Un modèle de notification peut servir à plusieurs plateformes cibles.   

## <a name="notification-hub-output-binding-properties"></a>Propriété de liaison de sortie du hub de notification
Le fichier function.json spécifie les propriétés suivantes :


|Propriété  |Description  |
|---------|---------|
|**name** | Nom de variable utilisé dans le code de fonction pour le message du hub de notification. |
|**type** | Cette propriété doit être définie sur `notificationHub`. |
|**tagExpression** | Expressions de balise vous permettant de demander que les notifications soient remises à un ensemble d’appareils qui se sont inscrits pour la réception de notifications correspondant à l’expression de balise.  Pour plus d’informations, voir [Routage et expressions de balise](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | Nom de la ressource de hub de notification dans le portail Azure. |
|**Connexion** | Cette chaîne de connexion doit correspondre à une chaîne de connexion **Paramètre d’application** définie sur la valeur *DefaultFullSharedAccessSignature* de votre hub de notification. |
|**direction** | Cette propriété doit être définie sur `out`. | 
|**platform** | La propriété de la plateforme indique la plateforme de notification ciblée par votre notification. Par défaut, si la propriété de la plateforme est omise dans la liaison de sortie, les notifications de modèle peuvent être utilisées pour cibler n’importe quelle plateforme configurée sur Azure Notification Hub. Pour en savoir plus sur l’utilisation de modèles en général pour envoyer entre des notifications entre plusieurs plateformes avec un Azure Notification Hub, consultez la rubrique [Modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Une fois définie, _platform_ doit avoir l’une des valeurs suivantes : <ul><li><code>apns</code>&mdash;Apple Push Notification Service (APNS). Pour en savoir plus sur la configuration du hub de notification pour Apple Push Notification Service et la réception de notifications dans une application cliente, consultez [Envoi de notifications Push vers iOS avec Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging (ADM)](https://developer.amazon.com/device-messaging). Pour en savoir plus sur la configuration du hub de notification pour ADM et la réception de notifications dans une application Kindle, consultez [Prise en main de Notification Hubs pour les applications Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging, qui est la nouvelle version de GCM, est également pris en charge. Pour plus d’informations, consultez [Envoi de notifications Push vers Android avec Azure Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Services de notification Push Windows (WNS)](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) ciblant les plateformes Windows. Windows Phone 8.1 et les versions ultérieures sont est également prises en charge par WNS. Pour plus d’informations, consultez [Prise en main de Notification Hubs pour les applications de plateforme Windows universelle](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Services de notifications Push Microsoft (MPNS)](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Cette plateforme prend en charge Windows Phone 8 et les plateformes antérieures de Windows Phone. Pour plus d’informations, consultez [Envoi de notifications Push avec Azure Notification Hubs sur Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

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
Pour utiliser une liaison de sortie de hub de notification, vous devez configurer la chaîne de connexion pour le hub. Vous pouvez sélectionner un hub de notification existant ou en créer un nouveau sous l’onglet *Intégrer* de votre fonction. Vous pouvez également configurer la chaîne de connexion manuellement. 

Pour configurer la chaîne de connexion sur un hub de notification existant :

1. Accédez à votre hub de notification dans le [portail Azure](https://portal.azure.com), choisissez **Stratégies d’accès**, puis sélectionnez le bouton Copier en regard de la stratégie **DefaultFullSharedAccessSignature**. Vous copiez ainsi la chaîne de connexion de la stratégie *DefaultFullSharedAccessSignature* dans votre hub de notification. Cette chaîne de connexion fournit l’autorisation d’accès de votre fonction pour l’envoi des messages de notification. 
    ![Copier la chaîne de connexion du hub de notification](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Accédez à votre application de fonction dans le portail Azure, choisissez **Paramètres de l’application**, ajoutez une clé telle que `MyHubConnectionString`, collez la stratégie *DefaultFullSharedAccessSignature* copiée dans votre hub de notification en tant que valeur, puis cliquez sur **Enregistrer**.

Vous pouvez désormais utiliser ce paramètre d’application nommé qui définit la connexion du hub de notification dans la liaison de sortie.

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


