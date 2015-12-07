<properties 
	pageTitle="Utilisation du stockage de files d’attente à partir de Java | Microsoft Azure" 
	description="Découvrez comment utiliser le service de File d'attente Azure pour créer et supprimer des files d'attente, ainsi que pour insérer, récupérer et supprimer des messages. Les exemples sont écrits en Java." 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="robmcm"/>

# Utilisation du stockage de files d'attente à partir de Java

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage des files d’attente Azure. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel (SDK) Azure Storage pour Java][]. Les scénarios traités portent sur l'**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d'attente, ainsi que sur la **création** et la **suppression** des files d'attente. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes](#NextSteps).

Remarque : un Kit de développement logiciel (SDK) est disponible pour les développeurs qui utilisent Azure Storage sur des appareils Android. Pour plus d'informations, consultez la page [Kit de développement logiciel (SDK) Azure Storage pour Android][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d’une application Java

Dans ce guide, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application Java en local, ou dans le code s'exécutant dans un rôle Web ou un rôle de travail dans Azure.

Pour ce faire, vous devez installer le Kit de développement Java (JDK) et créer un compte Azure Storage dans votre abonnement Azure. Vous devez ensuite vérifier que votre système de développement répond à la configuration minimale requise et aux dépendances répertoriées dans le référentiel [Kit de développement logiciel (SDK) Azure Storage pour Java][] sur GitHub. Si tel est le cas, vous pouvez suivre les instructions relatives au téléchargement et à l'installation des bibliothèques Azure Storage pour Java sur votre système à partir du référentiel. Une fois ces tâches effectuées, vous pouvez créer une application Java utilisant les exemples de cet article.

## Configuration de votre application pour accéder au stockage de files d’attente

Ajoutez les instructions import suivantes au début du fichier Java dans lequel vous voulez utiliser des API de stockage Azure pour accéder aux files d'attente :

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## Configuration d’une chaîne de connexion de stockage Azure

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker des points de terminaison et des informations d'identification permettant d'accéder aux services de gestion des données. Lors de l'exécution d'une application cliente, vous devez spécifier la chaîne de connexion de stockage au format suivant, en utilisant le nom de votre compte de stockage et la clé d'accès primaire pour le compte de stockage, répertoriés sur le portail de gestion pour les valeurs *AccountName* et *AccountKey*. Cet exemple vous montre comment déclarer un champ statique pour qu'il contienne une chaîne de connexion :

    // Define the connection-string with your values.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Dans une application exécutée au sein d'un rôle dans Microsoft Azure, cette chaîne peut être stockée dans le fichier de configuration de service *ServiceConfiguration.cscfg* et elle est accessible en appelant la méthode **RoleEnvironment.getConfigurationSettings**. Voici un exemple de code vous permettant d'extraire la chaîne de connexion à partir d'un élément **Setting** nommé *StorageConnectionString* dans le fichier de configuration de service :

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Les exemples ci-dessous partent du principe que vous avez utilisé l'une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## Création d'une file d'attente

Un objet **CloudQueueClient** vous permet d'obtenir les objets de référence pour les files d'attente. Le code suivant crée un objet **CloudQueueClient**. (Remarque : d’autres méthodes permettent de créer des objets **CloudStorageAccount** Pour plus d’informations, reportez-vous à la classe **CloudStorageAccount** dans la page [Référence du Kit de développement logiciel (SDK) du client Azure Storage].)

Utilisez l'objet **CloudQueueClient** pour obtenir une référence pointant vers la file d'attente à utiliser. Si la file d'attente n'existe pas, vous pouvez la créer :

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the queue client.
	   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

	   // Retrieve a reference to a queue.
	   CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Create the queue if it doesn't already exist.
	   queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Ajout d'un message à une file d'attente

