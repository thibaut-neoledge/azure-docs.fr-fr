<properties
   pageTitle="Surveiller votre charge de travail à l'aide de vues de gestion dynamique | Microsoft Azure"
   description="Comment surveiller votre charge de travail à l'aide de vues de gestion dynamique"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyama"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/29/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# Surveiller votre charge de travail à l'aide de vues de gestion dynamique

Cet article décrit comment utiliser les vues de gestion dynamique (DMV) pour surveiller votre charge de travail et analyser l'exécution des requêtes dans Azure SQL Data Warehouse.

## Suivi des connexions

La vue [sys.dm\_pdw\_exec\_sessions][] vous permet de surveiller les connexions à votre base de données Azure SQL Data Warehouse. Cette vue contient les sessions actives, ainsi que l’historique des sessions déconnectées récemment. L’ID de session (session\_id) est la clé principale pour cette vue. Elle est assignée de façon séquentielle pour chaque nouvelle connexion.

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## Analyse de l'exécution des requêtes
Pour surveiller l’exécution des requêtes, commencez par [sys.dm\_pdw\_exec\_requests][]. Cette vue contient les requêtes en cours, ainsi que l’historique des requêtes qui se sont terminées récemment. L’ID de requête (request\_id) identifie de manière unique chaque requête. Il s’agit de la clé principale pour cette vue. L’ID de requête (request\_id) est assignée de façon séquentielle pour chaque nouvelle requête. En interrogeant cette table pour un ID de session (session\_id) donné, vous obtenez toutes les requêtes pour une connexion donnée.

Si vous souhaitez enquêter sur l’exécution d’une requête particulière, voici la procédure courante à suivre.

### ÉTAPE 1: rechercher la requête à analyser

```sql
-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the 10 longest running queries
SELECT TOP 10 * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;
```

Notez l’ID de la requête que vous souhaitez examiner.

### ÉTAPE 2: vérifier si la requête est en attente de ressources

```sql
-- Find waiting tasks for your session.
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,  
      waits.object_type,
      waits.object_name,  
      waits.state  
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID33188'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Les résultats de la requête ci-dessus affichent l’état d’attente de votre requête.

- Si la requête est en attente de ressources de la part d'une autre requête, l'état sera **AcquireResources**.
- Si la requête possède toutes les ressources requises et n'est pas en attente, l'état sera **Granted**. Dans ce cas, passez à l’analyse des étapes de la requête.

### ÉTAPE 3 : rechercher l’étape la plus longue du plan de requête

Utilisez l’ID de requête pour récupérer une liste des étapes du plan de requête à partir de [sys.dm\_pdw\_request\_steps][]. Recherchez l'étape la plus longue en examinant le temps total écoulé.

```sql

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;
```

Enregistrez l'Index d'étape de l’étape la plus longue.

Vérifier la colonne *operation\_type* de l’exécution de l’étape de requête longue :

- Passez à l’étape 4a pour les **opérations SQL** : OnOperation, RemoteOperation, ReturnOperation.
- Passez à l’étape 4b pour **les opérations de déplacement des données** : ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### ÉTAPE 4a : rechercher la progression de l'exécution d'une étape SQL

Utilisez l’ID de requête et l’index des étapes pour récupérer des informations à partir de [sys.dm\_pdw\_sql\_requests][] qui contient des détails sur l’exécution de la requête sur les instances distribuées de SQL Server. Notez l’ID de distribution et le SPID si la requête est en cours d’exécution et si vous souhaitez obtenir le plan à partir de la distribution de SQL Server.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;
```


Si la requête est en cours d’exécution, [DBCC PDW\_SHOWEXECUTIONPLAN][] peut être utilisé pour récupérer le plan d’exécution de SQL Server pour l’étape SQL en cours d’exécution pour une distribution spécifique.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```

### ÉTAPE 4b : rechercher la progression de l'exécution d'une étape de déplacement des données

Utilisez l’ID de requête et l’index des étapes pour récupérer des informations sur l’étape de déplacement des données en cours d’exécution sur chaque distribution à partir de [sys.dm\_pdw\_dms\_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- Vérifiez la colonne *total\_elapsed\_time* pour voir si une distribution particulière prend beaucoup plus de temps que les autres pour le déplacement des données.
- Consultez la colonne *rows\_processed* de la distribution la plus longue pour voir si le nombre de lignes déplacées dans le cadre de cette distribution est nettement plus élevé que d’autres. Dans ce cas, cela peut indiquer un décalage des données sous-jacentes.

## Analyse de l’inclinaison de données

Utilisez [DBCC PDW\_SHOWSPACEUSED][] pour rechercher l’espace utilisé par une table.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");
```

Le résultat de cette requête affiche le nombre de lignes de la table stockées dans chacune des 60 distributions de votre base de données. Pour des performances optimales, les lignes de votre table distribuée doivent être partagées uniformément entre toutes les distributions.

Pour en savoir plus, consultez la page [Conception de table][].

## Étapes suivantes
Pour plus d’informations sur Transact-SQL et les vues de gestion dynamique (DMV), consultez la page [Vue d’ensemble de référence][].
Pour plus d’informations sur la gestion de SQL Data Warehouse, consultez la page [Vue d’ensemble de la gestion][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la gestion]: sql-data-warehouse-overview-manage.md
[Conception de table]: sql-data-warehouse-develop-table-design.md
[Vue d’ensemble de référence]: sql-data-warehouse-overview-reference.md
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW\_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!--MSDN references-->

<!---HONumber=AcomDC_0330_2016-->

