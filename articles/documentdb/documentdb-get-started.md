<properties
	pageTitle="Bases de données NoSQL - Prise en main du Kit de développement logiciel (SDK) DocumentDB .NET | Microsoft Azure"
	description="Apprenez à créer une base de données et à configurer un compte Azure DocumentDB. Créez une base de données, une collection et stockez des documents JSON dans votre compte de base de données NoSQL."
	keywords="Create a database, create database, nosql database, nosql databases, nuget, documentdb, azure, Microsoft azure"
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
	ms.date="09/16/2015"
	ms.author="anhoh"/>

#Prendre en main le Kit de développement logiciel (SDK) .NET de DocumentDB  

Bienvenue dans la prise en main du Kit de développement logiciel (SDK) .NET de DocumentDB ! À la fin de ce didacticiel, vous disposerez d’une application console qui crée et interroge des ressources DocumentDB.

Nous allons aborder les points suivants :

- Création et connexion à un compte DocumentDB
- Configuration de votre solution Visual Studio
- Création d’une base de données
- Création d’une collection
- Création de documents JSON
- Interrogation de la collection
- Suppression de la base de données

Vous n’avez pas le temps ? Ne vous inquiétez pas ! La solution complète est disponible sur [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Pour obtenir des instructions rapides, consultez [Obtenir la solution complète](#GetSolution).

À la fin, utilisez les boutons de vote en haut ou en bas de cette page pour nous faire part de vos commentaires. Si vous souhaitez que nous vous contactions directement, n’hésitez pas à inclure votre adresse de messagerie dans vos commentaires.

Commençons dès maintenant !

## Composants requis

Vérifiez que vous disposez des éléments suivants :

- Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit des services Azure](http://azure.microsoft.com/pricing/free-trial/) dès aujourd’hui.
- [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/).

## Étape 1 : créer un compte DocumentDB

Créons un compte DocumentDB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre solution Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Étape 2 : configurer votre solution Visual Studio

1. Ouvrez **Visual Studio** sur votre ordinateur.
2. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis choisissez **Projet**.
3. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Modèles** / **Visual C#** / **Application console**, nommez votre projet, puis cliquez sur **OK**.
4. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur votre nouvelle application console, qui se trouve sous votre solution Visual Studio.
5. Ensuite, sans quitter le menu, cliquez sur **Gérer les packages NuGet...**
6. Dans le volet situé à l'extrême gauche de la fenêtre **Gérer les packages NuGet**, cliquez sur **En ligne** / **nuget.org**.
7. Dans la zone d'entrée **Rechercher en ligne**, recherchez **Bibliothèque cliente DocumentDB**.
8. Dans les résultats, recherchez **Bibliothèques cliente Microsoft Azure DocumentDB**, puis cliquez sur **Installer**. L’ID de package de la bibliothèque cliente DocumentDB est [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

Parfait ! L’installation étant terminée, nous pouvons passer à l’écriture du code.

##<a id="Connect"></a>Étape 3 : se connecter à un compte DocumentDB

Tout d’abord, ajoutez ces références au début de votre application C#, dans le fichier Program.cs :

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

Ensuite, enregistrez le point de terminaison du compte DocumentDB et la clé d’accès primaire ou secondaire, qui se trouve sur le [portail Azure en version préliminaire](https://portal.azure.com).

![Capture d’écran du portail Azure en version préliminaire, présentant un compte DocumentDB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance dans le panneau du compte DocumentDB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

Nous allons commencer l’application de démonstration de prise en main en créant une instance de **DocumentClient**. Créez une tâche asynchrone appelée **GetStartedDemo** et instanciez le nouveau **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
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

> [AZURE.WARNING]Ne stockez jamais d'informations d'identification dans du code source. Pour ne pas compliquer cet exemple, elles sont indiquées dans le code source. Pour plus d'informations sur le stockage des informations d'identification dans un environnement de production, consultez [Azure Web Sites : fonctionnement des chaînes d'application et de connexion](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Examinez notre exemple d'application sur [GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs) pour obtenir un exemple sur le stockage d'informations d'identification en dehors du code source.

Maintenant que vous savez comment vous connecter à un compte DocumentDB et comment créer une instance de la classe **DocumentClient**, voyons comment utiliser les ressources DocumentDB.

## Étape 4 : créer une base de données
Vous pouvez créer une [base de données](documentdb-resources.md#databases) à l'aide de la méthode [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la classe **DocumentClient**. Une base de données est le conteneur logique de stockage de documents partitionné entre les collections. Créez votre base de données dans votre méthode **GetStartedDemo** après avoir créé votre classe **DocumentClient**.

	// Check to verify a database with the id=FamilyRegistry does not exist
	Database database = client.CreateDatabaseQuery().Where(db => db.Id == "FamilyRegistry").AsEnumerable().FirstOrDefault();

	// If the database does not exist, create a new database
    if (database == null)
    {
    	database = await client.CreateDatabaseAsync(
    		new Database
            {
            	Id = "FamilyRegistry"
            });

		// Write the new database's id to the console
		Console.WriteLine(database.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateColl"></a>Étape 5 : créer une collection  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** crée une collection S1, ce qui a des conséquences tarifaires. Pour plus d'informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).

Vous pouvez créer une [collection](documentdb-resources.md#collections) à l'aide de la méthode [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la classe **DocumentClient**. Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. La collection nouvellement créée est mappée à un [niveau de performance S1](documentdb-performance-levels.md). Créez une collection nommée **FamilyCollection** après la création de votre base de données dans la méthode **GetStartedDemo**.

    // Check to verify a document collection with the id=FamilyCollection does not exist
    DocumentCollection documentCollection = client.CreateDocumentCollectionQuery("dbs/" + database.Id).Where(c => c.Id == "FamilyCollection").AsEnumerable().FirstOrDefault();

	// If the document collection does not exist, create a new collection
    if (documentCollection == null)
    {
    	documentCollection = await client.CreateDocumentCollectionAsync("dbs/" + database.Id,
        	new DocumentCollection
            {
            	Id = "FamilyCollection"
            });

		// Write the new collection's id to the console
		Console.WriteLine(documentCollection.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateDoc"></a>Étape 6 : créer des documents
Vous pouvez créer un [document](documentdb-resources.md#documents) à l'aide de la méthode [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la classe **DocumentClient**. Les documents correspondent à du contenu JSON (arbitraire) défini par l'utilisateur. Nous pouvons maintenant insérer un ou plusieurs documents. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser de l’[outil de migration de données](documentdb-import-data.md) de DocumentDB.

Tout d'abord, nous devons créer les classes **Parent**, **Child**, **Pet**, **Address** et **Family**. Créez ces classes en ajoutant les sous-classes internes suivantes après la méthode **GetStartedDemo**.

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

Ensuite, créez vos documents dans votre méthode asynchrone **GetStartedDemo**.

    // Check to verify a document with the id=AndersenFamily does not exist
    Document document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "AndersenFamily").AsEnumerable().FirstOrDefault();

	// If the document does not exist, create a new document
	if (document == null)
	{
	    // Create the Andersen Family document
	    Family andersonFamily = new Family
	    {
	        Id = "AndersenFamily",
	        LastName = "Andersen",
	        Parents = new Parent[] {
	            new Parent { FirstName = "Thomas" },
	            new Parent { FirstName = "Mary Kay"}
	        },
	        Children = new Child[] {
	            new Child
	            { 
	                FirstName = "Henriette Thaulow", 
	                Gender = "female", 
	                Grade = 5, 
	                Pets = new Pet[] {
	                    new Pet { GivenName = "Fluffy" } 
	                }
	            } 
	        },
	        Address = new Address { State = "WA", County = "King", City = "Seattle" },
	        IsRegistered = true
	    };
	
	    await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, andersonFamily);
	}

    // Check to verify a document with the id=AndersenFamily does not exist
    document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "WakefieldFamily").AsEnumerable().FirstOrDefault();

    if (document == null)
    {
        // Create the WakeField document
        Family wakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new Parent[] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, wakefieldFamily);
	}

La base de données, la collection et les documents suivants sont maintenant créés dans votre compte DocumentDB.

![Diagramme illustrant la relation hiérarchique entre le compte, la base de données, la collection et les documents](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>Étape 7 : interroger les ressources DocumentDB

DocumentDB prend en charge les [requêtes](documentdb-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection. L'exemple de code suivant affiche différentes requêtes (à l'aide de la syntaxe SQL de DocumentDB et de LINQ) que nous pouvons exécuter sur les documents insérés à l'étape précédente. Ajoutez ces requêtes à votre méthode asynchrone **GetStartedDemo**.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

Le diagramme suivant montre comment la syntaxe de requête SQL de DocumentDB est appelée sur la collection que vous avez créée. C'est par ailleurs cette même logique qui s'applique à la requête LINQ.

![Diagramme illustrant l'étendue et la signification de la requête](./media/documentdb-get-started/collection-documents.png)

Le mot clé [FROM](documentdb-sql-query.md#from-clause) est facultatif dans la requête, car les requêtes DocumentDB sont déjà étendues à une collection unique. Par conséquent, « FROM Families f » peut être remplacé par «FROM root r » ou par tout autre nom de variable que vous choisissez. DocumentDB déduira que Families, root ou le nom de variable choisi fait par défaut référence à la collection actuelle.

##<a id="DeleteDatabase"></a>Étape 8 : supprimer la base de données.

Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (collections, documents, etc.). Vous pouvez supprimer la base de données et la classe DocumentClient en ajoutant l'extrait de code suivant à la fin de votre méthode asynchrone **GetStartedDemo**.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync("dbs/" + database.Id);
	client.Dispose();

##<a id="Run"></a>Étape 9 : exécuter votre application

Vous êtes maintenant en mesure d'exécuter votre application. À la fin de votre méthode **Main**, ajoutez la ligne de code suivante, ce qui vous permettra de lire la sortie de la console avant la fin d'exécution de l'application.

	Console.ReadLine();

Appuyez à présent sur F5 dans Visual Studio pour générer l'application en mode débogage.

La sortie de votre application de prise en main doit maintenant s'afficher. Celle-ci doit présenter les résultats des requêtes que nous avons ajoutées, qui doivent correspondre au texte d'exemple ci-dessous.

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query

Félicitations ! Vous venez de créer votre première application DocumentDB !

##<a id="GetSolution"></a> Obtenir la solution complète
Pour générer la solution GetStarted qui contient tous les exemples de cet article, vous devez avoir les éléments suivants :

-   [Un compte DocumentDB][documentdb-create-account].
-   La solution [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) disponible sur GitHub.

Pour restaurer les références au Kit de développement logiciel (SDK) .NET de DocumentDB dans Visual Studio, cliquez avec le bouton droit sur la solution **GetStarted** dans l’Explorateur de solutions, puis cliquez sur **Activer la restauration des packages NuGet**. Ensuite, dans le fichier App.config, mettez à jour les valeurs pour EndpointUrl et AuthorizationKey comme décrit à la section [Se connecter à un compte DocumentDB](#Connect).

## Étapes suivantes

-   Vous voulez un exemple ASP.NET MVC plus complexe ? Consultez [Création d'une application web avec ASP.NET MVC et DocumentDB](documentdb-dotnet-application.md).
-	Apprenez à [surveiller un compte DocumentDB](documentdb-monitor-accounts.md).
-	Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
-	Consultez la section Développement de la [page de documentation DocumentDB](../../services/documentdb/) pour découvrir plus en détail le modèle de programmation.

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=Sept15_HO4-->