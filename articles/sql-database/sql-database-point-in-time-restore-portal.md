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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurer une base de données SQL Azure à un point antérieur dans le temps avec le portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Cet article explique comment restaurer votre base de données à un point antérieur dans le temps à l’aide du portail Azure.

La [**limite de restauration dans le temps**](sql-database-point-in-time-restore.md) est une fonctionnalité en libre-service qui vous permet de restaurer une base de données à partir des sauvegardes automatiques que nous effectuons pour toutes les bases de données à n’importe quel point de la période de rétention de votre base de données. Pour en savoir plus sur les périodes de rétention des bases de données et des sauvegardes automatiques, consultez l’article [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

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
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)



## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->