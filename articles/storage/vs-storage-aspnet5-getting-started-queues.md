---
title: "Prendre en main le Stockage File d’attente et les services connectés de Visual Studio (ASP.NET 5) | Microsoft Docs"
description: "Prise en main du stockage de files d&quot;attente Azure dans un projet ASP.NET 5 dans Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b166ced70b845dd297a1eb87aaec5456ec3e0b31


---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-5"></a>Prendre en main le stockage de files d’attente et les services connectés de Visual Studio (ASP.NET 5)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d'ensemble
Cet article explique comment prendre en main Azure Queue Storage dans Visual Studio après avoir créé ou référencé un compte de stockage Azure dans un projet ASP.NET 5 au moyen de la boîte de dialogue **Ajouter des services connectés** de Visual Studio. L’opération **Ajouter des services connectés** installe les packages NuGet appropriés pour accéder au stockage Azure de votre projet et ajoute la chaîne de connexion pour le compte de stockage aux fichiers de configuration de votre projet.

Le service de stockage de files d'attente Azure permet de stocker un grand nombre de messages accessibles partout dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un message de file d’attente peut avoir une taille maximale de 64 Ko et une file d’attente peut contenir plusieurs millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage.

Pour commencer, vous devez créer une file d’attente Azure dans votre compte de stockage. Nous allons vous montrer comment créer une file d’attente dans le code. Et aussi comment effectuer des opérations de base sur les files d’attente, comme l’ajout, la modification, la lecture et la suppression de messages de files d’attente. Les exemples ont été écrits en code C\# et utilisent la bibliothèque du client de stockage Azure pour .NET. Pour plus d’informations sur ASP.NET, voir le site [ASP.NET](http://www.asp.net)(en anglais).

**REMARQUE :** parmi les API qui effectuent des appels au stockage Azure dans ASP.NET 5, certaines sont asynchrones. Pour plus d’informations, consultez l’article [Programmation asynchrone avec Async et Await](http://msdn.microsoft.com/library/hh191443.aspx) . Le code ci-dessous suppose que les méthodes de programmation asynchrone sont utilisées.

* Pour plus d’informations sur la manipulation des files d’attente par programme, consultez la page [Prise en main du stockage de files d’attente Azure à l’aide de .NET](storage-dotnet-how-to-use-queues.md) .
* Pour des informations générales sur Azure Storage, consultez la [documentation relative au stockage](https://azure.microsoft.com/documentation/services/storage/) .
* Pour des informations générales sur les services cloud Azure, consultez la [documentation des services cloud](https://azure.microsoft.com/documentation/services/cloud-services/) .
* Pour plus d’informations sur la programmation des applications ASP.NET, consultez la page [ASP.NET](http://www.asp.net) .

## <a name="access-queues-in-code"></a>Accéder à des files d’attente dans le code
Pour accéder à des files d’attente dans des projets ASP.NET 5, vous devez inclure les éléments suivants dans tout fichier source C# qui accède au stockage de files d’attente Azure.

1. Vérifiez que les déclarations d’espace de noms figurant au début du fichier C# incluent ces instructions **using** .
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Obtenez un objet **CloudQueueClient** pour référencer les objets de file d’attente de votre compte de stockage.  
   
        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Obtenez un objet **CloudQueue** pour référencer une file d’attente spécifique.
   
        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**REMARQUE :** placez tout le code ci-dessus avant celui des exemples suivants.

### <a name="create-a-queue-in-code"></a>Créer une file d’attente dans le code
Pour créer la file d’attente Azure dans le code, ajoutez simplement un appel à **CreateIfNotExistsAsync**.

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

## <a name="add-a-message-to-a-queue"></a>Ajout d'un message à une file d'attente
Pour insérer un message dans une file d’attente existante, créez un objet **CloudQueueMessage**, puis appelez la méthode **AddMessageAsync**.

Un objet **CloudQueueMessage** peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets.

Voici un exemple qui insère le message « Hello, World ».

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

## <a name="read-a-message-in-a-queue"></a>Lire un message dans une file d’attente
En appelant la méthode **PeekMessageAsync** , vous pouvez lire furtivement le message au début de la file d’attente sans pour autant l’en retirer.

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


## <a name="read-and-remove-a-message-in-a-queue"></a>Lire et supprimer un message dans une file d’attente
Votre code permet de supprimer (retirer) un message d’une file d’attente en deux étapes.

1. Quand vous appelez **GetMessageAsync** , vous obtenez le message suivant au sein de la file d’attente. Tout message renvoyé par un appel **GetMessageAsync** n’est plus visible par les autres codes lisant les messages de cette même file d’attente. Par défaut, ce message reste invisible pendant 30 secondes.
2. Pour finaliser la suppression du message de la file d’attente, vous devez appeler **DeleteMessageAsync**.

Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Le code suivant appelle **DeleteMessageAsync** juste après le traitement du message.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>des options supplémentaires pour l'enlèvement des messages
Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente.
Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode **GetMessages** pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle **foreach** . Il définit également le délai d’expiration de l’invisibilité à 5 minutes pour chaque message. Notez que le délai de 5 minutes démarre en même temps pour tous les messages. Donc une fois les 5 minutes écoulées après l’appel à **GetMessages**, tous les messages non supprimés redeviennent visibles.

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Obtention de la longueur de la file d'attente
Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **FetchAttributes** demande au service de files d’attente d’extraire les attributs de la file d’attente, y compris le nombre de messages. La propriété **ApproximateMethodCount** renvoie la dernière valeur extraite par la méthode **FetchAttributes**, sans appeler le service de File d’attente.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Utiliser le modèle Async-Await avec les API de file d’attente commune
Cet exemple décrit comment utiliser le modèle Async-Await avec les API de file d’attente commune. Cet exemple appelle la version asynchrone de chacune des méthodes indiquées. Vous pouvez le constater grâce au post-correctif Async de chaque méthode. Quand une méthode asynchrone est utilisée, le modèle Async-Await suspend l’exécution locale jusqu’à la fin de l’appel. Ce comportement permet au thread actuel d’effectuer d'autres tâches afin d’éviter les goulots d’étranglement au niveau des performances et d’améliorer la réactivité globale de votre application. Pour plus d’informations sur l’utilisation du modèle Async-Await dans .NET, consultez l’article [Async et Await (C# et Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to add to the queue.
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message.
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## <a name="delete-a-queue"></a>Suppression d'une file d'attente
Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **Delete** sur l'objet file d'attente.

    // Delete the queue.
    messageQueue.Delete();


## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]




<!--HONumber=Nov16_HO3-->


