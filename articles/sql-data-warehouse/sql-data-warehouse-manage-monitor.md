<properties
   pageTitle="Surveiller votre charge de travail à l'aide de vues de gestion dynamique | Microsoft Azure"
	description="Comment surveiller votre charge de travail à l'aide de vues de gestion dynamique"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="sahaj08"
	manager="barbkess"
	editor=""/>

<tags
   ms.service="sql-data-warehouse"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-services"
	ms.date="08/06/2015"
	ms.author="sahajs"/>

# Surveiller votre charge de travail à l'aide de vues de gestion dynamique

Cet article décrit comment utiliser les vues de gestion dynamique (DMV) pour surveiller votre charge de travail et analyser l'exécution des requêtes dans Azure SQL Data Warehouse.




## Autorisations

Dans SQL Data Warehouse, l’interrogation d’une vue de gestion dynamique nécessite des autorisations **VIEW DATABASE STATE**. L’autorisation **VIEW DATABASE STATE** renvoie des informations sur tous les objets de la base de données active. Pour accorder l’autorisation **VIEW DATABASE STATE** à un utilisateur de base de données spécifique, exécutez la requête suivante :

```

GRANT VIEW DATABASE STATE TO database_user;

```




## Suivi des connexions

Vous pouvez utiliser la vue *sys.dm\_pdw\_nodes\_exec\_connections* pour récupérer des informations sur les connexions établies avec votre base de données Azure SQL Data Warehouse. En outre, la vue *sys.dm\_exec\_sessions* sert à récupérer les informations sur toutes les connexions utilisateur actives.

```

SELECT * FROM sys.dm_pdw_nodes_exec_connections;
SELECT * FROM sys.dm_pdw_nodes_exec_sessions;

```


Utilisez la requête suivante pour récupérer les informations sur la connexion actuelle.

```

SELECT * 
FROM sys.dm_pdw_nodes_exec_connections AS c 
   JOIN sys.dm_pdw_nodes_exec_sessions AS s 
   ON c.session_id = s.session_id 
WHERE c.session_id = @@SPID;

```





## Analyse de l'exécution des requêtes
Il peut arriver que votre requête ne se termine pas ou s’exécute plus lentement que prévu. Dans ces cas de figure, vous pouvez utiliser la procédure suivante pour collecter des données et détecter l’origine du problème.



### ÉTAPE 1: rechercher la requête à analyser

```

-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the longest running queries
SELECT * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;

```

Enregistrez l'ID de demande de la requête.


  
### ÉTAPE 2: vérifier si la requête est en attente de ressources

```

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


Les résultats de la requête ci-dessus affiche l'état d'attente de votre demande.

- Si la requête est en attente de ressources de la part d'une autre requête, l'état sera **AcquireResources**.
- Si la requête possède toutes les ressources requises et n'est pas en attente, l'état sera **Granted**. Dans ce cas, passez à l’analyse des étapes de la requête.




### ÉTAPE 3 : rechercher l'étape la plus longue de la requête

Utilisez l'ID de demande pour récupérer une liste de toutes les étapes de la requête distribuée. Recherchez l'étape la plus longue en examinant le temps total écoulé.

```

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.
 
SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;

```

Enregistrez l'Index d'étape de l’étape la plus longue.

Vérifier la colonne *type\_opération* de l’exécution de l’étape de requête longue :

- Passez à l’étape 4a pour les **opérations SQL** : OnOperation, RemoteOperation, ReturnOperation.
- Passez à l’étape 4b pour **les opérations de déplacement des données** : ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.




### ÉTAPE 4a : rechercher la progression de l'exécution d'une étape SQL

Utilisez l'ID de demande et de l'Index de l'étape pour récupérer des informations sur la distribution de requête SQL Server dans le cadre de l'étape de la requête SQL. Enregistrez l'ID de nœud et le SPID.

```

-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;

```


Utilisez la requête suivante pour récupérer le plan d'exécution SQL Server pour l'étape SQL sur un nœud particulier.

```

-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node. 
-- Replace node_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```



### ÉTAPE 4b : rechercher la progression de l'exécution d'une étape de déplacement des données

Utilisez l'ID de demande et l'Index de l'étape pour récupérer des informations sur l'étape de déplacement de données en cours d'exécution sur chaque distribution.

```

-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.
 
SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- Vérifiez la colonne *total\_elapsed\_time* pour voir si une distribution particuière prend beaucoup plus de temps que les autres pour le déplacement des données. 
- Consultez la colonne *rows\_processed* de la distribution la plus longue pour voir si le nombre de lignes déplacées dans le cadre de cette distribution est nettement plus élevé que d’autres. Cela indique que votre requête présente une inclinaison de données.





## Analyse de l’inclinaison de données

```

-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");

```


Le résultat de cette requête affiche le nombre de lignes de la table stockées dans chacune des 60 distributions de votre base de données. Pour des performances optimales, les lignes de votre table distribuée doivent être partagées uniformément entre toutes les distributions. Pour en savoir plus, consultez la page [Conception de table][].



## Étapes suivantes
Pour plus d’informations sur la gestion de SQL Data Warehouse, consultez la page [Gestion de la vue d’ensemble][].

<!--Image references-->

<!--Article references-->
[Gestion de la vue d’ensemble]: sql-data-warehouse-overview-manage.md
[Conception de table]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!---HONumber=August15_HO9-->