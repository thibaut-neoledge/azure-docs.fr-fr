#####Création d'une table
Un objet **CloudTableClient** vous permet d'obtenir les objets de référence pour les tables et entités. Le code suivant crée un objet **CloudTableClient** et l'utilise pour créer une table. Il tente de référencer la table nommée " people ". S'il ne trouve aucune table ainsi nommée, il en crée une.

**Remarque :** tous les codes de ce guide partent du principe que l'application développée est un projet Azure Cloud Service et utilise une chaîne de connexion de stockage stockée dans la configuration de service de l'application Azure.

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	table.CreateIfNotExists();

#####Ajout d'une entité à une table
Pour ajouter une entité à une table, commencez par créer une classe définissant les propriétés de votre entité. Le code suivant définit une classe d'entité nommée **CustomerEntity**, qui utilise le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition.

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

Les opérations de table impliquant des entités sont effectuées en utilisant l'objet **CloudTable** créé à l'étape précédente, " Création d'une table ". L'objet **TableOperation** représente l'opération à effectuer. L'exemple de code suivant montre comment créer des objets **CloudTable** et **CustomerEntity**. Pour préparer l'opération, un objet **TableOperation** est créé pour insérer l'entité du client dans la table. Pour terminer, l'opération est exécutée en appelant CloudTable.Execute.

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

#####Insertion d'un lot d'entités
Vous pouvez insérer plusieurs entités dans une table en une seule opération d'écriture. L'exemple de code suivant crée deux objets d'entité (" Jeff Smith " et " Ben Smith ") et les ajoute à un objet **TableBatchOperation** en utilisant la méthode Insert. L'opération est ensuite exécutée par l'appel de CloudTable.Execute.

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

#####Recherche de toutes les entités d'une partition
Pour exécuter une requête de table portant sur toutes les entités d'une partition, utilisez un objet **TableQuery**. L'exemple de code suivant indique un filtre pour les entités où 'Smith' est la clé de partition. Il imprime les champs de chaque entité dans les résultats de requête vers la console.

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

#####Obtention d'une seule entité
Vous pouvez écrire une requête pour obtenir une seule entité. Le code suivant utilise un objet **TableOperation** pour spécifier un client nommé 'Ben Smith'. Cette méthode renvoie une seule entité (et non une collection). De plus, la valeur renvoyée dans TableResult.Result est un objet **CustomerEntity**. La méthode la plus rapide pour extraire une seule entité dans le service de **Table** consiste à spécifier une clé de partition et une clé de ligne.

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

#####Suppression d'une entité
Une fois l'entité trouvée, vous pouvez la supprimer. Le code suivant recherche l'entité de client " Ben Smith ", puis la supprime.

	// Create the CloudTable that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = table.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   table.Execute(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[En savoir plus sur Azure Storage](http://azure.microsoft.com/documentation/services/storage/)
Voir aussi [Consultation des ressources de stockage avec l'Explorateur de serveurs](http://msdn.microsoft.com/library/azure/ff683677.aspx).

<!--HONumber=42-->
