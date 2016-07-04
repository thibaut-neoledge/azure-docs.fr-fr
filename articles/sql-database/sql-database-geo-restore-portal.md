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
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Géo-restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-geo-restore.md)
- [Portail Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Cet article vous explique comment restaurer votre base de données sur un nouveau serveur à l’aide de la géo-restauration en utilisant le portail Azure.

## Sélectionner la base de données à restaurer

Pour restaurer une base de données dans le portail Azure, procédez comme suit :

1.	Ouvrez le [portail Azure](https://portal.azure.com).
2.  Sur le côté gauche de l’écran, sélectionnez **Nouveau** > **Données et stockage** > **Base de données SQL**.
3.  Sélectionnez **Sauvegarde** comme source, puis la sauvegarde géo-redondante à partir de laquelle vous souhaitez effectuer la récupération.

    ![Restaurer une base de données SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  Spécifiez un nom de base de données et le serveur sur lequel restaurer la base de données, puis cliquez sur Créer :

## Étapes suivantes

- Pour obtenir des instructions détaillées sur la restauration d’une base de données SQL Azure à l’aide du portail Azure à partir d’une sauvegarde géo-redondante, consultez [Géo-restauration à l’aide du portail Azure](sql-database-geo-restore-portal.md)
- Pour obtenir des informations détaillées concernant la restauration d’une base de données SQL Azure à partir d’une sauvegarde géo-redondante, consultez [Géo-restauration à l’aide de PowerShell](sql-database-geo-restore.md)
- Pour une discussion complète sur la récupération après une panne, consultez [Récupération après une panne](sql-database-disaster-recovery.md)

## Ressources supplémentaires

- [Scénarios de continuité des activités](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->