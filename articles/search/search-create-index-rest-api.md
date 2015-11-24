<properties
	pageTitle="Créer un index Azure Search à l’aide d’une API REST | Microsoft Azure | Service de recherche cloud hébergé"
	description="Créer un index dans le code à l’aide d’Azure Search et d’une API REST HTTP."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# Création d’un index Azure Search à l’aide d’une API REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Cet article explique comment créer un index à l’aide de l’[API REST d’Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx). Une partie du contenu ci-dessous est extraite de l’article [Création d’index (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Consultez l’article parent pour obtenir plus de contexte.

Avant de créer un index, vous devez avoir établi une connexion à votre service de recherche, généralement via un « httpClient ».

## Définir le schéma d’index

Pour créer un index à l’aide de l’API REST, vous devez émettre une requête POST au point de terminaison URL d’Azure Search. Vous allez définir la structure de votre index à l’aide de JSON dans le corps de la demande.

**Demande et en-têtes de demande** :

Dans l’exemple ci-dessous, vous devrez utiliser le point de terminaison de l’URL de votre service, et en particulier, noter le nom du service et de la version de l’API appropriée (la version actuelle de l’API est « 2015-02-28 » au moment de la publication de ce document). Dans les en-têtes de demande, vous devrez également fournir la clé administrateur primaire que vous avez reçue lors de la [création d’un service](https://msdn.microsoft.com/library/azure/dn798941.aspx/).

	POST https://[servicename].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Corps de la requête** :

C’est là que vous identifiez votre nom d’index (en l’occurrence, « hôtels »), ainsi que les [noms, types et attributs des champs](https://msdn.microsoft.com/library/azure/dn798941.aspx).

	{
		"name": "hotels",  
		"fields": [
			{"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
			{"name": "baseRate", "type": "Edm.Double"},
			{"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
			{"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer:"fr.lucene"},
			{"name": "hotelName", "type": "Edm.String"},
			{"name": "category", "type": "Edm.String"},
			{"name": "tags", "type": "Collection(Edm.String)"},
			{"name": "parkingIncluded", "type": "Edm.Boolean"},
			{"name": "smokingAllowed", "type": "Edm.Boolean"},
			{"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
			{"name": "rating", "type": "Edm.Int32"},
			{"name": "location", "type": "Edm.GeographyPoint"}
		]
	}

Pour que votre requête aboutisse, vous devez voir le code d’état « 201 créé ». Pour plus d’informations sur la création d’un index via l’API REST, visitez [cette page](https://msdn.microsoft.com/library/azure/dn798941.aspx).

<!---HONumber=Nov15_HO4-->