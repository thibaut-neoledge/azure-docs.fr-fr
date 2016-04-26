<properties
	pageTitle="Didacticiel sur la base de données SQL : prise en main de la sécurité"
	description="Apprenez à créer des comptes d'utilisateurs pour accéder et gérer une base de données."
	keywords=""
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
	ms.date="04/13/2016"
	ms.author="carlrab"/>

# Didacticiel sur la base de données SQL : créer des comptes d'utilisateurs de base de données SQL pour accéder et gérer une base de données à l'aide du portail Azure

Dans ce didacticiel, vous allez apprendre à utiliser le portail Azure pour :

- Vous connecter à la base de données SQL à l'aide d'une connexion du principal au niveau du serveur
- Créer un compte d'utilisateur de base de données SQL
- Accorder des autorisations dbo d'un compte d'utilisateur de base de données SQL au sein d'une base de données utilisateur
- Se connecter à une base de données SQL avec un compte d'utilisateur qui n'est pas un principal au niveau du serveur 

[AZURE.INCLUDE [Connexion](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Création d’un serveur logique de base de données SQL](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[AZURE.INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-create-new-database-user.md)]

[AZURE.INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[AZURE.INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## Étapes suivantes
Maintenant que vous avez terminé ce didacticiel sur la base de données SQL, créé un compte d'utilisateur et accordé des dbo au compte d'utilisateur, vous êtes prêt à passer à la [sécurité de la base de données SQL](sql-database-manage-logins.md).

<!---HONumber=AcomDC_0420_2016-->