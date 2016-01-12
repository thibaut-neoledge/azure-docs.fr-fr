<properties
   pageTitle="Résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers Base de données SQL"
   description="Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure, SSDT"
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

# Résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers Base de données SQL

Si vous constatez que votre base de données SQL Server source n’est pas compatible, vous disposez de plusieurs options pour corriger les problèmes de compatibilité identifiés.

> [AZURE.SELECTOR]
- Use [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Use [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-SSMS.md)

## Utilisation de SQL Server Data Tools pour Visual Studio

Utilisez SQL Server Data Tools pour Visual Studio (SSDT) pour importer le schéma de base de données dans un projet de base de données Visual Studio à des fins d’analyse. Pour l’analyse, vous devez désigner la plate-forme cible du projet en tant que base de données V12, puis réalisez le projet. Si la version est réussie, la base de données est compatible. Si la génération échoue, vous pouvez résoudre les erreurs dans SSDT (ou un des autres outils décrits dans cette rubrique). Une fois la génération du projet réussie, vous pouvez de nouveau publier ce dernier en tant que copie de base de données source, puis utiliser la fonction de comparaison des données de SSDT pour copier les données de la base de données source vers la base de données Azure SQL V12 compatible. Vous pouvez alors migrer cette base de données mise à jour. Pour utiliser cette option, téléchargez la [version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Schéma de migration VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  >[AZURE.NOTE]Si seule la migration de schéma est requise, vous pouvez publier le schéma directement depuis Visual Studio vers la base de données SQL Azure. Utilisez cette méthode lorsque le schéma de base de données nécessite un plus grand nombre de modifications qui peuvent être gérées par l’Assistant seul.

## Étape suivante : sélectionnez la méthode de migration et effectuez la migration

[Sélectionner la méthode de migration](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->