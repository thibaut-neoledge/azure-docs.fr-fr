<properties
   pageTitle="Restauration d’un Azure SQL Data Warehouse (Vue d’ensemble) | Microsoft Azure"
   description="Vue d’ensemble des options de restauration de base de données pour la récupération d’une base de données dans Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/28/2016"
   ms.author="lakshmir;barbkess;sonyama"/>


# Restauration d’un Azure SQL Data Warehouse (Vue d’ensemble)

> [AZURE.SELECTOR]
- [Vue d'ensemble][]
- [Portail][]
- [PowerShell][]
- [REST][]

Azure SQL Data Warehouse protège vos données avec un stockage localement redondant et des sauvegardes automatisées. Les sauvegardes automatisées ne requièrent aucun coût d’administration pour protéger vos bases de données d’une altération ou d’une suppression accidentelle. Si un utilisateur modifie ou supprime accidentellement des données, vous pouvez garantir la continuité des activités en restaurant votre base de données à un point antérieur dans le temps. SQL Data Warehouse utilise des instantanés Azure Storage Snapshots pour sauvegarder votre base de données façon transparente sans avoir besoin d’une interruption de service.

## Sauvegardes automatisées

Vos bases de données **actives** sont automatiquement sauvegardées au moins toutes les 8 heures et conservées pendant 7 jours. Cela vous permet de restaurer votre base de données active à une de plusieurs points de restauration au cours des 7 derniers jours.

Lorsqu’une base de données est suspendue, aucune nouvelle sauvegarde n’est créée et les sauvegardes précédentes sont supprimées après 7 jours. Si une base de données est interrompue pendant plus de 7 jours, la sauvegarde la plus récente est enregistrée pour garantir que vous disposez toujours d’au moins une sauvegarde.

Lorsqu’une base de données est supprimée, la dernière sauvegarde est enregistrée pendant 7 jours.

Exécutez cette requête sur votre SQL Data Warehouse actif pour voir quand la dernière sauvegarde a été effectuée :

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Si vous devez conserver une sauvegarde pendant plus de 7 jours, vous pouvez tout simplement restaurer l’un de vos points de restauration comme nouvelle base de données et suspendre éventuellement cette base de données pour ne payer que pour l’espace de stockage de cette sauvegarde.

## Redondance des données

En plus des sauvegardes, SQL Data Warehouse protège également vos données avec un stockage Azure Premium [localement redondant][]. Plusieurs copies synchrones des données sont conservées dans le centre de données local afin de garantir la protection transparente des données en cas de défaillance localisée. La redondance des données assure que vos données peuvent survivre à des problèmes liés à l’infrastructure tels que des défaillances de disque, etc. La redondance des données assure la continuité des activités avec une infrastructure hautement disponible tolérante aux pannes.

## Restauration d’une base de données

La restauration d’un SQL Data Warehouse est une opération simple qui peut être effectuée dans le portail Azure ou automatisée à l’aide de PowerShell ou d’API REST.


## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, voir la [vue d’ensemble de la continuité des activités de la base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble de la continuité des activités de la base de données SQL Azure]: ./sql-database-business-continuity.md
[localement redondant]: ../storage/storage-redundancy.md
[Vue d'ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0824_2016-->