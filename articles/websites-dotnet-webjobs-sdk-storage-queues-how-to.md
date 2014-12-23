<properties linkid="" pageTitle="Utilisation du stockage de files d'attente Azure à l'aide du Kit de développement logiciel (SDK) WebJobs" metaKeywords="WebJobs SDK Azure queue storage .NET C#" description="Learn how to work with Azure queue storage in the WebJobs SDK. Create and delete queues; insert, peek, get, and delete queue messages, and more." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Utilisation du stockage de files d'attente Azure à l'aide du Kit de développement logiciel (SDK) WebJobs

Ce guide montre comment écrire du code C# pour les scénarios courants à l'aide du
service de stockage de files d'attente Azure et du Kit de développement logiciel (SDK) WebJobs, version 1.0.0.

Le guide suppose que vous savez en quoi consiste le [Kit de développement logiciel (SDK) WebJobs](../websites-webjobs-sdk-storage-queues-how-to) et l'[Exécution de tâches de base](../websites-dotnet-webjobs-sdk-get-started/), telles que l'installation du package NuGet du Kit de développement logiciel (SDK) WebJobs, la création d'un compte de stockage Azure et la création de chaînes de connexion pour le Kit de développement logiciel (SDK) WebJobs qui pointent vers votre compte de stockage.

La plupart des extraits de code affichent uniquement les fonctions, pas le code qui crée l'objet " JobHost " comme dans cet exemple :

static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Sommaire

