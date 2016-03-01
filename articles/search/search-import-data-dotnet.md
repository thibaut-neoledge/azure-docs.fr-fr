<properties
	pageTitle="Importer des données dans Azure Search à l’aide de .NET | Microsoft Azure | Service de recherche cloud hébergé"
	description="Comment charger des données sur un index dans Azure Search à l’aide du kit de développement logiciel .NET ou de la bibliothèque .NET"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Importer des données dans Azure Search à l’aide de .NET
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Cet article explique comment remplir un index à l’aide du [kit de développement logiciel .NET d’Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx). Le contenu ci-dessous est un sous-ensemble de la documentation [Comment utiliser Azure Search à partir d’une application .NET](search-howto-dotnet-sdk.md). Consultez l’article parent pour obtenir la procédure détaillée.

Avant d’importer des données dans un index, vous devez déjà avoir créé un index.

En prenant l’exemple d’un index nommé « hôtels », vous pouvez construire une méthode d’importation de données de la manière suivante.

La prochaine étape dans `Main` consiste à remplir l'index créé. Cette opération est effectuée par la méthode suivante :

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                { 
                    HotelId = "1058-441", 
                    HotelName = "Fancy Stay", 
                    BaseRate = 199.0, 
                    Category = "Luxury", 
                    Tags = new[] { "pool", "view", "concierge" }, 
                    ParkingIncluded = false, 
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                    Rating = 5, 
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "666-437", 
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "970-501", 
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "956-532", 
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "566-518", 
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(sitecoreItems);
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Cette méthode présente quatre parties. La première crée un tableau d’objets `Hotel` qui servent de données d'entrée à charger dans l'index. Ces données sont codées en dur pour plus de simplicité. Dans votre application, vos données seront probablement issues d'une source de données externe, comme une base de données SQL.

La deuxième partie crée un `IndexAction` pour chaque `Hotel`, puis les regroupe dans un nouveau `IndexBatch`. Le lot est ensuite chargé dans l'index Azure Search par la méthode `Documents.Index`.

> [AZURE.NOTE] Dans cet exemple, nous allons simplement charger les documents. Si vous souhaitez fusionner les modifications dans un document existant ou supprimer un document, vous pouvez utiliser les méthodes `Merge`, `MergeOrUpload` ou `Delete` correspondantes.

La troisième partie de cette méthode est un bloc catch qui gère un cas d'erreur important pour l'indexation. Si votre service Azure Search ne parvient pas à indexer certains documents du lot, `Documents.Index` génère un `IndexBatchException`. Cela peut se produire si vous indexez des documents lorsque votre service est surchargé. **Nous vous recommandons vivement de prendre en charge explicitement ce cas de figure dans votre code.** Vous pouvez retarder puis relancer l'indexation des documents qui ont échoué, ouvrir une session et continuer comme dans l’exemple, ou faire autre chose selon la cohérence des données requise par votre application.

Enfin, la méthode retarde son exécution de deux secondes. L'indexation s’exécutant en mode asynchrone dans votre service Azure Search, l'exemple d'application doit attendre quelque temps afin de s'assurer que les documents sont disponibles pour la recherche. Ce genre de retard n’est nécessaire que dans les démonstrations, les tests et les exemples d'applications.

<!---HONumber=AcomDC_0224_2016-->