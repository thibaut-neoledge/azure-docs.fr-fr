<properties
   pageTitle="Déterminer la compatibilité de Base de données SQL à l’aide de SSMS"
   description="Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, Assistant d’exportation d’application de couche Données"
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
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Déterminer la compatibilité de Base de données SQL à l’aide de SSMS

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
 
Dans cet article, vous découvrirez comment déterminer si une base de données SQL Server est compatible pour la migration vers Base de données SQL à l’aide de l’Assistant d’exportation d’une application de couche données dans SQL Server Management Studio.

## Avec SQL Server Management Studio

1. Assurez-vous de disposer de la dernière version de SQL Server Management Studio. Les nouvelles versions de Management Studio sont mises à jour tous les mois afin de refléter les mises à jour publiées sur le portail Azure.

 	 >[AZURE.IMPORTANT]Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Ouvrez Management Studio et connectez-vous à votre base de données source dans l’Explorateur d’objets.
3. Dans l’Explorateur d’objets, cliquez avec le bouton droit de la souris sur la base de données source, pointez sur **Tâches**, puis cliquez sur **Exporter une application de couche Données…**

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Dans l’Assistant d’exportation, cliquez sur **Suivant**, puis sous l’onglet **Paramètres**, configurez l’exportation de manière à enregistrer le fichier BACPAC sur le disque local ou dans un objet blob Azure. L’enregistrement du fichier BACPAC ne pourra s’effectuer que si vous ne rencontrez aucun problème de compatibilité de base de données. Si des problèmes de compatibilité sont détectés, ils s’afficheront sur la console.

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Cliquez sur l’**onglet Avancé** et décochez la case **Sélectionner tout** pour ignorer l’exportation des données. À ce stade, nous ne cherchons qu’à tester la compatibilité.

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Cliquez sur **Suivant**, puis sur **Terminer**. Les éventuels problèmes de compatibilité de base de données s’afficheront une fois le schéma validé par l’Assistant.

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Si aucune erreur n’apparaît, votre base de données est compatible : vous êtes donc prêt pour la migration. Si des erreurs sont détectées, vous devez les corriger. Pour afficher les erreurs, cliquez sur **Erreur** au niveau de **Schéma de validation**. ![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.	Si le fichier *.BACPAC est correctement généré, votre base de données est alors compatible avec la base de données SQL, et vous êtes prêt pour la migration.

## Étape suivante : résoudre les problèmes de compatibilité, le cas échéant

[Résoudre les problèmes de compatibilité de base de données](sql-database-cloud-migrate-fix-compatibility-issues.md), le cas échéant.

<!---HONumber=AcomDC_1223_2015-->