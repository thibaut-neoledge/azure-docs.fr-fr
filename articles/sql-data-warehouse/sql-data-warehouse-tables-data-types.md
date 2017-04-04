---
title: "Types de données pour tables dans SQL Data Warehouse | Microsoft Docs"
description: "Prise en main des types de données pour les tables Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 7757de96601c426ff07e94cfa0c12d4dec8f06f5
ms.lasthandoff: 03/22/2017


---
# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Types de données pour tables dans SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Vue d’ensemble][Overview]
> * [Types de données][Data Types]
> * [Distribuer][Distribute]
> * [Index][Index]
> * [Partition][Partition]
> * [Statistiques][Statistics]
> * [Temporaire][Temporary]
> 
> 

SQL Data Warehouse prend en charge les types de données les plus couramment utilisés.  Voici une liste des types de données pris en charge par SQL Data Warehouse.  Pour plus d’informations sur la prise en charge du type de données, consultez la page [create table][create table] (créer une table).

| **Types de données pris en charge** |  |  |
| --- | --- | --- |
| [bigint][bigint] |[decimal][decimal] |[smallint][smallint] |
| [binary][binary] |[float][float] |[smallmoney][smallmoney] |
| [bit][bit] |[int][int] |[sysname][sysname] |
| [char][char] |[money][money] |[time][time] |
| [date][date] |[nchar][nchar] |[tinyint][tinyint] |
| [datetime][datetime] |[nvarchar][nvarchar] |[uniqueidentifier][uniqueidentifier] |
| [datetime2][datetime2] |[real][real] |[varbinary][varbinary] |
| [datetimeoffset][datetimeoffset] |[smalldatetime][smalldatetime] |[varchar][varchar] |

## <a name="data-type-best-practices"></a>Meilleures pratiques relatives au type de données
 Lorsque vous définissez vos types de colonnes, l’utilisation du plus petit type de données prenant en charge vos données améliore les performances de requête. Ceci est particulièrement important pour les colonnes CHAR et VARCHAR. Si la valeur la plus longue dans une colonne est de 25 caractères, définissez la colonne en tant que VARCHAR(25). Évitez de définir toutes les colonnes de caractères sur une grande longueur par défaut. En outre, définissez des colonnes VARCHAR lorsque cela suffit, au lieu d’utiliser [NVARCHAR][NVARCHAR].  Utilisez NVARCHAR(4000) ou VARCHAR(8000) lorsque c’est possible au lieu de NVARCHAR(MAX) ou VARCHAR(MAX).

## <a name="polybase-limitation"></a>Limitation de Polybase
Si vous utilisez Polybase pour charger vos tables, faites en sorte que la longueur des données ne dépasse pas 1 Mo.  Vous pouvez définir des lignes incluant des données d’une longueur variable, susceptibles de dépasser cette largeur maximale, et charger des lignes avec BCP. Toutefois, vous ne pouvez pas utiliser PolyBase pour charger ces données.  

## <a name="unsupported-data-types"></a>Types de données non pris en charge
Si vous migrez votre base de données à partir d’une autre plateforme SQL, comme une base de données SQL Azure, au cours de la migration, vous pouvez rencontrer certains types de données qui ne sont pas pris en charge sur SQL Data Warehouse.  Voici les types de données non pris en charge, ainsi que certaines solutions que vous pouvez utiliser à la place des types de données non pris en charge.

| Type de données | Solution de contournement |
| --- | --- |
| [geometry][geometry] |[varbinary][varbinary] |
| [geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |Fractionnez la colonne en plusieurs colonnes fortement typées. |
| [table][table] |Appliquez une conversion vers des tables temporaires. |
| [timestamp][timestamp] |Modifiez le code afin d’utiliser le paramètre [datetime2][datetime2] et la fonction `CURRENT_TIMESTAMP`.  Seules les constantes sont prises en charge en tant que valeurs par défaut, par conséquent le paramètre current_timestamp ne peut pas être défini comme une contrainte par défaut. Si vous devez migrer les valeurs de version de ligne à partir d’une colonne de type horodatage, utilisez le paramètre [BINARY(8)][BINARY] ou [VARBINARY(8)][BINARY] pour les valeurs de version de ligne NOT NULL ou NULL. |
| [xml][xml] |[varchar][varchar] |
| [types définis par l’utilisateur][user defined types] |appliquez à nouveau les types natifs, le cas échéant |
| valeurs par défaut |les valeurs par défaut prennent uniquement en charge des littéraux et des constantes.  Les expressions ou fonctions non déterministes comme `GETDATE()` ou `CURRENT_TIMESTAMP` ne sont pas prises en charge. |

La requête SQL ci-dessous peut être exécutée sur votre base de données SQL actuelle pour identifier les colonnes qui ne sont pas prises en charge par Azure SQL Data Warehouse :

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les articles [Table Overview (Vue d’ensemble des tables)][Overview], [Distributing a Table (Distribution d’une table)][Distribute], [Indexing a Table (Indexation d’une table)][Index], [Partitioning a Table (Partitionnement d’une table)][Partition], [Maintaining Table Statistics (Maintenance des statistiques de table)][Statistics] et [Temporary Tables (Tables temporaires)][Temporary].  Pour en savoir plus sur les meilleures pratiques, consultez [Meilleures pratiques relatives à SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx

