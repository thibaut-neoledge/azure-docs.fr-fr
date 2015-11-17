<properties
	pageTitle="Requêtes dans Azure Search | Microsoft Azure | Service de recherche cloud hébergé"
	description="Générer une requête de recherche dans Azure Search et utiliser les paramètres de recherche pour filtrer, trier et modeler les résultats de recherche."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/10/2015"
	ms.author="heidist"/>

# Requêtes dans Azure Search
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Les solutions personnalisées qui utilisent Azure Search pour prendre en charge une expérience de recherche intégré doivent inclure du code permettant de construire la requête de recherche et de l’envoyer pour traitement vers un service de recherche.

Vous pouvez utiliser le SDK .NET ou l’API REST pour écrire des méthodes d’exécution des requêtes. Dans le cadre de tests préliminaires ou d’une simple exploration, vous pouvez utiliser des outils tels que Fiddler ou Chrome Postman pour envoyer des requêtes à votre service.

<!---HONumber=Nov15_HO3-->