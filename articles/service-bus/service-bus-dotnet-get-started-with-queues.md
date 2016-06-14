<properties
   pageTitle="Prise en main des files d’attente Service Bus | Microsoft Azure"
   description="Comment écrire une application de console C# pour la messagerie Service Bus"
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#Prise en main des files d’attente Service Bus
[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

##Les opérations que nous allons effectuer
Dans ce didacticiel, nous allons :

1. Créer un espace de noms Service Bus à l’aide du Portail Azure.

2. Créer une file d’attente Messagerie Service Bus à l’aide du Portail Azure.

3. Écrire une application de console pour envoyer un message.

4. Écrire une application de console pour recevoir des messages.

##Composants requis
1. [Visual Studio 2013 / Visual Studio 2015](http://www.visualstudio.com)

2. Un abonnement Azure

##1\. Créer un espace de noms à l’aide du Portail Azure
Si vous disposez déjà d’un espace de noms Service Bus créé précédemment, passez directement à [Créer une file d’attente à l’aide de la section Portail Azure](#2-create-a-queue-using-the-azure-portal).

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

##2\. Créer une file d’attente à l’aide du Portail Azure
Si vous disposez déjà d’une file d’attente Service Bus créée précédemment, passez directement à [Envoi de messages vers la section file d’attente](#3-sending-messages-to-the-queue).

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

##3\. Envoi de messages vers la file d’attente
Pour envoyer des messages vers la file d’attente, nous allons écrire une application de console C# à l’aide de Visual Studio.

###Création d'une application console
1. Ouvrez Visual Studio et créez une nouvelle application de console.

###Ajout du package NuGet Service Bus
1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.

2. Recherchez « Microsoft Azure Service Bus » et sélectionnez l’élément **Microsoft Azure Service Bus**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.

    ![Sélectionner un package NuGet][1]

###Écrire du code pour envoyer un message vers la file d’attente
1. Ajoutez l’instruction using suivante au début du fichier Program.cs.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Ajoutez le code suivant à la méthode Main et définissez la variable **connectionString** en tant que chaîne de connexion obtenue lors de la création de l’espace de noms, et **queueName** en tant que nom de file d’attente utilisé lors de la création de la file d’attente.

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);

    ```

    Voici à quoi doit ressembler votre fichier Program.cs.

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;

    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                client.Send(message);
            }
        }
    }
    ```
  
3. Exécutez le programme et vérifiez le Portail Azure. Notez que la **Longueur de la file d’attente** doit maintenant valoir 1.
    
      ![Longueur de la file d’attente][2]
    
##4\. Réception de messages à partir de la file d'attente
1. Créez une nouvelle application de console et ajoutez une référence au package NuGet Service Bus comme pour l’application émettrice ci-dessus.

2. Ajoutez l’instruction using suivante au début du fichier Program.cs.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Ajoutez le code suivant à la méthode Main et définissez la variable **connectionString** en tant que chaîne de connexion obtenue lors de la création de l’espace de noms, et **queueName** en tant que nom de file d’attente utilisé lors de la création de la file d’attente.

    ```
    var connectionString = "";
    var queueName = "samplequeue";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
  
    Console.ReadLine();
    ```

      Voici à quoi doit ressembler votre fichier Program.cs :

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;
  
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
  
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
  
          Console.ReadLine();
        }
      }
    }
    ```
  
4. Exécutez le programme et vérifiez le Portail Azure. Notez que la **Longueur de la file d’attente** doit maintenant valoir 0.

    ![Longueur de la file d’attente][3]
  
Félicitations ! Vous avez maintenant créé une file d’attente, envoyé un message et reçu un message.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
##Étapes suivantes

Consultez notre référentiel GitHub pour obtenir des exemples des fonctionnalités plus avancées de la messagerie Azure Service Bus. [https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples]

<!--Image references-->

[1]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[2]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-send.png
[3]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->