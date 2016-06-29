<properties
	pageTitle="Restaurer une base de données SQL Azure à un point antérieur dans le temps (portail Azure) | Microsoft Azure"
	description="Restaurez une base de données SQL Azure à un point antérieur dans le temps."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurer une base de données SQL Azure à un point antérieur dans le temps avec le portail Azure


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-point-in-time-restore.md)
- [Portail Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Cet article explique comment restaurer votre base de données à un point antérieur dans le temps à partir de [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md) à l’aide du portail Azure.

## Sélectionner une base de données à restaurer à un point antérieur dans le temps

Pour restaurer une base de données dans le portail Azure, procédez comme suit :

1.	Ouvrez le [portail Azure](https://portal.azure.com).
2.  Sur le côté gauche de l’écran, sélectionnez **PARCOURIR** > **Bases de données SQL**.
3.  Accédez à la base de données à restaurer et sélectionnez-la.
4.  Dans la partie supérieure du panneau de votre base de données, sélectionnez **Restaurer** :

    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Indiquez un nom de base de données et un point de restauration, puis cliquez sur OK :

    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)


## Étapes suivantes

- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)
- [Restauration dans le temps](sql-database-point-in-time-restore.md)
- [Limite de restauration dans le temps à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)

## Ressources supplémentaires

- [restauration d’une base de données supprimée.](sql-database-restore-deleted-database.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->