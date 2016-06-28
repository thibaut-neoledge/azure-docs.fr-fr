<properties
   pageTitle="Se connecter à Azure SQL Data Warehouse | Microsoft Azure"
   description="Vue d’ensemble de la connexion à Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/20/2016"
   ms.author="sonyama;barbkess"/>

# Se connecter à Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-connect-overview.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Pilotes](sql-data-warehouse-connection-strings.md)

Vue d’ensemble de la connexion à Azure SQL Data Warehouse.

## Découvrir la chaîne de connexion à partir du portail

Votre SQL Data Warehouse est associé à un serveur SQL Azure. Pour vous connecter, vous devez disposer du nom complet du serveur (**nom\_serveur**.database.windows.net*).

Pour rechercher le nom complet du serveur :

1. Accédez au [portail Azure][].
2. Cliquez sur **Bases de données SQL**, puis sur la base de données à laquelle vous souhaitez vous connecter. Cet exemple utilise l’exemple de base de données AdventureWorksDW.
3. Recherchez le nom complet du serveur.

    ![Nom complet du serveur][1]

## Paramètres de connexion
SQL Data Warehouse standardise quelques paramètres pendant la connexion et la création d’objets. Ces paramètres ne peuvent pas être remplacés.

| Paramètre de base de données | Valeur |
| :----------------- | :--------------------------- |
| ANSI\_NULLS | ACTIVÉ |
| QUOTED\_IDENTIFIERS | ACTIVÉ |
| NO\_COUNT | DÉSACTIVÉ |
| DATEFORMAT | mdy |
| DATEFIRST | 7 |
| Classement de base de données | SQL\_Latin1\_General\_CP1\_CI\_AS |

## Sessions et demandes
Une fois qu’une connexion et qu’une session ont été établies, vous êtes prêt à écrire et à soumettre des requêtes à SQL Data Warehouse.

Chaque requête sera représentée par un ou plusieurs identificateurs de demande. Toutes les requêtes soumises sur cette connexion font partie d’une même session et seront donc représentées par un ID de session unique.

Toutefois, étant donné que SQL Data Warehouse est un système MPP (Massively Parallel Processing) distribué, les identificateurs de session et de demande sont tous deux exposés différemment par rapport à SQL Server.

Les sessions et les demandes sont représentées de manière logique par leurs identificateurs respectifs.

| Identificateur | Exemple de valeur |
| :--------- | :------------ |
| ID de la session | SID123456 |
| ID de la demande | QID123456 |

Notez que l’ID de la session présente le préfixe SID (forme courte de « Session ID ») et que les demandes comportent le préfixe QID (forme courte de « Query ID »).

Ces informations vous aideront à identifier vos requêtes lorsque vous surveillerez les performances de ces dernières. Vous pouvez surveiller les performances de vos requêtes en utilisant le [portail Azure] et les vues de gestion dynamique.

Cette requête identifie votre session en cours.

```sql
SELECT SESSION_ID()
;
```

Pour visualiser toutes les requêtes qui sont en cours d’exécution ou qui ont été récemment exécutées dans votre entrepôt de données, vous pouvez utiliser l’exemple ci-après. Cet exemple illustre la création d’une vue, puis son exécution.

```sql
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;

SELECT * FROM dbo.vSessionRequests;
```

## Étapes suivantes

Pour commencer à interroger votre entrepôt de données avec Visual Studio et d’autres applications, consultez [Query with Visual Studio (Soumettre des requêtes avec Visual Studio)][].


<!--Arcticles-->

[Query with Visual Studio (Soumettre des requêtes avec Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[portail Azure]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0622_2016-->