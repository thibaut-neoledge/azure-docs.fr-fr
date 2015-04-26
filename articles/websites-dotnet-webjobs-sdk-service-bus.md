<properties 
	pageTitle="Comment utiliser Service Bus Azure avec SDK WebJobs" 
	description="Apprenez à utiliser les rubriques et les files d'attente Service Bus Azure avec SDK WebJobs." 
	services="web-sites, service-bus" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Comment utiliser Service Bus Azure avec SDK WebJobs

Ce guide fournit des exemples de code C# qui montrent comment déclencher un processus, lorsqu'un objet blob Azure est créé ou mis à jour. Les exemples de code utilisent la version 1.x de [SDK WebJobs](websites-dotnet-webjobs-sdk.md).

Le guide suppose que vous savez [créer un projet WebJob dans Visual Studio avec des chaînes de connexion qui pointent vers votre compte de stockage](websites-dotnet-webjobs-sdk-get-started.md).

Les extraits de code montrent uniquement les fonctions, et non le code qui crée l'objet  `JobHost` comme dans cet exemple :

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Sommaire

-   [Conditions préalables](#prerequisites)
-   [Comment déclencher une fonction lors de la réception d'un message de file d'attente](#trigger)
-   [Création de messages de la file d'attente](#create)
-   [Utilisation des rubriques Service Bus](#topics)
-   [Rubriques couvertes par l'article de files d'attente de stockage](#queues)
-   [Étapes suivantes](#nextsteps)

## <a id="prerequisites"></a>Conditions préalables

Pour travailler avec Service Bus, vous devez installer le package NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/)en plus des autres packages SDK WebJobs. 

Vous devez également définir la chaîne de connexion AzureWebJobsServiceBus en plus des chaînes de connexion de stockage.

## <a id="trigger"></a>Comment déclencher une fonction lors de la réception d'un message de file d'attente Service Bus

Pour écrire une fonction que SDK WebJobs appelle lors de la réception d'un message de la file d'attente, utilisez l'attribut  `ServiceBusTrigger`. Le constructeur d'attribut prend un paramètre qui spécifie le nom de la file d'attente à interroger.

### Fonctionnement de ServicebusTrigger

Le Kit de développement logiciel reçoit un message en mode  `PeekLock` et appelle  `Complete` sur le message si la fonction se termine avec succès, ou appelle  `Abandon` si la fonction échoue. Si la fonction s'exécute au-delà du délai d'attente  `PeekLock`, le verrou est automatiquement renouvelé.

Service Bus effectue sa propre gestion de la file d'attente de messages incohérents, afin de n'être ni contrôlé par, ni configurable dans SDK WebJobs. 

### Message de file d'attente de chaîne

L'exemple de code suivant lit un message de la file d'attente qui contient une chaîne et écrit la chaîne dans le tableau de bord SDK WebJobs.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Remarque :** si vous créez les messages de file d'attente dans une application qui n'utilise pas SDK WebJobs, veillez à définir [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) sur " texte/brut ".

### Message de file d'attente OCT

Le Kit de développement logiciel désérialise automatiquement un message de file d'attente qui contient JSON pour un type OCT [(POCO - Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)). L'exemple de code suivant lit un message de file d'attente contenant un objet  `BlobInformation` doté d'une propriété  `BlobName` :

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Pour plus d'exemples de code montrant comment utiliser les propriétés de l'OCT pour fonctionner avec des objets blob et des tables au sein de la même fonction, consultez la [version des files d'attente de stockage de cet article](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs).

### Types avec lesquels ServiceBusTrigger fonctionne

En outre les types  `string` et OCT, vous pouvez utiliser l'attribut  `ServiceBusTrigger` avec un tableau d'octets ou un objet `BrokeredMessage`.

## <a id="create"></a>Création de messages de file d'attente Service Bus

Pour écrire une fonction qui crée une nouvelle utilisation de messages de file d'attente, utilisez l'attribut  `ServiceBus` et transmettez le nom de la file d'attente au constructeur d'attribut. 


### Création d'un message de file d'attente unique dans une fonction non async

L'exemple de code suivant utilise un paramètre de sortie pour créer un nouveau message dans la file d'attente nommée " outputqueue " avec le même contenu que le message reçu dans la file d'attente nommée " inputqueue ".

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

Le paramètre de sortie pour la création d'un message de file d'attente unique peut être l'un des types suivants :

* `string`
* `byte[]`
* `BrokeredMessage`
* Un type POCO sérialisable que vous définissez. Automatiquement sérialisé au format JSON.

Pour les paramètres de type OCT, un message de file d'attente est toujours créé lorsque la fonction se termine. Si le paramètre est null, le Kit de développement crée un message de file d'attente qui reprend la valeur null lorsque le message est reçu et désérialisé. Pour les autres types, si le paramètre est null, aucun message de file d'attente n'est créé.

### Création de plusieurs messages de file d'attente ou dans des fonctions asynchrones

Pour créer plusieurs messages, utilisez l'attribut  `ServiceBus` avec  `ICollector<T>` ou  `IAsyncCollector<T>`, comme illustré dans l'exemple de code suivant  :

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Chaque message de file d'attente est créé immédiatement quand la méthode  `Add` est appelée.

## <a id="topics"></a>Utilisation des rubriques Service Bus

Pour écrire une fonction que le Kit de développement appelle lorsqu'un message est reçu sur une rubrique Service Bus, utilisez l'attribut  `ServiceBusTrigger` avec le constructeur qui prend le nom de la rubrique et le nom d'abonnement, comme illustré dans l'exemple de code suivant :

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Pour créer un message dans une rubrique, utilisez l'attribut  `ServiceBus` avec un nom de rubrique de la même façon que vous l'utilisez avec un nom de file d'attente.

## <a id="queues"></a>Rubriques couvertes par l'article de procédure de files d'attente de stockage

Pour plus d'informations sur les scénarios de WebJobs SDK non spécifiques au Service Bus, consultez [Utilisation du stockage de file d'attente Azure avec SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Les sujets abordés dans cet article sont les suivants :

* Fonctions asynchrones
* Instances multiples
* Arrêt progressif
* Utilisation des attributs SDK WebJobs dans le corps d'une fonction
* Définition des chaînes de connexion du Kit de développement logiciel dans le code
* Définition des valeurs pour les paramètres du constructeur SDK WebJobs dans le code
* Déclenchement manuel d'une fonction
* Écriture de journaux

## <a id="nextsteps"></a>Étapes suivantes

Ce guide fournit des exemples de code qui montrent comment gérer des scénarios courants pour fonctionner avec Service Bus Azure. Pour plus d'informations sur l'utilisation d'Azure WebJobs et de SDK WebJobs, consultez [Azure WebJobs - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).



<!--HONumber=42-->
