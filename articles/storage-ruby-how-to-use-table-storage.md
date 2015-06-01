<properties 
	pageTitle="Utilisation du stockage de tables à partir de Ruby | Microsoft Azure" 
	description="Découvrez comment utiliser le service de stockage de tables dans Azure. Les exemples de code sont écrits à l'aide de l'API Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Utilisation du stockage de tables à partir de Ruby

[AZURE.INCLUDE [storage-selector-table-include](../includes/storage-selector-table-include.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du
service de Table Microsoft Azure. Les exemples sont écrits à l'aide de
l'API Ruby. Les scénarios traités incluent la **création et la suppression d'une table, ainsi que l'insertion et l'interrogation d'entités dans une table**.

[AZURE.INCLUDE [storage-table-concepts-include](../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Création d'une application Ruby

Créez une application Ruby. Pour obtenir des instructions, consultez le guide [Création d'une application Ruby sur Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configuration de votre application pour accéder au stockage

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de RubyGems pour obtenir le package

1. Utiliser une interface de ligne de commande comme **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).

2. Tapez **gem install azure** dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

	require "azure"

## Configuration d'une connexion Azure Storage

Le module Azure lit les variables d'environnement **AZURE_STORAGE_ACCOUNT** et **AZURE_STORAGE_ACCESS_KEY** pour obtenir les informations nécessaires à la connexion à votre compte de stockage Azure. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte avant d'utiliser **Azure::TableService** avec le code suivant :

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"

Pour obtenir ces valeurs :

1. Connectez-vous au [Portail de gestion Azure](https://manage.windowsazure.com/).

2. Accédez au compte de stockage que vous souhaitez utiliser.

3. Cliquez sur **MANAGE KEYS** au bas du volet de navigation.

4. Dans la boîte de dialogue contextuelle, vous voyez le nom du compte de stockage et la clé d'accès primaire ou secondaire. Vous pouvez utiliser soit la clé d'accès primaire, soit la clé d'accès secondaire.

## Création d'une table

L'objet **Azure::TableService** permet d'utiliser des tables et des entités. Pour créer une table, utilisez la méthode **create_table()**. L'exemple suivant crée une table ou imprime l'erreur le cas échéant.

	azure_table_service = Azure::TableService.new
	begin
	 azure_table_service.create_table("testtable")
	rescue
	 puts $!
	end

## Ajout d'une entité à une table

Pour ajouter une entité, créez tout d'abord un objet de hachage qui définit les propriétés de votre entité. Notez que pour chaque entité, vous devez spécifier une clé **PartitionKey** et une clé **RowKey**. Il s'agit d'identificateurs uniques de vos entités, dont les valeurs peuvent être interrogées bien plus rapidement que d'autres propriétés. Le service de stockage Azure utilise **PartitionKey** pour distribuer automatiquement les entités de la table sur plusieurs nœuds de stockage. Les entités partageant la même clé **PartitionKey** sont stockées sur le même nœud. **RowKey** est l'identifiant unique de l'entité dans sa partition. 

	entity = { "content" => "test entity", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## Procédure : Mise à jour d'une entité

Plusieurs méthodes permettent de mettre à jour une entité existante :

* **update_entity() :** met à jour une entité existante en la remplaçant.
* **merge_entity() :** met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l'entité existante.
* **insert_or_merge_entity() :** met à jour une entité existante en la remplaçant. En l'absence d'entité, une nouvelle entité est insérée.
* **insert_or_replace_entity() :** met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l'entité existante. En l'absence d'entité, une nouvelle entité est insérée.

L'exemple suivant illustre la mise à jour d'une entité avec **update_entity()** :

	entity = { "content" => "test entity with updated content", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

Avec **update_entity()** et **merge_entity()**, si l'entité mise à jour n'existe pas, l'opération échoue. Si vous voulez stocker une entité, qu'elle existe déjà ou non, utilisez plutôt **insert_or_replace_entity()** ou **insert_or_merge_entity()**.

## Procédure : Utilisation des groupes d'entités

Il est parfois intéressant de soumettre un lot d'opérations simultanément pour assurer un traitement atomique par le serveur. Pour cela, vous devez d'abord créer un objet **Batch**, puis utiliser la méthode **execute_batch()** sur **TableService**. L'exemple ci-dessous présente l'envoi de deux entités avec RowKey 2 et 3 dans un lot. Notez qu'il ne s'applique qu'aux entités possédant le même élément PartitionKey.

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable", 
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## Procédure : Interrogation d'une entité

Pour interroger une entité dans une table, utilisez la méthode **get_entity()** en passant le nom de table, **PartitionKey** et **RowKey**.

	result = azure_table_service.get_entity("testtable", "test-partition-key", 
	  "1")

## Procédure : Interrogation d'un ensemble d'entités

Pour interroger un ensemble d'entités dans une table, créez un objet de hachage de requête et utilisez la méthode **query_entities()**. L'exemple ci-dessous présente l'obtention de toutes les identités avec le même élément **PartitionKey** :

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

**Notez** que si l'ensemble de résultats est trop grand pour être renvoyé par une seule requête, un jeton de liaison sera renvoyé. Vous pourrez l'utiliser pour récupérer les pages suivantes.

## Procédure : Interrogation d'un sous-ensemble de propriétés d'entité

Vous pouvez utiliser une requête de table pour extraire uniquement quelques propriétés d'une entité. Cette technique, nommée " projection ", réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. Utilisez la clause select et transmettez le nom des propriétés à soumettre au client.

	query = { :filter => "PartitionKey eq 'test-partition-key'", 
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## Procédure : Suppression d'une entité

Pour supprimer une entité, utilisez la méthode **delete_entity()**. Vous devez transmettre le nom de la table qui contient l'entité, ainsi que les éléments PartitionKey et RowKey de l'entité.

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## Procédure : Suppression d'une table

Pour supprimer une table, utilisez la méthode **delete_table()** et passez le nom de la table que vous souhaitez supprimer.

		azure_table_service.delete_table("testtable")

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage des tables, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

- Consultez la référence MSDN suivante : [Azure Storage](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Visiter le [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
- Consulter le référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub

<!--HONumber=49-->