Pour insérer un message dans une file d'attente existante, commencez par créer un **CloudQueueMessage**. Appelez ensuite la méthode **addMessage**. Un **CloudQueueMessage** peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets. Voici le code qui crée une file d'attente (si elle n'existe pas) et insère le message « Hello, World ».

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Create the queue if it doesn't already exist.
    	queue.createIfNotExists();

    	// Create a message and add it to the queue.
    	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    	queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Lecture furtive du message suivant

Vous pouvez lire furtivement le message au début de la file d'attente sans l'enlever de la file d'attente en appelant **peekMessage**.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");
			
    	// Peek at the next message.
    	CloudQueueMessage peekedMessage = queue.peekMessage();
			
    	// Output the message value.
    	if (peekedMessage != null)
    	{
		  System.out.println(peekedMessage.getMessageContentAsString());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code suivant met à jour le message de la file d'attente avec un nouveau contenu et ajoute 60 secondes au délai d'expiration de la visibilité. Cette opération enregistre l'état de la tâche associée au message et accorde une minute supplémentaire au client pour traiter le message. Vous pouvez utiliser cette technique pour suivre des flux de travail à plusieurs étapes sur les messages de file d'attente, sans devoir reprendre du début si une étape du traitement échoue à cause d'une défaillance matérielle ou logicielle. Normalement, vous conservez aussi un nombre de nouvelles tentatives et si le message est retenté plus de *n* fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application par un message à chaque fois qu'il est traité.

L'exemple de code suivant effectue une recherche dans la file d'attente de messages, recherche le premier message dont le contenu correspond à « Hello, World », modifie le contenu du message, puis se ferme.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32. 
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields = 
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

L'exemple de code suivant met simplement à jour le premier message visible dans la file d'attente.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Retrieve the first visible message in the queue.
    	CloudQueueMessage message = queue.retrieveMessage();
			
    	if (message != null)
    	{
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields = 
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **downloadAttributes** demande au service de File d'attente plusieurs valeurs actives, y compris le nombre de messages dans une file d'attente. Ce nombre est approximatif étant donné que des messages peuvent être ajoutés ou supprimés une fois que le service de File d'attente a répondu à votre demande. La méthode **getApproximateMessageCount** renvoie la dernière valeur extraite par l'appel à **downloadAttributes**, sans appeler le service de File d'attente.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Download the approximate message count from the server.
    	queue.downloadAttributes();

    	// Retrieve the newly cached approximate message count.
    	long cachedMessageCount = queue.getApproximateMessageCount();
			
    	// Display the queue length.
    	System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Procédure : Suppression du message suivant dans la file d’attente

Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez **retrieveMessage**, vous obtenez le message suivant dans une file d'attente. Un message renvoyé par **retrieveMessage** devient invisible de tout autre code lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **deleteMessage**. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **deleteMessage** juste après le traitement du message.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
    	    CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Retrieve the first visible message in the queue.
    	CloudQueueMessage retrievedMessage = queue.retrieveMessage();
			
    	if (retrievedMessage != null)
    	{
    		// Process the message in less than 30 seconds, and then delete the message.
    		queue.deleteMessage(retrievedMessage);
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## Options supplémentaires pour la suppression des messages dans la file d'attente

Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message.

L'exemple de code suivant utilise la méthode **retrieveMessages** pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle **for**. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes (300 secondes) pour chaque message. Notez que le délai de cinq minutes démarre en même temps pour tous les messages, donc une fois les cinq minutes écoulées après l'appel de **retrieveMessages**, tous les messages n'ayant pas été supprimés redeviennent visibles.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes, 
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Procédure : Obtention de la liste des files d’attente

Pour obtenir la liste des files d'attente en cours, appelez la méthode **CloudQueueClient.listQueues()**, qui renvoie une collection d'objets **CloudQueue**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **deleteIfExists** sur l'objet **CloudQueue**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage des files d'attente, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

- [Kit de développement logiciel (SDK) Azure Storage pour Java][]
- [Référence du Kit de développement logiciel (SDK) du client Azure Storage][]
- [API REST d’Azure Storage][]
- [Blog de l'équipe Azure Storage][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Kit de développement logiciel (SDK) Azure Storage pour Java]: https://github.com/azure/azure-storage-java
[Kit de développement logiciel (SDK) Azure Storage pour Java]: https://github.com/azure/azure-storage-java
[Kit de développement logiciel (SDK) Azure Storage pour Android]: https://github.com/azure/azure-storage-android
[Référence du Kit de développement logiciel (SDK) du client Azure Storage]: http://dl.windowsazure.com/storage/javadoc/
[API REST d’Azure Storage]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/

<!---HONumber=AcomDC_1125_2015-->