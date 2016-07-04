<properties
	pageTitle="Restaurer une base de données SQL supprimée (portail Azure) | Microsoft Azure"
	description="Restaurez une base de données SQL Azure supprimée (portail Azure)."
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
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurer une base de données SQL Azure supprimée à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-restore-deleted-database.md)
- [Portail Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)


## Sélectionner la base de données à restaurer 

Pour restaurer une base de données dans le portail Azure, procédez comme suit :

1.	Ouvrez le [portail Azure](https://portal.azure.com).
2.  Sur le côté gauche de l’écran, sélectionnez **PARCOURIR** > **Serveurs SQL**.
3.  Accédez au serveur sur lequel la base de données que vous voulez restaurer est stockée, puis sélectionnez le serveur.
4.  Faites défiler jusqu’à la section **Opérations** du panneau de votre serveur, puis sélectionnez **Bases de données supprimées** : ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Sélectionnez la base de données supprimée que vous souhaitez restaurer.
6.  Spécifiez un nom de base de données, puis cliquez sur OK :

    ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## Étapes suivantes

- Pour obtenir des instructions détaillées sur la restauration d’une base de données supprimée à l’aide de PowerShell, consultez [Restaurer une base de données supprimée à l’aide de PowerShell](sql-database-restore-deleted-database-powershell.md).
- Pour obtenir des informations sur la restauration d’une base de données supprimée, consultez [Restaurer une base de données supprimée à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Pour obtenir des informations détaillées sur la restauration d’une base de données supprimée, consultez [Restaurer une base de données supprimée](sql-database-restore-deleted-database.md)
- Pour plus d’informations concernant les sauvegardes automatisées de base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).

## Ressources supplémentaires

- [Limite de restauration dans le temps](sql-database-point-in-time-restore.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->