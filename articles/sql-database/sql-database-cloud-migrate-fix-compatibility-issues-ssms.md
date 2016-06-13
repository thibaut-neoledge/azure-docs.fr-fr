<properties
   pageTitle="Résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers Base de données SQL"
   description="Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/31/2016"
   ms.author="carlrab"/>

# Résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration vers Base de données SQL

> [AZURE.SELECTOR]
- Utilisez l’[Assistant Migration SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Utilisez [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Utilisez [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Les utilisateurs expérimentés peuvent résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration vers Base de données SQL Azure.

## Avec SQL Server Management Studio

Servez-vous de SQL Server Management Studio pour résoudre les problèmes de compatibilité à l’aide de différentes commandes Transact-SQL, telles que **ALTER DATABASE**. Cette méthode est principalement destinée aux utilisateurs expérimentés qui sont à l’aise avec utilisation de Transact-SQL sur une base de données active. Dans le cas contraire, il est recommandé d’utiliser SSDT.



## Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migrez une base de données SQL Server compatible vers une base de données SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## En savoir plus

- [Base de données SQL V12](sql-database-v12-whats-new.md)
- [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
- [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0601_2016-->