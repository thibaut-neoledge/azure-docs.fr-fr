<properties
	pageTitle="Comment utiliser le stockage de tables à partir de .NET | Microsoft Azure"
	description="Découvrez comment utiliser le stockage de tables Microsoft Azure pour créer et supprimer des tables, et insérer et interroger les entités d’une table."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="06/15/2015"
	ms.author="tamram"/>


# Utilisation du stockage de tables à partir de .NET

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Vue d’ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage de tables Azure. Les exemples ont été écrits en code C# et utilisent la bibliothèque du client de stockage Azure pour .NET. Les scénarios traités incluent la **création et suppression d’une table**, ainsi que l’**utilisation d’entités de table**.

> [AZURE.NOTE]Ce guide cible la bibliothèque cliente de stockage Azure .NET 2.x et les versions ultérieures. Nous vous recommandons d’utiliser la version 4.x disponible via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou dans le [Kit de développement logiciel (SDK) Azure pour .NET](/downloads/). Consultez [Accès au stockage de tables par programme](#programmatically-access-table-storage) ci-dessous pour plus d’informations sur l’obtention de la bibliothèque cliente de stockage.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Accès au stockage de tables par programme

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Déclarations d'espace de noms
Ajoutez les déclarations d'espace de noms suivantes en haut de chaque fichier C# dans lequel vous souhaitez accéder au stockage Azure par programme :

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Prenez soin de bien référencer l’assembly `Microsoft.WindowsAzure.Storage.dll`.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

## Création d’une table

Un objet **CloudTableClient** vous permet d'obtenir les objets de référence pour les tables et entités. Le code suivant crée un objet **CloudTableClient** et l’utilise pour créer une table. Tous les codes contenus dans ce guide partent du principe que l'application développée est un projet de service cloud Azure et utilise une chaîne de connexion de stockage stockée dans la configuration de service de l'application Azure.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## Ajout d'une entité à une table

Les entités mappent vers les objets C# en utilisant une classe personnalisée dérivée d’une **TableEntity**. Pour ajouter une entité à une table, créez une classe définissant les propriétés de votre entité. Le code suivant définit une classe d’entité utilisant le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition. Ensemble, les clés de partition et de ligne d’une entité identifient l’entité de façon unique dans la table. Les requêtes d'entités dont les clés de partition sont identiques sont plus rapides que celles d'entités dont les clés de partition sont différentes, mais le fait d'utiliser différentes clés de partition améliore l'extensibilité des opérations parallèles. Si une propriété doit être stockée dans le service de Table, il doit s’agir d’une propriété publique d’un type pris en charge qui expose à la fois `get` et `set`. De plus, votre type d’entité *doit* exposer un constructeur sans paramètre.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Les opérations de table impliquant des entités sont effectuées par le biais de l’objet **CloudTable** que vous avez créé à l’aide de l’article « Procédure : Création d’une table ». L’opération à effectuer est représentée par un objet **TableOperation**. L’exemple de code suivant illustre la création des objets **CloudTable** et **CustomerEntity**. Pour préparer l’opération, un objet **TableOperation** est créé pour insérer l’entité du client dans la table. Finalement, l’opération est exécutée en appelant **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## Insertion d'un lot d'entités

Vous pouvez insérer un lot d'entités dans une table en une seule opération d'écriture. Autres remarques sur les opérations par lot :

1.  Vous pouvez effectuer des mises à jour, des suppressions et des insertions dans la même opération par lot.
2.  Une seule opération par lot peut inclure jusqu’à 100 entités.
3.  Toutes les entités d’une opération par lot doivent avoir la même clé de partition.
4.  Même s'il est possible d'exécuter une requête en tant qu'opération par lot, il doit s'agir de la seule opération du lot.

<!-- -->
L’exemple de code suivant crée deux objets d’entité et ajoute chacun d’eux à une **TableBatchOperation** en utilisant la méthode **Insert**. La méthode **CloudTable.Execute** est ensuite appelée pour exécuter l’opération.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

	// Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";

	// Create another customer entity and add it to the table.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";

	// Add both customer entities to the batch insert operation.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Execute the batch operation.
	table.ExecuteBatch(batchOperation);

## Extraction de toutes les entités d'une partition

Pour exécuter une requête de table pour toutes les entités d’une partition, utilisez un objet **TableQuery**. L’exemple de code suivant indique un filtre pour les entités où ’Smith’ est la clé de partition. Il imprime les champs de chaque entité dans les résultats de requête vers la console.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## Extraction d'un ensemble d'entités dans une partition

Si vous ne voulez pas exécuter une requête pour toutes les entités d'une partition, vous pouvez spécifier un ensemble en combinant le filtre de clé de partition avec un filtre de clé de ligne. L’exemple de code suivant utilise deux filtres pour obtenir toutes les entités dans la partition « Smith » où la clé de ligne (prénom) commence par une lettre située avant la lettre « E » dans l’ordre alphabétique, puis imprime les résultats de la requête.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

	// Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## Extraction d'une seule entité

Vous pouvez écrire une requête pour extraire une seule entité. Le code suivant utilise une **TableOperation** pour spécifier le client ’Ben Smith’. Cette méthode renvoie une seule entité, au lieu d’une collection. De plus, la valeur renvoyée dans **TableResult.Result** est un objet **CustomerEntity**. La méthode la plus rapide pour extraire une seule entité dans le service de Table consiste à spécifier une clé de partition et une clé de ligne.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

## Remplacement d'une entité

Pour mettre à jour une entité, récupérez-la dans le service de Table, modifiez l'objet d'entité, puis enregistrez les modifications dans la table de service. Le code suivant modifie le numéro de téléphone d’un client existant. Au lieu d’appeler la méthode **Insert**, ce code utilise la méthode **Replace**. Ceci entraîne le remplacement complet de l’entité sur le serveur, sauf si cette dernière a été modifiée depuis sa récupération, auquel cas l’opération échoue. Cet échec survient pour empêcher votre application de remplacer par erreur une modification apportée entre la récupération et la mise à jour par un autre composant de votre application. Pour gérer correctement cet échec, vous devez récupérer de nouveau l’entité, apporter vos modifications (si elles sont toujours valides), puis effectuer une autre opération **Replace**. La prochaine section vous apprendra à remplacer ce comportement.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Create the InsertOrReplace TableOperation
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Execute the operation.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

## Insertion ou remplacement d’une entité

Les opérations **Replace** échouent si l’entité est modifiée depuis sa récupération à partir du serveur. De plus, vous devez d’abord récupérer l’entité à partir du serveur pour que l’opération **Replace** réussisse. Cependant, il se peut parfois que vous ne sachiez pas si l'entité existe sur le serveur et si les valeurs stockées sont inadaptées. Votre mise à jour doit donc toutes les remplacer. Pour cela, vous devez utiliser une opération **InsertOrReplace**. Cette opération insère l'entité (s'il n'y en a pas déjà une) ou la remplace (s'il y en a une), indépendamment du moment de la dernière mise à jour. Dans l’exemple de code suivant, l’entité de client pour Ben Smith est toujours récupérée, mais elle est ensuite enregistrée sur le serveur en utilisant **InsertOrReplace**. Les mises à jour apportées à l'entité entre les opérations de récupération et de mise à jour sont remplacées.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Create the InsertOrReplace TableOperation
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Execute the operation.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

## Interrogation d'un sous-ensemble de propriétés d'entité

Vous pouvez utiliser une requête de table pour récupérer uniquement quelques propriétés au lieu de l’intégralité des propriétés de l’entité. Cette technique, nommée « projection », réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. La requête contenue dans le code suivant renvoie uniquement les adresses de messagerie électronique des entités dans la table. Pour ce faire, nous utilisons une requête **DynamicTableEntity**, ainsi qu’une requête **EntityResolver**. Pour plus d’informations sur la projection, consultez ce [billet de blog][]. Notez que la projection n'est pas prise en charge sur l'émulateur de stockage local : ce code s'exécute donc uniquement lors de l'utilisation d'un compte sur le service de Table.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and only select the Email property
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## Suppression d'une entité

Il est facile de supprimer une entité après l'avoir récupérée. Il suffit pour cela d'utiliser la procédure suivie pour la mise à jour d'une entité. Le code suivant extrait et supprime une entité de client.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

## Suppression d'une table

Pour finir, l'exemple de code suivant supprime une table d'un compte de stockage. Une table supprimée ne pourra plus être recréée pendant un certain temps.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## Récupérer des entités dans les pages de manière asynchrone

Si vous lisez un grand nombre d’entités et souhaitez traiter ou afficher les entités dès qu’elles sont récupérées au lieu d’attendre qu’elles aient toutes été renvoyées, vous pouvez les récupérer à l’aide d’une requête segmentée. Cet exemple illustre comment renvoyer les résultats au moyen du modèle Async-Await afin que l’exécution ne soit pas bloquée dans l’attente d’un ensemble de résultats volumineux. Pour plus d’informations sur l’utilisation du modèle Async-Await dans .NET, voir l’article [Programmation asynchrone avec Async et Await (C# et Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Initialize a default TableQuery to retrieve all the entities in the table
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1000 entities)
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end)
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received indicating the end of the table
    } while(continuationToken != null);

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage des tables, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

<ul>
<li>Pour plus d'informations sur les API disponibles, consultez la documentation de référence du service de Table&#160;:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Référence de la bibliothèque cliente de stockage pour&#160;.NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">Référence d’API REST</a></li>
  </ul>
</li>
<li>Pour plus d’informations sur les tâches avancées que vous pouvez effectuer avec le stockage Azure, consultez la page <a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Stockage et accessibilité des données dans Windows Azure</a>.</li>
<li>Découvrez comment simplifier le code que vous écrivez pour travailler avec Azure Storage à l’aide du <a href="../websites-dotnet-webjobs-sdk/">Kit de développement logiciel (SDK) Azure WebJobs.</li>
<li>Pour plus d'informations sur les autres options de stockage de données dans Azure, consultez d'autres guides de fonctionnalités.
  <ul>
    <li>Utilisez le <a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">stockage d’objets blob</a> pour stocker des données non structurées.</li>
    <li>Utilisez le <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">stockage des files d'attente</a> pour stocker des données structurées.</li>
    <li>Utilisez une <a href="/documentation/articles/sql-database-dotnet-how-to-use/">base de données SQL</a> pour stocker des données relationnelles.</li>
  </ul>
</li>
</ul>

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [billet de blog]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table Storage]: #tablestorage
 

<!---HONumber=July15_HO3-->