<properties 
	pageTitle="Comment utiliser le stockage d'objets blob Azure avec SDK WebJobs" 
	description="Apprenez à utiliser le stockage d'objets blob Azure avec SDK WebJobs Déclenchement d'un processus lorsqu'un nouvel objet blob apparaît dans un conteneur et gestion des  'poison blobs'." 
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

# Comment utiliser le stockage d'objets blob Azure avec SDK WebJobs

Ce guide fournit des exemples de code C# qui montrent comment déclencher un processus, lorsqu'un objet blob Azure est créé ou mis à jour. Les exemples de code utilisent la version 1.x de [SDK WebJobs](websites-dotnet-webjobs-sdk.md).

Pour plus d'exemples de code qui montrent comment créer des objets blob, consultez [Utilisation du stockage de file d'attente Azure avec SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 
		
Le guide suppose que vous savez [créer un projet WebJob dans Visual Studio avec des chaînes de connexion qui pointent vers votre compte de stockage](websites-dotnet-webjobs-sdk-get-started.md).

## Sommaire

-   [Comment déclencher une fonction lors de la création ou la mise à jour d'un objet blob](#trigger)
	- Espace réservé unique pour le nom d'objet blob avec extension
	- Espaces réservés de nom et d'extension blob distincts
-   [Types avec lesquels BlobTrigger fonctionne](#types)
-   [Obtention de contenu texte blob par liaison de chaîne](#string)
-   [Obtention de contenu blob sérialisé en utilisant ICloudBlobStreamBinder](#icbsb)
-   [Comment gérer des objets blob incohérents](#poison)
-   [Algorithme d'interrogation d'objets blob](#polling)
-   [Reçus d'objets blob](#receipts)
-   [Rubriques couvertes par l'article de files d'attente](#queues)
-   [Étapes suivantes](#nextsteps)

## <a id="trigger"></a>Comment déclencher une fonction lors de la création ou la mise à jour d'un objet blob

Cette section explique comment utiliser l'attribut  `BlobTrigger`. 

### Espace réservé unique pour le nom d'objet blob avec extension  

L'exemple de code suivant copie les objets de texte blob qui apparaissent dans le conteneur  *input* vers le conteneur  *output* :

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Le constructeur d'attribut prend un paramètre de chaîne qui spécifie le nom du conteneur et un espace réservé pour le nom de l'objet blob. Dans cet exemple, si un objet blob nommé *Blob1.txt* est créé dans le conteneur  *input*, la fonction crée un objet blob nommé  *Blob1.txt* dans le conteneur  *output*. 

Vous pouvez spécifier un modèle de nom avec l'espace réservé de nom d'objet blob, comme illustré dans l'exemple de code suivant :

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Ce code copie uniquement les objets blob qui ont des noms commençant par " original- ". Par exemple, *original-Blob1.txt* dans le conteneur  *input* est copié vers  *copy-Blob1.txt* dans le conteneur  *output*.

Si vous devez spécifier un modèle de nom pour les noms d'objet blob qui ont des accolades dans le nom, doublez les accolades. Par exemple, si vous souhaitez rechercher des objets blob dans le conteneur  *images* qui ont des noms comme suit :

		{20140101}-soundfile.mp3

utilisez ceci pour votre modèle :

		images/{{20140101}}-{name}

Dans l'exemple, la valeur d'espace réservé  *name* serait  *soundfile.mp3*. 

### Espaces réservés de nom et d'extension blob distincts

L'exemple de code suivant modifie l'extension de fichier pendant la copie des objets blob qui s'affichent dans le conteneur  *input* vers le conteneur  *output*. Le code enregistre l'extension de l'objet blob  *input* et définit l'extension de l'objet blob  *output* vers *.txt*.

		public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
		    [Blob("output/{name}.txt")] out string output,
		    string name,
		    string ext,
		    TextWriter logger)
		{
		    logger.WriteLine("Blob name:" + name);
		    logger.WriteLine("Blob extension:" + ext);
		    output = input.ReadToEnd();
		}

## <a id="types"></a>Types que vous pouvez lier aux objets blob

Vous pouvez utiliser l'attribut  `BlobTrigger` sur les types suivants :

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* Autres types de désérialisation par [ICloudBlobStreamBinder](#icbsb) 

Si vous souhaitez travailler directement avec le compte de stockage Azure, vous pouvez également ajouter un paramètre  `CloudStorageAccount` à la signature de méthode.

## <a id="string"></a>Obtention de contenu texte blob par liaison de chaîne

Si vous prévoyez des objets de texte blob, `BlobTrigger` peut être appliqué à un paramètre  `string`. L'exemple de code suivant lie un objet de texte blob à un paramètre  `string` nommé  `logMessage`. La fonction utilise ce paramètre pour écrire le contenu de l'objet blob dans le tableau de bord SDK WebJobs. 
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a>Obtention de contenu blob sérialisé en utilisant ICloudBlobStreamBinder

L'exemple de code suivant utilise une classe qui implémente `ICloudBlobStreamBinder` pour activer l'attribut  `BlobTrigger` afin de lier un objet blob au type  `WebImage`.

		public static void WaterMark(
		    [BlobTrigger("images3/{name}")] WebImage input,
		    [Blob("images3-watermarked/{name}")] out WebImage output)
		{
		    output = input.AddTextWatermark("WebJobs SDK", 
		        horizontalAlign: "Center", verticalAlign: "Middle",
		        fontSize: 48, opacity: 50);
		}
		public static void Resize(
		    [BlobTrigger("images3-watermarked/{name}")] WebImage input,
		    [Blob("images3-resized/{name}")] out WebImage output)
		{
		    var width = 180;
		    var height = Convert.ToInt32(input.Height * 180 / input.Width);
		    output = input.Resize(width, height);
		}

Le code de liaison  `WebImage` est fourni dans une classe  `WebImageBinder` qui dérive de  `ICloudBlobStreamBinder`.

		public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
		{
		    public Task<WebImage> ReadFromStreamAsync(Stream input, 
		        System.Threading.CancellationToken cancellationToken)
		    {
		        return Task.FromResult<WebImage>(new WebImage(input));
		    }
		    public Task WriteToStreamAsync(WebImage value, Stream output,
		        System.Threading.CancellationToken cancellationToken)
		    {
		        var bytes = value.GetBytes();
		        return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
		    }
		}

## <a id="poison"></a>Comment gérer des objets blob incohérents

Lorsqu'une fonction  `BlobTrigger` échoue, le Kit de développement l'appelle encore une fois, au cas où l'échec a été provoqué par une erreur temporaire. Si le problème est causé par le contenu de l'objet blob, la fonction échoue à chaque fois qu'il essaie de traiter l'objet blob. Par défaut, le Kit de développement appelle une fonction jusqu'à 5 fois pour un objet blob donné. Si le cinquième essai échoue, le Kit de développement logiciel ajoute un message à une file d'attente nommée  *webjobs-blobtrigger-poison*.

Vous pouvez configurer un nombre maximal de tentatives. Le même paramètre [MaxDequeueCount](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#configqueue) est utilisé pour la gestion des objets blob incohérents et la gestion des messages de file d'attente de messages incohérents. 

Le message de file d'attente pour les objets blob incohérent est un objet JSON qui contient les propriétés suivantes :

* FunctionId (au format *{WebJob name}*.Functions.*{Function name}*, par exemple : WebJob1.Functions.CopyBlob)
* BlobType (" BlockBlob " ou " PageBlob ")
* ContainerName
* BlobName
* ETag (identificateur de version de blob, par exemple : " 0x8D1DC6E70A277EF ")

Dans l'exemple de code suivant, la fonction  `CopyBlob` comporte du code qui provoque son échec chaque fois qu'elle est appelée. Une fois le Kit de développement l'a appelée autant de fois que le nombre maximal de tentatives, un message est créé dans la file d'attente de messages incohérents blob, et ce message est traité par la fonction  `LogPoisonBlob`. 

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("textblobs/output-{name}")] out string output)
		{
		    throw new Exception("Exception for testing poison blob handling");
		    output = input.ReadToEnd();
		}
		
		public static void LogPoisonBlob(
		[QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
		    TextWriter logger)
		{
		    logger.WriteLine("FunctionId: {0}", message.FunctionId);
		    logger.WriteLine("BlobType: {0}", message.BlobType);
		    logger.WriteLine("ContainerName: {0}", message.ContainerName);
		    logger.WriteLine("BlobName: {0}", message.BlobName);
		    logger.WriteLine("ETag: {0}", message.ETag);
		}

Le Kit de développement logiciel désérialise automatiquement le message JSON. Voici la classe  `PoisonBlobMessage` : 

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a>Algorithme d'interrogation d'objets blob

SDK WebJobs analyse tous les conteneurs spécifiés par les attributs  `BlobTrigger` au démarrage de l'application. Dans un compte de stockage volumineux, cette analyse peut prendre du temps. Il peut se passer un certain temps avant que les nouveaux objets blob soient trouvés et que les fonctions  `BlobTrigger` soient exécutées.

Pour détecter des objets blob nouveaux ou modifiés après le démarrage de l'application, le Kit de développement lit régulièrement les journaux de stockage d'objets blob. Les journaux des objets blob sont mis en mémoire tampon et sont écrits uniquement physiquement toutes les 10 minutes environ. Il peut donc y avoir retard important après la création ou la mise à jour d'un objet blob avant l'exécution de la fonction  `BlobTrigger` correspondante. 

Il existe une exception pour les objets blob que vous créez à l'aide de l'attribut  `Blob`. Lorsque SDK WebJobs crée un nouvel objet blob, il transmet le nouvel objet blob immédiatement à toutes les fonctions  `BlobTrigger` correspondantes. Par conséquent, si vous avez une chaîne d'entrées et de sorties d'objets blob, le Kit de développement peut les traiter efficacement. Mais si vous souhaitez qu'une exécution de faible latence des fonctions de traitement d'objets blob pour les objets blob qui sont créés ou mis à jour par d'autres moyens, nous vous recommandons d'utiliser  `QueueTrigger` plutôt que  `BlobTrigger`.

### <a id="receipts"></a>Reçus d'objets blob

SDK WebJobs s'assure qu'aucune fonction  `BlobTrigger` n'est appelée plus d'une fois pour le même objet blob nouveau ou mis à jour. Il procède en conservant les  *blob receipts* afin de déterminer si une version de l'objet blob donné a été traitée.

Les reçus blob sont stockés dans un conteneur nommé  *azure-webjobs-hosts* dans le compte de stockage Azure spécifié par la chaîne de connexion AzureWebJobsStorage. Un reçu de blob contient les informations suivantes :

* La fonction a été appelée pour l'objet blob (" *{WebJob name}*.Functions.*{Function name}* ", par exemple : "WebJob1.Functions.CopyBlob")
* le nom du conteneur
* le type d'objet blob (" BlockBlob " ou " PageBlob ")
* le nom de l'objet blob
* l'ETag (identificateur de version de blob, par exemple : " 0x8D1DC6E70A277EF ")

Si vous souhaitez forcer le retraitement d'un objet blob, vous pouvez supprimer manuellement le reçu de l'objet blob pour cet objet blob à partir du conteneur  *azure-webjobs-hosts*.

## <a id="queues"></a>Rubriques couvertes par l'article de files d'attente

Pour plus d'informations sur la façon de gérer le traitement des objets blob déclenchés par un message de file d'attente ou pour les scénarios SDK WebJobs non spécifiques au traitement des objets blob, consultez [Utilisation du stockage de file d'attente Azure avec SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Les rubriques abordées dans cet article sont les suivantes :

* Fonctions asynchrones
* Instances multiples
* Arrêt progressif
* Utilisation des attributs SDK WebJobs dans le corps d'une fonction
* Définition des chaînes de connexion du Kit de développement logiciel dans le code.
* Définition des valeurs pour les paramètres du constructeur SDK WebJobs dans le code
* Configuration de  `MaxDequeueCount` pour la gestion des objets blob incohérents.
* Déclenchement manuel d'une fonction
* Écriture de journaux

## <a id="nextsteps"></a>Étapes suivantes

Cette guide fournit des exemples de code qui montrent comment gérer des scénarios courants d'utilisation des objets blob Azure. Pour plus d'informations sur le Kit de développement logiciel (SDK) WebJobs Azure, consultez [Tâches web Azure - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).



<!--HONumber=42-->
