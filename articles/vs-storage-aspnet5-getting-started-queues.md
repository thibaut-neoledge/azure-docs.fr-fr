<properties 
	pageTitle="Prise en main d'Azure Storage" 
	description="Prise en main du stockage de files d'attente Azure dans un projet ASP.NET 5 dans Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="kempb"/>

# Prise en main d'Azure Storage (projets ASP.NET 5)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-queues.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

Le service de stockage de files d'attente Azure permet de stocker un grand nombre de messages accessibles partout dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d’attente peut avoir une taille de 64 Ko et une file d’attente peut contenir des millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage. Pour plus d'informations, consultez la page [Utilisation du service de stockage de files d'attente à partir de .NET](storage-dotnet-how-to-use-queues.md/ "Utilisation du service de stockage de files d’attente à partir de .NET").

Pour accéder par programme aux files d'attente des projets ASP.NET 5, vous devez ajouter les éléments suivants, s'ils ne sont pas déjà présents.

1. Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;
		using System.Threading.Tasks;

2. Utilisez le code ci-dessous pour obtenir le paramètre de configuration.

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Obtention de la chaîne de connexion de stockage
Avant de commencer à manipuler une file d’attente, vous devez obtenir la chaîne de connexion pour le compte de stockage dans lequel résideront les files d’attente. Vous pouvez utiliser le type **CloudStorageAccount** pour représenter les informations de votre compte de stockage. Si vous travaillez dans un projet ASP.NET 5, vous pouvez appeler la méthode get de l'objet Configuration pour obtenir votre chaîne de connexion de stockage et les informations de votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant.

**REMARQUE** : les API qui effectuent des appels vers le stockage Azure dans ASP.NET 5 sont asynchrones. Pour plus d’informations, voir l’article [Programmation asynchrone avec Async et Await](http://msdn.microsoft.com/library/hh191443.aspx). Le code ci-dessous suppose que les méthodes de programmation asynchrone sont utilisées.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####Création d'une file d'attente
Un objet **CloudQueueClient** vous permet d'obtenir les objets de référence pour les files d'attente. Le code suivant crée un objet **CloudQueueClient**. Tous les codes présentés dans cette rubrique utilisent une chaîne de connexion de stockage enregistrée dans la configuration de service de l'application Azure. Plusieurs méthodes permettent de créer un objet **CloudStorageAccount**. Pour plus d’informations, consultez la documentation relative à [CloudStorageAccount](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount").

**REMARQUE** : les API qui effectuent des appels vers le stockage Azure dans ASP.NET 5 sont asynchrones. Pour plus d’informations, voir l’article [Programmation asynchrone avec Async et Await](http://msdn.microsoft.com/library/hh191443.aspx). Le code ci-dessous suppose que les méthodes de programmation asynchrone sont utilisées.

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilisez l'objet **queueClient** pour obtenir une référence pointant vers la file d'attente à utiliser. Il tente de référencer la file d'attente nommée « myqueue ». S'il ne trouve aucune file d'attente ainsi nommée, il en crée une.

	// Get a reference to a queue named “myqueue”.
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn’t already there, then create it.
	await queue.CreateIfNotExistsAsync();

**REMARQUE** : utilisez l’ensemble de ce code devant le code des sections suivantes.

#####Insertion d'un message dans une file d'attente
Pour insérer un message dans une file d'attente existante, commencez par créer un objet **CloudQueueMessage**. Appelez ensuite la méthode AddMessageAsync(). Un objet **CloudQueueMessage** peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets. Voici le code qui crée une file d'attente (si elle n'existe pas) et insère le message « Hello, World ».

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	await queue.AddMessageAsync(message);

#####Lecture furtive du message suivant
En appelant la méthode PeekMessageAsync(), vous pouvez lire furtivement le message au début de la file d'attente sans pour autant l'en retirer.

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

#####Suppression du message suivant
Votre code permet de supprimer (retirer) un message d'une file d'attente en deux étapes.


1. Lorsque vous appelez GetMessageAsync(), vous obtenez le message suivant au sein de la file d'attente. Tout message renvoyé par un appel GetMessageAsync() n'est plus visible par les autres codes lisant les messages de cette même file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. 
2.	Pour finaliser la suppression du message de la file d'attente, vous devez appeler DeleteMessageAsync(). 

Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Le code suivant appelle DeleteMessageAsync() juste après le traitement du message.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

	// Process the message in less than 30 seconds, and then delete the message.
	await queue.DeleteMessageAsync(retrievedMessage);

[En savoir plus sur Azure Storage](http://azure.microsoft.com/documentation/services/storage/) Voir aussi [Consultation et gestion des ressources de stockage avec l’Explorateur de serveurs](http://msdn.microsoft.com/library/azure/ff683677.aspx) et [ASP.NET 5](http://www.asp.net/vnext) (en anglais).

<!---HONumber=58-->