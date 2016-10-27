<properties
   pageTitle="Utilisation d'Azure Search à partir d'une application .NET | Microsoft Azure | Service de recherche cloud hébergé"
   description="Comment utiliser Azure Search à partir d'une application .NET"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="10/06/2016"
   ms.author="brjohnst"/>


# <a name="how-to-use-azure-search-from-a-.net-application"></a>Comment utiliser Azure Search à partir d'une application .NET

Cet article est une procédure pas à pas dont le but est de vous aider à utiliser le [SDK .NET Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx). Vous pouvez utiliser le SDK .NET pour intégrer une expérience de recherche enrichie dans votre application à l'aide d’Azure Search.

## <a name="what's-in-the-azure-search-sdk"></a>Contenu du SDK Azure Search

Ce kit de développement se compose d'une bibliothèque cliente, `Microsoft.Azure.Search`. Il vous permet de gérer vos index, sources de données et indexeurs, ainsi que de télécharger et gérer des documents, et d'exécuter des requêtes, sans avoir à gérer les détails de HTTP et de JSON.

La bibliothèque cliente définit des classes comme `Index`, `Field` et `Document`, ainsi que des opérations telles que `Indexes.Create` et `Documents.Search` sur les classes `SearchServiceClient` et `SearchIndexClient`. Ces classes sont organisées dans les espaces de noms suivants :

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models.](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

La version actuelle du Kit de développement logiciel (SDK) .NET Azure Search est désormais mise à la disposition générale. Si vous souhaitez fournir des commentaires que nous pourrons intégrer dans la prochaine version, consultez notre [page de commentaires](https://feedback.azure.com/forums/263029-azure-search/).

Le SDK .NET prend en charge la version `2015-02-28` de l'API REST d’Azure Search, documentée sur [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx). Cette version inclut désormais la prise en charge de la syntaxe de requête Lucene et des analyseurs de langage Microsoft. Les nouvelles fonctionnalités qui ne font *pas* partie de cette version, comme la prise en charge du paramètre de recherche `moreLikeThis`, sont en [préversion](search-api-2015-02-28-preview.md) et ne sont pas encore disponibles dans le SDK. Consultez [Contrôle de version du service Search](https://msdn.microsoft.com/library/azure/dn864560.aspx) pour connaître les mises à jour disponibles sur chaque fonctionnalité.

Les autres fonctionnalités non prises en charge dans ce SDK sont les suivantes :

  - [Opérations de gestion](https://msdn.microsoft.com/library/azure/dn832684.aspx). Les opérations de gestion incluent l’approvisionnement de services Azure Search et la gestion des clés API. Elles seront prises en charge dans un prochain SDK .NET d’Azure Search.

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Mise à niveau vers la dernière version du Kit de développement logiciel (SDK)

Si vous utilisez déjà une version antérieure du Kit de développement logiciel (SDK) .NET Azure Search et que vous souhaitez mettre à niveau vers la nouvelle version mise à la disposition générale, [cet article](search-dotnet-sdk-migration.md) vous explique comment procéder.

## <a name="requirements-for-the-sdk"></a>Configuration requise pour le SDK

1. Visual Studio 2013 ou Visual Studio 2015.

2. Votre propre service Azure Search. Pour utiliser le SDK, vous devez connaître le nom de votre service et une ou plusieurs clés API. [Créer un service dans le portail](search-create-service-portal.md) vous guidera à travers ces étapes.

3. Téléchargez le [package NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) du SDK .NET Azure Search en utilisant « Gérer les packages NuGet » dans Visual Studio. Recherchez le package nommé `Microsoft.Azure.Search` sur NuGet.org.

Le Kit de développement logiciel (SDK) .NET Azure Search prend en charge les applications qui ciblent .NET Framework version 4.5, ainsi que les applications Windows Store ciblant Windows 8.1 et Windows Phone 8.1. Silverlight n’est pas pris en charge.

## <a name="core-scenarios"></a>Principaux scénarios

Vous devez faire plusieurs choses dans votre application de recherche. Dans ce didacticiel, nous aborderons ces principaux scénarios :

- Création d'un index
- Remplissage de l'index avec des documents
- Recherche de documents à l'aide de filtres et de la recherche en texte intégral

L'exemple de code suivant illustre chacun de ces scénarios. N'hésitez pas à utiliser les extraits de code dans votre propre application.

### <a name="overview"></a>Vue d'ensemble

L’application exemple que nous allons examiner crée un index nommé « hotels », le remplit avec des documents, puis exécute des requêtes de recherche. Voici le programme principal, décrivant le flux global :

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

Nous allons le détailler, étape par étape. Tout d’abord, nous devons créer un objet `SearchServiceClient`. Cet objet vous permet de gérer les index. Pour en construire un, vous devez indiquer le nom de votre service Azure Search, ainsi qu’une clé API d'administration.

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE] Si vous fournissez une clé incorrecte (par exemple, une clé de requête là où une clé d’administration était demandée), `SearchServiceClient` génère une `CloudException` avec le message d’erreur « Forbidden » la première fois que vous invoquez une méthode d'opération dessus, comme `Indexes.Create`. Si cette situation se produit, vérifiez la clé API.

Les quelques lignes suivantes appellent des méthodes pour créer un index nommé « hotels », en le supprimant s’il existe déjà. Nous étudierons ces méthodes un peu plus tard.

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

Ensuite, l'index doit être rempli. Pour ce faire, nous avons besoin de `SearchIndexClient`. Il existe deux façons d'en obtenir un : en le créant ou en appelant `Indexes.GetClient` sur `SearchServiceClient`. Pour des raisons pratiques, nous allons opter pour la deuxième solution.

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE] Dans une application de recherche classique, le remplissage et la gestion des index sont gérés par un composant séparé des requêtes de recherche. `Indexes.GetClient` est pratique pour remplir un index car il vous évite de fournir un autre `SearchCredentials`. Pour ce faire, il transmet la clé d’administration que vous avez utilisée afin de créer le `SearchServiceClient` dans le nouveau `SearchIndexClient`. Toutefois, dans la partie de votre application qui exécute des requêtes, il vaut mieux créer le `SearchIndexClient` directement afin de transmettre une clé de requête au lieu d'une clé d'administration. Cela est conforme au principe du moindre privilège et vous permet de mieux sécuriser votre application. Pour en savoir plus sur les clés d’administration et les clés de requête, cliquez [ici](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Maintenant que nous avons un `SearchIndexClient`, nous pouvons remplir l'index. Cette opération est exécutée par une autre méthode que nous étudierons ultérieurement.

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

Enfin, nous exécutons quelques requêtes de recherche et affichons les résultats, là encore en utilisant `SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

Si vous exécutez cette application avec un nom de service et une clé API valides, la sortie doit être similaire à celle-ci :

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []
    Complete.  Press any key to end application...

Le code source complet de l'application est fourni à la fin de cet article.

Ensuite, nous allons étudier chacune des méthodes appelées par `Main`.

### <a name="creating-an-index"></a>Création d'un index

Après la création d’un `SearchServiceClient`, la prochaine opération effectuée par `Main` est de supprimer l'index « hotels » s’il existe déjà. Cette opération est effectuée par la méthode suivante :

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Cette méthode utilise `SearchServiceClient` pour vérifier si l'index existe et, dans l’affirmative, elle le supprime.

> [AZURE.NOTE] L'exemple de code dans cet article utilise les méthodes synchrones du SDK .NET Azure Search pour plus de simplicité. Nous vous recommandons d'utiliser les méthodes asynchrones dans vos propres applications pour les rendre évolutives et réactives. Par exemple, dans la méthode ci-dessus, vous pouvez utiliser `ExistsAsync` et `DeleteAsync` au lieu de `Exists` et `Delete`.

Ensuite, `Main` crée un index « hotels » en appelant cette méthode :

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Cette méthode crée un objet `Index` avec une liste d’objets `Field` qui définit le schéma du nouvel index. Chaque champ a un nom, un type de données et plusieurs attributs qui définissent son comportement de recherche. En plus des champs, vous pouvez ajouter des profils de notation, des générateurs de suggestions ou des options CORS à l'index (éléments supprimés de l'exemple pour des raisons de concision). Vous trouverez plus d’informations sur l’objet Index et ses composants dans les informations de référence du SDK sur [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx), ainsi que dans les [informations de référence sur l’API REST de Recherche Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

### <a name="populating-the-index"></a>Remplissage de l'index

La prochaine étape dans `Main` consiste à remplir l'index créé. Cette opération est effectuée par la méthode suivante :

    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                {
                    HotelId = "1058-441",
                    HotelName = "Fancy Stay",
                    BaseRate = 199.0,
                    Category = "Luxury",
                    Tags = new[] { "pool", "view", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                    Rating = 5,
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "666-437",
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "970-501",
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "956-532",
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "566-518",
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(documents);
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

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Cette méthode présente quatre parties. La première crée un tableau d’objets `Hotel` qui servent de données d'entrée à charger dans l'index. Ces données sont codées en dur pour plus de simplicité. Dans votre application, vos données seront probablement issues d'une source de données externe, comme une base de données SQL.

La deuxième partie crée un `IndexBatch` contenant les documents. Vous spécifiez l’opération que vous souhaitez appliquer au lot au moment de sa création, dans ce cas en appelant `IndexBatch.Upload`. Le lot est ensuite chargé dans l'index Azure Search par la méthode `Documents.Index` .

> [AZURE.NOTE] Dans cet exemple, nous allons simplement charger les documents. Si vous souhaitez fusionner les modifications dans les documents existants ou supprimer des documents, vous pouvez créer des lots en appelant `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` ou `IndexBatch.Delete` à la place. Vous pouvez aussi combiner plusieurs opérations dans un lot unique en appelant `IndexBatch.New`, qui accepte une collection d’objets `IndexAction`, dont chacun indique à Azure Search d’effectuer une opération spécifique sur un document. Vous pouvez créer chaque `IndexAction` avec sa propre opération en appelant la méthode correspondante comme `IndexAction.Merge`, `IndexAction.Upload`, et ainsi de suite.

La troisième partie de cette méthode est un bloc catch qui gère un cas d'erreur important pour l'indexation. Si votre service Azure Search ne parvient pas à indexer certains documents du lot, `Documents.Index` génère un `IndexBatchException`. Cela peut se produire si vous indexez des documents lorsque votre service est surchargé. **Nous vous recommandons vivement de prendre en charge explicitement ce cas de figure dans votre code.**  Vous pouvez retarder puis relancer l'indexation des documents qui ont échoué, ouvrir une session et continuer comme dans l’exemple, ou faire autre chose selon la cohérence des données requise par votre application.

Enfin, la méthode retarde son exécution de deux secondes. L'indexation s’exécutant en mode asynchrone dans votre service Azure Search, l'exemple d'application doit attendre quelque temps afin de s'assurer que les documents sont disponibles pour la recherche. Ce genre de retard n’est nécessaire que dans les démonstrations, les tests et les exemples d'applications.

#### <a name="how-the-.net-sdk-handles-documents"></a>Gestion des documents par le Kit de développement logiciel (SDK) .NET

Vous demandez peut-être comment le SDK .NET Azure Search peut charger des instances d’une classe définie par l'utilisateur, comme `Hotel` , dans l'index. Pour répondre à cette question, examinons la classe `Hotel` :

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

La première chose à remarquer est que chaque propriété publique de `Hotel` correspond à un champ dans la définition de l'index, mais à une différence près : le nom de chaque champ commence par une minuscule, tandis que le nom de chaque propriété publique de `Hotel` commence par une majuscule. Il s'agit d'un scénario courant dans les applications .NET qui effectuent une liaison de données là où le schéma cible est en dehors du contrôle du développeur de l'application. Plutôt que de violer les consignes d’affectation de noms de .NET en faisant commencer les noms de propriété par une minuscule, vous pouvez demander au SDK d’attribuer automatiquement une casse minuscule aux noms de propriété avec l’attribut `[SerializePropertyNamesAsCamelCase]` .

> [AZURE.NOTE] Le SDK .NET Azure Search utilise la bibliothèque [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) pour sérialiser et désérialiser vos objets de modèle personnalisés vers et à partir de JSON. Vous pouvez personnaliser cette sérialisation si nécessaire. Pour plus d’informations, consultez [Sérialisation personnalisée avec JSON.NET](#JsonDotNet).
 
La deuxième chose importante sur la classe `Hotel` concerne les types de données des propriétés publiques. Les types .NET de ces propriétés correspondent à leurs types de champ équivalents dans la définition de l’index. Par exemple, la propriété de chaîne `Category` correspond au champ `category`, qui est de type `Edm.String`. Il existe des mappages de type similaires entre `bool?` et `Edm.Boolean`, `DateTimeOffset?` et `Edm.DateTimeOffset`, etc. Les règles spécifiques pour le mappage de type sont documentées avec la méthode `Documents.Get`[MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Cette capacité à utiliser vos propres classes comme des documents fonctionne dans les deux sens. Vous pouvez également récupérer les résultats de la recherche et laisser le SDK les désérialiser automatiquement à un type de votre choix, comme nous le verrons dans la section suivante.

> [AZURE.NOTE] Le SDK .NET Azure Search prend également en charge les documents dynamiquement typés à l’aide de la classe `Document`, qui est un mappage de type clé/valeur entre des noms de champ et des valeurs de champ. Cela est utile dans les cas où vous ne connaissez pas le schéma de l’index lors de sa conception et où il serait peu pratique d’établir une liaison à des classes de modèles spécifiques. Toutes les méthodes du SDK qui gèrent les documents ont des surcharges qui fonctionnent avec la classe `Document` , ainsi que des surcharges fortement typées qui acceptent un paramètre de type générique. Seules ces dernières sont utilisées dans l'exemple de code de ce didacticiel. Pour plus d’informations sur la classe `Document` , cliquez [ici](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Remarque importante sur les types de données**

Lorsque vous créez vos propres classes de modèles à mapper à un index Azure Search, nous vous recommandons de déclarer les propriétés des types de valeurs, par exemple `bool` et `int`, comme acceptant la valeur null (par exemple, `bool?` au lieu de `bool`). Si vous utilisez une propriété ne pouvant être définie sur null, vous devez **garantir** qu’aucun document de cet index ne contient de valeur null pour le champ correspondant. Ni le Kit de développement logiciel ni le service Azure Search ne vous aideront à appliquer cette recommandation.

Il ne s’agit pas d’une préoccupation hypothétique : imaginez un scénario dans lequel vous ajoutez un nouveau champ à un index existant qui est de type `Edm.Int32`. Après la mise à jour de la définition d’index, ce nouveau champ prendra la valeur null pour tous les documents (car tous les types peuvent avoir la valeur null dans Azure Search). Si vous utilisez ensuite une classe de modèle avec une propriété `int` ne pouvant être définie sur null pour ce champ, vous obtiendrez l’exception `JsonSerializationException` ci-dessous lorsque vous tenterez de récupérer des documents :

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Pour cette raison, nous vous recommandons d'utiliser des types pour lesquels la valeur null est autorisée en tant que meilleure pratique.

<a name="JsonDotNet"></a>
#### <a name="custom-serialization-with-json.net"></a>Sérialisation personnalisée avec JSON.NET

Le kit de développement logiciel utilise JSON.NET pour sérialiser et désérialiser les documents. Si nécessaire, vous pouvez personnaliser la sérialisation et la désérialisation en définissant votre propre `JsonConverter` ou `IContractResolver` (pour plus d’informations, consultez la [documentation JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm)). Cela peut s’avérer utile lorsque vous souhaitez adapter une classe de modèle existante de votre application à utiliser avec Azure Search et d’autres scénarios plus avancés. Par exemple, avec la sérialisation personnalisée, vous pouvez :

 - Incluez ou exclure certaines propriétés de votre classe de modèle dans le stockage en tant que champs de document.
 - Mappage des noms de propriété dans le code et des noms de champ de votre index.
 - Créez des attributs personnalisés pouvant être utilisés à la fois pour le mappage des propriétés sur les champs du document champs du document et pour la création de la définition d’index correspondante.

Vous pouvez trouver des exemples d’implémentation de sérialisation personnalisée dans les tests d’unités du kit de développement logiciel Azure Search .NET Azure GitHub. [Ce dossier](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models) est un bon point de départ. Il contient des classes qui sont utilisées par les tests de sérialisation personnalisés.

### <a name="searching-for-documents-in-the-index"></a>Recherche de documents dans l'index

La dernière étape dans l'exemple d'application consiste à rechercher certains documents dans l'index. La méthode suivante effectue cette opération :

    private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response.Results)
        {
            Console.WriteLine(result.Document);
        }
    }

Tout d'abord, cette méthode crée un objet `SearchParameters` . Cela permet de spécifier des options supplémentaires pour la requête, comme le tri, le filtrage, la pagination et la facettisation. Dans cet exemple, nous définissons uniquement la propriété `Filter` .

L'étape suivante consiste à exécuter la requête de recherche. Cette opération est effectuée avec la méthode `Documents.Search` . Dans ce cas, nous transmettons le texte de recherche à utiliser en tant que chaîne, ainsi que les paramètres de recherche créés précédemment. Nous spécifions également `Hotel` comme paramètre de type pour `Documents.Search`, qui demande au SDK de désérialiser les documents figurant dans les résultats de recherche, en objets de type `Hotel`.

Enfin, cette méthode parcourt toutes les correspondances dans les résultats de recherche et imprime chaque document dans la console.

Examinons comment cette méthode est appelée :

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Dans le premier appel, nous recherchons tous les documents contenant les termes « fancy » ou « wifi ». Dans le deuxième appel, le texte à rechercher est « * », ce qui signifie « rechercher tous les éléments ». Pour plus d’informations sur la syntaxe des requêtes de recherche, cliquez [ici](https://msdn.microsoft.com/library/azure/dn798920.aspx).

Le deuxième appel utilise une expression `$filter` OData, `category eq 'Luxury'`. Celle-ci limite la recherche et ne renvoie que les documents dont le champ `category` correspond exactement à la chaîne « Luxury ». Pour plus d’informations sur la syntaxe OData prise en charge par Azure Search, cliquez [ici](https://msdn.microsoft.com/library/azure/dn798921.aspx).

Maintenant que vous savez ce que font ces deux appels, il est plus facile de comprendre pourquoi leur sortie se présente comme suit :

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []

La première recherche renvoie deux documents. Le nom du premier contient « fancy », tandis que le champ `tags` du second contient « wifi ». La deuxième recherche renvoie deux documents, qui sont les seuls de l’index dont le champ `category` contient « Luxury ».

Cette étape termine le didacticiel, mais ne vous arrêtez pas en si bon chemin. **Étapes suivantes** fournit des ressources supplémentaires pour en apprendre davantage sur Azure Search.

## <a name="next-steps"></a>Étapes suivantes

- Parcourez les références pour le [SDK .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) et [l’API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) sur MSDN.
- Approfondissez vos connaissances grâce aux [vidéos et autres exemples et didacticiels](search-video-demo-tutorial-list.md).
- Découvrez les fonctionnalités et capacités de cette version du SDK Azure Search : [Présentation d’Azure Search](https://msdn.microsoft.com/library/azure/dn798933.aspx)
- Consultez les [conventions d’affectation de noms](https://msdn.microsoft.com/library/azure/dn857353.aspx) pour apprendre les règles de dénomination des différents objets.
- Faites connaissance avec les [types de données pris en charge](https://msdn.microsoft.com/library/azure/dn798938.aspx) par Azure Search.


## <a name="sample-application-source-code"></a>Code source de l'exemple d'application

Voici le code source complet de l'exemple d'application utilisé dans cette procédure. Notez que vous devez remplacer les espaces réservés au nom du service et à la clé API dans le fichier Program.cs par vos propres valeurs si vous souhaitez générer et exécuter l'exemple.

Program.cs :

```csharp
using System;
using System.Configuration;
using System.Linq;
using System.Threading;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    class Program
    {
        // This sample shows how to delete, create, upload documents and query an index
        static void Main(string[] args)
        {
            // Put your search service name here. This is the hostname portion of your service URL.
            // For example, if your service URL is https://myservice.search.windows.net, then your
            // service name is myservice.
            string searchServiceName = "myservice";

            string apiKey = "Put your API admin key here.";

            SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

            Console.WriteLine("{0}", "Deleting index...\n");
            DeleteHotelsIndexIfExists(serviceClient);

            Console.WriteLine("{0}", "Creating index...\n");
            CreateHotelsIndex(serviceClient);

            ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

            Console.WriteLine("{0}", "Uploading documents...\n");
            UploadDocuments(indexClient);

            Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
            SearchDocuments(indexClient, searchText: "fancy wifi");

            Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
            SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("hotels"))
            {
                serviceClient.Indexes.Delete("hotels");
            }
        }

        private static void CreateHotelsIndex(SearchServiceClient serviceClient)
        {
            var definition = new Index()
            {
                Name = "hotels",
                Fields = new[]
                {
                    new Field("hotelId", DataType.String)                       { IsKey = true },
                    new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                    new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                    new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                    new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                    new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                }
            };

            serviceClient.Indexes.Create(definition);
        }

        private static void UploadDocuments(ISearchIndexClient indexClient)
        {
            var documents =
                new Hotel[]
                {
                    new Hotel()
                    {
                        HotelId = "1058-441",
                        HotelName = "Fancy Stay",
                        BaseRate = 199.0,
                        Category = "Luxury",
                        Tags = new[] { "pool", "view", "concierge" },
                        ParkingIncluded = false,
                        LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                        Rating = 5,
                        Location = GeographyPoint.Create(47.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "666-437",
                        HotelName = "Roach Motel",
                        BaseRate = 79.99,
                        Category = "Budget",
                        Tags = new[] { "motel", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                        Rating = 1,
                        Location = GeographyPoint.Create(49.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "970-501",
                        HotelName = "Econo-Stay",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "pool", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(46.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "956-532",
                        HotelName = "Express Rooms",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "wifi", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(48.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "566-518",
                        HotelName = "Surprisingly Expensive Suites",
                        BaseRate = 279.99,
                        Category = "Luxury",
                        ParkingIncluded = false
                    }
                };

            try
            {
                var batch = IndexBatch.Upload(documents);
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

            // Wait a while for indexing to complete.
            Thread.Sleep(2000);
        }

        private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
        {
            // Execute search based on search text and optional filter
            var sp = new SearchParameters();

            if (!String.IsNullOrEmpty(filter))
            {
                sp.Filter = filter;
            }

            DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
            foreach (SearchResult<Hotel> result in response.Results)
            {
                Console.WriteLine(result.Document);
            }
        }
    }
}
```

Hotel.cs :

```csharp
using System;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }
}
```



<!--HONumber=Oct16_HO2-->


