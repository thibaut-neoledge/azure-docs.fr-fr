<properties urlDisplayName="Queue Service" pageTitle="Utilisation du service de File d'attente (Node.js) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Node.js" description="Découvrez comment utiliser le service de File d'attente Azure pour créer et supprimer des files d'attente, ainsi que pour insérer, récupérer et supprimer des messages. Les exemples sont écrits en Node.js." metaCanonical="" services="storage" documentationCenter="nodejs" title="How to Use the Queue Service from Node.js" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="mwasson" />





# Utilisation du service de file d'attente à partir de Node.js

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de file d'attente Azure. Les exemples sont écrits en utilisant l'API Node.js. Les scénarios traitent de l' **insertion**, de la **lecture furtive**, de l'**obtention** et de la **suppression** de messages de la file d'attente, et de la **création et suppression de files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes][].

## Sommaire

* [Présentation du service de file d'attente][]   
* [Concepts][]   
* [Création d'un compte Azure Storage][]  
* [Création d'une application Node.js][]   
* [Configuration de votre application pour accéder au stockage][]   
* [Configuration d'une chaîne de connexion Azure Storage][]   
* [Procédure : Création d'une file d'attente][]   
* [Procédure : Insertion d'un message dans une file d'attente][]   
* [Procédure : Lecture furtive du message suivant][]   
* [Procédure : Enlèvement du message suivant de la file d'attente][]   
* [Procédure : Modification du contenu d'un message en file d'attente][]   
* [Procédure : Options supplémentaires pour l'enlèvement des messages de la file d'attente][]   
* [Procédure : Obtention de la longueur de la file d'attente][]   
* [Procédure : Suppression d'une file d'attente][]   
* [Procédure : Utilisation des signatures d'accès partagé][]
* [Étapes suivantes][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a name="create-account"></a>Création d'un compte Azure Storage</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Création d'une application Node.js

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les page [Création et déploiement d'une application Node.js dans un site Web Azure], [Service cloud Node.js][Node.js Cloud Service] (avec Windows PowerShell) ou [Site Web avec WebMatrix].

## <a name="configure-access"> </a>Configuration de votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous avez besoin du Kit de développement logiciel (SDK) Azure Storage pour Node.js, qui inclut un ensemble de bibliothèques pratiques qui communiquent avec les services REST de stockage.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande telle que **PowerShell** (Windows,) **Terminal** (Mac) ou **Bash** (Unix) pour accéder au dossier dans lequel vous avez créé votre exemple d'application.

2.  Tapez **npm install azure-storage** dans la fenêtre de commande, ce qui doit aboutir à la sortie suivante :

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier     **node\_modules** a été créé. Dans ce dossier, recherchez le dossier **azure-storage**, qui contient les bibliothèques dont vous avez besoin pour accéder au stockage.

### Importation du package

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier **server.js** de l'application dans laquelle vous souhaitez utiliser le stockage :

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Configuration d'une connexion Azure Storage

Le module Azure lit les variables d'environnement AZURE\_STORAGE\_ACCOUNT and AZURE\_STORAGE\_ACCESS\_KEY ou AZURE\_STORAGE\_CONNECTION\_STRING pour obtenir les informations obligatoires pour se connecter à votre compte de stockage Azure. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l'appel de **createQueueService**.

Pour obtenir un exemple de configuration des variables d'environnement dans le portail de gestion pour un site web Azure, consultez la rubrique [Application web Node.js avec stockage]

## <a name="create-queue"> </a>Procédure : Création d'une file d'attente

Le code suivant crée un objet **QueueService**, ce qui vous permet d'utiliser les files d'attente.

    var queueSvc = azure.createQueueService();

Utilisez la méthode **createQueueIfNotExists**, qui renvoie la file d'attente spécifiée si elle existe déjà ou crée une file d'attente avec le nom spécifié si elle n'existe pas encore.

	queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
	  }
	});

Si la file d'attente est créée, `result` a la valeur true. Si la file d'attente existe, `result` a la valeur false.

###Filtres

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées via **QueueService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

		function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler " next " en passant un rappel avec la signature suivante :

		function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse de la requête au serveur), le rappel doit appeler la fonction next, si elle existe, pour continuer à traiter d'autres filtres ou simplement appeler finalCallback pour terminer l'utilisation du service.

Deux filtres qui implémentent la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js : **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **QueueService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="insert-message"> </a>Procédure : Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d'attente, utilisez la méthode **createMessage** qui crée un message et l'ajoute à la file d'attente.

	queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
	  if(!error){
	    // Message inserted
	  }
	});

## <a name="peek-message"> </a>Procédure : Lecture furtive du message suivant

Vous pouvez lire furtivement le message au début de la file d'attente sans l'enlever de la file d'attente en appelant la méthode **peekMessages**. Par défaut, **peekMessages** lit un seul message.

	queueSvc.peekMessages('myqueue', function(error, result, response){
	  if(!error){
		// Messages peeked
	  }
	});

`result` contient le message.

> [WACOM.NOTE] L'utilisation de  **peekMessages** alors qu'il n'y a pas de message dans la file d'attente ne renvoie pas d'erreur, mais ne renvoie pas non plus de message.

## <a name="get-message"> </a>Procédure : Enlèvement du message suivant de la file d'attente

Le traitement d'un message se fait en deux étapes :

1. Enlever le message de la file d'attente.

2. Supprimer le message.

Pour enlever un message de la file d'attente, utilisez **getMessage**. Cela rend le message invisible dans la file d'attente, et aucun autre client ne peut le traiter. Lorsque votre application a traité le message, appelez **deleteMessage** pour supprimer le message de la file d'attente. L'exemple suivant obtient un message, puis le supprime :

	queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
	    // message dequed
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageid, message.popreceipt, function(error, response){
	      if(!error){
		    //message deleted
		  }
		});
	  }
	});

