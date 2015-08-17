<properties 
	pageTitle="Mise en route avec le stockage de files d’attente Azure et les appareils connectés Visual Studio" 
	description="Mise en route de l'utilisation du stockage de files d'attente Azure dans un projet de service cloud dans Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>


<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="patshea123"/>


# Mise en route avec le stockage de files d’attente Azure et les appareils connectés Visual Studio

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-cloud-services-getting-started-queues.md)
> - [What happened](vs-storage-cloud-services-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

Cet article décrit comment prendre en main le stockage de tables Azure dans Visual Studio après avoir créé ou référencé un compte de stockage Azure dans un projet de services cloud via la boîte de dialogue **Ajouter des services connectés** de Visual Studio.

Nous allons vous montrer comment créer une file d’attente dans le code. Nous vous indiquerons aussi comment effectuer des opérations de base sur les files d’attente, comme l’ajout, la modification, la lecture et la suppression des messages des files d’attente. Les exemples ont été écrits en code C# et utilisent la [bibliothèque cliente de stockage Azure pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

 - Pour plus d’informations sur la manipulation d’objets blob par le code, consultez la page [Utilisation du stockage d’objets blob à partir de .NET](storage-dotnet-how-to-use-blobs.md "Utilisation du stockage d’objets blob à partir de .NET").
 - Pour des informations générales sur le stockage Azure, consultez la [documentation du stockage](https://azure.microsoft.com/documentation/services/storage/).
 - Pour des informations générales sur les services cloud Azure, consultez la [documentation des services cloud](http://azure.microsoft.com/documentation/services/cloud-services/).
 - Pour plus d’informations sur la programmation d’applications ASP.NET, consultez la page [ASP.NET](http://www.asp.net).


Le service de stockage de files d'attente Azure permet de stocker un grand nombre de messages accessibles partout dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d’attente peut avoir une taille de 64 Ko et une file d’attente peut contenir des millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage.

L’opération **Ajouter des services connectés** installe les packages NuGet appropriés pour accéder au stockage Azure de votre projet et ajoute la chaîne de connexion au compte de stockage dans les fichiers de configuration de votre projet.

Pour commencer, vous devez créer une file d’attente Azure dans votre compte de stockage.

##Accéder à des files d’attente dans le code

Pour accéder à des files d’attente dans les projets ASP.NET, vous devez inclure les éléments suivants dans les fichiers sources C# qui accèdent au stockage de files d’attente Azure.

1. Vérifiez que les déclarations d’espace de noms figurant au début du fichier C# incluent ces instructions `using`.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenez un objet `CloudStorageAccount` représentant les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Obtenez un objet `CloudQueueClient` pour référencer les objets de file d’attente dans votre compte de stockage.

	    // Create the table client.
    	CloudQuecClient queueClient = storageAccount.CreateCloudTableClient();

4. Obtenez un objet `CloudQueue` pour référencer une file d’attente spécifique.

    	// Get a reference to a table named "messageQueue"
	    CloudTable messageQueue = queueClient.GetQueueReference("messageQueue");


**REMARQUE :** placez tout le code ci-dessus avant celui des exemples suivants.

###Créer une file d’attente dans le code

Pour créer la file d’attente Azure dans le code, ajoutez simplement un appel à `CreateIfNotExists`.

	// Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Create the CloudTable if it does not exist
	messageQueue.CreateIfNotExists();

##Ajout d'un message à une file d'attente

Pour insérer un message dans une file d’attente, créez un objet `CloudQueueMessage`, puis appelez la méthode AddMessage().

Il est possible de créer un objet `CloudQueueMessage` à partir d’une chaîne (au format UTF-8) ou d’un tableau d’octets.

Voici un exemple qui insère le message « Hello, World ».

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	messageQueue.AddMessage(message);

##Lire un message dans une file d’attente

En appelant la méthode PeekMessage(), vous pouvez lire furtivement le message au début de la file d'attente sans pour autant l'en retirer.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

##Lire et supprimer un message dans une file d’attente

Votre code permet de supprimer (retirer) un message d'une file d'attente en deux étapes. 1. Lorsque vous appelez GetMessage(), vous obtenez le message suivant au sein de la file d'attente. Tout message renvoyé par un appel GetMessage() n'est plus visible par les autres codes lisant les messages de cette même file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. 2. Pour finaliser la suppression du message dans la file d’attente, appelez DeleteMessage1().

Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Le code suivant appelle DeleteMessage() juste après le traitement du message.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

	// Process the message in less than 30 seconds

  	// Then delete the message.
	await messageQueue.DeleteMessage(retrievedMessage);


## Options supplémentaires pour le traitement et la suppression de messages de file d’attente

Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L’exemple de code suivant utilise la méthode `GetMessages` pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l’aide d’une boucle `foreach`. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message. Notez que le délai de 5 minutes démarre en même temps pour tous les messages. Donc une fois les 5 minutes écoulées après l’appel à `GetMessages`, tous les messages non supprimés redeviennent visibles.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        messageQueue.DeleteMessage(message);
    }

## Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode `FetchAttributes` demande au service de File d’attente de récupérer les attributs de la file d’attente, y compris le nombre de messages. La propriété `ApproximateMethodCount` renvoie la dernière valeur extraite par la méthode `FetchAttributes`, sans appeler le service de File d’attente.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Fetch the queue attributes.
	messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Utiliser le modèle Async-Await avec les API de file d’attente commune

Cet exemple décrit comment utiliser le modèle Async-Await avec les API de file d’attente commune. L’exemple appelle la version asynchrone de chacune des méthodes spécifiées, comme indiqué par le suffixe Async de chaque méthode. Quand une méthode asynchrone est utilisée, le modèle Async-Await suspend l’exécution locale jusqu’à la fin de l’appel. Ce comportement permet au thread actuel d’effectuer d'autres tâches afin d’éviter les goulots d’étranglement au niveau des performances et d’améliorer la réactivité globale de votre application. Pour plus d’informations sur l’utilisation du modèle Async-Await dans .NET, voir l’article [Programmation asynchrone avec Async et Await (C# et Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## Suppression d'une file d'attente

Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode `Delete` sur l’objet de file d’attente.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

    // Delete the queue.
    messageQueue.Delete();



##Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
			

<!---HONumber=August15_HO6-->