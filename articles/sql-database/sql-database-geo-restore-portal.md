<properties
	pageTitle="Restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante (portail Azure) | Microsoft Azure"
	description="Géo-restaurez une base de données SQL Azure à partir d’une sauvegarde géo-redondante (portail Azure)."
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


# Géo-restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-recovery-using-backups.md)
- [Restauration géographique : PowerShell](sql-database-geo-restore-powershell.md)

Cet article vous explique comment restaurer votre base de données sur un nouveau serveur à l’aide de la géo-restauration en utilisant le portail Azure.

## Sélectionner la base de données à restaurer

Pour restaurer une base de données dans le portail Azure, procédez comme suit :

1.	Ouvrez le [portail Azure](https://portal.azure.com).
2.  Sur le côté gauche de l’écran, sélectionnez **Nouveau** > **Données et stockage** > **Base de données SQL**.
3.  Sélectionnez **Sauvegarde** comme source, puis la sauvegarde géo-redondante à partir de laquelle vous souhaitez effectuer la récupération.

    ![Restaurer une base de données SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  Spécifiez un nom de base de données et le serveur sur lequel restaurer la base de données, puis cliquez sur Créer :


## Étapes suivantes

- Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->