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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurer une base de données SQL Azure supprimée à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

Cet article vous indique comment restaurer une base de données SQL Azure supprimée.

En cas de suppression d’une base de données, la base de données SQL Microsoft Azure permet de la restaurer au moment de cette suppression. La base de données SQL Microsoft Azure stocke la sauvegarde de base de données supprimée pendant la période de rétention de la base de données.

La période de rétention d’une base de données supprimée est déterminée par le niveau de service de la base de données pendant sa période d’existence, ou le nombre de jours d’existence de la base de données. La valeur la plus faible est choisie. Pour en savoir plus sur la rétention de base de données, consultez notre [vue d’ensemble de la fonction de continuité d’activité](sql-database-business-continuity.md).

## Sélectionnez la base de données à restaurer. 

Pour restaurer une base de données dans le portail Azure, procédez comme suit :

1.	Ouvrez le [portail Azure](https://portal.azure.com).
2.  Sur le côté gauche de l’écran, sélectionnez **PARCOURIR** > **Serveurs SQL**.
3.  Accédez au serveur sur lequel la base de données que vous voulez restaurer est stockée, puis sélectionnez le serveur.
4.  Faites défiler jusqu’à la section **Opérations** de votre panneau serveur, puis sélectionnez **Bases de données supprimées** : ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Sélectionnez la base de données supprimée que vous souhaitez restaurer.
6.  Spécifiez un nom de base de données, puis cliquez sur OK :

    ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## Étapes suivantes

- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)



## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->