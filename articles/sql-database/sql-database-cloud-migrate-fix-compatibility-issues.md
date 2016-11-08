---
title: Résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers une base de données SQL | Microsoft Docs
description: Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab

---
# Utilisation de l’Assistant Migration SQL Azure pour résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers une base de données SQL Azure
> [!div class="op_single_selector"]
> * Utilisez l’[Assistant Migration SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * Utilisez [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * Utilisez [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> 
> 

Dans cet article, vous apprendrez à détecter et résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de l’Assistant Migration SQL Azure avant la migration vers une base de données SQL Azure.

## Utilisez l’assistant de migration SQL Azure
Utilisez l’outil CodePlex de l’[Assistant Migration de base de données SQL Microsoft Azure](http://sqlazuremw.codeplex.com/) pour générer un script T-SQL à partir d’une base de données source incompatible. Ce script est ensuite transformé par l’Assistant pour le rendre compatible avec Base de données SQL. Enfin, vous vous connectez à Base de données SQL Azure pour exécuter le script. Cet outil analyse également les fichiers de trace afin de déterminer les problèmes de compatibilité. Ce script peut être généré avec un schéma uniquement, ou peut inclure des données au format BCP. Pour obtenir une documentation supplémentaire, y compris des instructions pas à pas, consultez la rubrique [Assistant de migration SQL Azure](http://sqlazuremw.codeplex.com/) sur le site Codeplex.

 ![Schéma de migration SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

> [!NOTE]
> Certains schémas non compatibles détectés par l’Assistant ne peuvent pas être corrigés par ses transformations intégrées. Un script non compatible qui ne peut pas être traité est signalé comme une erreur, et des commentaires sont insérés dans le script généré. Si de nombreuses erreurs sont détectées, utilisez Visual Studio ou SQL Server Management Studio pour examiner et résoudre chaque erreur que l’Assistant Migration SQL Server ne permet pas de corriger.
> 
> 

## Étapes suivantes
* [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Migrez une base de données SQL Server compatible vers une base de données SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Ressources supplémentaires
* [Base de données SQL V12](sql-database-v12-whats-new.md)
* [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
* [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->