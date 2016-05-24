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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Géo-restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Cet article vous explique comment restaurer votre base de données sur un nouveau serveur à l’aide de la géo-restauration en utilisant le portail Azure.

La [géo-restauration](sql-database-geo-restore.md) permet de restaurer une base de données à partir d’une sauvegarde géo-redondante utilisée pour créer une nouvelle base de données. La base de données peut être créée sur n’importe quel serveur dans n’importe quelle région Azure. Comme elle utilise une sauvegarde géo-redondante en tant que source, vous pouvez la mettre à profit pour récupérer une base de données même si celle-ci est inaccessible en raison d’une défaillance. La géo-restauration est automatiquement activée pour tous les niveaux de service, sans coûts supplémentaires.

## Sélectionner la base de données à restaurer

Pour restaurer une base de données dans le portail Azure, procédez comme suit :

1.	Ouvrez le [portail Azure](https://portal.azure.com).
2.  Sur le côté gauche de l’écran, sélectionnez **Nouveau** > **Données et stockage** > **Base de données SQL**.
3.  Sélectionnez **Sauvegarde** comme source, puis sélectionnez la sauvegarde géo-redondante à partir de laquelle vous souhaitez effectuer la récupération.

    ![Restaurer une base de données SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  Spécifiez un nom de base de données et le serveur sur lequel restaurer la base de données, puis cliquez sur Créer :

## Étapes suivantes

- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exercices de récupération d'urgence](sql-database-disaster-recovery-drills.md)


## Ressources supplémentaires

- [Restauration géographique](sql-database-geo-restore.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->