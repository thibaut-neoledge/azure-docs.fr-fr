---
title: "Utilisation du stockage de fichiers à partir de Java | Microsoft Docs"
description: "Découvrez comment utiliser le service de fichiers Azure pour charger, télécharger, répertorier et supprimer des fichiers. Les exemples sont écrits en Java."
services: storage
documentationcenter: java
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 60d4bcafa51a6eedea06901a0557d13b4e85ec7a


---
# <a name="how-to-use-file-storage-from-java"></a>Utilisation du stockage de fichiers à partir de Java
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Vue d'ensemble
Ce guide explique comment effectuer des opérations de base sur le service de stockage de fichiers Microsoft Azure. Au moyen d’exemples écrits en Java, vous allez apprendre à créer des partages et des répertoires, ainsi qu’à télécharger, répertorier et supprimer des fichiers. Si vous ne connaissez pas le service de stockage de fichiers Microsoft Azure, l’étude des concepts abordés dans les sections suivantes vous sera très utile pour comprendre les exemples.

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Création d’une application Java
Pour générer les exemples, vous avez besoin du Kit de développement Java (JDK) et du [Kit de développement Azure Storage (SDK) pour Java][]. Vous devez également avoir préalablement créé un compte de stockage Azure.

## <a name="setup-your-application-to-use-file-storage"></a>Configuration de votre application pour l’utilisation du stockage de fichiers
Pour utiliser les API de stockage Azure, ajoutez l’instruction suivante au début du fichier Java depuis lequel vous voulez accéder au service de stockage.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Configuration d’une chaîne de connexion de stockage Azure
Pour pouvoir utiliser le stockage de fichiers, vous devez vous connecter à votre compte de stockage Azure. La première étape consiste à configurer une chaîne de connexion, que nous allons utiliser pour nous connecter à votre compte de stockage. Pour cela, nous allons définir une variable statique.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Remplacez your_storage_account_name (nom de votre compte de stockage) et your_storage_account_key (clé de votre compte de stockage) par les valeurs réelles de votre compte de stockage.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Connexion à un compte de stockage Azure
Pour vous connecter à votre compte de stockage, vous devez utiliser l’objet **CloudStorageAccount**, en transmettant une chaîne de connexion à sa méthode **parse**.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** lève une exception InvalidKeyException. Vous devrez donc le placer dans un bloc try/catch.

## <a name="how-to-create-a-share"></a>Création d’un partage
Tous les fichiers et répertoires d’un stockage de fichiers se trouvent dans un conteneur appelé **partage**. Votre compte de stockage peut avoir autant de partages que le permet la capacité de votre compte. Pour pouvoir accéder à un partage et à son contenu, vous devez utiliser un client de stockage de fichiers.

```java
// Create the file storage client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

À l’aide de ce dernier, vous pouvez ensuite obtenir une référence à un partage.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Pour créer le partage, utilisez la méthode **createIfNotExists** de l’objet CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

À ce stade, le **partage** contient une référence à un partage nommé **sampleshare**.

## <a name="how-to-upload-a-file"></a>Chargement d’un fichier
Un partage de fichiers de stockage Azure contient au minimum un répertoire racine dans lequel les fichiers peuvent résider. Cette section décrit comment télécharger un fichier du stockage local vers le répertoire racine d’un partage.

La première étape du téléchargement d’un fichier consiste à obtenir une référence au répertoire dans lequel ce fichier doit résider. Pour cela, vous devez appeler la méthode **getRootDirectoryReference** de l’objet de partage.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Maintenant que vous avez une référence au répertoire racine du partage, vous pouvez télécharger un fichier vers ce répertoire à l’aide du code ci-après.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="how-to-create-a-directory"></a>Création d’un répertoire
Vous pouvez également organiser le stockage en plaçant des fichiers dans des sous-répertoires, plutôt que de tous les mettre dans le répertoire racine. Le service de stockage de fichiers Azure permet de créer autant de répertoires que le permet votre compte. Le code ci-dessous crée un sous-répertoire nommé **sampledir** sous le répertoire racine.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="how-to-list-files-and-directories-in-a-share"></a>Obtention d’une liste des fichiers et répertoires d’un partage
Il est facile d’obtenir la liste de fichiers et de répertoires d’un partage en appelant **listFilesAndDirectories** sur une référence CloudFileDirectory. La méthode renvoie une liste d’objets ListFileItem sur laquelle vous pouvez effectuer une itération. Par exemple, le code suivant répertorie les fichiers et répertoires du répertoire racine.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="how-to-download-a-file"></a>Téléchargement d’un fichier
Le téléchargement de fichier est l’une des opérations les plus fréquentes que vous effectuerez sur le stockage de fichiers. Dans l’exemple suivant, le code télécharge SampleFile.txt et affiche son contenu.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="how-to-delete-a-file"></a>Suppression d’un fichier
La suppression de fichier est également une opération de stockage de fichier courante. Le code suivant supprime un fichier nommé SampleFile.txt et stocké dans un répertoire nommé **sampledir**.

```java
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
```

## <a name="how-to-delete-a-directory"></a>Suppression d’un répertoire
La suppression d’un répertoire est une tâche relativement simple, mais il convient de noter que vous ne pouvez pas supprimer de répertoire contenant des fichiers ou d’autres répertoires.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="how-to-delete-a-share"></a>Suppression d’un partage
La suppression d’un partage s’effectue en appelant la méthode **deleteIfExists** sur un objet CloudFileShare. Voici un exemple de code permettant d’effectuer cette opération.

```java
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
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les autres API de stockage Azure, suivez ces liens.

* [Centre de développement Java](http://azure.microsoft.com/develop/java/)
* [Kit de développement logiciel (SDK) Azure Storage pour Java](https://github.com/azure/azure-storage-java)
* [Kit de développement logiciel (SDK) Azure Storage pour Android](https://github.com/azure/azure-storage-android)
* [Référence du Kit de développement logiciel (SDK) du client Azure Storage](http://dl.windowsazure.com/storage/javadoc/)
* [API REST des services d’Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
* [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)




<!--HONumber=Nov16_HO3-->


