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
	ms.date="07/17/2016"
	ms.author="sstein"
	ms.workload="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurer une base de données SQL Azure à un point antérieur dans le temps avec le portail Azure


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-recovery-using-backups.md)
- [Limite de restauration dans le temps : PowerShell](sql-database-point-in-time-restore-powershell.md)

Cet article explique comment restaurer votre base de données à un point antérieur dans le temps à partir de [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md) à l’aide du portail Azure.

## Sélectionner une base de données à restaurer à un point antérieur dans le temps

Pour restaurer une base de données dans le portail Azure, procédez comme suit :

1.	Ouvrez le [portail Azure](https://portal.azure.com).
2.  Sur le côté gauche de l’écran, sélectionnez **PARCOURIR** > **Bases de données SQL**.
3.  Accédez à la base de données à restaurer et sélectionnez-la.
4.  Dans la partie supérieure du panneau de votre base de données, sélectionnez **Restaurer** :

    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Indiquez un nom de base de données et un point de restauration, puis cliquez sur OK :

    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)



## Étapes suivantes

- Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->