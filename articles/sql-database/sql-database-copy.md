<properties
	pageTitle="Copie d’une base de données SQL Azure | Microsoft Azure"
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



# Copie d'une base de données SQL Azure

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-copy.md)
- [Portail Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Vous pouvez utiliser les [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md) Azure pour créer une copie de votre base de données SQL. L’opération de copie copie la fin du journal des transactions, puis utilise les sauvegardes complètes, différentielles ainsi que les journaux de transactions qui font partie des sauvegardes automatisées pour créer une base de données cohérente au niveau transactionnel avec la base de données source au moment de la sauvegarde finale du journal de transactions.

Vous pouvez créer la copie de la base de données sur le même serveur ou sur un autre serveur. Le niveau de service et le niveau de performances (niveau de tarification) de la copie de base de données sont les mêmes que ceux de la base de données source. Une fois la copie terminée, la copie devient une base de données indépendante et entièrement fonctionnelle. Les connexions, les utilisateurs et les autorisations peuvent être gérés indépendamment.

Lorsque vous copiez une base de données sur le même serveur logique, les mêmes connexions peuvent être utilisées sur les deux bases de données. L'élément principal de sécurité que vous utilisez pour copier la base de données devient le propriétaire de la base de données (DBO) sur la nouvelle base de données. Tous les utilisateurs de la base de données, leurs autorisations et leurs identificateurs de sécurité (SID) sont copiés vers la copie de base de données.

Lorsque vous copiez une base de données vers un autre serveur logique, le principal de sécurité sur le nouveau serveur devient le propriétaire de la base de données sur la nouvelle base de données. Les utilisateurs de la base de données qui sont des utilisateurs contenus pouvant être utilisés dans la base de données copiée. Toutefois, lorsque vous copiez la base de données vers un nouveau serveur, les connexions des utilisateurs échoueront généralement car ces connexions n’existent pas sur le nouveau serveur, et si elles existent, leur SID peut ne pas correspondre. Une fois que la nouvelle base de données est en ligne sur le serveur de destination, utilisez l'instruction [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) pour remapper les utilisateurs de la nouvelle base de données avec des connexions sur le serveur de destination. Pour résoudre les problèmes d’utilisateurs orphelins, consultez [Dépannage des utilisateurs orphelins](https://msdn.microsoft.com/library/ms175475.aspx).


Pour copier une base de données SQL, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL à copier. Si vous n’avez pas de base de données SQL, créez-en une en procédant de la manière décrite dans [Créer votre première base de données SQL Azure](sql-database-get-started.md).

## Étapes suivantes

- Consultez la page [Copier une base de données SQL Azure à l’aide du portail Azure](sql-database-copy-portal.md) pour copier une base de données à l’aide du portail Azure.
- Consultez la page [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) pour copier une base de données à l’aide de PowerShell.
- Consultez la page [Copier une base de données SQL Azure à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
- Consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données vers un autre serveur logique.



## Ressources supplémentaires

- [Gérer les connexions](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données vers un fichier BACPAC](sql-database-export.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0622_2016-->