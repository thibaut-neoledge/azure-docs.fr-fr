<properties 
	pageTitle="Tri de données DocumentDB à l'aide de la clause Order By | Azure" 
	description="Découvrez comment utiliser ORDER BY dans des requêtes DocumentDB dans LINQ et SQL, et comment spécifier une stratégie d'indexation pour les requêtes ORDER BY." 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="arramac"/>

# Tri de données DocumentDB à l'aide de la clause Order By
Microsoft Azure DocumentDB prend en charge l'interrogation de documents à l'aide du langage SQL sur les documents JSON. Il est possible de trier les résultats de la requête à l'aide de la clause ORDER BY dans les instructions de requête SQL.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

- Comment interroger avec la clause Order By ?
- Comment configurer une stratégie d'indexation pour la clause Order By ?
- Quelles sont les étapes suivantes ?

Des [exemples](#samples) et un [FAQ](#faq) sont également fournis.

Pour obtenir une référence complète sur les requêtes SQL, consultez le [didacticiel sur les requêtes DocumentDB](documentdb-sql-query.md).

## Interrogation avec la clause Order By
Comme dans ANSI-SQL, vous pouvez désormais inclure une clause Order By facultative dans les instructions SQL lors de l'interrogation de DocumentDB. La clause peut inclure un argument ASC/DESC facultatif pour spécifier l'ordre dans lequel les résultats doivent être récupérés.

### Classement à l'aide de SQL
Par exemple, voici une requête pour récupérer des livres dans l'ordre décroissant de leurs titres.

    SELECT * 
    FROM Books 
    ORDER BY Books.Title DESC

### Classement à l'aide de SQL avec filtrage
Vous pouvez classer à l'aide de n'importe quelle propriété imbriquée au sein de documents comme Books.ShippingDetails.Weight, et vous pouvez spécifier des filtres supplémentaires dans la clause WHERE en combinaison avec Order By, comme dans l'exemple suivant :

    SELECT * 
    FROM Books 
	WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### Classement à l'aide du fournisseur LINQ pour .NET
À l'aide du Kit de développement logiciel (SDK) .NET version 1.2.0 et versions ultérieures, vous pouvez également utiliser la clause OrderBy() ou OrderByDescending() dans les requêtes LINQ comme dans l'exemple suivant :

    foreach (Book book in client.CreateDocumentQuery<Book>(booksCollection.SelfLink)
        .OrderBy(b => b.PublishTimestamp)) 
    {
        // Iterate through books
    }

### Classement avec pagination à l'aide du Kit de développement logiciel (SDK) .NET
À l'aide de la prise en charge native de la pagination dans les SDK DocumentDB, vous pouvez récupérer une page de résultats à la fois, comme dans l'extrait de code .NET suivant. Ici, nous extrayons jusqu'à 10 résultats simultanément à l'aide de FeedOptions.MaxItemCount et de l'interface IDocumentQuery.

    var booksQuery = client.CreateDocumentQuery<Book>(
        booksCollection.SelfLink,
        "SELECT * FROM Books ORDER BY Books.PublishTimestamp DESC"
        new FeedOptions { MaxItemCount = 10 })
      .AsDocumentQuery();
            
    while (booksQuery.HasMoreResults) 
    {
        foreach(Book book in await booksQuery.ExecuteNextAsync<Book>())
        {
            // Iterate through books
        }
    }

DocumentDB prend en charge le tri avec une chaîne numérique unique ou une propriété booléenne par requête, avec des types de requêtes supplémentaires prochainement disponibles. Pour plus de détails, consultez la section [Quelles sont les étapes suivantes ?](#Whats_coming_next).

## Configurer une stratégie d'indexation pour la clause Order By

Rappelez-vous que DocumentDB prend en charge deux types d'index (le hachage et la plage), qui peuvent être définis pour des chemins d'accès ou des propriétés spécifiques, des types de données (chaînes ou numéros) et à des valeurs de précision différentes (précision maximale ou une valeur de précision fixe). Étant donné que DocumentDB utilise le hachage indexation par défaut, vous devez créer une nouvelle collection avec une stratégie d'indexation personnalisée avec une plage de nombres, de chaînes ou les deux, pour pouvoir utiliser Order By.

>[AZURE.NOTE]Les index de plage de chaîne ont été introduits le 7 juillet 2015 avec la version 2015-06-03 de l'API REST. Pour créer des stratégies pour Order By sur les chaînes, vous devez utiliser la version 1.2.0 du Kit de développement logiciel (SDK) .NET ou la version 1.1.0 du Kit de développement logiciel (SDK) Python, Node.js ou Java.
>
>Avant la version 2015-06-03 de l'API REST, la stratégie d'indexation de la collection par défaut était le hachage des chaînes et des nombres. Désormais, la stratégie est définie par le hachage pour les chaînes et la plage pour les nombres.

Pour plus d'informations, consultez [Stratégies d'indexation de DocumentDB](documentdb-indexing-policies.md).

### Indexation pour la clause Order By sur toutes les propriétés numériques
Voici comment vous pouvez créer une collection avec l’indexation « Toutes les plages » pour Order By sur toutes les propriétés numériques ou de chaîne qui apparaissent dans les documents JSON. Ici, « /* » représente toutes les propriétés ou tous les chemins dans la collection, et -1 représente la précision maximale.
                   
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

>[AZURE.NOTE]Notez que Order By retourne uniquement des résultats des types de données (chaîne et nombre) qui sont indexés avec un RangeIndex. Par exemple, si vous disposez d'une stratégie d’indexation par défaut qui ne possède qu’une RangeIndex pour les nombres, une clause Order By sur un chemin d'accès avec des valeurs de chaîne ne retournera aucun document.

### Indexation pour la clause Order By d'une propriété unique
Voici comment vous pouvez créer une collection avec une indexation pour la clause Order By pour la propriété Title qui est une chaîne. Il existe deux chemins d'accès, un pour la propriété Title ("/Titre/?") avec l'indexation de plage, et l'autre pour toutes les autres propriétés avec le modèle d'indexation par défaut, qui est le hachage pour les chaînes et la plage pour les nombres.
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    // Use defaults which are:
    // (a) for strings, use Hash with precision 3 (just equality queries)
    // (b) for numbers, use Range with max precision (for equality, range and order by queries)
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*",
            Indexes = new Collection<Index> { 
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }            
        });

## Exemples
Examinez ce [projet d'exemples Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) qui montre comment utiliser la clause Order By, y compris la création de stratégies d'indexation et de pagination à l'aide de la clause Order By. Les exemples sont open source et nous vous invitons à envoyer des requêtes d'extraction avec des contributions qui permettront aux autres développeurs DocumentDB de les exploiter. Veuillez vous reporter aux [instructions de contribution](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) pour obtenir des conseils sur la façon de contribuer.

## Quelles sont les étapes suivantes ?

Les mises à jour futures du service développeront davantage la prise en charge de la clause Order By présentée ici. Nous travaillons sur les ajouts suivants et la publication de ces améliorations sera basée sur vos commentaires :

- Stratégies d'indexation dynamique : prise en charge permettant de modifier la stratégie d'indexation après la création de la collection dans le portail Azure.
- Prise en charge des index composés pour une clause Order By plus efficace et une clause Order By sur plusieurs propriétés.

## Forum Aux Questions

**Quelles plateformes/versions du Kit de développement logiciel (SDK) prennent le classement en charge ?**

Pour créer des collections avec la stratégie d'indexation requise pour Order By, vous devez télécharger la dernière version du Kit de développement logiciel (SDK) (1.2.0 pour .NET et 1.1.0 pour Node.js, JavaScript, Python et Java). Le Kit de développement logiciel (SDK) .NET 1.2.0 est également requis pour utiliser OrderBy() et OrderByDescending() dans des expressions LINQ.


**Quelle est la consommation d'unités de demande (RU) des requêtes Order By ?**

Étant donné que la clause Order By utilise l'index DocumentDB pour les recherches, le nombre d'unités de demande consommées par les requêtes Order By sera semblable aux requêtes équivalentes sans Order By. Comme toute autre opération sur DocumentDB, le nombre d'unités de demande dépend de la taille/forme des documents, ainsi que de la complexité de la requête.


**Quelle est la surcharge d'index attendue pour Order By ?**

La surcharge de stockage de l'indexation sera proportionnelle au nombre de propriétés. Dans le pire des cas, la surcharge d'index représentera 100 % des données. Il n'existe aucune différence en termes de surcharge de débit (unités de demande) entre l'indexation Range/Order By et l'indexation de hachage par défaut.

**Comment puis-je interroger mes données existantes dans DocumentDB à l'aide de la clause Order By ?**

Ceci sera pris en charge avec la disponibilité de l'amélioration des Stratégies d'indexation dynamique mentionnée dans la section [Quelles sont les étapes suivantes ?](what's-coming-next). Pour faire ceci aujourd'hui, vous devez exporter vos données et les réimporter dans une nouvelle collection DocumentDB créée avec un index Range/Order By. L'outil d'importation de DocumentDB permet de migrer les données entre les collections.

**Quelles sont les limitations actuelles de la clause Order By ?**

La clause Order By peut être spécifiée uniquement pour une propriété numérique ou une chaîne lorsqu'il s’agit d’une indexation de plage avec la précision maximale (-1).

Vous ne pouvez pas effectuer les opérations suivantes :
 
- Order By avec des propriétés de chaîne internes comme id, _rid et _self (disponible prochainement). - Order By avec des propriétés dérivées du résultat d'une jointure à l'intérieur du document (disponible prochainement).
- Order By sur plusieurs propriétés (disponible prochainement).
- La clause Order By avec des requêtes sur les bases de données, les collections, les utilisateurs, les autorisations ou les pièces jointes (prochainement disponible).
- Order By avec des propriétés calculées, par exemple : le résultat d'une expression ou une fonction UDF/intégrée.

## Étapes suivantes

Répliquez le [projet d'exemples Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) et classez vos données !

## Références
* [Référence de requête DocumentDB](documentdb-sql-query.md)
* [Référence de stratégie d'indexation de DocumentDB](documentdb-indexing-policies.md)
* [Référence SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Exemples Order By de DocumentDB](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)
 

<!---HONumber=July15_HO3-->