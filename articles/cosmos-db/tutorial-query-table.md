---
title: "Comment interroger des données de table dans Azure Cosmos DB ? | Microsoft Docs"
description: "Apprendre à interroger des données de table dans Azure Cosmos DB"
services: cosmosdb
documentationcenter: 
author: kanshiG
manager: jhubbard
editor: 
tags: 
ms.assetid: 14bcb94e-583c-46f7-9ea8-db010eb2ab43
ms.service: cosmosdb
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: govindk
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: cdd855aeac7dd30c52accb407289ca6db7dab4ae
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-how-to-query-with-the-table-api-preview"></a>Azure Cosmos DB : Comment interroger avec l’API de table (version préliminaire) ?

L’[API de table](table-introduction.md) d’Azure Cosmos DB (version préliminaire) prend en charge les requêtes OData et [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) requêtes sur des données de clé/valeur (table).  

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Interrogation des données avec l’API de table

## <a name="sample-table"></a>Exemple de table

L’exemple de table `People` suivant est utilisé pour les interrogations de cet article :

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Walter | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 | 

## <a name="about-the-table-api-preview"></a>À propos de l’API de table (version préliminaire)

Étant donné qu’Azure Cosmos DB est compatible avec les API de stockage de table Azure, consultez [Querying Tables and Entities (Interrogation de tables et d’entités)] (https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities) pour plus d’informations sur la manière dont effectuer des interrogations avec l’API de table. 

Pour plus d’informations sur les fonctionnalités premium offertes par Azure Cosmos DB, consultez [Azure Cosmos DB: Table API](table-introduction.md) (Azure Cosmos DB : API de table) et [Develop with the Table API using .NET](tutorial-develop-table-dotnet.md) (Développer avec l’API de table à l’aide de .NET. 

## <a name="prerequisites"></a>Composants requis

Pour le bon fonctionnement de ces requêtes, vous devez disposer d’un compte Azure Cosmos DB et de données d’entité dans le conteneur. Cela n’est pas le cas ? Lancez le [démarrage rapide de 5 minutes](https://aka.ms/acdbtnetqs) ou le [didacticiel destiné aux développeurs](https://aka.ms/acdbtabletut) pour créer un compte et alimenter votre base de données.

## <a name="querying-on-partition-key-and-row-key"></a>Interrogation de la clé de partition et de la clé de ligne
Étant donné que les propriétés PartitionKey et RowKey forment la clé principale d’une entité, vous pouvez utiliser une syntaxe spéciale pour identifier l’entité, comme suit : 

**Requête**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**Résultats**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

Vous pouvez également spécifier ces propriétés dans le cadre de l’option `$filter`, comme indiqué dans la section suivante. Notez que les noms de propriété de clé et les valeurs de constante respectent la casse. Les propriétés PartitionKey et RowKey sont de type chaîne. 

## <a name="querying-with-an-odata-filter"></a>Interrogation avec un filtre ODATA
Lorsque vous élaborez une chaîne de filtrage, n’oubliez pas les règles suivantes : 

* Utilisez les opérateurs logiques définis par la spécification du protocole OData Protocol pour comparer une propriété par rapport à une valeur. Notez qu’il n’est pas possible de comparer une propriété à une valeur dynamique, car l’une des parties de l’expression doit avoir une valeur constante. 
* Le nom de la propriété, l’opérateur et la valeur de constante doivent être séparés par des espaces codés URL. Un espace est codé URL sous la forme `%20`. 
* Toutes les parties de la chaîne de filtrage respectent la casse. 
* Pour que le filtre retourne des résultats valides, la valeur constante doit être du même type de données que la propriété. Pour plus d’informations sur les types de propriétés pris en charge, consultez [Présentation du modèle de données du service de Table](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). 

Voici un exemple de requête montre comment filtrer par PartitionKey et la propriété Email utilisant un protocole ODATA `$filter`.

**Requête**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Pour en savoir plus sur la façon d’élaborer des expressions de filtre pour différents types de données, consultez l’article [Querying Tables and Entities (Interrogation de tables et d’entités)](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities)

**Résultats**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Ben |Smith | Ben@contoso.com| 425-555-0102 |

## <a name="querying-with-linq"></a>Interrogation avec LINQ 
Vous pouvez également effectuer une interrogation à l’aide de LINQ, ce qui se traduit par les expressions de requête ODATA correspondantes. Voici un exemple montrant comment créer des requêtes à l’aide du Kit de développement logiciel (SDK) .NET.

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("people");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition(PartitionKey, QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition(Email, QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Effectuer des interrogations à l’aide de l’API de table (version préliminaire) 

Vous pouvez maintenant poursuivre avec le didacticiel suivant montrant comment distribuer vos données globalement.

> [!div class="nextstepaction"]
> [Distribuer vos données globalement](tutorial-global-distribution-documentdb.md)

