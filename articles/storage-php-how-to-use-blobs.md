<properties title="How to use blob storage (PHP) - Azure feature guide" pageTitle="How to use blob storage (PHP) | Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs. Code samples are written in PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="robmcm" manager="wpickett" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Utilisation du service Blob à partir de PHP

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service Blob Azure. Les exemples sont écrits en PHP et utilisent le [Kit de développement logiciel (SDK) Azure pour PHP][]. Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d'une source)** et la **suppression** d'objets blob. Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes][].

## Sommaire

-   [Présentation du stockage d'objets blob][]
-   [Concepts][]
-   [Création d'un compte de stockage Azure][]
-   [Création d'une application PHP][]
-   [Configuration de votre application pour accéder au service Blob][]
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

## <span id="CreateApplication"></span></a>Création d'une application PHP

La référence de classes dans le Kit de développement logiciel (SDK) Azure pour PHP constitue la seule exigence pour créer une application PHP qui accède au service Blob Azure dans votre code. Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes.

Dans ce guide, vous allez utiliser des fonctionnalités de service qui peuvent être appelées dans une application PHP localement, ou dans du code qui s'exécute dans un rôle web, un rôle de travail ou un site web Azure.

## <span id="GetClientLibrary"></span></a>Obtention des bibliothèques clientes Azure

[WACOM.INCLUDE [get-client-libraries][]]

## <span id="ConfigureStorage"></span></a>Configuration de votre application pour accéder au service Blob

Pour utiliser des API de service Blob Azure, vous devez procéder comme suit :

1.  référencer le fichier de chargeur automatique à l'aide de l'instruction [require\_once][] ; et
2.  référencer toute classe que vous êtes susceptible d'utiliser.

L'exemple suivant montre comment inclure le fichier du chargeur automatique et référencer la classe **ServicesBuilder**.

> [WACOM.NOTE]
> Cet exemple et d'autres exemples de cet article partent du principe que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou en tant que package PEAR, vous devez référencer le fichier de chargeur automatique `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

Dans les exemples ci-dessous, l'instruction `require_once` s'affichera toujours, mais seules les classes nécessaires aux besoins de l'exemple à exécuter sont référencées.

## <span id="ConnectionString"></span></a>Configuration d'une chaîne de connexion de stockage Azure

Pour instancier un client de service Blob Azure, vous devez disposer au préalable d'une chaîne de connexion valide. Le format de la chaîne de connexion du service Blob est le suivant :

Pour accéder à un service en ligne :

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Pour accéder au stockage de l'émulateur :

    UseDevelopmentStorage=true

Pour créer un client de service Azure, vous devez utiliser la classe **ServicesBuilder**. Vous pouvez :

-   lui passer directement la chaîne de connexion ; ou
-   utiliser **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :

    -   par défaut une source externe est prise en charge : variables d'environnement ;
    -   de nouvelles sources peuvent être ajoutées via une extension de la classe **ConnectionStringSource**.

Dans les exemples ci-dessous, la chaîne de connexion est passée directement.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <span id="CreateContainer"></span></a> Création d'un conteneur

Un objet **BlobRestProxy** vous permet de créer un conteneur d'objets blob avec la méthode **createContainer**. Lors de la création d'un conteneur, vous pouvez définir des options sur ce dernier, mais vous n'y êtes pas obligé. L'exemple ci-dessous montre comment définir l'ACL et les métadonnées du conteneur.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Blob\Models\CreateContainerOptions;
    use WindowsAzure\Blob\Models\PublicAccessType;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    // OPTIONAL: Set public access policy and metadata.
    // Create container options object.
    $createContainerOptions = new CreateContainerOptions(); 

    // Set public access policy. Possible values are 
    // PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
    // CONTAINER_AND_BLOBS:     
    // Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous 
    // request, but cannot enumerate containers within the storage account.
    //
    // BLOBS_ONLY:
    // Specifies public read access for blobs. Blob data within this 
    // container can be read via anonymous request, but container data is not 
    // available. proxys cannot enumerate blobs within the container via 
    // anonymous request.
    // If this value is not specified in the request, container data is 
    // private to the account owner.
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

L'appel de **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** rend le conteneur et les données d'objets blob accessibles via des demandes anonymes. L'appel de **setPublicAccess(PublicAccessType::BLOBS\_ONLY)** ne rend que les données d'objets blob accessibles via des demandes anonymes. Pour plus d'informations sur les ACL de conteneur, consultez la page [Set Container ACL (API REST)][].

Pour plus d'informations sur les codes d'erreur des services d'objets blob, consultez la page [Codes d'erreur de service BLOB][].

## <span id="UploadBlob"></span></a> Téléchargement d'un objet blob dans un conteneur

Pour télécharger un fichier en tant qu'objet blob, utilisez la méthode **BlobRestProxy-\>createBlockBlob**. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. L'exemple de code ci-dessous part du principe que le conteneur a déjà été créé et utilise [fopen][] pour ouvrir le fichier en tant que flux.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Notez que l'exemple ci-dessus télécharge un objet blob en tant que flux. Toutefois, un objet blob peut également être téléchargé en tant que chaîne à l'aide de la fonction [file\_get\_contents][] par exemple. Pour ce faire, remplacez `$content = fopen("c:\myfile.txt", "r");` dans l'exemple ci-dessus par `$content = file_get_contents("c:\myfile.txt");`.

## <span id="ListBlobs"></span></a> Création d'une liste d'objets blob dans un conteneur

Pour répertorier les objets blob dans un conteneur, utilisez la méthode **BlobRestProxy-\>listBlobs** avec une boucle **foreach** pour lire en boucle le résultat. Le code suivant génère en sortie le nom de chaque objet blob dans un conteneur et son URI dans le navigateur.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // List blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();
        
        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DownloadBlob"></span></a> Téléchargement d'un objet blob

Pour télécharger un objet blob, appelez la méthode **BlobRestProxy-\>getBlob**, puis la méthode **getContentStream** sur l'objet **GetBlobResult** résultant.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Notez que l'exemple ci-dessus télécharge un objet blob en tant que ressource de flux (comportement par défaut). Toutefois, vous pouvez utiliser la fonction [stream\_get\_contents][] pour convertir le flux renvoyé en chaîne.

## <span id="DeleteBlob"></span></a> Suppression d'un objet blob

Pour supprimer un objet blob, passez le nom du conteneur et le nom de l'objet blob à **BlobRestProxy-\>deleteBlob**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteContainer"></span></a> Suppression d'un conteneur d'objets blob

Enfin, pour supprimer un conteneur d'objets blob, passez le nom du conteneur à **BlobRestProxy-\>deleteContainer**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="NextSteps"></span></a>Étapes suivantes

Maintenant que vous avez appris les principes de base du service Blob Azure, suivez ces liens pour découvrir des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][]
-   Accédez au blog de l'équipe Azure Storage : <http://blogs.msdn.com/b/windowsazurestorage/>
-   Consultez l'exemple d'objet blob de blocs PHP à l'adresse <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
-   Consultez l'exemple d'objet blob de pages PHP à l'adresse <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

  [Kit de développement logiciel (SDK) Azure pour PHP]: http://go.microsoft.com/fwlink/?LinkID=252473
  [Étapes suivantes]: #NextSteps
  [Présentation du stockage d'objets blob]: #what-is
  [Concepts]: #concepts
  [Création d'un compte de stockage Azure]: #CreateAccount
  [Création d'une application PHP]: #CreateApplication
  [Configuration de votre application pour accéder au service Blob]: #ConfigureStorage
  [Configuration d'une chaîne de connexion de stockage Azure]: #ConnectionString
  [Création d'un conteneur]: #CreateContainer
  [Téléchargement d'un objet blob dans un conteneur]: #UploadBlob
  [Création d'une liste d'objets blob dans un conteneur]: #ListBlobs
  [Téléchargement d'un objet blob]: #DownloadBlob
  [Suppression d'un objet blob]: #DeleteBlob
  [Suppression d'un conteneur d'objets blob]: #DeleteContainer
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [get-client-libraries]: ../includes/get-client-libraries.md
  [require\_once]: http://php.net/require_once
  [Set Container ACL (API REST)]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179391.aspx
  [Codes d'erreur de service BLOB]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
  [fopen]: http://www.php.net/fopen
  [file\_get\_contents]: http://php.net/file_get_contents
  [stream\_get\_contents]: http://www.php.net/stream_get_contents
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
