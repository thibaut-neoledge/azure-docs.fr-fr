---
title: Prise en main des rubriques et abonnements Azure Service Bus | Microsoft Docs
description: "Écrivez une application console C# qui utilise les rubriques et les abonnements de messageries Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 3646d14be662af0fdf80790cb53ddc581b33a146
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-service-bus-topics"></a>Prise en main des rubriques Service Bus

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ce didacticiel couvre les étapes suivantes :

1. Créer un espace de noms Service Bus à l’aide du Portail Azure.
2. Créer une rubrique Service Bus à l’aide du Portail Azure.
3. Créer un abonnement Service Bus vers cette rubrique à l’aide du Portail Azure.
4. Écrire une application de console pour envoyer un message vers la rubrique.
5. Écrivez une application console pour recevoir ce message depuis l’abonnement.

## <a name="prerequisites"></a>Composants requis

1. [Visual Studio 2015 ou une version ultérieure](http://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2017.
2. Un abonnement Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Créer un espace de noms à l’aide du Portail Azure

Si vous avez déjà créé un espace de noms Service Bus Messaging, passez directement à la section [Créer une rubrique à l’aide du portail Azure](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Créer une rubrique à l’aide du Portail Azure

1. Connectez-vous au [portail Azure][azure-portal].
2. Dans le volet de navigation gauche du portail, cliquez sur **Service Bus** (si vous ne voyez pas **Service Bus**, cliquez sur **Plus de services**).
3. Cliquez sur l’espace de noms dans lequel vous souhaitez créer la rubrique. Le panneau de vue d’ensemble d’espace de noms s’affiche :
   
    ![Création d'une rubrique][createtopic1]
4. Dans le panneau de **l’espace de noms Service Bus**, sélectionnez **Rubriques**, puis cliquez sur **Ajouter une rubrique**.
   
    ![Sélectionnez les rubriques][createtopic2]
5. Entrez un nom pour la rubrique et désélectionnez l’option **Activer le partitionnement**. Conservez les valeurs par défaut des autres options.
   
    ![Sélectionner Nouveau][createtopic3]
6. Au bas du panneau, cliquez sur **Créer**.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Créer un abonnement à la rubrique

1. Dans le volet ressources du portail, cliquez sur l’espace de noms que vous avez créé à l’étape 1, puis cliquez sur le nom de la rubrique que vous avez créée à l’étape 2.
2. Sur la partie supérieure du volet Vue d’ensemble, cliquez sur le signe plus à côté de l’option **Abonnement** pour ajouter un abonnement à cette rubrique.

    ![Créer un abonnement][createtopic4]

3. Entrez un nom pour l’abonnement. Conservez les valeurs par défaut des autres options.

## <a name="4-send-messages-to-the-topic"></a>4. Envoyez des messages à la rubrique

Pour envoyer des messages vers la rubrique, nous écrivons une application de console C# à l’aide de Visual Studio.

### <a name="create-a-console-application"></a>Création d’une application console

Ouvrez Visual Studio et créez un projet **Application de console (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Ajout du package NuGet Service Bus

1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.
2. Cliquez sur l’onglet **Parcourir**, recherchez **WindowsAzure.ServiceBus**, puis sélectionnez l’élément **WindowsAzure.ServiceBus**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.
   
    ![Sélectionner un package NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-topic"></a>Écrire du code pour envoyer un message vers la rubrique

1. Ajoutez l’instruction `using` suivante au début du fichier Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Ajoutez le code suivant à la méthode `Main` . Configurez la variable `connectionString` en tant que chaîne de connexion obtenue lors de la création de l’espace de noms, puis configurez `topicName` en tant que nom utilisé lors de la création de la rubrique.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
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

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Exécutez le programme et consultez le portail Azure : cliquez sur le nom de votre rubrique dans le panneau de l’espace de noms **Vue d’ensemble**. Le panneau de rubrique **Fondamentaux** est affiché. Dans les abonnements répertoriés au bas du panneau, notez que la valeur **Nombre de messages** de chaque abonnement doit désormais être égale à 1. Chaque fois que vous exécutez l’application de l’expéditeur sans récupérer les messages (tel que décrit que la section suivante), cette valeur augmente de 1. Notez également que la taille actuelle de la rubrique incrémente la valeur **Actuel** sur le panneau **Fondamentaux** chaque fois que l’application ajoute un message à la rubrique/l’abonnement.
   
      ![Taille des messages][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Réception des messages de l’abonnement

1. Pour recevoir le ou les messages que vous venez d’envoyer, créez une application console et ajoutez une référence au package NuGet Service Bus, identique à l’application de l’expéditeur précédente.
2. Ajoutez l’instruction `using` suivante au début du fichier Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Ajoutez le code suivant à la méthode `Main` . Configurez la variable `connectionString` en tant que chaîne de connexion obtenue lors de la création de l’espace de noms, puis configurez `topicName` en tant que nom utilisé lors de la création de la rubrique. Assurez-vous de remplacer `<your subscription name>` par le nom de l’espace de noms de l’abonnement créé à l’étape 3. 
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
4. Réexécutez le programme et vérifiez le portail. Notez que les valeurs **Nombre de messages** et **Actuel** sont à présent de 0.
   
    ![Longueur de la rubrique][topic-message-receive]

Félicitations ! Vous avez créé une rubrique et un abonnement, envoyé un message et reçu ce message.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [référentiels GitHub accompagnés d’exemples](https://github.com/Azure/azure-service-bus/tree/master/samples) qui illustrent certaines des fonctionnalités les plus avancées de la messagerie Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
