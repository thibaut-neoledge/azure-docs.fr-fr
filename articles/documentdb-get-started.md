<properties 
	pageTitle="Prendre en main le Kit de développement logiciel (SDK) .NET de DocumentDB | Azure" 
	description="Learn how to create and configure an Azure DocumentDB account, create databases, create collections, and store JSON documents within your NoSQL document database account." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/12/2014" 
	ms.author="mimig"/>

#Prendre en main le Kit de développement logiciel (SDK) .NET de DocumentDB  

Ce guide vous montre comment prendre en main [**Microsoft Azure DocumentDB (version préliminaire)**](https://portal.azure.com/#gallery/Microsoft.DocumentDB) et le Kit de développement logiciel (SDK) .NET de DocumentDB. DocumentDB est un service de base de données NoSQL orienté documents, disposant d'un certain nombre d'API et de Kits de développement logiciel (SDK). Les exemples de code de cet article sont écrits en C# et utilisent le [Kit de développement logiciel (SDK) .NET de DocumentDB](http://go.microsoft.com/fwlink/p/?linkid=402989), qui est proposé et distribué sous forme de package NuGet. 

Les scénarios couverts dans cet article incluent la création et la configuration d'un compte DocumentDB, la création de bases de données et de collections, et le stockage de documents JSON dans le compte. Chacun de ces exemples fait partie d'une solution complète disponible sur [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Vous pouvez [télécharger la solution](#GetSolution) pour afficher l'exemple de code dans le contexte, ou vous pouvez simplement consulter les exemples de l'article.

##Sommaire

-	[Se connecter à un compte DocumentDB](#Connect)
-	[Créer une base de données](#CreateDB)
-	[Créer une collection](#CreateColl)
-	[Créer un document](#CreateDoc)
-	[Interroger les ressources DocumentDB](#Query)
-	[Obtenir la solution complète](#GetSolution)
-	[Étapes suivantes](#NextSteps)

##<a id="Connect"></a>Se connecter à un compte DocumentDB

Nous allons commencer par créer une instance de la classe **DocumentClient** afin d'établir une connexion à notre compte DocumentDB.   Nous devons suivre les références suivantes dans notre application C# :  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
Une classe **DocumentClient** peut être instanciée à l'aide du point de terminaison du compte DocumentDB et de la clé d'accès primaire ou secondaire associée au compte.  

Le point de terminaison du compte DocumentDB et les clés peuvent être obtenus à partir du panneau de la [version préliminaire du portail de gestion Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) de votre compte DocumentDB. 

![][1]
 
>Remarque : les clés d'accès DocumentDB disponibles dans le panneau Clés octroient un accès administratif à votre compte DocumentDB et à toutes ses ressources.  DocumentDB prend également en charge l'utilisation de clés de ressource qui permettent aux clients de lire, écrire et supprimer des ressources dans le compte DocumentDB conformément aux autorisations que vous leur avez accordées, et sans qu'une clé de compte ne soit nécessaire.    

Créez le client en utilisant du code comme dans l'exemple suivant.  

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

**Avertissement :** ne jamais stocker les informations d'identification dans le code source. Dans le cadre de cet exemple, les informations d'identification sont toutefois affichées dans le code source. Pour plus d'informations sur le stockage des informations d'identification, consultez [Sites Web Windows Azure : fonctionnement des chaînes d'application et des chaînes de connexion](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

Maintenant que vous savez vous connecter à un compte DocumentDB et créer une instance de **DocumentClient**, voyons comment utiliser les ressources DocumentDB.  

##<a id="CreateDB"></a>Créer une base de données
À l'aide du Kit de développement logiciel (SDK) .NET de DocumentDB, une base de données DocumentDB peut être créée via la méthode **CreateDatabaseAsync** d'un DocumentClient.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Créer une collection  

À l'aide du Kit de développement logiciel (SDK) .NET de DocumentDB, une collection DocumentDB peut être créée via la méthode **CreateDocumentCollectionAsync** d'un DocumentClient.  La base de données créée à l'étape précédente possède plusieurs propriétés, dont **CollectionsLink**.  Grâce à ces informations, nous pouvons maintenant créer une collection.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>Création de documents	
À l'aide du Kit de développement logiciel (SDK) .NET de DocumentDB, un document DocumentDB peut être créé via la méthode **CreateDocumentAsync** d'un DocumentClient.  La collection créée à l'étape précédente possède plusieurs propriétés, dont **DocumentsLink**.  Grâce à ces informations, nous pouvons maintenant insérer un ou plusieurs documents.  Pour cet exemple, nous imaginerons que nous disposons d'une classe Family décrivant les attributs d'une famille, notamment le nom, le sexe et l'âge.  

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
DocumentDB prend en charge des requêtes enrichies destinées aux documents JSON stockés dans chaque collection.  L'exemple de code suivant affiche différentes requêtes (à l'aide de la syntaxe SQL de DocumentDB et de LINQ) que nous pouvons exécuter sur les documents insérés à l'étape précédente.  

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

-   [Compte DocumentDB][documentdb-create-account].
-   La solution [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) disponible sur GitHub. 

Pour restaurer les références au Kit de développement logiciel (SDK) .NET de DocumentDB dans Visual Studio 2013, cliquez avec le bouton droit sur la solution GetStarted dans l'Explorateur de solutions, puis cliquez sur Activer la restauration des packages NuGet, qui permet de restaurer les références. 

##<a id="NextSteps"></a>Étapes suivantes
-	Découvrez comment [surveiller un compte DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Pour plus d'informations sur le modèle de programmation, consultez la section Développement de la [page de documentation DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).


[Se connecter à un compte DocumentDB]: #Connect
[Créer une base de données]: #CreateDB
[Créer une collection]: #CreateColl
[Créer un document]: #CreateDoc
[Interroger les ressources DocumentDB]: #Query
[Étapes suivantes]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: ../documentdb-create-account/
[documentdb-manage]: ../documentdb-manage/

[1]: ./media/documentdb-get-started/gs1.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
