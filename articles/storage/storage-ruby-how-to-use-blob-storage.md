<properties
	pageTitle="Utilisation du stockage d’objets blob à partir de Ruby | Microsoft Azure"
	description="Stockez des données non structurées dans le cloud avec Azure Blob Storage (stockage d’objets)."
	services="storage"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
    ms.date="08/11/2016"
	ms.author="robmcm"/>


# Utilisation du stockage d'objets blob à partir de Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## Vue d'ensemble

Le stockage d’objets blob Azure est un service qui stocke des données non structurées dans le cloud en tant qu’objets/blobs. Ce service peut stocker tout type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d’installation d’application. Le stockage d’objets blob est également appelé Blob Storage.

Ce guide décrit le déroulement de scénarios courants dans le cadre de l’utilisation de Blob Storage. Les exemples sont écrits à l'aide de l'API Ruby. Les scénarios traités incluent le **chargement, le téléchargement, l'énumération** et la **suppression** d'objets blob.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d'une application Ruby

Créez une application Ruby. Pour des instructions, consultez [Application web Ruby on Rails sur une machine virtuelle Azure](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md).

## Configuration de votre application pour accéder au stockage

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de RubyGems pour obtenir le package

1. Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Tapez « gem install azure » dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

	require "azure"

## Configuration d'une connexion Azure Storage

Le module Azure lit les variables d'environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY** pour obtenir les informations nécessaires à la connexion à votre compte Azure Storage. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte avant d'utiliser **Azure::Blob::BlobService** avec le code suivant :

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Pour obtenir ces valeurs à partir d’un compte de stockage classique ou Resource Manager sur le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez au compte de stockage que vous souhaitez utiliser.
3. Dans le panneau Paramètres à droite, cliquez sur **Clés d’accès**.
4. Dans le panneau Clés d’accès qui apparaît, la clé d’accès 1 et la clé d’accès 2 sont affichées. Vous pouvez utiliser les deux.
5. Cliquez sur l'icône de copie pour copier la clé dans le Presse-papiers.

Pour obtenir ces valeurs à partir d’un compte de stockage classique sur le portail Azure Classic :

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Accédez au compte de stockage que vous souhaitez utiliser.
3. Cliquez sur **GÉRER LES CLÉS D’ACCÈS** en bas du volet de navigation.
4. Dans la boîte de dialogue contextuelle, vous voyez le nom du compte de stockage et la clé d'accès primaire ou secondaire. Vous pouvez utiliser soit la clé d'accès primaire, soit la clé d'accès secondaire.
5. Cliquez sur l'icône de copie pour copier la clé dans le Presse-papiers.

## Créer un conteneur

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

L'objet **Azure::Blob::BlobService** permet d'utiliser des conteneurs et des objets blob. Pour créer un conteneur, utilisez la méthode **create\_container()**.

L’exemple de code suivant crée un conteneur ou imprime l'erreur le cas échéant.

	azure_blob_service = Azure::Blob::BlobService.new
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

* **container** : spécifie un accès public en lecture pour les objets blob. Les données d'objets blob à l'intérieur de ce conteneur peuvent être lues via une demande anonyme, mais les données du conteneur ne sont pas disponibles. Les clients ne peuvent pas énumérer les objets blob à l'intérieur du conteneur via une demande anonyme.

Vous pouvez également modifier le niveau d'accès public d'un conteneur en utilisant la méthode **set\_container\_acl()** afin de spécifier le niveau d'accès public.

Dans l'exemple de code suivant, le niveau d'accès public du **conteneur** est modifié :

	azure_blob_service.set_container_acl('test-container', "container")

## Charger un objet blob dans un conteneur

Pour télécharger du contenu dans un objet blob, utilisez la méthode **create\_block\_blob()** pour créer l'objet blob, utiliser un fichier ou une chaîne en tant que contenu de l'objet blob.

Le code suivant télécharge le fichier **test.png** en tant que nouvel objet blob nommé « image-blob » dans le conteneur.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Création d'une liste d'objets blob dans un conteneur

Pour énumérer les conteneurs, utilisez la méthode **list\_containers()**. Pour énumérer les objets blob à l'intérieur d'un conteneur, utilisez la méthode **list\_blobs()**.

Cette action génère les URL de tous les objets blob de tous les conteneurs pour le compte.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Télécharger des objets blob

Pour télécharger des objets blob, utilisez la méthode **get\_blob()** afin d'extraire le contenu.

L'exemple de code suivant illustre l'utilisation de **get\_blob()** pour télécharger le contenu d'« image-blob » et l'écrire dans un fichier local.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Suppression d'un objet blob
Pour supprimer un objet blob, utilisez la méthode **delete\_blob()**. L'exemple de code suivant illustre la suppression d'un objet blob.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Étapes suivantes

Pour en savoir plus sur les tâches de stockage plus complexes, cliquez sur les liens ci-dessous :

- [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
- Référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub
- [Transfert de données avec l'utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0817_2016-->