> [WACOM.NOTE] Par défaut, un message est masqué uniquement pendant 30 secondes avant d'être de nouveau visible pour les autres clients. Vous pouvez indiquer une autre valeur en utilisant `options.visibilityTimeout` avec **getMessages**.

> [WACOM.NOTE]
> L'utilisation de <b>getMessages</b> alors qu'il n'y a pas de message dans la file d'attente ne renvoie pas d'erreur, mais ne renvoie pas non plus de message.

## <a name="change-contents"> </a>Procédure : Modification du contenu d'un message en file d'attente

Vous pouvez changer le contenu d'un message qui se trouve dans la file d'attente en utilisant **updateMessage**. L'exemple suivant met à jour le texte d'un message :

    queueSvc.getMessages('myqueue', function(error, result, response){
	  if(!error){
		// Got the message
		var message = result[0];
		queueSvc.updateMessage('myqueue', message.messageid, message.popreceipt, 10, {messageText: 'new text'}, function(error, result, response){
		  if(!error){
			// Message updated successfully
		  }
		});
	  }
	});

## <a name="advanced-get"> </a>Procédure : Options supplémentaires pour l'enlèvement des messages de la file d'attente

Il existe deux méthodes pour personnaliser l'extraction d'un message d'une file d'attente :

* `options.numOfMessages` - Extraire un lot de messages (jusqu'à 32).
* `options.visibilityTimeout` - Définir une durée d'invisibilité plus longue ou plus courte.

L'exemple suivant utilise la méthode **getMessages** pour obtenir 15 messages en un appel. Ensuite, il traite
chaque message à l'aide d'une boucle for. La durée d'invisibilité est passée à cinq minutes pour tous les messages renvoyés par cette méthode.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
	  if(!error){
		// Messages retreived
		for(var index in result){
		  // text is available in result[index].messageText
		  var message = result[index];
		  queueSvc.deleteMessage(queueName, message.messageid, message.popreceipt, function(error, response){
			if(!error){
			  // Message deleted
			}
		  });
		}
	  }
	});

## <a name="get-queue-length"> </a>Procédure : Obtention de la longueur de la file d'attente

**getQueueMetadata** retourne des métadonnées sur la file d'attente, y compris le nombre approximatif de messages en attente dans la file d'attente.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
	  if(!error){
		// Queue length is available in result.approximatemessagecount
	  }
	});

## <a name="list-queue"> </a>Procédure : Liste des files d'attente

Pour extraire une liste de files d'attente, utilisez **listQueuesSegmented**. Pour extraire une liste filtrée par un certain préfixe, utilisez **listQueuesSegmentedWithPrefix**.

	queueSvc.listQueuesSegmented(null, function(error, result, response){
	  if(!error){
	    // result.entries contains the list of queues
	  }
	});

Si toutes les files d'attente ne peuvent pas être retournées, `result.continuationToken` peut être utilisé comme premier paramètre de **listQueuesSegmented** ou comme second paramètre de **listQueuesSegmentedWithPrefix** pour extraire plus de résultats.

## <a name="delete-queue"> </a>Procédure : Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode
**deleteQueue** sur l'objet file d'attente.

    queueSvc.deleteQueue(queueName, function(error, response){
		if(!error){
			// Queue has been deleted
		}
	});

Pour effacer tous les messages d'une file d'attente sans supprimer cette dernière, utilisez **clearMessages**.

## <a name="sas"></a>Utilisation Utilisation des signatures d'accès partagé

