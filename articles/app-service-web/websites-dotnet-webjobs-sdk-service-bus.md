<properties 
	pageTitle="Utilisation de Microsoft Azure Service Bus avec le Kit de développement logiciel (SDK) WebJobs" 
	description="Apprenez à utiliser les files d’attente et les rubriques Azure Service Bus avec le Kit de développement logiciel (SDK) WebJobs." 
	services="app-service\web, service-bus" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="tdykstra"/>

# Utilisation de Microsoft Azure Service Bus avec le Kit de développement logiciel (SDK) WebJobs

## Vue d’ensemble

Ce guide fournit des exemples de code c# qui montrent comment déclencher un processus lors de la réception d’un message Azure Service Bus. Les exemples de code utilisent le [Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk.md) version 1.x.

Ce guide suppose que vous savez [comment créer un projet WebJob dans Visual Studio avec des chaînes de connexion qui pointent vers votre compte de stockage](websites-dotnet-webjobs-sdk-get-started.md).

Les extraits de code présentent uniquement les fonctions, et non le code chargé de créer l’objet `JobHost` comme dans cet exemple :

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

Le référentiel azure-webjobs-sdk-samples sur GitHub.com contient un [exemple de code complet Service Bus](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs).

## <a id="prerequisites"></a> Conditions préalables

Pour utiliser Service Bus, vous devez installer le package NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) en plus des autres packages du Kit de développement logiciel (SDK) WebJobs.

Vous devez aussi définir la chaîne de connexion AzureWebJobsServiceBus en plus des chaînes de connexion de stockage. Pour cela, vous pouvez utiliser la section `connectionStrings` du fichier App.config, comme illustré dans l’exemple suivant :

		<connectionStrings>
		    <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
		</connectionStrings>

Pour un exemple de projet incluant la valeur de la chaîne de connexion de Service Bus dans le fichier App.config, consultez [Exemple de Service Bus](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus).

Les chaînes de connexion peuvent également être définies dans l’environnement d’exécution Azure, qui remplace ensuite les valeurs de App.config quand la tâche web s’exécute dans Azure. Pour plus d’informations, consultez [Prise en main du SDK WebJobs](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account).

## <a id="trigger"></a> Déclenchement d’une fonction durant la réception d’un message en file d’attente Service Bus

Pour écrire une fonction que le Kit de développement logiciel (SDK) WebJobs appelle durant la réception d’un message en file d’attente, utilisez l’attribut `ServiceBusTrigger`. Le constructeur d’attribut prend un paramètre qui spécifie le nom de la file d’attente à interroger.

### Fonctionnement de ServiceBusTrigger

Le Kit de développement logiciel (SDK) reçoit un message en mode `PeekLock` et appelle l’élément `Complete` sur le message si la fonction se termine correctement. Si la fonction échoue, il appelle l’élément `Abandon`. Si la fonction s’exécute au-delà du délai imparti à `PeekLock`, le verrou est automatiquement renouvelé.

Service Bus effectue sa propre gestion de la file d’attente de messages incohérents, qui ne peut pas être contrôlée ou configurée par le Kit de développement logiciel (SDK) WebJobs.

### Message de file d’attente de chaîne

L’exemple de code suivant lit un message de file d’attente qui contient une chaîne qu’il écrit dans le tableau de bord du Kit de développement logiciel (SDK) WebJobs.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Remarque :** si vous créez les messages en file d’attente dans une application qui n’utilise pas le Kit de développement logiciel (SDK) WebJobs, veillez à attribuer au paramètre [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) la valeur « text/plain ».

### Message de file d’attente POCO

