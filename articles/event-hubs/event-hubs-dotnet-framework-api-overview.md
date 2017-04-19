---
title: "Vue d’ensemble des API Azure Event Hubs .NET Framework | Microsoft Docs"
description: "Résumé de certaines des principales API clientes Event Hubs .NET Framework."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7f3b6cc0-9600-417f-9e80-2345411bd036
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: 0e59d117bd9a1355b5b58c76cf9b39ec3005b122
ms.lasthandoff: 02/02/2017


---
# <a name="event-hubs-net-framework-api-overview"></a>Vue d’ensemble de l’API Event Hubs .NET Framework
Cet article passe en revue certaines des principales API clientes Event Hubs .NET Framework. Il en existe deux catégories : les API de gestion et les API du runtime. Les API du runtime comportent toutes les opérations nécessaires pour envoyer et recevoir un message. Les opérations de gestion vous permettent de gérer l’état d’une entité Event Hubs en créant, modifiant et supprimant des entités.

Les scénarios d’analyse couvrent la gestion et l’exécution. Pour obtenir une documentation de référence détaillée sur les API .NET, consultez les informations de référence de [l’API .NET Service Bus](/dotnet/api) et de [l’API EventProcessorHost](/dotnet/api).

## <a name="management-apis"></a>API de gestion
Pour effectuer les opérations de gestion suivantes, vous devez avoir des autorisations de **gestion** sur l’espace de noms Event Hubs :

### <a name="create"></a>Créer
```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>Mettre à jour
```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Supprimer
```csharp
await namespaceManager.DeleteEventHubAsync("Event Hub name");
```

## <a name="run-time-apis"></a>API de runtime
### <a name="create-publisher"></a>Créer un éditeur
```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("Event Hub name");
```

### <a name="publish-message"></a>Publier un message
```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Créer un consommateur
```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Consommer un message
```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>API de l’hôte du processeur d’événements
Ces API offrent une résilience aux processus de travail qui peuvent devenir indisponibles, en distribuant les partitions sur les workers disponibles.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

L’interface [IEventProcessor](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) est définie comme suit :

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les scénarios des concentrateurs d’événements, consultez ces liens :

* [Nouveautés des concentrateurs d'événements Azure ?](event-hubs-what-is-event-hubs.md)
* [Guide de programmation de concentrateurs d’événements](event-hubs-programming-guide.md)

Les informations de référence de l'API .NET se trouvent ici :

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft.Azure.ServiceBus.EventProcessorHost](/dotnet/api/microsoft.azure.servicebus.eventprocessorhost)

