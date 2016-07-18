<properties
   pageTitle="Choix de conception et techniques de codage pour le développement avec SQL Data Warehouse | Microsoft Azure"
   description="Concepts de développement, choix de conception, recommandations et des techniques de codage pour SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Choix de conception et techniques de codage pour SQL Data Warehouse

Consultez les articles sur le développement afin de mieux comprendre les choix de conception, les recommandations et les techniques de codage fondamentaux pour SQL Data Warehouse.

## Choix de conception clés
Les articles suivants offrent un aperçu de quelques-uns des concepts principaux et des décisions de conception que vous devrez maîtriser pour le développement de votre entrepôt de données distribué à l’aide de SQL Data Warehouse :

- [connexions][]
- [accès concurrentiel][]
- [transactions][]
- [schémas définis par l’utilisateur][]
- [Distribution de tables][]
- [Index de table][]
- [partitions de table][]
- [CTAS][]
- [statistiques][]

## Recommandations pour le développement et techniques de codage
Ces articles mettent l’accent sur des techniques de codage spécifiques, des conseils et des recommandations pour le développement de votre SQL Data Warehouse :

- [procédures stockées][]
- [étiquettes][]
- [vues][]
- [tables temporaires][]
- [SQL dynamique][]
- [bouclage][]
- [regrouper par options][]
- [attribution de variables][]

## Étapes suivantes
Une fois que vous avez consulté les articles sur le développement, accédez à la page des [informations de référence sur Transact-SQL][] pour en savoir plus sur la syntaxe prise en charge pour SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[accès concurrentiel]: ./sql-data-warehouse-develop-concurrency.md
[connexions]: ./sql-data-warehouse-develop-connections.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL dynamique]: ./sql-data-warehouse-develop-dynamic-sql.md
[regrouper par options]: ./sql-data-warehouse-develop-group-by-options.md
[étiquettes]: ./sql-data-warehouse-develop-label.md
[bouclage]: ./sql-data-warehouse-develop-loops.md
[statistiques]: ./sql-data-warehouse-tables-statistics.md
[procédures stockées]: ./sql-data-warehouse-develop-stored-procedures.md
[Distribution de tables]: ./sql-data-warehouse-tables-distribute.md
[Index de table]: ./sql-data-warehouse-tables-index.md
[partitions de table]: ./sql-data-warehouse-tables-partition.md
[tables temporaires]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[schémas définis par l’utilisateur]: ./sql-data-warehouse-develop-user-defined-schemas.md
[attribution de variables]: ./sql-data-warehouse-develop-variable-assignment.md
[vues]: ./sql-data-warehouse-develop-views.md
[informations de référence sur Transact-SQL]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0706_2016-->