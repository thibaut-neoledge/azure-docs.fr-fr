---
title: "Surveiller le stockage en mémoire XTP | Microsoft Docs"
description: "Estimer et surveiller la capacité et l’utilisation du stockage en mémoire XTP ; résoudre l’erreur de capacité 41823"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jodebrui
ms.openlocfilehash: e953b60493c5a7c7a7ad74533471bd321d42abef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-in-memory-oltp-storage"></a>Surveiller le stockage OLTP In-Memory
Lorsque vous utilisez [OLTP en mémoire](sql-database-in-memory.md), les données des tables à mémoire optimisée et les variables de table résident dans un stockage OLTP en mémoire. Chaque niveau de service Premium est doté d’une taille de stockage OLTP en mémoire maximale, qui est décrite dans [Limites de ressources d’une base de données unique](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) et [Limites de ressources d’un pool élastique](sql-database-resource-limits.md#elastic-pool-change-storage-size). Une fois que cette limite est dépassée, des opérations d’insertion et de mise à jour peuvent commencer à échouer (en générant l’erreur 41823). À ce stade, vous devez soit supprimer des données pour libérer de la mémoire, soit mettre à niveau le niveau de performances de votre base de données.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Déterminer si la taille des données est adaptée à la capacité de stockage en mémoire
Déterminer les plafonds de stockage des différents niveaux de service Premium. Consultez [Limites de ressources d’une base de données unique](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) et [Limites de ressources d’un pool élastique](sql-database-resource-limits.md#elastic-pool-change-storage-size).

L’estimation de la mémoire requise pour une table à mémoire optimisée s’effectue de la même façon pour SQL Server que dans Base de données SQL Azure. Prenez quelques minutes pour consulter cette rubrique sur [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Notez que la table et les lignes de variable de table, ainsi que les index, sont pris en compte pour le calcul de la taille maximale des données utilisateur. En outre, l’instruction ALTER TABLE a besoin de suffisamment d’espace pour créer une version de la table entière et de ses index.

## <a name="monitoring-and-alerting"></a>Surveillance et alerte
Vous pouvez surveiller l’utilisation du stockage en mémoire sous forme de pourcentage de la limite maximale de stockage de votre niveau de performances dans le [portail Azure](https://portal.azure.com/) : 

1. Sur le panneau Base de données, recherchez la zone Utilisation de ressources, puis cliquez sur Modifier.
2. Sélectionnez la métrique `In-Memory OLTP Storage percentage`.
3. Pour ajouter une alerte, cliquez sur la zone Taux d’utilisation de la ressource pour ouvrir le panneau Métriques, puis cliquez sur Ajouter une alerte.

Vous pouvez également utiliser la requête suivante pour afficher l’utilisation du stockage en mémoire :

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Résoudre les situations de mémoire insuffisante - erreur 41823
Quand la mémoire devient insuffisante, les opérations INSERT, UPDATE et CREATE échouent en générant le message d’erreur 41823.

Le message d’erreur 41823 indique que les tables à mémoire optimisée et les variables de table ont dépassé la taille maximale.

Pour résoudre cette erreur, deux possibilités s’offrent à vous :

* supprimer des données des tables à mémoire optimisée, en déchargeant potentiellement les données vers des tables traditionnelles sur disque ;
* adapter le niveau de service afin de disposer d’un stockage en mémoire suffisant pour les données que vous devez conserver dans des tables à mémoire optimisée.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’instructions sur la surveillance, consultez [Analyse de base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md).
