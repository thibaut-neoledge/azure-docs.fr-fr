<properties urlDisplayName="Table Service" pageTitle="Utilisation du stockage de tables (PHP) | Microsoft Azure" metaKeywords="Azure Table service PHP, Azure creating table, Azure deleting table, Azure insert table, Azure query table" description="Learn how to use the Table service from PHP to create and delete a table, and insert, delete, and query the table." metaCanonical="" services="storage" documentationCenter="PHP" title="How to use the Table service from PHP" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/24/2014" ms.author="tomfitz" />

# Utilisation du service de Table à partir de PHP

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de Table Azure. Les exemples sont écrits en PHP et utilisent le [Kit de développement logiciel (SDK) Azure pour PHP][download]. Les scénarios traités incluent la **création et la suppression d'une table, l'insertion, la suppression et l'interrogation d'entités dans une table**. Pour plus d'informations sur le service de Table Azure, consultez la section [Étapes suivantes](#NextSteps) .

##Sommaire

* [Présentation du service de Table](#what-is)
* [Concepts](#concepts)
* [Création d'un compte de stockage Azure](#CreateAccount)
* [Création d'une application PHP](#CreateApplication)
* [Configuration de votre application pour accéder au service de Table](#ConfigureStorage)
* [Configuration d'une chaîne de connexion de stockage Azure](#ConnectionString)
* [Procédure : Création d'une table](#CreateTable)
* [Procédure : Ajout d'une entité à une table](#AddEntity)
* [Procédure : Extraction d'une seule entité](#RetrieveEntity)
* [Procédure : Extraction de toutes les entités d'une partition](#RetEntitiesInPartition)
* [Procédure : Extraction d'un sous-ensemble d'entités dans une partition](#RetrieveSubset)
* [Procédure : Extraction d'un sous-ensemble de propriétés d'entité](#RetPropertiesSubset)
* [Procédure : Mise à jour d'une entité](#UpdateEntity)
* [Procédure : Traitement par lots d'opérations de table](#BatchOperations)
* [Procédure : Suppression d'une table](#DeleteTable)
* [Étapes suivantes](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

##<a id="CreateAccount"></a>Création d'un compte de stockage Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a id="CreateApplication"></a>Création d'une application PHP

La référence de classes dans le Kit de développement logiciel (SDK) Azure pour PHP constitue la seule exigence pour créer une application PHP qui accède au service de Table Azure. Vous pouvez utiliser tous les outils de développement pour créer votre application, y compris Bloc-notes.

Dans ce guide, vous allez utiliser les fonctionnalités du service de Table qui peuvent être appelées dans une application PHP en local, ou dans le code s'exécutant dans un rôle web, un rôle de travail ou un site web Azure.

##<a id="GetClientLibrary"></a>Obtention des bibliothèques clientes Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureStorage"></a>Configuration de votre application pour accéder au service de Table

Pour utiliser les API du service de Table Azure, vous devez procéder comme suit :

1. référencer le fichier de chargeur automatique à l'aide de l'instruction [require_once][require_once] et
2. référencer toute classe que vous êtes susceptible d'utiliser.

L'exemple suivant montre comment inclure le fichier du chargeur automatique et référencer la classe **ServicesBuilder**.

> [WACOM.NOTE]
> Cet exemple et d'autres exemples de cet article partent du principe que vous avez installé les bibliothèques clientes PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou en tant que package PEAR, vous devez référencer le fichier de chargeur automatique <code>WindowsAzure.php</code> .

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Dans les exemples ci-dessous, l'instruction `require_once` s'affichera toujours, mais seules les classes nécessaires aux besoins de l'exemple à exécuter sont référencées.

##<a id="ConnectionString"></a>Configuration d'une chaîne de connexion de stockage Azure

Pour instancier un client de service de Table Azure, vous devez disposer au préalable d'une chaîne de connexion valide. Le format de la chaîne de connexion du service de Table est le suivant :

Pour accéder à un service en ligne :

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Pour accéder au stockage de l'émulateur :

	UseDevelopmentStorage=true


Pour créer un client de service Azure, vous devez utiliser la classe **ServicesBuilder**. Vous pouvez :

* lui passer directement la chaîne de connexion ; ou
* utiliser **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
	* par défaut une source externe est prise en charge : variables d'environnement ;
	* vous pouvez ajouter de nouvelles sources via une extension de la classe **ConnectionStringSource**

Dans les exemples ci-dessous, la chaîne de connexion est passée directement.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


##<a id="CreateTable"></a>Création d'une table

Vous pouvez créer une table avec un objet **TableRestProxy** via la méthode  **createTable**. Au moment de créer une table, vous pouvez définir le délai d'expiration du service de Table (pour plus d'informations sur le délai d'expiration du service de Table, consultez la page [Définition de délais d'expiration pour les opérations du service de Table][table-service-timeouts].)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

	try	{
		// Create table.
		$tableRestProxy->createTable("mytable");
	}
	catch(ServiceException $e){
		$code = $e->getCode();
		$error_message = $e->getMessage();
		// Handle exception based on error codes and messages.
		// Error codes and messages can be found here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
	}

Pour plus d'informations sur les restrictions au niveau des noms de table, consultez la page [Présentation du modèle de données du service de Table][table-data-model].

##<a id="AddEntity"></a>Utilisation Ajout d'une entité à une table

Pour ajouter une entité à une table, créez un objet **Entity** et transmettez-le à  **TableRestProxy->insertEntity**. Notez que lorsque vous créez une entité, vous devez spécifier une clé `PartitionKey` et une clé `RowKey`. Il s'agit des identificateurs uniques d'une entité, dont les valeurs peuvent être interrogées bien plus rapidement que les autres propriétés d'entité. Le système utilise `PartitionKey` pour distribuer automatiquement les entités de la table sur plusieurs nœuds de stockage. Les entités partageant la même clé `PartitionKey` sont stockées sur le même nœud. (les opérations réalisées sur plusieurs entités offrent de meilleures performances lorsque ces entités sont stockées sur un même nœud plutôt que sur différents nœuds). La clé `RowKey` est l'ID unique d'une entité au sein d'une partition.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$entity = new Entity();
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", 
						 EdmType::DATETIME, 
						 new DateTime("2012-11-05T08:15:00-08:00"));
	$entity->addProperty("Location", EdmType::STRING, "Home");
	
	try{
		$tableRestProxy->insertEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
	}

Pour plus d'informations sur les propriétés et les types de table, consultez la page [Présentation du modèle de données du service de Table][table-data-model].

La classe **TableRestProxy** offre deux autres méthodes pour insérer des entités : **insertOrMergeEntity** et **insertOrReplaceEntity**. Pour utiliser ces méthodes, créez un objet **Entity** et transmettez-le en tant que paramètre à l'une ou l'autre des méthodes. Chaque méthode insère l'entité si elle n'existe pas. Si l'entité existe déjà, **insertOrMergeEntity** met à jour la valeur des propriétés si celles-ci existent déjà et en ajoute de nouvelles dans le cas contraire, alors que **insertOrReplaceEntity** remplace entièrement une entité existante. L'exemple suivant montre comment utiliser **insertOrMergeEntity**. Si l'entité associée à la clé `PartitionKey` " tasksSeattle " et à la clé `RowKey` " 1 " n'existe pas encore, elle est insérée. HEn revanche, si elle a été ajoutée auparavant (comme indiqué dans l'exemple précédent), la propriété `DueDate` est mise à jour et la propriété `Status` est ajoutée. Les propriétés `Description` et `Location` sont également mises à jour, mais avec des valeurs qui de fait les laissent inchangées. Si ces deux dernières propriétés n'ont pas été ajoutées comme indiqué dans l'exemple, mais qu'elles existaient sur l'entité cible, leurs valeurs existantes restent inchangées.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	//Create new entity.
	$entity = new Entity();
	
	// PartitionKey and RowKey are required.
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	
	// If entity exists, existing properties are updated with new values and
	// new properties are added. Missing properties are unchanged.
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
	$entity->addProperty("Location", EdmType::STRING, "Home");
	$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.
	
	try	{
		// Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
		// would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
		$tableRestProxy->insertOrMergeEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	   

##<a id="RetrieveEntity"></a>Utilisation Extraction d'une seule entité

La méthode **TableRestProxy->getEntity** vous permet d'extraire une seule entité via une requête portant sur `PartitionKey` et `RowKey`. Dans l'exemple ci-dessous, la clé de partition `tasksSeattle` et la clé de ligne `1` sont transmises à la méthode **getEntity**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entity = $result->getEntity();

	echo $entity->getPartitionKey().":".$entity->getRowKey();

##<a id="RetEntitiesInPartition"></a>Utilisation Extraction de toutes les entités d'une partition

Les requêtes d'entité sont construites à l'aide de filtres (pour plus d'informations, consultez la page  [Interrogation de tables et d'entités][filters]). Pour extraire toutes les entités d'une partition, utilisez le filtre " PartitionKey eq *nom_partition* ". L'exemple suivant montre comment extraire toutes les entités de la partition `tasksSeattle` en transmettant un filtre à la méthode **queryEntities**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "PartitionKey eq 'tasksSeattle'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

##<a id="RetrieveSubset"></a>Utilisation Extraction d'un sous-ensemble d'entités dans une partition

Pour extraire un sous-ensemble d'entités dans une partition, il est possible d'utiliser le modèle de l'exemple précédent. Le sous-ensemble d'entités extrait varie en fonction du filtre utilisé (pour plus d'informations, consultez la page [Interrogation de tables et d'entités][filters]).L'exemple suivant montre comment utiliser un filtre pour extraire toutes les entités avec une valeur d'emplacement spécifique dans `Location` et une date d'échéance`DueDate` antérieure à une date spécifiée.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

##<a id="RetPropertiesSubset"></a>Utilisation Extraction d'un sous-ensemble de propriétés d'entité

Une requête peut extraire un sous-ensemble de propriétés d'entité. Cette technique, nommée " projection ", réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. Pour spécifier une propriété à extraire, transmettez son nom à la méthode **Query->addSelectField**. Vous pouvez appeler cette méthode plusieurs fois pour ajouter des propriétés supplémentaires. Après l'exécution de **TableRestProxy->queryEntities**, les entités renvoyées contiennent uniquement les propriétés sélectionnées (si vous voulez renvoyer un sous-ensemble d'entités de table, utilisez un filtre comme indiqué dans les requêtes précédentes).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\QueryEntitiesOptions;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$options = new QueryEntitiesOptions();
	$options->addSelectField("Description");
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	// All entities in the table are returned, regardless of whether 
	// they have the Description field.
	// To limit the results returned, use a filter.
	$entities = $result->getEntities();

	foreach($entities as $entity){
		$description = $entity->getProperty("Description")->getValue();
		echo $description."<br />";
	}

##<a id="UpdateEntity"></a>Utilisation Mise à jour d'une entité

Vous pouvez mettre à jour une entité existante en lui appliquant les méthodes  **Entity->setProperty** et **Entity->addProperty**, puis en appelant **TableRestProxy->updateEntity**. Dans l'exemple suivant, une entité est extraite, une propriété modifiée, une autre propriété supprimée et une nouvelle propriété ajoutée. À noter que pour supprimer une propriété, il suffit de lui attribuer la valeur  **null**. 

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	
	$entity = $result->getEntity();
	
	$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
	
	$entity->setPropertyValue("Location", null); //Removed Location.
	
	$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

	try	{
		$tableRestProxy->updateEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="DeleteEntity"></a>Utilisation Suppression d'une entité

Pour supprimer une entité, transmettez le nom de la table et les clés `PartitionKey` et `RowKey` de l'entité à la méthode **TableRestProxy->deleteEntity**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete entity.
		$tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Notez que pour les contrôles d'accès concurrentiel, vous pouvez définir la suppression de la propriété Etag d'une entité en employant la méthode **DeleteEntityOptions->setEtag** et en transmettant l'objet **DeleteEntityOptions** à **deleteEntity** en tant que quatrième paramètre.

##<a id="BatchOperations"></a>Utilisation Traitement par lots d'opérations de table

La méthode **TableRestProxy->batch** permet d'exécuter plusieurs opérations dans une même demande. Ce modèle implique d'ajouter des opérations à l'objet **BatchRequest** et de transmettre l'objet  **BatchRequest** à la méthode **TableRestProxy->batch**. Pour ajouter une opération à un objet **BatchRequest**, vous pouvez appeler l'une des méthodes suivantes à plusieurs reprises :

* **addInsertEntity** (permet d'ajouter une opération insertEntity)
* **addUpdateEntity** (permet d'ajouter une opération updateEntity)
* **addMergeEntity** (permet d'ajouter une opération mergeEntity)
* **addInsertOrReplaceEntity** (permet d'ajouter une opération insertOrReplaceEntity)
* **addInsertOrMergeEntity** (permet d'ajouter une opération insertOrMergeEntity)
* **addDeleteEntity** (permet d'ajouter une opération deleteEntity)

L'exemple suivant montre comment exécuter des opérations **insertEntity** et **deleteEntity** dans une même demande :

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;
	use WindowsAzure\Table\Models\BatchOperations;

 	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	// Create list of batch operation.
	$operations = new BatchOperations();
	
	$entity1 = new Entity();
	$entity1->setPartitionKey("tasksSeattle");
	$entity1->setRowKey("2");
	$entity1->addProperty("Description", null, "Clean roof gutters.");
	$entity1->addProperty("DueDate", 
						  EdmType::DATETIME, 
						  new DateTime("2012-11-05T08:15:00-08:00"));
	$entity1->addProperty("Location", EdmType::STRING, "Home");
	
	// Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

	// Add operation to list of batch operations.
	$operations->addDeleteEntity("mytable", "tasksSeattle", "1");
	
	try	{
		$tableRestProxy->batch($operations);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Pour plus d'informations sur le traitement par lots d'opérations de table, consultez la page [Exécution de transactions de groupe d'entités][entity-group-transactions].

##<a id="DeleteTable"></a>Utilisation Suppression d'une table

Enfin, pour supprimer une table, transmettez son nom à la méthode **TableRestProxy->deleteTable**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete table.
		$tableRestProxy->deleteTable("mytable");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="NextSteps"></a>Étapes suivantes

Maintenant que vous avez appris les principes de base du service de Table Azure, suivez ces liens pour découvrir des tâches de stockage plus complexes.

- Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure] []
- Accédez au blog de l'équipe Azure Storage : <http://blogs.msdn.com/b/windowsazurestorage/>

[télécharger]: http://go.microsoft.com/fwlink/?LinkID=252473
[Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179338.aspx
[filters]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dd894038.aspx

<!--HONumber=35_1-->
