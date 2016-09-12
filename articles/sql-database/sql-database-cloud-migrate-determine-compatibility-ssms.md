<properties
   pageTitle="Utilisez SQL Server Management Studio afin de déterminer la compatibilité de la base de données SQL avant la migration vers une base de données SQL Azure | Microsoft Azure"
   description="Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, Assistant d’exportation d’application de couche Données"
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
   ms.date="08/29/2016"
   ms.author="carlrab"/>

# Utilisez SQL Server Management Studio afin de déterminer la compatibilité de la base de données SQL avant la migration vers une base de données SQL Azure

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Conseiller de mise à niveau](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
 
Dans cet article, vous découvrirez comment déterminer si une base de données SQL Server est compatible pour la migration vers Base de données SQL à l’aide de l’Assistant d’exportation d’une application de couche données dans SQL Server Management Studio.

## Avec SQL Server Management Studio

1. Assurez-vous de disposer de la dernière version de SQL Server Management Studio. Les nouvelles versions de Management Studio sont mises à jour tous les mois afin de refléter les mises à jour publiées sur le portail Azure.

 	 > [AZURE.IMPORTANT] Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Ouvrez Management Studio et connectez-vous à votre base de données source dans l’Explorateur d’objets.
3. Dans l’Explorateur d’objets, cliquez avec le bouton droit de la souris sur la base de données source, pointez sur **Tâches**, puis cliquez sur **Exporter une application de couche Données…**

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Dans l’Assistant d’exportation, cliquez sur **Suivant**, puis sous l’onglet **Paramètres**, configurez l’exportation de manière à enregistrer le fichier BACPAC sur le disque local ou dans un objet blob Azure. L’enregistrement du fichier BACPAC ne peut s’effectuer que si vous ne rencontrez aucun problème de compatibilité de base de données. Si des problèmes de compatibilité sont détectés, ils s’affichent sur la console.

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Cliquez sur **l’onglet Avancé** et décochez la case **Sélectionner tout** pour ignorer l’exportation des données. À ce stade, nous ne cherchons qu’à tester la compatibilité.

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Cliquez sur **Suivant**, puis sur **Terminer**. Les éventuels problèmes de compatibilité de base de données s’affichent une fois le schéma validé par l’Assistant.

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Si aucune erreur n’apparaît, votre base de données est compatible : vous êtes donc prêt pour la migration. Si des erreurs sont détectées, vous devez les corriger. Pour afficher les erreurs, cliquez sur **Erreur** au niveau de **Schéma de validation**. ![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.	Si le fichier *.BACPAC est correctement généré, votre base de données est alors compatible avec la base de données SQL, et vous êtes prêt pour la migration.

## Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Résoudre les problèmes de compatibilité de migration de la base de données](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrez une base de données SQL Server compatible vers une base de données SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Ressources supplémentaires

- [Base de données SQL V12](sql-database-v12-whats-new.md)
- [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
- [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0831_2016-->