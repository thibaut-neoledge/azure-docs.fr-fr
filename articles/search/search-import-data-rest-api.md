<properties
	pageTitle="Importer des requêtes dans Azure Search à l’aide de l’API REST | Microsoft Azure | Service de recherche cloud hébergé"
	description="Comment charger des données sur un index dans Azure Search à l’aide de l’API REST."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/17/2016"
	ms.author="heidist"/>

# Importer des données dans Azure Search à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Cet article explique comment remplir un index à l’aide de l’[API REST d’Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx). Une partie du contenu ci-dessous est extraite de l’article [Ajout, mise à jour ou suppression de documents (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798930.aspx). Consultez l’article parent pour obtenir plus de contexte.

Pour distribuer des documents dans l’index à l’aide de l’API REST, vous prévoyez d’émettre les requêtes HTTP POST au point de terminaison URL de votre service.

**Demande et en-têtes de demande** :

Dans l’URL, vous devez fournir le nom de votre service, ainsi que la version d’API appropriée (la version actuelle de l’API est celle du « 2015-02-28 » au moment de la publication de ce document).

Dans les en-têtes de requête, vous devez définir le Type de contenu et de fournir la clé d’administration primaire de votre service.

	POST https://[servicename].search.windows.net/indexes/[indexname]/docs/index?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Corps de la requête** :


	{
		"value": [
			{
				"@search.action": "upload",
				"hotelId": "1",
				"baseRate": 199.0,
				"description": "Best hotel in town",
				"description_fr": "Meilleur hôtel en ville",
				"hotelName": "Fancy Stay",
				"category": "Luxury",
				"tags": ["pool", "view", "wifi", "concierge"],
				"parkingIncluded": false,
				"smokingAllowed": false,
				"lastRenovationDate": "2010-06-27T00:00:00Z",
				"rating": 5,
				"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
			},
			{
				"@search.action": "upload",
				"hotelId": "2",
				"baseRate": 79.99,
				"description": "Cheapest hotel in town",
				"description_fr": "Hôtel le moins cher en ville",
				"hotelName": "Roach Motel",
				"category": "Budget",
				"tags": ["motel", "budget"],
				"parkingIncluded": true,
				"smokingAllowed": true,
				"lastRenovationDate": "1982-04-28T00:00:00Z",
				"rating": 1,
				"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
			}
		]
	}

Dans ce cas, nous utilisons « télécharger » en tant qu’action de recherche. Lors de la mise à jour et de la suppression d’un document existant, vous pouvez utiliser « fusionner », « mergeOrUpload » et « supprimer ».

Lors de la mise à jour de vos index, vous recevrez un code d’état « 200 OK » pour une réussite. Vous recevrez un code d’état « 207 » si au moins un élément de votre requête n’a pas été indexé.

Pour plus d’informations sur les actions de document et les réponses de réussite/d’erreur, consultez [cette page](https://msdn.microsoft.com/library/azure/dn798930.aspx).

<!---HONumber=AcomDC_0224_2016-->