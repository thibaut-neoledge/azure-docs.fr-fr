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
ms.date: 08/29/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3ccc3b176df2f0a5d864554a74508292d272bd5a
ms.contentlocale: fr-fr
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Présentation d’Azure Cosmos DB : API de table

[Azure Cosmos DB](introduction.md) fournit l’API de table (version préliminaire) pour des applications écrites pour le service de stockage de table Azure et nécessite des fonctionnalités premium telles que la [distribution globale clés en main](distribute-data-globally.md), un [débit dédié](partition-data.md), des latences inférieures à 10 millisecondes dans 99 pour cent des cas partout dans le monde, une haute disponibilité garantie et un [indexage secondaire automatique](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Ces applications peuvent migrer vers Azure Cosmos DB à l’aide de l’API de table sans aucune modification de code, jouissent des fonctionnalités premium.

Nous vous recommandons de commencer par visionner la vidéo suivante, dans laquelle Aravind Ramachandran explique comment prendre en main l’API de table pour Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>API de table standard et Premium
Si vous utilisez actuellement le stockage de table Azure, vous bénéficiez des avantages suivants en les déplaçant vers la version préliminaire de « table premium » d’Azure Cosmos DB :

|  | Stockage de table Azure | Azure Cosmos DB : stockage de table (version préliminaire) |
| --- | --- | --- |
| Latence | Rapide, mais aucune limite supérieure sur la latence | Une latence de quelques millisecondes pour les lectures et écritures, soutenue par des lectures à < 10 ms de latence et des écritures à < 15 ms de latence dans plus de 99 pour cent des cas, à n’importe quelle échelle, n’importe où dans le monde |
| Débit | Modèle de débit variable. Les tables ont une limite d’évolutivité de 20 000 opérations/s | Hautement évolutif avec un [débit dédié réservé par table](request-units.md), qui est soutenu par des contrats SLA. Les comptes n’ont aucune limite supérieure sur le débit, et prennent en charge > 10 millions d’opérations/s par table |
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
* [Créer une application .NET à l’aide de l’API de table](create-table-dotnet.md)
* [Développer avec l’API de table dans .NET](tutorial-develop-table-dotnet.md)
* [Interroger des données de table avec l’API de table](tutorial-query-table.md)
* [Comment configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API de table](tutorial-global-distribution-table.md)
* [Kit de développement logiciel (SDK) de l’API de table d’Azure Cosmos DB pour .NET](table-sdk-dotnet.md)

