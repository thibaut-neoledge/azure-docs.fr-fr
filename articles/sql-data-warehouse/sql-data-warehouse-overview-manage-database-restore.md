<properties
   pageTitle="Restauration d’une base de données dans Azure SQL Data Warehouse (Vue d’ensemble) | Microsoft Azure"
   description="Vue d’ensemble des options de restauration de base de données pour la récupération d’une base de données dans Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/02/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Restauration d’une base de données dans Azure SQL Data Warehouse (Vue d’ensemble)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-overview-manage-database-restore.md)
- [Portail](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

Cet article décrit les options de restauration d’une base de données dans Azure SQL Data Warehouse. Il inclut la restauration d’un entrepôt de données actives et d’un entrepôt de données supprimées. Les entrepôts de données actives et supprimées sont restaurés à partir d’instantanés automatiques créés à partir de tous les entrepôts de données.

## Scénarios de récupération

**Récupération après des défaillances d’infrastructure :** ce scénario est relatif à la récupération après des défaillances d’infrastructure (défaillances de disques, etc.). Un client souhaite garantir la continuité des activités avec une infrastructure hautement disponible, à tolérance de pannes.

**Récupération après des erreurs d’utilisateurs :** ce scénario est relatif à la récupération après des corruptions ou suppressions accidentelles de données. Si un utilisateur modifie ou supprime accidentellement des données, un client souhaite garantir la disponibilité des activités en restaurant la base de données à un point antérieur dans le temps.

## Stratégies de capture d’instantanés

[AZURE.INCLUDE [Stratégie de rétention des sauvegardes SQL Data Warehouse](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Fonctionnalités de restauration de base de données

Penchons-nous sur la manière dont SQL Data Warehouse améliore la fiabilité de votre base de données et prend en charge les scénarios mentionnés plus haut récupération et de continuité des activités


### Redondance des données

SQL Data Warehouse stocke toutes les données sur un stockage Azure Premium Storage [localement redondant (LRS)](../storage/storage-redundancy.md) et conserve trois copies de vos données.

### Restauration de base de données

Cette fonctionnalité est utilisée pour restaurer votre base de données à un point antérieur dans le temps. Le service SQL Data Warehouse d’Azure protège toutes les bases de données avec des captures instantanées de stockage automatiques au moins toutes les 8 heures et les conserve pendant 7 jours afin de vous offrir un ensemble discret de points de restauration. Les fonctionnalités automatiques de capture d’instantanés et de restauration ne requièrent aucun coût d’administration pour protéger les bases de données d’une corruption ou d’une suppression accidentelle. Pour en savoir plus sur la restauration de base de données, consultez la section [Tâches de restauration de base de données][].

## Étapes suivantes
Pour d’autres tâches de gestion significatives, consultez la [Vue d’ensemble de la gestion][].

<!--Image references-->

<!--Article references-->
[Azure storage redundancy options]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[Vue d’ensemble de la gestion]: sql-data-warehouse-overview-management.md
[Tâches de restauration de base de données]: sql-data-warehouse-manage-database-restore-portal.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->