Les signatures d'accès partagé sont un moyen sécurisé de fournir un accès précis aux files d'attente sans fournir le nom ni les clés de votre compte de stockage. Elles servent souvent à fournir un accès limité à vos files d'attente, par exemple pour autoriser une application mobile à soumettre des messages.

Une application approuvée, comme un service cloud, génère une signature d'accès partagé à l'aide de **generateSharedAccessSignature** de **QueueService**, et la fournit à une application non approuvée ou à moitié approuvée. Par exemple, une application mobile. La signature d'accès partagé est générée à l'aide d'une stratégie, qui décrit les dates de début et de fin de validité de la signature, et le niveau d'accès accordé au détenteur de la signature.

L'exemple suivant génère une nouvelle stratégie d'accès partagé qui autorise le détenteur de la signature d'accès partagé à ajouter des messages à la file d'attente et expire 100 minutes après son heure de création.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

	var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
	var host = queueSvc.host;

Notez que les informations sur l'hôte doivent également être fournies, car elles sont obligatoires lorsque le détenteur de la signature d'accès partagé tente d'accéder à la file d'attente.

L'application cliente utilise les signatures d'accès partagé avec **QueueServiceWithSAS** pour effectuer les opérations sur la table. L'exemple suivant se connecte à la file d'attente et crée un message.

	var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
	sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
	  if(!error){
	    //message added
	  }
	});

Comme la signature d'accès partagé a été générée avec un accès en ajout, une erreur sera renvoyée en cas de tentative de lecture, de mise à jour ou de suppression des messages.

###Listes de contrôle d'accès

Vous pouvez également utiliser une liste de contrôle d'accès (ACL) pour définir la stratégie d'accès pour une signature d'accès partagé. Cela est utile si vous voulez autoriser plusieurs clients à accéder à une file d'attente, mais fournir des stratégies d'accès différentes à chaque client.

Une liste de contrôle d'accès est implémentée à l'aide d'un tableau de stratégies d'accès, dans lequel un ID est associé à chaque stratégie. L'exemple suivant définit deux stratégies ; une pour " user1 " et une pour " user2 " :

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

L'exemple suivant obtient la liste de contrôle d'accès active pour **myqueue**, puis ajoute les nouvelles stratégies à l'aide de **setQueueAcl**. Cette approche permet :

	queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		queueSvc.setQueueAcl('myqueue', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Lorsque la liste de contrôle d'accès est définie, vous pouvez créer une signature d'accès partagé basée sur l'ID pour une stratégie. L'exemple suivant crée une signature d'accès partagé pour " user2 " :

	queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous connaissez les bases du stockage de files d'attente, consultez les liens suivants
pour apprendre à réaliser des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][].
-   Consultez le [blog de l'équipe Azure Storage][].
-   Consultez le référentiel [Kit de développement logiciel (SDK) Azure Storage pour Node][] sur GitHub.

  [Kit de développement logiciel (SDK) Azure Storage pour Node]: https://github.com/Azure/azure-storage-node
  [Étapes suivantes]: #next-steps
  [Présentation du service de file d'attente]: #what-is
  [Concepts]: #concepts
  [Création d'un compte Azure Storage]: #create-account
  [Création d'une application Node.js]: #create-app
  [Configuration de votre application pour accéder au stockage]: #configure-access
  [Configuration d'une chaîne de connexion Azure Storage]: #setup-connection-string
  [Procédure : Création d'une file d'attente]: #create-queue
  [Procédure : Insertion d'un message dans une file d'attente]: #insert-message
  [Procédure : Lecture furtive du message suivant]: #peek-message
  [Procédure : Enlèvement du message suivant de la file d'attente]: #get-message
  [Procédure : Modification du contenu d'un message en file d'attente]: #change-contents
  [Procédure : Options supplémentaires pour l'enlèvement des messages de la file d'attente]: #advanced-get
  [Procédure : Obtention de la longueur de la file d'attente]: #get-queue-length
  [Procédure : Suppression d'une file d'attente]: #delete-queue
  [Procédure : Utilisation des signatures d'accès partagé]: #sas
  [utilisation de l'API REST]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh264518.aspx
  [Azure Management Portal]: http://manage.windowsazure.com
  [Création et déploiement d'une application Node.js dans Site Web Azure]: /fr-fr/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [Service cloud Node.js avec stockage]: /fr-fr/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Application Web Node.js avec stockage]: /fr-fr/documentation/articles/storage-nodejs-use-table-storage-web-site/

  
  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png
  
  
  
  [Service cloud Node.js]: /fr-fr/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
 [Site web avec WebMatrix]: /fr-fr/documentation/articles/web-sites-nodejs-use-webmatrix/

<!--HONumber=35.1-->
