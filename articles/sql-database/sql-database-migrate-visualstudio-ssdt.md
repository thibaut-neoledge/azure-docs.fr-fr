<properties 
   pageTitle="Migration effectuée à l’aide de Visual Studio et SSDT"
	description="Microsoft Azure SQL Database, migration de base de données, importer une base de données, exporter une base de données, assistant de migration"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="08/24/2015"
	ms.author="carlrab"/>

#Mettre à jour la base de données existante et la déployer dans Azure SQL Database

![alt text](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

Utilisez cette option lorsque la migration d’une base de données locale vers Azure SQL Database V12 nécessite des modifications du schéma, car la base de données utilise des fonctionnalités de SQL Server qui ne sont pas prises en charge dans Azure SQL Database, ou pour tester si des fonctionnalités non prises en charge sont présentes dans une base de données locale.

Utilisez la [dernière version de SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) avec Visual Studio 2013 Update 4 ou version ultérieure.

Avec cette option :

 - SQL Server Data Tools (SSDT) pour Visual Studio est d’abord utilisé pour créer un projet de base de données à partir de la base de données source. 
 - La plateforme du projet cible est alors définie sur Azure SQL Database V12 et le projet est généré pour identifier tous les problèmes de compatibilité. 
 - Une fois le projet correctement généré, le schéma est republié dans une copie de la base de données source (sans écraser celle-ci).
 - La fonctionnalité de comparaison de données dans SSDT est ensuite utilisée pour comparer la base de données source à la base de données compatible SQL Azure créée, puis pour mettre à jour la base de données avec les données de la base de données source. 
 - La base de données mise à jour est ensuite déployée sur Azure à l’aide de SSMS, soit directement, soit en exportant puis en important un fichier BACPAC.
 
>[AZURE.NOTE]Remarque : si seul le déploiement du schéma est requis, le schéma mis à jour peut être directement publié dans Azure SQL Database.

## Étapes de la migration

1.	Ouvrez l’**Explorateur d’objets SQL Server** dans Visual Studio. Utilisez l’option **Ajouter SQL Server** pour vous connecter à l’instance SQL Server qui contient la base de données en cours de migration. Localisez la base de données dans l’explorateur, cliquez dessus avec le bouton droit de la souris et sélectionnez **Créer un nouveau projet...**. 

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	Configurez les paramètres d’importation sur la valeur **Importer uniquement les objets de portée application**. Décochez les options d’importation des éléments suivants : connexions référencées, autorisations et paramètres de base de données.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Cliquez sur **Démarrer** pour importer la base de données et créer le projet, qui doit contenir un fichier de script T-SQL pour chaque objet de cette base de données. Les fichiers de script sont imbriqués dans des dossiers au sein du projet.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit de la souris sur le projet de base de données, puis sélectionnez Propriétés. La page **Paramètres du projet** s’ouvre, vous permettant de définir le champ Plateforme cible sur la base de données SQL Microsoft Azure version 12.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	Avec le bouton droit de la souris, cliquez sur le projet, puis sélectionnez **Générer** pour générer le projet.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	La **liste d’erreurs** affiche chaque incompatibilité. Dans ce cas, le nom d’utilisateur NT AUTHORITY\\NETWORK SERVICE est incompatible. Vous pouvez donc le mettre en commentaire ou le supprimer (et gérer les conséquences de la suppression de cet identifiant et de ce rôle de la solution de base de données).

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)
7.	Double-cliquez sur le premier script pour l’ouvrir dans une fenêtre de requête, mettez-le en commentaire, puis exécutez le script. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

8.	Répétez ce processus pour chaque script contenant des incompatibilités jusqu’à résoudre toutes les erreurs. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
9.	Lorsque la base de données ne contient pas d’erreurs, cliquez avec le bouton droit de la souris sur le projet, puis sélectionnez **Publier** pour générer et publier la base de données vers une copie de la base de données source (nous vous recommandons vivement d’utiliser une copie, au moins au départ). 
 - Avant d’effectuer la publication, selon la version source de SQL Server (antérieure à SQL Server 2014), vous devrez peut-être réinitialiser la plateforme cible du projet cible pour activer le déploiement. 
 - Si vous effectuez la migration d’une base de données SQL Server de version antérieure, vous ne devez introduire aucune fonctionnalité non prise en charge par le système SQL Server source dans le projet, sauf si vous migrez la base de données vers une version plus récente de SQL Server. 

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

10.	Dans l’Explorateur d’objets de SQL Server, cliquez avec le bouton droit de la souris sur votre base de données source, puis cliquez sur **Comparaison de données** pour comparer le projet à la base de données d’origine et comprendre les modifications apportées par l’assistant. Sélectionnez votre version Azure SQL V12 de la base de données, puis cliquez sur **Terminer**.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

12.	Passez en revue les différences détectées, puis cliquez sur **Mettre à jour la cible** pour migrer les données de la base de données source dans la base de données Azure SQL V12.

	![alt text](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)

14.	Déployez les données et le schéma de base de données compatibles SQL Azure V12 dans SQL Azure à l’aide de SSMS. Consultez [Migration d’une base de données compatible à l’aide de SSMS](sql-database-migrate-ssms.md).

<!---HONumber=August15_HO9-->