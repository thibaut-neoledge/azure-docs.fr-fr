<properties 
	pageTitle="Prendre en main le Kit de développement logiciel (SDK) .NET de DocumentDB | Azure" 
	description="Découvrez comment créer et configurer un compte Azure DocumentDB, créer des bases de données et des collections, et stocker des documents JSON dans votre compte de base de données de documents NoSQL." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="anhoh"/>

#Prendre en main le Kit de développement logiciel (SDK) .NET de DocumentDB  

Ce guide vous montre comment prendre en main [Microsoft Azure DocumentDB (version préliminaire)](https://portal.azure.com/#gallery/Microsoft.DocumentDB) et le [Kit de développement logiciel (SDK) .NET de DocumentDB](https://go.microsoft.com/fwlink/p/?linkid=402989). DocumentDB est un service de base de données NoSQL orienté documents, disposant d'un [certain nombre d'API et de Kits de développement logiciel (SDK)](https://go.microsoft.com/fwlink/p/?linkid=522476). Les exemples de code de cet article sont écrits en C# et utilisent le Kit de développement logiciel (SDK) .NET de DocumentDB, qui est proposé et distribué sous forme de package NuGet. 

Les scénarios couverts dans cet article incluent la création et la configuration d'un compte DocumentDB, la création de bases de données et de collections, et le stockage de documents JSON dans le compte. Chacun de ces exemples fait partie d'une solution complète disponible sur [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Vous pouvez [télécharger la solution](#GetSolution) pour afficher l'exemple de code dans le contexte, ou vous pouvez simplement consulter les exemples de l'article.

##<a id="CreateAccount"></a>Création d'un compte DocumentDB

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

##<a id="Connect"></a>Connexion à un compte DocumentDB

Nous allons commencer par créer une instance de la classe [DocumentClient](https://go.microsoft.com/fwlink/p/?linkid=522477) afin d'établir une connexion à notre compte DocumentDB. Nous devons suivre les références suivantes dans notre application C# :  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
Une classe **DocumentClient** peut être instanciée à l'aide du point de terminaison du compte DocumentDB et de la clé d'accès primaire ou secondaire associée au compte. Créez le client à l'aide d'un code semblable à celui-ci :   

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

> [AZURE.WARNING] Ne stockez jamais d'informations d'identification dans du code source. Pour ne pas compliquer cet exemple, elles sont indiquées dans le code source. Consultez la page [Sites web Azure : fonctionnement des chaînes d'application et de connexion](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) pour plus d'informations sur le stockage des informations d'identification dans un environnement de production. 

Les valeurs de EndpointUrl et AuthorizationKey sont l'URI et la CLÉ PRIMAIRE de votre compte DocumentDB, qui peuvent être obtenues à partir du [portail de gestion Azure en version préliminaire](https://portal.azure.com) correspondant à votre compte DocumentDB. 

![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the KEYS button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade][1]
 
Ces clés accordent un accès administratif à votre compte DocumentDB et aux ressources qu'il contient. DocumentDB prend également en charge l'utilisation de clés de ressource qui permettent aux clients de lire, écrire et supprimer des ressources dans le compte DocumentDB conformément aux autorisations que vous leur avez accordées, et sans qu'une clé de compte ne soit nécessaire. Pour plus d'informations sur les clés de ressources, consultez la section Autorisations de l'article [Modèle de ressource et concepts de DocumentDB](documentdb-resources.md).

Maintenant que vous savez vous connecter à un compte DocumentDB et créer une instance de la classe **DocumentClient**, voyons comment utiliser les ressources DocumentDB.  

##<a id="CreateDB"></a>Créer une base de données
Vous pouvez créer une base de données à l'aide de la méthode [CreateDatabaseAsync](https://go.microsoft.com/fwlink/p/?linkid=522478) de la classe **DocumentClient**.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Créer une collection  

Vous pouvez créer une collection à l'aide de la méthode [CreateDocumentCollectionAsync](https://go.microsoft.com/fwlink/p/?linkid=522479) de la classe **DocumentClient**.  La base de données créée à l'étape précédente possède plusieurs propriétés, dont [CollectionsLink](https://go.microsoft.com/fwlink/p/?linkid=522481).  Grâce à ces informations, nous pouvons maintenant créer une collection.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>Créer des documents	
Vous pouvez créer un document à l'aide de la méthode [CreateDocumentAsync](https://go.microsoft.com/fwlink/p/?linkid=522482) de la classe **DocumentClient**.  La collection créée à l'étape précédente possède plusieurs propriétés, dont [DocumentsLink](https://go.microsoft.com/fwlink/p/?linkid=522483).  Grâce à ces informations, nous pouvons maintenant insérer un ou plusieurs documents.  Pour cet exemple, nous imaginerons que nous disposons d'une classe Family décrivant les attributs d'une famille, notamment le nom, le sexe et l'âge. Pour voir notre exemple de classe Family, visitez notre [référentiel GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs). 

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
 

##<a id="Query"></a>Interroger les ressources DocumentDB

DocumentDB prend en charge les requêtes enrichies sur des documents JSON stockés dans chaque collection.  L'exemple de code suivant affiche différentes requêtes (à l'aide de la syntaxe SQL de DocumentDB et de LINQ) que nous pouvons exécuter sur les documents insérés à l'étape précédente. 

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

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
	
##<a id="GetSolution"></a>Obtenir la solution complète
Pour générer la solution GetStarted qui contient tous les exemples de cet article, vous devez avoir les éléments suivants :

-   un [compte DocumentDB][documentdb-create-account]
-   La solution [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) disponible sur GitHub 

Pour restaurer les références au Kit de développement logiciel (SDK) .NET de DocumentDB dans Visual Studio 2013, cliquez avec le bouton droit sur la solution GetStarted dans l'Explorateur de solutions, puis cliquez sur Activer la restauration des packages NuGet. Ensuite, dans le fichier App.config, mettez à jour les valeurs EndpointUrl et AuthorizationKey comme décrit à la section [Connexion à un compte DocumentDB](#Connect). 

##<a id="NextSteps"></a>Étapes suivantes
-	Découvrez comment [surveiller un compte DocumentDB](https://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](http://www.documentdb.com/sql/demo).
-	Consultez la section Développement de la [page de documentation DocumentDB](../documentation/services/documentdb/) pour obtenir plus d'informations sur le modèle de programmation.


[Connexion à un compte DocumentDB]: #Connect
[Créer une base de données]: #CreateDB
[Créer une collection]: #CreateColl
[Créer des documents]: #CreateDoc
[Interroger les ressources DocumentDB]: #Query
[Étapes suivantes]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[1]: ../includes/media/documentdb-keys/keys.png


<!--HONumber=49-->