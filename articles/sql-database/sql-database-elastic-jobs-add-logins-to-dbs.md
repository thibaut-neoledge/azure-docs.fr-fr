<properties 
	title="How to add a users to an elastic database pool" 
	pageTitle="Ajout d'utilisateurs à un pool élastique de bases de données" 
	description="Vous devez ajouter un utilisateur disposant des privilèges pour chaque base de données du pool" 
	metaKeywords="azure sql database elastic databases credentials" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/27/2015" 
	ms.author="ddove; sidneyh" />

# Ajout d’utilisateurs à un pool de base de données élastique

La fonctionnalité **Tâches de bases de données élastiques** (version préliminaire) vous permet d'exécuter un script Transact-SQL dans un groupe de bases de données, notamment une collection de bases de données personnalisée, un **pool de base de données élastique** ou un **ensemble de partitions de base de données élastique** dans la base de données SQL Azure. Pour exécuter le script, vous devez ajouter les autorisations appropriées à chaque base de données du pool dans laquelle la tâche s’exécutera. Pour en savoir plus, voir [Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx) ou [Ajout d'utilisateurs à votre base de données SQL Azure](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)

## Composants requis
* Installez les [composants de la tâche élastique](sql-database-elastic-jobs-service-installation.md). 

## Ajout d'utilisateurs aux bases de données

1.	Connectez-vous d'abord au **master** du serveur de base de données SQL Azure où résident les bases de données de votre pool élastique de bases de données puis créez une connexion en utilisant les mêmes informations d'identification fournies lors de l'installation des **tâches de bases de données élastiques**.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Connectez-vous à chaque base de données du pool et créez un utilisateur avec les mêmes nom et mot de passe. L'utilisateur doit disposer des autorisations suffisantes pour exécuter la tâche. Ce code doit être exécuté sur chaque base de données.

		CREATE USER admin1 FROM LOGIN login1;
		
3. L'utilisateur doit également disposer des autorisations suffisantes pour exécuter le script spécifié pour la tâche. Utilisez la procédure **sp\_addrolemember** pour fournir à l'utilisateur les autorisations minimales requises pour exécuter le script avec succès.

## Étapes suivantes

Pour assurer la création et la gestion des tâches, consultez la rubrique [Création et gestion de tâches de bases de données élastiques](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Oct15_HO3-->