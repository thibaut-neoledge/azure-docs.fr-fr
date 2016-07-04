<properties
	pageTitle="Copie d’une base de données SQL Azure à l’aide du portail Azure | Microsoft Azure"
	description="Création d'une copie d'une base de données SQL Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/16/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Copie d’une base de données SQL Azure à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-copy.md)
- [Portail Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Les étapes suivantes vous montrent comment copier une base de données SQL avec le [portail Azure](https://portal.azure.com) sur le même serveur ou un serveur différent.

Pour copier une base de données SQL, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL à copier. Si vous n’avez pas de base de données SQL, créez-en une en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).


## Copie de votre base de données SQL

Ouvrez le panneau SQL Database de la base de données que vous voulez copier :

1.	Accédez au [portail Azure](https://portal.azure.com).
2.	Accédez à la base de données que vous souhaitez copier : Parcourir > Bases de données SQL
3.	Dans le panneau de la base de données SQL, cliquez sur **Copier** pour ouvrir le panneau **Copier** :

    ![copier une base de données][1]

1.  Entrez un nom pour la copie de la base de données. Un nom par défaut est fourni, mais vous pouvez le modifier si vous le souhaitez.
2.  Sélectionner un **serveur cible**. Le serveur cible est celui où la copie de la base de données sera créée. Vous pouvez créer un nouveau serveur ou sélectionnez un serveur existant dans la liste.
3.  Cliquez sur **OK** pour démarrer le processus de copie.

    ![nom et serveur de la base de données][2]


## Contrôle de la progression de l'opération de copie

- Après le démarrage de la copie, cliquez sur la notification du portail pour plus d'informations.

    ![notification][3]
 
    ![monitor][4]


## Vérifiez que la base de données est en ligne sur le serveur

- Cliquez sur **PARCOURIR** > **Bases de données SQL** et vérifiez que la nouvelle base de données est **En ligne**.


## Résolution des connexions

Pour résoudre les connexions à l’issue de l’opération de copie, consultez [Résoudre les connexions](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## Étapes suivantes

- Consultez la page [Copie d’une base de données SQL Azure](sql-database-copy.md) pour une vue d’ensemble de la copie d’une base de données SQL Azure.
- Consultez la page [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) pour copier une base de données à l’aide de PowerShell.
- Consultez la page [Copier une base de données SQL Azure à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
- Consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données vers un autre serveur logique.



## Ressources supplémentaires

- [Gérer les connexions](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données vers un fichier BACPAC](sql-database-export.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

<!---HONumber=AcomDC_0622_2016-->