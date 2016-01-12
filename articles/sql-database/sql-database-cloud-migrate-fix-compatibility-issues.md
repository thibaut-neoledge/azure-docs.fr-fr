<properties
   pageTitle="Résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers Base de données SQL"
   description="Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure"
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
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Utilisez l’assistant de migration SQL Azure

Utilisez l’outil Codeplex de l’[assistant de migration SQL Azure](http://sqlazuremw.codeplex.com/) pour générer un script T-SQL à partir d’une base de données source incompatible, qui sera ensuite transformé par l’assistant de manière à garantir sa compatibilité avec Base de données SQL. Il vous suffit alors de vous connecter à Base de données SQL Azure pour exécuter le script. Cet outil analyse également les fichiers de trace afin de déterminer les problèmes de compatibilité. Ce script peut être généré avec un schéma uniquement, ou peut inclure des données au format BCP. Pour obtenir une documentation supplémentaire, y compris des instructions pas à pas, consultez la rubrique [Assistant de migration SQL Azure](http://sqlazuremw.codeplex.com/) sur le site Codeplex.

 ![Schéma de migration SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  >[AZURE.NOTE]Remarque : les schémas non compatibles que l’assistant est susceptible de détecter ne peuvent pas tous être corrigés par ses transformations intégrées. Un script non compatible qui ne peut pas être traité est signalé comme une erreur et des commentaires sont insérés dans le script généré. Si de nombreuses erreurs sont détectées, utilisez Visual Studio ou SQL Server Management Studio pour examiner et résoudre chaque erreur que l’Assistant Migration SQL Server ne permet pas de corriger.

## Étape suivante : sélectionnez la méthode de migration et effectuez la migration

[Sélectionner la méthode de migration](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->