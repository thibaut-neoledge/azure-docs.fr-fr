<properties
	pageTitle="Didacticiel sur la base de données SQL : Créer une base de données SQL | Microsoft Azure"
	description="Découvrez comment configurer un serveur logique SQL Database, une règle de pare-feu de serveur, une base de données SQL et des exemples de données. Apprenez également à vous connecter avec les outils clients, à configurer des utilisateurs et à définir une règle de pare-feu de base de données."
	keywords="didacticiel sur la base de données sql, créer une base de données sql"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/05/2016"
	ms.author="carlrab"/>


# Didacticiel sur SQL Database : Créer une base de données SQL en quelques minutes à l’aide du portail Azure

**Base de données unique**

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Ce didacticiel vous montre comment utiliser le Portail Azure pour :

- créer un serveur logique Azure SQL Database afin d’héberger des bases de données SQL ;
- créer une base de données SQL sans données avec des exemples de données ou avec des données d’une sauvegarde de base de données SQL ;
- créer une règle de pare-feu au niveau du serveur pour une seule adresse IP ou une plage d’adresses IP.

Vous pouvez exécuter les mêmes tâches à l’aide de [C#](sql-database-get-started-csharp.md) ou de [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Connexion](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

[AZURE.INCLUDE [Création d’un serveur logique de base de données SQL](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Étapes suivantes
Maintenant que vous avez terminé ce didacticiel sur SQL Database et que vous avez créé une base de données avec des exemples de données, vous êtes prêt pour l’exploration à l’aide de vos outils préférés.

- Si vous êtes familiarisé avec Transact-SQL et SQL Server Management Studio (SSMS), découvrez comment [connecter et interroger une base de données SQL avec SSMS](sql-database-connect-query-ssms.md).

- Si vous connaissez Excel, découvrez comment [vous connecter à une base de données SQL dans Azure avec Excel](sql-database-connect-excel.md).

- Si vous êtes prêt à commencer le codage, choisissez votre langage de programmation dans [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md).

- Si vous souhaitez déplacer vos bases de données SQL Server locales vers Azure, consultez la page [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md) pour plus d’informations.

- Si vous souhaitez charger des données dans une nouvelle table à partir d’un fichier CSV avec l’outil de ligne de commande BCP, consultez la page [Chargement de données dans une base de données SQL à partir d’un fichier CSV à l’aide de BCP](sql-database-load-from-csv-with-bcp.md).


## Ressources supplémentaires

[Définition de la base de données SQL](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0907_2016-->