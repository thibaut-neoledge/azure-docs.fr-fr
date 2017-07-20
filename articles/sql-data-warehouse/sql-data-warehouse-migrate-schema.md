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
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 07ca2321852e276502187e768177e7e82bdfd080
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017


---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migration de votre schéma vers SQL Data Warehouse
Conseils pour la migration de vos schémas SQL vers SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Planification de la migration du schéma

Lorsque vous planifiez une migration, consultez la [vue d’ensemble des tables][table overview] pour vous familiariser avec les considérations relatives à la conception des tables tels que les statistiques, la distribution, le partitionnement et l’indexation.  Elle dresse également la liste de certaines [fonctionnalités de table non prises en charge][unsupported table features] et leurs solutions de contournement.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Utilisation des schémas définis par l’utilisateur pour consolider la base de données

Votre charge de travail existante a probablement plusieurs bases de données. Par exemple, un entrepôt de données SQL Server peut inclure une base de données de la zone de transit, une base de données de l’entrepôt de données et quelques bases de données de mini-Data Warehouse. Dans cette topologie, chaque base de données s’exécute comme une charge de travail distincte avec des stratégies de sécurité distinctes.

À l’inverse, SQL Data Warehouse exécute l’intégralité de la charge de travail des entrepôts de données au sein d’une seule et même base de données. Les jointures entre plusieurs bases de données ne sont pas autorisées. Par conséquent, SQL Data Warehouse s’attend que l’ensemble des tables utilisées par l’entrepôt de données soient stockées au sein d’une seule et même base de données.

Nous vous recommandons d’utiliser des schémas définis par l’utilisateur pour consolider votre charge de travail existante dans une seule base de données. Pour obtenir des exemples, consultez [Schémas définis par l’utilisateur](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Utilisation des types de données compatibles
Modifiez vos types de données pour être compatible avec SQL Data Warehouse. Consultez l’article sur les [types de données][data types] pour obtenir la liste des types de données non pris en charge et pris en charge. Cette rubrique fournit des solutions de contournement pour les types non pris en charge. Il fournit également une requête pour identifier les types existants qui ne sont pas pris en charge par SQL Data Warehouse.

## <a name="minimize-row-size"></a>Réduction de la taille de ligne
Pour de meilleures performances, réduisez la longueur de ligne de vos tables. Étant donné qu’une longueur de ligne plus courte permet de meilleures performances, utilisez les types de données les plus courts. 

Pour la largeur de ligne de table, PolyBase a une limite de 1 Mo.  Si vous envisagez de charger des données sur SQL Data Warehouse avec PolyBase, mettez à jour vos tables pour vous assurer que la largeur de ligne maximale ne dépasse pas 1 Mo. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>Spécification de l’option de distribution
SQL Data Warehouse est un système de base de données distribuées. Chaque table est distribuée ou répliquée sur les nœuds de calcul. Il existe une option de table qui vous permet de spécifier comment distribuer les données. Les options disponibles sont : tourniquet (Round Robin), répliquée, ou hachage distribué. Chaque option a ses avantages et inconvénients. Si vous ne spécifiez pas l’option de distribution, SQL Data Warehouse utilisera le tourniquet en tant que la valeur par défaut.

- Tourniquet est la valeur par défaut. Il est le plus simple à utiliser, et charge les données aussi rapidement que possible, mais les jointures nécessiteront le déplacement de données, causant un ralentissement des performances des requêtes.
- Les répliquées enregistrent une copie de la table dans chaque nœud de calcul. Les tables répliquées sont performantes, car elles ne nécessitent pas le déplacement des données pour les jointures et les agrégations. Elles requièrent cependant un stockage supplémentaire et sont par conséquent mieux adaptées à des tables plus petites.
- Le hachage distribué distribue les lignes sur tous les nœuds via une fonction de hachage. Les tables de hachage distribué sont au cœur de SQL Data Warehouse car elles sont conçues pour fournir d’excellentes performances de requête sur des tables volumineuses. Cette option nécessite une bonne planification afin de sélectionner la meilleure colonne sur laquelle distribuer les données. Toutefois, si vous ne choisissez pas la meilleure colonne la première fois, vous pouvez facilement redistribuer les données sur une autre colonne. 

Pour choisir la meilleure option de distribution pour chaque table, consultez [Tables distribuées](sql-data-warehouse-tables-distribute.md).


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

<!--MSDN references-->


<!--Other Web references-->

