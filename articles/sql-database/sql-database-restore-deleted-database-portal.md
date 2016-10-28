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
	ms.date="07/09/2016"
	ms.author="sstein"
	ms.workload="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurer une base de données SQL Azure supprimée à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-recovery-using-backups.md)
- [Restauration d’une base de données supprimée : PowerShell](sql-database-restore-deleted-database-powershell.md)

## Sélectionner la base de données à restaurer 

Pour restaurer une base de données dans le portail Azure, procédez comme suit :

1.	Ouvrez le [portail Azure](https://portal.azure.com).
2.  Sur le côté gauche de l’écran, sélectionnez **PARCOURIR** > **Serveurs SQL**.
3.  Accédez au serveur sur lequel la base de données que vous voulez restaurer est stockée, puis sélectionnez le serveur.
4.  Faites défiler jusqu’à la section **Opérations** du panneau de votre serveur, puis sélectionnez **Bases de données supprimées** : ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Sélectionnez la base de données supprimée que vous souhaitez restaurer.
6.  Spécifiez un nom de base de données, puis cliquez sur OK :

    ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## Étapes suivantes

- Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->