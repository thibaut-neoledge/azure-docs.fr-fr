<properties
   pageTitle="Résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers une base de données SQL | Microsoft Azure"
   description="Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure, SSDT"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Utilisation de SQL Server Data Tools pour Visual Studio afin de migrer une base de données SQL Server vers une base de données SQL Azure 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Conseiller de mise à niveau](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Dans cet article, vous apprendrez à détecter et résoudre les problèmes de compatibilité de base de données SQL Server à l’aide deSQL Server Data Tools pour Visual Studio avant la migration vers une base de données SQL Azure.

## Utilisation de SQL Server Data Tools pour Visual Studio

Utilisez SQL Server Data Tools pour Visual Studio (SSDT) pour importer le schéma de base de données dans un projet de base de données Visual Studio à des fins d’analyse. Pour l’analyse, vous devez désigner la plate-forme cible du projet en tant que base de données V12, puis réalisez le projet. Si la version est réussie, la base de données est compatible. Si la génération échoue, vous pouvez résoudre les erreurs dans SSDT (ou un des autres outils décrits dans cette rubrique). Une fois la génération du projet réussie, vous pouvez de nouveau publier ce dernier en tant que copie de base de données source, puis utiliser la fonction de comparaison des données de SSDT pour copier les données de la base de données source vers la base de données Azure SQL V12 compatible. Vous pouvez alors migrer cette base de données mise à jour. Pour utiliser cette option, téléchargez la [version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Schéma de migration VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Si seule la migration de schéma est requise, vous pouvez publier le schéma directement depuis Visual Studio vers la base de données SQL Azure. Utilisez cette méthode lorsque le schéma de base de données nécessite un plus grand nombre de modifications qui peuvent être gérées par l’Assistant seul.

## Détection des problèmes de compatibilité à l’aide de SQL Server Data Tools pour Visual Studio
   
1.	Ouvrez l’**Explorateur d’objets SQL Server** dans Visual Studio. Utilisez l’option **Ajouter SQL Server** pour vous connecter à l’instance SQL Server qui contient la base de données en cours de migration. Localisez la base de données dans l’explorateur, cliquez dessus avec le bouton droit de la souris et sélectionnez **Créer un nouveau projet...**.
    
	![Nouveau projet](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)
   
2.	Configurez les paramètres d’importation sur la valeur **Importer uniquement les objets de portée application**. Décochez les options d’importation des éléments suivants : connexions référencées, autorisations et paramètres de base de données.

    ![alt text](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Cliquez sur **Démarrer** pour importer la base de données et créer le projet, qui doit contenir un fichier de script T-SQL pour chaque objet de cette base de données. Les fichiers de script sont imbriqués dans des dossiers au sein du projet.

    ![alt text](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit de la souris sur le projet de base de données, puis sélectionnez Propriétés. La page **Paramètres du projet** s’ouvre, vous permettant de définir le champ Plateforme cible sur Microsoft Azure SQL Database V12.
    
    ![alt text](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)
    
5.	Avec le bouton droit de la souris, cliquez sur le projet, puis sélectionnez **Générer** pour générer le projet.
    
	![alt text](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)
    
6.	La **liste d’erreurs** affiche chaque incompatibilité. Dans ce cas, le nom d’utilisateur NT AUTHORITY\\NETWORK SERVICE est incompatible. Vous pouvez donc le mettre en commentaire ou le supprimer (et gérer les conséquences de la suppression de cet identifiant et de ce rôle de la solution de base de données).
    
	![alt text](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)
    
## Résolution des problèmes de compatibilité à l’aide de SQL Server Data Tools pour Visual Studio        
  
1.	Double-cliquez sur le premier script pour l’ouvrir dans une fenêtre de requête, mettez-le en commentaire, puis exécutez le script. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.	Répétez ce processus pour chaque script contenant des incompatibilités jusqu’à résoudre toutes les erreurs. ![alt text](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)  
 
3.	Lorsque la base de données ne contient pas d’erreurs, cliquez avec le bouton droit de la souris sur le projet, puis sélectionnez **Publier** pour générer et publier la base de données vers une copie de la base de données source (nous vous recommandons vivement d’utiliser une copie, au moins au départ).
 - Avant d’effectuer la publication, selon la version source de SQL Server (antérieure à SQL Server 2014), vous devrez peut-être réinitialiser la plateforme cible du projet cible pour activer le déploiement.
 - Si vous effectuez la migration d’une base de données SQL Server de version antérieure, vous ne devez introduire aucune fonctionnalité non prise en charge par le système SQL Server source dans le projet, sauf si vous migrez la base de données vers une version plus récente de SQL Server.

    	![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
    	![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
    
4.	Dans l’Explorateur d’objets de SQL Server, cliquez avec le bouton droit de la souris sur votre base de données source, puis cliquez sur **Comparaison de données** pour comparer le projet à la base de données d’origine et comprendre les modifications apportées par l’assistant. Sélectionnez votre version Azure SQL V12 de la base de données, puis cliquez sur **Terminer**.
    
	![alt text](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)
    
	![alt text](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)
    
5.	Passez en revue les différences détectées, puis cliquez sur **Mettre à jour la cible** pour migrer les données de la base de données source dans la base de données Azure SQL V12.
    
	![alt text](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)
    
6.	Choisissez un modèle de déploiement. Consultez [Migrer une base de données SQL Server compatible vers une base de données SQL](sql-database-cloud-migrate.md).

## Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Ressources supplémentaires

- [Base de données SQL V12](sql-database-v12-whats-new.md)
- [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
- [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0803_2016-->