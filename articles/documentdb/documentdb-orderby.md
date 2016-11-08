---
title: Tri de données DocumentDB à l'aide de la clause Order By | Microsoft Docs
description: Découvrez comment utiliser ORDER BY dans des requêtes DocumentDB dans LINQ et SQL, et comment spécifier une stratégie d'indexation pour les requêtes ORDER BY.
services: documentdb
author: arramac
manager: jhubbard
editor: cgronlun
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2016
ms.author: arramac

---
# Tri de données DocumentDB à l'aide de la clause Order By
Microsoft Azure DocumentDB prend en charge l'interrogation de documents à l'aide du langage SQL sur les documents JSON. Il est possible de trier les résultats de la requête à l'aide de la clause ORDER BY dans les instructions de requête SQL.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

* Comment interroger avec la clause Order By ?
* Comment configurer une stratégie d'indexation pour la clause Order By ?
* Quelles sont les étapes suivantes ?

Des [exemples](#samples) et un [FAQ](#faq) sont également fournis.

Pour obtenir une référence complète sur les requêtes SQL, consultez le [didacticiel sur les requêtes DocumentDB](documentdb-sql-query.md).

## Interrogation avec la clause Order By
Comme dans ANSI-SQL, vous pouvez désormais inclure une clause Order By facultative dans les instructions SQL lors de l'interrogation de DocumentDB. La clause peut inclure un argument ASC/DESC facultatif pour spécifier l'ordre dans lequel les résultats doivent être récupérés.

### Classement à l'aide de SQL
Par exemple, voici une requête pour récupérer les 10 premiers livres dans l’ordre décroissant de leurs titres.

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### Classement à l'aide de SQL avec filtrage
Vous pouvez classer à l'aide de n'importe quelle propriété imbriquée au sein de documents comme Books.ShippingDetails.Weight, et vous pouvez spécifier des filtres supplémentaires dans la clause WHERE en combinaison avec Order By, comme dans l'exemple suivant :

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### Classement à l'aide du fournisseur LINQ pour .NET
À l'aide du Kit de développement logiciel (SDK) .NET version 1.2.0 et versions ultérieures, vous pouvez également utiliser la clause OrderBy() ou OrderByDescending() dans les requêtes LINQ comme dans l'exemple suivant :

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB prend en charge le tri avec une chaîne numérique unique ou une propriété booléenne par requête, avec des types de requêtes supplémentaires prochainement disponibles. Pour plus de détails, consultez la section [Quelles sont les étapes suivantes ?](#Whats_coming_next).

## Configurer une stratégie d'indexation pour la clause Order By
Rappelez-vous que DocumentDB prend en charge deux types d'index (le hachage et la plage), qui peuvent être définis pour des chemins d'accès ou des propriétés spécifiques, des types de données (chaînes ou numéros) et à des valeurs de précision différentes (précision maximale ou une valeur de précision fixe). Étant donné que DocumentDB utilise le hachage indexation par défaut, vous devez créer une nouvelle collection avec une stratégie d'indexation personnalisée avec une plage de nombres, de chaînes ou les deux, pour pouvoir utiliser Order By.

> [!NOTE]
> Les index de plage de chaîne ont été introduits le 7 juillet 2015 avec la version 2015-06-03 de l'API REST. Pour créer des stratégies pour Order By sur les chaînes, vous devez utiliser la version 1.2.0 du Kit de développement logiciel (SDK) .NET ou la version 1.1.0 du Kit de développement logiciel (SDK) Python, Node.js ou Java.
> 
> Avant la version 2015-06-03 de l'API REST, la stratégie d'indexation de la collection par défaut était le hachage des chaînes et des nombres. Désormais, la stratégie est définie par le hachage pour les chaînes et la plage pour les nombres.
> 
> 

Pour plus d'informations, consultez [Stratégies d'indexation de DocumentDB](documentdb-indexing-policies.md).

### Indexation pour la clause Order By sur toutes les propriétés numériques
Voici comment vous pouvez créer une collection avec l’indexation « Toutes les plages » pour Order By sur toutes les propriétés numériques ou de chaîne qui apparaissent dans les documents JSON. Ici, nous remplaçons le type d’index par défaut pour les valeurs de chaîne par le type Plage, et nous définissons la précision maximale (-1).

    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

> [!NOTE]
> Notez que Order By retourne uniquement des résultats des types de données (chaîne et nombre) qui sont indexés avec un RangeIndex. Par exemple, si vous disposez d'une stratégie d’indexation par défaut qui ne possède qu’une RangeIndex pour les nombres, une clause Order By sur un chemin d'accès avec des valeurs de chaîne ne retournera aucun document.
> 
> Si vous avez défini une clé de partition pour vos collections, notez que la clause Order By est uniquement prise en charge dans les requêtes qui effectuent un filtrage par rapport à une clé de partition unique.
> 
> 

### Indexation pour la clause Order By d'une propriété unique
Voici comment vous pouvez créer une collection avec une indexation pour la clause Order By pour la propriété Title qui est une chaîne. Il existe deux chemins d'accès, un pour la propriété Title ("/Titre/?") avec l'indexation de plage, et l'autre pour toutes les autres propriétés avec le modèle d'indexation par défaut, qui est le hachage pour les chaînes et la plage pour les nombres.

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## Exemples
Examinez ce [projet d'exemples Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) qui montre comment utiliser la clause Order By, y compris la création de stratégies d'indexation et de pagination à l'aide de la clause Order By. Les exemples sont open source et nous vous invitons à envoyer des requêtes d'extraction avec des contributions qui permettront aux autres développeurs DocumentDB de les exploiter. Veuillez vous reporter aux [instructions de contribution](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) pour obtenir des conseils sur la façon de contribuer.

## Forum Aux Questions
**Quelle est la consommation d'unités de demande (RU) des requêtes Order By ?**

Étant donné que la clause Order By utilise l'index DocumentDB pour les recherches, le nombre d'unités de demande consommées par les requêtes Order By sera semblable aux requêtes équivalentes sans Order By. Comme toute autre opération sur DocumentDB, le nombre d'unités de demande dépend de la taille/forme des documents, ainsi que de la complexité de la requête.

**Quelle est la surcharge d'index attendue pour Order By ?**

La surcharge de stockage de l'indexation sera proportionnelle au nombre de propriétés. Dans le pire des cas, la surcharge d'index représentera 100 % des données. Il n'existe aucune différence en termes de surcharge de débit (unités de demande) entre l'indexation Range/Order By et l'indexation de hachage par défaut.

**Comment puis-je interroger mes données existantes dans DocumentDB à l'aide de la clause Order By ?**

Pour trier les résultats de la requête à l’aide de la clause Order By, vous devez modifier la stratégie d’indexation de la collection afin d’utiliser un type d’index de plage (Range) par rapport à la propriété utilisée pour le tri. Consultez [Modification de la stratégie d’indexation](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection).

**Quelles sont les limitations actuelles de la clause Order By ?**

La clause Order By peut être spécifiée uniquement pour une propriété numérique ou une chaîne lorsqu'il s’agit d’une indexation de plage avec la précision maximale (-1).

Vous ne pouvez pas effectuer les opérations suivantes :

* Order By avec des propriétés de chaîne internes comme id, \_rid et \_self (disponible prochainement).
* Order By avec des propriétés dérivées du résultat d'une jointure à l'intérieur du document (disponible prochainement).
* Order By sur plusieurs propriétés (disponible prochainement).
* La clause Order By avec des requêtes sur les bases de données, les collections, les utilisateurs, les autorisations ou les pièces jointes (prochainement disponible).
* Order By avec des propriétés calculées, par exemple : le résultat d'une expression ou une fonction UDF/intégrée.

## Étapes suivantes
Répliquez le [projet d'exemples Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) et classez vos données !

## Références
* [Référence de requête DocumentDB](documentdb-sql-query.md)
* [Référence de stratégie d'indexation de DocumentDB](documentdb-indexing-policies.md)
* [Référence SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Exemples Order By de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)

<!---HONumber=AcomDC_0713_2016-->