---
title: "Comment utiliser la Recherche Azure à partir d’une application .NET | Microsoft Docs"
description: "Comment utiliser Azure Search à partir d'une application .NET"
services: search
documentationcenter: 
author: brjohnstmsft
manager: jlembicz
editor: 
ms.assetid: 93653341-c05f-4cfd-be45-bb877f964fcb
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: 7273ae6a698f2af52e78ea2aae9ca5cd80f6a2b1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Comment utiliser Azure Search à partir d'une application .NET
Cet article est une procédure pas à pas dont le but est de vous aider à utiliser le [SDK .NET Azure Search](https://aka.ms/search-sdk). Vous pouvez utiliser le SDK .NET pour intégrer une expérience de recherche enrichie dans votre application à l'aide d’Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Contenu du SDK Azure Search
Ce kit de développement se compose d'une bibliothèque cliente, `Microsoft.Azure.Search`. Il vous permet de gérer vos index, sources de données et indexeurs, ainsi que de télécharger et gérer des documents, et d'exécuter des requêtes, sans avoir à gérer les détails de HTTP et de JSON.

La bibliothèque cliente définit des classes comme `Index`, `Field` et `Document`, ainsi que des opérations telles que `Indexes.Create` et `Documents.Search` sur les classes `SearchServiceClient` et `SearchIndexClient`. Ces classes sont organisées dans les espaces de noms suivants :

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

La version actuelle du Kit de développement logiciel (SDK) .NET Azure Search est désormais mise à la disposition générale. Si vous souhaitez fournir des commentaires que nous pourrons intégrer dans la prochaine version, consultez notre [page de commentaires](https://feedback.azure.com/forums/263029-azure-search/).

Le Kit de développement logiciel (SDK) .NET prend en charge la version `2016-09-01` de [l’API REST de la Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/). Cette version inclut désormais la prise en charge des analyseurs personnalisés et de l’indexeur de table Azure et des objets Blob Azure. Les fonctionnalités d’évaluation qui ne font *pas* partie de cette version, par exemple la prise en charge de l’indexation des fichiers CSV et JSON, sont disponibles en [préversion](search-api-2016-09-01-preview.md) et accessibles par l’intermédiaire de la [version 4.0.1-preview du Kit SDK .NET](https://aka.ms/search-sdk-preview).

Ce Kit de développement logiciel (SDK) ne prend pas en charge les [opérations de gestion](https://docs.microsoft.com/rest/api/searchmanagement/) telles que la création et la mise à l’échelle des services de recherche, ainsi que la gestion des clés API. Si vous avez besoin de gérer vos ressources de recherche à partir d’une application .NET, vous pouvez utiliser le [Kit de développement logiciel (SDK) .NET de la Recherche Azure](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Mise à niveau vers la dernière version du Kit de développement logiciel (SDK)
Si vous utilisez déjà une version antérieure du Kit de développement logiciel (SDK) .NET Azure Search et que vous souhaitez mettre à niveau vers la nouvelle version mise à la disposition générale, [cet article](search-dotnet-sdk-migration.md) vous explique comment procéder.

## <a name="requirements-for-the-sdk"></a>Configuration requise pour le SDK
1. Visual Studio 2017.
2. Votre propre service Azure Search. Pour utiliser le SDK, vous devez connaître le nom de votre service et une ou plusieurs clés API. [Créer un service dans le portail](search-create-service-portal.md) vous guidera à travers ces étapes.
3. Téléchargez le [package NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) du SDK .NET Azure Search en utilisant « Gérer les packages NuGet » dans Visual Studio. Recherchez le package nommé `Microsoft.Azure.Search` sur NuGet.org.

Le Kit de développement logiciel (SDK) .NET Recherche Azure prend en charge les applications qui ciblent .NET Framework 4.6 et .NET Core.

## <a name="core-scenarios"></a>Principaux scénarios
Vous devez faire plusieurs choses dans votre application de recherche. Dans ce didacticiel, nous aborderons ces principaux scénarios :

* Création d'un index
* Remplissage de l'index avec des documents
* Recherche de documents à l'aide de filtres et de la recherche en texte intégral

L'exemple de code suivant illustre chacun de ces scénarios. N'hésitez pas à utiliser les extraits de code dans votre propre application.

### <a name="overview"></a>Vue d'ensemble
L’application exemple que nous allons examiner crée un index nommé « hotels », le remplit avec des documents, puis exécute des requêtes de recherche. Voici le programme principal, décrivant le flux global :

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteHotelsIndexIfExists(serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateHotelsIndex(serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Vous trouverez le code source complet de l’exemple d’application utilisé dans cette procédure sur [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Nous allons le détailler, étape par étape. Tout d’abord, nous devons créer un objet `SearchServiceClient`. Cet objet vous permet de gérer les index. Pour en construire un, vous devez indiquer le nom de votre service Azure Search, ainsi qu’une clé API d'administration. Vous pouvez entrer ces informations dans le fichier `appsettings.json` de [l’exemple d’application](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Si vous fournissez une clé incorrecte (par exemple, une clé de requête là où une clé d’administration était demandée), `SearchServiceClient` génère une `CloudException` avec le message d’erreur « Forbidden » la première fois que vous invoquez une méthode d'opération dessus, comme `Indexes.Create`. Si cette situation se produit, vérifiez la clé API.
> 
> 

Les quelques lignes suivantes appellent des méthodes pour créer un index nommé « hotels », en le supprimant s’il existe déjà. Nous étudierons ces méthodes un peu plus tard.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

Ensuite, l'index doit être rempli. Pour ce faire, nous avons besoin de `SearchIndexClient`. Il existe deux façons d'en obtenir un : en le créant ou en appelant `Indexes.GetClient` sur `SearchServiceClient`. Pour des raisons pratiques, nous allons opter pour la deuxième solution.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> Dans une application de recherche classique, le remplissage et la gestion des index sont gérés par un composant séparé des requêtes de recherche. `Indexes.GetClient` est pratique pour remplir un index car il vous évite de fournir un autre `SearchCredentials`. Pour ce faire, il transmet la clé d’administration que vous avez utilisée afin de créer le `SearchServiceClient` dans le nouveau `SearchIndexClient`. Toutefois, dans la partie de votre application qui exécute des requêtes, il vaut mieux créer le `SearchIndexClient` directement afin de transmettre une clé de requête au lieu d'une clé d'administration. Cela est conforme au principe du moindre privilège et vous permet de mieux sécuriser votre application. Pour en savoir plus sur les clés d’administration et les clés de requête, cliquez [ici](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Maintenant que nous avons un `SearchIndexClient`, nous pouvons remplir l'index. Cette opération est exécutée par une autre méthode que nous étudierons ultérieurement.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Enfin, nous exécutons quelques requêtes de recherche et affichons les résultats. Cette fois-ci, nous utilisons un `SearchIndexClient` différent :

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

Nous examinerons la méthode `RunQueries` plus en détail un peu plus tard. Voici le code permettant de créer le nouveau `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Cette fois, nous utilisons une clé de requête, car nous n’avons pas besoin d’obtenir un accès en écriture à l’index. Vous pouvez entrer ces informations dans le fichier `appsettings.json` de [l’exemple d’application](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Si vous exécutez cette application avec un nom de service et une clé API valides, la sortie doit être similaire à ce qui suit :

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Waiting for documents to be indexed...
    
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
    
    Complete.  Press any key to end application...

Le code source complet de l'application est fourni à la fin de cet article.

Ensuite, nous allons étudier chacune des méthodes appelées par `Main`.

### <a name="creating-an-index"></a>Création d'un index
Après la création d’un `SearchServiceClient`, la prochaine opération effectuée par `Main` est de supprimer l'index « hotels » s’il existe déjà. Cette opération est effectuée par la méthode suivante :

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
    }
}
```

Cette méthode utilise `SearchServiceClient` pour vérifier si l'index existe et, dans l’affirmative, elle le supprime.

> [!NOTE]
> L'exemple de code dans cet article utilise les méthodes synchrones du SDK .NET Azure Search pour plus de simplicité. Nous vous recommandons d'utiliser les méthodes asynchrones dans vos propres applications pour les rendre évolutives et réactives. Par exemple, dans la méthode ci-dessus, vous pouvez utiliser `ExistsAsync` et `DeleteAsync` au lieu de `Exists` et `Delete`.
> 
> 

Ensuite, `Main` crée un index « hotels » en appelant cette méthode :

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>()
    };

    serviceClient.Indexes.Create(definition);
}
```

Cette méthode crée un objet `Index` avec une liste d’objets `Field` qui définit le schéma du nouvel index. Chaque champ a un nom, un type de données et plusieurs attributs qui définissent son comportement de recherche. La classe `FieldBuilder` utilise la réflexion pour créer une liste d’objets `Field` pour l’index en examinant les attributs et propriétés publics de la classe de modèle `Hotel` donnée. Nous examinerons la classe `Hotel` plus en détail un peu plus tard.

> [!NOTE]
> Vous pouvez toujours créer directement la liste des objets `Field` au lieu d’utiliser la fonctionnalité `FieldBuilder`, si nécessaire. Par exemple, vous ne souhaitez pas utiliser une classe de modèle, ou vous pouvez être amené à recourir à une classe de modèle existante, que vous ne souhaitez pas modifier en ajoutant des attributs.
>
> 

En plus des champs, vous pouvez ajouter des profils de notation, des générateurs de suggestions ou des options CORS à l'index (éléments supprimés de l'exemple pour des raisons de concision). Vous trouverez plus d’informations sur l’objet Index et ses composants dans la page de [référence sur le Kit de développement logiciel (SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index#microsoft_azure_search_models_index), ainsi que dans la page de [référence sur l’API REST de la Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Remplissage de l'index
La prochaine étape dans `Main` consiste à remplir l'index créé. Cette opération est effectuée par la méthode suivante :

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        { 
            HotelId = "1", 
            BaseRate = 199.0, 
            Description = "Best hotel in town",
            DescriptionFr = "Meilleur hôtel en ville",
            HotelName = "Fancy Stay",
            Category = "Luxury", 
            Tags = new[] { "pool", "view", "wifi", "concierge" },
            ParkingIncluded = false, 
            SmokingAllowed = false,
            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
            Rating = 5, 
            Location = GeographyPoint.Create(47.678581, -122.131577)
        },
        new Hotel()
        { 
            HotelId = "2", 
            BaseRate = 79.99,
            Description = "Cheapest hotel in town",
            DescriptionFr = "Hôtel le moins cher en ville",
            HotelName = "Roach Motel",
            Category = "Budget",
            Tags = new[] { "motel", "budget" },
            ParkingIncluded = true,
            SmokingAllowed = true,
            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
            Rating = 1,
            Location = GeographyPoint.Create(49.678581, -122.131577)
        },
        new Hotel() 
        { 
            HotelId = "3", 
            BaseRate = 129.99,
            Description = "Close to town hall and the river"
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Cette méthode présente quatre parties. La première crée un tableau d’objets `Hotel` qui servent de données d'entrée à charger dans l'index. Ces données sont codées en dur pour plus de simplicité. Dans votre application, vos données seront probablement issues d'une source de données externe, comme une base de données SQL.

La deuxième partie crée un `IndexBatch` contenant les documents. Vous spécifiez l’opération que vous souhaitez appliquer au lot au moment de sa création, dans ce cas en appelant `IndexBatch.Upload`. Le lot est ensuite chargé dans l'index Azure Search par la méthode `Documents.Index` .

> [!NOTE]
> Dans cet exemple, nous allons simplement charger les documents. Si vous souhaitez fusionner les modifications dans les documents existants ou supprimer des documents, vous pouvez créer des lots en appelant `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` ou `IndexBatch.Delete` à la place. Vous pouvez aussi combiner plusieurs opérations dans un lot unique en appelant `IndexBatch.New`, qui accepte une collection d’objets `IndexAction`, dont chacun indique à Azure Search d’effectuer une opération spécifique sur un document. Vous pouvez créer chaque `IndexAction` avec sa propre opération en appelant la méthode correspondante comme `IndexAction.Merge`, `IndexAction.Upload`, et ainsi de suite.
> 
> 

La troisième partie de cette méthode est un bloc catch qui gère un cas d'erreur important pour l'indexation. Si votre service Azure Search ne parvient pas à indexer certains documents du lot, `Documents.Index` génère un `IndexBatchException`. Cela peut se produire si vous indexez des documents lorsque votre service est surchargé. **Nous vous recommandons vivement de prendre en charge explicitement ce cas de figure dans votre code.** Vous pouvez retarder puis relancer l'indexation des documents qui ont échoué, ouvrir une session et continuer comme dans l’exemple, ou faire autre chose selon la cohérence des données requise par votre application.

> [!NOTE]
> Vous pouvez utiliser la méthode `FindFailedActionsToRetry` pour construire un nouveau lot contenant uniquement les actions qui ont échoué lors d’un précédent appel à `Index`. La méthode est documentée [ici](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception#Microsoft_Azure_Search_IndexBatchException_FindFailedActionsToRetry_Microsoft_Azure_Search_Models_IndexBatch_System_String_). Vous trouverez sur [StackOverflow](http://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) un fil de discussion sur l’utilisation adéquate de cette méthode.
>
>

Enfin, la méthode `UploadDocuments` retarde son exécution de deux secondes. L'indexation s’exécutant en mode asynchrone dans votre service Azure Search, l'exemple d'application doit attendre quelque temps afin de s'assurer que les documents sont disponibles pour la recherche. Ce genre de retard n’est nécessaire que dans les démonstrations, les tests et les exemples d'applications.

#### <a name="how-the-net-sdk-handles-documents"></a>Gestion des documents par le Kit de développement logiciel (SDK) .NET
Vous demandez peut-être comment le SDK .NET Azure Search peut charger des instances d’une classe définie par l'utilisateur, comme `Hotel` , dans l'index. Pour répondre à cette question, examinons la classe `Hotel` :

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

La première chose à remarquer est que chaque propriété publique de `Hotel` correspond à un champ dans la définition de l'index, mais à une différence près : le nom de chaque champ commence par une minuscule, tandis que le nom de chaque propriété publique de `Hotel` commence par une majuscule. Il s'agit d'un scénario courant dans les applications .NET qui effectuent une liaison de données là où le schéma cible est en dehors du contrôle du développeur de l'application. Plutôt que de violer les consignes d’affectation de noms de .NET en faisant commencer les noms de propriété par une minuscule, vous pouvez demander au SDK d’attribuer automatiquement une casse minuscule aux noms de propriété avec l’attribut `[SerializePropertyNamesAsCamelCase]` .

> [!NOTE]
> Le SDK .NET Azure Search utilise la bibliothèque [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) pour sérialiser et désérialiser vos objets de modèle personnalisés vers et à partir de JSON. Vous pouvez personnaliser cette sérialisation si nécessaire. Pour plus d’informations, consultez [Sérialisation personnalisée avec JSON.NET](#JsonDotNet).
> 
> 

Le deuxième élément à prendre est compte, ce sont les attributs tels que `IsFilterable`, `IsSearchable`, `Key`, et `Analyzer`, qui décorent chaque propriété publique. Ces attributs sont mappés directement aux [attributs correspondants de l’index de la Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/create-index#request). La classe `FieldBuilder` les utilise pour construire des définitions de champ pour l’index.

La troisième chose importante sur la classe `Hotel` concerne les types de données des propriétés publiques. Les types .NET de ces propriétés correspondent à leurs types de champ équivalents dans la définition de l’index. Par exemple, la propriété de chaîne `Category` correspond au champ `category`, qui est de type `Edm.String`. Il existe des mappages de type similaires entre `bool?` et `Edm.Boolean`, `DateTimeOffset?` et `Edm.DateTimeOffset`, etc. Les règles spécifiques pour le mappage de type sont documentées avec la méthode `Documents.Get` dans [l’article de référence sur le Kit de développement logiciel (SDK) .NET du service Recherche Azure](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_). La classe `FieldBuilder` effectue ce mappage pour vous, mais il peut toutefois être utile de comprendre son fonctionnement, pour les situations éventuelles de résolution des problèmes de sérialisation.

Cette capacité à utiliser vos propres classes comme des documents fonctionne dans les deux sens. Vous pouvez également récupérer les résultats de la recherche et laisser le SDK les désérialiser automatiquement à un type de votre choix, comme nous le verrons dans la section suivante.

> [!NOTE]
> Le SDK .NET Azure Search prend également en charge les documents dynamiquement typés à l’aide de la classe `Document`, qui est un mappage de type clé/valeur entre des noms de champ et des valeurs de champ. Cela est utile dans les cas où vous ne connaissez pas le schéma de l’index lors de sa conception et où il serait peu pratique d’établir une liaison à des classes de modèles spécifiques. Toutes les méthodes du SDK qui gèrent les documents ont des surcharges qui fonctionnent avec la classe `Document` , ainsi que des surcharges fortement typées qui acceptent un paramètre de type générique. Seules ces dernières sont utilisées dans l'exemple de code de ce didacticiel. La classe `Document` hérite des données de l’élément `Dictionary<string, object>`. Vous trouverez plus d’informations [ici](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document#microsoft_azure_search_models_document).
> 
> 

**Pourquoi utiliser des types de données Nullable**

Lorsque vous créez vos propres classes de modèles à mapper à un index Azure Search, nous vous recommandons de déclarer les propriétés des types de valeurs, par exemple `bool` et `int`, comme acceptant la valeur null (par exemple, `bool?` au lieu de `bool`). Si vous utilisez une propriété ne pouvant être définie sur null, vous devez **garantir** qu’aucun document de cet index ne contient de valeur null pour le champ correspondant. Ni le Kit de développement logiciel ni le service Azure Search ne vous aideront à appliquer cette recommandation.

Il ne s’agit pas d’une préoccupation hypothétique : imaginez un scénario dans lequel vous ajoutez un nouveau champ à un index existant qui est de type `Edm.Int32`. Après la mise à jour de la définition d’index, ce nouveau champ prendra la valeur null pour tous les documents (car tous les types peuvent avoir la valeur null dans Azure Search). Si vous utilisez ensuite une classe de modèle avec une propriété `int` ne pouvant être définie sur null pour ce champ, vous obtiendrez l’exception `JsonSerializationException` ci-dessous lorsque vous tenterez de récupérer des documents :

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Pour cette raison, nous vous recommandons d'utiliser des types pour lesquels la valeur null est autorisée en tant que meilleure pratique.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Sérialisation personnalisée avec JSON.NET
Le kit de développement logiciel utilise JSON.NET pour sérialiser et désérialiser les documents. Si nécessaire, vous pouvez personnaliser la sérialisation et la désérialisation en définissant votre propre `JsonConverter` ou `IContractResolver` (pour plus d’informations, consultez la [documentation JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm)). Cela peut s’avérer utile lorsque vous souhaitez adapter une classe de modèle existante de votre application à utiliser avec Azure Search et d’autres scénarios plus avancés. Par exemple, avec la sérialisation personnalisée, vous pouvez :

* Incluez ou exclure certaines propriétés de votre classe de modèle dans le stockage en tant que champs de document.
* Mappez des noms de propriété dans le code et des noms de champ de votre index.
* Créez des attributs personnalisés qui peuvent être utilisés pour le mappage des propriétés aux champs du document.

Vous pouvez trouver des exemples d’implémentation de sérialisation personnalisée dans les tests d’unités du kit de développement logiciel Azure Search .NET Azure GitHub. [Ce dossier](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models) est un bon point de départ. Il contient des classes qui sont utilisées par les tests de sérialisation personnalisés.

### <a name="searching-for-documents-in-the-index"></a>Recherche de documents dans l'index
La dernière étape dans l'exemple d'application consiste à rechercher certains documents dans l'index. La méthode suivante effectue cette opération :

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
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
}
```

Chaque fois qu’elle exécute une requête, cette méthode crée tout d’abord un objet `SearchParameters`. Cela permet de spécifier des options supplémentaires pour la requête, comme le tri, le filtrage, la pagination et la facettisation. Dans cette méthode, nous définissons les propriétés `Filter`, `Select`, `OrderBy`, et `Top` pour différentes requêtes. Toutes les propriétés `SearchParameters` sont documentées [ici](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

L'étape suivante consiste à exécuter la requête de recherche. Cette opération est effectuée avec la méthode `Documents.Search` . Pour chaque requête, nous transmettons le texte de recherche à utiliser en tant que chaîne (ou l’élément `"*"`, s’il n’en existe aucun), ainsi que les paramètres de recherche créés précédemment. Nous spécifions également `Hotel` comme paramètre de type pour `Documents.Search`, qui demande au SDK de désérialiser les documents figurant dans les résultats de recherche, en objets de type `Hotel`.

> [!NOTE]
> Pour plus d’informations sur la syntaxe des requêtes de recherche, cliquez [ici](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Enfin, après l’exécution de chaque requête, cette méthode parcourt toutes les correspondances dans les résultats de la recherche et imprime chaque document dans la console :

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

Examinons de plus près chacune des requêtes exécutées. Voici le code permettant d’exécuter la première requête :

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

Dans ce cas, nous recherchons des hôtels qui correspondent au mot « budget ». Nous voulons uniquement obtenir uniquement les noms de ces hôtels, comme l’indique le paramètre `Select`. Voici les résultats :

    Name: Roach Motel

Ensuite, nous souhaitons rechercher les hôtels proposant des chambres à moins de 150 $ par nuit, et obtenir uniquement l’ID de l’hôtel et sa description :

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Cette requête utilise une expression `$filter` OData (`baseRate lt 150`) pour filtrer les documents dans l’index. Pour plus d’informations sur la syntaxe OData prise en charge par Azure Search, cliquez [ici](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Voici les résultats de la requête :

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

Ensuite, nous souhaitons rechercher les deux hôtels ayant été le plus récemment rénovés, et afficher le nom et la date de la dernière rénovation de ces derniers. Voici le code : 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Dans ce cas, nous utilisons à nouveau la syntaxe OData pour spécifier le paramètre `OrderBy` en tant que `lastRenovationDate desc`. Nous définissons également le paramètre `Top` sur 2 pour vérifier que nous obtenons uniquement les deux premiers documents. Comme précédemment, nous définissons le paramètre `Select` pour spécifier les champs qui doivent être renvoyés.

Voici les résultats :

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Pour finir, nous souhaitons rechercher tous les hôtels correspondant au mot « motel » :

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Voici les résultats, qui incluent tous les champs, dans la mesure où nous n’avons pas spécifié la propriété `Select` :

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

Cette étape termine le didacticiel, mais ne vous arrêtez pas en si bon chemin. **Étapes suivantes** fournit des ressources supplémentaires pour en apprendre davantage sur Azure Search.

## <a name="next-steps"></a>Étapes suivantes
* Parcourez les références relatives au [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) et à [l’API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Approfondissez vos connaissances grâce aux [vidéos et autres exemples et didacticiels](search-video-demo-tutorial-list.md).
* Consultez les [conventions d’affectation de noms](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) pour apprendre les règles de dénomination des différents objets.
* Faites connaissance avec les [types de données pris en charge](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) par Azure Search.
