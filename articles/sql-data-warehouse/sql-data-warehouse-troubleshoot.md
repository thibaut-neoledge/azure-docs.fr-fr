<properties
   pageTitle="Dépannage | Microsoft Azure"
   description="Dépannage de SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;sonyama;barbkess"/>

# Résolution de problèmes
La rubrique suivante répertorie certains des problèmes les plus courants auxquels les clients sont confrontés avec Azure SQL Data Warehouse.

## Connectivité
La connexion à Azure SQL Data Warehouse peut échouer pour plusieurs raisons :

- Les règles du pare-feu ne sont pas définies
- Les outils/protocoles ne sont pas pris en charge

### Règles de pare-feu
Les bases de données SQL Azure sont protégées par des pare-feux au niveau du serveur et de la base de données pour s'assurer que seules les adresses IP connues peuvent accéder aux bases de données. Les pare-feux sont sécurisés par défaut, ce qui signifie que vous devez autoriser l'accès de votre adresse IP avant de vous connecter.

Pour configurer votre pare-feu pour l'accès, suivez les étapes décrites dans la section [Configurer l'accès au pare-feu du serveur pour l'adresse IP de votre client](sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip) de la page [Mise en service](sql-data-warehouse-get-started-provision.md).

### Les outils/protocoles ne sont pas pris en charge
SQL Data Warehouse prend en charge [Visual Studio 2013/2015](sql-data-warehouse-get-started-connect.md) comme environnements de développement et [SQL Server Native Client 10/11 (ODBC)](https://msdn.microsoft.com/library/ms131415.aspx) pour la connectivité client.

Consultez nos pages sur la [connexion](sql-data-warehouse-get-started-connect.md) pour en savoir plus.

## Performances des requêtes
SQL Data Warehouse utilise des constructions SQL Server communes pour l'exécution de requêtes, notamment les statistiques. Les [statistiques](sql-data-warehouse-develop-statistics.md) sont des objets contenant des informations sur la plage et la fréquence de valeurs dans une colonne de base de données. Le moteur de requête utilise ces statistiques pour optimiser l'exécution des requêtes et améliorer leurs performances. Vous pouvez utiliser la requête suivante pour déterminer la dernière fois que vos statistiques ont été mises à jour.

```
SELECT
	sm.[name]								    AS [schema_name],
	tb.[name]								    AS [table_name],
	co.[name]									AS [stats_column_name],
	st.[name]									AS [stats_name],
	STATS_DATE(st.[object_id],st.[stats_id])	AS [stats_last_updated_date]
FROM
	sys.objects				AS ob
	JOIN sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
	JOIN sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND	st.[object_id]		= sc.[object_id]
	JOIN sys.columns		AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
	JOIN sys.types           AS ty	ON	co.[user_type_id]	= ty.[user_type_id]
	JOIN sys.tables          AS tb	ON	co.[object_id]		= tb.[object_id]
	JOIN sys.schemas         AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE
	1=1
	AND st.[user_created] = 1;
```

Consultez notre pages [Statistiques](sql-data-warehouse-develop-statistics.md) pour en savoir plus.

## Concepts clés de performances

Consultez les articles suivants afin de mieux comprendre certains concepts supplémentaires clés de performance et de mise à l’échelle :

- [performances et mise à l’échelle][]
- [modèle concurrentiel][]
- [tables de conception][]
- [associer une clé de distribution par hachage à votre table][]
- [statistiques pour améliorer les performances][]

## Étapes suivantes
Consultez la [vue d’ensemble sur le développement][] afin de bénéficier de recommandations sur le développement d’une solution SQL Data Warehouse.

<!--Image references-->

<!--Article references-->

[performances et mise à l’échelle]: sql-data-warehouse-performance-scale.md
[modèle concurrentiel]: sql-data-warehouse-develop-concurrency.md
[tables de conception]: sql-data-warehouse-develop-table-design.md
[associer une clé de distribution par hachage à votre table]: sql-data-warehouse-develop-hash-distribution-key
[statistiques pour améliorer les performances]: sql-data-warehouse-develop-statistics.md
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0309_2016-->