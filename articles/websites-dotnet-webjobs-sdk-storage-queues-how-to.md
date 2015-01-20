<properties linkid="" pageTitle="Utilisation du stockage de file d'attente Azure avec le Kit de développement logiciel (SDK) WebJobs" metaKeywords="kit SDK WebJobs Azure stockage de file d'attente .NET C#" description="Découvrez comment utiliser le stockage de file d'attente Azure avec le Kit de développement logiciel (SDK) WebJobs. Créez et supprimez des files d'attente. Insérez, lisez, recevez et supprimez les messages de la file d'attente et bien plus encore." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Utilisation du stockage de file d'attente Azure avec le Kit de développement logiciel (SDK) WebJobs

Ce guide montre comment écrire du code C# pour des scénarios courants à l'aide du
service de stockage de file d'attente Azure et du Kit SDK WebJobs Azure, version 1.0.0.

Le guide part du principe que vous savez déjà [ce qu'est le Kit SDK Webjobs](../websites-webjobs-sdk-storage-queues-how-to) et [comment effectuer des tâches de base](../websites-dotnet-webjobs-sdk-get-started/), telles qu'installer le package NuGet du Kit SDK WebJobs, créer un compte de stockage Windows Azure et créer des chaînes de connexion pour le Kit SDK WebJobs qui pointent vers votre compte de stockage.

La plupart des extraits de code montrent uniquement les fonctions, et non le code qui crée l'objet `JobHost` comme dans cet exemple :

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Sommaire

-   [Comment déclencher une fonction lors de la réception d'un message de file d'attente](#trigger)
	- Messages de file d'attente de chaîne
	- Messages de file d'attente d'objet POCO
	- Fonctions asynchrones
	- Algorithme d'interrogation
	- Exécution en parallèle
	- Obtenir des métadonnées de file d'attente ou de message de file d'attente
	- Arrêt correct
-   [Comment créer un message de file d'attente lors du traitement d'un message de file d'attente](#createqueue)
	- Messages de file d'attente de chaîne
	- Messages de file d'attente d'objet POCO
	- Créer plusieurs messages
	- Utiliser des attributs de file d'attente dans le corps d'une fonction
-   [Comment lire et écrire des objets blob lors du traitement d'un message de file d'attente](#blobs)
	- Messages de file d'attente de chaîne
	- Messages de file d'attente d'objet POCO
	- Utiliser des attributs d'objets blob dans le corps d'une fonction
-   [Comment gérer les messages incohérents](#poison)
	- Gestion automatique des messages incohérents
	- Gestion manuelle des messages incohérents
-   [Comment définir les options de configuration](#config)
	- Définir les chaînes de connexion du Kit SDK dans le code
	- Configurer les paramètres QueueTrigger
	- Obtenir des noms de files d'attente à partir de la configuration
-   [Comment déclencher une fonction manuellement](#manual)
-   [Comment écrire des journaux](#logs)
-   [Étapes suivantes](#nextsteps)

## <a id="trigger"></a> Comment déclencher une fonction lors de la réception d'un message de file d'attente

Pour écrire une fonction que le Kit SDK appelle quand un message de file d'attente est reçu, utilisez l'attribut `QueueTrigger` avec une chaîne ou un paramètre POCO selon ce que vous vous attendez à obtenir dans le message. Le constructeur d'attribut prend un paramètre de chaîne qui spécifie le nom de la file d'attente à interroger. Vous pouvez également [définir de manière dynamique le nom de la file d'attente](#config).

Si votre site web s'exécute sur plusieurs machines virtuelles, la tâche web s'exécute sur chaque machine, qui attend des déclencheurs et essaie d'exécuter les fonctions. Dans certains scénarios, cela peut entraîner que certaines fonctions traitent deux fois les mêmes données ; ces fonctions doivent donc être idempotentes (écrites de façon que, si elles sont appelées de manière répétitive avec les mêmes données d'entrée, elles ne produisent pas des résultats en double).  

### Messages de file d'attente de chaîne

Dans l'exemple suivant, la file d'attente contient un message de chaîne ; ainsi, `QueueTrigger` est appliqué à un paramètre de chaîne nommé `logMessage` qui contient le contenu du message de la file d'attente. La fonction [écrit un message de journal dans le tableau de bord](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Outre `string`, le paramètre peut être un tableau d'octets, un objet `CloudQueueMessage` ou un objet POCO que vous définissez.

### Messages de file d'attente d'objet POCO

Dans l'exemple suivant, le message de file d'attente contient JSON pour un objet `BlobInformation` qui inclut une propriété `BlobName`. Le Kit SDK désérialise automatiquement l'objet.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

L'exemple suivant montre comment créer un message de file d'attente POCO sans l'aide du Kit SDK WebJobs, analysable par le Kit SDK. Le Kit SDK utilise le [package NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) pour sérialiser et désérialiser les messages.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Fonctions asynchrones

La fonction asynchrone suivante [écrit un journal dans le tableau de bord](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Les fonctions asynchrones peuvent prendre un jeton d'annulation, comme illustré dans l'exemple suivant, qui copie un objet blob. (Pour obtenir une explication de l'espace réservé `queueTrigger`, consultez la section [Objets blob](#blobs) .

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### Algorithme d'interrogation

Le Kit SDK implémente un algorithme d'interruption exponentiel et aléatoire pour réduire l'effet de l'interrogation de file d'attente inactive sur les coûts de stockage.  Quand un message est détecté, le Kit SDK attend deux secondes, puis vérifie s'il existe un autre message ; quand aucun message n'est détecté, il attend environ quatre secondes avant de réessayer. Après plusieurs échecs de tentatives d'obtention d'un message de file d'attente, le temps d'attente continue à augmenter jusqu'à ce qu'il atteigne le délai d'attente maximal par défaut (une minute). [Le délai d'attente maximal est configurable](#config).

### Exécution en parallèle

Si vous avez plusieurs fonctions à l'écoute sur différentes files d'attente, le Kit SDK les appelle en parallèle quand des messages sont reçus simultanément. 

Il en est de même quand plusieurs messages sont reçus en provenance d'une file d'attente unique. Par défaut, le Kit SDK obtient un lot de 16 messages de file d'attente à la fois et exécute la fonction qui les traite en parallèle. [La taille de lot est configurable](#config). Quand le nombre de messages en cours de traitement tombe sous la moitié de la taille de lot, le Kit SDK obtient un autre lot et commence à traiter ces messages. Par conséquent, le nombre maximal de messages traités simultanément par la fonction est une fois et demie la taille de lot. Cette limite s'applique séparément à chaque fonction qui a un attribut `QueueTrigger`. Si vous ne souhaitez pas d'exécution en parallèle pour les messages reçus sur une file d'attente, affectez la valeur 1 à la taille de lot.

### <a id="queuemetadata"></a>Obtenir des métadonnées de file d'attente ou de message de file d'attente

Vous pouvez obtenir les propriétés de messages suivantes en ajoutant des paramètres à la signature de méthode :

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contient le texte du message)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Si vous souhaitez travailler directement avec l'API de stockage Azure, vous pouvez également ajouter un paramètre `CloudStorageAccount`.

L'exemple suivant écrit toutes ces métadonnées dans un journal d'application INFO. Dans l'exemple, logMessage et queueTrigger contiennent le contenu du message de file d'attente.

		public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
		    DateTimeOffset expirationTime,
		    DateTimeOffset insertionTime,
		    DateTimeOffset nextVisibleTime,
		    string id,
		    string popReceipt,
		    int dequeueCount,
		    string queueTrigger,
		    CloudStorageAccount cloudStorageAccount,
		    TextWriter logger)
		{
		    logger.WriteLine(
		        "logMessage={0}\n" +
			"expirationTime={1}\ninsertionTime={2}\n" +
		        "nextVisibleTime={3}\n" +
		        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
		        "queue endpoint={7} queueTrigger={8}",
		        logMessage, expirationTime,
		        insertionTime,
		        nextVisibleTime, id,
		        popReceipt, dequeueCount,
		        cloudStorageAccount.QueueEndpoint,
		        queueTrigger);
		}

Voici un exemple de journal écrit par l'exemple de code :

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

### <a id="graceful"></a>Arrêt correct

Une fonction qui s'exécute dans une tâche web continue peut accepter un paramètre `CancellationToken` qui permet au système d'exploitation de notifier la fonction quand la tâche web est sur le point de se terminer. Vous pouvez utiliser cette notification pour vous assurer que la fonction ne s'arrête pas de manière inattendue et laisse les données dans un état incohérent.

L'exemple suivant montre comment vérifier l'arrêt imminent d'une tâche web dans une fonction.

	public static void GracefulShutdownDemo(
	            [QueueTrigger("inputqueue")] string inputText,
	            TextWriter logger,
	            CancellationToken token)
	{
	    for (int i = 0; i < 100; i++)
	    {
	        if (token.IsCancellationRequested)
	        {
	            logger.WriteLine("Function was cancelled at iteration {0}", i);
	            break;
	        }
	        Thread.Sleep(1000);
	        logger.WriteLine("Normal processing for queue message={0}", inputText);
	    }
	}

**Remarque :** le tableau de bord peut ne pas afficher correctement l'état et la sortie des fonctions qui ont été arrêtées.
 
Pour plus d'informations, consultez [Arrêt correct de WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a> Comment créer un message de file d'attente lors du traitement d'un message de file d'attente

Pour écrire une fonction qui crée un message de file d'attente, utilisez l'attribut `Queue` sur une chaîne de sortie ou le paramètre POCO. Comme `QueueTrigger`, vous passez le nom de la file d'attente sous forme de chaîne ou vous pouvez [définir de manière dynamique le nom de la file d'attente](#config).

### Messages de file d'attente de chaîne

L'exemple suivant crée un message de file d'attente dans la file d'attente nommée " outputqueue " avec le même contenu que le message de file d'attente reçu dans la file d'attente nommée " inputqueue ".

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

Si le type de paramètre de sortie est l'un des types suivants et que l'objet n'est pas null quand la fonction se termine, le Kit SDK crée un message de file d'attente :

* `string` 
* `byte[]`
* Un type POCO sérialisable que vous définissez
* `CloudQueueMessage`

Pour créer plusieurs messages, consultez **Créer plusieurs messages** plus loin dans cette section.

Vous pouvez également utiliser `CloudQueue` comme type de paramètre de sortie si vous souhaitez envoyer des messages manuellement.

### Messages de file d'attente d'objet POCO

Pour créer un message de file d'attente qui contient un objet POCO plutôt qu'une chaîne, passez le type POCO comme paramètre de sortie au constructeur d'attribut `Queue`. 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

Le Kit SDK sérialise automatiquement l'objet au format JSON. Un message de file d'attente est toujours créé, même si l'objet est null.

### Créer plusieurs messages

Pour créer plusieurs messages, affectez `ICollector<T>` ou `IAsyncCollector` comme type de paramètre pour la file d'attente de sortie, comme indiqué dans l'exemple suivant.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Chaque message de file d'attente est créé immédiatement quand la méthode `Add` est appelée.

### <a id="queuebody"></a>Utiliser des attributs de file d'attente dans le corps d'une fonction

Si vous avez besoin d'effectuer un travail dans votre fonction avant d'utiliser l'attribut `Queue` pour créer un message de file d'attente, vous pouvez utiliser l'interface `IBinder` pour obtenir un objet `CloudQueue` qui vous permet de travailler directement avec une file d'attente. 

L'exemple suivant prend un message de file d'attente d'entrée et crée un message avec le même contenu dans une file d'attente de sortie. Le nom de file d'attente de sortie est défini par le code dans le corps de la fonction.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> Comment lire et écrire des objets blob et des tables lors du traitement d'un message de file d'attente

Les attributs `Blob` et `Table` permettent de lire et d'écrire des objets blob et des tables. Les exemples de cette section s'appliquent aux objets blob.

Vous pouvez utiliser l'attribut `Blob` avec, entre autres, les types `Stream`, `TextReader`, `TextWriter` et `CloudBlockBlob`. Le constructeur d'attribut prend un paramètre `blobPath` qui spécifie le conteneur et le nom de l'objet blob ; quand l'attribut décore un objet `Stream`, un autre paramètre de constructeur spécifie le mode `FileAccess` en lecture, écriture ou lecture/écriture. Si vous devez effectuer un travail dans votre fonction avant de lier un objet blob à un objet, vous pouvez utiliser l'attribut dans le corps de la fonction [comme indiqué plus haut pour l'attribut Queue](#queuebody).

### Messages de file d'attente de chaîne

Pour un message de file d'attente qui contient une chaîne, `queueTrigger` est un espace réservé que vous pouvez utiliser dans le paramètre `blobPath` de l'attribut `Blob` qui contient le contenu du message. 

L'exemple suivant utilise des objets `Stream` pour lire et écrire des objets blob. Le message de file d'attente fournit le nom d'un objet blob situé dans le conteneur textblobs. Une copie de l'objet blob avec " -new " ajouté au nom est créée dans le même conteneur. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

L'exemple suivant utilise un objet `CloudBlockBlob` pour supprimer un objet blob.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### Messages de file d'attente d'objet POCO

Pour les objets POCO stockés au format JSON dans le message de file d'attente, vous pouvez utiliser des espaces réservés qui nomment des propriétés de l'objet dans le paramètre `blobPath` de l'attribut `Queue`. Vous pouvez également utiliser des [noms de propriétés de métadonnées de file d'attente](#queuemetadata) comme espaces réservés. 

L'exemple suivant copie un objet blob vers un nouvel objet blob avec une autre extension, en utilisant des propriétés de l'objet `BlobInformation` pour spécifier les noms des objets blob d'entrée et de sortie. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

L'exemple suivant montre comment créer un message de file d'attente POCO sans l'aide du Kit SDK WebJobs, analysable par le Kit SDK. Le Kit SDK utilise le [package NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) pour sérialiser et désérialiser les messages.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> Comment gérer les messages incohérents

Les messages dont le contenu provoque l'échec d'une fonction sont appelés *messages incohérents*. Quand la fonction échoue, le message de file d'attente n'est pas supprimé et il est finalement récupéré, provoquant la répétition du cycle. Le Kit SDK peut interrompre automatiquement le cycle après un nombre limité d'itérations, ou vous pouvez le faire manuellement.

### Gestion automatique des messages incohérents

Le Kit SDK appelle une fonction jusqu'à cinq fois pour traiter un message de file d'attente. Si la cinquième tentative échoue, le message est déplacé vers une file d'attente de messages incohérents. [Vous pouvez configurer le nombre maximal de tentatives](#config). 

La file d'attente de messages incohérents se nomme *{nom_file_d'attente_d'origine}*-poison. Vous pouvez écrire une fonction pour traiter les messages de la file d'attente de messages incohérents en les consignant dans un journal ou en envoyant une notification signalant qu'une attention manuelle est nécessaire. 

Dans l'exemple suivant, la fonction `CopyBlob` échoue quand un message de file d'attente contient le nom d'un objet blob qui n'existe pas. Quand cela se produit, le message est déplacé de la file d'attente copyblobqueue vers la file d'attente copyblobqueue-poison. `ProcessPoisonMessage` consigne ensuite le message incohérent dans le journal.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}
		
		public static void ProcessPoisonMessage(
		    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
		{
		    logger.WriteLine("Failed to copy blob, name=" + blobName);
		}

L'illustration suivante montre la sortie de console de ces fonctions lorsqu'un message incohérent est traité.

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Gestion manuelle des messages incohérents

Vous pouvez obtenir le nombre de fois qu'un message a été récupéré pour le traitement en ajoutant un paramètre `int` nommé `dequeueCount` à votre fonction. Vous pouvez ensuite vérifier le nombre d'enlèvements de la file d'attente dans le code de fonction et effectuer votre propre gestion des messages incohérents quand le nombre dépasse un seuil, comme illustré dans l'exemple suivant.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 5)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

Pour que ce code fonctionne comme prévu, vous devrez augmenter le nombre maximal de nouvelles tentatives pour la gestion automatique des messages incohérents ou le nombre d'enlèvements de la file d'attente dans cet exemple ne dépasserait jamais cinq.

## <a id="config"></a> Comment définir les options de configuration

Vous pouvez utiliser le type `JobHostConfiguration` pour définir les options de configuration suivantes :

* Définition des chaînes de connexion du Kit SDK dans le code.
* Configuration des paramètres `QueueTrigger` tels que le nombre maximal d'enlèvements de la file d'attente.
* Obtention des noms de files d'attente à partir de la configuration.

### <a id="setconnstr"></a>Définir les chaînes de connexion du Kit SDK dans le code

La définition des chaînes de connexion du Kit SDK dans le code vous permet d'utiliser vos propres noms de chaînes de connexion dans les fichiers de configuration ou les variables d'environnement, comme illustré dans l'exemple suivant.

		static void Main(string[] args)
		{
		    var _storageConn = ConfigurationManager
		        .ConnectionStrings["MyStorageConnection"].ConnectionString;
		
		    var _dashboardConn = ConfigurationManager
		        .ConnectionStrings["MyDashboardConnection"].ConnectionString;
		
		    var _serviceBusConn = ConfigurationManager
		        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
		
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.StorageConnectionString = _storageConn;
		    config.DashboardConnectionString = _dashboardConn;
		    config.ServiceBusConnectionString = _serviceBusConn;
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueue"></a>Configurer les paramètres de file d'attente

Vous pouvez configurer les paramètres suivants qui s'appliquent au traitement des messages de file d'attente :

- Le nombre maximal de messages de file d'attente récupérés simultanément pour être exécutés en parallèle (la valeur par défaut est 16).
- Le nombre maximal de nouvelles tentatives avant qu'un message de file d'attente soit envoyé vers une file d'attente de messages incohérents (la valeur par défaut est cinq).
- La durée d'attente maximale avant une nouvelle interrogation quand une file d'attente est vide (la valeur par défaut est une minute).

L'exemple suivant montre comment configurer ces paramètres :

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>Obtenir des noms de files d'attente à partir de la configuration

Le type `JobHostConfiguration` permet de passer un objet `NameResolver` qui fournit le nom de la file d'attente au Kit SDK pour les attributs `QueueTrigger` et `Queue`.

Par exemple, supposons que vous souhaitez utiliser une file d'attente nommée logqueuetest dans l'environnement de test et une autre nommée logqueueprod en production. Au lieu d'un nom de file d'attente codé en dur, vous souhaitez spécifier le nom d'une entrée dans la collection `appSettings` ayant le nom de la file d'attente réelle. Si la clé `appSettings` est logqueue, votre fonction pourrait ressembler à l'exemple suivant.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

Votre classe " NameResolver " pu obtenir ensuite le nom de la file d'attente à partir de " appSettings " comme indiqué dans l'exemple suivant :

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Vous passez la classe `NameResolver` dans l'objet `JobHost` comme indiqué dans l'exemple suivant.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>Comment déclencher une fonction manuellement

Pour déclencher une fonction manuellement, utilisez la méthode `Call` ou `CallAsync` sur l'objet `JobHost` et l'attribut `NoAutomaticTrigger` sur la fonction, comme indiqué dans l'exemple suivant. 

		public class Program
		{
		    static void Main(string[] args)
		    {
		        JobHost host = new JobHost();
		        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
		    }
		
		    [NoAutomaticTrigger]
		    public static void CreateQueueMessage(
		        TextWriter logger, 
		        string value, 
		        [Queue("outputqueue")] out string message)
		    {
		        message = value;
		        logger.WriteLine("Creating queue message: ", message);
		    }
		}

## <a id="logs"></a>Comment écrire des journaux

Pour écrire des journaux qui apparaissent dans la page de tableau de bord WebJobs liée à un appel de fonction particulier, utilisez un objet `TextWriter` que vous obtenez à partir d'un paramètre dans votre signature de méthode.

Pour écrire des [journaux de suivi d'application](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview), utilisez `Console.Out` (crée des journaux marqués comme INFO) et `Console.Error` (crée des journaux marqués comme ERROR). Une alternative consiste à utiliser [Trace ou TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx).

Les journaux d'application apparaissent dans les fichiers journaux du site web, les tables Azure ou les objets blob Azure, selon la configuration de votre site web Azure. Les 100 journaux d'application les plus récents apparaissent également dans le tableau de bord si le programme s'exécute dans une tâche web Azure. (Aucun journal d'application n'apparaît dans le tableau de bord à partir d'un programme qui s'exécute localement ou dans tout autre environnement.)   

Vous pouvez désactiver la journalisation en [affectant la valeur null à la chaîne de connexion du tableau de bord](#config).

L'exemple suivant montre plusieurs manières d'écrire des journaux :

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

Dans le tableau de bord du Kit SDK WebJobs, la sortie de l'objet `TextWriter` apparaît quand vous accédez à la page pour l'appel d'une fonction particulière et que vous cliquez sur **Toggle Output** :

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Dans le tableau de bord du Kit SDK WebJobs, les 100 lignes des journaux d'applications les plus récentes apparaissent quand vous accédez à la page de la tâche web (et non à celle de l'appel de fonction) et que vous cliquez sur **Toggle Output**.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

Dans une tâche web continue, les journaux des applications apparaissent dans /data/jobs/continuous/*{webjobname}*/job_log.txt dans le système de fichiers du site web.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Dans un objet blob Azure, les journaux d'application ressemblent à ceci :
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Dans une table Azure, les journaux `Console.Out` et `Console.Error` ressemblent à ceci :

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Étapes suivantes

Cette rubrique fournit des exemples de code qui montrent comment gérer des scénarios courants d'utilisation des files d'attente Azure. Pour plus d'informations sur le Kit de développement logiciel (SDK) WebJobs Azure, consultez [Tâches web Azure - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).

<!--HONumber=35.2-->
