---
title: "Migration de votre schéma vers SQL Data Warehouse | Microsoft Docs"
description: "Conseils relatifs à la migration de votre schéma vers Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1ac4db6d9404567be0d3d12f91a96fa4e31ef3bf


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Migration de votre schéma vers SQL Data Warehouse
Les résumés suivants vous permettent de comprendre les différences entre SQL Server et SQL Data Warehouse afin de faciliter la migration de votre base de données.

## <a name="table-migration"></a>Migration de table
Lorsque vous migrez vos tables, vous souhaitez connaître les fonctionnalités de table des tables SQL Data Warehouse.  La [vue d’ensemble des tables][vue d’ensemble des tables] est un excellent point de départ.  Cet article vous présente les éléments les plus importants à prendre en compte lors de la création d’une table, tels que les statistiques, la distribution, le partitionnement et l’indexation des tables.  Il aborde également certaines [fonctionnalités de table non prises en charge][fonctionnalités de table non prises en charge] et des solutions de contournement.

SQL Data Warehouse prend en charge les types de données métiers courants.  Consultez l’article sur les [types de données][types de données] pour obtenir la liste des [des types de données non pris en charge][des types de données non pris en charge] et pris en charge.  L’article sur les [types de données][types de données] contient également une requête permettant d’identifier les [des types de données non pris en charge][des types de données non pris en charge].  Lors de la conversion de vos types de données, veillez à consulter les [meilleures pratiques relatives aux types de données][meilleures pratiques relatives aux types de données].

## <a name="next-steps"></a>Étapes suivantes
Après avoir avez correctement migré votre schéma de base de données vers SQL Data Warehouse, passez à l’un des articles suivants :

* [Migration de vos données][Migration de vos données]
* [Migration de votre code][Migration de votre code]

Pour en savoir plus sur les meilleures pratiques relatives à SQL Data Warehouse, consultez l’article sur les [meilleures pratiques][meilleures pratiques].

<!--Image references-->

<!--Article references-->
[Migration de votre code]: ./sql-data-warehouse-migrate-code.md
[Migration de vos données]: ./sql-data-warehouse-migrate-data.md
[meilleures pratiques]: ./sql-data-warehouse-best-practices.md
[vue d’ensemble des tables]: ./sql-data-warehouse-tables-overview.md
[fonctionnalités de table non prises en charge]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[types de données]: ./sql-data-warehouse-tables-data-types.md
[des types de données non pris en charge]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[meilleures pratiques relatives aux types de données]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


