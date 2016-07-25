<properties
   pageTitle="Gestion de la concurrence et des charges de travail dans SQL Data Warehouse | Microsoft Azure"
   description="Décrit la gestion de la concurrence et des charges de travail dans Azure SQL Data Warehouse pour le développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gestion de la concurrence et des charges de travail dans SQL Data Warehouse

Pour offrir des performances prévisibles à grande échelle, SQL Data Warehouse permet aux utilisateurs de contrôler les niveaux de concurrence, ainsi que les allocations de ressources telles que la mémoire et la définition des priorités du processeur. Cet article vous présente les concepts de gestion de la concurrence et de gestion des charges de travail, en expliquant comment ces deux fonctionnalités ont été implémentées, ainsi que la procédure à suivre pour les contrôler dans votre entrepôt de données. La gestion de charge de travail SQL Data Warehouse a pour but de vous aider à prendre en charge des environnements multi-utilisateurs. Elle n’est pas destinée aux charges de travail mutualisées.

## Limites de concurrence

SQL Data Warehouse autorise jusqu’à 1024 connexions simultanées. Les 1024 connexions peuvent soumettre des requêtes simultanément. Toutefois, pour optimiser le débit, SQL Data Warehouse peut mettre certaines requêtes en file d’attente pour s’assurer que chaque requête reçoit une allocation de mémoire minimale. La mise en file d’attente se produit au moment de l’exécution de la requête quand des limites de concurrence sont atteintes. Cela permet à SQL Data Warehouse d’augmenter le débit total en veillant à ce que les requêtes actives puissent accéder aux ressources de mémoire essentielles.

Les limites de concurrence sont régies par deux concepts, les **requêtes simultanées** et les **emplacements de concurrence**. Pour qu’une requête s’exécute, elle doit s’exécuter à la fois sous la limite de concurrence et dans les limites de l’allocation d’emplacement de concurrence.

