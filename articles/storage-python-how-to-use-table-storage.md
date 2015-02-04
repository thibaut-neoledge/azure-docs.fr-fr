<properties urlDisplayName="Table Service" pageTitle="Utilisation du stockage de tables (Python) | Microsoft Azure" metaKeywords="Azure table Python, creating table Azure, deleting table Azure, inserting table Azure, querying table Azure" description="Découvrez comment utiliser le service de Table de Python pour créer, supprimer, insérer et interroger une table." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Table Storage Service from Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="robmcm" />





# Utilisation du service de stockage de tables à partir de Python 
Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage de tables Azure. Les exemples sont écrits en utilisant l'API Python. Les scénarios traités incluent la**création et la suppression d'une table, l'insertion et l'interrogation d'entités dans une table**. Pour plus d'informations sur les tables, consultez la section [Étapes suivantes][].

## Sommaire

[Présentation du service de Table][]   
 [Concepts][]   
 [Création d'un compte Azure Storage][]   
 [Procédure : Création d'une table][]   
 [Procédure : Ajout d'une entité à une table][]   
 [Procédure : Mise à jour d'une entité][]   
 [Procédure : Modification d'un groupe d'entités][]   
 [Procédure : Interrogation d'une entité][]   
 [Procédure : Interrogation d'un ensemble d'entités][]   
 [Procédure : Interrogation d'un sous-ensemble de propriétés d'entité][]   
 [Procédure : Suppression d'une entité][]   
 [Procédure : Suppression d'une table][]   
 [Étapes suivantes][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"> </a>Création d'un compte de stockage Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**Remarque :** si vous devez installer Python ou les bibliothèques clientes, consultez le [Guide d'installation de Python](../python-how-to-install/).


## <a name="create-table"> </a>Création d'une table

L'objet **TableService** permet d'utiliser les services de Table. Le code suivant permet de créer un objet  **TableService**. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

	from azure.storage import TableService, Entity

Le code suivant permet de créer un objet  **TableService** en utilisant le nom et la clé du compte de stockage.  Remplacez " myaccount " et " mykey " par le compte et la clé réels.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## <a name="add-entity"> </a>Ajout d'une entité à une table

Pour ajouter une entité, commencez par créer un dictionnaire définissant les noms et valeurs des propriétés de votre entité. Notez que pour chaque entité, vous devez spécifier les clés **PartitionKey** et **RowKey**. Elles représentent les identificateurs uniques de vos entités, dont les valeurs peuvent être interrogées
bien plus rapidement que d'autres propriétés. Le système utilise **PartitionKey** pour distribuer automatiquement les entités de la table sur plusieurs nœuds de stockage. Les entités partageant la même clé **PartitionKey** sont stockées sur le même nœud. Exemple **RowKey** est l'identifiant unique de l'entité dans la partition dont il fait partie.

Pour ajouter une entité à votre table, transmettez un objet dictionnaire à la méthode **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

Vous pouvez également transmettre une instance de la classe **Entity** à la méthode **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## <a name="update-entity"> </a>Mise à jour d'une entité

Ce code montre comment remplacer l'ancienne version d'une entité existante par une version mise à jour.

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Si l'entité à remplacer n'existe pas, l'opération de mise à jour échoue. Si vous voulez stocker une entité, qu'elle existe déjà ou non, utilisez **insert\_or\_replace_entity**.  Dans l'exemple suivant, le premier appel remplace l'entité existante. Le deuxième appel insère une nouvelle entité, car il n'existe aucune entité ayant les clés **PartitionKey** et **RowKey** spécifiées dans la table.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>Modification d'un groupe d'entités

Il est parfois intéressant de soumettre un lot d'opérations simultanément pour assurer un traitement atomique par le serveur. Pour cela, vous devez utiliser la méthode **begin\_batch** sur **TableService**, puis appeler les habituelles séries d'opérations. Lorsque vous devez soumettre le lot, appelez **commit\_batch**. Notez que toutes les entités doivent se trouver dans la même partition pour pouvoir être modifiées par lot. L'exemple ci-dessous permet d'ajouter deux entités dans un lot.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## <a name="query-for-entity"> </a>Interrogation d'une entité

Pour interroger une entité dans une table, utilisez la méthode **get\_entity**, en transmettant les clés **PartitionKey** et **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## <a name="query-set-entities"> </a>Interrogation d'un ensemble d'entités

Cet exemple recherche toutes les tâches dans Seattle avec la clé **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## <a name="query-entity-properties"> </a>Interrogation d'un sous-ensemble de propriétés d'entité

Vous pouvez utiliser une requête de table pour extraire uniquement quelques propriétés d'une entité. Cette technique, nommée " projection ", réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. Utilisez le paramètre **select** et transmettez les noms des propriétés à soumettre au client.

La requête contenue dans le code suivant renvoie uniquement les **Descriptions** des entités de la table.

*Notez que l'extrait suivant fonctionne uniquement dans le service cloud de stockage et qu'il n'est pas pris en charge par l'émulateur
de stockage.*

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## <a name="delete-entity"> </a>Suppression d'une entité

Vous pouvez supprimer une entité en utilisant ses clés de partition et de ligne.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-table"> </a>Suppression d'une table

Le code suivant permet de supprimer une table d'un compte de stockage.

	table_service.delete_table('tasktable')

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez acquis les bases du stockage de tables, suivez ces liens pour savoir comment effectuer des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][]
-   [Consultez le blog de l'équipe Azure Storage][]

  [Étapes suivantes]: #next-steps
  [Présentation du service de Table]: #what-is
  [Concepts]: #concepts
  [Création d'un compte Azure Storage]: #create-account
  [Procédure : Création d'une table]: #create-table
  [Procédure : Ajout d'une entité à une table]: #add-entity
  [Procédure : Mise à jour d'une entité]: #update-entity
  [Procédure : Modification d'un groupe d'entités]: #change-entities
  [Procédure : Interrogation d'une entité]: #query-for-entity
  [Procédure : Interrogation d'un ensemble d'entités]: #query-set-entities
  [Procédure : Interrogation d'un sous-ensemble de propriétés d'entité]: #query-entity-properties
  [Procédure : Suppression d'une entité]: #delete-entity
  [Procédure : Suppression d'une table]: #delete-table
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
  [Consultez le blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=35.1-->
