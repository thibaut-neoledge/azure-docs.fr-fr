<properties title="Getting Started with Azure Storage" pageTitle="Prise en main d'Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Mise en route](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)
> - [Que s'est-il passé ?](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Prise en main d'Azure Storage (Projets ASP.NET vNext)

> [AZURE.SELECTOR]
> - [Objets blob](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Files d'attente](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [Tables](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

Le service de stockage de tables Azure vous permet de stocker de grandes quantités de données structurées. Il s'agit d'une banque de données NoSQL qui accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles.  Pour plus d'informations, consultez la page [Utilisation du stockage de tables à partir de .NET](http://azure.microsoft.com/fr-fr/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET").

Pour accéder par programme aux tables des projets ASP.NET 5, vous devez ajouter les éléments suivants, s'ils ne sont pas déjà présents.

1. Ajoutez la déclaration d'espace de noms suivante en haut de chaque fichier C# pour lequel vous souhaitez accéder à Azure Storage par programmation :

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;
		using Microsoft.Framework.ConfigurationModel;
		using System.Threading.Tasks;

2. Utilisez le code ci-dessous pour obtenir le paramètre de configuration.

		Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Obtention de la chaîne de connexion de stockage
Pour pouvoir exploiter une table, vous devez avant tout obtenir la chaîne de connexion du compte de stockage dans lequel résideront les tables. Pour représenter les informations de votre compte de stockage, vous pouvez utiliser le type **CloudStorageAccount**. Si vous travaillez dans un projet ASP.NET vNext, vous pouvez appeler la méthode get de l'objet Configuration pour obtenir votre chaîne de connexion de stockage et les informations de votre compte de stockage à partir de la configuration du service Azure, comme illustré dans le code suivant :

**REMARQUE :** les API qui effectuent des appels vers le stockage Azure dans ASP.NET 5 sont asynchrones. Pour plus d'informations, consultez la page [Programmation asynchrone avec Async et Await](http://msdn.microsoft.com/library/hh191443.aspx). Le code ci-dessous suppose que les méthodes de programmation asynchrone sont utilisées.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####Création d'une table
Un objet **CloudTableClient** vous permet d'obtenir les objets de référence pour les tables et entités. Le code suivant crée un objet **CloudTableClient** et l'utilise pour créer une table. Il tente de référencer la table nommée " people ". S'il ne trouve aucune table ainsi nommée, il en crée une.

**REMARQUE :** tous les codes indiqués dans ce guide partent du principe que l'application développée est un projet de service cloud Azure et utilisent une chaîne de connexion de stockage enregistrée dans la configuration de service de l'application Azure. En outre, utilisez l'intégralité de ce code avant le code des sections suivantes.

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	await table.CreateIfNotExistsAsync();

#####Ajout d'une entité à une table
Pour ajouter une entité à une table, commencez par créer une classe définissant les propriétés de votre entité. Le code suivant définit une classe d'entité nommée **CustomerEntity**, utilisant le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition.

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

Les opérations de table impliquant des entités sont effectuées en utilisant l'objet **CloudTable** créé à l'étape précédente, " Création d'une table ". L'objet **TableOperation** représente l'opération à effectuer. L'exemple de code suivant montre comment créer des objets **CloudTable** et **CustomerEntity**. Pour préparer l'opération, un objet **TableOperation** est créé pour insérer l'entité du client dans la table. Pour terminer, l'opération est exécutée en appelant CloudTable.ExecuteAsync.

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	await table.ExecuteAsync(insertOperation);

#####Insertion d'un lot d'entités
Vous pouvez insérer plusieurs entités dans une table en une seule opération d'écriture. L'exemple de code suivant crée deux objets d'entité (" Jeff Smith " et " Ben Smith ") et les ajoute à un objet **TableBatchOperation** en utilisant la méthode Insert. L'opération est ensuite exécutée par l'appel de CloudTable.ExecuteBatchAsync.

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
	await table.ExecuteBatchAsync(batchOperation);

#####Recherche de toutes les entités d'une partition
Pour exécuter une requête de table portant sur toutes les entités d'une partition, utilisez un objet **TableQuery**. L'exemple de code suivant indique un filtre pour les entités où Smith est la clé de partition. Il imprime les champs de chaque entité dans les résultats de requête vers la console.

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await table.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


#####Obtention d'une seule entité
Vous pouvez écrire une requête pour obtenir une seule entité. Le code suivant utilise un objet **TableOperation** pour spécifier le client " Ben Smith ". Cette méthode renvoie une seule entité (et non une collection). De plus, la valeur renvoyée dans TableResult.Result est un objet **CustomerEntity**. La méthode la plus rapide pour extraire une seule entité du service **Table** consiste à spécifier une clé de partition et une clé de ligne.

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await table.ExecuteAsync(retrieveOperation);
	
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
	   await table.ExecuteAsync(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[En savoir plus sur Azure Storage](http://azure.microsoft.com/documentation/services/storage/)
Voir également [Consultation des ressources de stockage avec l'Explorateur de serveurs](http://msdn.microsoft.com/fr-fr/library/azure/ff683677.aspx) et [ASP.NET 5](http://www.asp.net/vnext).
