<properties
   pageTitle="Surveiller votre charge de travail à l'aide de vues de gestion dynamique | Microsoft Azure"
   description="Comment surveiller votre charge de travail à l'aide de vues de gestion dynamique"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/31/2016"
   ms.author="barbkess"/>


# <a name="monitor-your-workload-using-dmvs"></a>Surveiller votre charge de travail à l'aide de vues de gestion dynamique

Cet article décrit comment utiliser les vues de gestion dynamique (DMV) pour surveiller votre charge de travail et analyser l'exécution des requêtes dans Azure SQL Data Warehouse.

## <a name="permissions"></a>Autorisations

Pour interroger les vues de gestion dynamique (DMV) de cet article, vous avez besoin de l’autorisation VIEW DATABASE STATE ou CONTROL. L’autorisation généralement accordée est VIEW DATABASE STATE car elle est beaucoup plus restrictive.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Suivi des connexions

Toutes les connexions à SQL Data Warehouse sont enregistrées dans [sys.dm_pdw_exec_sessions][].  Cette DMV contient les 10 000 dernières connexions.  L’ID de session (session_id) est la clé principale. Elle est affectée de façon séquentielle pour chaque nouvelle connexion.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Surveillance de l’exécution des rêquetes

Toutes les requêtes exécutées sur SQL Data Warehouse sont enregistrées dans [sys.dm_pdw_exec_requests][].  Cette DMV contient les 10 000 dernières requêtes exécutées.  L’ID de requête (request_id) identifie de manière unique chaque requête. Il s’agit de la clé principale pour cette DMV.  L’ID de requête (request_id) est affecté de façon séquentielle pour chaque nouvelle requête et a le préfixe QID, qui signifie ID de requête.  En interrogeant cette DMV pour un ID de session (session_id) donné, vous obtenez toutes les requêtes pour une connexion donnée.

>[AZURE.NOTE] Les procédures stockées utilisent plusieurs ID de requête.  Les ID de requête sont affectés dans un ordre séquentiel. 

Voici les étapes à suivre pour analyser les heures et les plans d’exécution d’une requête spécifique.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>ÉTAPE 1 : Identifier la requête que vous souhaitez examiner

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

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Dans les résultats de requête précédents, **notez l’ID de la requête** que vous souhaitez examiner.

Les requêtes ayant l’état **Interrompu** sont mises en file d’attente en raison des limites de concurrence. Ces requêtes apparaissent également dans la requête sys.dm_pdw_waits de type UserConcurrencyResourceType. Référez-vous à [Gestion de la concurrence et des charges de travail][] pour en savoir plus sur les limites de concurrence. Les requêtes peuvent également attendre d’autres raisons, par exemple des verrouillages d’objets.  Si votre requête est en attente d’une ressource, consultez la rubrique [Examen des requêtes en attente de ressources][] plus loin dans cet article.

Pour simplifier la recherche d’une requête dans la table sys.dm_pdw_exec_requests, utilisez [LABEL][] pour affecter un commentaire à votre requête qui peut être recherché dans la vue sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>ÉTAPE 2 : examiner le plan de requête

Utilisez l’ID de requête pour récupérer le plan SQL distribué (DSQL) de la requête dans [sys.dm_pdw_request_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Lorsqu’un plan DSQL prend plus de temps que prévu, la cause peut être un plan complexe avec de nombreuses étapes DSQL ou une seule étape chronophage.  Si le plan comprend de nombreuses étapes avec plusieurs opérations de déplacement, envisagez d’optimiser vos distributions de table pour réduire le déplacement des données. L’article [Table distribution][] explique pourquoi les données doivent être déplacées pour résoudre une requête et explique certaines stratégies de distribution permettant de réduire le déplacement des données.

Pour examiner les détails d’une seule étape, vérifiez la colonne *operation_type* de l’étape de la requête longue et notez **l’index de l’étape** :

- Passez à l’étape 3a pour les **opérations SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Passez à l’étape 3b pour **les opérations de déplacement des données**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>ÉTAPE 3a : examiner SQL dans les bases de données distribuées

Utilisez l’ID de requête et l’index de l’étape pour récupérer des détails dans [sys.dm_pdw_sql_requests][], qui contient des informations sur l’exécution de l’étape de la requête sur toutes les bases de données distribuées.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quand l’étape de la requête est en cours d’exécution, [DBCC PDW_SHOWEXECUTIONPLAN][] peut être utilisé pour récupérer le plan estimé de SQL Server dans le cache du plan SQL Server pour l’étape en cours d’exécution dans une distribution particulière.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>ÉTAPE 3b : examiner le déplacement des données sur les bases de données distribuées

Utilisez l’ID de requête et l’index de l’étape pour récupérer des informations sur une étape de déplacement des données en cours d’exécution sur chaque distribution dans [sys.dm_pdw_dms_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Vérifiez la colonne *total_elapsed_time* pour voir si une distribution particulière prend un temps significativement plus important que les autres pour le déplacement des données.
- Pour la distribution longue, consultez la colonne *rows_processed* pour voir si le nombre de lignes déplacées dans le cadre de cette distribution est nettement plus élevé que les autres. Dans ce cas, cela peut indiquer un décalage des données sous-jacentes.

Si la requête est en cours d’exécution, [DBCC PDW_SHOWEXECUTIONPLAN][] peut être utilisé pour récupérer le plan estimé de SQL Server auprès du cache de plans SQL Serveur pour l’étape SQL en cours d’exécution dans une distribution particulière.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## <a name="monitor-waiting-queries"></a>Surveillance des requêtes en attente

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

Si la requête attend activement des ressources provenant d'une autre requête, l'état affichera **AcquireResources**.  Si la requête possède toutes les ressources requises, l'état sera **Granted**.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les vues de gestion dynamique, consultez [Vues système][].
Pour plus d’informations sur les bonnes pratiques, consultez [Bonnes pratiques pour SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md
[Meilleures pratiques relatives à SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Vues système]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Gestion de la concurrence et des charges de travail]: ./sql-data-warehouse-develop-concurrency.md
[Examen des requêtes en attente de ressources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx



<!--HONumber=Oct16_HO2-->


