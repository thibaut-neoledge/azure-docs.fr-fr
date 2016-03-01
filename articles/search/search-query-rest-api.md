<properties
	pageTitle="Générer des requêtes dans Azure Search à l’aide d’appels REST | Microsoft Azure | Service de recherche cloud hébergé"
	description="Générer une requête de recherche dans Azure Search et utiliser les paramètres de recherche pour filtrer, trier et modeler les résultats de recherche à l’aide de la bibliothèque .NET ou du kit de développement logiciel."
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
	ms.date="02/17/2016"
	ms.author="heidist"/>

# Générer des requêtes dans Azure Search à l’aide d’appels REST
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Cet article explique comment construire une requête sur un index à l’aide de l’[API REST d’Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx). Une partie du contenu ci-dessous est extrait de l’article [Recherche de documents (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Consultez l’article parent pour obtenir plus de contexte.

Avant l’importation, vous devez disposer d’un index existant associé à des documents qui fournissent des données de recherche.

Pour faire une recherche sur votre index à l’aide de l’API REST, vous devez émettre une requête HTTP GET. Vos paramètres de requête seront définis au sein de l’URL de la requête HTTP.

**Demande et en-têtes de demande** :

Dans l’URL, vous devrez fournir le nom de votre service, le nom d’index, ainsi que la version appropriée de l’API. La chaîne de requête à la fin de l’URL sera à l’endroit où vous fournissez les paramètres de requête. L’un des paramètres de la chaîne de requête doit être la version API appropriée (la version actuelle de l’API est « 2015-02-28 » au moment de la publication de ce document).

Dans les en-têtes de demande, vous devez définir le Type de contenu et de fournir la clé d’administration primaire ou secondaire de votre service.

	GET https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Azure Search propose de nombreuses options pour créer des requêtes extrêmement performantes. Pour en savoir plus sur les différents paramètres d’une chaîne de requête, visitez [cette page](https://msdn.microsoft.com/library/azure/dn798927.aspx).

**Exemples :**

Voici quelques exemples avec différentes chaînes de requête. Ces exemples utilisent un index factice nommé « hôtels » :

recherche le terme « qualité » dans l’ensemble de l’index :

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=quality&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Rechercher dans l’ensemble de l’index :

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Rechercher la totalité de l’index et ordonner en fonction d’un champ spécifique (lastRenovationDate) :

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Une demande de requête réussie entraîne un Code d’état « 200 OK » et les résultats de recherche se trouvent au format JSON dans le corps de réponse. Pour plus d’informations, consultez la section « Réponse » de [cette page](https://msdn.microsoft.com/library/azure/dn798927.aspx).

<!---HONumber=AcomDC_0224_2016-->