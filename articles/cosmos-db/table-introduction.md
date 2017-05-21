---
title: "Introduction à l’API de table d’Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment vous pouvez utiliser Azure Cosmos DB pour stocker et interroger d’immenses volumes de données de données de valeur de clé avec une faible latence, avec les API MongoDB OSS populaires."
services: cosmosdb
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8226ecfb5fa8c70b162ae776679302db6cfdcdbc
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Présentation d’Azure Cosmos DB : API de table

[Azure Cosmos DB](introduction.md) est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale pour les applications stratégiques. Azure Cosmos DB fournit la [distribution mondiale clés en main](../documentdb/documentdb-distribute-data-globally.md), la [mise à l’échelle élastique du débit et du stockage](partition-data.md), des latences de l’ordre de quelques millisecondes dans le monde entier dans plus de 99 pour cent des cas, [cinq niveaux de cohérence bien définis](../documentdb/documentdb-consistency-levels.md) et une garantie d’une haute disponibilité, le tout soutenu par nos [contrats SLA de pointe](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/). Azure Cosmos DB [indexe automatiquement les données](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sans avoir à gérer la gestion des schémas et des index. Il est multi-modèle et prend en charge les modèles de données en colonnes, graphiques et clé-valeur. 

![API de stockage de table Azure et Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

Azure Cosmos DB fournit l’API de table pour les applications nécessitant un stockage clé-valeur avec un schéma flexible. Les kits de développement logiciel et API REST de [stockage de table Azure](../storage/storage-introduction.md) peuvent servir pour travailler avec Azure Cosmos DB. Vous pouvez utiliser Azure Cosmos DB pour créer des tables avec des exigences de débit élevées. Azure Cosmos DB prend actuellement en charge les tables optimisées en débit (appelées familièrement « tables premium ») dans la version préliminaire publique. 

Vous pouvez continuer à utiliser le stockage de table Azure pour les tables avec des exigences de débit et de stockage inférieures. Azure Cosmos DB introduira la prise en charge des tables optimisées pour le stockage dans une prochaine mise à jour, et les comptes de stockage de table Azure nouveaux ou existants seront mis à niveau vers Azure Cosmos DB.

## <a name="premium-and-standard-table-apis"></a>API de table standard et Premium
Si vous utilisez actuellement le stockage de table Azure, vous bénéficiez des avantages suivants en les déplaçant vers la version préliminaire de « table premium » d’Azure Cosmos DB :

|  | Stockage de table Azure | Azure Cosmos DB : stockage de table (version préliminaire) |
| --- | --- | --- |
| Latence | Rapide, mais aucune limite supérieure sur la latence | Une latence de quelques millisecondes pour les lectures et écritures, soutenue par des lectures à < 10 ms de latence et des écritures à < 15 ms de latence dans plus de 99 pour cent des cas, à n’importe quelle échelle, n’importe où dans le monde |
| Débit | Modèle de débit hautement évolutif, mais non dédié. Les tables ont une limite d’évolutivité de 20 000 opérations/s | Hautement évolutif avec un [débit dédié réservé par table](../documentdb/documentdb-request-units.md), qui est soutenu par des contrats SLA. Les comptes n’ont aucune limite supérieure sur le débit, et prennent en charge > 10 millions d’opérations/s par table |
| Diffusion mondiale | Une région unique avec une région de lecture secondaire en option pour la haute disponibilité. Vous ne pouvez pas lancer le basculement | [Distribution mondiale clés en main](../documentdb/documentdb-distribute-data-globally.md) de 1 à plus de 30 régions, prise en charge des [basculements automatiques et manuels](../documentdb/documentdb-regional-failovers.md) à tout moment, partout dans le monde |
| Indexation | Index primaire uniquement sur PartitionKey et RowKey. Pas d’index secondaire | Indexation automatique et complète de toutes les propriétés, aucune gestion des index |
| Requête | L’exécution des requêtes utilise un index de clé primaire, et effectue une recherche dans le cas contraire. | Les requêtes peuvent tirer parti de l’indexation automatique de propriétés pour des temps de requête rapides. Le moteur de base de données d’Azure Cosmos DB est capable de prendre en charge les agrégats, les données géospatiales et le tri. |
| Cohérence | Robuste au sein de la région principale, avec une région secondaire en option | [cinq niveaux de cohérence bien définis](../documentdb/documentdb-consistency-levels.md) pour compenser la disponibilité, la latence, le débit ou la cohérence en fonction des besoins de votre application |
| Tarification | Optimisé pour le stockage  | Optimisé pour le débit |
| Contrats SLA | Disponibilité à 99,9 % | Disponibilité de 99,99 % au sein d’une seule région, avec la possibilité d’ajouter d’autres régions pour accroître la disponibilité. [Contrats SLA complets à la pointe du secteur](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) sur la disponibilité générale |

## <a name="how-to-get-started"></a>Pour commencer

Créez un compte Azure Cosmos DB dans le [portail Azure](https://portal.azure.com) et commencez par notre [Démarrage rapide pour l’API de table avec .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Étapes suivantes

Voici quelques conseils pour vous aider à démarrer :
* Commencez avec [l’API de table Azure Cosmos DB](create-table-dotnet.md) à l’aide du SDK de table .NET existant.
* En savoir plus sur la [distribution globale avec Azure Cosmos DB](../documentdb/documentdb-distribute-data-globally.md).
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](../documentdb/documentdb-request-units.md).
