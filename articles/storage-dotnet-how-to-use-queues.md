<properties 
	pageTitle="Utilisation du stockage de files d'attente à partir de .NET | Microsoft Azure" 
	description="Découvrez comment utiliser le stockage de files d'attente Microsoft Azure pour créer et supprimer des files d'attente, ainsi que pour insérer, lire, obtenir et supprimer les messages en file d'attente." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tamram"/>

# Utilisation du stockage de files d'attente à partir de .NET

[AZURE.INCLUDE [storage-selector-queue-include](../includes/storage-selector-queue-include.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du
service de stockage de files d'attente Azure. Les exemples ont été écrits en code C\# et utilisent le client Azure Storage pour .NET. Les scénarios traités incluent l'**insertion**,
la **lecture furtive**, la **récupération** et la **suppression** des messages de file d'attente, ainsi que
la **création et la suppression des files d'attente**. 

> [AZURE.NOTE] Ce guide cible la bibliothèque cliente de stockage Azure .NET 2.x et les versions ultérieures. Nous vous recommandons d'utiliser la version 4.x disponible via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou dans le [Kit de développement logiciel (SDK) Azure pour .NET] (/downloads/). Consultez [Accès au stockage de files d'attente par programme](#programmatically-access-queue-storage) ci-dessous pour plus d'informations sur l'obtention de la bibliothèque cliente de stockage.

[AZURE.INCLUDE [storage-queue-concepts-include](../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../includes/storage-configure-connection-string-include.md)]

## Accès au stockage de files d'attente par programme

### Obtention de l'assembly
Vous pouvez utiliser NuGet pour obtenir l'assembly `Microsoft.WindowsAzure.Storage.dll`. Cliquez avec le bouton droit de la souris sur votre projet dans l'**Explorateur de solutions** et sélectionnez **Gérer les packages NuGet**.  Effectuez une recherche en ligne sur " WindowsAzure.Storage ", puis cliquez sur **Installer** pour lancer l'installation du package Azure Storage et de ses dépendances.

'Microsoft.WindowsAzure.Storage.dll' est également inclus dans le Kit de développement logiciel (SDK) Azure pour .NET, téléchargeable à partir du <a href="http://azure.microsoft.com/develop/net/#">Centre de développement .NET</a>. L'assembly est installé dans le répertoire `%Program Files%\Microsoft SDKs\Azure\.NET SDK\<sdk-version>\ref\`.

### Déclarations d'espace de noms
Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Assurez-vous de bien référencer l'assembly `Microsoft.WindowsAzure.Storage.dll`.

### Récupération de votre chaîne de connexion
Vous pouvez utiliser le type **CloudStorageAccount** pour représenter les informations de votre compte de stockage. Si vous utilisez un 
modèle de projet Microsoft Azure et/ou qu'une référence pointe vers l'espace de noms 
Microsoft.WindowsAzure.CloudConfigurationManager, vous pouvez utiliser le type **CloudConfigurationManager** pour récupérer votre chaîne de connexion de stockage, ainsi que vos informations de compte de stockage depuis la configuration de service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si vous créez une application sans référence pointant vers Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de connexion est située dans le fichier `web.config` ou `app.config` comme indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour récupérer la chaîne de connexion.  Vous devez ajouter une référence pointant vers System.Configuration.dll à votre projet, ainsi qu'une autre déclaration d'espace de noms :

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### Dépendances ODataLib
Les dépendances ODataLib de la bibliothèque cliente de stockage pour .NET sont résolues via les packages ODataLib (version 5.0.2) disponibles avec NuGet et non pas avec les services de données WCF.  Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet.  Les packages ODataLib spécifiques sont [OData], [Edm] et [Spatial].

## Création d'une file d'attente

Un objet **CloudQueueClient** vous permet d'obtenir les objets de référence pour les files d'attente.
Le code suivant crée un objet **CloudQueueClient**. Tous les codes de ce guide utilisent une chaîne de connexion de stockage stockée dans la configuration de service de l'application Azure. Plusieurs méthodes permettent de créer un objet **CloudStorageAccount**. Pour plus d'informations, consultez la documentation de [CloudStorageAccount][].

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilisez l'objet **queueClient** pour obtenir une référence pointant vers la file d'attente à utiliser. Si la file d'attente n'existe pas, vous pouvez la créer :

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

## Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d'attente existante, commencez par créer un
**CloudQueueMessage**. Appelez ensuite la méthode **AddMessage**. Un
**CloudQueueMessage** peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'**octets**. Voici le code qui crée une file d'attente (si elle n'existe pas) et insère le message 'Hello, World' :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExist();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## Lecture furtive du message suivant

En appelant la méthode **PeekMessage**, vous pouvez lire furtivement le message au début de la file d'attente sans pour autant l'en retirer.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display message.
	Console.WriteLine(peekedMessage.AsString);

## Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code suivant met à jour le message de la file d'attente avec un nouveau contenu et ajoute 60 secondes au délai d'expiration de la visibilité. Cette opération enregistre l'état de la tâche associée au message et accorde une minute supplémentaire au client pour traiter le message. Vous pouvez utiliser cette technique pour suivre des flux de travail à plusieurs étapes sur les messages de file d'attente, sans devoir reprendre du début si une étape du traitement échoue à cause d'une défaillance matérielle ou logicielle. Normalement, vous conservez aussi un nombre de nouvelles tentatives, et si le message est retenté plus de *n* fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application par un message chaque fois qu'il est traité.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## Enlèvement du message suivant de la file d'attente

Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez
**GetMessage**, vous obtenez le message suivant dans une file d'attente. Tout message renvoyé par un appel **GetMessage** n'est plus visible par les autres codes lisant les messages de cette même file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **DeleteMessage**. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **DeleteMessage** juste après le traitement du message.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## Utilisation d'options supplémentaires pour l'enlèvement des messages

Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente.
Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode
**GetMessages** pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle **foreach**. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message. Notez que le délai de 5 minutes démarre en même temps pour tous les messages, donc une fois les 5 minutes écoulées après l'appel à **GetMessages**, tous les messages n'ayant pas été supprimés redeviennent visibles.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

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

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La
méthode **FetchAttributes** demande au service de File d'attente de récupérer les attributs de la file d'attente, y compris le nombre de messages. La propriété **ApproximateMethodCount** renvoie la dernière valeur extraite par la méthode
**FetchAttributes**, sans appeler le service de File d'attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Fetch the queue attributes.
	queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode
**Delete** sur l'objet file d'attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage des files d'attente, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

<ul>
<li>Pour plus d'informations sur les API disponibles, consultez la documentation de référence des services de files d'attente :
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Référence de la bibliothèque cliente de stockage pour .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">Référence d'API REST</a></li>
  </ul>
</li>
<li>Pour plus d'informations sur les tâches avancées que vous pouvez effectuer avec Azure Storage, consultez la page <a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Stockage et accessibilité des données dans Azure</a>.</li>
<li>Découvrez comment simplifier le code que vous écrivez pour travailler avec Azure Storage à l'aide du <a href="../websites-dotnet-webjobs-sdk/">Kit de développement logiciel WebJobs Azure.</li>
<li>Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
  <ul>
    <li>Utilisez le <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">stockage de table</a> pour stocker des données structurées.</li>
    <li>Utilisez le <a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">stockage d'objets blob</a> pour stocker des données non structurées.</li>
    <li>Utilisez une <a href="/documentation/articles/sql-database-dotnet-how-to-use/">base de données SQL</a> pour stocker des données relationnelles.</li>
  </ul>
</li>
</ul>

  [Télécharger et installer le Kit de développement logiciel (SDK) Azure pour .NET] : /develop/net/
  [Référence de bibliothèque cliente .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Création d'un projet Azure dans Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuration des chaînes de connexion]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=49-->