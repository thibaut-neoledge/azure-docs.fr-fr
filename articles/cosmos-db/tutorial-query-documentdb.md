---
title: "Comment effectuer des interrogation avec le langage SQL dans Azure Cosmos DB ? | Microsoft Docs"
description: "Apprendre à interroger à l’aide de données DocumentDB avec le langage SQL dans Azure Cosmos DB"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmosdb
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: dd34ff43e78175b0d6a6e38bbd1303070f6549ab
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-how-to-query-using-sql"></a>Azure Cosmos DB : Comment effectuer des interrogations avec le langage .SQL ?

L’[API DocumentDB](../documentdb/documentdb-introduction.md) Azure Cosmos DB prend en charge l’interrogation de documents à l’aide de SQL. Cet article fournit un exemple de document et deux exemples de requêtes SQL et de résultats.

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Interrogation des données avec SQL

## <a name="sample-document"></a>Exemple de document

L’exemple de document suivant est utilisé pour les interrogations SQL de cet article.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="where-can-i-run-sql-queries"></a>Où puis-je exécuter des requêtes SQL ?

Vous pouvez exécuter des requêtes à l’aide de l’Explorateur de données dans le portail Azure, via l’[API REST et Kits de développement logiciel (SDK) ](../documentdb/documentdb-query-collections-query-explorer.md)et même l’[interface de requête](https://www.documentdb.com/sql/demo), qui exécute des requêtes sur un ensemble d’exemples de données existant.

Pour plus d’informations sur les requêtes SQL, consultez :
* [Requête SQL et syntaxe SQL](../documentdb/documentdb-sql-query.md)

## <a name="prerequisites"></a>Composants requis

Ce didacticiel suppose que vous ayez un compte et une collection Azure Cosmos DB. Cela n’est pas le cas ? Lancez le [démarrage rapide de 5 minutes](create-mongodb-nodejs.md) ou le [didacticiel destiné aux développeurs](tutorial-develop-mongodb.md) pour créer un compte et une collection.

## <a name="example-query-1"></a>Exemple de requête 1

Étant donné l’exemple de document de famille ci-dessus, la requête SQL suivante renvoie les documents où le champ id correspond à `WakefieldFamily`. Comme il s’agit d’une instruction `SELECT *`, le résultat de la requête est le document JSON complet :

**Requête**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Résultats**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

## <a name="example-query-2"></a>Exemple de requête 2

La requête suivante renvoie tous les noms donnés des enfants de la famille dont l’ID correspond à `WakefieldFamily` classés par classe.

**Requête**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.children.grade ASC

**Résultats**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Effectuer des interrogations à l’aide de SQL  

Vous pouvez maintenant poursuivre avec le didacticiel suivant montrant comment distribuer vos données globalement.

> [!div class="nextstepaction"]
> [Distribuer vos données globalement](tutorial-global-distribution-documentdb.md)


