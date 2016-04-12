<properties
	pageTitle="Didacticiel NoSQL  kit de développement logiciel (SDK) .NET de DocumentDB | Microsoft Azure"
	description="Un didacticiel NoSQL qui crée une application de base de données en ligne et de console #C à l’aide du Kit de développement logiciel (SDK) .NET de DocumentDB. DocumentDB est une base de données NoSQL pour JSON."
	keywords="didacticiel nosql, base de données en ligne, application console c#"
	services="documentdb"
	documentationCenter=".net"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# Didacticiel NoSQL : générer une application de console C# DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.JS](documentdb-nodejs-get-started.md)

Bienvenue dans le didacticiel NoSQL pour le Kit de développement logiciel (SDK) de DocumentDB ! À la fin de ce didacticiel, vous disposerez d’une application console qui crée et interroge des ressources DocumentDB.

Nous allons aborder les points suivants :

- Création et connexion à un compte DocumentDB
- Configuration de votre solution Visual Studio
- Création d’une base de données en ligne
- Création d’une collection
- Création de documents JSON
- Interrogation de la collection
- Suppression de la base de données

Vous n’avez pas le temps ? Ne vous inquiétez pas ! La solution complète est disponible sur [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Pour obtenir des instructions rapides, consultez [Obtenir la solution complète](#GetSolution).

À la fin, utilisez les boutons de vote en haut ou en bas de cette page pour nous faire part de vos commentaires. Si vous souhaitez que nous vous contactions directement, n’hésitez pas à inclure votre adresse de messagerie dans vos commentaires.

Commençons dès maintenant !

## Composants requis

Vérifiez que vous disposez des éléments suivants :

- Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/) dès aujourd’hui.
- [Visual Studio 2013 / Visual Studio 2015](http://www.visualstudio.com/).

## Étape 1 : créer un compte DocumentDB

Créons un compte DocumentDB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre solution Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Étape 2 : Configuration de votre solution Visual Studio

1. Ouvrez **Visual Studio** sur votre ordinateur.
2. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis choisissez **Projet**.
3. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Modèles** / **Visual C#** / **Application console**, nommez votre projet, puis cliquez sur **OK**.
4. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur votre nouvelle application console, qui se trouve sous votre solution Visual Studio.
5. Ensuite, sans quitter le menu, cliquez sur **Gérer les packages NuGet...**
6. Dans le volet situé à l'extrême gauche de la fenêtre **Gérer les packages NuGet**, cliquez sur **En ligne** / **nuget.org**.
7. Dans la zone d'entrée **Rechercher en ligne**, recherchez **Bibliothèque cliente DocumentDB**.
8. Dans les résultats, recherchez **Bibliothèques cliente Microsoft Azure DocumentDB**, puis cliquez sur **Installer**. L’ID de package de la bibliothèque cliente DocumentDB est [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

Parfait ! L’installation étant terminée, nous pouvons passer à l’écriture du code. Vous trouverez un projet de code complet de ce didacticiel dans [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

##<a id="Connect"></a>Étape 3 : se connecter à un compte DocumentDB

Tout d’abord, ajoutez ces références au début de votre application C#, dans le fichier Program.cs :

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT] Pour pouvoir exécuter ce didacticiel NoSQL, veillez à ajouter les dépendances ci-dessus.

Ensuite, enregistrez le point de terminaison du compte DocumentDB et la clé d’accès primaire ou secondaire, qui se trouve sur le [portail Azure](https://portal.azure.com).

![Capture d’écran du portail Azure utilisée par le didacticiel NoSQL pour créer une application de console C#. Présente un compte DocumentDB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance dans le panneau du compte DocumentDB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

Nous allons commencer l’application de démonstration de prise en main en créant une instance de **DocumentClient**. Créez une tâche asynchrone appelée **GetStartedDemo** et instanciez le nouveau **DocumentClient**.

	private static async Task GetStartedDemo()
	{
		// Create a new instance of the DocumentClient
		DocumentClient client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Appelez la tâche asynchrone à partir de la méthode **Main**, comme dans le code ci-dessous.

	public static void Main(string[] args)
	{
		try
		{
			GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

Maintenant que vous savez comment vous connecter à un compte DocumentDB et comment créer une instance de la classe **DocumentClient**, voyons comment utiliser les ressources DocumentDB.

## Étape 4 : Création d’une base de données en ligne
Votre [base de données](documentdb-resources.md#databases) DocumentDB peut être créée à l’aide de la méthode [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la classe **DocumentClient**. Une base de données est le conteneur logique de stockage de documents JSON partitionné entre les collections. Créez votre base de données dans votre méthode **GetStartedDemo** après avoir créé votre classe **DocumentClient**.

	// Check if the database FamilyDB does not exist
	string databaseName = "FamilyDB";
	
	try
	{
		await this.client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(familyName));
	}
	catch (DocumentClientException de)
	{
		// If the database does not exist, create a new database
		if (de.StatusCode == HttpStatusCode.NotFound)
		{
			await this.client.CreateDatabaseAsync(new Database { Id = familyName });
			this.WriteToConsoleAndPromptToContinue("Created {0}", databaseName);
		}
		else
		{
			throw;
		}
	}
	
##<a id="CreateColl"></a>Étape 5 : créer une collection  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** crée une collection avec un débit réservé , ce qui a des conséquences sur le plan tarifaire. Pour plus d'informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).

Vous pouvez créer une [collection](documentdb-resources.md#collections) à l'aide de la méthode [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la classe **DocumentClient**. Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. Créez une collection nommée **FamilyCollection** après la création de votre base de données dans la méthode **GetStartedDemo**.

	string collectionName = "FamilyCollection";
	try
	{
		await this.client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName));
	}
	catch (DocumentClientException de)
	{
		// If the document collection does not exist, create a new collection
		if (de.StatusCode == HttpStatusCode.NotFound)
		{
			// Configure the collection. Optionally, you can configure partitioning and indexing behavior of the collection here.
			DocumentCollection collectionInfo = new DocumentCollection();
			collectionInfo.Id = collectionName;

			// DocumentDB collections can be reserved with throughput specified in request units/second. 1 RU is a normalized request equivalent to the read
			// of a 1KB document.  Here we create a collection with 400 RU/s. 
			await this.client.CreateDocumentCollectionAsync(
				UriFactory.CreateDatabaseUri(databaseName),
				new DocumentCollection { Id = collectionName },
				new RequestOptions { OfferThroughput = 400 });

			this.WriteToConsoleAndPromptToContinue("Created {0}", collectionName);
		}
		else
		{
			throw;
		}
	}

##<a id="CreateDoc"></a>Étape 6 : Création de documents JSON
Vous pouvez créer un [document](documentdb-resources.md#documents) à l'aide de la méthode [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la classe **DocumentClient**. Les documents correspondent à du contenu JSON (arbitraire) défini par l'utilisateur. Nous pouvons maintenant insérer un ou plusieurs documents. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser de l’[outil de migration de données](documentdb-import-data.md) de DocumentDB.

Nous devons tout d’abord créer une classe **Famille** représentant les objets stockés dans DocumentDB dans cet exemple. Nous allons également créer des sous-classes **Parent**, **Enfant**, **Animal** et **Adresse** qui seront utilisées dans la classe **Famille**. Notez que les documents doivent contenir une propriété **Id** sérialisée en tant qu’**id** dans JSON. Créez ces classes en ajoutant les sous-classes internes suivantes après la méthode **GetStartedDemo**.

	public class Family
	{
		[JsonProperty(PropertyName = "id")]
		public string Id { get; set; }
		public string LastName { get; set; }
		string string District { get; set; }
		public Parent[] Parents { get; set; }
		public Child[] Children { get; set; }
		public Address Address { get; set; }
		public bool IsRegistered { get; set; }
	}
	
	public class Parent
	{
		public string FamilyName { get; set; }
		public string FirstName { get; set; }
	}

	public class Child
	{
		public string FamilyName { get; set; }
		public string FirstName { get; set; }
		public string Gender { get; set; }
		public int Grade { get; set; }
		public Pet[] Pets { get; set; }
	}

	public class Pet
	{
		public string GivenName { get; set; }
	}

	public class Address
	{
		public string State { get; set; }
		public string County { get; set; }
		public string City { get; set; }
	}


Ensuite, créez vos documents dans votre méthode asynchrone **GetStartedDemo**. Commencez par créer une méthode **CreateFamilyDocumentIfNotExists** comme indiqué ci-dessous :

	private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
	{
		try
		{
			await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
		}
		catch (DocumentClientException de)
		{
			if (de.StatusCode == HttpStatusCode.NotFound)
			{
				await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
				this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
			}
			else
			{
				throw;
			}
		}
	}

Insérez ensuite deux documents, un pour la famille Andersen, l’autre pour la famille Wakefield, comme indiqué ci-dessous :

	// Insert a document, here we create a Family object
	Family andersenFamily = new Family
	{
		Id = "Andersen.1",
		LastName = "Andersen",
		Parents = new Parent[] 
		{
			new Parent { FirstName = "Thomas" },
			new Parent { FirstName = "Mary Kay" }
		},
		Children = new Child[] 
		{
			new Child
			{
				FirstName = "Henriette Thaulow",
				Gender = "female",
				Grade = 5,
				Pets = new Pet[] 
				{
					new Pet { GivenName = "Fluffy" }
				}
			}
		},
		District = "WA5",
		Address = new Address { State = "WA", County = "King", City = "Seattle" },
		IsRegistered = true
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

	Family wakefieldFamily = new Family
	{
		Id = "Wakefield.7",
		LastName = "Wakefield",
		Parents = new Parent[]
		{
			new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
			new Parent { FamilyName = "Miller", FirstName = "Ben" }
		},
		Children = new Child[]
		{
			new Child
			{
				FamilyName = "Merriam",
				FirstName = "Jesse",
				Gender = "female",
				Grade = 8,
				Pets = new Pet[]
				{
					new Pet { GivenName = "Goofy" },
					new Pet { GivenName = "Shadow" }
				}
			},
			new Child
			{
				FamilyName = "Miller",
				FirstName = "Lisa",
				Gender = "female",
				Grade = 1
			}
		},
		District = "NY23",
		Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
		IsRegistered = false
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

La base de données, la collection et les documents suivants sont maintenant créés dans votre compte DocumentDB.

![Diagramme illustrant la relation hiérarchique existant entre le compte, la base de données en ligne, la collection et les documents utilisés par le didacticiel NoSQL pour créer une application de console C#](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>Étape 7 : interroger les ressources DocumentDB

DocumentDB prend en charge les [requêtes](documentdb-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection. L'exemple de code suivant affiche différentes requêtes (à l'aide de la syntaxe SQL de DocumentDB et de LINQ) que nous pouvons exécuter sur les documents insérés à l'étape précédente. Ajoutez ces requêtes à votre méthode asynchrone **GetStartedDemo**.

	// Run a simple query via LINQ. DocumentDB indexes all properties, so queries can be completed efficiently and with low latency.
	// Here we find the Andersen family via its LastName
	IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
		UriFactory.CreateDocumentCollectionUri(databaseName, collectionName))
		.Where(f => f.LastName == "Andersen");

	// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
	Console.WriteLine("Running LINQ query...");
	foreach (Family family in familyQuery)
	{
			Console.WriteLine("\tRead {0}", family);
	}

	// Now execute the same query via direct SQL
	IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
		UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
		"SELECT * FROM Family WHERE Family.lastName = 'Andersen'");

	Console.WriteLine("Running direct SQL query...");
	foreach (Family family in familyQuery)
	{
		Console.WriteLine("\tRead {0}", family);
	}

Le diagramme suivant montre comment la syntaxe de requête SQL de DocumentDB est appelée sur la collection que vous avez créée. C'est par ailleurs cette même logique qui s'applique à la requête LINQ.

![Diagramme illustrant l’étendue et la signification de la requête utilisée par le didacticiel NoSQL pour créer une application de console C#](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

Le mot clé [FROM](documentdb-sql-query.md#from-clause) est facultatif dans la requête, car les requêtes DocumentDB sont déjà étendues à une collection unique. Par conséquent, « FROM Families f » peut être remplacé par «FROM root r » ou par tout autre nom de variable que vous choisissez. DocumentDB déduira que Families, root ou le nom de variable choisi fait par défaut référence à la collection actuelle.

##<a id="DeleteDatabase"></a>Étape 8 : suppression de la base de données en ligne

Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (collections, documents, etc.). Vous pouvez supprimer la base de données et la classe DocumentClient en ajoutant l'extrait de code suivant à la fin de votre méthode asynchrone **GetStartedDemo**.

	// Clean up/delete the database
	await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

##<a id="Run"></a>Étape 9 : Exécution de votre application console C#

Vous êtes maintenant en mesure d'exécuter votre application. À la fin de votre méthode **Main**, ajoutez la ligne de code suivante, ce qui vous permettra de lire la sortie de la console avant la fin d'exécution de l'application.

	Console.ReadLine();

Appuyez à présent sur F5 dans Visual Studio pour générer l'application en mode débogage.

La sortie de votre application de prise en main doit maintenant s'afficher. Celle-ci doit présenter les résultats des requêtes que nous avons ajoutées, qui doivent correspondre au texte d'exemple ci-dessous.

	Created FamilyDB
	Press any key to continue ...
	Created FamilyCollection
	Press any key to continue ...
	Created Family Andersen.1
	Press any key to continue ...
	Created Family Wakefield.7
	Press any key to continue ...
	Running LINQ query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	Running direct SQL query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	End of demo, press any key to exit.

Félicitations ! Vous avez terminé ce didacticiel NoSQL et que vous disposez d’une application de console C# qui fonctionne !

##<a id="GetSolution"></a> Obtenir la solution du didacticiel NoSQL complète
Pour générer la solution GetStarted qui contient tous les exemples de cet article, vous devez avoir les éléments suivants :

-   [Un compte DocumentDB][documentdb-create-account].
-   La solution [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) disponible sur GitHub.

Pour restaurer les références au Kit de développement logiciel (SDK) .NET de DocumentDB dans Visual Studio, cliquez avec le bouton droit sur la solution **GetStarted** dans l’Explorateur de solutions, puis cliquez sur **Activer la restauration des packages NuGet**. Ensuite, dans le fichier App.config, mettez à jour les valeurs pour EndpointUrl et AuthorizationKey comme décrit à la section [Se connecter à un compte DocumentDB](#Connect).

## Étapes suivantes

-   Vous voulez un exemple de didacticiel NoSQL ASP.NET MVC plus complexe ? Consultez [Création d'une application web avec ASP.NET MVC et DocumentDB](documentdb-dotnet-application.md).
-	Apprenez à [surveiller un compte DocumentDB](documentdb-monitor-accounts.md).
-	Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
-	Consultez la section Développer de la [page de documentation DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/) pour découvrir plus en détail le modèle de programmation.

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

<!---HONumber=AcomDC_0330_2016-->