-   [Déclenchement d'une fonction à la réception d'un message de file d'attente](#trigger)
	- Messages de file d'attente de chaîne
	- Messages de file d'attente d'objet POCO
	- Fonctions asynchrones
	- Algorithme d'interrogation
	- Exécution en parallèle
	- Obtention des métadonnées de file d'attente ou de message de file d'attente
	- Arrêt progressif
-   [Création d'un message de file d'attente pendant le traitement d'un message de file d'attente](#createqueue)
	- Messages de file d'attente de chaîne
	- Messages de file d'attente d'objet POCO
	- Création de plusieurs messages
	- Utilisation d'attributs de file d'attente dans le corps d'une fonction
-   [Lecture et écriture d'objets blob pendant le traitement d'un message de file d'attente](#blobs)
	- Messages de file d'attente de chaîne
	- Messages de file d'attente d'objet POCO
	- Utilisation d'attributs d'objet blob dans le corps d'une fonction
-   [Gestion de messages incohérents](#poison)
	- Gestion automatique des messages incohérents
	- Gestion manuelle des messages incohérents
-   [Définition des options de configuration](#config)
	- Définition des chaînes de connexion du Kit de développement logiciel (SDK) dans le code
	- Configuration des paramètres QueueTrigger
	- Obtention des noms de file d'attente de la configuration
-   [Déclenchement manuel d'une fonction](#manual)
-   [Écriture de journaux](#logs)
-   [Étapes suivantes](#nextsteps)

## <a id="trigger"></a> Déclenchement d'une fonction à la réception d'un message de file d'attente

Pour écrire une fonction que le Kit de développement logiciel (SDK) appelle à la réception d'un message de file d'attente, utilisez l'attribut " QueueTrigger " avec une chaîne ou un paramètre POCO selon ce que vous voulez obtenir dans le message. Le constructeur d'attribut prend un paramètre de chaîne qui spécifie le nom de la file d'attente à interroger. Vous pouvez également [définir le nom de la file d'attente de manière dynamique](#config).

Si votre site web s'exécute sur plusieurs machines virtuelles, la tâche web WebJob s'exécute sur chaque machine, qui attend des déclencheurs et essaie d'exécuter les fonctions. Dans certains scénarios, cela peut entraîner que certaines fonctions traitent deux fois les mêmes données ; ces fonctions doivent donc être idempotentes (écrites de façon que, si elles sont appelées de manière répétitive avec les mêmes données d'entrée, elles ne produisent pas des résultats en double).  

### Messages de file d'attente de chaîne

Dans l'exemple suivant, la file d'attente contient un message de chaîne, ainsi " QueueTrigger " est appliqué à un paramètre de chaîne nommé " logMessage " qui renferme le contenu du message de file d'attente. La fonction [écrit un message de journal dans le tableau de bord](#logs)..
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Outre " string ", le paramètre peut être un tableau d'octets, un objet " CloudQueueMessage " ou un objet POCO que vous définissez.

### Messages de file d'attente d'objet POCO

Dans l'exemple suivant, le message de file d'attente contient JSON pour un objet " BlobInformation " qui inclut une propriété " BlobName ". Le Kit de développement logiciel (SDK) désérialise automatiquement l'objet.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

L'exemple suivant montre comment créer un message de file d'attente POCO sans recourir au Kit de développement logiciel (SDK) WebJobs, que le Kit de développement logiciel (SDK) peut analyser. Le Kit de développement logiciel (SDK) utilise le [package Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) pour sérialiser et désérialiser les messages.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Fonctions asynchrones

La fonction async suivante [écrit un journal dans le tableau de bord](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Les fonctions Async peuvent prendre un jeton d'annulation, comme illustré dans l'exemple suivant, qui copie un objet blob. (Pour obtenir une explication de l'espace réservé " queueTrigger ", consultez la section [Objets blob](#blobs)).

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### Algorithme d'interrogation

Le Kit de développement logiciel (SDK) implémente un algorithme d'interruption exponentielle aléatoire pour réduire l'effet d'interrogation de l'inactivité de file d'attente sur les coûts de transaction de stockage.  En présence de message, le Kit de développement logiciel (SDK) attend deux secondes, puis vérifie si un autre message existe. En l'absence de message, il attend environ quatre secondes avant de réessayer. Après d'autres tentatives pour obtenir un message de file d'attente, le temps d'attente continue d'augmenter jusqu'à ce qu'il atteigne le délai maximal, d'une minute par défaut. [Le délai d'attente maximal est configurable](#config).

### Exécution en parallèle

Si vous avez plusieurs fonctions à l'écoute sur différentes files d'attente, le Kit de développement logiciel (SDK) les appelle en parallèle à la réception simultanée des messages. 

Il en est de même quand plusieurs messages sont reçus pour une file d'attente unique. Par défaut, le Kit de développement logiciel obtient un lot de 16 messages de file d'attente à la fois et exécute la fonction qui les traite en parallèle. [La taille du lot est configurable](#config). Quand le nombre en cours de traitement diminue jusqu'à constituer la moitié de la taille du lot, le Kit de développement logiciel (SDK) obtient un autre lot et commence à traiter ces messages. Par conséquent, le nombre maximal de messages simultanés en cours de traitement par fonction représente une fois et demie la taille du lot. Cette limite s'applique séparément à chaque fonction qui a un attribut " QueueTrigger ". Si vous ne voulez pas d'exécution parallèle pour les messages reçus sur une file d'attente, attribuez la valeur 1 à la taille du lot.

### <a id="queuemetadata"></a>Obtention des métadonnées de file d'attente ou de message de file d'attente

Vous pouvez obtenir les propriétés de message suivantes en ajoutant des paramètres à la signature de méthode :

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contains message text)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Si vous voulez utiliser directement l'API de stockage Azure, vous pouvez également ajouter le paramètre " CloudStorageAccount ".

L'exemple suivant écrit toutes ces métadonnées dans le journal des applications INFO. Dans l'exemple, logMessage et queueTrigger renferment le contenu du message de file d'attente.

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
		        "logMessage={0}\n"
				nexpirationTime={1}\ninsertionTime={2}\n" +
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

Une fonction qui s'exécute dans une tâche web WebJob continue peut accepter un paramètre " CancellationToken " qui permet au système d'exploitation de notifier la fonction quand la tâche web est sur le point de se terminer. Vous pouvez utiliser cette notification pour vous assurer que la fonction ne s'arrête pas de manière inopinée en laissant les données dans un état incohérent.

L'exemple suivant montre comment vérifier l'arrêt imminent d'une tâche web WebJob dans une fonction.

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

**Remarque :** le tableau de bord ne peut pas afficher correctement l'état ni la sortie des fonctions qui ont été arrêtées.
 
Pour plus d'informations, consultez la rubrique [Arrêt progressif de WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a> Création d'un message de file d'attente pendant le traitement d'un message de file d'attente

Pour écrire une fonction qui crée un message de file d'attente, utilisez l'attribut " Queue " sur une chaîne de sortie ou le paramètre POCO. Comme " QueueTrigger ", vous transmettez le nom de la file d'attente sous forme de chaîne, ou vous pouvez [définir le nom de la file d'attente de manière dynamique](#config).

### Messages de file d'attente de chaîne

L'exemple suivant crée un message de file d'attente dans la file nommée " outputqueue " avec le même contenu que celui du message de file d'attente reçu dans la file nommée " inputqueue ".

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

Si le type de paramètre de sortie est l'un des types suivants et que l'objet n'est pas null quand la fonction se termine, le Kit de développement logiciel (SDK) crée un message de file d'attente :

* `string` 
* `byte[]`
* un type POCO sérialisable que vous définissez
* `CloudQueueMessage`

Pour créer plusieurs messages, consultez **Création de plusieurs messages** plus bas dans cette section.

Vous pouvez également utiliser " CloudQueue " comme type de paramètre de sortie si vous voulez envoyer des messages manuellement.

### Messages de file d'attente d'objet POCO

Pour créer un message de file d'attente qui contient un objet POCO plutôt qu'une chaîne, transmettez le type POCO comme paramètre de sortie au constructeur d'attribut " Queue ". 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

Le Kit de développement logiciel (SDK) sérialise automatiquement l'objet JSON. Un message de file d'attente est toujours créé, même si l'objet est null.

### Création de plusieurs messages

Pour créer plusieurs messages, le type de paramètre pour la file d'attente de sortie doit être " ICollector<T> " ou " IAsyncCollector ", comme indiqué dans l'exemple suivant.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Chaque message de file d'attente est créé immédiatement quand la méthode " Add " est appelée.

### <a id="queuebody"></a>Utilisation d'attributs de file d'attente dans le corps d'une fonction

Si vous avez besoin de modifier votre fonction avant d'utiliser l'attribut " Queue " pour créer un message de file d'attente, vous pouvez utiliser l'interface " IBinder " pour obtenir un objet " CloudQueue " vous permettant d'utiliser directement une file d'attente. 

L'exemple suivant prend un message de la file d'attente d'entrée et crée un message avec le même contenu dans une file d'attente de sortie. Le nom de la file d'attente de sortie est défini par le code du corps de la fonction.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> Lecture et écriture d'objets blob et de tables pendant le traitement d'un message de file d'attente

Les attributs " Blob " et " Table " vous permettent de lire et d'écrire des objets blob et des tables. Les exemples de cette section s'appliquent aux objets blob.

Certains des types avec lesquels l'attribut " Blob " peut être utilisé incluent " Stream ", " TextReader ", " TextWriter " et " CloudBlockBlob ". Le constructeur d'attribut prend un paramètre " blobPath " qui spécifie le nom du conteneur et de l'objet blob. Quand l'attribut décore un objet " Stream ", un autre paramètre de constructeur définit le mode " FileAccess " sur Lecture, Écriture ou Lecture/écriture. Si vous avez besoin de modifier votre fonction avant la liaison d'un objet blob à un objet, vous pouvez utiliser l'attribut dans le corps de la fonction, [comme indiqué plus haut pour l'attribut de file d'attente.](#queuebody).

### Messages de file d'attente de chaîne

Pour un message de file d'attente qui contient une chaîne, " queueTrigger " est un espace réservé que vous pouvez utiliser dans le paramètre " blobPath " de l'attribut " Blob " qui contient le contenu du message. 

L'exemple suivant utilise des objets " Stream " pour lire et écrire des objets blob. Le message de file d'attente fournit le nom d'un objet blob situé dans le conteneur textblobs. Une copie de l'objet blob avec " -new " ajouté au nom est créé dans le même conteneur. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

L'exemple suivant utilise un objet " CloudBlockBlob " pour supprimer un objet blob.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### Messages de file d'attente d'objet POCO

Pour les objets POCO stockés au format JSON dans le message de file d'attente, vous pouvez utiliser des espaces réservés qui nomment les propriétés de nom de l'objet dans le paramètre " blobPath " de l'attribut " Queue ". Vous pouvez également utiliser des [noms de propriété de métadonnées de file d'attente](#queuemetadata) comme espaces réservés. 

L'exemple suivant copie un objet blob vers un nouvel objet blob avec une autre extension, à l'aide des propriétés de l'objet " BlobInformation " pour spécifier les noms des objets blob d'entrée et de sortie. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

L'exemple suivant montre comment créer un message de file d'attente POCO sans recourir au Kit de développement logiciel (SDK) WebJobs, que le Kit de développement logiciel (SDK) peut analyser. Le Kit de développement logiciel (SDK) utilise le [package Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) pour sérialiser et désérialiser les messages.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> Gestion de messages incohérents

Les messages dont le contenu entraîne l'échec d'une fonction sont appelés *messages incohérents*. Quand la fonction échoue, le message de file d'attente n'est pas supprimé et est finalement récupéré, entraînant la répétition du cycle. Le Kit de développement logiciel (SDK) peut interrompre automatiquement le cycle après un nombre limité d'itérations, ou vous pouvez le faire manuellement.

### Gestion automatique des messages incohérents

Le Kit de développement logiciel appelle une fonction jusqu'à 5 fois pour traiter un message de file d'attente. Si la cinquième tentative échoue, le message est déplacé vers une file d'attente de messages incohérents. [Vous pouvez configurer un nombre maximal de tentatives](#config). 

La file d'attente de messages incohérents est nommée *{originalqueuename}*-poison. Vous pouvez écrire une fonction pour traiter les messages de file d'attente de messages incohérents en les enregistrant ou en envoyant une notification qu'une attention manuelle est nécessaire. 

Dans l'exemple suivant, la fonction " CopyBlob " ne fonctionnera pas si un message de file d'attente contient le nom d'un objet blob qui n'existe pas. Le cas échéant, le message est déplacé de la file d'attente copyblobqueue à la file d'attente copyblobqueue-poison. Le " ProcessPoisonMessage " enregistre ensuite le message incohérent.

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

L'illustration suivante montre la sortie de console de ces fonctions pendant le traitement d'un message incohérent.

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Gestion manuelle des messages incohérents

Vous pouvez obtenir le nombre de fois qu'un message a été récupéré en vue de son traitement en ajoutant un paramètre " int " nommé " dequeueCount " à votre fonction. Vous pouvez ensuite vérifier le nombre de retraits dans le code de fonction et effectuer votre propre gestion de messages incohérents quand le nombre dépasse un seuil, comme illustré dans l'exemple suivant.

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

Pour que ce code fonctionne comme prévu, vous devez augmenter le nombre maximal de tentatives de gestion automatique des messages incohérents ou le nombre de retraits dans cet exemple ne devrait jamais dépasser 5.

## <a id="config"></a> Définition des options de configuration

Vous pouvez utiliser le type " JobHostConfiguration " pour définir les options de configuration suivantes :

* Définition des chaînes de connexion du Kit de développement logiciel (SDK) dans le code
* Configuration des paramètres " QueueTrigger " tels que le nombre de retraits maximal
* Obtention des noms de file d'attente de la configuration

### <a id="setconnstr"></a>Définition des chaînes de connexion du Kit de développement logiciel (SDK) dans le code

La définition des chaînes de connexion du Kit de développement logiciel (SDK) dans le code vous permet d'utiliser vos propres noms de chaîne de connexion dans les fichiers de configuration ou des variables d'environnement, comme illustré dans l'exemple suivant.

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

### <a id="configqueue"></a>Configuration des paramètres de file d'attente

Vous pouvez configurer les paramètres suivants qui s'appliquent au traitement du message de file d'attente :

- nombre maximal de messages de file d'attente prélevés simultanément pour être exécutés en parallèle (la valeur par défaut est 16) ;
- nombre maximal de tentatives avant l'envoi d'un message de file d'attente à une file d'attente de messages incohérents (la valeur par défaut est 5) ;
- durée d'attente maximale avant la nouvelle interrogation quand une file d'attente est vide (la valeur par défaut est 1 minute).

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

### <a id="configqueuenames"></a>Obtention des noms de file d'attente de la configuration

Le type " JobHostConfiguration " vous permet de transmettre un objet " NameResolver " qui fournit le nom de file d'attente au Kit de développement logiciel pour les attributs " QueueTrigger " et " Queue ".

Par exemple, supposons que vous souhaitez utiliser une file d'attente nommée logqueuetest dans l'environnement de test et une nommée logqueueprod en production. Au lieu d'un nom de file d'attente codé en dur, vous voulez spécifier le nom d'une entrée dans la collection " appSettings " ayant le nom de la file d'attente réelle. Si la clé " appSettings " est logqueue, votre fonction peut ressembler à l'exemple suivant.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

Votre classe " NameResolver " peut alors obtenir le nom de la file d'attente à partir d'" appSettings " comme indiqué dans l'exemple suivant :

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Vous transmettez la classe " NameResolver " à l'objet " JobHost " comme indiqué dans l'exemple suivant.

static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>Déclenchement manuel d'une fonction

Pour déclencher manuellement une fonction, utilisez la méthode " Call " ou " CallAsync " sur l'objet " JobHost " et l'attribut " NoAutomaticTrigger " sur la fonction, comme indiqué dans l'exemple suivant. 

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

## <a id="logs"></a>Écriture de journaux

Pour écrire des journaux qui apparaissent dans la page du tableau de bord WebJobs liée à un appel de fonction particulier, utilisez un objet " TextWriter " que vous obtenez à partir d'un paramètre dans votre signature de méthode.

Pour écrire des [journaux de suivi d'application](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview), utilisez " Console.Out " (crée des journaux marqués INFO) et " Console.Error " (crée des journaux marqués ERROR). Une autre solution consiste à utiliser [Trace ou TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx).

Les journaux des applications s'affichent dans les fichiers journaux du site web, les tables Azure ou les objets blob Azure selon la configuration de votre site web Azure. Les 100 journaux des applications les plus récents apparaissent également dans le tableau de bord si le programme s'exécute dans Azure WebJobs. Aucun journal des applications ne s'affiche dans le tableau de bord à partir d'un programme qui s'exécute localement ou dans tout autre environnement.   

Vous pouvez désactiver la journalisation en [définissant la chaîne de connexion du tableau de bord sur null](#config).

L'exemple suivant affiche plusieurs méthodes pour écrire les journaux :

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

Dans le tableau de bord du Kit de développement logiciel (SDK) WebJobs, la sortie de l'objet " TextWriter " apparaît quand vous accédez à la page d'un appel de fonction spécifique et cliquez sur **Activer/désactiver la sortie** :

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Dans le tableau de bord du Kit de développement logiciel (SDK) WebJobs, les 100 lignes les plus récentes des journaux d'applications apparaissent quand vous accédez à la page de la tâche web WebJob (pas pour l'appel de la fonction) et cliquez sur **Activer/désactiver la sortie**.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

Dans une tâche web WebJob continue, les journaux des applications s'affichent dans /data/jobs/continuous/*{webjobname}*/job_log.txt dans le système de fichiers du site web.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Dans l'objet blob Azure, les journaux des applications ressemblent à ce qui suit :
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Dans une table Azure, les journaux " Console.Out " et " Console.Error " ressemblent à ceci :

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Étapes suivantes

Cette rubrique a fourni des exemples de code qui montrent comment gérer des scénarios courants pour l'utilisation des files d'attente Azure. Pour plus d'informations sur l'utilisation d'Azure WebJobs et du Kit de développement logiciel (SDK) associé, consultez la page [Azure WebJobs - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).
