<properties
   pageTitle="Migration de votre solution vers SQL Data Warehouse | Microsoft Azure"
   description="Recommandations relatives à la migration de votre solution vers la plateforme Microsoft Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Migration de votre solution vers SQL Data Warehouse

SQL Data Warehouse est un système de base de données distribuée qui se met à l’échelle de manière flexible afin de répondre à vos besoins. Pour maintenir des niveaux appropriés de performances et de mise à l’échelle, certaines fonctionnalités de SQL Server ne sont pas implémentées dans SQL Data Warehouse. Les rubriques suivantes sur la migration évoquent certains des facteurs clés relatifs à la migration de votre solution vers SQL Data Warehouse. Le développement d’entrepôts de données prenant en charge la mise à l’échelle introduisant différents modèles de conception, les approches traditionnelles ne sont pas toujours les plus indiquées. Vous pouvez donc constater qu’adapter votre solution existante garantit la possibilité de tirer pleinement parti de la plateforme distribuée fournie par SQL Data Warehouse.

Il est également important de se rappeler de la nature de SQL Data Warehouse, qui est une plateforme basée sur Microsoft Azure. Par conséquent, il est possible qu’une portion de votre migration implique le transfert vers le Cloud de vos données. Le transfert de données est une problématique à part entière, qui doit être étudiée avec soin, plus particulièrement lorsque les volumes augmentent. Le transfert et le chargement des données sont des rubriques distinctes.

## Recommandations en matière de migration

Avant d’amorcer votre migration, prenez le temps de lire ces articles afin de mieux comprendre certains concepts fondamentaux et différences en relation avec les produits.

- [Migration de votre schéma][]
- [Migration de vos données][]
- [Migration de votre code][]

## Étapes suivantes

L’équipe de conseils à la clientèle (CAT, Customer Advisory Team) peut également fournir quelques recommandations sur SQL Data Warehouse, qu’elle publie en tant que billets de blog. Consultez son article [Migrating data to Azure SQL Data Warehouse in practice][] \(Migration des données vers Azure SQL Data Warehouse dans la pratique) pour obtenir des conseils supplémentaires sur la migration.

<!--Image references-->

<!--Article references-->
[Migration de votre schéma]: sql-data-warehouse-migrate-schema.md
[Migration de vos données]: sql-data-warehouse-migrate-data.md
[Migration de votre code]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

<!---HONumber=AcomDC_0831_2016-->