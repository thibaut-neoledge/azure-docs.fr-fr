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
	ms.date="04/20/2015" 
	ms.author="sidneyh" />

# Ajout d'utilisateurs au pool élastique de bases de données

Les tâches de bases de données élastiques vous permettent d'exécuter le même script sur chaque base de données d'un [pool élastique de bases de données](sql-database-elastic-pool.md). Pour exécuter le script, vous devez ajouter les autorisations appropriées à chaque base de données du pool. Cet utilisateur peut être le même principal au niveau du serveur créé lors de l'installation des **tâches de bases de données élastiques** et il s'agit des **informations d'identification de tâche** fournies pour gérer les métadonnées de la base de données de **contrôle**. Pour en savoir plus, voir [Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx?f=255&MSPPError=-2147217396) ou [Ajout d'utilisateurs à votre base de données SQL Azure](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)

## Conditions préalables
* [Créer un pool élastique de bases de données (version préliminaire)](sql-database-elastic-pool-portal.md)
* Installez les [composants de la tâche élastique](sql-database-elastic-jobs-service-installation.md). 

## Ajout d'utilisateurs aux bases de données

1.	Connectez-vous d'abord au **master** du serveur de base de données SQL Azure où résident les bases de données de votre pool élastique de bases de données puis créez une connexion en utilisant les mêmes informations d'identification fournies lors de l'installation des **tâches de bases de données élastiques**.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Connectez-vous à chaque base de données du pool et créez un utilisateur avec les mêmes nom et mot de passe. L'utilisateur doit disposer des autorisations suffisantes pour exécuter la tâche. Ce code doit être exécuté sur chaque base de données.

		CREATE USER admin1 FROM LOGIN login1;
		
3. L'utilisateur doit également disposer des autorisations suffisantes pour exécuter le script spécifié pour la tâche. Utilisez la procédure **sp_addrolemember** pour fournir à l'utilisateur les autorisations minimales requises pour exécuter le script avec succès.

## Étapes suivantes

Exécutez une tâche sur le pool de bases de données. Voir [Création et gestion de tâches de bases de données élastiques](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=58-->