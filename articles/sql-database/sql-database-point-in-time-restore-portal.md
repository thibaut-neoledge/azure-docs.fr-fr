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
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
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
2.  Sur le côté gauche de l’écran, sélectionnez **PARCOURIR** > **Bases de données SQL**.
3.  Accédez à la base de données à restaurer et sélectionnez-la.
4.  Dans la partie supérieure du panneau de votre base de données, sélectionnez **Restaurer** :

    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Indiquez un nom de base de données et un point de restauration, puis cliquez sur OK :

    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)


## Étapes suivantes

- Pour obtenir des instructions détaillées sur la récupération à un point dans le temps à l’aide de PowerShell, consultez [Limite de restauration dans le temps à l’aide de PowerShell](sql-database-point-in-time-restore-powershell.md).
- Pour obtenir des informations sur la récupération à un point dans le temps à l’aide de l’API REST, consultez [Limite de restauration dans le temps à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Pour une vue d’ensemble de la limite de restauration dans le temps, consultez [Limite de restauration dans le temps](sql-database-point-in-time-restore.md). ore.md)
- Pour une discussion complète sur la récupération après une erreur utilisateur ou une erreur d’application, consultez [Récupération d’erreur utilisateur](sql-database-user-error-recovery.md).

## Ressources supplémentaires

- [Scénarios de continuité des activités](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->