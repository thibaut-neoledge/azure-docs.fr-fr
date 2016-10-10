<properties
	pageTitle="Utilisation du stockage de tables Azure à partir de Ruby | Microsoft Azure"
	description="Stockez des données structurées dans le cloud à l’aide du stockage de tables Azure, un magasin de données NoSQL."
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
	ms.author="gusapost;robmcm"/>


# Utilisation du stockage de tables Azure à partir de Ruby

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de tables Azure. Les exemples sont écrits à l'aide de l'API Ruby. Les scénarios traités incluent la **création et la suppression d'une table, l'insertion et l'interrogation d'entités dans une table**.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d'une application Ruby

Pour obtenir des instructions sur la création d’une application Ruby, consultez la page [Application web Ruby on Rails sur une machine virtuelle Azure](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md).


## Configuration de votre application pour accéder au stockage

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services REST d’Azure Storage.

### Utilisation de RubyGems pour obtenir le package

1. Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Tapez **gem install azure** dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l’aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser Azure Storage :

	require "azure"

## Configurer une connexion Azure Storage

Le module Azure lit les variables d’environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY** pour obtenir les informations nécessaires à la connexion à votre compte Azure Storage. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte avant d'utiliser **Azure::TableService** grâce au code suivant :

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

## Création d’une table

L’objet **Azure::TableService** permet d’utiliser des tables et des entités. Pour créer une table, utilisez la méthode **create\_table()**. L'exemple suivant crée une table ou imprime l'erreur le cas échéant.

	azure_table_service = Azure::TableService.new
	begin
	  azure_table_service.create_table("testtable")
	rescue
	  puts $!
	end

## Ajout d’une entité à une table

Pour ajouter une entité, créez tout d'abord un objet de hachage qui définit les propriétés de votre entité. Notez que pour chaque entité, vous devez spécifier les clés **PartitionKey** et **RowKey**. Il s’agit d’identificateurs uniques de vos entités, dont les valeurs peuvent être interrogées bien plus rapidement que d’autres propriétés. Azure Storage utilise **PartitionKey** pour distribuer automatiquement les entités de la table sur plusieurs nœuds de stockage. Les entités partageant la même clé **PartitionKey** sont stockées sur le même nœud. **RowKey** est l'identifiant unique de l'entité dans sa partition.

	entity = { "content" => "test entity",
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## Mise à jour d'une entité

Plusieurs méthodes permettent de mettre à jour une entité existante :

* **update\_entity() :** met à jour une entité existante en la remplaçant.
* **merge\_entity() :** met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l’entité existante.
* **insert\_or\_merge\_entity() :** met à jour une entité existante en la remplaçant. En l'absence d'entité, une nouvelle entité est insérée.
* **insert\_or\_replace\_entity() :** met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l’entité existante. En l'absence d'entité, une nouvelle entité est insérée.

L'exemple suivant illustre la mise à jour d'une entité avec **update\_entity()** :

	entity = { "content" => "test entity with updated content",
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

Avec **update\_entity()** et **merge\_entity()**, si l’entité que vous mettez à jour n’existe pas, l’opération échoue. Si vous voulez stocker une entité, qu'elle existe déjà ou non, utilisez plutôt **insert\_or\_replace\_entity()** ou **insert\_or\_merge\_entity()**.

## Utilisation des groupes d'entités

Il est parfois intéressant de soumettre un lot d'opérations simultanément pour assurer un traitement atomique par le serveur. Pour cela, vous devez d'abord créer un objet **Lot**, puis utiliser la méthode **execute\_batch()** sur **TableService**. L'exemple ci-dessous présente l'envoi de deux entités avec RowKey 2 et 3 dans un lot. Notez qu'il ne s'applique qu'aux entités possédant le même élément PartitionKey.

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable",
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## Interrogation d’une entité

Pour interroger une entité dans une table, utilisez la méthode **get\_entity()** en transmettant le nom de table, **PartitionKey** et **RowKey**.

	result = azure_table_service.get_entity("testtable", "test-partition-key",
	  "1")

## Interrogation d’un ensemble d’entités

Pour interroger un ensemble d'entités dans une table, créez un objet de hachage de requête et utilisez la méthode **query\_entities()**. L'exemple ci-dessous présente l'obtention de toutes les identités avec le même élément **PartitionKey** :

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] Si l’ensemble de résultats est trop grand pour être renvoyé par une seule requête, un jeton de liaison est renvoyé. Vous pouvez l’utiliser pour récupérer les pages suivantes.

## Interrogation d'un sous-ensemble de propriétés d'entité

Vous pouvez utiliser une requête de table pour extraire uniquement quelques propriétés d’une entité. Cette technique, nommée « projection », réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. Utilisez la clause select et transmettez le nom des propriétés à soumettre au client.

	query = { :filter => "PartitionKey eq 'test-partition-key'",
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## Suppression d’une entité

Pour supprimer une entité, utilisez la méthode **delete\_entity()**. Vous devez transmettre le nom de la table qui contient l’entité, ainsi que les éléments PartitionKey et RowKey de l’entité.

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## Suppression d’une table

Pour supprimer une table, utilisez la méthode **delete\_table()** et transmettez le nom de la table que vous souhaitez supprimer.

		azure_table_service.delete_table("testtable")

## Étapes suivantes

Pour en savoir plus sur les tâches de stockage plus complexes, cliquez sur les liens ci-dessous :

- [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
- Référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub

<!---HONumber=AcomDC_0928_2016-->