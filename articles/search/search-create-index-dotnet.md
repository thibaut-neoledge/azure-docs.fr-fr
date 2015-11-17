<properties
	pageTitle="Créer un index Azure Search à l’aide de .NET | Microsoft Azure | Service de recherche cloud hébergé"
	description="Créer un index dans le code à l’aide du kit de développement logiciel ou de la bibliothèque .NET d’Azure Search."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Création d’un index Azure Search à l’aide de .NET
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Cet article explique comment créer un index à l’aide du [kit de développement logiciel .NET d’Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx). Le contenu ci-dessous est un sous-ensemble de la documentation [Comment utiliser Azure Search à partir d’une application .NET](search-howto-dotnet-sdk.md). Consultez l’article parent pour obtenir la procédure détaillée.

Avant de créer un index, vous devez avoir établi une connexion à votre service de recherche, généralement via un objet `SearchServiceClient`. Pour garantir un redéploiement sans heurts, il est recommandé de supprimer un index existant portant le même nom, le cas échéant.

En prenant l’exemple d’un index nommé « hôtels », vous pouvez construire une méthode de la manière suivante.

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Cette méthode utilise `SearchServiceClient` pour vérifier si l'index existe et, dans l’affirmative, elle le supprime.

Pour créer un nouvel index « hôtels », construisez une méthode semblable à l’exemple suivant :

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

Cette méthode crée un objet `Index` avec une liste d’objets `Field` qui définit le schéma du nouvel index. Chaque champ a un nom, un type de données et plusieurs attributs qui définissent son comportement de recherche. En plus des champs, vous pouvez ajouter des profils de notation, des générateurs de suggestions ou des options CORS à l'index (éléments supprimés de l'exemple pour des raisons de concision). Vous trouverez plus d'informations sur l'objet Index et ses composants dans la référence du SDK sur [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx), ainsi que dans l’[API REST Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx).

<!---HONumber=Nov15_HO3-->