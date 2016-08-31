<properties
   pageTitle="Surveiller votre charge de travail à l'aide de vues de gestion dynamique | Microsoft Azure"
   description="Comment surveiller votre charge de travail à l'aide de vues de gestion dynamique"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/22/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# Surveiller votre charge de travail à l'aide de vues de gestion dynamique

Cet article décrit comment utiliser les vues de gestion dynamique (DMV) pour surveiller votre charge de travail et analyser l'exécution des requêtes dans Azure SQL Data Warehouse.

## Suivi des connexions

La vue [sys.dm\_pdw\_exec\_sessions][] vous permet de surveiller les connexions à votre base de données Azure SQL Data Warehouse. Cette vue contient les sessions actives, ainsi que l’historique des sessions déconnectées récemment. L’ID de session (session\_id) est la clé principale pour cette vue. Elle est assignée de façon séquentielle pour chaque nouvelle connexion.

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## Surveillance de l’exécution des rêquetes

Pour surveiller l’exécution des requêtes, commencez par [sys.dm\_pdw\_exec\_requests][]. Cette vue contient les requêtes en cours, ainsi que l’historique des requêtes qui se sont terminées récemment. L’ID de requête (request\_id) identifie de manière unique chaque requête. Il s’agit de la clé principale pour cette vue. L’ID de requête (request\_id) est assignée de façon séquentielle pour chaque nouvelle requête. En interrogeant cette table pour un ID de session (session\_id) donné, vous obtenez toutes les requêtes pour une connexion donnée.

>[AZURE.NOTE] Les procédures stockées utilisent plusieurs request\_ids. Les ID de demande seront attribués par ordre séquentiel.

Voici les étapes à suivre pour analyser les heures et les plans d’exécution d’une requête spécifique.

### ÉTAPE 1 : Identifier la requête que vous souhaitez examiner

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;
```

Dans les résultats de la requête ci-dessus, **notez l’ID de la requête** que vous souhaitez examiner.

Les requêtes affichant un état Suspendu sont mises en file d’attente en raison des limites de concurrence, comme indiqué en détail dans la rubrique [Gestion de la concurrence et des charges de travail][]. Ces requêtes apparaîtront également dans la requête sys.dm\_pdw\_waits de type UserConcurrencyResourceType. Les requêtes peuvent également attendre pour d’autres raisons, par exemple des verrous. Si votre requête est en attente d’une ressource, consultez la page [Examen des requêtes en attente de ressources][].

### ÉTAPE 2 : rechercher l’étape la plus longue du plan de requête

Utilisez l’ID de requête pour récupérer une liste des étapes du plan de requête à partir de [sys.dm\_pdw\_request\_steps][]. Recherchez l'étape la plus longue en examinant le temps total écoulé.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Vérifier la colonne *operation\_type* de l’exécution de l’étape de requête longue et notez l**’index des étapes** :

- Passez à l’étape 3a pour les **opérations SQL** : OnOperation, RemoteOperation, ReturnOperation.
- Passez à l’étape 3b pour **les opérations de déplacement des données** : ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### ÉTAPE 3a : rechercher la progression de l’exécution d’une étape SQL

Utilisez l’ID de requête et l’index des étapes pour récupérer des détails à partir de [sys.dm\_pdw\_sql\_requests][], qui contient des informations sur l’exécution de la requête sur chaque instance distribuée de SQL Server.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Si la requête est en cours d’exécution, [DBCC PDW\_SHOWEXECUTIONPLAN][] peut être utilisé pour récupérer le plan estimé de SQL Server pour le plan de mise en cache SQL Serveur de l’étape SQL en cours d’exécution dans une distribution spécifique.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### ÉTAPE 3b : rechercher la progression de l’exécution d’une étape de déplacement des données

Utilisez l’ID de requête et l’index des étapes pour récupérer des informations sur l’étape de déplacement des données en cours d’exécution sur chaque distribution à partir de [sys.dm\_pdw\_dms\_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Vérifiez la colonne *total\_elapsed\_time* pour voir si une distribution particulière prend beaucoup plus de temps que les autres pour le déplacement des données.
- Consultez la colonne *rows\_processed* de la distribution la plus longue pour voir si le nombre de lignes déplacées dans le cadre de cette distribution est nettement plus élevé que d’autres. Dans ce cas, cela peut indiquer un décalage des données sous-jacentes.

Si la requête est en cours d’exécution, [DBCC PDW\_SHOWEXECUTIONPLAN][] peut être utilisé pour récupérer le plan estimé de SQL Server pour le plan de mise en cache SQL Serveur de l’étape SQL en cours d’exécution dans une distribution spécifique.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## Surveillance des requêtes en attente

Si vous constatez que votre requête ne progresse pas car elle attend une ressource, voici une requête qui vous indique toutes les ressources attendues par une requête.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Si la requête attend activement des ressources provenant d'une autre requête, l'état affichera **AcquireResources**. Si la requête possède toutes les ressources requises, l'état sera **Granted**.

## Étapes suivantes
Pour plus d’informations sur Transact-SQL et les vues de gestion dynamique (DMV), consultez la page [Vues système][].  
Pour plus d’informations sur la gestion de SQL Data Warehouse, consultez la page [Vue d’ensemble de la gestion][].  
Pour connaître les meilleures pratiques, voir [Meilleures pratiques relatives à SQL Data Warehouse][]

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md
[Meilleures pratiques relatives à SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Vues système]: ./sql-data-warehouse-reference-tsql-system-views.md
[Gestion de la concurrence et des charges de travail]: ./sql-data-warehouse-develop-concurrency.md
[Examen des requêtes en attente de ressources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0810_2016-->
