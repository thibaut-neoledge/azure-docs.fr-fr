---
title: Sauvegardes de SQL Data Warehouse | Microsoft Docs
description: "Découvrez les sauvegardes de base de données intégrées de SQL Data Warehouse, qui vous permettent de restaurer un entrepôt de données SQL Azure à un point de restauration ou dans une autre région géographique."
services: sql-data-warehouse
documentationcenter: 
author: lakshmi1812
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 71f2798871c946b1edce467e1f491e0d62b342c6
ms.openlocfilehash: fb61dd8b33581740557be6e5902bbe573f11999c


---
# <a name="sql-data-warehouse-backups"></a>Sauvegardes de SQL Data Warehouse
SQL Data Warehouse propose des sauvegardes locales et géographiques dans le cadre de ses fonctionnalités de sauvegarde d’entrepôt de données . Celles-ci incluent des captures instantanées Stockage Blob Azure et le stockage géoredondant. Utilisez des sauvegardes d’entrepôt de données pour restaurer votre entrepôt de données à un point de restauration dans la région primaire, ou pour restaurer dans une autre région géographique. Cet article explique les spécificités des sauvegardes dans SQL Data Warehouse.

## <a name="what-is-a-data-warehouse-backup"></a>Qu’est-ce qu’une sauvegarde d’entrepôt de données ?
Une sauvegarde d’entrepôt de données est constituée de données que vous pouvez utiliser pour restaurer un entrepôt de données à un moment donné.  Comme SQL Data Warehouse est un système distribué, une sauvegarde d’entrepôt de données est constituée de nombreux fichiers qui sont stockés dans des objets blob Azure. 

Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Pour plus d’informations, consultez [Vue d’ensemble de la continuité de l’activité](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Redondance des données
SQL Data Warehouse protège vos données en les stockant dans un stockage Premium Azure localement redondant. Cette fonctionnalité Stockage Azure stocke plusieurs copies synchrones des données dans le centre de données local afin de garantir une protection transparente des données en cas de défaillance localisée. La redondance des données garantit que vos données peuvent survivre à des problèmes liés à l’infrastructure, comme des défaillances de disque. La redondance des données assure la continuité des activités avec une infrastructure hautement disponible tolérante aux pannes.

Pour en savoir plus sur :

* Stockage Premium Azure : consultez [Introduction à Stockage Premium Azure](../storage/storage-premium-storage.md).
* Stockage localement redondant : consultez [Réplication de Stockage Azure](../storage/storage-redundancy.md#locally-redundant-storage).

## <a name="azure-storage-blob-snapshots"></a>Captures instantanées d’objet blob Stockage Azure
Un avantage de l’utilisation de Stockage Premium Azure est que SQL Data Warehouse utilise des captures instantanées d’objet blob Stockage Azure pour sauvegarder localement l’entrepôt de données. Vous pouvez restaurer un entrepôt de données à un point de restauration de capture instantanée. Les captures instantanées démarrent au minimum toutes les huit heures et sont disponibles pendant sept jours.  

Pour en savoir plus sur :

* Captures instantanées d’objets blob Azure : consultez [Créer une capture instantanée d’objets blob](../storage/storage-blob-snapshots.md).

## <a name="geo-redundant-backups"></a>Sauvegardes géoredondantes
Toutes les 24 heures, SQL Data Warehouse stocke l’entrepôt de données complet dans le stockage Standard. L’entrepôt de données complet est créé de façon à correspondre au moment de la dernière capture instantanée. Le stockage standard appartient à un compte de stockage géoredondant avec un accès en lecture (RA-GRS). La fonctionnalité RA-GRS du service Stockage Microsoft Azure réplique les fichiers de sauvegarde sur un [centre de données associé](../best-practices-availability-paired-regions.md). Cette géoréplication vous garantit de pouvoir restaurer un entrepôt de base de données dans le cas où vous ne pouvez pas accéder aux captures instantanées de votre région primaire. 

Cette fonctionnalité est activée par défaut. Si vous ne souhaitez pas utiliser des sauvegardes géo-redondantes, vous pouvez vous [désabonner] (https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicy?redirectedfrom=msdn). 

> [!NOTE]
> Dans le stockage Azure, le terme *réplication* fait référence à la copie de fichier d’un emplacement à un autre. La *réplication de base de données* de SQL fait référence à la gestion de la synchronisation de plusieurs bases de données secondaires avec une base de données primaire. 
> 
> 

Pour en savoir plus sur :

* Le stockage géoredondant, consultez la section [Réplication Azure Storage](../storage/storage-redundancy.md).
* Le stockage RA-GRS, consultez la section [Stockage géoredondant avec accès en lecture](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Planification et période de rétention des sauvegardes d’entrepôt de données
SQL Data Warehouse crée des captures instantanées sur vos entrepôts de données en ligne toutes les quatre à huit heures et conserve chaque capture instantanée pendant sept jours. Vous pouvez restaurer votre base de données en ligne à un des points de restauration des sept derniers jours. 

Pour voir quand la dernière capture instantanée a démarré, exécutez cette requête sur votre entrepôt de données SQL Data Warehouse en ligne. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Si vous avez besoin de conserver une capture instantanée pendant plus de sept jours, vous pouvez restaurer un point de restauration sur un nouvel entrepôt de données. Une fois la restauration terminée, SQL Data Warehouse démarre la création de captures instantanées sur le nouvel entrepôt de données. Si vous n’apportez pas de modifications au nouvel entrepôt de données, les captures instantanées restent vides et par conséquent, le coût de la capture instantanée est minimal. Vous pouvez aussi suspendre la base de données pour éviter que SQL Data Warehouse crée des captures instantanées.

### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>Que se passe-t-il pour la rétention de mes sauvegardes pendant la mise en suspens de mon entrepôt de données ?
SQL Data Warehouse ne crée pas de captures instantanées et ne fait pas expirer les captures instantanées pendant la mise en suspens d’un entrepôt de données. L’ancienneté de la capture instantanée ne change pas pendant que l’entrepôt de données est mis en suspens. La rétention des captures instantanées est basée sur le nombre de jours pendant lesquels l’entrepôt de données est en ligne et non pas sur les jours du calendrier.

Par exemple, si une capture instantanée démarre le 1er octobre à 16h00 et que l’entrepôt de données est mis en suspens le 3 octobre à 16h00, l’ancienneté de la capture instantanée est de deux jours. Quand l’entrepôt de données revient en ligne, la capture instantanée a une ancienneté de deux jours. Si l’entrepôt de données est mis en ligne le 5 octobre à 16h00, l’instantané a une ancienneté de deux jours et est conservé pendant encore cinq jours.

Quand l’entrepôt de données revient en ligne, SQL Data Warehouse reprend de nouvelles captures instantanées et fait expirer les captures instantanées quand elles ont plus de sept jours de données.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>Quelle est la durée de la période de rétention pour un entrepôt de données supprimé ?
Quand un entrepôt de données est supprimé, l’entrepôt de données et les captures instantanées sont enregistrés pour une durée de sept jours, puis ils sont supprimés. Vous pouvez restaurer l’entrepôt de données à un des points de restauration enregistrés.

> [!IMPORTANT]
> Si vous supprimez une instance SQL Server logique, toutes les bases de données qui appartiennent à l’instance sont également supprimées, sans pouvoir être récupérées. Vous ne pouvez pas restaurer un serveur supprimé.
> 
> 

## <a name="data-warehouse-backup-costs"></a>Coûts des sauvegardes des entrepôts de données
Le coût total de votre entrepôt de données principal et de sept jours de captures instantanées d’objets blob Azure est arrondi au To le plus proche. Par exemple, si la taille de votre entrepôt de données est de 1,5 To et que les captures instantanées utilisent 100 Go, vous êtes facturé pour 2 To de données aux prix du Stockage Premium Azure. 

> [!NOTE]
> Chaque capture instantanée est initialement vide et augmente à mesure que vous apportez des modifications à l’entrepôt de données principal. La taille de toutes les captures instantanées augmente au fil des modifications de l’entrepôt de données. Par conséquent, les coûts de stockage pour les captures instantanées augmentent en fonction de l’importance des modifications.
> 
> 

Si vous utilisez le stockage géoredondant, vous payez des frais de stockage distincts. Le stockage géoredondant est facturé au prix standard du stockage géoredondant avec accès en lecture (RA-GRS).

Pour plus d’informations sur la tarification de SQL Data Warehouse, consultez [SQL Data Warehouse Tarification](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>Utilisation des sauvegardes de base de données
La principale utilisation des sauvegardes SQL Data Warehouse est de restaurer l’entrepôt de données à un des points de restauration de la période de rétention.  

* Pour restaurer un entrepôt de données SQL, consultez [Restaurer un entrepôt de données SQL](sql-data-warehouse-restore-database-overview.md).

## <a name="related-topics"></a>Rubriques connexes
### <a name="scenarios"></a>Scénarios
* Pour une vue d’ensemble de la continuité des activités, consultez [Vue d’ensemble de la continuité des activités](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

* Pour restaurer un entrepôt de données, consultez [Restaurer un entrepôt de données SQL](sql-data-warehouse-restore-database-overview.md).

<!-- ### Tutorials -->




<!--HONumber=Nov16_HO4-->


