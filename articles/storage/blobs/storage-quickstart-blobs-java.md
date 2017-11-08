---
title: "Guide de démarrage rapide Azure - Transférer des objets vers/à partir du stockage blob Azure avec Java | Microsoft Docs"
description: "Apprenez rapidement à transférer des objets vers/à partir du stockage blob Azure avec Java"
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: quickstart
ms.date: 11/01/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: 0ed27077f649df2d0673351cda708b9a358ff92b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-java"></a>Transférer des objets vers/à partir du stockage blob Azure avec Java

Dans ce guide de démarrage rapide, vous apprenez à utiliser Java pour charger, télécharger et lister des objets blob de blocs dans un conteneur de stockage blob Azure.

## <a name="prerequisites"></a>Composants requis

Pour effectuer ce démarrage rapide :

* Installez un environnement de développement intégré (IDE) pour lequel Maven est intégré ou installez et configurez Maven pour travailler à partir de la ligne de commande
    * Ce didacticiel utilise [Eclipse](http://www.eclipse.org/downloads/) avec la configuration « IDE Eclipse pour les développeurs Java »
    


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-storage-account-using-the-azure-portal"></a>Créer un compte de stockage dans le portail Azure

Commencez par créer un compte de stockage à usage général pour les besoins de ce guide de démarrage rapide. 

1. Accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure. 
2. Dans le menu Hub, sélectionnez **Nouveau** > **Stockage** > **Compte de stockage - blob, fichier, table, file d’attente**. 
3. Entrez un nom pour votre compte de stockage. Le nom doit contenir entre 3 et 24 caractères, et uniquement des lettres minuscules et des chiffres. Il doit également être unique.
4. Définissez `Deployment model` sur **Resource Manager**.
5. Définissez `Account kind` sur **Usage général**.
6. Définissez `Performance` sur **Standard**. 
7. Définissez `Replication` sur **Stockage localement redondant (LRS)**.
8. Définissez `Storage service encryption` sur **Désactivé**.
9. Définissez `Secure transfer required` sur **Désactivé**.
10. Sélectionnez votre abonnement. 
11. Pour `resource group`, créez-en un et donnez-lui un nom unique. 
12. Sélectionnez le paramètre `Location` à utiliser pour votre compte de stockage.
13. Cochez **Épingler au tableau de bord** et cliquez sur **Créer** pour créer votre compte de stockage. 

Une fois votre compte de stockage créé, il est épinglé au tableau de bord. Cliquez sur le compte pour l’ouvrir. Sous PARAMÈTRES, cliquez sur **Clés d’accès**. Sélectionnez une clé et copiez-la dans le Presse-papiers, puis collez-la dans un éditeur de texte pour l’utiliser plus tard. Copiez également le nom de votre compte de stockage et collez-le dans un éditeur de texte pour une utilisation ultérieure.

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

[L’exemple d’application](https://github.com/Azure-Samples/storage-blobs-java-quickstart) utilisé dans ce guide de démarrage rapide est une application console de base. 

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Cette commande clone le dépôt dans votre dossier git local. Pour ouvrir le projet, lancez Eclipse et fermez l’écran d’accueil. Sélectionnez **Fichier**, puis **Open Projects from File System** (Ouvrir des projets à partir du système de fichiers). Vérifiez que l’option **Detect and configure project natures** (Détecter et configurer les natures de projet) est activée. Sélectionnez **Répertoire**, puis accédez à l’emplacement où vous avez stocké le référentiel cloné. Dans celui-ci, sélectionnez le dossier**javaBlobsQuickstart**. Vérifiez que le projet **javaBlobsQuickstarts** s’affiche en tant que projet Eclipse, puis sélectionnez **Terminer**.

Une fois l’importation du projet terminée, ouvrez **AzureApp.java** (situé dans **blobQuickstart.blobAzureApp** à l’intérieur de **src/main/java**) et remplacez `accountname` et `accountkey` dans la chaîne `storageConnectionString`. Puis exécutez l’application.
     

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage
    
Dans l’application, vous devez fournir la chaîne de connexion de votre compte de stockage. Ouvrez le fichier **AzureApp.Java**. Recherchez la variable `storageConnectionString`. Remplacez les valeurs `AccountName` et `AccountKey` dans la chaîne de connexion par celles que vous avez enregistrées à partir du portail Azure. Votre `storageConnectionString` doit être semblable à ce qui suit :

```java
    public static final String storageConnectionString ="DefaultEndpointsProtocol=https;" +
     "AccountName=<Namehere>;" +
    "AccountKey=<Keyhere>";
```

## <a name="run-the-sample"></a>Exécution de l'exemple

Cet exemple crée un fichier de test dans votre répertoire par défaut (Mes Documents, pour les utilisateurs de Windows), le charge dans le stockage blob, liste les objets blob dans le conteneur, puis télécharge le fichier avec un nouveau nom pour vous permettre de comparer les fichiers anciens et nouveaux. 

Exécutez l’exemple en appuyant sur **Ctrl+F11** dans Eclipse.

Si vous souhaitez exécuter l’exemple à l’aide de Maven au niveau de la ligne de commande, ouvrez un interpréteur de commandes et accédez à **blobAzureApp** à l’intérieur de votre répertoire cloné. Puis saisissez `mvn compile exec:java`.

Voici un exemple de résultat si vous deviez exécuter l’application sur Windows.

```
Location of file: C:\Users\<user>\Documents\results.txt
File has been written
URI of blob is: http://myexamplesacct.blob.core.windows.net/quickstartblobs/results.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

 Avant de continuer, vérifiez votre répertoire par défaut (Mes Documents, pour les utilisateurs de Windows) pour les deux fichiers. Vous pouvez les ouvrir et constater qu’ils sont identiques. Copiez l’URL de l’objet blob en dehors de la fenêtre de console et collez-la dans un navigateur pour afficher le contenu du fichier dans Stockage Blob. Quand vous appuyez sur la touche Entrée, le conteneur de stockage et les fichiers sont supprimés.

Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage. 

Une fois que vous avez vérifié les fichiers, appuyez sur la touche Entrée pour terminer la démonstration et supprimer les fichiers de test. Comme vous savez maintenant ce que fait l’exemple, ouvrez le fichier**AzureApp.java** pour examiner le code. 

## <a name="get-references-to-the-storage-objects"></a>Obtenir des références aux objets de stockage

La première chose à faire est de créer les références aux objets utilisés pour accéder et gérer Stockage Blob. Ces objets reposent l’un sur l’autre : chacun est utilisé par le suivant dans la liste.

* Créez une instance de l’objet **CloudStorageAccount** pointant vers le [compte de stockage](/java/api/com.microsoft.azure.management.storage._storage_account). 

* Créez une instance de l’objet **CloudBlobClient** pointant vers le [service Blob](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) de votre compte de stockage. 

* Créez une instance de l’objet **CloudBlobContainer** qui représente le [conteneur](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) auquel vous accédez. Les conteneurs sont utilisés pour organiser vos objets blob de la même façon que vous utilisez des dossiers pour organiser vos fichiers sur votre ordinateur.

Une fois que vous avez le **CloudBlobContainer**, vous pouvez créer une instance de l’objet **CloudBlockBlob** qui pointe vers l’objet [blob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) spécifique qui vous intéresse, et effectuer une opération de chargement, téléchargement, copie, etc.

Dans cette section, vous créez une instance des objets, un conteneur, puis définissez des autorisations sur le conteneur pour les objets blob soient publics et accessibles par une simple URL. Le conteneur est appelé **quickstartblobs**. 

Cet exemple utilise **CreateIfNotExists**, car nous voulons créer un conteneur chaque fois que l’exemple est exécuté. Dans un environnement de production où vous utilisez le même conteneur pour toute l’application, il est préférable d’appeler une seule fois **CreateIfNotExists**. Ou vous pouvez créer le conteneur à l’avance pour ne pas avoir à le créer dans le code.

```java
CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
        
CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

// Get a reference to a container.
// The container name must be lower case
CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

// Create the container if it does not exist.
container.createIfNotExists();

// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les objets blob de blocs sont les plus couramment utilisés et nous les utilisons donc dans ce guide de démarrage rapide. 

Pour charger un fichier dans un objet blob, obtenez une référence à l’objet blob dans le conteneur cible. Une fois que vous avez la référence d’objet blob, vous pouvez y charger des données à l’aide de [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Cette opération crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe.

L’exemple de code crée un fichier local à utiliser pour le chargement et le téléchargement, en stockant le fichier à charger comme **source** et le nom de l’objet blob dans **blob**. L'exemple suivant charge le fichier dans votre conteneur nommé **quickstartblobs**.

```java
//Getting the path to user's myDocuments folder
String myDocs = FileSystemView.getFileSystemView().getDefaultDirectory().getPath();

//Creating a file in the myDocuments folder
File source = new File(myDocs + File.separator + "results.txt");
try( Writer output = new BufferedWriter(new FileWriter(source)))
{
    System.out.println("Location of file: " + source.toString());

    output.write("Hello Azure!");
    output.close();
    
    System.out.println("File has been written");
}
catch(IOException x) 
{
    System.err.println(x);
}

//Getting blob reference
CloudBlockBlob blob = container.getBlockBlobReference("results.txt");

//Creating a FileInputStream as it is necessary for the upload
FileInputStream myFile = new FileInputStream(source);

//Creating blob and uploading file to it
blob.upload( myFile, source.length());

//Closing FileInputStream
myFile.close();
```

Il existe plusieurs méthodes de chargement que vous pouvez utiliser avec Stockage Blob. Par exemple, si vous avez un flux de mémoire, vous pouvez utiliser la méthode UploadFromStreamAsync au lieu de UploadFromFileAsync. 

Les objets blob de blocs peuvent être n’importe quel type de fichier texte ou binaire. Les objets blob de pages sont principalement utilisés pour les fichiers VHD utilisés pour stocker des machines virtuelles IaaS. Les objets blob d’ajout sont utilisés pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. La plupart des objets stockés dans Stockage Blob sont des objets blob de blocs.

## <a name="list-the-blobs-in-a-container"></a>Création d'une liste d'objets blob dans un conteneur

Vous pouvez obtenir la liste des fichiers du conteneur à l’aide de [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). Le code suivant récupère la liste des objets blob, puis effectue une itération sur ces derniers pour montrer les URI des objets blob rencontrés. Vous pouvez copier l’URI à partir de la fenêtre Commande et la coller dans un navigateur pour afficher le fichier.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

## <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez des objets blob sur votre disque local à l’aide de [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

Le code suivant télécharge l’objet blob chargé dans la section précédente et ajoute le suffixe « _DOWNLOADED » au nom de l’objet blob pour pouvoir voir les deux fichiers sur le disque local. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in your default directory.
blob.downloadToFile(myDocs + File.separator + "results_DOWNLOADED.txt");
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des objets blob chargés dans ce guide de démarrage rapide, vous pouvez supprimer le conteneur à l’aide de [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Cela supprime également les fichiers dans le conteneur.

```java
//Deletes container if it exists then deletes files created locally
container.deleteIfExists();
downloadedFile.deleteOnExit();
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et le stockage blob Azure avec Java. Pour en savoir plus sur l’utilisation de Stockage Blob, consultez le Guide pratique de Stockage Blob.

> [!div class="nextstepaction"]
> [Guide pratique des opérations Stockage Blob](storage-java-how-to-use-blob-storage.md)

Pour plus d’informations sur l’Explorateur Stockage et les objets blob, consultez [Gérer les ressources de stockage Blob Azure avec l’Explorateur Stockage](../../vs-azure-tools-storage-explorer-blobs.md).