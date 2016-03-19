<properties
    pageTitle="Interroger un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET | Microsoft Azure | Service de recherche de cloud hébergé"
    description="Créez une requête de recherche dans Azure Search et utilisez des paramètres de recherche pour filtrer et trier les résultats de recherche."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="brjohnst"/>

# Interroger un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET
> [AZURE.SELECTOR]
- [Vue d'ensemble](search-query-overview.md)
- [Explorateur de recherche](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Cet article explique comment interroger un index à l’aide du [Kit de développement logiciel (SDK) .NET Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx). Avant de commencer cette procédure, vous devez déjà avoir [créé un index Azure Search](search-create-index-dotnet.md) et l’avoir [rempli de données](search-import-data-dotnet.md).

Notez que tous les exemples de code figurant dans cet article sont écrits en C#. L’intégralité du code source est disponible [sur GitHub](http://aka.ms/search-dotnet-howto).

## I. Identifier la clé API de requête de votre service Azure Search
Maintenant que vous avez créé un index Azure Search, vous êtes presque prêt à générer des requêtes à l’aide du Kit de développement logiciel (SDK) .NET. Tout d’abord, vous devez obtenir l’une des clés API de requête qui a été générée pour le service de recherche que vous avez configuré. À chaque demande, le Kit de développement logiciel (SDK) .NET envoie la clé API à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

1. Pour accéder aux clés API de votre service, vous devez vous connecter au [portail Azure](https://portal.azure.com/)
2. Accédez au panneau de votre service Azure Search
3. Cliquez sur l’icône « Clés »

Votre service comporte des *clés d’administration* et des *clés de requête*.

  - Vos *clés d’administration* primaire et secondaire accordent des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service, de créer et supprimer des index, des indexeurs et des sources de données. Deux clés sont à votre disposition afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et inversement.
  - Vos *clés de requête* accordent l’accès en lecture seule aux index et aux documents. Elles sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.

Dans le cadre de l’interrogation d’un index, vous pouvez utiliser l’une de vos clés de requête. Vos clés d’administration peuvent également servir pour les requêtes, mais il est recommandé d’utiliser une clé de requête dans votre code d’application, afin de respecter le [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## II. Créer une instance de la classe SearchIndexClient
Pour générer des requêtes avec le Kit de développement logiciel (SDK) .NET Azure Search, vous devez créer une instance de la classe `SearchIndexClient`. Cette classe dispose de plusieurs constructeurs. Celui qui vous intéresse prend le nom du service de recherche, le nom de l’index et un objet `SearchCredentials` en tant que paramètres. `SearchCredentials` encapsule votre clé API.

Le code suivant crée un `SearchIndexClient` pour l’index « hotels » [créé dans [Créer un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-create-index-dotnet.md)] en utilisant des valeurs pour le nom de service de recherche et la clé API qui sont stockés dans le fichier de configuration de l’application (`app.config` ou `web.config`) :

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient` a une propriété `Documents`. Cette propriété fournit toutes les méthodes dont vous avez besoin pour interroger les index Azure Search.

## III. Interroger votre index
La recherche à l’aide du Kit de développement logiciel (SDK) .NET est aussi simple que l’appel de la méthode `Documents.Search` sur votre `SearchIndexClient`. Cette méthode accepte quelques paramètres, notamment le texte de recherche, avec un objet `SearchParameters` qui peut être utilisé pour affiner la requête.

#### Types de requête

Azure Search propose de nombreuses options pour créer des requêtes extrêmement performantes. Les deux principaux types de requête que vous allez utiliser sont `search` et `filter`. Une requête `search` recherche un ou plusieurs termes dans tous les champs de _recherche_ de votre index et fonctionne comme les moteurs de recherche Google ou Bing. Une requête `filter` évalue une expression booléenne dans tous les champs sur lesquels vous pouvez _filtrer_ dans un index. Contrairement aux requêtes `search`, les requêtes `filter` correspondent au contenu exact d’un champ, ce qui signifie qu’elles respectent la casse pour les champs string.

Vous pouvez utiliser des recherches et des filtres conjointement ou séparément. Si vous les utilisez conjointement, le filtre est d’abord appliqué à la totalité de l’index et la recherche est effectuée sur les résultats du filtre. Les filtres peuvent donc être une technique utile pour améliorer les performances des requêtes, car ils limitent le nombre de documents que doit traiter la requête de recherche.

Les filtres et les recherches sont exécutés à l’aide de la méthode `Documents.Search`. Une requête de recherche peut être transmise dans le paramètre `searchText`, tandis qu’une expression de filtre peut être transmise dans la propriété `Filter` de la classe `SearchParameters`. Pour filtrer sans effectuer de recherche, transmettez simplement `"*"` pour le paramètre `searchText`. Pour effectuer une recherche sans appliquer de filtre, ne définissez pas la propriété `Filter` et ne transmettez pas une instance `SearchParameters`.

La syntaxe des expressions de filtre est un sous-ensemble du [langage de filtre OData](https://msdn.microsoft.com/library/azure/dn798921.aspx). Pour les requêtes de recherche, vous pouvez utiliser la [syntaxe simplifiée](https://msdn.microsoft.com/library/azure/dn798920.aspx) ou la [syntaxe de requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx).

Pour en savoir plus sur les différents paramètres d’une requête, consultez la [référence du Kit de développement logiciel (SDK) .NET sur MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters.aspx). Vous trouverez également ci-dessous quelques exemples de requêtes.

#### Exemples de requêtes

L’exemple de code suivant montre différentes manières d’interroger l’index « hotels » défini dans [Créer un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-create-index-dotnet.md#DefineIndex). Notez que les documents renvoyés avec les résultats de recherche sont des instances de la classe `Hotel`, qui a été définie dans [Importer des données dans Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-import-data-dotnet.md#HotelClass). L’exemple de code utilise une méthode `WriteDocuments` pour générer les résultats de recherche dans la console. Cette méthode est décrite dans la section suivante.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters = 
    new SearchParameters() 
    { 
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## IV. Traiter les résultats de recherche
La méthode `Documents.Search` renvoie un objet `DocumentSearchResult` qui contient les résultats de la requête. L’exemple figurant dans la section précédente utilisait une méthode appelée `WriteDocuments` pour générer les résultats de recherche dans la console :

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Voici les résultats des requêtes figurant dans la section précédente, en supposant que l’index « hotels » est rempli à l’aide des exemples de données figurant dans [Importer des données dans Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-import-data-dotnet.md) :

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

L’exemple de code ci-dessus utilise la console pour générer les résultats de recherche. De même, vous devez afficher les résultats de recherche dans votre propre application. Consultez [cet exemple sur GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) pour obtenir un exemple illustrant l’affichage des résultats de recherche dans une application web ASP.NET MVC.

<!---HONumber=AcomDC_0309_2016-->