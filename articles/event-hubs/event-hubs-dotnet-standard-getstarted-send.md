---
title: "Envoyer des événements vers Azure Event Hubs avec .NET Standard | Microsoft Docs"
description: "Prise en main de l’envoi d’événements vers Event Hubs dans .NET Standard"
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
ms.date: 03/01/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 6a6fe5e2e706fd8ab4ee6c51cde5b54fa703688b
ms.lasthandoff: 03/06/2017

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>Prise en main de l’envoi de messages vers Event Hubs dans .NET Standard

> [!NOTE]
> Cet exemple est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender).

Ce didacticiel montre comment écrire une application console .NET Core qui envoie un jeu de messages à un hub d’événements. Vous pouvez soit exécuter la solution [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender) telle quelle, en remplaçant les chaînes `EhConnectionString` et `EhEntityPath` par les valeurs de votre hub d’événements, soit suivre les étapes de ce didacticiel pour créer votre propre solution.

## <a name="prerequisites"></a>Composants requis

1. [Microsoft Visual Studio 2015 ou 2017](http://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2015, mais Visual Studio 2017 est également pris en charge.
2. [Outils Visual Studio 2015 ou 2017 .NET Core](http://www.microsoft.com/net/core).
3. Un abonnement Azure.
4. Un espace de noms Event Hubs.

Pour envoyer des messages à un hub d’événements, nous allons écrire une application console C# à l’aide de Visual Studio.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Créer un espace de noms Event Hubs et un hub d’événements

La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le hub d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md), puis passez aux étapes suivantes.

## <a name="create-a-console-application"></a>Création d’une application console

Lancez Visual Studio. Dans le menu Fichier, cliquez sur **Nouveau**, puis sur **Projet**. Créez une application console .NET Core.

![][1]

## <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

Ajoutez le package NuGet [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) à votre projet.

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Écriture de code pour envoyer des messages à un hub d’événements

1. Ajoutez les instructions `using` ci-après en haut du fichier Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    ```

2. Ajoutez des constantes à la classe `Program` pour le chemin de l’entité et la chaîne de connexion Event Hubs (nom du hub d’événements individuel). Remplacez les espaces réservés entre crochets par les valeurs appropriées obtenues lors de la création du hub d’événements.

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Ajoutez une nouvelle méthode nommée `MainAsync` à la classe `Program`, comme suit :

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
        // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```
    
4. Ajoutez une nouvelle méthode nommée `SendMessagesToEventHub` à la classe `Program`, comme suit :

    ```csharp
    // Creates an Event Hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Ajoutez le code suivant à la méthode `Main` dans la classe `Program`.

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Voici à quoi doit ressembler votre fichier Program.cs.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
       
        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
        
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
        
            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
                // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };
        
                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
        
                await SendMessagesToEventHub(100);
        
                await eventHubClient.CloseAsync();
        
                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }
        
            // Creates an Event Hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }
        
                    await Task.Delay(10);
                }
        
                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```
  
6. Exécutez le programme et assurez-vous qu’il n’y a aucune erreur.
  
Félicitations ! Vous venez d’envoyer des messages à un hub d’événements.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Recevoir des événements d’Event Hubs](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
* [Créer un hub d’événements](event-hubs-create.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcore.png
