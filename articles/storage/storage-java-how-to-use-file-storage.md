<properties
	pageTitle="Utilisation du stockage de fichiers à partir de Java | Microsoft Azure"
	description="Découvrez comment utiliser le service de fichiers Azure pour charger, télécharger, répertorier et supprimer des fichiers. Les exemples sont écrits en Java."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe" />

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="01/11/2016"
	ms.author="jutang"/>

# Utilisation du stockage de fichiers à partir de Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

## Vue d'ensemble

Ce guide explique comment effectuer des opérations de base sur le service de stockage de fichiers Microsoft Azure. Au moyen d’exemples écrits en Java, vous allez apprendre à créer des partages et des répertoires, ainsi qu’à télécharger, répertorier et supprimer des fichiers. Si vous ne connaissez pas le service de stockage de fichiers Microsoft Azure, l’étude des concepts abordés dans les sections suivantes vous sera très utile pour comprendre les exemples.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d’une application Java

Pour générer les exemples, vous avez besoin du Kit de développement Java (JDK) et du [Kit de développement Azure Storage (SDK) pour Java][]. Vous devez également avoir préalablement créé un compte de stockage Azure.

## Configuration de votre application pour l’utilisation du stockage de fichiers

Pour utiliser les API de stockage Azure, ajoutez l’instruction suivante au début du fichier Java depuis lequel vous voulez accéder au service de stockage.

	// Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
	import com.microsoft.azure.storage.file.*;

## Configuration d’une chaîne de connexion de stockage Azure

Pour pouvoir utiliser le stockage de fichiers, vous devez vous connecter à votre compte de stockage Azure. La première étape consiste à configurer une chaîne de connexion, que nous allons utiliser pour nous connecter à votre compte de stockage. Pour cela, nous allons définir une variable statique.

	// Configure the connection-string with your values
	public static final String storageConnectionString =
	    "DefaultEndpointsProtocol=http;" +
	    "AccountName=your_storage_account_name;" +
	    "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Remplacez your\_storage\_account\_name (nom de votre compte de stockage) et your\_storage\_account\_key (clé de votre compte de stockage) par les valeurs réelles de votre compte de stockage.

## Connexion à un compte de stockage Azure

Pour vous connecter à votre compte de stockage, vous devez utiliser l’objet **CloudStorageAccount**, en transmettant une chaîne de connexion à sa méthode **parse**.

	// Use the CloudStorageAccount object to connect to your storage account
	try {
		CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
	} catch (InvalidKeyException invalidKey) {
		// Handle the exception
	}

**CloudStorageAccount.parse** lève une exception InvalidKeyException. Vous devrez donc le placer dans un bloc try/catch.

## Création d’un partage

Tous les fichiers et répertoires d’un stockage de fichiers se trouvent dans un conteneur appelé **partage**. Votre compte de stockage peut avoir autant de partages que le permet la capacité de votre compte. Pour pouvoir accéder à un partage et à son contenu, vous devez utiliser un client de stockage de fichiers.

	// Create the file storage client.
	CloudFileClient fileClient = storageAccount.createCloudFileClient();

À l’aide de ce dernier, vous pouvez ensuite obtenir une référence à un partage.

	// Get a reference to the file share
	CloudFileShare share = fileClient.getShareReference("sampleshare");

Pour créer le partage, utilisez la méthode **createIfNotExists** de l’objet CloudFileShare.

	if (share.createIfNotExists()) {
		System.out.println("New share created");
	}

À ce stade, le **partage** contient une référence à un partage nommé **sampleshare**.

## Chargement d’un fichier

Un partage de fichiers de stockage Azure contient au minimum un répertoire racine dans lequel les fichiers peuvent résider. Cette section décrit comment télécharger un fichier du stockage local vers le répertoire racine d’un partage.

La première étape du téléchargement d’un fichier consiste à obtenir une référence au répertoire dans lequel ce fichier doit résider. Pour cela, vous devez appeler la méthode **getRootDirectoryReference** de l’objet de partage.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

