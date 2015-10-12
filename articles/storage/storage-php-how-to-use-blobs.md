<properties
	pageTitle="Utilisation du stockage d’objets blob à partir de PHP | Microsoft Azure"
	description="Découvrez comment utiliser le service blob Azure pour charger, répertorier, télécharger et supprimer des objets blob. Les exemples de code sont écrits en PHP."
	documentationCenter="php"
	services="storage"
	authors="tfitzmac"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="tomfitz"/>

# Utilisation du stockage d’objets blob à partir de PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service blob Azure. Les exemples sont écrits en PHP et utilisent le [Kit de développement logiciel (SDK) Azure pour PHP][download]. Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d'une source)** et la **suppression** d'objets blob. Pour plus d’informations sur les objets blob, consultez la section [Étapes suivantes](#next-steps).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d'une application PHP

Le référencement de classes issues du Kit de développement logiciel (SDK) Azure pour PHP dans votre code constitue la seule exigence pour créer une application PHP qui accède au service blob Azure. Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes.

Dans ce guide, vous allez utiliser des fonctionnalités de service qui peuvent être appelées dans une application PHP localement ou dans le code d’un rôle web, d’un rôle de travail ou d’un site web Azure.

## Obtention des bibliothèques clientes Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Configuration de votre application pour accéder au service blob

Pour utiliser des API de service blob Azure, vous devez procéder comme suit :

1. référencer le fichier de chargeur automatique à l'aide de l'instruction [require\_once][require_once] ; et
2. référencer toute classe que vous êtes susceptible d'utiliser.

L'exemple suivant montre comment inclure le fichier du chargeur automatique et référencer la classe **ServicesBuilder**.

> [AZURE.NOTE]Cet exemple et d'autres exemples de cet article partent du principe que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou en tant que package PEAR, vous devez référencer le fichier de chargeur automatique `WindowsAzure.php`.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Dans les exemples ci-dessous, l’instruction `require_once` s’affiche toujours, mais seules les classes nécessaires à l’exécution de l’exemple sont référencées.

## Configuration d’une connexion de stockage Azure

Pour instancier un client de service blob Azure, vous devez disposer d’une chaîne de connexion valide. Le format de la chaîne de connexion du service Blob est le suivant :

Pour accéder à un service en ligne :

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Pour accéder à l’émulateur de stockage :

	UseDevelopmentStorage=true


Pour créer un client de service Azure, vous devez utiliser la classe **ServicesBuilder**. Vous pouvez :

* lui transmettre directement la chaîne de connexion ; ou
* utiliser **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
	* Par défaut, il prend en charge une source externe : les variables d’environnement.
	* Vous pouvez ajouter de nouvelles sources via une extension de la classe **ConnectionStringSource**.

Dans les exemples ci-dessous, la chaîne de connexion est passée directement.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## Création d’un conteneur

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Un objet **BlobRestProxy** vous permet de créer un conteneur d'objets blob avec la méthode **createContainer**. Lors de la création d'un conteneur, vous pouvez définir des options sur ce dernier, mais vous n'y êtes pas obligé. (L’exemple ci-dessous montre comment définir la liste de contrôle d’accès (ACL) et les métadonnées du conteneur.)

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

	// Set container metadata.
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");

	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

L'appel de **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** rend le conteneur et les données d'objets blob accessibles via des demandes anonymes. L'appel de **setPublicAccess(PublicAccessType::BLOBS\_ONLY)** ne rend que les données d'objets blob accessibles via des demandes anonymes. Pour plus d’informations sur les ACL de conteneur, consultez la page [Définition d’ACL de conteneur (API REST)][container-acl].

Pour plus d'informations sur les codes d'erreur des services d'objets blob, consultez la page [Codes d'erreur de service BLOB][error-codes].

## Charger un objet blob dans un conteneur

Pour télécharger un fichier en tant qu'objet blob, utilisez la méthode **BlobRestProxy->createBlockBlob**. Si l’objet blob n’existe pas, cette opération le crée. S’il existe, il est remplacé. L'exemple de code ci-dessous part du principe que le conteneur a déjà été créé et utilise [fopen][fopen] pour ouvrir le fichier en tant que flux.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";

	try	{
		//Upload blob
		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Notez que l’exemple ci-dessus télécharge un objet blob en tant que flux. Toutefois, un objet blob peut également être téléchargé en tant que chaîne à l'aide de la fonction [file\_get\_contents][file_get_contents] par exemple. Pour ce faire, utilisez l’exemple précédent et remplacez `$content = fopen("c:\myfile.txt", "r");` par `$content = file_get_contents("c:\myfile.txt");`.

## Création d'une liste d'objets blob dans un conteneur

Pour répertorier les objets blob dans un conteneur, utilisez la méthode **BlobRestProxy->listBlobs** avec une boucle **foreach** pour lire en boucle le résultat. Le code suivant affiche le nom de chaque objet blob dans un conteneur et son URI dans le navigateur.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	try	{
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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


## Téléchargement d'un objet blob

Pour télécharger un objet blob, appelez la méthode **BlobRestProxy->getBlob**, puis la méthode **getContentStream** sur l'objet **GetBlobResult** résultant.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	try	{
		// Get blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob");
		fpassthru($blob->getContentStream());
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Notez que l'exemple ci-dessus télécharge un objet blob en tant que ressource de flux (comportement par défaut). Toutefois, vous pouvez utiliser la fonction [stream\_get\_contents][stream-get-contents] pour convertir le flux renvoyé en chaîne.

## Supprimer un blob.

Pour supprimer un objet blob, passez le nom du conteneur et le nom de l'objet blob à **BlobRestProxy->deleteBlob**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	try	{
		// Delete container.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Suppression d'un conteneur d'objets blob

Enfin, pour supprimer un conteneur d'objets blob, passez le nom du conteneur à **BlobRestProxy->deleteContainer**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	try	{
		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Étapes suivantes

Maintenant que vous connaissez les principes de base du service blob Azure, suivez ces liens pour apprendre à exécuter les tâches de stockage plus complexes.

- Consultez la référence MSDN suivante : [Azure Storage](http://msdn.microsoft.com/library/azure/gg433040.aspx).
- Consultez le [blog de l’équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).
- Consultez l'exemple d'objet blob de blocs PHP à l'adresse <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- Consultez l'exemple d'objet blob de pages PHP à l'adresse <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

Pour plus d’informations, consultez également le [Centre pour développeurs PHP](/develop/php/).


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Storing and accessing data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

<!---HONumber=Oct15_HO1-->