Le Kit de développement logiciel (SDK) désérialise automatiquement un message en file d’attente qui contient JSON pour un type d’objet POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)). L’exemple de code suivant lit un message en file d’attente qui contient un objet `BlobInformation` doté d’une propriété `BlobName` :

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Pour obtenir des exemples de code montrant comment utiliser les propriétés de l’objet POCO de façon à les rendre compatibles avec les objets blob et les tables contenus d’une même fonction, consultez la [version de cet article qui traite des files d’attente de stockage](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Si votre code qui crée le message de la file d'attente n'utilise pas le SDK WebJobs, utilisez du code similaire à l'exemple suivant :

		var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
		BlobInformation blobInformation = new BlobInformation () ;
		var message = new BrokeredMessage(blobInformation);
		client.Send(message);

### Types utilisés par ServiceBusTrigger

Outre les types d’objet POCO et `string`, vous pouvez utiliser l’attribut `ServiceBusTrigger` avec un tableau d’octets ou un objet `BrokeredMessage`.

## <a id="create"></a> Création de messages de file d’attente Service Bus

Pour écrire une fonction qui crée un message de file d’attente, utilisez l’attribut `ServiceBus` et transmettez le nom de la file d’attente au constructeur d’attribut.


### Création d’un message de file d’attente unique dans une fonction non asynchrone

L’exemple de code suivant utilise un paramètre de sortie pour créer un message dans la file d’attente « outputqueue » avec le même contenu que le message reçu dans la file d’attente « inputqueue ».

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

Le paramètre de sortie utilisé pour créer un message de file d’attente unique peut être de l’un des types suivants :

* `string`
* `byte[]`
* `BrokeredMessage`
* Type POCO sérialisable que vous définissez. Sérialisé automatiquement au format JSON.

Pour les paramètres de type POCO, un message de file d’attente est toujours créé au moment où la fonction se termine ; si le paramètre a la valeur null, le Kit de développement logiciel (SDK) crée un message de file d’attente qui retourne la valeur null quand le message est reçu et désérialisé. Pour les autres types, si le paramètre a la valeur null, aucun message de file d’attente n’est créé.

### Création de plusieurs messages de file d’attente dans des fonctions asynchrones

Pour créer plusieurs messages, utilisez l’attribut `ServiceBus` avec `ICollector<T>` ou `IAsyncCollector<T>`, comme illustré dans l’exemple de code suivant :

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Chaque message de file d’attente est créé immédiatement après l’appel de la méthode `Add`.

## <a id="topics"></a>Utilisation des rubriques Service Bus

Pour écrire une fonction que le Kit de développement logiciel (SDK) appelle au moment où un message est reçu sur une rubrique Service Bus, utilisez l’attribut `ServiceBusTrigger` avec le constructeur qui prend le nom de rubrique et le nom d’abonnement, comme illustré dans l’exemple de code suivant :

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Pour créer un message sur une rubrique, utilisez l’attribut `ServiceBus` avec un nom de rubrique, comme vous le faites avec un nom de file d’attente.

## Fonctionnalités ajoutées dans la version 1.1

Les fonctionnalités suivantes ont été ajoutées dans la version 1.1 :

* Autoriser la personnalisation complète du traitement des messages via `ServiceBusConfiguration.MessagingProvider`.
* `MessagingProvider` prend en charge la personnalisation de `MessagingFactory` et `NamespaceManager` de Service Bus.
* Un modèle de stratégie `MessageProcessor` vous permet de spécifier un processeur par file d'attente/rubrique.
* L'accès concurrentiel de traitement des message est pris en charge par défaut. 
* Personnalisation facile de `OnMessageOptions` via `ServiceBusConfiguration.MessageOptions`.
* Autoriser la spécification des [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) sur `ServiceBusTriggerAttribute`/`ServiceBusAttribute` (pour les scénarios où vous ne disposez pas des droits de gestion). 

## <a id="queues"></a>Sujets connexes traités dans l’article de procédure relatif aux files d’attente de stockage

Pour en savoir plus sur les scénarios de Kit de développement logiciel (SDK) WebJobs non spécifiques de Service Bus, voir [Utilisation du stockage de file d’attente Azure avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Les sujets abordés dans cet article sont les suivants :

* Fonctions asynchrones
* Instances multiples
* Arrêt approprié
* Utilisation des attributs du Kit de développement logiciel (SDK) WebJobs dans le corps d’une fonction
* Définition des chaînes de connexion du SDK dans le code
* Définition des valeurs des paramètres de constructeur du Kit de développement logiciel (SDK) WebJobs dans le code
* Déclenchement manuel d’une fonction
* Écriture de journaux

## <a id="nextsteps"></a>Étapes suivantes

Ce guide fournit des exemples de code qui indiquent comment gérer des scénarios courants pour l’utilisation d’Azure Service Bus. Pour plus d’informations sur l’utilisation d’Azure Webjobs et du Kit de développement logiciel (SDK) WebJobs Azure, consultez la rubrique [Azure Webjobs - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=AcomDC_0406_2016-->