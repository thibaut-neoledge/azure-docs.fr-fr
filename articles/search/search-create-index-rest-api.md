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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Création d’un index Azure Search à l’aide d’une API REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Cet article explique comment créer un index à l’aide de l’[API REST d’Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx). Une partie du contenu ci-dessous est extraite de l’article [Création d’index (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Consultez l’article parent pour obtenir plus de contexte.

Avant de créer un index, vous devez avoir établi une connexion à votre service de recherche, généralement via un « httpClient ».

La création d’un index à l’aide de l’API REST se déroule en trois temps : configuration d’une connexion, fourniture d’un schéma d’index et exécution d’une commande qui génère l’index. Les extraits de code sont dérivés de l’[exemple de profils de score](search-get-started-scoring-profiles.md).

##Définir le schéma d’index

L’API REST accepte les demandes dans JSON. Pour créer le schéma, vous pouvez inclure un fichier de schéma autonome dans un document JSON. L’exemple suivant illustre cette approche.

Dans l’exemple à partir duquel est dérivé cet extrait de code, le JSON ci-dessous est enregistré dans un fichier nommé schema.json.

	{
	    "name": "musicstoreindex",
	    "fields": [
	        { "name": "key", "type": "Edm.String", "key": true },
	        { "name": "albumTitle", "type": "Edm.String"},
	        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	        { "name": "genre", "type": "Edm.String" },
	        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	        { "name": "artistName", "type": "Edm.String"},
	        { "name": "orderableOnline", "type": "Edm.Boolean" },
	        { "name": "rating", "type": "Edm.Int32" },
	        { "name": "tags", "type": "Collection(Edm.String)" },
	        { "name": "price", "type": "Edm.Double", "filterable": false },
	        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	        { "name": "inventory", "type": "Edm.Int32" },
	        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	    ],
	    "scoringProfiles": [
	        {
	            "name": "boostGenre",
	            "text": {
	                "weights": {
	                    "albumTitle": 5,
	                    "genre": 50,
	                    "artistName": 5
	                }
	            }
	        },
	        {
	            "name": "newAndHighlyRated",
	            "functions": [
	                {
	                    "type": "freshness",
	                    "fieldName": "lastUpdated",
	                    "boost": 10,
	                    "interpolation": "quadratic",
	                    "freshness": {
	                        "boostingDuration": "P365D"
	                    }
	                },
	                {
	                    "type": "magnitude",
	                    "fieldName": "rating",
	                    "boost": 10,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 1,
	                        "boostingRangeEnd": 5,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        },
	        {
	            "name": "boostMargin",
	            "functions": [
	
	                {
	                    "type": "magnitude",
	                    "fieldName": "margin",
	                    "boost": 50,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 50,
	                        "boostingRangeEnd": 100,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        }
	    ]
	}

##Paramétrer une requête HTTP pour établir une connexion et créer un index

Les API REST formulent la connexion HTTP à chaque demande. Chaque demande spécifie toujours l’URL du service, la version de l’API que vous utilisez, ainsi qu’une clé administrateur qui accorde les droits d’accès en lecture-écriture sur le service (cette clé est ici appelée « clé primaire », car c’est ainsi qu’elle est référencée dans le portail).

L’extrait de code utilise des valeurs statiques en tant qu’entrées, notamment un nom pour l’index, comme spécifié dans le fichier app.config (non illustré). L’exemple a été créé ainsi dans un souci de simplicité.

        private const string ApiVersion = "api-version=2015-02-28";
        private static string serviceUrl = ConfigurationManager.AppSettings["serviceUrl"];
        private static string indexName = ConfigurationManager.AppSettings["indexName"];
        private static string primaryKey = ConfigurationManager.AppSettings["primaryKey"];

L’exemple suivant décrit une demande HTTP PUT envoyée à l’URL de service avec un `api-version` ainsi que le nom de l’index à créer.

        static bool CreateIndex()
        {
            // Create an index using an index name
            Uri requestUri = new Uri(serviceUrl + indexName + "?" + ApiVersion);

            // Load the json containing the schema from an external file
            string json = File.ReadAllText("schema.json");

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PutAsync(requestUri,        // To create index use PUT
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.Created)   // For Posts we want to know response had no content
                {
                    Console.WriteLine("Index created. \r\n");
                    return true;
                }

                Console.WriteLine("Index creation failed: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->