---
title: "Introduction à l’API de table d’Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment vous pouvez utiliser Azure Cosmos DB pour stocker et interroger d’immenses volumes de données de données de valeur de clé avec une faible latence, avec les API MongoDB OSS populaires."
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/09/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ef57753aeeace0086c815d83600f92422996032a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Présentation d’Azure Cosmos DB : API de table

[Azure Cosmos DB](introduction.md) est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale pour les applications stratégiques. Azure Cosmos DB fournit la [distribution mondiale clés en main](distribute-data-globally.md), la [mise à l’échelle élastique du débit et du stockage](partition-data.md), des latences de l’ordre de quelques millisecondes dans le monde entier dans plus de 99 pour cent des cas, [cinq niveaux de cohérence bien définis](consistency-levels.md) et une garantie d’une haute disponibilité, le tout soutenu par nos [contrats SLA de pointe](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexe automatiquement les données](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sans avoir à s’occuper de la gestion des schémas et des index. Il est multi-modèle et prend en charge les modèles de données en colonnes, graphiques et clé-valeur. 

![API de stockage de table Azure et Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

Azure Cosmos DB fournit l’API Table (version préliminaire) pour les applications qui ont besoin d’un magasin de paires clé-valeur avec un schéma flexible, des performances prévisibles, une distribution globale et un débit élevé. L’API Table fournit les mêmes fonctionnalités que le stockage Table Azure, mais il tire parti des avantages du moteur Azure Cosmos DB. 

Vous pouvez continuer à utiliser le stockage Table Azure pour les tables présentant des exigences de débit et de stockage inférieures. Azure Cosmos DB introduira la prise en charge des tables optimisées pour le stockage dans une prochaine mise à jour, et les comptes de stockage de table Azure nouveaux ou existants seront mis à niveau vers Azure Cosmos DB.

## <a name="premium-and-standard-table-apis"></a>API de table standard et Premium
Si vous utilisez actuellement le stockage de table Azure, vous bénéficiez des avantages suivants en les déplaçant vers la version préliminaire de « table premium » d’Azure Cosmos DB :

|  | Stockage de table Azure | Azure Cosmos DB : stockage de table (version préliminaire) |
| --- | --- | --- |
| Latence | Rapide, mais aucune limite supérieure sur la latence | Une latence de quelques millisecondes pour les lectures et écritures, soutenue par des lectures à < 10 ms de latence et des écritures à < 15 ms de latence dans plus de 99 pour cent des cas, à n’importe quelle échelle, n’importe où dans le monde |
| Débit | Modèle de débit hautement évolutif, mais non dédié. Les tables ont une limite d’évolutivité de 20 000 opérations/s | Hautement évolutif avec un [débit dédié réservé par table](request-units.md), qui est soutenu par des contrats SLA. Les comptes n’ont aucune limite supérieure sur le débit, et prennent en charge > 10 millions d’opérations/s par table |
| Diffusion mondiale | Une région unique avec une région de lecture secondaire en option pour la haute disponibilité. Vous ne pouvez pas lancer le basculement | [Distribution mondiale clés en main](distribute-data-globally.md) de 1 à plus de 30 régions, prise en charge des [basculements automatiques et manuels](regional-failover.md) à tout moment, partout dans le monde |
| Indexation | Index primaire uniquement sur PartitionKey et RowKey. Pas d’index secondaire | Indexation automatique et complète de toutes les propriétés, aucune gestion des index |
| Requête | L’exécution des requêtes utilise un index de clé primaire, et effectue une recherche dans le cas contraire. | Les requêtes peuvent tirer parti de l’indexation automatique de propriétés pour des temps de requête rapides. Le moteur de base de données d’Azure Cosmos DB est capable de prendre en charge les agrégats, les données géospatiales et le tri. |
| Cohérence | Robuste au sein de la région principale, avec une région secondaire en option | [Cinq niveaux de cohérence bien définis](consistency-levels.md) pour compenser la disponibilité, la latence, le débit ou la cohérence en fonction des besoins de votre application |
| Tarification | Optimisé pour le stockage  | Optimisé pour le débit |
| Contrats SLA | Disponibilité à 99,9 % | Disponibilité de 99,99 % au sein d’une seule région, avec la possibilité d’ajouter d’autres régions pour accroître la disponibilité. [Contrats SLA complets à la pointe du secteur](https://azure.microsoft.com/support/legal/sla/cosmos-db/) sur la disponibilité générale |

## <a name="how-to-get-started"></a>Pour commencer

Créez un compte Azure Cosmos DB dans le [portail Azure](https://portal.azure.com) et commencez par notre [Démarrage rapide pour l’API de table avec .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Étapes suivantes

Voici quelques conseils pour vous aider à démarrer :
* Commencez avec [l’API de table Azure Cosmos DB](create-table-dotnet.md) à l’aide du SDK de table .NET existant.
* En savoir plus sur la [distribution globale avec Azure Cosmos DB](distribute-data-globally.md).
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md).

