---
title: "Recevoir des événements d’Azure Event Hubs avec .NET Standard | Microsoft Docs"
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
ms.date: 03/03/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 65ed5164b8d010453ed34e8b8cdf68915e136007
ms.lasthandoff: 03/22/2017

---

# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Bien démarrer avec la réception de messages à l’aide de l’hôte du processeur d’événements dans .NET Standard

> [!NOTE]
> Cet exemple est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver).

Ce didacticiel explique comment écrire une application console .NET Core qui reçoit des messages d’un hub d’événements à l’aide d’**EventProcessorHost**. Vous pouvez exécuter la solution [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver) telle quelle, en remplaçant les chaînes par vos valeurs de compte de stockage et Event Hub. Ou vous pouvez suivre les étapes de ce didacticiel pour créer les vôtres.

## <a name="prerequisites"></a>Composants requis

* [Microsoft Visual Studio 2015 ou 2017](http://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2015, mais Visual Studio 2017 est également pris en charge.
* [Outils Visual Studio 2015 ou 2017 .NET Core](http://www.microsoft.com/net/core).
* Un abonnement Azure.
* Un espace de noms Azure Event Hubs.
* Un compte de stockage Azure.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Créer un espace de noms Event Hubs et un hub d’événements  

La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le hub d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md), puis passez aux étapes suivantes.  

## <a name="create-an-azure-storage-account"></a>Création d'un compte de stockage Azure  

1. Connectez-vous au [portail Azure](https://portal.azure.com).  
2. Dans le panneau de navigation gauche du portail, cliquez sur **Nouveau**, puis sur **Données** et sur **Compte de stockage**.  
3. Renseignez les champs dans le panneau de compte de stockage, puis cliquez sur **Créer**.

    ![Créer un compte de stockage][1]

4. Après l’affichage du message **Déploiements réussis**, cliquez sur le nom du nouveau compte de stockage. Dans le panneau **Bases**, cliquez sur **Objets blob**. Quand le panneau **Service Blob** s’ouvre, cliquez sur **+ Conteneur** en haut. Nommez le conteneur, puis fermez le panneau **Service Blob**.  
5. Cliquez sur **Clés d’accès** dans le panneau de gauche, puis copiez le nom du conteneur de stockage, le compte de stockage et la valeur de **key1**. Enregistrez ces valeurs dans le Bloc-notes ou un autre emplacement temporaire.  

## <a name="create-a-console-application"></a>Création d’une application console

1. Démarrez Visual Studio. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**. Créez une application console .NET Core.

    ![Nouveau projet][2]

2. Dans l’Explorateur de solutions, double-cliquez sur le fichier **project.json** pour l’ouvrir dans l’éditeur de Visual Studio.
3. Ajoutez la chaîne `"portable-net45+win8"` à la déclaration `"imports"` dans la section `"frameworks"`. Cette section doit maintenant apparaître comme suit. Cette chaîne est nécessaire en raison de la dépendance entre Stockage Azure et OData :

    ```json
    "frameworks": {
      "netcoreapp1.0": {
        "imports": [
          "dnxcore50",
          "portable-net45+win8"
        ]
      }
    }
    ```

4. Dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

Notez que ce didacticiel montre comment écrire une application .NET Core. Si vous souhaitez cibler le .NET Framework, ajoutez la ligne de code suivante au fichier project.json, dans la section `"frameworks"` :

```json
"net451": {
},
```

## <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

Ajoutez les packages NuGet suivants au projet :
* [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
* [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

## <a name="implement-the-ieventprocessor-interface"></a>Implémentation de l’interface IEventProcessor

1. Dans l’Explorateur de solutions, cliquez sur **Ajouter**, puis sur **Classe**. Nommez la nouvelle classe **SimpleEventProcessor**.

2. Ouvrez le fichier SimpleEventProcessor.cs et ajoutez les instructions `using` suivantes au début du fichier.

    ```csharp
    using System.Text;
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

3. Implémentez l’interface `IEventProcessor`. Remplacez tout le contenu de la classe `SimpleEventProcessor` par le code suivant :

    ```csharp
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
    ```

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Écrire une méthode de console qui utilise la classe SimpleEventProcessor pour recevoir des messages

1. Ajoutez les instructions `using` ci-après en haut du fichier Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. Ajoutez des constantes à la classe `Program` pour la chaîne de connexion Event Hubs, le nom du hub d’événements, le nom du conteneur de compte de stockage, le nom du compte de stockage et la clé du compte de stockage. Ajoutez le code suivant, en remplaçant les espaces réservés par les valeurs correspondantes.

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Ajoutez une nouvelle méthode nommée `MainAsync` à la classe `Program`, comme suit :

    ```csharp
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

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Ajoutez la ligne de code suivante à la méthode `Main` :

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Voici à quoi doit ressembler votre fichier Program.cs :

    ```csharp
    namespace SampleEphReceiver
    {

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

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Exécutez le programme et assurez-vous qu’il n’y a aucune erreur.

Félicitations ! Vous recevez maintenant les messages d’un hub d’événements à l’aide de l’hôte du processeur d’événements.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Créer un Event Hub)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png

