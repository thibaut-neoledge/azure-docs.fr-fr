<properties 
    pageTitle="Créer ou déplacer une base de données SQL Azure dans un pool élastique à l’aide de T-SQL | Microsoft Azure" 
    description="Utilisez T-SQL pour créer une base de données SQL Azure dans un pool élastique. Ou utilisez T-SQL pour déplacer la base de données dans et en dehors des pools." 
	services="sql-database" 
    documentationCenter="" 
    authors="sidneyh" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/01/2016"
    ms.author="sidneyh"/>

# Surveiller et gérer un pool de base de données élastique avec Transact-SQL  

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Utilisez les commandes [Create Database (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) et [Alter Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) pour créer et déplacer les bases de données dans et en dehors des pools élastiques. Le pool élastique doit exister avant de pouvoir utiliser ces commandes. Ces commandes affectent uniquement les bases de données. La création de nouveaux pools et le paramétrage des propriétés du pool (telles qu’eDTU min. et max.) ne peuvent pas être modifiés avec les commandes T-SQL.


> [AZURE.NOTE] Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs SQL Database V12. Si vous disposez d’un serveur SQL Database V11, vous pouvez [utiliser PowerShell pour effectuer une mise à niveau vers V12 et créer un pool](sql-database-upgrade-server-portal.md) en une seule étape.


## Créer une base de données dans un pool de base de données élastique
Utilisez la commande CREATE DATABASE avec l’option SERVICE\_OBJECTIVE.

	CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
	-- Create a database named db1 in a pool named S3M100.

Toutes les bases de données d’un pool élastique héritent du niveau de service du pool élastique (De base, Standard, Premium).


## Déplacer une base de données entre les pools élastiques
Utilisez la commande ALTER DATABASE avec MODIFY et définissez l’option SERVICE\_OBJECTIVE sur ELASTIC\_POOL ; définissez le nom sur le nom du pool cible.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
	-- Move the database named db1 to a pool named P1M125  


## Déplacer une base de données dans un pool élastique 
Utilisez la commande ALTER DATABASE avec MODIFY et définissez l’option SERVICE\_OBJECTIVE sur ELASTIC\_POOL ; définissez le nom sur le nom du pool cible.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
	-- Move the database named db1 to a pool named S3100.

## Déplacer une base de données hors d’un pool élastique
Utilisez la commande ALTER DATABASE et définir l’option SERVICE\_OBJECTIVE sur l’un des niveaux de performances (S0, S1, etc.).

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
	-- Changes the database into a stand-alone database with the service objective S1.

## Répertorier les bases de données dans un pool élastique
Utilisez la vue [sys.database\_service \_objectives](https://msdn.microsoft.com/library/mt712619) pour répertorier toutes les bases de données dans un pool élastique. Connectez-vous à la base de données MASTER pour interroger la vue.

>[AZURE.NOTE] Actuellement, service\_objective\_column pour les bases de données dans les pools élastiques renvoie un jeton interne de la chaîne d’objectif de service. Il sera remplacé par la chaîne « ElasticPool ».
>

	SELECT d.name, slo.*  
	FROM sys.databases d 
	JOIN sys.database_service_objectives slo  
	ON d.database_id = slo.database_id
	WHERE elastic_pool_name = 'MyElasticPool'; 

## Surveiller l’utilisation des ressources des pools élastiques

Utilisez la vue [sys.elastic\_pool \_resource \_stats](https://msdn.microsoft.com/library/mt280062.aspx) pour examiner les statistiques d’utilisation des ressources d’un pool élastique sur un serveur logique. Connectez-vous à la base de données MASTER pour interroger la vue.

	SELECT * FROM sys.elastic_pool_resource_stats 
	WHERE elastic_pool_name = 'MyElasticPool'
	ORDER BY end_time DESC;

## Surveiller l’utilisation des ressources d’une base de données dans un pool élastique
Utilisez la vue [sys.dm \_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) ou [sys.resource \_stats](https://msdn.microsoft.com/library/dn269979.aspx) pour examiner les statistiques d’utilisation des ressources d’une base de données dans un pool élastique. Ce processus revient à interroger l’utilisation des ressources de toutes les bases de données uniques.

## Étapes suivantes

Après avoir créé un pool élastique de bases de données, vous pouvez gérer des bases de données élastiques du pool en créant des tâches élastiques. Les tâches élastiques facilitent l'exécution de scripts T-SQL, quel que soit le nombre de bases de données dans le pool. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).

<!---HONumber=AcomDC_0406_2016-->