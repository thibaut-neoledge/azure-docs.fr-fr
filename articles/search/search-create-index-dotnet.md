<properties
    pageTitle="Créer un index Azure Search à l’aide du kit de développement logiciel (SDK) .NET | Microsoft Azure | Service de recherche cloud hébergé"
    description="Créer un index dans le code à l’aide du kit de développement logiciel (SDK) .NET d’Azure Search."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="brjohnst"/>

# Création d’un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET
> [AZURE.SELECTOR]
- [Vue d'ensemble](search-what-is-an-index.md)
- [Portail](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


Cet article vous guidera dans la création d’un [index](https://msdn.microsoft.com/library/azure/dn798941.aspx) Azure Search à l’aide du [kit de développement logiciel (SDK) .NET Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Avant de suivre ce guide et de passer à la création d’un index, vous devez avoir déjà [créé un service Azure Search](search-create-service-portal.md).

Notez que tous les exemples de code de cet article sont écrits en C#. L’intégralité du code source est disponible [sur GitHub](http://aka.ms/search-dotnet-howto).

## I. Identifier la clé API d’administration de votre service Azure Search
Maintenant que vous avez configuré un service Azure Search, vous être presque prêt à émettre des requêtes sur le système d’extrémité de votre service à l’aide du kit de développement logiciel (SDK) .NET. Tout d'abord, vous devez obtenir l’une des clés API d'administration générées pour le service Search que vous avez configuré. Le kit de développement logiciel (SDK) .NET envoie la clé API à chaque demande à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

1. Pour accéder aux clés API de votre service, vous devez vous connecter au [portail Azure](https://portal.azure.com/)
2. Accédez au panneau de votre service Azure Search
3. Cliquez sur l’icône « Clés »

Votre service comporte à la fois des *clés d’administration* et des *clés de requête*.

  - Les *clés d’administration* principales et secondaires vous accordent des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données. Deux clés sont à votre disposition afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et inversement.
  - Vos *clés de requête* vous accordent un accès en lecture seule aux index et documents ; elles sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.

Dans le cadre de la création d’un index, vous pouvez utiliser votre clé d’administration principale ou secondaire.

<a name="CreateSearchServiceClient"></a>
## II. Création d’une instance de la classe SearchServiceClient
Pour commencer à utiliser le Kit de développement logiciel (SDK) .NET Azure Search, vous devez créer une instance de la classe `SearchServiceClient`. Cette classe dispose de plusieurs constructeurs. Celle que vous cherchez utilise le nom de votre service de recherche et un objet `SearchCredentials` en tant paramètres. `SearchCredentials` encapsule votre clé API.

Le code suivant crée un nouveau `SearchServiceClient` à l'aide de valeurs pour le nom du service de recherche et la clé API stockés dans le fichier de configuration de l'application (`app.config` ou `web.config`) :

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` a une propriété `Indexes`. Cette propriété fournit toutes les méthodes dont vous avez besoin pour créer, afficher, mettre à jour ou supprimer des index Azure Search.

> [AZURE.NOTE] La classe `SearchServiceClient` gère les connexions à votre service de recherche. Pour éviter l'ouverture d'un trop grand nombre de connexions, essayez de partager une seule instance de `SearchServiceClient` dans votre application, si possible. Ses méthodes sont thread-safe pour activer le partage.

<a name="DefineIndex"></a>
## III. Définition de votre index Azure Search à l’aide de la classe `Index`
Un seul appel à la méthode `Indexes.Create` crée votre index. Cette méthode prend comme paramètre un objet `Index` qui définit votre index Azure Search. Vous devez créer un objet `Index` et l'initialiser comme suit :

1. Définissez la propriété `Name` de l’objet `Index` sur le nom de votre index.
2. Définissez la propriété `Fields` de l’objet `Index` sur l’array d’objets `Field`. Chaque objet `Field` définit le comportement d'un champ dans l'index. Vous pouvez fournir le nom du champ au constructeur, ainsi que le type de données (ou un analyseur pour les champs de chaîne). Vous pouvez également définir d'autres propriétés comme `IsSearchable`, `IsFilterable`, etc.

Il est important de ne perdre de vue ni votre expérience de recherche ni vos besoins métiers lorsque vous concevez votre index, chaque `Field` devant être affecté à des [propriétés correctes](https://msdn.microsoft.com/library/azure/dn798941.aspx). Ces propriétés déterminent les fonctionnalités de recherche (filtrage, facettes, tri de recherche en texte intégral, etc.) qui s’appliqueront à chaque champ. Pour les propriétés que vous ne définissez pas explicitement, la classe `Field` désactive par défaut la fonctionnalité de recherche correspondante, sauf si vous l'activez de façon spécifique.

Dans notre exemple, nous avons nommé notre index « hotels » et défini ses champs de la manière suivante :

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Nous avons soigneusement choisi les valeurs des propriétés pour chaque `Field` en fonction de la manière dont elles seront vraisemblablement utilisées dans une application. Par exemple, il est probable que les personnes recherchant des hôtels seront intéressées par les correspondances de mots-clés sur le champ `description`. Ainsi, nous activons la recherche en texte intégral de ce champ en définissant `IsSearchable` sur `true`.

Notez que, dans votre index, un seul champ de type `DataType.String` doit être désigné en tant que _clé_ en définissant `IsKey` sur `true` (voir `hotelId` dans l’exemple ci-dessus).

La définition d’index ci-dessus utilise un analyseur de langue personnalisé pour le champ `description_fr` dans la mesure où il est destiné à stocker du texte en français. Pour plus d’informations sur les analyseurs de langue, consultez la [rubrique relative à la prise en charge linguistique sur MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) ainsi que le [billet de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) correspondant.

> [AZURE.NOTE]  Notez que, en transmettant `AnalyzerName.FrLucene` dans le constructeur, le `Field` sera automatiquement défini comme étant de type `DataType.String` et `IsSearchable` sera défini sur `true`.

## IV. Création de l'index
Maintenant qu’un objet `Index` a été initialisé, vous pouvez créer l’index en appelant `Indexes.Create` sur votre objet `SearchServiceClient` :

```csharp
serviceClient.Indexes.Create(definition);
```

Pour une requête réussie, la méthode effectuera un retour normalement. En cas de problème avec la requête, comme un paramètre non valide, la méthode lève `CloudException`.

Pour supprimer un index, il vous suffit d’appeler la méthode `Indexes.Delete` sur votre `SearchServiceClient`. Voici, par exemple, la requête à utiliser pour supprimer l’index « hotels » :

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] L'exemple de code dans cet article utilise les méthodes synchrones du SDK .NET Azure Search pour plus de simplicité. Nous vous recommandons d'utiliser les méthodes asynchrones dans vos propres applications pour les rendre évolutives et réactives. Par exemple, dans les exemples ci-dessus, vous pouvez utiliser `CreateAsync` et `DeleteAsync` au lieu de `Create` et `Delete`.

## Suivant
Après avoir créé un index Azure Search, vous pouvez commencer à [télécharger du contenu dans votre index](search-what-is-data-import.md) afin d’y lancer des recherches.

<!---HONumber=AcomDC_0316_2016-->