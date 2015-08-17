<properties 
	pageTitle="Utilisation du stockage d’objets blob à partir de Ruby | Microsoft Azure" 
	description="Découvrez comment utiliser le service BLOB Azure pour charger, répertorier, télécharger et supprimer du contenu d'objets blob. Les exemples sont écrits en Ruby." 
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
	ms.date="05/11/2015" 
	ms.author="tomfitz"/>


# Utilisation du stockage d'objets blob à partir de Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service BLOB Azure. Les exemples sont écrits à l'aide de l'API Ruby. Les scénarios traités incluent le **chargement, le téléchargement, l'énumération** et la **suppression** d'objets blob.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d'une application Ruby

Créez une application Ruby. Pour obtenir des instructions, consultez le guide [Création d'une application Ruby sur Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configuration de votre application pour accéder au stockage

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de RubyGems pour obtenir le package

1. Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Tapez « gem install azure » dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

	require "azure"

## Configuration d'une connexion Azure Storage

Le module Azure lit les variables d'environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY** pour obtenir les informations nécessaires à la connexion à votre compte Azure Storage. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte avant d'utiliser **Azure::BlobService** avec le code suivant :

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Pour obtenir ces valeurs :

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/).
2. Accédez au compte de stockage que vous voulez utiliser.
3. Cliquez sur **GÉRER LES CLÉS** au bas du volet de navigation.
4. Dans la boîte de dialogue contextuelle, vous voyez le nom du compte de stockage et la clé d'accès primaire ou secondaire. Vous pouvez utiliser soit la clé d'accès primaire, soit la clé d'accès secondaire.

## Procédure : création d’un conteneur

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

L'objet **Azure::BlobService** permet d'utiliser des conteneurs et des objets blob. Pour créer un conteneur, utilisez la méthode **create\_container()**.

L'exemple suivant crée un conteneur ou imprime l'erreur le cas échéant.

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Si vous souhaitez que les fichiers du conteneur soient publics, vous pouvez définir le niveau d'accès du conteneur.

Vous pouvez simplement modifier l’appel à <strong>create\_container()</strong> pour passer l’option **:public\_access\_level** :

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


Les valeurs valides pour l'option **:public\_access\_level** sont les suivantes :

* **blob** : spécifie un accès public total en lecture pour le conteneur et les données d’objets blob. Les clients peuvent énumérer les objets blob à l’intérieur du conteneur via une demande anonyme, mais ne peuvent pas énumérer les conteneurs dans le compte de stockage.

* **container** : spécifie un accès public en lecture pour les objets blob. les données d’objets blob à l’intérieur de ce conteneur peuvent être lues via une demande anonyme, mais les données du conteneur ne sont pas disponibles. Les clients ne peuvent pas énumérer les objets blob à l'intérieur du conteneur via une demande anonyme.

Vous pouvez également modifier le niveau d'accès public d'un conteneur en utilisant la méthode **set\_container\_acl()** afin de spécifier le niveau d'accès public.
 
Dans l'exemple suivant, le niveau d'accès public du **conteneur** est modifié :

	azure_blob_service.set_container_acl('test-container', "container")

## Procédure : chargement d’un objet blob dans un conteneur

Pour télécharger du contenu dans un objet blob, utilisez la méthode **create\_block\_blob()** pour créer l'objet blob, utiliser un fichier ou une chaîne en tant que contenu de l'objet blob.

Le code suivant télécharge le fichier **test.png** en tant que nouvel objet blob nommé « image-blob » dans le conteneur.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Procédure : création d’une liste d’objets blob dans un conteneur

Pour énumérer les conteneurs, utilisez la méthode **list\_containers()**. Pour énumérer les objets blob à l'intérieur d'un conteneur, utilisez la méthode **list\_blobs()**.

Cette action génère les URL de tous les objets blob de tous les conteneurs pour le compte.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Procédure : téléchargement d’objets blob

Pour télécharger des objets blob, utilisez la méthode **get\_blob()** afin d'extraire le contenu.

L'exemple suivant illustre l'utilisation de **get\_blob()** pour télécharger le contenu d'« image-blob » et l'écrire dans un fichier local.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Procédure : suppression d’un objet blob
Pour supprimer un objet blob, utilisez la méthode **delete\_blob()**. L'exemple suivant illustre la suppression d'un objet blob.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage des objets blob, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

- Consultez la référence MSDN suivante : [Azure Storage](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Consultez le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
- Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub.
 

<!---HONumber=August15_HO6-->