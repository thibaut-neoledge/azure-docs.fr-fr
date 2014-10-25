<properties title="Get started with a DocumentDB account" pageTitle="Get started with a DocumentDB account | Azure" description="Learn how to create and configure an Azure DocumentDB account, create databases, create collections, and store JSON documents within the account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="paulettm" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Prise en main d'un compte DocumentDB

Ce guide décrit la prise en main d'**Azure DocumentDB (version préliminaire)**. Les exemples sont écrits en C\# et utilisent le Kit de développement logiciel (SDK) .NET de DocumentDB. Les scénarios couverts incluent la création et la configuration d'un compte DocumentDB, la création de bases de données et de collections, ainsi que le stockage de documents JSON dans le compte. Pour plus d'informations sur l'utilisation d'Azure DocumentDB, consultez la section Étapes suivantes.

Afin d'utiliser ce guide de prise en main, vous devez posséder un compte DocumentDB et une clé d'accès (primaire ou secondaire) pour le compte. Pour plus d'informations, consultez les pages suivantes :

-   [Création d'un compte DocumentDB][Création d'un compte DocumentDB]

## Sommaire

-   [Connexion à un compte DocumentDB][Connexion à un compte DocumentDB]
-   [Création d'une base de données][Création d'une base de données]
-   [Création d'une collection][Création d'une collection]
-   [Création de documents][Création de documents]
-   [Interrogation de ressources DocumentDB][Interrogation de ressources DocumentDB]
-   [Étapes suivantes][Étapes suivantes]

## <span id="Connect"></span></a>Connexion à un compte DocumentDB

Différents Kits de développement logiciel (SDK) et API fonctionnent par programme avec DocumentDB. Les exemples ci-dessous sont écrits en C\# et utilisent le Kit de développement logiciel (SDK) .NET de DocumentDB.

Commençons par la création d'un DocumentClient afin d'établir une connexion à notre compte DocumentDB. Nous devons suivre les références suivantes dans notre application C\# :

    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Microsoft.Azure.Documents;  

Un DocumentClient peut être créé à l'aide du point de terminaison de compte DocumentDB et d'une clé d'accès primaire ou secondaire associée au compte.

Le point de terminaison du compte DocumentDB et les clés peuvent être obtenus dans le volet de la version préliminaire du portail de gestion Azure pour votre compte DocumentDB.

![][]

> Remarque : les clés d'accès DocumentDB disponibles dans le volet Clés octroient un accès administratif à votre compte DocumentDB et à toutes ses ressources. DocumentDB prend également en charge l'utilisation de clés de ressource qui permettent aux clients de lire, d'écrire et de supprimer des ressources dans le compte DocumentDB conformément aux autorisations que vous leur avez accordées, et sans qu'une clé de compte soit nécessaire.

Créez le client en utilisant du code comme dans l'exemple suivant.

    private static string endpointUrl = "<your endpoint URI>";
    private static string authorizationKey = "<your key>";

    //Create a new instance of the DocumentClient
    var client = new DocumentClient(new Uri(endpointUrl), authorizationKey);  

**Avertissement :** ne stockez jamais d'informations d'identification dans du code source. Pour ne pas compliquer cet exemple, elles sont indiquées dans le code source. Consultez la rubrique [Sites web Windows Azure : fonctionnement des chaînes d'application et de connexion][Sites web Windows Azure : fonctionnement des chaînes d'application et de connexion] pour plus d'informations sur le stockage des informations d'identification.

Maintenant que vous savez vous connecter à un compte DocumentDB et créer une instance de DocumentClient, voyons comment travailler avec des ressources DocumentDB.

## <span id="CreateDB"></span></a>Création d'une base de données

En utilisant le Kit de développement logiciel (SDK) .NET, une base de données DocumentDB peut être créée via la méthode CreateDatabaseAsync d'un DocumentClient.

    //Create a Database
     Database database = await client.CreateDatabaseAsync(
        new Database
        {
        Id = "FamilyRegistry"
        });

## <span id="CreateColl"></span></a>Création d'une collection

