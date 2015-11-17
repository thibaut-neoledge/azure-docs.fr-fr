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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importer des données dans Azure Search à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Cet article explique comment remplir un index à l’aide de l’[API REST d’Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx). Une partie du contenu ci-dessous est extrait de l’article [Ajout, mise à jour ou suppression de documents] (API REST du service Azure Search)https://msdn.microsoft.com/library/azure/dn798930.aspx). Consultez l’article parent pour obtenir plus de contexte.

Avant l’importation, vous devez disposer d’un index existant pour recevoir les données.

Lorsque vous utilisez l’API REST, l’ingestion des données est basée sur une requête HTTP Put ou POST. Les extraits de code sont dérivés de l’[exemple de profils de score](search-get-started-scoring-profiles.md).

        static bool PostDocuments(string fileName)
        {
            // Add some documents to the newly created index
            Uri requestUri = new Uri(serviceUrl + indexName + "/docs/index?" + ApiVersion);

            // Load the json containing the data from an external file
            string json = File.ReadAllText(fileName);

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PostAsync(requestUri,       // To add data use POST
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.OK)
                {
                    Console.WriteLine("Documents posted from file {0}. \r\n", fileName);
                    return true;
                }

                Console.WriteLine("Documents failed to upload: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->