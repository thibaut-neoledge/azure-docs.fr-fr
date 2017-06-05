---
title: "Migration de votre schéma vers SQL Data Warehouse | Microsoft Docs"
description: "Conseils relatifs à la migration de votre schéma vers Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0630f43642a0be98c470032d32b74ca14ee144e5
ms.contentlocale: fr-fr
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Migration de votre schéma vers SQL Data Warehouse
Les résumés suivants vous permettent de comprendre les différences entre SQL Server et SQL Data Warehouse afin de faciliter la migration de votre base de données.

## <a name="table-migration"></a>Migration de table
Lorsque vous migrez vos tables, vous souhaitez connaître les fonctionnalités de table des tables SQL Data Warehouse.  La [vue d’ensemble des tables][table overview] est un excellent point de départ.  Cet article vous présente les éléments les plus importants à prendre en compte lors de la création d’une table, tels que les statistiques, la distribution, le partitionnement et l’indexation des tables.  Il aborde également certaines [fonctionnalités de table non prises en charge][unsupported table features] et des solutions de contournement.

SQL Data Warehouse prend en charge les types de données métiers courants.  Consultez l’article sur les [types de données][data types] pour obtenir la liste des [types de données non pris en charge][unsupported data types] et pris en charge.  L’article sur les [types de données][data types] contient également une requête permettant d’identifier les [types de données non pris en charge][unsupported data types].  Lors de la conversion de vos types de données, veillez à consulter les [bonnes pratiques relatives aux types de données][data type best practices].

## <a name="next-steps"></a>Étapes suivantes
Après avoir avez correctement migré votre schéma de base de données vers SQL Data Warehouse, passez à l’un des articles suivants :

* [Migration de vos données][Migrate your data]
* [Migration de votre code][Migrate your code]

Pour en savoir plus sur les bonnes pratiques relatives à SQL Data Warehouse, consultez l’article sur les [bonnes pratiques][best practices].

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[data type best practices]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

