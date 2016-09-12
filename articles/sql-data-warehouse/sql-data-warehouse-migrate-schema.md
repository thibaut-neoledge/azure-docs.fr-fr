<properties
   pageTitle="Migration de votre schéma vers SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à la migration de votre schéma vers Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Migration de votre schéma vers SQL Data Warehouse#

Les résumés suivants vous permettent de comprendre les différences entre SQL Server et SQL Data Warehouse afin de faciliter la migration de votre base de données.

## Migration de table

Lorsque vous migrez vos tables, vous souhaitez connaître les fonctionnalités de table des tables SQL Data Warehouse. La [vue d’ensemble des tables][] est un excellent point de départ. Cet article vous présente les éléments les plus importants à prendre en compte lors de la création d’une table, tels que les statistiques, la distribution, le partitionnement et l’indexation des tables. Il aborde également certaines [fonctionnalités de table non prises en charge][] et des solutions de contournement.

SQL Data Warehouse prend en charge les types de données métiers courants. Consultez l’article sur les [types de données][] pour obtenir la liste [des types de données non pris en charge][] et pris en charge. L’article sur les [types de données][] contient également une requête pour identifier les [types de données non pris en charge][]. Lors de la conversion de vos types de données, veillez à consulter les [meilleures pratiques relatives aux types de données][].

## Étapes suivantes
Après avoir avez correctement migré votre schéma de base de données vers SQL Data Warehouse, passez à l’un des articles suivants :

- [Migration de vos données][]
- [Migration de votre code][]

Pour en savoir plus sur les meilleures pratiques relatives à SQL Data Warehouse, consultez l’article sur les [meilleures pratiques][].

<!--Image references-->

<!--Article references-->
[Migration de votre code]: ./sql-data-warehouse-migrate-code.md
[Migration de vos données]: ./sql-data-warehouse-migrate-data.md
[meilleures pratiques]: ./sql-data-warehouse-best-practices.md
[vue d’ensemble des tables]: ./sql-data-warehouse-tables-overview.md
[fonctionnalités de table non prises en charge]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[types de données]: ./sql-data-warehouse-tables-data-types.md
[des types de données non pris en charge]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[types de données non pris en charge]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[meilleures pratiques relatives aux types de données]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0831_2016-->