Maintenant que vous avez une référence au répertoire racine du partage, vous pouvez télécharger un fichier vers ce répertoire à l’aide du code ci-après.

	// Define the path to a local file.
	final String filePath = "C:\\temp\\Readme.txt";

	CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
	cloudFile.uploadFromFile(filePath);

## Création d’un répertoire

Vous pouvez également organiser le stockage en plaçant des fichiers dans des sous-répertoires, plutôt que de tous les mettre dans le répertoire racine. Le service de stockage de fichiers Azure permet de créer autant de répertoires que le permet votre compte. Le code ci-dessous crée un sous-répertoire nommé **sampledir** sous le répertoire racine.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the sampledir directory
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

	if (sampleDir.createIfNotExists()) {
		System.out.println("sampledir created");
	} else {
		System.out.println("sampledir already exists");
	}

## Obtention d’une liste des fichiers et répertoires d’un partage

Il est facile d’obtenir la liste de fichiers et de répertoires d’un partage en appelant **listFilesAndDirectories** sur une référence CloudFileDirectory. La méthode renvoie une liste d’objets ListFileItem sur laquelle vous pouvez effectuer une itération. Par exemple, le code suivant répertorie les fichiers et répertoires du répertoire racine.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
		System.out.println(fileItem.getUri());
	}


## Téléchargement d’un fichier

Le téléchargement de fichier est l’une des opérations les plus fréquentes que vous effectuerez sur le stockage de fichiers. Dans l’exemple suivant, le code télécharge SampleFile.txt et affiche son contenu.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the directory that contains the file
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

	//Get a reference to the file you want to download
	CloudFile file = sampleDir.getFileReference("SampleFile.txt");

	//Write the contents of the file to the console.
	System.out.println(file.downloadText());

## Suppression d’un fichier

La suppression de fichier est également une opération de stockage de fichier courante. Le code suivant supprime un fichier nommé SampleFile.txt et stocké dans un répertoire nommé **sampledir**.


	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory where the file to be deleted is in
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

	String filename = "SampleFile.txt"
	CloudFile file;

	file = containerDir.getFileReference(filename)
	if ( file.deleteIfExists() ) {
		System.out.println(filename + " was deleted");
	}


## Suppression d’un répertoire

La suppression d’un répertoire est une tâche relativement simple, mais il convient de noter que vous ne pouvez pas supprimer de répertoire contenant des fichiers ou d’autres répertoires.

	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory you want to delete
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

	// Delete the directory
	if ( containerDir.deleteIfExists() ) {
		System.out.println("Directory deleted");
	}


## Suppression d’un partage

La suppression d’un partage s’effectue en appelant la méthode **deleteIfExists** sur un objet CloudFileShare. Voici un exemple de code permettant d’effectuer cette opération.

	try
	{
	    // Retrieve storage account from connection-string.
	    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	    // Create the file client.
	   CloudFileClient fileClient = storageAccount.createCloudFileClient();

	   // Get a reference to the file share
	   CloudFileShare share = fileClient.getShareReference("sampleshare");

	   if (share.deleteIfExists()) {
		   System.out.println("sampleshare deleted");
	   }
	} catch (Exception e) {
		e.printStackTrace();
	}

## Étapes suivantes

Pour en savoir plus sur les autres API de stockage Azure, suivez ces liens.

- [Kit de développement logiciel (SDK) Azure Storage pour Java]
- [Référence du Kit de développement logiciel (SDK) du client Azure Storage]
- [API REST d’Azure Storage]
- [Blog de l'équipe Azure Storage]
- [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy)

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Kit de développement Azure Storage (SDK) pour Java]: https://github.com/azure/azure-storage-java
[Kit de développement logiciel (SDK) Azure Storage pour Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Référence du Kit de développement logiciel (SDK) du client Azure Storage]: http://dl.windowsazure.com/storage/javadoc/
[API REST d’Azure Storage]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/

<!---HONumber=AcomDC_0128_2016-->