En utilisant le Kit de développement logiciel (SDK) .NET, une collection DocumentDB peut être créée via la méthode CreateDocumentCollectionAsync d'un DocumentClient. La base de données créée précédemment possède plusieurs propriétés, l'une d'elles étant SelfLink. Grâce à ces informations, nous pouvons maintenant créer une collection.

        //Create a document collection 
    documentCollection = new DocumentCollection
        {
            Id = "FamilyCollection"
        };

        documentCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,documentCollection); 

## <span id="CreateDoc"></span></a>Création de documents

En utilisant le Kit de développement logiciel (SDK) .NET, un document DocumentDB peut être créé via la méthode CreateDocumentAsync d'un DocumentClient. La collection créée précédemment possède plusieurs propriétés, l'une d'elles étant DocumentsLink. Grâce à ces informations, nous pouvons maintenant insérer un ou plusieurs documents. Pour cet exemple, nous imaginerons que nous disposons d'une classe Family décrivant les attributs d'une famille, notamment le nom, le sexe et l'âge.

    private static async Task CreateDocuments(string    colSelfLink)
    {
        Family AndersonFamily = new Family
        {
            Id = "AndersenFamily",
            LastName = "Andersen",
            Parents =  new Parent[] {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay"}
            },
            Children = new Child[] {
                new Child
                { 
                    FirstName = "Henriette Thaulow", 
                    Gender = "female", 
                    Grade = 5, 
                    Pets = new [] {
                        new Pet { GivenName = "Fluffy" } 
                    }
                } 
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
        };

        await client.CreateDocumentAsync(colSelfLink, AndersonFamily);

        Family WakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new [] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child
                {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child
                {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        await client.CreateDocumentAsync(colSelfLink, WakefieldFamily);



## <span id="Query"></span></a>Interrogation de ressources DocumentDB

DocumentDB prend en charge des requêtes enrichies destinées aux documents JSON stockés dans chaque collection. L'exemple de code ci-dessous affiche différentes requêtes (utilisant la syntaxe SQL de DocumentDB et LINQ) que nous pouvons exécuter sur les documents insérés précédemment.

    //
    //Querying the documents using DocumentDB SQL for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink, 
    "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
    Console.WriteLine("\tRead {0} from SQL", family);
    }

    //
    //Querying the documents using LINQ for the Andersen family
    //
    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
     Console.WriteLine("\tRead {0} from LINQ", family);
    }

    //
    //Querying the documents using LINQ lambdas for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink)
    .Where(f => f.Id == "AndersenFamily")
    .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    //
    //DocumentDB SQL -  using <> interchangably with != for "not equals"
    //
    families = client.CreateDocumentQuery<Family>(colSelfLink, "SELECT * FROM Families f WHERE f.id <> 'AndersenFamily'");

    //   
    // LINQ - combine equality and inequality
    //
    families = from f in client.CreateDocumentQuery<Family>(colSelfLink)
           where f.Id == "Wakefield" && f.Address.City != "NY"
           select f; 

## <span id="NextSteps"></span></a>Étapes suivantes

-   Apprenez à [surveiller un compte DocumentDB][surveiller un compte DocumentDB].
-   Pour plus d'informations sur le modèle de programmation, consultez la section Développement de la [page de documentation DocumentDB][page de documentation DocumentDB].

  [Création d'un compte DocumentDB]: ../documentdb-create-account/
  [Connexion à un compte DocumentDB]: #Connect
  [Création d'une base de données]: #CreateDB
  [Création d'une collection]: #CreateColl
  [Création de documents]: #CreateDoc
  [Interrogation de ressources DocumentDB]: #Query
  [Étapes suivantes]: #NextSteps
  []: ./media/documentdb-get-started/gs1.png
  [Sites web Windows Azure : fonctionnement des chaînes d'application et de connexion]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [surveiller un compte DocumentDB]: http://go.microsoft.com/fwlink/p/?LinkId=402378
  [page de documentation DocumentDB]: http://go.microsoft.com/fwlink/p/?LinkID=402319
