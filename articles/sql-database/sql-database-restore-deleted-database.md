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
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Vue d’ensemble : restauration d’une base de données SQL Azure supprimée

> [AZURE.SELECTOR]
- [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Limite de restauration dans le temps](sql-database-point-in-time-restore.md)
- [Restauration d’une base de données supprimée](sql-database-restore-deleted-database.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Scénarios de continuité des activités](sql-database-business-continuity-scenarios.md)


Vous pouvez restaurer une base de données supprimée pendant la période de rétention pour les [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md) pour votre [niveau de service](sql-database-service-tiers.md). Vous pouvez utiliser le [portail Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) ou [l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## Restauration d’une base de données récemment supprimée

Vous pouvez restaurer la base de données en utilisant le même nom de base de données ou un autre nom sur le serveur logique qui contenait la base de données d’origine. Pour les bases de données supprimées, le point de restauration est fixé à l’heure de suppression de la base de données.

> [AZURE.IMPORTANT] Si vous supprimez une instance de serveur de base de données SQL Azure, toutes ses bases de données sont également supprimées et ne peuvent pas être récupérées.

## Durée de restauration

Le temps nécessaire pour restaurer une base de données dépend de nombreux facteurs, notamment de la taille de la base de données, le nombre de journaux de transactions, le point de restauration sélectionné et la quantité d’activités devant être relues pour reconstruire l’état au point sélectionné. Pour une base de données de très grande taille et/ou très active, la restauration peut prendre plusieurs heures. La restauration d’une base de données crée systématiquement une nouvelle base de données sur le même serveur que la base de données d’origine. La base de données restaurée doit donc être renommée. La majorité des restaurations de bases de données se terminent dans un délai de 12 heures.

## Résumé

Les sauvegardes automatiques protègent vos bases de données d’une suppression accidentelle. Cette solution gratuite et ne nécessitant aucune gestion est disponible pour toutes les bases de données SQL.

## Étapes suivantes

- Pour obtenir des instructions détaillées sur la restauration d’une base de données supprimée à l’aide du portail Azure, consultez [Restaurer une base de données supprimée à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md).
- Pour obtenir des instructions détaillées sur la restauration d’une base de données supprimée à l’aide de PowerShell, consultez [Restaurer une base de données supprimée à l’aide de PowerShell](sql-database-restore-deleted-database-powershell.md).
- Pour obtenir des informations sur la restauration d’une base de données supprimée, consultez [Restaurer une base de données supprimée à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Pour plus d’informations concernant les sauvegardes automatisées de base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).

## Ressources supplémentaires

- [Limite de restauration dans le temps](sql-database-point-in-time-restore.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->