<properties 
	pageTitle="Utilisation du stockage d'objets blob (Node.js) | Microsoft Azure" 
	description="Découvrez comment utiliser le service BLOB Azure pour charger, télécharger, répertorier et supprimer du contenu d'objet blob. Les exemples sont écrits en Node.js." 
	services="storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="mwasson"/>





# Utilisation du service BLOB à partir de Node.js

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du
service Blob Azure. Les exemples sont écrits en utilisant
l'API Node.js. Les scénarios traités incluent **le téléchargement (vers une cible)**, **l'énumération**,
**le téléchargement (à partir d'une source)** et **la suppression** d'objets blobs. Pour plus d'informations sur les objets blob,
consultez la section [Étapes suivantes][].

## Sommaire

* [Présentation du service BLOB][]
* [Concepts][]
* [Création d'un compte Azure Storage][]
* [Création d'une application Node.js][]
* [Configuration de votre application pour accéder au stockage][]
* [Configuration d'une chaîne de connexion Azure Storage][]
* [Procédure : Création d'un conteneur][]  
* [Procédure : Téléchargement d'un objet blob dans un conteneur][]  
* [Procédure : Création d'une liste d'objets blob dans un conteneur][]  
* [Procédure : Téléchargement d'objets blob][]  
* [Procédure : Suppression d'un objet blob][]  
* [Procédure : Accès simultané][]     
* [Procédure : Utilisation des signatures d'accès partagé][]     
* [Étapes suivantes][]

[AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>Création d'un compte Azure Storage
[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"></a>Création d'une application Node.js

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les pages [Création et déploiement d'une application Node.js dans un site Web Azure], [Service cloud Node.js][Service cloud Node.js] (avec Windows PowerShell) ou [Site Web avec WebMatrix].

## <a name="configure-access"></a>Configuration de votre application pour accéder au stockage

Pour utiliser le stockage Azure, vous avez besoin du Kit de développement logiciel (SDK) Azure Storage pour Node.js, qui inclut un ensemble de bibliothèques pratiques qui communiquent avec les services REST de stockage.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) pour accéder au dossier dans lequel vous avez créé votre exemple d'application.

2.  Tapez **npm install azure-storage** dans la fenêtre de commande, ce qui doit
    aboutir à la sortie suivante :

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier qu'un
    dossier **node\_modules** a été créé. Dans ce dossier, recherchez le package
    **azure-storage** qui contient les bibliothèques dont vous avez besoin pour accéder
    au stockage.

### Importation du package

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier
**server.js** de l'application dans laquelle vous souhaitez utiliser le stockage :

    var azure = require('azure-storage');

## <a name="setup-connection-string"></a>Configuration d'une connexion Azure Storage

Le module Azure lit les variables d'environnement AZURE\_STORAGE\_ACCOUNT and AZURE\_STORAGE\_ACCESS\_KEY ou AZURE\_STORAGE\_CONNECTION\_STRING pour obtenir les informations obligatoires pour se connecter à votre compte de stockage Azure. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l'appel de **createBlobService**.

Pour un exemple de définition des variables d'environnement dans le portail de gestion pour un site web Azure, consultez [Application web Node.js avec stockage]

## <a name="create-container"></a>Procédure : Création d'un conteneur

L'objet **BlobService** permet d'utiliser des conteneurs et des objets blob. Le
code suivant crée un objet **BlobService**. Ajoutez ce qui suit vers
le début de **server.js** :

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] Vous pouvez accéder à un objet blob de façon anonyme en utilisant **createBlobServiceAnonymous** et en fournissant l'adresse hôte. Par exemple, `var blobSvc = azure.createBlobService('https://myblob.blob.core.windows.net/');`.

Tous les objets blob résident dans un conteneur. Pour créer un conteneur, utilisez **createContainerIfNotExists**. Le code suivant crée un nouveau conteneur appelé " mycontainer "

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

Si le conteneur est créé, `result` a la valeur true. Si le conteneur existe déjà, `result` a la valeur false. `response` contient des informations sur l'opération, y compris les informations [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) du conteneur.

###Sécurité du conteneur

Par défaut, les nouveaux conteneurs sont privés et ne sont pas accessibles de façon anonyme. Pour que le conteneur soit public afin de permettre l'accès anonyme, définissez le niveau d'accès au conteneur sur **blob** ou **container**.

* **Objet blob** : permet l'accès en lecture anonyme au contenu et aux métadonnées de l'objet blob de ce conteneur, mais pas aux métadonnées du conteneur telles que la liste de tous les objets blob d'un conteneur.

* **Conteneur** : permet l'accès en lecture anonyme au contenu et aux métadonnées de l'objet blob, ainsi qu'aux métadonnées du conteneur.

L'exemple suivant illustre le niveau d'accès défini sur **blob** : 

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

Vous pouvez également modifier le niveau d'accès d'un conteneur en le spécifiant via **setContainerAcl**. Dans l'exemple suivant, le niveau d'accès du conteneur est modifié :

    blobSvc.setContainerAcl('mycontainer', null, 'container', function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

Le résultat contient des informations sur l'opération, y compris les informations **ETag** du conteneur.

###Filtres

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées via **BlobService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

		function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler " next " en passant un rappel avec la signature suivante :

		function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse à la requête du serveur), le rappel doit appeler next, s'il existe, pour continuer à traiter d'autres filtres ou appeler finalCallback pour terminer l'appel du service.

Deux filtres implémentant la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **BlobService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-blob"></a>Procédure : Téléchargement d'un objet blob dans un conteneur

Un objet blob peut être de blocs ou de pages. Les objets blob de blocs permettent un téléchargement plus efficace des données volumineuses, alors que les objets blob de pages sont optimisés pour les opérations de lecture/écriture. Pour plus d'informations, consultez la page [Présentation des objets blob de blocs et de pages](http://msdn.microsoft.com/library/azure/ee691964.aspx).

###Objets blob de blocs

Pour télécharger des données dans un objet blob de blocs, utilisez :

* **createBlockBlobFromLocalFile** : permet de créer un objet blob de blocs et de télécharger le contenu d'un fichier.

* **createBlockBlobFromStream** : permet de créer un objet blob de blocs et de télécharger le contenu d'un flux.

* **createBlockBlobFromText** : permet de créer un objet blob de blocs et de télécharger le contenu d'une chaîne.

* **createWriteStreamToBlockBlob** : fournit un flux d'écriture vers un objet blob de blocs.

L'exemple suivant télécharge le contenu du fichier **test.txt** dans **myblob**.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

Le `result` retourné par ces méthodes contient les informations sur l'opération, comme l'**ETag** de l'objet blob.

###Objets blob de pages

Pour télécharger des données dans un objet blob de pages, utilisez :

* **createPageBlob** : permet de créer un objet blob de pages d'une certaine longueur.

* **createPageBlobFromLocalFile** : permet de créer un objet blob de pages et de télécharger le contenu d'un fichier.

* **createPageBlobFromStream** : permet de créer un objet blob de pages et de télécharger le contenu d'un flux.

* **createWriteStreamToExistingPageBlob** : fournit un flux d'écriture à un objet blob de pages existant.

* **createWriteStreamToNewPageBlob** : permet de créer un objet blob et de fournir un flux pour y écrire.

L'exemple suivant télécharge le contenu du fichier **test.txt** dans **mypageblob**.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE] Les objets blob de pages sont constitués de " pages " de 512 octets. Une erreur peut se produire lors du téléchargement de données lorsque leur taille n'est pas un multiple de 512.

## <a name="list-blob"></a>Procédure : Création d'une liste d'objets blob dans un conteneur

Pour créer une liste d'objets blob dans un conteneur, utilisez la méthode **listBlobsSegmented**. Pour renvoyer les objets blob avec un préfixe donné, utilisez **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

Le `result` contient une collection `entries`, qui est un tableau d'objets décrivant chaque objet blob. Si tous les objets blob ne peuvent pas être renvoyés, `result` fournit également un `continuationToken`, qui peut servir de deuxième paramètre pour extraire d'autres entrées.

## <a name="download-blob"></a>Procédure : Téléchargement d'objets blob

Pour télécharger les données d'un objet blob, utilisez :

* **getBlobToFile** : permet d'écrire le contenu de l'objet blob dans un fichier.

* **getBlobToStream** : permet d'écrire le contenu de l'objet blob dans un flux.

* **getBlobToText** : permet d'écrire le contenu de l'objet blob dans une chaîne.

* **createReadStream** : fournit un flux pour lire à partir de l'objet blob.

L'exemple suivant présente l'utilisation de **getBlobToStream** pour télécharger le contenu de l'objet blob **myblob** et le stocker dans le fichier **output.txt** en utilisant un flux :

    var fs=require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

Le `result` contient les informations de l'objet blob, y compris les informations **ETag**.

## <a name="delete-blob"></a>Procédure : Suppression d'un objet blob

Pour supprimer un objet blob, appelez **deleteBlob**. L'exemple suivant supprime l'objet blob **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

##<a name="concurrent-access"></a>Procédure : Accès simultané

Pour activer la prise en charge de l'accès simultané à un objet blob par plusieurs clients ou instances de processus, vous pouvez utiliser **ETags** ou **leases**.

* **Etag** permet de détecter la modification de l'objet blob ou du conteneur par un autre processus.

* **Le bail** permet d'obtenir l'accès exclusif, renouvelable, en écriture ou en suppression à un objet blob pour une période donnée.

###ETag

Les ETag doivent être utilisés si vous avez besoin que plusieurs clients ou instances écrivent simultanément sur l'objet blob. Les ETag vous permettent de déterminer si le conteneur ou l'objet blob a été modifié depuis sa création ou son premier accès en lecture, ce qui vous évite d'écraser des changements effectués par un autre client ou processus.

Les conditions ETag peuvent être définies à l'aide du paramètre facultatif `options.accessConditions`. L'exemple suivant télécharge le fichier **test.txt** uniquement si l'objet blob existe déjà et a la valeur ETag dans `etagToMatch`.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

L'utilisation des ETags suit le modèle général suivant :

1. Obtention de l'ETag à la suite d'une opération create, list ou get.

2. Exécution d'une action, recherche de la modification éventuelle de la valeur ETag.

Si la valeur a été modifiée, cela signifie qu'un autre client ou qu'une autre instance a modifié l'objet blob ou le conteneur depuis que vous avez obtenu la valeur ETag.

###Lease

Il est possible d'acquérir un nouveau bail à l'aide de la méthode **acquireLease**, en spécifiant l'objet blob ou le conteneur concerné. Par exemple, le code suivant permet d'acquérir un bail sur **myblob**.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log(result);
	  }
	});

Les opérations suivantes sur **myblob** doivent fournir le paramètre `options.leaseId`. L'ID du bail est renvoyé comme `result.id` à partir de **acquireLease**.

> [AZURE.NOTE] Par défaut, la durée du bail est infinie. Vous pouvez spécifier une durée finie (entre 15 et 60 secondes) en fournissant le paramètre `options.leaseDuration`.

Pour supprimer un bail, utilisez **releaseLease**. Pour terminer un bail, mais empêcher d'autres utilisateurs d'obtenir un nouveau bail avant la fin de la durée d'origine, utilisez **breakLease**.

## <a name="sas"></a>Procédure : Utilisation des signatures d'accès partagé

Les signatures d'accès partagé sont un moyen sécurisé de fournir un accès précis aux objets blob et aux conteneurs sans fournir le nom ni les clés de votre compte de stockage. Elles servent souvent à fournir un accès limité à vos données, par exemple pour autoriser une application mobile à accéder à des objets blob.

> [AZURE.NOTE] Vous pouvez bien sûr autoriser l'accès anonyme aux objets blob, mais les signatures d'accès partagé permettent de mieux contrôler les accès, car elles doivent être générées.

Une application approuvée, comme un service cloud, génère une signature d'accès partagé à l'aide de **generateSharedAccessSignature** de **BlobService** et la fournit à une application non approuvée ou à moitié approuvée. Par exemple, une application mobile. La signature d'accès partagé est générée à l'aide d'une stratégie, qui décrit les dates de début et de fin de validité de la signature, et le niveau d'accès accordé au détenteur de la signature.

L'exemple suivant génère une nouvelle stratégie d'accès partagé qui autorise le détenteur de la signature d'accès partagé à effectuer des opérations de lecture sur l'objet blob **myblob** et expire 100 minutes après son heure de création.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	    
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};
	
	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

Notez que les informations sur l'hôte doivent également être fournies, car elles sont obligatoires lorsque le détenteur de la signature d'accès partagé tente d'accéder au conteneur.

L'application cliente utilise ensuite les signatures d'accès partagé avec **BlobServiceWithSAS** pour effectuer les opérations sur l'objet blob. Le code suivant obtient les informations sur **myblob**.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Comme la signature d'accès partagé a été générée avec un accès en lecture seule, une erreur sera renvoyée en cas de tentative de modification de l'objet blob.

###Listes de contrôle d'accès

Vous pouvez également utiliser une liste de contrôle d'accès (ACL) pour définir la stratégie d'accès pour une signature d'accès partagé. Cela est utile si vous voulez autoriser plusieurs clients à accéder à un conteneur, mais fournir des stratégies d'accès différentes à chaque client.

Une liste de contrôle d'accès est implémentée à l'aide d'un tableau de stratégies d'accès, dans lequel un ID est associé à chaque stratégie. L'exemple suivant définit deux stratégies ; une pour " user1 " et une pour " user2 " :

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

L'exemple suivant obtient la liste de contrôle d'accès active pour **mycontainer**, puis ajoute les nouvelles stratégies à l'aide de **setBlobAcl**. Cette approche permet :

	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Lorsque la liste de contrôle d'accès est définie, vous pouvez créer une signature d'accès partagé basée sur l'ID pour une stratégie. L'exemple suivant crée une signature d'accès partagé pour " user2 " :

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les bases du stockage d'objets blob, consultez les liens suivants
pour apprendre à réaliser des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][].
-   Visitez le [Blog de l'équipe Azure Storage][].
-   Consultez le référentiel [Kit de développement logiciel (SDK) Azure Storage pour Node][] sur GitHub.

  [Kit de développement logiciel (SDK) Azure Storage pour Node]: https://github.com/Azure/azure-storage-node
  [Étapes suivantes]: #next-steps
  [Présentation du service BLOB]: #what-is
  [Concepts]: #concepts
  [Création d'un compte Azure Storage]: #create-account
  [Création d'une application Node.js]: #create-app
  [Configuration de votre application pour accéder au stockage]: #configure-access
  [Configuration d'une chaîne de connexion Azure Storage]: #setup-connection-string
  [Procédure : Création d'un conteneur]: #create-container
  [Procédure : Téléchargement d'un objet blob dans un conteneur]: #upload-blob
  [Procédure : Création d'une liste d'objets blob dans un conteneur]: #list-blob
  [Procédure : Téléchargement d'objets blob]: #download-blobs
  [Procédure : Suppression d'un objet blob]: #delete-blobs
  [Procédure : Accès simultané]: #concurrent-access
  [Procédure : Utilisation des signatures d'accès partagé]: #sas
[Création et déploiement d'une application Node.js dans un site Web Azure]: /fr-FR/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /fr-FR/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Application web Node.js avec stockage]: /fr-FR/documentation/articles/storage-nodejs-use-table-storage-web-site/
 [Site Web avec WebMatrix]: /fr-FR/documentation/articles/web-sites-nodejs-use-webmatrix/
  [using the REST API]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
  [Azure Management Portal]: http://manage.windowsazure.com
  [Service cloud Node.js]: /fr-FR/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/

\<!--HONumber=42-->
