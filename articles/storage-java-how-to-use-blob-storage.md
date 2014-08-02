<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="" solutions="" manager="" editor="" />

Utilisation du stockage d'objets blob à partir de Java
======================================================

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage d'objets blob Azure. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel (SDK) Azure pour Java](http://www.windowsazure.com/en-us/develop/java/). Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d'une source)** et la **suppression** d'objets blob. Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes](#NextSteps).

Sommaire
--------

-   [Présentation du stockage d'objets blob](#what-is)
-   [Concepts](#Concepts)
-   [Création d'un compte de stockage Azure](#CreateAccount)
-   [Création d'une application Java](#CreateApplication)
-   [Configuration de votre application pour accéder au stockage d'objets blob](#ConfigureStorage)
-   [Configuration d'une chaîne de connexion de stockage Azure](#ConnectionString)
-   [Création d'un conteneur](#CreateContainer)
-   [Téléchargement d'un objet blob dans un conteneur](#UploadBlob)
-   [Création d'une liste d'objets blob dans un conteneur](#ListBlobs)
-   [Téléchargement d'un objet blob](#DownloadBlob)
-   [Suppression d'un objet blob](#DeleteBlob)
-   [Suppression d'un conteneur d'objets blob](#DeleteContainer)
-   [Étapes suivantes](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Création d'un compte de stockage Azure
--------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Création d'une application Java
-------------------------------

Dans ce guide, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application Java en local, ou dans le code s'exécutant dans un rôle Web ou un rôle de travail dans Azure. Nous partons du principe que vous avez téléchargé et installé le Kit de développement Java (JDK) et suivi les instructions figurant sur la page [Téléchargement du Kit de développement logiciel (SDK) Azure pour Java](http://www.windowsazure.com/en-us/develop/java/) pour installer les bibliothèques Azure pour Java et le Kit de développement logiciel (SDK) Azure, et que vous avez créé un compte de stockage Azure dans votre abonnement Azure.

Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes. Vous devez seulement être en mesure de compiler un projet Java et de référencer des bibliothèques Azure pour Java.

Configuration de votre application pour accéder au stockage d'objets blob
-------------------------------------------------------------------------

Ajoutez l'instruction import suivante au début du fichier Java dans lequel vous voulez utiliser des API de stockage Azure pour accéder aux objets blob :

    // Ajoutez les imports suivants pour utiliser des API d'objets Blob
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

Configuration d'une chaîne de connexion de stockage Azure
---------------------------------------------------------

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker des points de terminaison et des informations d’identification permettant d'accéder aux services de gestion des données. Lors de l'exécution d'une application cliente, vous devez spécifier la chaîne de connexion de stockage au format suivant, en utilisant le nom de votre compte de stockage et la clé d'accès primaire pour le compte de stockage, répertoriés sur le portail de gestion pour les valeurs *AccountName* et *AccountKey*. Cet exemple vous montre comment déclarer un champ statique pour qu'il contienne une chaîne de connexion :

    // Définissez la chaîne de connexion à l'aide de vos valeurs
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Dans une application exécutée au sein d'un rôle dans Azure, cette chaîne peut être stockée dans le fichier de configuration de service ServiceConfiguration.cscfg et elle est accessible en appelant la méthode **RoleEnvironment.getConfigurationSettings**. Voici un exemple de code vous permettant d'extraire la chaîne de connexion à partir d'un élément **Setting** nommé *StorageConnectionString* dans le fichier de configuration de service :

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Création d'un conteneur
-----------------------

Un objet CloudBlobClient vous permet d'obtenir les objets de référence pour les conteneurs et objets blob. Le code suivant crée un objet **CloudBlobClient**.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client d'objet blob
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

Tous les objets blob résident dans un conteneur. Utilisez l'objet **CloudBlobClient** pour obtenir une référence pointant vers le conteneur à utiliser. Si le conteneur n'existe pas, vous pouvez le créer en utilisant la méthode **createIfNotExist** ; sinon, le conteneur existant est renvoyé. Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur.

    // Récupérez une référence pointant vers un conteneur
    // Le nom du conteneur doit être en minuscule
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Créez le conteneur s'il n'existe pas
    container.createIfNotExist();

Si vous souhaitez que les fichiers soient publics, vous pouvez définir le niveau d'accès du conteneur.

    // Créez un objet d'autorisations
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Ajoutez un accès public à l'objet d'autorisations
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Définissez les autorisations du conteneur
    container.uploadPermissions(containerPermissions);

Tous les utilisateurs d'Internet peuvent afficher les objets blob d'un conteneur public, mais l'accès public ne permet que la lecture.

Téléchargement d'un objet blob dans un conteneur
------------------------------------------------

Pour télécharger un fichier vers un objet blob, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d'objet blob. Dès lors que vous disposez d'une référence d'objet blob, vous pouvez télécharger un flux vers cet objet. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. Cet exemple de code illustre ce point en supposant que le conteneur existe.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client d'objet blob
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Récupérez une référence pointant vers un conteneur créé précédemment
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Créez ou remplacez l'objet blob "myimage.jpg" avec du contenu provenant d'un fichier local
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File("c:\myimages\myimage.jpg");
    blob.upload(new FileInputStream(source), source.length());

Création d'une liste d'objets blob dans un conteneur
----------------------------------------------------

Pour créer une liste d'objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur comme pour le téléchargement d'un objet blob. Vous pouvez utiliser la méthode **listBlobs** du conteneur avec une boucle « for ». Le code suivant génère l'URI de chaque objet blob d'un conteneur sur la console.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client d'objet blob
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Récupérez une référence pointant vers un conteneur créé précédemment
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Créez une boucle autour des objets blob du conteneur et sortez l'URI dans chacun d'eux
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }

Le service BLOB suit également le concept de répertoires dans des conteneurs. Vous pouvez donc organiser vos objets blob selon une structure proche de celle des dossiers.

Par exemple, vous pouvez avoir un conteneur nommé « photos », dans lequel vous pouvez télécharger des objets blob nommés « rootphoto1 », « 2010/photo1 », « 2010/photo2 » et « 2011/photo1 ». Vous créez ainsi virtuellement les répertoires « 2010 » et « 2011 » dans le conteneur « photos ». Lorsque vous appelez la méthode **listBlobs** pour le conteneur « photos », la collection renvoyée contient les objets **CloudBlobDirectory** et **CloudBlob** qui représentent les répertoires et objets blob contenus au niveau supérieur. Dans ce cas, les répertoires « 2010 » et « 2011 » et la photo « rootphoto1 » sont renvoyés. Vous pouvez utiliser l'opérateur **instanceof** pour différencier ces objets.

Vous pouvez également transmettre les paramètres à la méthode **listBlobs** avec le paramètre **useFlatBlobListing** défini sur true. Cela permet de renvoyer chaque objet blob, indépendamment du répertoire. Pour plus d'informations, consultez le fichier CloudBlobContainer.listBlobs dans la documentation Javadocs.

Téléchargement d'un objet blob
------------------------------

Pour télécharger des objets blob, procédez comme pour le chargement d'un objet blob afin d'obtenir une référence d'objet blob. Dans l'exemple de chargement, vous avez appelé la méthode upload sur l'objet blob. Dans l'exemple suivant, appelez la méthode download pour transférer les contenus d'objets blob vers un objet de flux tel que **FileOutputStream** pouvant être utilisé pour rendre l'objet blob persistant dans un fichier local.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client d'objet blob
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Récupérez une référence pointant vers un conteneur créé précédemment
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Pour chaque élément du conteneur
    for (ListBlobItem blobItem : container.listBlobs()) {
        // Si l'élément est un objet blob et non un répertoire virtuel
        if (blobItem instanceof CloudBlob) {
            // Téléchargez l'élément et enregistrez-le dans un fichier du même nom
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream(blob.getName()));
        }
    }

Suppression d'un objet blob
---------------------------

Pour supprimer un objet blob, obtenez une référence d'objet blob et appelez la méthode **delete**.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client d'objet blob
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Récupérez une référence pointant vers un conteneur créé précédemment
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Récupérez une référence pointant vers un objet blob nommé "myimage.jpg"
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Supprimez l'objet blob
    blob.delete();

Suppression d'un conteneur d'objets blob
----------------------------------------

Pour supprimer un conteneur d'objets blob, commencez par obtenir une référence de conteneur d'objets blob, puis appelez la méthode delete.

    // Récupérez le compte de stockage à partir de la chaîne de connexion
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Créez le client d'objet blob
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Récupérez une référence pointant vers un conteneur créé précédemment
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Supprimez le conteneur d'objets blob
    container.delete();

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du stockage d'objets blob, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure]
-   Accédez au blog de l'équipe Azure Storage : <http://blogs.msdn.com/b/windowsazurestorage/>

