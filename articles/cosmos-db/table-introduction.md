---
title: "Introduction à l’API de table d’Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment vous pouvez utiliser Azure Cosmos DB pour stocker et interroger d’immenses volumes de données clé-valeur avec une faible latence, avec les API MongoDB OSS populaires."
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
ms.sourcegitcommit: f2ac16c2f514aaa7e3f90fdf0d0b6d2912ef8485
ms.openlocfilehash: 2073948d44ccc4b9b83e4eaf4f250dc272e46292
ms.contentlocale: fr-fr
ms.lasthandoff: 09/08/2017

---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Présentation d’Azure Cosmos DB : API de table

[Azure Cosmos DB](introduction.md) fournit l’API de table (préversion) aux applications qui sont écrites pour le stockage de table Azure et qui ont besoin de fonctionnalités Premium comme :

* [Une distribution mondiale clé en main](distribute-data-globally.md).
* [Un débit dédié](partition-data.md) partout dans le monde.
* Des latences de quelques millisecondes au 99e centile.
* Une haute disponibilité garantie.
* [Une indexation secondaire automatique](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

Ces applications peuvent migrer vers Azure Cosmos DB à l’aide de l’API de table sans aucune modification de code, et tirer parti des fonctionnalités Premium.

Nous vous recommandons de visionner la vidéo suivante, dans laquelle Aravind Ramachandran explique comment prendre en main l’API de table pour Azure Cosmos DB :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>API de table standard et Premium
Si vous utilisez actuellement le stockage de table, vous bénéficiez des avantages suivants en passant à la préversion de « table Premium » d’Azure Cosmos DB :

| | Stockage de tables Azure | Azure Cosmos DB : stockage de table (version préliminaire) |
| --- | --- | --- |
| Latence | Rapide, mais aucune limite supérieure sur la latence. | Une latence de quelques millisecondes pour les lectures et écritures, appuyée par des lectures avec une latence de < 10 ms et des écritures avec une latence de < 15 ms au 99e centile, à n’importe quelle échelle, n’importe où dans le monde. |
| Débit | Modèle de débit variable. Les tables ont une limite d’évolutivité de 20 000 opérations/s. | Hautement évolutif avec un [débit dédié réservé par table](request-units.md), qui est appuyé par des contrats de niveau de service. Les comptes n’ont aucune limite supérieure sur le débit, et prennent en charge > 10 millions d’opérations/s par table. |
| Diffusion mondiale | Une région unique avec une région de lecture secondaire en option pour la haute disponibilité. Vous ne pouvez pas lancer le basculement. | [Une distribution mondiale clé en main](distribute-data-globally.md) de 1 à plus de 30 régions. Prise en charge des [basculements automatiques et manuels](regional-failover.md) à tout moment, partout dans le monde. |
| Indexation | Index primaire uniquement sur PartitionKey et RowKey. Pas d’index secondaire. | Indexation automatique et complète de toutes les propriétés, aucune gestion des index. |
| Interroger | L’exécution des requêtes utilise un index de clé primaire, et effectue une recherche dans le cas contraire. | Les requêtes peuvent tirer parti de l’indexation automatique de propriétés pour des temps de requête rapides. Le moteur de base de données d’Azure Cosmos DB est capable de prendre en charge les agrégats, les données géospatiales et le tri. |
| Cohérence | Forte au sein de la région primaire. Éventuelle au sein de la région secondaire. | [Cinq niveaux de cohérence bien définis](consistency-levels.md) pour compenser la disponibilité, la latence, le débit ou la cohérence en fonction des besoins de votre application. |
| Tarification | Optimisation pour le stockage. | Optimisation pour le débit. |
| Contrats SLA | Disponibilité de 99,99 %. | Disponibilité de 99,99 % au sein d’une seule région, avec la possibilité d’ajouter d’autres régions pour accroître la disponibilité. [Contrats de niveau de service complets à la pointe du secteur](https://azure.microsoft.com/support/legal/sla/cosmos-db/) sur la disponibilité générale. |

## <a name="get-started"></a>Prise en main

Créez un compte Azure Cosmos DB dans le [portail Azure](https://portal.azure.com). Continuez avec notre [démarrage rapide pour l’API de table à l’aide de .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Étapes suivantes

Voici quelques conseils pour vous aider à démarrer :
* [Azure Cosmos DB : Créer une application .NET à l’aide de l’API de table](create-table-dotnet.md)
* [Développer avec l’API de table dans .NET](tutorial-develop-table-dotnet.md)
* [Interroger des données de table avec l’API de table](tutorial-query-table.md)
* [Comment configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API de table](tutorial-global-distribution-table.md)
* [Kit de développement logiciel (SDK) de l’API de table d’Azure Cosmos DB pour .NET](table-sdk-dotnet.md)