- Les **requêtes simultanées** correspondent simplement au nombre de requêtes s’exécutant simultanément. SQL Data Warehouse prend en charge jusqu’à 32 **requêtes simultanées** sur les entrepôts de données volumineux, DW1000 et versions ultérieures. Toutefois, étant donné que le nombre de requêtes simultanées varie en fonction du nombre de DWU (Data Warehouse Unit), nous avons créé le tableau ci-dessous pour montrer les limitations par DWU.
- Le concept d**’emplacement de concurrence** est plus dynamique. Chaque requête concurrente consomme un ou plusieurs emplacements de concurrence. Le nombre exact d’emplacements consommés par une requête dépend de la taille de votre SQL Data Warehouse et de la [classe de ressource](#resource-classes) de la requête.

Le tableau ci-dessous décrit les limites pour les requêtes simultanées et les emplacements de concurrence.

### Limites de concurrence

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| Nombre maximal de requêtes concurrentes | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| Nombre maximal d’emplacements de concurrence | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 120 | 240 |

Quand l’un de ces seuils est atteint, les nouvelles requêtes sont mises en file d’attente. Les requêtes en file d’attente sont exécutées d’après le principe du « premier entré premier sorti » à mesure que les requêtes sont exécutées et que le nombre de requêtes et d’emplacements passe au-dessous de ces limites.

> [AZURE.NOTE]  Les requêtes SELECT s’exécutant exclusivement sur les vues de gestion dynamique (DMV) ou les affichages catalogue ne sont **pas** régies par les classes de ressources. Cela permet aux utilisateurs de surveiller le système même quand tous les emplacements de concurrence sont utilisés.

## Classes de ressources

Les classes de ressources constituent une partie essentielle de la gestion des charges de travail SQL Data Warehouse, car elles permettent d’allouer davantage de mémoire et/ou de cycles de processeur aux requêtes exécutées par un utilisateur donné. Il existe quatre classes de ressources, chacune sous la forme d’un **rôle de base de données**. Les quatre classes de ressources sont **smallrc, mediumrc, largerc et xlargerc**. Les utilisateurs de smallrc bénéficient d’une plus petite quantité de mémoire, d’où une concurrence accrue. En revanche, les utilisateurs affectés à xlargerc disposent de grandes quantités de mémoire. Ainsi, une plus faible proportion de ces requêtes sont autorisées à s’exécuter simultanément.

Il existe quelques types de requêtes qui ne tirent pas profit d’une allocation de mémoire supérieure et qui ignorent donc leur allocation de classe de ressources et s’exécutent à la place dans une classe de ressources plus petite. Le fait de forcer ces requêtes à toujours s’exécuter dans la petite classe de ressources leur permet de s’exécuter quand les emplacements de concurrence sont sous pression et les empêche d’utiliser plus d’emplacements que nécessaire. Ces [exceptions de classes de ressources](#resource-class-exceptions) sont traitées plus loin dans cet article.

Par défaut, chaque utilisateur appartient à la petite classe de ressources, smallrc. La procédure `sp_addrolemember` sert à augmenter la classe de ressources, et `sp_droprolemember` à la réduire. Par exemple, cette commande augmente la classe de ressources de loaduser à largerc :

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Une bonne pratique consiste à créer des utilisateurs qui sont affectés en permanence à une classe de ressources, plutôt que de modifier la classe de ressources d’un utilisateur. Par exemple, les charges des tables columnstore en cluster créent des index de qualité supérieure quand davantage de mémoire leur est allouée. Pour vous assurer que les charges ont accès à une mémoire plus élevée, créez un utilisateur spécifiquement pour le chargement des données et affectez-le définitivement à une classe de ressources supérieure.

Voici quelques autres détails concernant la classe de ressources :

- L’autorisation `ALTER ROLE` est obligatoire pour modifier la classe de ressources d’un utilisateur.
- Vous pouvez ajouter un utilisateur à l’une ou plusieurs des classes de ressources plus élevées, mais les utilisateurs prennent les attributs de la classe de ressource la plus élevée à laquelle ils sont attribués. Autrement dit, si un utilisateur est affecté à la fois à mediumrc et largerc, la classe de ressource supérieure (largerc) est celle qui sera honorée.
- Vous ne pouvez pas modifier la classe de ressources de l’administrateur système.
 
Vous trouverez plus de détails et des exemples de création d’utilisateurs et de leur affectation à des classes de ressources dans la section [Gestion des utilisateurs](#Managing-users) à la fin de cet article.

## Allocation de mémoire

L’augmentation de la classe de ressources d’un utilisateur présente des avantages et des inconvénients. Si vous augmentez la classe de ressources d’un utilisateur, ses requêtes ont accès à davantage de mémoire et peuvent s’exécuter plus rapidement, mais cela réduit également le nombre de requêtes simultanées qui peuvent s’exécuter. Il s’agit d’un compromis entre allouer de grandes quantités de mémoire à une seule requête et permettre l’exécution d’autres requêtes simultanées qui nécessitent également des allocations de mémoire. Si un utilisateur dispose de davantage de mémoire pour une requête, les autres utilisateurs auront moins de mémoire disponible pour exécuter les leurs.

Le tableau suivant mappe la mémoire allouée à chaque distribution par DWU et classe de ressources. Dans SQL Data Warehouse, il existe 60 distributions par base de données. Ainsi, une requête en cours d’exécution sur un DW2000 dans la classe de ressources xlarge a accès à 6400 Mo dans chacune des 60 bases de données.

### Allocations de mémoire par distribution (Mo)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| smallrc | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| mediumrc | 100 | 200 | 200 | 400 | 400 | 400 | 800 | 800 | 800 | 1 600 | 1 600 | 3 200 |
| largerc | 200 | 400 | 400 | 800 | 800 | 800 | 1 600 | 1 600 | 1 600 | 3 200 | 3 200 | 6 400 |
| xlargerc | 400 | 800 | 800 | 1 600 | 1 600 | 1 600 | 3 200 | 3 200 | 3 200 | 6 400 | 6 400 | 12 800 |


Avec le même exemple ci-dessus, à l’échelle du système 375 Go de mémoire totale (6400 Mo * 60 distributions / 1024 pour convertir en Go) sont allouées à une requête en cours d’exécution sur un DW2000 dans la classe de ressources xlarge.

### Allocations de mémoire à l’échelle du système (Go)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
|smallrc | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 |
|mediumrc | 6 | 12 | 12 | 23 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 188 |
|largerc | 12 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 375 |
|xlargerc | 23 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 188 | 375 | 375 | 750 |

## Consommation des emplacements de concurrence

Comme mentionné ci-dessus, plus la ressource de classes est élevée, plus la mémoire allouée est élevée. La mémoire étant une ressource fixe, plus la mémoire allouée par requête est élevée, moins il est possible de prendre en charge de concurrence. Le tableau suivant réitère le nombre d’emplacements de concurrence disponibles par DWU, ainsi que les emplacements consommés par chaque classe de ressources.

### Allocation et consommation des emplacements de concurrence

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :---------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| **Allocation** | | | | | | | | | | | | |
| Nombre maximal de requêtes concurrentes | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| Nombre maximal d’emplacements de concurrence | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 80 | 80 |
| **Consommation des emplacements** | | | | | | | | | | | | |
| smallrc | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 32 |
| largerc | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 64 |
| xlargerc | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 | 64 | 128 |

D’après ce tableau, nous constatons qu’un SQL Data Warehouse exécuté en tant que DW100 autorise soit quatre requêtes smallrc simultanées, soit deux requêtes largerc simultanées.

## Importance de la requête

Il existe en tout huit groupes de charges de travail qui contrôlent le comportement des classes de ressources. Toutefois, seuls quatre des huit groupes de charges de travail sont utilisés à une DWU donnée. Cela est logique, car chaque groupe de charges de travail est affecté à smallrc, mediumrc, largerc ou xlargerc. Il est important de bien comprendre ces groupes de charges de travail, car certains sont affectés d’une **IMPORTANCE** plus élevée. L’importance est utilisée pour la planification du processeur. Les requêtes exécutées avec une importance élevée obtiennent trois fois plus de cycles processeur que celles exécutées avec une importance moyenne. Ainsi, les mappages d’emplacements de concurrence déterminent également l’importance du processeur. Quand une requête utilise 16 emplacements ou plus, elle s’exécute avec une importance élevée.

Vous trouverez ci-dessous les mappages d’importance pour chaque groupe de charges de travail.

| Groupes de charges de travail | Mappage d’emplacement de concurrence | Mappage d’importance |
| :------------------  | :----------------------: | :----------------- |
| SloDWGroupC00 | 1 | Moyenne |
| SloDWGroupC01 | 2 | Moyenne |
| SloDWGroupC02 | 4 | Moyenne |
| SloDWGroupC03 | 8 | Moyenne |
| SloDWGroupC04 | 16 | Élevé |
| SloDWGroupC05 | 32 | Élevé |
| SloDWGroupC06 | 64 | Élevé |
| SloDWGroupC07 | 128 | Élevé |

Pour un SQL Data Warehouse DW500, les groupes de charges de travail actifs sont mappés aux classes de ressources comme suit.

| Classe de ressources | Groupe de charges de travail | Emplacements de concurrence utilisés | Importance |
| :--------------- | :------------- | :--------------------:   | :--------- |
| smallrc | SloDWGroupC00 | 1 | Moyenne |
| mediumrc | SloDWGroupC02 | 4 | Moyenne |
| largerc | SloDWGroupC03 | 8 | Moyenne |
| xlargerc | SloDWGroupC04 | 16 | Élevé |


La requête DMV suivante permet d’examiner en détail les différences d’allocation des ressources mémoire du point de vue du gouverneur de ressources, ou d’analyser l’utilisation active et historique des groupes de charges de travail lors de la résolution des problèmes :

```sql
WITH rg
AS
(   SELECT  
     pn.name						AS node_name
    ,pn.[type]						AS node_type
    ,pn.pdw_node_id					AS node_id
    ,rp.name						AS pool_name
    ,rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,wg.name						AS group_name
    ,wg.importance					AS group_importance
    ,wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,wg.max_dop						AS group_max_dop
    ,wg.effective_max_dop				AS group_effective_max_dop
    ,wg.total_request_count				AS group_total_request_count
    ,wg.total_queued_request_count			AS group_total_queued_request_count
    ,wg.active_request_count				AS group_active_request_count
    ,wg.queued_request_count				AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
    	    AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn	
            ON	wg.pdw_node_id	= pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT	pool_name
,		pool_max_mem_MB
,		group_name
,		group_importance
,		(pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,		node_name
,		node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM	rg
ORDER BY
	node_name
,	group_request_max_memory_grant_pcnt
,	group_importance
;
```

## Exceptions de classes de ressources

La plupart des requêtes honorent des classes de ressources, mais il existe quelques exceptions. Cela se produit généralement quand les ressources nécessaires pour accomplir l’action sont faibles. Autrement dit, les exceptions sont généralement des cas où une requête n’utilisera jamais la mémoire plus élevée allouée par les classes de ressources supérieures. Dans ces cas-là, la classe de ressources par défaut ou petite (smallrc) est toujours utilisée, quelle que soit la classe de ressources affectée à l’utilisateur. Par exemple, `CREATE LOGIN` s’exécute toujours dans smallrc. Les ressources nécessaires pour accomplir cette opération sont très faibles. Par conséquent, il serait inutile d'inclure la requête dans le modèle d'emplacement de concurrence. Il serait contre-productif de pré-allouer de grandes quantités de mémoire pour cette action. En excluant `CREATE LOGIN` du modèle d'emplacement de concurrence, SQL Data Warehouse peut être beaucoup plus efficace.

Les instructions suivantes ne respectent **pas** les classes de ressource :

- CREATE ou DROP TABLE
- ALTER TABLE ... SWITCH, SPLIT ou MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE, UPDATE ou DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE, ALTER ou DROP USER
- CREATE, ALTER ou DROP PROCEDURE
- CREATE ou DROP VIEW
- INSERT VALUES
- SELECT à partir des affichages système et des DMV
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

### Requêtes qui honorent les limites de concurrence

Il est important de noter que la majorité des requêtes utilisateur sont susceptibles d'être régies par les classes de ressources. La règle générale est que la charge de travail de la requête active doit respecter les seuils de requêtes simultanées et d’emplacement de concurrence, sauf si elle a été spécifiquement exclue par la plateforme. En tant qu'utilisateur final, vous ne pouvez pas choisir d'exclure une requête du modèle d'emplacement de concurrence. Dès que l’un de ces seuils est dépassé, les requêtes sont placées en file d’attente. Les requêtes mises en file d’attente sont alors traitées dans l’ordre de priorité suivi par l’heure de la requête.

Les instructions suivantes **honorent** les classes de ressources :

- INSERT-SELECT
- UPDATE
- SUPPRIMER
- SELECT (lors de l’interrogation des tables d’utilisateur)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT
- Chargement de données
- Opérations de déplacement de données effectuées par le Service le déplacement des données (DMS)


## Gestion des utilisateurs

1. **Créer une connexion :** ouvrez une connexion à la base de données **master** de votre SQL Data Warehouse et exécutez les commandes suivantes.
	
	```sql
	CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
	CREATE USER newperson for LOGIN newperson;
	```

	> [AZURE.NOTE] Il est judicieux de créer des utilisateurs pour les connexions dans la base de données master à la fois dans Azure SQL Database et Azure SQL Data Warehouse. Deux rôles de serveur sont disponibles à ce niveau et nécessitent que la connexion ait un utilisateur dans la base de données MASTER afin d’accorder l’appartenance. Il s’agit des rôles `Loginmanager` et `dbmanager`. Dans la base de données SQL Azure et SQL Data Warehouse, ces rôles octroient des droits de gestion des connexions et de création des bases de données. Ce n’est pas le cas de SQL Server. Pour plus d’informations, consultez l’article [Authentification et autorisation de base de données SQL : octroi de l’accès][].

2. **Créer un compte d’utilisateur :** ouvrez une connexion à la **base de données SQL Data Warehouse** et exécutez la commande suivante.

	```sql
	CREATE USER newperson FOR LOGIN newperson;
	```

3. **Accorder des autorisations :** l’exemple indiqué ci-dessous accorde `CONTROL` sur la base de données SQL Data Warehouse. Au niveau de la base de données, `CONTROL` est l’équivalent de db\_owner dans SQL Server.

	```sql
	GRANT CONTROL ON DATABASE::MySQLDW to newperson;
	```

4. **Augmenter la classe de ressources :** pour ajouter un utilisateur à un rôle de gestion des charges de travail plus élevé, exécutez la requête suivante.

	```sql
	EXEC sp_addrolemember 'largerc', 'newperson'
	```

5. **Diminuer la classe de ressources :** pour supprimer un utilisateur d’un rôle de gestion des charges de travail, exécutez la requête suivante.

	```sql
	EXEC sp_droprolemember 'largerc', 'newperson';
	```

	> [AZURE.NOTE] Il n’est pas possible de supprimer un utilisateur dans la classe smallrc.

## Détection des requêtes en file d’attente et autres vues de gestion dynamique

Vous pouvez utiliser la DMV `sys.dm_pdw_exec_requests` pour identifier les requêtes en attente dans une file d’attente de concurrence.

```sql
SELECT 	 r.[request_id]				 AS Request_ID
	,r.[status]				 AS Request_Status
	,r.[submit_time]			 AS Request_SubmitTime
	,r.[start_time]				 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Vous pouvez afficher les rôles de gestion des charges de travail avec `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

La requête suivante montre le rôle auquel est affecté chaque utilisateur.

```sql
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse offre les types d’attente suivants.

- LocalQueriesConcurrencyResourceType : requêtes qui figurent à l’extérieur de l’infrastructure d’emplacements de concurrence. Les requêtes DMV et les fonctions système telles que `SELECT @@VERSION` sont des exemples de requête locale.
- UserConcurrencyResourceType : requêtes qui figurent à l’intérieur de l’infrastructure d’emplacements de concurrence. Les requêtes exécutées sur des tables d’utilisateurs finaux sont des exemples de requêtes qui doivent utiliser ce type de ressource.
- DmsConcurrencyResourceType : attentes résultant d’opérations de déplacement de données.
- BackupConcurrencyResourceType : cette attente indique qu’une base de données est en cours de sauvegarde. La valeur maximale de ce type de ressource est égale à 1. Si plusieurs sauvegardes ont été demandées en même temps, les autres sont placées en file d’attente.

Vous pouvez utiliser la DMV `sys.dm_pdw_waits` pour connaître les ressources attendues par une demande.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]			AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,	SESSION_ID()			AS Current_session
,	s.[status]			AS Session_status
,	s.[login_name]
,	s.[query_count]
,	s.[client_id]
,	s.[sql_spid]
,	r.[command]			AS Request_command
,	r.[label]
,	r.[status]			AS Request_status
,	r.[submit_time]
,	r.[start_time]
,	r.[end_compile_time]
,	r.[end_time]
,	DATEDIFF(ms,r.[submit_time],r.[start_time])		AS Request_queue_time_ms
,	DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,	DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,	r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID();
```

La DMV `sys.dm_pdw_resource_waits` affiche uniquement les attentes de ressources consommées par une requête donnée. Le temps d’attente d’une ressource mesure uniquement le délai nécessaire à la fourniture de la ressource, par opposition au temps d’attente de signal qui correspond au délai requis par le serveur SQL Server sous-jacent pour planifier la requête dans le processeur.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE	[session_id] <> SESSION_ID();
```

Vous pouvez utiliser la DMV `sys.dm_pdw_wait_stats` pour l’analyse des tendances historiques des attentes.

```sql
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w;
```

## Étapes suivantes

Pour plus d’informations sur la gestion de la sécurité et des utilisateurs de base de données, consultez [Sécuriser une base de données dans SQL Data Warehouse][]. Pour plus d’informations sur la façon dont les classes de ressources plus élevées peuvent améliorer la qualité des index columnstore en cluster, consultez [Reconstruire des index pour améliorer la qualité de segment].

<!--Image references-->

<!--Article references-->
[Sécuriser une base de données dans SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Reconstruire des index pour améliorer la qualité de segment]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality

<!--MSDN references-->
[Authentification et autorisation de base de données SQL : octroi de l’accès]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0713_2016-->