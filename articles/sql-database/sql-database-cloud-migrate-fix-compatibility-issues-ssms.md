---
title: "Résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration vers SQL Database | Microsoft Docs"
description: "Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5f7d3544-b07e-415a-a2ae-96e49bf5d756
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 355353fb15a00860573699cc652543b61c62c2c1


---
# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>Résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration vers Base de données SQL
> [!div class="op_single_selector"]
> * Utilisez l’ [Assistant Migration SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * Utilisez [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * Utilisez [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> 
> 

Les utilisateurs expérimentés peuvent résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration vers Base de données SQL Azure.

> [!IMPORTANT]
> Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="using-sql-server-management-studio"></a>Avec SQL Server Management Studio
Servez-vous de SQL Server Management Studio pour résoudre les problèmes de compatibilité à l’aide de différentes commandes Transact-SQL, telles que **ALTER DATABASE**. Cette méthode est principalement destinée aux utilisateurs expérimentés qui sont à l’aise avec utilisation de Transact-SQL sur une base de données active. Dans le cas contraire, il est recommandé d’utiliser SSDT. 

## <a name="next-steps"></a>Étapes suivantes
* [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Migrez une base de données SQL Server compatible vers une base de données SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Fonctionnalités SQL Database](sql-database-features.md)
* [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
* [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


