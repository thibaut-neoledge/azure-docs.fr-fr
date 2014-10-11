<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Utilisation du stockage d'objets blob à partir de Java

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage d'objets blob Microsoft Azure. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel (SDK) Azure Storage pour Java][]. Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d'une source)** et la **suppression** d'objets blob. Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes][].

Remarque : Un Kit de développement logiciel (SDK) est disponible pour les développeurs qui utilisent Azure Storage sur des appareils Android. Pour plus d'informations, consultez la page [Kit de développement logiciel (SDK) Azure Storage pour Android][].

## <a name="Contents"> </a>Sommaire

-   [Présentation du stockage d'objets blob][]
-   [Concepts][]
-   [Création d'un compte de stockage Azure][]
-   [Création d'une application Java][]
-   [Configuration de votre application pour accéder au stockage d'objets blob][]
-   [Configuration d'une chaîne de connexion de stockage Azure][]
-   [Création d'un conteneur][]
-   [Téléchargement d'un objet blob dans un conteneur][]
-   [Création d'une liste d'objets blob dans un conteneur][]
-   [Téléchargement d'un objet blob][]
-   [Suppression d'un objet blob][]
-   [Suppression d'un conteneur d'objets blob][]
-   [Étapes suivantes][]

[WACOM.INCLUDE [howto-blob-storage][]]

## <span id="CreateAccount"></span></a>Création d'un compte de stockage Azure

[WACOM.INCLUDE [create-storage-account][]]

## <a name="CreateApplication"> </a>Création d'une application Java

Dans ce guide, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application Java en local, ou dans le code s'exécutant dans un rôle Web ou un rôle de travail dans Azure.

Pour ce faire, vous devez installer Java Development Kit (JDK) et créer un compte de stockage Azure dans votre abonnement Azure. Vous devez ensuite vérifier que votre système de développement répond à la configuration minimale requise et aux dépendances répertoriées dans le référentiel [Kit de développement logiciel (SDK) Azure Storage pour Java][] sur GitHub. Si votre système répond à ces exigences, vous pouvez suivre les instructions de téléchargement et d'installation des bibliothèques Azure Storage pour Java correspondant à votre système à partir de ce référentiel. Une fois ces opérations accomplies, vous pouvez créer une application Java en utilisant les exemples de cet article.

## <a name="ConfigureStorage"> </a>Configuration de votre application pour accéder au stockage d'objets blob

Ajoutez les instructions import suivantes au début du fichier Java dans lequel vous voulez utiliser des API de stockage Azure pour accéder aux objets blob :

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## <a name="ConnectionString"> </a>Configuration d'une chaîne de connexion de stockage Azure

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker
des points de terminaison et des informations d’identification permettant d'accéder aux services de gestion des données. Lors de l'exécution d'une application cliente, vous devez spécifier la chaîne de connexion de stockage au format suivant, en utilisant le nom de votre compte de stockage et la clé d'accès primaire pour le compte de stockage, répertoriés sur le portail de gestion pour les valeurs *AccountName* et *AccountKey*. Cet exemple vous montre comment déclarer un champ statique pour qu'il contienne une chaîne de connexion :

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Dans une application exécutée au sein d'un rôle dans Microsoft Azure, cette chaîne peut être stockée dans le fichier de configuration de service *ServiceConfiguration.cscfg* et elle est accessible en appelant la méthode **RoleEnvironment.getConfigurationSettings**. Voici un exemple de code vous permettant d'extraire la chaîne de connexion à partir d'un élément **Setting** nommé *StorageConnectionString* dans le fichier de configuration de service :

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Les exemples suivants partent du principe que vous avez utilisé une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## <a name="CreateContainer"> </a> Création d'un conteneur

Un objet CloudBlobClient vous permet d'obtenir les objets de référence pour les conteneurs et objets blob. Le code suivant crée un objet **CloudBlobClient**. Remarque : d'autres méthodes sont disponibles pour créer des objets **CloudStorageAccount**. Pour plus d'informations, consultez la rubrique **CloudStorageAccount** du document [Référence du Kit de développement logiciel (SDK) du client Azure Storage][].)

Tous les objets blob résident dans un conteneur. Utilisez l'objet **CloudBlobClient** pour obtenir une référence pointant vers le conteneur à utiliser. Si le conteneur n'existe pas, vous pouvez le créer en utilisant la méthode **createIfNotExists** ; sinon, le conteneur existant est renvoyé. Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Get a reference to a container.
       // The container name must be lower case
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Create the container if it does not exist.
        container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

### Facultatif : Configuration d'un conteneur pour l'accès public

Par défaut, les autorisations d'un conteneur sont configurées pour l'accès privé, mais vous pouvez facilement les configurer pour octroyer un accès public en lecture seule à tous les utilisateurs d'Internet :

    // Create a permissions object.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container.
    container.uploadPermissions(containerPermissions);

## <a name="UploadBlob"> </a> Téléchargement d'un objet blob dans un conteneur

Pour télécharger un fichier vers un objet blob, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d'objet blob. Dès lors que vous disposez d'une référence d'objet blob, vous pouvez télécharger un flux vers cet objet. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. Cet exemple de code illustre ce point en supposant que le conteneur existe.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
        // Define the path to a local file.
        final String filePath = "C:\\myimages\\myimage.jpg";

        // Create or overwrite the "myimage.jpg" blob with contents from a local file.
        CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
        File source = new File(filePath);
        blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="ListBlobs"> </a> Création d'une liste d'objets blob dans un conteneur

Pour créer une liste d'objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur comme pour le téléchargement d'un objet blob. Vous pouvez utiliser la méthode **listBlobs** du conteneur avec une boucle **for**. Le code suivant génère l'URI de chaque objet blob d'un conteneur sur la console.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
        // Loop over blobs within the container and output the URI to each of them.
        for (ListBlobItem blobItem : container.listBlobs()) {
           System.out.println(blobItem.getUri());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Le service BLOB suit également le concept de répertoires dans des conteneurs. Vous pouvez donc organiser vos objets blob selon une structure proche de celle des dossiers.

Par exemple, vous pouvez avoir un conteneur nommé « photos », dans lequel vous pouvez télécharger des objets blob nommés « rootphoto1 », « 2010/photo1 », « 2010/photo2 » et « 2011/photo1 ». Vous créez ainsi virtuellement les répertoires « 2010 » et « 2011 » dans le conteneur « photos ». Lorsque vous appelez la méthode **listBlobs** pour le conteneur « photos », la collection renvoyée contient les objets **CloudBlobDirectory** et **CloudBlob** qui représentent les répertoires et objets blob contenus au niveau supérieur. Dans ce cas, les répertoires « 2010 » et « 2011 » et la photo « rootphoto1 » sont renvoyés. Vous pouvez utiliser l'opérateur **instanceof** pour différencier ces objets.

Vous pouvez également transmettre les paramètres à la méthode **listBlobs** avec le
paramètre **useFlatBlobListing** défini sur true. Cela permet de renvoyer
chaque objet blob, indépendamment du répertoire. Pour plus
d'informations, consultez la rubrique **CloudBlobContainer.listBlobs** du document [Référence du Kit de développement logiciel (SDK) du client Azure Storage][].

## <a name="DownloadBlob"> </a> Téléchargement d'un objet blob

Pour télécharger des objets blob, procédez comme pour le chargement d'un objet blob afin d'obtenir une référence d'objet blob. Dans l'exemple de chargement, vous avez appelé la méthode upload sur l'objet blob. Dans l'exemple suivant, appelez la méthode download pour transférer les contenus d'objets blob vers un objet de flux tel que **FileOutputStream** pouvant être utilisé pour rendre l'objet blob persistant dans un fichier local.

    try
    {
        // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Loop through each blob item in the container.
       for (ListBlobItem blobItem : container.listBlobs()) {
           // If the item is a blob, not a virtual directory.
           if (blobItem instanceof CloudBlob) {
               // Download the item and save it to a file with the same name.
                CloudBlob blob = (CloudBlob) blobItem;
                blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteBlob"> </a> Suppression d'un objet blob

Pour supprimer un objet blob, obtenez une référence d'objet blob et appelez la méthode **deleteIfExists**.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Retrieve reference to a blob named "myimage.jpg".
       CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

       // Delete the blob.
       blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteContainer"> </a> Suppression d'un conteneur d'objets blob

Pour supprimer un conteneur d'objets blob, commencez par obtenir une référence de conteneur d'objets blob, puis
appelez la méthode **deleteIfExists**.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Delete the blob container.
       container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Étapes suivantes

Maintenant que vous avez appris les bases du stockage d'objets blob, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   [Kit de développement logiciel (SDK) Azure Storage pour Java][]
-   [Référence du Kit de développement logiciel (SDK) du client Azure Storage][]
-   [API REST d'Azure Storage][]
-   [Blog de l'équipe Azure Storage][]

  [Kit de développement logiciel (SDK) Azure Storage pour Java]: https://github.com/azure/azure-storage-java
  [Étapes suivantes]: #NextSteps
  [Kit de développement logiciel (SDK) Azure Storage pour Android]: https://github.com/azure/azure-storage-android
  [Présentation du stockage d'objets blob]: #what-is
  [Concepts]: #Concepts
  [Création d'un compte de stockage Azure]: #CreateAccount
  [Création d'une application Java]: #CreateApplication
  [Configuration de votre application pour accéder au stockage d'objets blob]: #ConfigureStorage
  [Configuration d'une chaîne de connexion de stockage Azure]: #ConnectionString
  [Création d'un conteneur]: #CreateContainer
  [Téléchargement d'un objet blob dans un conteneur]: #UploadBlob
  [Création d'une liste d'objets blob dans un conteneur]: #ListBlobs
  [Téléchargement d'un objet blob]: #DownloadBlob
  [Suppression d'un objet blob]: #DeleteBlob
  [Suppression d'un conteneur d'objets blob]: #DeleteContainer
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Référence du Kit de développement logiciel (SDK) du client Azure Storage]: http://dl.windowsazure.com/storage/javadoc/
  [API REST d'Azure Storage]: http://msdn.microsoft.com/en-us/library/azure/gg433040.aspx
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
