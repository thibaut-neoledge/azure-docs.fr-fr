<properties 
	pageTitle="Utilisation du stockage d'objets blob (Ruby) | Microsoft Azure" 
	description="Découvrez comment utiliser le service BLOB Azure pour charger, télécharger, répertorier et supprimer du contenu d'objet blob. Les exemples sont écrits en Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="tomfitz"/>





#Utilisation du service BLOB à partir de Ruby

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service BLOB Azure. Les exemples sont écrits à l'aide de l'API Ruby.
Les scénarios traités incluent le **chargement, le téléchargement**, l'**énumération et la suppression** d'objets blob.
Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes](#next-steps) .

##Sommaire

* [Présentation du service BLOB](#what-is)
* [Concepts](#concepts)
* [Création d'un compte Azure Storage](#CreateAccount)
* [Création d'une application Ruby](#CreateRubyApp)
* [Configuration de votre application pour accéder au stockage](#ConfigAccessStorage)
* [Configuration d'une connexion Azure Storage](#SetupStorageConnection)
* [Procédure : Création d'un conteneur](#CreateContainer)
* [Procédure : Téléchargement d'un objet blob dans un conteneur](#UploadBlob)
* [Procédure : Création d'une liste d'objets blob dans un conteneur](#ListBlobs)
* [Procédure : Téléchargement d'objets blob](#DownloadBlobs)
* [Procédure : Suppression d'un objet blob](#DeleteBlob)
* [Étapes suivantes](#NextSteps)


[AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a id="CreateAccount"></a>Création d'un compte Azure Storage

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="CreateRubyApp"></a>Création d'une application Ruby

Créez une application Ruby. Pour obtenir des instructions, 
consultez le guide [Création d'une application Ruby sur Azure](/fr-FR/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="ConfigAccessStorage"></a>Configuration de votre application pour accéder au stockage

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de RubyGems pour obtenir le package

1. Utiliser une interface de ligne de commande comme **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).

2. Tapez " gem install azure " dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

	require "azure"

## <a id="SetupStorageConnection"></a>Configuration d'une connexion Azure Storage

Le module azure lit les variables d'environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS_KEY** 
pour obtenir les informations obligatoires pour se connecter à votre compte de stockage Azure. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte avant d'utiliser **Azure::BlobService** avec le code suivant :

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Pour obtenir ces valeurs :

1. Connectez-vous au [Portail de gestion Azure](https://manage.windowsazure.com/).
2. Accédez au compte de stockage que vous voulez utiliser.
3. Cliquez sur **GÉRER LES CLÉS** au bas du volet de navigation.
4. Dans la boîte de dialogue contextuelle, vous voyez le nom du compte de stockage et la clé d'accès primaire ou secondaire. Vous pouvez utiliser soit la clé d'accès primaire, soit la clé d'accès secondaire.

## <a id="CreateContainer"></a>Procédure : Création d'un conteneur

L'objet **Azure::BlobService** permet d'utiliser des conteneurs et des objets blob. Pour créer un conteneur, utilisez la méthode **create\_container()**.

L'exemple suivant crée un conteneur ou imprime l'erreur le cas échéant.

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Si vous souhaitez que les fichiers du conteneur soient publics, vous pouvez définir le niveau d'accès du conteneur. 

Vous pouvez modifier l'appel <strong>create\_container()</strong> afin de transmettre l'option **:public\_access\_level** :

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


Les valeurs valides pour l'option **:public\_access\_level** sont les suivantes :

* **objet blob :** spécifie un accès en lecture public complet pour les données de conteneur et d'objets blob. Les clients peuvent énumérer les objets blob du conteneur via une demande anonyme, mais ne peut pas énumérer les conteneurs dans le compte de stockage.

* **conteneur :** spécifie un accès en lecture public pour les objets blob. Les données blob de ce conteneur peuvent être lues via une demande anonyme, mais les données de conteneur ne seront pas disponibles. Les clients ne peuvent pas énumérer les objets blob du conteneur via une demande anonyme.

Vous pouvez également modifier le niveau d'accès public d'un conteneur en utilisant la méthode **set\_container\_acl()** afin de spécifier le niveau d'accès public.

Dans l'exemple suivant, le niveau d'accès public du **conteneur** est modifié :

	azure_blob_service.set_container_acl('test-container', "container")

## <a id="UploadBlob"></a>Procédure : Téléchargement d'un objet blob dans un conteneur

Pour télécharger du contenu dans un objet blob, utilisez la méthode **create\_block\_blob()** pour créer l'objet blob, utiliser un fichier ou une chaîne en tant que contenu de l'objet blob. 

Le code suivant télécharge le fichier **test.png** en tant que nouvel objet blob nommé " image-blob " dans le conteneur.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## <a id="ListBlobs"></a>Procédure : Création d'une liste d'objets blob dans un conteneur

Pour énumérer les conteneurs, utilisez la méthode **list_containers()**. 
Pour énumérer les objets blob à l'intérieur d'un conteneur, utilisez la méthode **list\_blobs()**. 

Cette action génère les URL de tous les objets blob de tous les conteneurs pour le compte.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## <a id="DownloadBlobs"></a>Procédure : Téléchargement d'objets blob

Pour télécharger des objets blob, utilisez la méthode **get\_blob()** afin d'extraire le contenu. 

L'exemple suivant illustre l'utilisation de **get\_blob()** pour télécharger le contenu d'" image-blob " et l'écrire dans un fichier local.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## <a id="DeleteBlob"></a>Procédure : Suppression d'un objet blob
Pour supprimer un objet blob, utilisez la méthode **delete\_blob()**. L'exemple suivant illustre la suppression d'un objet blob.

	azure_blob_service.delete_blob(container.name, "image-blob")

## <a id="NextSteps"></a>Étapes suivantes

Maintenant que vous avez appris les bases du stockage d'objets blob, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/fr-FR/library/windowsazure/gg433040.aspx)
-   Visitez le [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
-   Accédez au [référentiel du Kit de développement logiciel (SDK) Azure pour Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub.
<!--HONumber=42-->
