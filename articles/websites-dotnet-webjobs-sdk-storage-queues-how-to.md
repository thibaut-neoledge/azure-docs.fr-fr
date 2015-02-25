<properties 
	pageTitle="Comment utiliser le stockage de la file d'attente Azure avec SDK WebJobs" 
	description="Apprenez à utiliser le stockage de la file d'attente Azure avec SDK WebJobs. Create and delete queues; insert, peek, get, and delete queue messages, and more." 
	services="web-sites, storage" 
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

# Comment utiliser le stockage de la file d'attente Azure avec SDK WebJobs

Ce guide fournit des exemples de code C# qui montrent comment utiliser la version 1.x du Kit de développement logiciel Azure WebJobs avec le service de stockage de file d'attente Azure.

Le guide suppose que vous savez [créer un projet WebJob dans Visual Studio avec des chaînes de connexion qui pointent vers votre compte de stockage](../websites-dotnet-webjobs-sdk-get-started/).

La plupart des extraits de code montrent uniquement les fonctions, et non le code qui crée l'objet  `JobHost` comme dans cet exemple :

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Sommaire

-   [Comment déclencher une fonction lors de la réception d'un message de file d'attente](#trigger)
	- Messages de file d'attente de chaîne
	- Messages de file d'attente OCT
	- Fonctions asynchrones
	- Types avec lesquels l'attribut QueueTrigger fonctionne
	- Algorithme d'interrogation
	- Instances multiples
	- Exécution en parallèle
	- Obtenir des métadonnées de file d'attente ou de message de file d'attente
	- Arrêt progressif
-   [Comment créer un message de file d'attente lors du traitement d'un message de file d'attente](#createqueue)
	- Messages de file d'attente de chaîne
	- Messages de file d'attente OCT
	- Création de plusieurs messages ou dans des fonctions asynchrones
	- Types avec lesquels l'attribut Queue fonctionne
	- Utilisation des attributs SDK WebJobs dans le corps d'une fonction
-   [Comment lire et écrire des objets blob lors du traitement d'un message de file d'attente](#blobs)
	- Messages de file d'attente de chaîne
	- Messages de file d'attente OCT
	- Types avec lesquels l'attribut Blob fonctionne
-   [Comment gérer les messages incohérents](#poison)
	- Gestion automatique des messages incohérents
	- Gestion manuelle des messages incohérents
-   [Comment définir les options de configuration](#config)
	- Définir les chaînes de connexion du Kit SDK dans le code
	- Configurer les paramètres QueueTrigger
	- Définition des valeurs pour les paramètres du constructeur SDK WebJobs dans le code
-   [Comment déclencher une fonction manuellement](#manual)
-   [Comment écrire des journaux](#logs)
-   [Étapes suivantes](#nextsteps)

## <a id="trigger"></a>Comment déclencher une fonction lors de la réception d'un message de file d'attente

Pour écrire une fonction que SDK WebJobs appelle lors de la réception d'un message de la file d'attente, utilisez l'attribut  `QueueTrigger`. Le constructeur d'attribut prend un paramètre de chaîne qui spécifie le nom de la file d'attente à interroger. Vous pouvez également [définir de manière dynamique le nom de la file d'attente](#config).

### Messages de file d'attente de chaîne

Dans l'exemple suivant, la file d'attente contient un message de chaîne ; ainsi,  `QueueTrigger` est appliqué à un paramètre de chaîne nommé  `logMessage` qui contient le contenu du message de la file d'attente. La fonction [écrit un message de journal dans le tableau de bord](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Outre  `string`, le paramètre peut être un tableau d'octets, un objet  `CloudQueueMessage` ou un objet OCT que vous définissez.

### Messages de file d'attente OCT [(POCO - Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Dans l'exemple suivant, le message de file d'attente contient JSON pour un objet  `BlobInformation` qui inclut une propriété  `BlobName`. Le Kit SDK désérialise automatiquement l'objet.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Le Kit SDK utilise le [package NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) pour sérialiser et désérialiser les messages. Si vous créez des messages de la file d'attente dans un programme qui n'utilise pas SDK WebJobs, vous pouvez écrire le code comme dans l'exemple suivant pour créer un message de file d'attente OCT que le Kit de développement peut analyser. 

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Fonctions asynchrones

La fonction asynchrone suivante [écrit un journal dans le tableau de bord](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Les fonctions asynchrones peuvent prendre un [jeton d'annulation](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), comme illustré dans l'exemple suivant, qui copie un objet blob. (Pour obtenir une explication de l'espace réservé  `queueTrigger`, consultez la section [Objets blob](#blobs) .)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### <a id="qtattributetypes"></a>Types avec lesquels l'attribut QueueTrigger fonctionne

Vous pouvez utiliser  `QueueTrigger` avec les types suivants :

* `string`
* Un type OCT sérialisé au format JSON
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a>Algorithme d'interrogation

Le Kit de développement logiciel (SDK) implémente un algorithme d'interruption exponentielle aléatoire pour réduire l'effet d'interrogation de l'inactivité de file d'attente sur les coûts de transaction de stockage.  Quand un message est détecté, le Kit SDK attend deux secondes, puis vérifie s'il existe un autre message ; quand aucun message n'est détecté, il attend environ quatre secondes avant de réessayer. Après plusieurs échecs de tentatives d'obtention d'un message de file d'attente, le temps d'attente continue à augmenter jusqu'à ce qu'il atteigne le délai d'attente maximal par défaut (une minute). [Le délai d'attente maximal est configurable](#config).

### <a id="instances"></a>Instances multiples

Si votre site Web s'exécute sur plusieurs instances, une tâche Web continue s'exécute sur chaque machine, qui attend des déclencheurs et essaie d'exécuter les fonctions. Dans certains scénarios, cela peut entraîner que certaines fonctions traitent deux fois les mêmes données ; ces fonctions doivent donc être idempotentes (écrites de façon que, si elles sont appelées de manière répétitive avec les mêmes données d'entrée, elles ne produisent pas des résultats en double).  

### <a id="parallel"></a>Exécution en parallèle

Si vous avez plusieurs fonctions à l'écoute sur différentes files d'attente, le Kit SDK les appelle en parallèle quand des messages sont reçus simultanément. 

Il en est de même quand plusieurs messages sont reçus en provenance d'une file d'attente unique. Par défaut, le Kit SDK obtient un lot de 16 messages de file d'attente à la fois et exécute la fonction qui les traite en parallèle. [La taille de lot est configurable](#config). Quand le nombre de messages en cours de traitement tombe sous la moitié de la taille de lot, le Kit SDK obtient un autre lot et commence à traiter ces messages. Par conséquent, le nombre maximal de messages traités simultanément par la fonction est une fois et demie la taille de lot. Cette limite s'applique séparément à chaque fonction qui a un attribut  `QueueTrigger`. Si vous ne souhaitez pas d'exécution en parallèle pour les messages reçus sur une file d'attente, affectez la valeur 1 à la taille de lot.

### <a id="queuemetadata"></a>Obtention des métadonnées de file d'attente ou de message de file d'attente

Vous pouvez obtenir les propriétés de message suivantes en ajoutant des paramètres à la signature de méthode :

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contains message text)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Si vous souhaitez travailler directement avec l'API de stockage Azure, vous pouvez également ajouter un paramètre  `CloudStorageAccount`.

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

### <a id="graceful"></a>Arrêt progressif

Une fonction qui s'exécute dans une tâche Web continue peut accepter un paramètre  `CancellationToken` qui permet au système d'exploitation de notifier la fonction quand la tâche Web est sur le point de se terminer. Vous pouvez utiliser cette notification pour vous assurer que la fonction ne s'arrête pas de manière inattendue et laisse les données dans un état incohérent.

L'exemple suivant montre comment vérifier l'arrêt imminent d'une tâche Web dans une fonction.

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

## <a id="createqueue"></a>Comment créer un message de file d'attente lors du traitement d'un message de file d'attente

Pour écrire une fonction qui crée un message de file d'attente, utilisez l'attribut  `Queue`. Comme  `QueueTrigger`, vous passez le nom de la file d'attente sous forme de chaîne ou vous pouvez [définir de manière dynamique le nom de la file d'attente](#config).

### Messages de file d'attente de chaîne

L'exemple de code suivant non async crée un message de file d'attente dans la file d'attente nommée " outputqueue " avec le même contenu que le message de file d'attente reçu dans la file d'attente nommée " inputqueue ". (Pour les fonctions asynchrones, utilisez  `IAsyncCollector<T>` comme indiqué plus loin dans cette section.)


		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}
  
### Messages de file d'attente OCT [(POCO - Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Pour créer un message de file d'attente qui contient un objet POCO plutôt qu'une chaîne, passez le type POCO comme paramètre de sortie au constructeur d'attribut  `Queue`.
 
		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

Le Kit SDK sérialise automatiquement l'objet au format JSON. Un message de file d'attente est toujours créé, même si l'objet est null.

### Création de plusieurs messages ou dans des fonctions asynchrones

Pour créer plusieurs messages, affectez  `ICollector<T>` ou  `IAsyncCollector<T>` comme type de paramètre pour la file d'attente de sortie, comme indiqué dans l'exemple suivant.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Chaque message de file d'attente est créé immédiatement quand la méthode  `Add` est appelée.

### Types avec lesquels l'attribut Queue fonctionne

Vous pouvez utiliser l'attribut  `Queue` sur les types de paramètre suivants :

* `out string` (crée un message de file d'attente si la valeur du paramètre n'est pas null lorsque la fonction se termine)
* `out byte[]` (fonctionne comme `string`) 
* `out CloudQueueMessage` (fonctionne comme `string`) 
* `out POCO` (un type sérialisable qui crée un message avec un objet null si le paramètre est null lorsque la fonction se termine)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (pour la création manuelle de messages en utilisant directement l'API de stockage Azure)

### <a id="ibinder"></a>Utilisation des attributs SDK WebJobs dans le corps d'une fonction

Si vous avez besoin d'effectuer un travail dans votre fonction avant d'utiliser un attribut WebJobs SDK comme `Queue`,  `Blob`, ou  `Table`, vous pouvez utiliser l'interface  `IBinder`.

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

L'interface  `IBinder` peut également être utilisée avec les attributs  `Table` et  `Blob`.

## <a id="blobs"></a>Comment lire et écrire des objets blob et des tables lors du traitement d'un message de file d'attente

Les attributs  `Blob` et  `Table` permettent de lire et d'écrire des objets blob et des tables. Les exemples de cette section s'appliquent aux objets blob. Pour plus d'exemples de code qui montrent comment déclencher des processus lorsque les objets blob sont créés ou mis à jour, consultez [Comment utiliser le stockage d'objets blob Azure avec SDK WebJobs](../websites-dotnet-webjobs-sdk-storage-blobs-how-to/), et pour des exemples de code qui lisent et écrivent des tables, consultez [Comment utiliser le stockage de table Windows Azure avec SDK WebJobs](../websites-dotnet-webjobs-sdk-storage-tables-how-to/).

### Messages de file d'attente de chaîne qui déclenchent des opérations d'objet blob

Pour un message de file d'attente qui contient une chaîne,  `queueTrigger` est un espace réservé que vous pouvez utiliser dans le paramètre  `Blob` de l'attribut  `blobPath` qui contient le contenu du message. 

L'exemple suivant utilise des objets  `Stream` pour lire et écrire des objets blob. Le message de file d'attente est le nom d'un objet blob situé dans le conteneur textblobs. Une copie de l'objet blob avec " -new " ajouté au nom est créée dans le même conteneur. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Le constructeur d'attribut  `Blob` prend un paramètre  `blobPath` qui spécifie le conteneur et le de l'objet blob. Pour plus d'informations sur cet espace réservé, consultez [Comment utiliser le stockage d'objets blob Azure avec SDK WebJobs](../websites-dotnet-webjobs-sdk-storage-blobs-how-to/), 

Lorsque l'attribut décore un objet  `Stream`, un autre paramètre de constructeur spécifie l' `FileAccess` en mode lecture, écriture ou lecture/écriture. 

L'exemple suivant utilise un objet  `CloudBlockBlob` pour supprimer un objet blob. Le message de la file d'attente est le nom de l'objet blob.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### <a id="pocoblobs"></a>Messages de file d'attente OCT [(POCO - Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Pour un objet POCO stocké au format JSON dans le message de file d'attente, vous pouvez utiliser des espaces réservés qui nomment des propriétés de l'objet dans le paramètre  `Queue` de l'attribut  `blobPath`. Vous pouvez également utiliser des [noms de propriétés de métadonnées de file d'attente](#queuemetadata) comme espaces réservés. 

L'exemple suivant copie un objet blob vers un nouvel objet blob avec une autre extension. Le message de la file d'attente est un objet  `BlobInformation` qui inclut les propriétés  `BlobName` et  `BlobNameWithoutExtension`. Les noms de propriété sont utilisés comme espaces réservés dans le chemin d'accès de l'objet blob pour les attributs  `Blob`. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Le Kit SDK utilise le [package NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) pour sérialiser et désérialiser les messages. Si vous créez des messages de la file d'attente dans un programme qui n'utilise pas SDK WebJobs, vous pouvez écrire le code comme dans l'exemple suivant pour créer un message de file d'attente OCT que le Kit de développement peut analyser.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

Si vous devez effectuer un travail dans votre fonction avant de lier un objet blob à un objet, vous pouvez utiliser l'attribut dans le corps de la fonction [comme indiqué plus haut pour l'attribut Queue](#ibinder).

### <a id="blobattributetypes"></a>Types avec lesquels vous pouvez utiliser l'attribut blob
 
L'attribut  `Blob` peut être utilisé avec les types suivants :

* `Stream` (lecture ou écriture, spécifié par le paramètre de constructeur FileAccess)
* `TextReader`
* `TextWriter`
* `string` (lecture)
* `out string` (écriture, crée un objet blob uniquement si le paramètre de chaîne n'est pas null lorsque la fonction revient)
* OCT (lecture)
* sortie OCT (écriture, crée toujours un objet blob, le crée en tant qu'objet null si le paramètre OCT est null lorsque la fonction revient)
* `CloudBlobStream` (écriture)
* `ICloudBlob` (lecture ou écriture)
* `CloudBlockBlob` (lecture ou écriture) 
* `CloudPageBlob` (lecture ou écriture) 

## <a id="poison"></a>Comment gérer les messages incohérents

Les messages dont le contenu provoque l'échec d'une fonction sont appelés  *poison messages*. Quand la fonction échoue, le message de file d'attente n'est pas supprimé et il est finalement récupéré, provoquant la répétition du cycle. Le Kit SDK peut interrompre automatiquement le cycle après un nombre limité d'itérations, ou vous pouvez le faire manuellement.

### Gestion automatique des messages incohérents

Le Kit SDK appelle une fonction jusqu'à cinq fois pour traiter un message de file d'attente. Si la cinquième tentative échoue, le message est déplacé vers une file d'attente de messages incohérents. [Vous pouvez configurer le nombre maximal de tentatives](#config). 

La file d'attente de messages incohérents se nomme *{originalqueuename}*. Vous pouvez écrire une fonction pour traiter les messages de la file d'attente de messages incohérents en les consignant dans un journal ou en envoyant une notification signalant qu'une attention manuelle est nécessaire. 

Dans l'exemple suivant, la fonction  `CopyBlob` échoue quand un message de file d'attente contient le nom d'un objet blob qui n'existe pas. Quand cela se produit, le message est déplacé de la file d'attente copyblobqueue vers la file d'attente copyblobqueue-poison.  `ProcessPoisonMessage` consigne ensuite le message incohérent dans le journal.

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

Vous pouvez obtenir le nombre de fois qu'un message a été récupéré pour le traitement en ajoutant un paramètre  `int` nommé  `dequeueCount` à votre fonction. Vous pouvez ensuite vérifier le nombre d'enlèvements de la file d'attente dans le code de fonction et effectuer votre propre gestion des messages incohérents quand le nombre dépasse un seuil, comme illustré dans l'exemple suivant.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 3)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

## <a id="config"></a>Comment définir les options de configuration

Vous pouvez utiliser le type  `JobHostConfiguration` pour définir les options de configuration suivantes :

* Définition des chaînes de connexion du Kit de développement logiciel dans le code.
* Configuration des paramètres  `QueueTrigger` tels que le nombre maximal d'enlèvements de la file d'attente.
* Obtention des noms de files d'attente à partir de la configuration.

### <a id="setconnstr"></a>Définition des chaînes de connexion du Kit de développement logiciel (SDK) dans le code

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

### <a id="configqueue"></a>Configuration des paramètres QueueTrigger

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
		    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="setnamesincode"></a>Définition des valeurs pour les paramètres du constructeur SDK WebJobs dans le code

Parfois vous souhaitez spécifier un nom de file d'attente, un nom d'objet blob ou un conteneur, ou un nom de table dans le code au lieu de le coder en dur. Par exemple, vous pouvez souhaiter spécifier le nom de file d'attente de  `QueueTrigger` dans un fichier de configuration ou une variable d'environnement. 

Vous pouvez le faire en passant un objet  `NameResolver` dans le type  `JobHostConfiguration`. Vous incluez des espaces réservés spéciaux délimités par des signes de pourcentage (%) dans les paramètres de constructeur d'attribut SDK WebJobs et votre code  `NameResolver` spécifie les valeurs réelles à la place de ces espaces réservés.

Par exemple, supposons que vous souhaitez utiliser une file d'attente nommée logqueuetest dans l'environnement de test et une autre nommée logqueueprod en production. Au lieu d'un nom de file d'attente codé en dur, vous souhaitez spécifier le nom d'une entrée dans la collection  `appSettings` ayant le nom de la file d'attente réelle. Si la clé  `appSettings` est logqueue, votre fonction pourrait ressembler à l'exemple suivant.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

Votre classe  `NameResolver` a pu obtenir ensuite le nom de la file d'attente à partir de  `appSettings` comme indiqué dans l'exemple suivant :

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Vous passez la classe  `NameResolver` dans l'objet  `JobHost` comme indiqué dans l'exemple suivant.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 
**Remarque :** les noms de file d'attente, de table et d'objets blob sont résolus chaque fois une fonction est appelée, mais les noms de conteneur d'objets blob sont résolus uniquement lorsque l'application démarre. Vous ne pouvez pas modifier le nom de conteneur d'objets blob pendant l'exécution du travail.

## <a id="manual"></a>Comment déclencher une fonction manuellement

Pour déclencher une fonction manuellement, utilisez la méthode  `Call` ou  `CallAsync` sur l'objet  `JobHost` et l'attribut  `NoAutomaticTrigger` sur la fonction, comme indiqué dans l'exemple suivant. 

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

Le tableau de bord affiche les journaux dans deux emplacements : la page de la tâche Web et la page pour une instance particulière de tâche Web. 

![Logs in WebJob page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

La sortie des méthodes de console que vous appelez dans une fonction ou dans la méthode  `Main()` s'affiche sur la page du tableau de bord de la tâche Web, et non sur la page pour un appel de méthode particulière. La sortie de l'objet TextWriter que vous obtenez à partir d'un paramètre dans votre signature de méthode apparaît sur la page tableau de bord pour un appel de méthode.

La sortie de console ne peut pas être liée à un appel de méthode particulier parce que la console est monothread, tandis que de nombreuses fonctions de travail peuvent s'exécuter en même temps. C'est pour cela que le Kit de développement logiciel (SDK) fournit à chaque appel de fonction son propre objet générateur de journal unique.

Pour écrire des [journaux de suivi d'application](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview), utilisez  `Console.Out` (crée des journaux marqués INFO) et  `Console.Error` (crée des journaux marqués ERROR). Une alternative consiste à utiliser [Trace ou TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), qui fournissent des commentaires, des avertissements, et des niveaux critique en plus des informations et des erreurs. Les journaux de suivi d'application apparaissent dans les fichiers journaux du site Web, les tables Azure ou les objets blob Azure, selon la configuration de votre site Web Azure. Comme cela s'avère vrai pour toutes les sorties de console, les 100 journaux d'application les plus récents apparaissent également sur la page tableau de bord pour la tâche Web, et non sur la page pour un appel de fonction. 

La sortie de console s'affiche dans le tableau de bord uniquement si le programme s'exécute dans WebJob Azure, et non si le programme est exécuté localement ou dans tout autre environnement.

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

Dans le tableau de bord du Kit SDK WebJobs, la sortie de l'objet  `TextWriter` apparaît quand vous accédez à la page pour l'appel d'une fonction particulière et que vous cliquez sur **Toggle Output** :

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Dans le tableau de bord de SDK WebJobs, les 100 lignes de sortie de console les plus récentes apparaissent quand vous accédez à la page de la tâche Web (et non à celle de l'appel de fonction) et que vous cliquez sur **Toggle Output**.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

Dans une tâche Web continue, les journaux des applications apparaissent dans /data/jobs/continuous/*{webjobname}*/job_log.txt dans le système de fichiers du site Web.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Dans un objet blob Azure, les journaux d'application ressemblent à ceci :
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Dans une table Azure, les journaux  `Console.Out` et  `Console.Error` ressemblent à ceci :

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a>Étapes suivantes

Cette guide fournit des exemples de code qui montrent comment gérer des scénarios courants d'utilisation des files d'attente Azure. Pour plus d'informations sur le Kit de développement logiciel (SDK) WebJobs Azure, consultez [Tâches Web Azure - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).


<!--HONumber=42-->
