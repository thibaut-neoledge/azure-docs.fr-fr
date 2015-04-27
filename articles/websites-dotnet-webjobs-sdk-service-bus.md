<properties 
	pageTitle="Utilisation d'Azure Service Bus avec le Kit de développement logiciel (SDK) WebJobs" 
	description="Apprenez à utiliser les files d'attente et les rubriques Azure Service Bus avec le Kit de développement logiciel (SDK) WebJobs." 
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

# Utilisation d'Azure Service Bus avec le Kit de développement logiciel (SDK) WebJobs

## Vue d'ensemble

Ce guide fournit des exemples de code c# qui montrent comment déclencher un processus pendant la création ou la mise à jour d'un objet blob Azure. Les exemples de code utilisent le [Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk.md) version 1.x.

Le guide suppose que vous savez [comment créer un projet WebJob dans Visual Studio avec des chaînes de connexion qui pointent vers votre compte de stockage](websites-dotnet-webjobs-sdk-get-started.md).

Les extraits de code présentent uniquement les fonctions, et non le code chargé de créer l'objet `JobHost` comme dans cet exemple :

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## <a id="prerequisites"></a> Conditions préalables

Pour utiliser Service Bus, vous devez installer le package NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) en plus des autres packages du SDK WebJobs. 

Vous devez aussi définir la chaîne de connexion AzureWebJobsServiceBus en plus des chaînes de connexion de stockage.

## <a id="trigger"></a> Déclenchement d'une fonction durant la réception d'un message de file d'attente Service Bus

Pour écrire une fonction que le SDK WebJobs appelle durant la réception d'un message de file d'attente, utilisez l'attribut `ServiceBusTrigger`. Le constructeur d'attribut prend un paramètre qui spécifie le nom de la file d'attente à interroger.

### Fonctionnement de ServicebusTrigger

Le Kit de développement logiciel (SDK) reçoit un message en mode `PeekLock` et appelle `Complete` sur le message si la fonction se termine correctement. Si la fonction échoue, il appelle `Abandon`. Si la fonction s'exécute plus longtemps que le délai imparti à  `PeekLock`, le verrou est automatiquement renouvelé.

Service Bus assure sa propre gestion de la file d'attente des messages incohérents pour éviter que le SDK WebJobs puisse la contrôler ou la configurer. 

### Message de file d'attente de chaîne

L'exemple de code suivant lit un message de file d'attente qui contient une chaîne qu'il écrit dans le tableau de bord du SDK WebJobs.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Remarque :** si vous créez les messages de file d'attente dans une application qui n'utilise pas le SDK WebJobs, veillez à attribuer à[BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) la valeur " text/plain ".

### Message de file d'attente POCO

Le Kit de développement logiciel (SDK) désérialise automatiquement un message de file d'attente qui contient JSON pour un type d'objet POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)). L'exemple de code suivant lit un message de file d'attente qui contient un objet `BlobInformation` doté d'une propriété `BlobName` :

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Pour obtenir des exemples de code montrant comment utiliser les propriétés de l'objet POCO de façon à les rendre compatibles avec les objets blob et les tables contenus d'une même fonction, consultez la [version de cet article qui traite des files d'attente de stockage](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs).

### Types utilisés par ServiceBusTrigger

Outre les types `string` et POCO,  vous pouvez utiliser l'attribut `ServiceBusTrigger` avec un tableau d'octets ou un objet `BrokeredMessage`.

## <a id="create"></a> Création de messages de file d'attente Service Bus

Pour écrire une fonction qui crée un message de file d'attente, utilisez l'attribut `ServiceBus` et transmettez le nom de la file d'attente au constructeur d'attribut. 


### Création d'un message de file d'attente unique dans une fonction non asynchrone

L'exemple de code suivant utilise un paramètre de sortie pour créer un message dans la file d'attente " outputqueue " avec le même contenu que le message reçu dans la file d'attente " inputqueue ".

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

Le paramètre de sortie utilisé pour créer un message de file d'attente unique peut être de l'un des types suivants :

* `string`
* `byte[]`
* `BrokeredMessage`
* Type POCO sérialisable que vous définissez. Sérialisé automatiquement au format JSON.

Pour les paramètres de type POCO, un message de file d'attente est toujours créé au moment où la fonction se termine ; si le paramètre a la valeur null, le SDK crée un message de file d'attente qui retourne la valeur null quand le message est reçu et désérialisé. Pour les autres types, si le paramètre a la valeur null, aucun message de file d'attente n'est créé.

### Création de plusieurs messages de file d'attente dans des fonctions asynchrones

Pour créer plusieurs messages, utilisez l'attribut `ServiceBus` avec `ICollector<T>` ou `IAsyncCollector<T>`, comme illustré dans l'exemple de code suivant :

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Chaque message de file d'attente est créé de suite après que la méthode `Add` est appelée.

## <a id="topics"></a>Utilisation des rubriques Service Bus

Pour écrire une fonction que le Kit de développement logiciel (SDK) appelle au moment où un message est reçu sur une rubrique Service Bus, utilisez l'attribut `ServiceBusTrigger` avec le constructeur qui prend le nom de rubrique et le nom d'abonnement, comme illustré dans l'exemple de code suivant :

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Pour créer un message sur une rubrique, utilisez l'attribut `ServiceBus` avec un nom de rubrique comme vous le faites avec un nom de file d'attente.

## <a id="queues"></a>Sujets connexes traités dans l'article de procédure relatif aux files d'attente de stockage

Pour plus d'informations sur les scénarios de SDK WebJobs non spécifiques à Service Bus, consultez la rubrique [Utilisation du stockage de file d'attente Azure avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Les sujets abordés dans cet article sont les suivants :

* Fonctions asynchrones
* Instances multiples
* Arrêt approprié
* Utilisation des attributs du SDK WebJobs dans le corps d'une fonction
* Définition des chaînes de connexion du SDK dans le code
* Définition des valeurs des paramètres de constructeur du SDK WebJobs dans le code
* Déclenchement manuel d'une fonction
* Écriture de journaux

## <a id="nextsteps"></a> Étapes suivantes

Ce guide vous a présenté des exemples de code qui montrent comment gérer des scénarios courants pour utiliser Azure Service Bus. Pour plus d'informations sur l'utilisation d'Azure WebJobs et du Kit de développement logiciel (SDK) WebJobs, consultez la rubrique [Ressources recommandées Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

<!--HONumber=45--> 
