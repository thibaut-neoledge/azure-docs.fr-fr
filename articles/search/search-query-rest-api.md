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
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Générer des requêtes dans Azure Search à l’aide d’appels REST 
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Cet article explique comment construire une requête sur un index à l’aide de l’[API REST d’Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx). Une partie du contenu ci-dessous est extrait de l’article [Recherche de documents (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Consultez l’article parent pour obtenir plus de contexte.

Avant l’importation, vous devez disposer d’un index existant associé à des documents qui fournissent des données de recherche.

Lorsque vous utilisez l’API REST, les requêtes sont basées sur une requête HTTP GET. Les extraits de code sont dérivés de l’[exemple de profils de score](search-get-started-scoring-profiles.md).

        static JObject ExecuteRequest(string action, string query = "")
        {
            // original:  string url = serviceUrl + indexName + "/" + action + "?" + ApiVersion; 
            string url = serviceUrl + indexName + "/docs?" + action ;
            if (!String.IsNullOrEmpty(query))
            {
                url += query + "&" + ApiVersion;
            }

            string response = ExecuteGetRequest(url);
            return JObject.Parse(response);

        }

        static string ExecuteGetRequest(string requestUri)
        {
            //This will execute a get request and return the response
            using (HttpClient client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.GetAsync(requestUri).Result;        // Searches are done over port 80 using Get
                return response.Content.ReadAsStringAsync().Result;
            }

        }

<!---HONumber=Nov15_HO3-->