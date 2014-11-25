<properties urlDisplayName="Blob Service" pageTitle="Utilisation du stockage d'objets blob (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Utilisation du stockage d'objets blob à partir de Java

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage d'objets blob Microsoft Azure. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel (SDK) Azure Storage pour Java][Kit de développement logiciel (SDK) Azure Storage pour Java]. Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d'une source)** et la **suppression** d'objets blob. Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes](#NextSteps).

Remarque : Un Kit de développement logiciel (SDK) est disponible pour les développeurs qui utilisent Azure Storage sur des appareils Android. Pour plus d'informations, consultez la page [Kit de développement logiciel (SDK) Azure Storage pour Android][Kit de développement logiciel (SDK) Azure Storage pour Android]. 

## <a name="Contents"> </a>Sommaire

* [Présentation du stockage d'objets blob](#what-is)
* [Concepts](#Concepts)
* [Création d'un compte de stockage Azure](#CreateAccount)
* [Création d'une application Java](#CreateApplication)
* [Configuration de votre application pour accéder au stockage d'objets blob](#ConfigureStorage)
* [Configuration d'une chaîne de connexion de stockage Azure](#ConnectionString)
* [ Création d'un conteneur](#CreateContainer)
* [ Téléchargement d'un objet blob dans un conteneur](#UploadBlob)
* [ Création d'une liste d'objets blob dans un conteneur](#ListBlobs)
* [ Téléchargement d'un objet blob](#DownloadBlob)
* [ Suppression d'un objet blob](#DeleteBlob)
* [ Suppression d'un conteneur d'objets blob](#DeleteContainer)
* [Étapes suivantes](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Création d'un compte de stockage Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Création d'une application Java

Dans ce guide, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application Java en local, ou dans le code s'exécutant dans un rôle Web ou un rôle de travail dans Azure.

Pour ce faire, vous devez installer le Kit de développement Java (JDK) et créer un compte Azure Storage dans votre abonnement Azure. Vous devez ensuite vérifier que votre système de développement répond à la configuration minimale requise et aux dépendances répertoriées dans le référentiel [Kit de développement logiciel (SDK) Azure Storage pour Java][Kit de développement logiciel (SDK) Azure Storage pour Java] sur GitHub. Si tel est le cas, vous pouvez suivre les instructions relatives au téléchargement et à l'installation des bibliothèques Azure Storage pour Java sur votre système à partir du référentiel. Une fois ces tâches effectuées, vous pouvez créer une application Java utilisant les exemples de cet article.

## <a name="ConfigureStorage"> </a>Configuration de votre application pour accéder au stockage d'objets blob

Ajoutez l'instruction import suivante au début du fichier Java dans lequel vous voulez utiliser des API de stockage Azure pour accéder aux objets blob :

    // Ajoutez les imports suivants pour utiliser des API d'objets Blob.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## <a name="ConnectionString"> </a>Configuration d'une chaîne de connexion de stockage Azure

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker
des points de terminaison et des informations d'identification permettant d'accéder aux services de gestion des données. Lors de l'exécution d'une application cliente, vous devez spécifier la chaîne de connexion de stockage au format suivant, en utilisant le nom de votre compte de stockage et la clé d'accès primaire pour le compte de stockage, répertoriés sur le portail de gestion pour les valeurs *AccountName* et *AccountKey*. Cet exemple vous montre comment déclarer un champ statique pour qu'il contienne une chaîne de connexion :

    // Définissez la chaîne de connexion à l'aide de vos valeurs
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Dans une application exécutée au sein d'un rôle dans Microsoft Azure, cette chaîne peut être stockée dans le fichier de configuration de service *ServiceConfiguration.cscfg* et elle est accessible en appelant la méthode **RoleEnvironment.getConfigurationSettings**. Voici un exemple de code vous permettant d'extraire la chaîne de connexion à partir d'un élément **Setting** nommé *StorageConnectionString* dans le fichier de configuration de service :

    // Récupérez un compte de stockage à partir d'une chaîne de connexion.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Les exemples ci-dessous partent du principe que vous avez utilisé l'une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## <a name="CreateContainer"> </a> Création d'un conteneur

Un objet CloudBlobClient vous permet d'obtenir les objets de référence pour les conteneurs et objets blob. Le code suivant crée un objet **CloudBlobClient**. Remarque : d'autres méthodes permettent de créer des objets **CloudStorageAccount**. Pour plus d'informations, reportez-vous à la classe **CloudStorageAccount** sur la page [Référence du Kit de développement logiciel (SDK) du client Azure Storage].)

Tous les objets blob résident dans un conteneur. Utilisez l'objet **CloudBlobClient** pour obtenir une référence pointant vers le conteneur à utiliser. Si le conteneur n'existe pas, vous pouvez le créer en utilisant la méthode **createIfNotExists** ; sinon, le conteneur existant est renvoyé. Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur.

	try
    {
        // Récupérez un compte de stockage à partir d'une chaîne de connexion.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Créez le client d'objet blob.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Récupérez une référence pointant vers un conteneur.
	   // Le nom du conteneur doit être en minuscule
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

	   // Créez le conteneur s'il n'existe pas.
    	container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Obtenez la trace de la pile.
        e.printStackTrace();
    }

###Facultatif : Configuration d'un conteneur pour un accès public ###

Par défaut, les autorisations d'un conteneur sont configurées pour un accès privé. Cependant, vous pouvez aisément les configurer pour permettre un accès public en lecture seule pour tous les internautes :

    // Créez un objet d'autorisations.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Ajoutez un accès public à l'objet d'autorisations.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Définissez les autorisations du conteneur.
    container.uploadPermissions(containerPermissions);

## <a name="UploadBlob"> </a> Téléchargement d'un objet blob dans un conteneur

Pour télécharger un fichier vers un objet blob, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d'objet blob. Dès lors que vous disposez d'une référence d'objet blob, vous pouvez télécharger un flux vers cet objet. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. Cet exemple de code illustre ce point en supposant que le conteneur existe.

	try
    {
        // Récupérez un compte de stockage à partir d'une chaîne de connexion.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Créez le client d'objet blob.
    	CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Récupérez une référence pointant vers un conteneur créé précédemment.
    	CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
        // Définissez le chemin d'accès au fichier local.
        final String filePath = "C:\\myimages\\myimage.jpg";

    	// Créez ou remplacez l'objet blob "myimage.jpg" avec du contenu provenant d'un fichier local.
    	CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    	File source = new File(filePath);
    	blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Obtenez la trace de la pile.
        e.printStackTrace();
    }

## <a name="ListBlobs"> </a> Création d'une liste d'objets blob dans un conteneur

Pour créer une liste d'objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur comme pour le téléchargement d'un objet blob. Vous pouvez utiliser la méthode **listBlobs** du conteneur avec une boucle **for**. Le code suivant génère l'URI de chaque objet blob d'un conteneur sur la console.

	try
    {
        // Récupérez un compte de stockage à partir d'une chaîne de connexion.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Créez le client d'objet blob.
    	CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    	// Récupérez une référence pointant vers un conteneur créé précédemment.
    	CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
    	// Créez une boucle autour des objets blob du conteneur et sortez l'URI dans chacun d'eux.
    	for (ListBlobItem blobItem : container.listBlobs()) {
	       System.out.println(blobItem.getUri());
	   }
    }
    catch (Exception e)
    {
        // Sortez la trace de la pile.
        e.printStackTrace();
    }

Le service BLOB suit également le concept de répertoires dans des conteneurs. Vous pouvez donc organiser vos objets blob selon une structure proche de celle des dossiers.

Par exemple, vous pouvez avoir un conteneur nommé " photos ", dans lequel vous pouvez télécharger des objets blob nommés " rootphoto1 ", " 2010/photo1 ", " 2010/photo2 " et " 2011/photo1 ". Vous créez ainsi virtuellement les répertoires " 2010 " et " 2011 " dans le conteneur " photos ". Lorsque vous appelez la méthode **listBlobs** pour le conteneur " photos ", la collection renvoyée contient les objets **CloudBlobDirectory** et **CloudBlob** qui représentent les répertoires et objets blob contenus au niveau supérieur. Dans ce cas, les répertoires " 2010 " et " 2011 " et la photo " rootphoto1 " sont renvoyés. Vous pouvez utiliser l'opérateur **instanceof** pour différencier ces objets.

Vous pouvez également transmettre des paramètres à la méthode **listBlobs** avec
le paramètre **useFlatBlobListing** défini sur true. Cela permet de
renvoyer chaque objet blob, indépendamment du répertoire. Pour plus
d'informations, reportez-vous à la classe **CloudBlobContainer.listBlobs** sur la page [Référence du Kit de développement logiciel (SDK) du client Azure Storage].

## <a name="DownloadBlob"> </a> Téléchargement d'un objet blob

Pour télécharger des objets blob, procédez comme pour le chargement d'un objet blob afin d'obtenir une référence d'objet blob. Dans l'exemple de chargement, vous avez appelé la méthode upload sur l'objet blob. Dans l'exemple suivant, appelez la méthode download pour transférer les contenus d'objets blob vers un objet de flux tel que **FileOutputStream** pouvant être utilisé pour rendre l'objet blob persistant dans un fichier local.

    try
    {
    	// Récupérez un compte de stockage à partir d'une chaîne de connexion.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Créez le client d'objet blob.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Récupérez une référence pointant vers un conteneur créé précédemment.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Créez une boucle entre chaque élément d'objet blob dans le conteneur.
	   for (ListBlobItem blobItem : container.listBlobs()) {
	       // Si l'élément est un objet blob et non un répertoire virtuel.
	       if (blobItem instanceof CloudBlob) {
	           // Téléchargez l'élément et enregistrez-le dans un fichier du même nom.
    	        CloudBlob blob = (CloudBlob) blobItem;
    	        blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
    	    }
    	}
    }
    catch (Exception e)
    {
        // Sortez la trace de la pile.
        e.printStackTrace();
    }

## <a name="DeleteBlob"> </a> Suppression d'un objet blob

Pour supprimer un objet blob, obtenez une référence d'objet blob et appelez la méthode **deleteIfExists**.

    try
    {
	   // Récupérez un compte de stockage à partir d'une chaîne de connexion.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Créez le client d'objet blob.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Récupérez une référence pointant vers un conteneur créé précédemment.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Récupérez une référence pointant vers un objet blob nommé "myimage.jpg".
	   CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

	   // Supprimez l'objet blob.
	   blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Sortez la trace de la pile.
        e.printStackTrace();
    }

## <a name="DeleteContainer"> </a> Suppression d'un conteneur d'objets blob

Pour supprimer un conteneur d'objets blob, commencez par obtenir une référence de conteneur d'objets blob, puis
appelez la méthode **deleteIfExists**.

    try
    {
	   // Récupérez un compte de stockage à partir d'une chaîne de connexion.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Créez le client d'objet blob.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Récupérez une référence pointant vers un conteneur créé précédemment.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Supprimez le conteneur d'objets blob.
	   container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Sortez la trace de la pile.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Étapes suivantes

Maintenant que vous avez appris les bases du stockage d'objets blob, suivez ces liens pour apprendre des tâches de stockage plus complexes.

- [Kit de développement logiciel (SDK) Azure Storage pour Java]
- [Référence du Kit de développement logiciel (SDK) du client Azure Storage]
- [API REST d'Azure Storage]
- [Blog de l'équipe Azure Storage]

[Kit de développement logiciel (SDK) Azure pour Java]: http://www.windowsazure.com/fr-fr/develop/java/
[Kit de développement logiciel (SDK) Azure Storage pour Java]: https://github.com/azure/azure-storage-java
[Kit de développement logiciel (SDK) Azure Storage pour Android]: https://github.com/azure/azure-storage-android
[Référence du Kit de développement logiciel (SDK) du client Azure Storage]: http://dl.windowsazure.com/storage/javadoc/
[API REST d'Azure Storage]: http://msdn.microsoft.com/fr-fr/library/azure/gg433040.aspx
[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
