<properties
   pageTitle="Continuité d’activité dans le Cloud - Restauration d’une base de données supprimée - Base de données SQL | Microsoft Azure"
   description="Apprenez-en plus sur la limite de restauration dans le temps, qui vous permet de restaurer une base de données SQL Azure à un point antérieur dans le temps (jusqu’à 35 jours)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Vue d’ensemble : restauration d’une base de données SQL Azure supprimée

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-restore-deleted-database.md)
- [Portail Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

Vous pouvez restaurer une base de données supprimée pendant la période de rétention pour les [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md). Vous pouvez utiliser le [portail Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) [l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

Si vous supprimez une base de données, la dernière sauvegarde est conservée pendant la période de rétention normale, ce qui vous permet de restaurer la base de données au point auquel elle a été supprimée.

## Restauration d’une base de données récemment supprimée

Pour les bases de données supprimées, le point de restauration est fixé à l’heure de suppression de la base de données. Lorsque vous restaurez une base de données supprimée, elle peut uniquement être restaurée sur le serveur contenant la base de données d’origine. Gardez cela à l’esprit lorsque vous supprimez un serveur, car une fois le serveur supprimé, vous ne serez pas en mesure de restaurer les bases de données qui y étaient stockées.

> [AZURE.IMPORTANT] Si vous supprimez une instance de serveur de base de données SQL Azure, toutes ses bases de données sont également supprimées et ne peuvent pas être récupérées.

## Résumé

Les sauvegardes automatiques protègent vos bases de données d’une suppression accidentelle. Cette solution gratuite et ne nécessitant aucune gestion est disponible pour toutes les bases de données SQL.

## Étapes suivantes

- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)
- [Restaurer une base de données à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md)
- [Restaurer une base de données à l’aide de PowerShell](sql-database-restore-deleted-database-powershell.md)
- [Restaurer une base de données à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)

## Ressources supplémentaires

- [Restauration dans le temps](sql-database-point-in-time-restore.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->