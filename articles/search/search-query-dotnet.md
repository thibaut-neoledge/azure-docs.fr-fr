<properties
	pageTitle="Créer des requêtes dans Azure Search en utilisant .NET | Microsoft Azure | Service de recherche cloud hébergé"
	description="Créez une requête de recherche dans Azure Search et utilisez des paramètres de recherche pour filtrer, trier et formater les résultats de la recherche en utilisant la bibliothèque ou le kit de développement logiciel (SDK) .NET."
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
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Générer des requêtes dans Azure Search en utilisant .NET
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Cet article explique comment construire une requête à l’aide du [kit de développement logiciel (SDK) .NET d’Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx). Le contenu ci-dessous est un sous-ensemble de la documentation [Comment utiliser Azure Search à partir d’une application .NET](search-howto-dotnet-sdk.md). Consultez l’article parent pour obtenir la procédure détaillée.

Avant de créer une requête, vous devez avoir établi une connexion à votre service de recherche, généralement via un objet `SearchServiceClient`.

L’extrait de code suivant crée une méthode qui transmet une entrée de chaîne de recherche à une méthode SearchDocuments.

	private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
	{
		// Execute search based on search text and optional filter
		var sp = new SearchParameters();
	
		if (!String.IsNullOrEmpty(filter))
		{
			sp.Filter = filter;
		}
	
		DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
		foreach (SearchResult<Hotel> result in response)
		{
			Console.WriteLine(result.Document);
		}
	}
	
Cette méthode commence par créer un nouvel objet SearchParameters. Cela permet de spécifier des options supplémentaires pour la requête, comme le tri, le filtrage, la pagination et la facettisation. Dans cet exemple, nous définissons uniquement la propriété Filter.

L'étape suivante consiste à exécuter la requête de recherche. Cette opération est effectuée à l’aide de la méthode Documents.Search. Dans ce cas, nous transmettons le texte de recherche à utiliser en tant que chaîne, ainsi que les paramètres de recherche créés précédemment. Nous spécifions également «Hotel » comme type de paramètre pour Documents.Search, qui indique au SDK de désérialiser les documents dans les résultats de recherche en objets de type « Hotel ».

Enfin, cette méthode parcourt toutes les correspondances dans les résultats de recherche et imprime chaque document dans la console.

Examinons comment cette méthode est appelée :

	SearchDocuments(indexClient, searchText: "fancy wifi");
	SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Dans le premier appel, nous recherchons tous les documents contenant les termes « fancy » ou « wifi ». Dans le deuxième appel, le texte à rechercher est « * », ce qui signifie « rechercher tous les éléments ». Pour plus d’informations sur la syntaxe des requêtes de recherche, consultez [Syntaxe des requêtes simples dans Azure Search](https://msdn.microsoft.com/library/azure/dn798920.aspx).

Le deuxième appel utilise une expression OData $filter, équivalant à la catégorie « Luxury ». Celle-ci limite la recherche pour ne renvoyer que les documents dont le champ de catégorie correspond exactement à la chaîne « Luxury ». Pour plus d’informations sur la syntaxe OData, consultez [Syntaxe des expressions OData pour Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx).

<!---HONumber=AcomDC_0128_2016-->