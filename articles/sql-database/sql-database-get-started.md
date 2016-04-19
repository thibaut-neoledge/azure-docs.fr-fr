<properties
	pageTitle="Didacticiel sur la base de données SQL : Créer une base de données SQL | Microsoft Azure"
	description="Découvrez comment configurer un serveur logique de base de données SQL, une règle de pare-feu de serveur, une base de données SQL, des exemples de données, mais également comment vous connecter avec des outils clients, configurer les utilisateurs et des règles de pare-feu de base de données."
	keywords="didacticiel sur la base de données SQL, créer une base de données sql"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/12/2016"
	ms.author="carlrab"/>

# Didacticiel sur la base de données SQL : Créer une base de données SQL en quelques minutes à l’aide du portail Azure

**Base de données unique**

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Dans ce didacticiel, vous allez apprendre à utiliser le portail Azure pour :

- créer un serveur logique de base de données SQL pour héberger des bases de données SQL ;
- créer une base de données SQL sans données avec des exemples de données ou avec des données d’une sauvegarde de base de données SQL ;
- créer une règle de pare-feu au niveau du serveur pour une seule adresse IP ou une plage d’adresses IP.

Utilisez les liens proposés pour exécuter les mêmes tâches à l’aide de [c#](sql-database-get-started-csharp.md) ou [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Connexion](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Création d’un serveur logique de base de données SQL](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Étapes suivantes
Maintenant que vous avez terminé ce didacticiel sur la base de données SQL et que vous avez créé une base de données avec des exemples de données, vous êtes prêt pour l’exploration à l’aide de vos outils préférés.

- Si vous êtes familiarisé avec Transact-SQL et SQL Server Management Studio, découvrez comment [Connecter et interroger une base de données SQL avec SSMS](sql-database-connect-query-ssms.md).

- Si vous connaissez Excel, découvrez comment [Se connecter à une base de données SQL avec Excel](sql-database-connect-excel.md).

- Si vous êtes prêt à commencer le codage, voir [Connecter et interroger votre base de données SQL avec C#](sql-database-connect-query.md) et [Utilisation de la base de données SQL à partir de .NET (C#)](sql-database-develop-dotnet-simple.md). Pour des exemples et des procédures Node.js, Python, Ruby, Java, PHP et C++, parallèlement à C#, voir [Exemples de code de démarrage rapide pour base de données SQL](sql-database-develop-quick-start-client-code-samples.md).

- Si vous souhaitez déplacer vos bases de données SQL Server locales vers Azure, voir [Migration d’une base de données vers la base de données SQL Azure](sql-database-cloud-migrate.md) pour plus d’informations.


## Ressources supplémentaires

[Vue d’ensemble des bases de données SQL](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0413_2016-->