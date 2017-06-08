---
title: "T-SQL : Gérer un pool élastique Azure SQL Database | Microsoft Docs"
description: "Utilisez T-SQL pour gérer un pool élastique Azure SQL Database."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 4e288e17-bc3e-4255-9fbe-0a2ac0dbd7dd
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
ms.translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: a3acd5f4ec63061254b550737ae9fb4d39b343c6
ms.contentlocale: fr-fr
ms.lasthandoff: 03/10/2017


---
# <a name="monitor-and-manage-an-elastic-pool-with-transact-sql"></a>Surveiller et gérer un pool élastique avec Transact-SQL
Cette rubrique vous montre comment créer des [pools élastiques](sql-database-elastic-pool.md) évolutifs avec Transact-SQL.  Vous pouvez également créer et gérer un pool élastique Azure avec le [Portail Azure](https://portal.azure.com/), [PowerShell](sql-database-elastic-pool-manage-powershell.md), l’API REST ou [C#](sql-database-elastic-pool-manage-csharp.md). Vous pouvez également créer et déplacer des bases de données vers et depuis des pools élastiques à l’aide de [Transact-SQL](sql-database-elastic-pool-manage-tsql.md).


Utilisez les commandes [Create Database (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) et [Alter Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) pour créer et déplacer les bases de données dans et en dehors des pools élastiques. Le pool élastique doit exister avant de pouvoir utiliser ces commandes. Ces commandes affectent uniquement les bases de données. La création de nouveaux pools et le paramétrage des propriétés du pool (telles qu’eDTU min. et max.) ne peuvent pas être modifiés avec les commandes T-SQL.

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>Créer une base de données regroupée dans un pool élastique
Utilisez la commande CREATE DATABASE avec l’option SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in an elastic named S3M100.

Toutes les bases de données d’un pool élastique héritent du niveau de service du pool élastique (De base, Standard, Premium). 

## <a name="move-a-database-between-elastic-pools"></a>Déplacer une base de données entre les pools élastiques
Utilisez la commande ALTER DATABASE avec l’option MODIFY puis définissez l’option SERVICE\_OBJECTIVE sur ELASTIC\_POOL. Définissez le nom sur le nom du pool cible.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to an elastic named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique
Utilisez la commande ALTER DATABASE avec l’option MODIFY puis définissez l’option SERVICE\_OBJECTIVE sur ELASTIC_POOL. Définissez le nom sur le nom du pool cible.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to an elastic named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Déplacer une base de données hors d’un pool élastique
Utilisez la commande ALTER DATABASE et définir l’option SERVICE_OBJECTIVE sur l’un des niveaux de performances (par exemple S0 ou S1).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Répertorier les bases de données dans un pool élastique
Utilisez la vue [sys.database\_service \_objectives](https://msdn.microsoft.com/library/mt712619) pour répertorier toutes les bases de données dans un pool élastique. Connectez-vous à la base de données MASTER pour interroger la vue.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>Obtenir les données d’utilisation des ressources d’un pool élastique
Utilisez la vue [sys.elastic\_pool \_resource \_stats](https://msdn.microsoft.com/library/mt280062.aspx) pour examiner les statistiques d’utilisation des ressources d’un pool élastique sur un serveur logique. Connectez-vous à la base de données MASTER pour interroger la vue.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-a-pooled-database"></a>Obtenir les données d’utilisation des ressources d’une base de données mise en pool
Utilisez la vue [sys.dm\_ db\_ resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) ou [sys.resource \_stats](https://msdn.microsoft.com/library/dn269979.aspx) pour examiner les statistiques d’utilisation des ressources d’une base de données dans un pool élastique. Ce processus revient à interroger l’utilisation des ressources pour une base de données unique.

## <a name="next-steps"></a>Étapes suivantes
Après avoir créé un pool élastique, vous pouvez gérer des bases de données élastiques du pool en créant des tâches élastiques. Les tâches élastiques facilitent l'exécution de scripts T-SQL, quel que soit le nombre de bases de données dans le pool. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md). 

Consultez [Montée en charge avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md): utilisez les outils de base de données élastique pour monter en charge, déplacer des données, exécuter des requêtes ou créer des transactions.


