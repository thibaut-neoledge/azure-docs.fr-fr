---
title: Créer ou déplacer une base de données SQL Azure dans un pool élastique à l’aide de T-SQL | Microsoft Docs
description: Utilisez T-SQL pour créer une base de données SQL Azure dans un pool élastique. Ou utilisez T-SQL pour déplacer la base de données dans et en dehors des pools.
services: sql-database
documentationcenter: ''
author: srinia
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia

---
# Surveiller et gérer un pool de base de données élastique avec Transact-SQL
> [!div class="op_single_selector"]
> * [Portail Azure](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

Utilisez les commandes [Create Database (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) et [Alter Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) pour créer et déplacer les bases de données dans et en dehors des pools élastiques. Le pool élastique doit exister avant de pouvoir utiliser ces commandes. Ces commandes affectent uniquement les bases de données. La création de nouveaux pools et le paramétrage des propriétés du pool (telles qu’eDTU min. et max.) ne peuvent pas être modifiés avec les commandes T-SQL.

> [!NOTE]
> Les pools élastiques sont mis à la disposition générale dans toutes les régions Azure, à l’exception du Nord du centre des États-Unis et de l’Inde de l’Ouest, où ils sont actuellement en préversion. Les pools élastiques seront mis à la disposition générale dans ces régions dès que possible. En outre, les pools élastiques ne prennent pas en charge les bases de données utilisant [l’OLTP ou l’analytique en mémoire](sql-database-in-memory.md).
> 
> 

## Créer une base de données dans un pool de base de données élastique
Utilisez la commande CREATE DATABASE avec l’option SERVICE\_OBJECTIVE.

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

Toutes les bases de données d’un pool élastique héritent du niveau de service du pool élastique (De base, Standard, Premium).

## Déplacer une base de données entre les pools élastiques
Utilisez la commande ALTER DATABASE avec MODIFY et définissez l’option SERVICE\_OBJECTIVE sur ELASTIC\_POOL ; définissez le nom sur le nom du pool cible.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## Déplacer une base de données dans un pool élastique
Utilisez la commande ALTER DATABASE avec MODIFY et définissez l’option SERVICE\_OBJECTIVE sur ELASTIC\_POOL ; définissez le nom sur le nom du pool cible.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## Déplacer une base de données hors d’un pool élastique
Utilisez la commande ALTER DATABASE et définir l’option SERVICE\_OBJECTIVE sur l’un des niveaux de performances (S0, S1, etc.).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## Répertorier les bases de données dans un pool élastique
Utilisez la vue [sys.database\_service \_objectives](https://msdn.microsoft.com/library/mt712619) pour répertorier toutes les bases de données dans un pool élastique. Connectez-vous à la base de données MASTER pour interroger la vue.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## Obtenir les données d’utilisation des ressources d’un pool
Utilisez la vue [sys.elastic\_pool \_resource \_stats](https://msdn.microsoft.com/library/mt280062.aspx) pour examiner les statistiques d’utilisation des ressources d’un pool élastique sur un serveur logique. Connectez-vous à la base de données MASTER pour interroger la vue.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## Obtenir les données d’utilisation des ressources d’une base de données élastique
Utilisez la vue [sys.dm\_ db\_ resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) ou [sys.resource \_stats](https://msdn.microsoft.com/library/dn269979.aspx) pour examiner les statistiques d’utilisation des ressources d’une base de données dans un pool élastique. Ce processus revient à interroger l’utilisation des ressources de toutes les bases de données uniques.

## Étapes suivantes
Après avoir créé un pool élastique de bases de données, vous pouvez gérer des bases de données élastiques du pool en créant des tâches élastiques. Les tâches élastiques facilitent l'exécution de scripts T-SQL, quel que soit le nombre de bases de données dans le pool. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).

Consultez [Vue d’ensemble des fonctionnalités de base de données élastique](sql-database-elastic-scale-introduction.md) : utilisez les outils de base de données élastique pour monter en charge, déplacer des données, exécuter des requêtes ou créer des transactions.

<!---HONumber=AcomDC_0907_2016-->