---
title: "Prise en main des files d’attente Azure Service Bus | Microsoft Docs"
description: "Écrire une application console C# qui utilise les files d’attente de messagerie Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 02d0ce093bc42cffa4f3993826c61c8aeca4d033
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---
# <a name="get-started-with-service-bus-queues"></a>Prise en main des files d’attente Service Bus
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>Les opérations que nous allons effectuer
Ce didacticiel couvre les étapes suivantes :

1. Créer un espace de noms Service Bus à l’aide du Portail Azure.
2. Créer une file d’attente Service Bus à l’aide du portail Azure.
3. Écrire une application de console pour envoyer un message.
4. Écrire une application console pour recevoir les messages envoyés à l’étape précédente.

## <a name="prerequisites"></a>Composants requis
1. [Visual Studio 2015 ou une version ultérieure](http://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2017.
2. Un abonnement Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Créer un espace de noms à l’aide du Portail Azure
Si vous avez déjà créé un espace de noms de messagerie Service Bus, passez directement à la section [Créer une file d’attente à l’aide du portail Azure](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Créer une file d’attente à l’aide du Portail Azure
Si vous avez déjà créé une file d’attente Service Bus, passez directement à la section [Envoyer des messages à la file d’attente](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Envoyer des messages à la file d’attente
Pour envoyer des messages à la file d’attente, nous allons écrire une application console C# à l’aide de Visual Studio.

### <a name="create-a-console-application"></a>Création d’une application console

Ouvrez Visual Studio et créez un projet **Application de console (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Ajout du package NuGet Service Bus
1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.
2. Cliquez sur l’onglet **Parcourir**, recherchez **Microsoft Azure Service Bus**, puis sélectionnez l’élément **WindowsAzure.ServiceBus**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.
   
    ![Sélectionner un package NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Écrire du code pour envoyer un message vers la file d’attente
1. Ajoutez l’instruction `using` suivante au début du fichier Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Ajoutez le code suivant à la méthode `Main` . Définissez la variable `connectionString` sur la chaîne de connexion obtenue lors de la création de l’espace de noms, puis définissez `queueName` sur le nom de file d’attente utilisé lors de la création de la file d’attente.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Voici à quoi doit ressembler votre fichier Program.cs.
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Exécutez le programme et consultez le portail Azure : cliquez sur le nom de votre file d’attente dans le panneau **Vue d’ensemble** de l’espace de noms. Le panneau **Fondamentaux** de la file d’attente est affiché. Notez que la valeur **Nombre de messages actifs** doit à présent être de 1. Chaque fois que vous exécutez l’application de l’expéditeur sans récupérer les messages, cette valeur augmente de 1. Notez également que la taille actuelle de la file d’attente s’incrémente chaque fois que l’application y ajoute un message.
   
      ![Taille des messages][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Recevoir les messages d'une file d'attente

1. Pour recevoir le message que vous venez d’envoyer, créez une application console et ajoutez une référence au package NuGet Service Bus, identique à l’application d’expéditeur précédente.
2. Ajoutez l’instruction `using` suivante au début du fichier Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Ajoutez le code suivant à la méthode `Main` . Définissez la variable `connectionString` sur la chaîne de connexion que vous avez obtenue lors de la création de l’espace de noms, puis définissez `queueName` sur le nom de file d’attente utilisé lors de la création de la file d’attente.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Voici à quoi doit ressembler votre fichier Program.cs :
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. Réexécutez le programme et vérifiez le portail. Notez que les valeurs **Nombre de messages actifs** et **Actuel** sont à présent de 0.
   
    ![Longueur de la file d’attente][queue-message-receive]

Félicitations ! Vous avez maintenant créé une file d’attente, envoyé un message et reçu un message.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [référentiels GitHub accompagnés d’exemples](https://github.com/Azure/azure-service-bus/tree/master/samples) qui illustrent certaines des fonctionnalités les plus avancées de la messagerie Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

