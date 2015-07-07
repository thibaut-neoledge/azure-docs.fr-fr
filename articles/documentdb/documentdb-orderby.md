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
	ms.date="06/04/2015" 
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
Par exemple, voici une requête pour récupérer des livres dans l'ordre décroissant de PublishTimestamp.

    SELECT * 
    FROM Books 
    ORDER BY Books.PublishTimestamp DESC

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

DocumentDB prend en charge le classement par rapport aux types numériques (pas des chaînes) et uniquement pour une seule propriété Order By par requête, dans cette version d'évaluation de la fonction. Pour plus de détails, consultez la section [Quelles sont les étapes suivantes ?](#Whats_coming_next).

## Configurer une stratégie d'indexation pour la clause Order By
Pour exécuter les requêtes Order By, vous devez :

- Indexer les chemins d'accès spécifiques dans vos documents avec une précision maximale, (ou) 
- Indexer de manière récursive *tous* les chemins d'accès de la collection entière avec une précision maximale 

La précision maximale (représentée en tant que précision de -1 dans la configuration JSON) utilise un nombre variable d'octets en fonction de la valeur en cours d'indexation. Par conséquent :

- Les propriétés contenant des valeurs numériques plus grandes, comme par exemple les horodatages d'époque, auront une précision maximale avec une surcharge d'index élevée. 
- Les propriétés contenant des valeurs numériques plus petites (énumérations, zéros, codes postaux, âge, etc.) auront une surcharge d'index faible.

Pour plus d'informations, consultez [Stratégies d'indexation de DocumentDB](documentdb-indexing-policies.md).

### Indexation pour la clause Order By sur toutes les propriétés numériques
Voici comment vous pouvez créer une collection avec indexation pour la clause Order By sur n'importe quelle propriété (numérique).
                   

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range, 
            Path = "/",
            NumericPrecision = -1 });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

### Indexation pour la clause Order By d'une propriété unique
Voici comment vous pouvez créer une collection avec indexation pour la clause Order By sur la propriété PublishTimestamp uniquement.

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range,
            Path = "/"PublishTimestamp"/?",
            NumericPrecision = -1
        });

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            Path = "/"
        });


## Exemples
Examinez ce [projet d'exemples Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) qui montre comment utiliser la clause Order By, y compris la création de stratégies d'indexation et de pagination à l'aide de la clause Order By. Les exemples sont open source et nous vous invitons à envoyer des requêtes d'extraction avec des contributions qui permettront aux autres développeurs DocumentDB de les exploiter. Veuillez vous reporter aux [instructions de contribution](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) pour obtenir des conseils sur la façon de contribuer.

## Quelles sont les étapes suivantes ?

Les mises à jour futures du service développeront davantage la prise en charge de la clause Order By présentée ici. Nous travaillons sur les ajouts suivants et la publication de ces améliorations sera basée sur vos commentaires :

- Stratégies d'indexation dynamique : prise en charge permettant de modifier la stratégie d'indexation après la création de la collection.
- Index de plage de chaîne : Index pour la prise en charge des requêtes de plage (>, <, >=, <=) par rapport aux valeurs de chaîne. Pour cela, nous lancerons un nouveau schéma plus riche pour l'indexation de stratégies.
- Prise en charge de chaîne Order By dans la requête DocumentDB.
- Possibilité de mettre à jour la stratégie d'indexation à l'aide du portail Azure en version préliminaire.
- Prise en charge des index composés pour une clause Order By plus efficace et une clause Order By sur plusieurs propriétés.

## Forum Aux Questions

**Quelles plateformes/versions du Kit de développement logiciel (SDK) prennent le classement en charge ?**

Étant donné que la clause Order By est une mise à jour côté serveur, il est inutile de télécharger une nouvelle version du SDK pour utiliser cette fonctionnalité. Toutes les plateformes et les versions du SDK, notamment le SDK JavaScript côté serveur, peuvent utiliser la clause Order By à l'aide de chaînes de requête SQL. Si vous utilisez LINQ, vous devez télécharger la version 1.2.0 ou version plus récente de Nuget.

**Quelle est la consommation d'unités de demande (RU) des requêtes Order By ?**

Étant donné que la clause Order By utilise l'index DocumentDB pour les recherches, le nombre d'unités de demande consommées par les requêtes Order By sera semblable aux requêtes équivalentes sans Order By. Comme toute autre opération sur DocumentDB, le nombre d'unités de demande dépend de la taille/forme des documents, ainsi que de la complexité de la requête.


**Quelle est la surcharge d'index attendue pour Order By ?**

La surcharge de stockage d'index sera proportionnelle au nombre de propriétés numériques. Dans le pire des cas, la surcharge d'index représentera 100 % des données. Il n'existe aucune différence en termes de surcharge de débit (unités de demande) entre l'indexation Range/Order By et l'indexation de hachage par défaut.

**Ce changement a-t-il un impact sur les requêtes sans Order By ?**

À présent, le fonctionnement des requêtes sans Order By reste inchangé. Avant la publication de cette fonctionnalité, toutes les requêtes DocumentDB renvoyaient des résultats dans l'ordre ResourceId (_rid). Avec Order By, les requêtes sont naturellement renvoyées dans l'ordre spécifié des valeurs. Dans les requêtes Order By, _rid sera utilisé comme ordre de tri secondaire lorsque plusieurs documents avec la même valeur sont renvoyés.

**Comment puis-je interroger mes données existantes dans DocumentDB à l'aide de la clause Order By ?**

Ceci sera pris en charge avec la disponibilité de l'amélioration des Stratégies d'indexation dynamique mentionnée dans la section [Quelles sont les étapes suivantes ?](what's-coming-next). Pour faire ceci aujourd'hui, vous devez exporter vos données et les réimporter dans une nouvelle collection DocumentDB créée avec un index Range/Order By. L'outil d'importation de DocumentDB permet de migrer les données entre les collections.

**Quelles sont les limitations actuelles de la clause Order By ?**

La clause Order By ne peut être spécifiée que sur une propriété numérique et uniquement en cas d'indexation de la plage avec une précision maximale de (-1). La clause Order By est uniquement prise en charge sur les collections de documents.

Vous ne pouvez pas effectuer les opérations suivantes :
 
- Order By avec des propriétés de chaîne (disponible prochainement).
- Order By avec des propriétés de chaîne internes comme id, _rid et _self (disponible prochainement). - Order By avec des propriétés dérivées du résultat d'une jointure à l'intérieur du document (disponible prochainement).
- Order By sur plusieurs propriétés (disponible prochainement).
- Order By avec des propriétés calculées, par exemple : le résultat d'une expression ou une fonction UDF/intégrée.
- Order By avec des requêtes sur les bases de données, les collections, les utilisateurs, les autorisations ou les pièces jointes.

## Étapes suivantes

Répliquez le [projet d'exemples Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) et classez vos données !

## Références
* [Référence de requête DocumentDB](documentdb-sql-query.md)
* [Référence de stratégie d'indexation de DocumentDB](documentdb-indexing-policies.md)
* [Référence SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Exemples Order By de DocumentDB](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)
 

<!---HONumber=62-->