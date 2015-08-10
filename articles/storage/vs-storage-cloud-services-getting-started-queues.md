<properties 
	pageTitle="Prise en main d'Azure Storage" 
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
	ms.date="07/22/2015" 
	ms.author="patshea123"/>

# Prise en main d'Azure Storage (Projets de service cloud)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-cloud-services-getting-started-queues.md)
> - [What Happened](vs-storage-cloud-services-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

Le service de stockage de files d'attente Azure permet de stocker un grand nombre de messages accessibles partout dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d’attente peut avoir une taille de 64 Ko et une file d’attente peut contenir des millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage.

Cet article décrit comment prendre en main le stockage de tables Azure dans Visual Studio une fois que vous avez créé ou référencé un compte de stockage Azure dans un projet de services cloud via la boîte de dialogue **Ajouter des services connectés** de Visual Studio. L’opération **Ajouter des services connectés** installe les packages NuGet appropriés pour accéder au stockage Azure de votre projet et ajoute la chaîne de connexion pour le compte de stockage aux fichiers de configuration de votre projet.

Pour commencer, vous devez créer une file d’attente Azure dans votre compte de stockage. Nous vous montrerons comment créer une file d’attente à partir de l’**Explorateur de serveurs** de Visual Studio. Si vous préférez, nous vous montrerons aussi comment créer une file d’attente dans le code.

Nous vous indiquerons aussi comment effectuer des opérations de base sur les files d’attente, comme l’ajout, la modification, la lecture et la suppression des messages des files d’attente. Les exemples ont été écrits en code C# et utilisent la bibliothèque du client de stockage Azure pour .NET. Pour plus d’informations sur ASP.NET, voir le site [ASP.NET](http://www.asp.net) (en anglais).

##Créer une file d’attente dans l’Explorateur de serveurs
[AZURE.INCLUDE [vs-create-queue-in-server-explorer](../../includes/vs-create-queue-in-server-explorer.md)]

##Accéder à des files d’attente dans le code

Pour accéder à des files d’attente dans les projets ASP.NET, vous devez inclure les éléments suivants dans les fichiers sources C# qui accèdent au stockage de files d’attente Azure.

1. Assurez-vous que les déclarations d’espace de noms figurant au début du fichier C# incluent ces instructions `using`.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenez un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Obtenez un objet **CloudQueueClient** pour référencer les objets de file d’attente de votre compte de stockage.

	    // Create the table client.
    	CloudQuecClient queueClient = storageAccount.CreateCloudTableClient();

4. Obtenez un objet **CloudQueue** pour référencer une file d’attente spécifique.

    	// Get a reference to a table named "messageQueue"
	    CloudTable messageQueue = queueClient.GetQueueReference("messageQueue");


**REMARQUE** : utilisez tout le code ci-dessus avant le code des exemples suivants.

###Créer une file d’attente dans le code

Pour créer la file d’attente Azure dans le code au lieu d’utiliser l’**Explorateur de serveurs** de Visual Studio, ajoutez simplement un appel à `CreateIfNotExists()`.

	// Create the CloudTable if it does not exist
	queue.CreateIfNotExists();

##Ajout d'un message à une file d'attente

Pour insérer un message dans une file d’attente existante, créez un objet **CloudQueueMessage**, puis appelez la méthode AddMessage().

Un objet **CloudQueueMessage** peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets.

Voici un exemple qui insère le message « Hello, World ».

	// Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	messageQueue.AddMessage(message);

##Lire un message dans une file d’attente

En appelant la méthode PeekMessage(), vous pouvez lire furtivement le message au début de la file d'attente sans pour autant l'en retirer.

    // Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

	// Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

##Lire et supprimer un message dans une file d’attente

Votre code permet de supprimer (retirer) un message d'une file d'attente en deux étapes. 1. Lorsque vous appelez GetMessage(), vous obtenez le message suivant au sein de la file d'attente. Tout message renvoyé par un appel GetMessage() n'est plus visible par les autres codes lisant les messages de cette même file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. 2. Pour finaliser la suppression du message dans la file d’attente, appelez DeleteMessage1().

Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Le code suivant appelle DeleteMessage() juste après le traitement du message.

	// Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

	// Process the message in less than 30 seconds

  	// Then delete the message.
	await messageQueue.DeleteMessage(retrievedMessage);


## Utilisation d’options supplémentaires pour l’enlèvement des messages

Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode **GetMessages** pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle **foreach**. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message. Notez que le délai de 5 minutes démarre en même temps pour tous les messages, donc une fois les 5 minutes écoulées après l'appel de **GetMessages**, tous les messages n'ayant pas été supprimés redeviennent visibles.

    // Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **FetchAttributes** demande au service de files d'attente d'extraire les attributs de la file d'attente, y compris le nombre de messages. La propriété **ApproximateMethodCount** renvoie la dernière valeur extraite par la méthode **FetchAttributes**, sans appeler le service de files d'attente.

    // Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

	// Fetch the queue attributes.
	messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Utiliser le modèle Async-Await avec les API de file d’attente commune

Cet exemple décrit comment utiliser le modèle Async-Await avec les API de file d’attente commune. L’exemple appelle la version asynchrone de chacune des méthodes spécifiées, comme indiqué par le suffixe Async de chaque méthode. Quand une méthode asynchrone est utilisée, le modèle Async-Await suspend l’exécution locale jusqu’à la fin de l’appel. Ce comportement permet au thread actuel d’effectuer d'autres tâches afin d’éviter les goulots d’étranglement au niveau des performances et d’améliorer la réactivité globale de votre application. Pour plus d’informations sur l’utilisation du modèle Async-Await dans .NET, voir l’article [Programmation asynchrone avec Async et Await (C# et Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **Delete** sur l'objet file d'attente.

    // Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

    // Delete the queue.
    messageQueue.Delete();



##Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
			

<!---HONumber=July15_HO5-->