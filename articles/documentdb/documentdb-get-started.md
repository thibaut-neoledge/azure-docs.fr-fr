<properties
	pageTitle="Prendre en main le Kit de développement logiciel (SDK) .NET de DocumentDB | Azure"
	description="Découvrez comment créer et configurer un compte Azure DocumentDB, créer des bases de données et des collections, et stocker des documents JSON dans votre compte de base de données de documents NoSQL."
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
	ms.topic="get-started-article" 
	ms.date="05/19/2015"
	ms.author="anhoh"/>

#Prendre en main le Kit de développement logiciel (SDK) .NET de DocumentDB  

Ce didacticiel vous montre comment prendre en main [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) et le [Kit de développement logiciel (SDK) .NET de DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/). Vous allez générer une application console chargée de créer et d'interroger les ressources DocumentDB, puis d'écrire la sortie dans la fenêtre de console.

DocumentDB est un service de base de données NoSQL orienté documents, qui propose un certain [nombre d'API et de Kits de développement logiciel (SDK)](https://msdn.microsoft.com/library/dn781482.aspx). Le code de cet article est écrit en C# et utilise le Kit de développement logiciel (SDK) .NET de DocumentDB, qui est proposé et distribué sous forme de package NuGet.

Les scénarios traités dans cet article sont les suivants :

- Création et connexion à un compte DocumentDB
- Ajout de DocumentDB à votre solution Visual Studio
- Création de bases de données
- Création de collections
- Création de documents JSON
- Interrogation de ressources
- Suppression de bases de données

Vous n'avez pas le temps de terminer le didacticiel et vous souhaitez simplement obtenir la solution exploitable ? Pas d'inquiétudes. La solution complète est disponible sur [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Pour obtenir des instructions rapides, consultez [Obtenir la solution complète](#GetSolution).

## Configuration requise

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

- Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/) Update 4 ou version ultérieure.

## Étape 1 : créer un compte DocumentDB

Commençons par créer un compte DocumentDB. Si vous avez déjà un compte, vous pouvez passer à l'étape [Configurer votre solution Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Étape 2 : configurer votre solution Visual Studio

1. Ouvrez **Visual Studio** sur votre ordinateur.
2. Sélectionnez **Nouveau** dans le menu **Fichier** et choisissez **Projet**.
3. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Modèles** / **Visual C#** / **Application console**, nommez votre projet, puis cliquez sur **Ajouter**.
4. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur votre nouvelle application console, qui se trouve sous votre solution Visual Studio.
5. Ensuite, sans quitter le menu, cliquez sur **Gérer les packages NuGet...**
6. Dans le volet situé à l'extrême gauche de la fenêtre **Gérer les packages NuGet**, cliquez sur **En ligne** / **nuget.org**.
7. Dans la zone d'entrée **Rechercher en ligne**, recherchez **Bibliothèque cliente DocumentDB**.
8. Dans les résultats, recherchez **Bibliothèques cliente Microsoft Azure DocumentDB**, puis cliquez sur **Installer**. L’ID de package de la bibliothèque cliente DocumentDB est [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

Parfait ! Vous êtes maintenant en mesure d'utiliser DocumentDB.

##<a id="Connect"></a>Étape 3 : se connecter à un compte DocumentDB

Nous allons commencer par créer une instance de la classe [DocumentClient](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.aspx) dans le but d'établir une connexion à notre compte DocumentDB. Nous aurons besoin des références suivantes au début de notre application C# :

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

Ensuite, une classe **DocumentClient** peut être instanciée à l'aide du point de terminaison du compte DocumentDB et de la clé d'accès primaire ou secondaire associée au compte. Ajoutez ces propriétés à votre classe.

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

Créons à présent une tâche asynchrone appelée **GetStartedDemo** dans votre classe. Dans cette nouvelle tâche, créons et configurons votre classe **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient.
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Appelons la tâche asynchrone à partir de la méthode Main, comme dans le code ci-dessous.

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

EndpointUrl et AuthorizationKey ont pour valeur l'URI et la CLÉ PRIMAIRE de votre compte DocumentDB, qui peuvent être obtenues dans le panneau [Clés](https://portal.azure.com) de votre compte DocumentDB.

![Capture d’écran du portail Azure en version préliminaire, présentant un compte DocumentDB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance dans le panneau du compte DocumentDB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés][keys]

Ces clés accordent un accès administratif à votre compte DocumentDB et aux ressources qu'il contient. DocumentDB prend également en charge l'utilisation de clés de ressource qui permettent aux clients de lire, écrire et supprimer des ressources dans le compte DocumentDB conformément aux autorisations que vous leur avez accordées, et sans qu'une clé de compte ne soit nécessaire. Pour plus d'informations sur les clés de ressources, consultez [Autorisations](documentdb-resources.md#permissions) et [Affichage, copie et régénération de clés d'accès](documentdb-manage-account.md#keys).

Maintenant que vous savez comment vous connecter à un compte DocumentDB et comment créer une instance de la classe **DocumentClient**, voyons comment utiliser les ressources DocumentDB.

## Étape 4 : créer une base de données
Vous pouvez créer une [base de données](documentdb-resources.md#databases) à l'aide de la méthode [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la classe **DocumentClient**. Une base de données est le conteneur logique de stockage de documents partitionné entre les collections. Créez votre base de données dans votre méthode **GetStartedDemo** après avoir créé votre classe **DocumentClient**.

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Étape 5 : créer une collection  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** crée une collection S1, ce qui a des conséquences tarifaires. Pour plus d'informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).

Vous pouvez créer une [collection](documentdb-resources.md#collections) à l'aide de la méthode [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la classe **DocumentClient**. Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. La collection nouvellement créée est mappée à un [niveau de performance S1](documentdb-performance-levels.md). La base de données créée à l'étape précédente possède plusieurs propriétés, l'une d'elles étant [CollectionsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.database.collectionslink.aspx). Grâce à ces informations, nous pouvons maintenant créer une collection après avoir créé notre base de données.

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });

##<a id="CreateDoc"></a>Étape 6 : créer des documents
Vous pouvez créer un [document](documentdb-resources.md#documents) à l'aide de la méthode [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la classe **DocumentClient**. Les documents correspondent à du contenu JSON (arbitraire) défini par l'utilisateur. La collection créée à l'étape précédente possède plusieurs propriétés, l'une d'elles étant [DocumentsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.documentcollection.documentslink.aspx). Grâce à ces informations, nous pouvons maintenant insérer un ou plusieurs documents. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser de l’[outil de migration de données](documentdb-import-data.md) de DocumentDB.

Tout d'abord, nous devons créer les classes **Parent**, **Child**, **Pet**, **Address** et **Family**. Pour cela, ajoutons les sous-classes internes suivantes.

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

    // Create the Andersen family document.
	Family AndersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents =  new Parent[] {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay"}
        },
        Children = new Child[] {
            new Child {
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, AndersenFamily);

    // Create the WakeField family document.
    Family WakefieldFamily = new Family
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, WakefieldFamily);

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

    // Query the documents using DocumentSQL with one join.
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join.
    items = client.CreateDocumentQuery<Family>(documentCollection.DocumentsLink)
        .SelectMany(family => family.Children
            .Select(children => new
            {
                family = family.Id,
                child = children.FirstName
            }));

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

Le diagramme suivant montre comment la syntaxe de requête SQL de DocumentDB est appelée sur la collection que vous avez créée. C'est par ailleurs cette même logique qui s'applique à la requête LINQ.

![Diagramme illustrant l'étendue et la signification de la requête](./media/documentdb-get-started/collection-documents.png)

Le mot clé [FROM](documentdb-sql-query.md/#from-clause) est facultatif dans la requête, car les requêtes DocumentDB sont déjà étendues à une collection unique. Par conséquent, « FROM Families f » peut être remplacé par «FROM root r » ou par tout autre nom de variable que vous choisissez. DocumentDB déduira que Families, root ou le nom de variable choisi fait par défaut référence à la collection active.

##<a id="DeleteDatabase"></a>Étape 8 : supprimer la base de données.

Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (collections, documents, etc.). Vous pouvez supprimer la base de données et la classe DocumentClient en ajoutant l'extrait de code suivant à la fin de votre méthode asynchrone **GetStartedDemo**.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync(database.SelfLink);
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
	{
	  "id": "AndersenFamily",
 	  "child": "Henriette Thaulow"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Jesse"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Lisa"
	}
	{ family = AndersenFamily, child = Henriette Thaulow }
	{ family = WakefieldFamily, child = Jesse }
	{ family = WakefieldFamily, child = Lisa }


> [AZURE.NOTE]Si vous exécutez l'application plusieurs fois sans supprimer la base de données, vous risquez de créer une base de données avec un ID déjà utilisé. Pour éviter ce problème, vérifiez qu'il n'existe pas déjà une base de données, une collection ou un document avec ce même ID. Pour savoir comment procéder, consultez notre [page GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started).

##<a id="GetSolution"></a> Obtenir la solution complète
Pour générer la solution GetStarted qui contient tous les exemples de cet article, vous devez avoir les éléments suivants :

-   [Un compte DocumentDB][documentdb-create-account].
-   La solution [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) disponible sur GitHub.

Pour restaurer les références au Kit de développement logiciel (SDK) .NET de DocumentDB dans Visual Studio 2013, cliquez avec le bouton droit sur la solution **GetStarted** dans l'Explorateur de solutions, puis cliquez sur **Activer la restauration des packages NuGet**. Ensuite, dans le fichier App.config, mettez à jour les valeurs pour EndpointUrl et AuthorizationKey comme décrit à la section [Se connecter à un compte DocumentDB](#Connect).

## Étapes suivantes
-   Vous voulez un exemple ASP.NET MVC plus complexe ? Consultez [Création d'une application web avec ASP.NET MVC et DocumentDB](documentdb-dotnet-application.md).
-	Apprenez à [surveiller un compte DocumentDB](documentdb-monitor-accounts.md).
-	Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
-	Consultez la section Développement de la [page de documentation DocumentDB](../../services/documentdb/) pour découvrir plus en détail le modèle de programmation.

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=July15_HO4-->