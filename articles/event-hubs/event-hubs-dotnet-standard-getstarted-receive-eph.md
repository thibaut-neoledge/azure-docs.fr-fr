---
title: "Recevoir des événements d’Azure Event Hubs avec .Net Standard | Microsoft Docs"
description: "Prise en main de la réception des messages avec EventProcessorHost dans .NET Standard"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/31/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 57175ddc53d5856cd3492d4c631a92d4bf9247c4
ms.openlocfilehash: 859f87356448041a320c2e126478aabf1efa0d44
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-receiving-messages-with-the-eventprocessorhost-in-net-standard"></a>Prise en main de la réception des messages avec EventProcessorHost dans .NET Standard

> [!NOTE]
> Cet exemple est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleEphReceiver).

## <a name="what-will-be-accomplished"></a>Les opérations que nous allons effectuer

Ce didacticiel explique comment créer la solution existante **SampleEphReceiver** (à l’intérieur de ce dossier). Vous pouvez exécuter la solution telle quelle, en remplaçant les chaînes `EhConnectionString`, `EhEntityPath`, et `StorageAccount` par vos valeurs de compte de stockage et Event Hub ou suivre ce didacticiel pour créer votre propre solution.

Dans ce didacticiel, nous allons écrire une application console .NET Core pour recevoir des messages d’un Event Hub avec **EventProcessorHost**.

## <a name="prerequisites"></a>Composants requis

1. [Visual Studio 2015](http://www.visualstudio.com).

2. [Outils Visual Studio 2015 .NET Core](http://www.microsoft.com/net/core).

3. Un abonnement Azure.

4. Un espace de noms Event Hubs.
    
## <a name="receive-messages-from-the-event-hub"></a>Recevoir les messages d’Event Hub

### <a name="create-a-console-application"></a>Création d’une application console

1. Ouvrez Visual Studio et créez une application console .NET Core.

### <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

* Ajoutez les packages NuGet suivants à votre projet :
  * [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
  * [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

### <a name="implement-the-ieventprocessor-interface"></a>Implémentation de l’interface IEventProcessor

1. Créez une classe appelée « SimpleEventProcessor ».

2. Ajoutez les instructions `using` ci-après au début du fichier SimpleEventProcessor.cs.

    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

3. Implémentez l’interface `IEventProcessor`. La classe doit ressembler à cela :

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Collections.Generic;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class SimpleEventProcessor : IEventProcessor
        {
            public Task CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
                return Task.CompletedTask;
            }
    
            public Task OpenAsync(PartitionContext context)
            {
                Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
                return Task.CompletedTask;
            }
    
            public Task ProcessErrorAsync(PartitionContext context, Exception error)
            {
                Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
                return Task.CompletedTask;
            }
    
            public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (var eventData in messages)
                {
                    var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                    Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
                }
    
                return context.CheckpointAsync();
            }
        }
    }
    ```

### <a name="write-a-main-console-method-that-uses-simpleeventprocessor-to-receive-messages-from-an-event-hub"></a>Rédaction d’une méthode de console principale utilisant `SimpleEventProcessor` pour recevoir des messages d’un Event Hub

1. Ajoutez les instructions `using` ci-après en haut du fichier Program.cs.
  
    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. Ajoutez des constantes à la classe `Program` pour la chaîne de connexion Event Hubs, le chemin d’accès à l’Event Hub, le nom du conteneur de stockage, le nom du compte de stockage et la clé du compte de stockage. Remplacez les espaces réservés par leurs valeurs correspondantes.

    ```cs
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Ajoutez une nouvelle méthode nommée `MainAsync` à la classe `Program`, comme suit :
    ```cs
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Ajoutez la ligne de code suivante à la méthode `Main` :

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Voici à quoi doit ressembler votre fichier Program.cs :

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";
    
            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");
    
                var eventProcessorHost = new EventProcessorHost(
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
                    StorageConnectionString,
                    StorageContainerName);
    
                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
    
                Console.WriteLine("Receiving. Press enter key to stop worker.");
                Console.ReadLine();
    
                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```
  
4. Exécutez le programme et assurez-vous qu’il n’y a aucune erreur.
  
Félicitations ! Vous recevez désormais des messages d’un Event Hub.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Créer un Event Hub)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)
