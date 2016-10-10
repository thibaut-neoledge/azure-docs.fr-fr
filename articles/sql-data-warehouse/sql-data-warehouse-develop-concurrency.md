<properties
   pageTitle="Gestion de la concurrence et des charges de travail dans SQL Data Warehouse | Microsoft Azure"
   description="Décrit la gestion de la concurrence et des charges de travail dans Azure SQL Data Warehouse pour le développement de solutions."
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
   ms.date="09/27/2016"
   ms.author="sonyama;barbkess;jrj"/>

# Gestion de la concurrence et des charges de travail dans SQL Data Warehouse

Pour offrir des performances prévisibles à grande échelle, Microsoft Azure SQL Data Warehouse vous permet de contrôler les niveaux de concurrence, ainsi que les allocations de ressources telles que la définition des priorités du processeur et de la mémoire. Cet article vous présente les concepts de gestion de la concurrence et de gestion des charges de travail, en expliquant comment ces deux fonctionnalités ont été implémentées, ainsi que la procédure à suivre pour les contrôler dans votre entrepôt de données. La gestion de charge de travail SQL Data Warehouse a pour but de vous aider à prendre en charge des environnements multi-utilisateurs. Elle n’est pas destinée aux charges de travail mutualisées.

## Limites de concurrence

SQL Data Warehouse autorise jusqu’à 1024 connexions simultanées. Les 1024 connexions peuvent soumettre des requêtes simultanément. Toutefois, pour optimiser le débit, SQL Data Warehouse peut mettre certaines requêtes en file d’attente pour s’assurer que chaque requête reçoit une allocation de mémoire minimale. La mise en file d’attente se produit lors de l’exécution de la requête. La mise en file d’attente lorsque les limites de concurrence sont atteintes permet à SQL Data Warehouse d’augmenter le débit total en veillant à ce que les requêtes actives puissent accéder aux ressources de mémoire essentielles.

Les limites de concurrence sont régies par deux concepts : les *requêtes simultanées* et les *emplacements de concurrence*. Pour qu’une requête s’exécute, elle doit s’exécuter à la fois dans limite de concurrence de requête et dans les limites de l’allocation d’emplacement de concurrence.

- Les requêtes simultanées sont les requêtes s’exécutant simultanément. SQL Data Warehouse prend en charge jusqu’à 32 requêtes simultanées sur les tailles de DWU plus importantes.
- Les emplacements de concurrence sont alloués en fonction de la DWU. Chaque DWU100 fournit 4 emplacements de concurrence. Par exemple, une DW100 alloue 4 emplacements de concurrence et une DW1000 en alloue 40. Chaque requête consomme un ou plusieurs emplacements de concurrence, selon la [classe de ressources](#resource-classes) de la requête. Les requêtes en cours d’exécution dans la classe de ressource smallrc consomment un emplacement de concurrence. Les requêtes en cours d’exécution dans une classe de ressource supérieure consomment plusieurs emplacements de concurrence.

Le tableau suivant décrit les limites des requêtes simultanées et des emplacements de concurrence pour différentes tailles de DWU.

### Limites de concurrence

| DWU | Nombre maximal de requêtes simultanées | Emplacements de concurrence alloués |
| :----  | :---------------------: | :-------------------------: |
| DW100 | 4 | 4 |
| DW200 | 8 | 8 |
| DW300 | 12 | 12 |
| DW400 | 16 | 16 |
| DW500 | 20 | 20 |
| DW600 | 24 | 24 |
| DW1000 | 32 | 40 |
| DW1200 | 32 | 48 |
| DW1500 | 32 | 60 |
| DW2000 | 32 | 80 |
| DW3000 | 32 | 120 |
| DW6000 | 32 | 240 |

Lorsque l’un de ces seuils est atteint, les nouvelles requêtes sont mises en file d’attente et exécutées sur la base du modèle premier entré, premier sorti. À mesure que les requêtes se terminent et que le nombre de requêtes et d’emplacements chute en deçà de la limite, les requêtes mises en file d’attente sont publiées.

> [AZURE.NOTE]  *Certaines* requêtes s’exécutant exclusivement sur les vues de gestion dynamique (DMV) ou les affichages catalogue ne sont régies par aucune des limites de concurrence. Les utilisateurs peuvent surveiller le système en toutes circonstances, quel que soit le nombre de requêtes en cours d’exécution dessus.

## Classes de ressources

Les classes de ressources vous aident à contrôler l’allocation de mémoire et les cycles de processeur pour une requête. Vous pouvez affecter quatre classes de ressources à un utilisateur sous la forme de *rôles de base de données*. Les quatre classes de ressources sont **smallrc**, **mediumrc**, **largerc** et **xlargerc**. Les utilisateurs de smallrc bénéficient d’une plus petite quantité de mémoire et peuvent tirer parti d’une concurrence accrue. En revanche, les utilisateurs affectés à xlargerc disposent de grandes quantités de mémoire. Ainsi, une plus faible proportion de leurs requêtes peuvent s’exécuter simultanément.

Par défaut, chaque utilisateur appartient à la petite classe de ressources, smallrc. La procédure `sp_addrolemember` sert à augmenter la classe de ressources, et `sp_droprolemember` à la réduire. Par exemple, cette commande augmente la classe de ressources de loaduser à largerc :

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Une bonne pratique consiste à affecter définitivement des utilisateurs à une classe de ressources, plutôt que de modifier leur classe de ressources. Par exemple, les charges des tables columnstore en cluster créent des index de qualité supérieure quand davantage de mémoire leur est allouée. Pour vous assurer que les charges ont accès à une mémoire plus élevée, créez un utilisateur spécifiquement pour le chargement des données et affectez-le définitivement à une classe de ressources supérieure.

Certains types de requêtes ne bénéficient pas d’une allocation de mémoire supérieure. Le système ignore leur allocation de classe de ressources et exécute toujours ces requêtes dans la petite classe de ressources. Si ces requêtes sont toujours exécutées dans la petite classe de ressources, elles peuvent s’exécuter lorsque des emplacements de concurrence sont sous pression et elles ne consomment pas plus d’emplacements que nécessaire. Consultez [Exceptions de classe de ressources](#query-exceptions-to-concurrency-limits) pour plus d’informations.

Voici quelques autres détails concernant la classe de ressources :

- L’autorisation *Modifier le rôle* est obligatoire pour modifier la classe de ressources d’un utilisateur.
- Vous pouvez ajouter un utilisateur à l’une ou plusieurs des classes de ressources plus élevées, mais les utilisateurs prennent les attributs de la classe de ressources la plus élevée à laquelle ils sont affectés. Autrement dit, si un utilisateur est affecté à la fois à mediumrc et largerc, la classe de ressources supérieure (largerc) est celle qui sera honorée.
- Vous ne pouvez pas modifier la classe de ressources de l’administrateur système.

Pour obtenir un exemple détaillé, consultez [Exemple de modification d’une classe de ressources utilisateur](#changing-user-resource-class-example).

## Allocation de mémoire

L’augmentation de la classe de ressources d’un utilisateur présente des avantages et des inconvénients. L’augmentation d’une classe de ressources pour un utilisateur octroiera à ses requêtes un accès à une mémoire supplémentaire, ce qui peut entraîner une accélération de leur exécution. Toutefois, les classes de ressources supérieures sont par ailleurs associées à une réduction du nombre de requêtes simultanées pouvant s’exécuter. Il s’agit d’un compromis entre l’allocation de grandes quantités de mémoire à une seule requête et la permission de l’exécution d’autres requêtes simultanées (qui nécessitent également des allocations de mémoire). Si de grandes quantités de mémoire sont allouées à un utilisateur pour une requête, les autres utilisateurs n’auront pas accès à cette mémoire pour exécuter une requête.

Le tableau suivant mappe la mémoire allouée à chaque distribution par DWU et classe de ressources.

### Allocations de mémoire par distribution (Mo)

| DWU | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100 | 100 | 100 | 200 | 400 |
| DW200 | 100 | 200 | 400 | 800 |
| DW300 | 100 | 200 | 400 | 800 |
| DW400 | 100 | 400 | 800 | 1 600 |
| DW500 | 100 | 400 | 800 | 1 600 |
| DW600 | 100 | 400 | 800 | 1 600 |
| DW1000 | 100 | 800 | 1 600 | 3 200 |
| DW1200 | 100 | 800 | 1 600 | 3 200 |
| DW1500 | 100 | 800 | 1 600 | 3 200 |
| DW2000 | 100 | 1 600 | 3 200 | 6 400 |
| DW3000 | 100 | 1 600 | 3 200 | 6 400 |
| DW6000 | 100 | 3 200 | 6 400 | 12 800 |

Dans le tableau précédent, vous constatez qu’une requête s’exécutant sur une DW2000 dans la classe de ressources xlargerc dispose d’un accès à 6 400 Mo de mémoire dans chacune des 60 bases de données distribuées. Dans SQL Data Warehouse, il existe 60 distributions. Par conséquent, pour calculer l’allocation totale de mémoire pour une requête d’une classe de ressources données, les valeurs ci-dessus doivent être multipliées par 60.

### Allocations de mémoire à l’échelle du système (Go)

| DWU | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100 | 6 | 6 | 12 | 23 |
| DW200 | 6 | 12 | 23 | 47 |
| DW300 | 6 | 12 | 23 | 47 |
| DW400 | 6 | 23 | 47 | 94 |
| DW500 | 6 | 23 | 47 | 94 |
| DW600 | 6 | 23 | 47 | 94 |
| DW1000 | 6 | 47 | 94 | 188 |
| DW1200 | 6 | 47 | 94 | 188 |
| DW1500 | 6 | 47 | 94 | 188 |
| DW2000 | 6 | 94 | 188 | 375 |
| DW3000 | 6 | 94 | 188 | 375 |
| DW6000 | 6 | 188 | 375 | 750 |

Dans ce tableau d’allocations de mémoire à l’échelle du système, vous pouvez constater qu’une requête s’exécutant sur une DW2000 dans la classe de ressource xlargerc se voit allouer un total de 375 Go de mémoire (6 400 Mo * 60 distributions / 1 024 pour la conversion en Go) sur l’intégralité de votre instance SQL Data Warehouse.

## Consommation des emplacements de concurrence

SQL Data Warehouse accorde plus de mémoire aux requêtes qui s’exécutent dans des classes de ressources supérieures. La mémoire est une ressource fixe. Par conséquent, plus la mémoire allouée par requête est importante, plus le nombre de requêtes s’exécutant en simultané est faible. Le tableau suivant reprend tous les concepts précédents dans une vue unique qui présente le nombre d’emplacements de concurrence disponibles par DWU, ainsi que les emplacements consommés par chaque classe de ressources.

### Allocation et consommation des emplacements de concurrence

| DWU | Nombre maximal de requêtes concurrentes | Emplacements de concurrence alloués | Emplacements utilisés par smallrc | Emplacements utilisés par mediumrc | Emplacements utilisés par largerc | Emplacements utilisés par xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100 | 4 | 4 | 1 | 1 | 2 | 4 |
| DW200 | 8 | 8 | 1 | 2 | 4 | 8 |
| DW300 | 12 | 12 | 1 | 2 | 4 | 8 |
| DW400 | 16 | 16 | 1 | 4 | 8 | 16 |
| DW500 | 20 | 20 | 1 | 4 | 8 | 16 |
| DW600 | 24 | 24 | 1 | 4 | 8 | 16 |
| DW1000 | 32 | 40 | 1 | 8 | 16 | 32 |
| DW1200 | 32 | 48 | 1 | 8 | 16 | 32 |
| DW1500 | 32 | 60 | 1 | 8 | 16 | 32 |
| DW2000 | 32 | 80 | 1 | 16 | 32 | 64 |
| DW3000 | 32 | 120 | 1 | 16 | 32 | 64 |
| DW6000 | 32 | 240 | 1 | 32 | 64 | 128 |


Dans ce tableau, vous pouvez constater que SQL Data Warehouse en cours d’exécution en tant que DW1000 alloue un maximum de 32 requêtes simultanées et un total de 40 emplacements de concurrence. Si tous les utilisateurs étaient en cours d’exécution dans smallrc, 32 requêtes simultanées seraient alors autorisées car chaque requête consommerait un emplacement de concurrence. Si tous les utilisateurs sur une DW1000 étaient en cours d’exécution dans mediumrc, chaque requête bénéficierait de 800 Mo par distribution pour une allocation de mémoire totale de 47 Go par requête, et la concurrence serait limitée à cinq utilisateurs (40 emplacements de concurrence / 8 emplacements par utilisateur mediumrc).

## Importance de la requête

SQL Data Warehouse implémente des classes de ressources à l’aide de groupes de charges de travail. Il existe en tout huit groupes de charges de travail qui contrôlent le comportement des classes de ressources dans les différentes tailles de DWU. Pour les DWU, SQL Data Warehouse n’utilise que quatre des huit groupes de charges de travail. Cela est logique car chaque groupe de charges de travail est affecté à une des quatre classes de ressources : smallrc, mediumrc, largerc ou xlargerc. Il est important de bien comprendre ces groupes de charges de travail, car certains sont définis comme ayant une *importance* plus élevée. L’importance est utilisée pour la planification du processeur. Les requêtes exécutées avec une importance élevée obtiennent trois fois plus de cycles processeur que celles exécutées avec une importance moyenne. Ainsi, les mappages d’emplacements de concurrence déterminent également la priorité du processeur. Quand une requête utilise 16 emplacements ou plus, elle s’exécute avec une importance élevée.

Le tableau ci-dessous présente les mappages d’importance pour chaque groupe de charges de travail.

### Mappage des groupes de charges de travail aux emplacements de concurrence et au niveau d’importance

| Groupes de charges de travail | Mappage d’emplacement de concurrence | Mo / Distribution | Mappage d’importance |
| :-------------- | :----------------------: | :---------------: | :----------------- |
| SloDWGroupC00 | 1 | 100 | Moyenne |
| SloDWGroupC01 | 2 | 200 | Moyenne |
| SloDWGroupC02 | 4 | 400 | Moyenne |
| SloDWGroupC03 | 8 | 800 | Moyenne |
| SloDWGroupC04 | 16 | 1 600 | Élevé |
| SloDWGroupC05 | 32 | 3 200 | Élevé |
| SloDWGroupC06 | 64 | 6 400 | Élevé |
| SloDWGroupC07 | 128 | 12 800 | Élevé |

À partir du graphique **Allocation et consommation des emplacements de concurrence**, vous pouvez constater qu’une DW500 utilise 1, 4, 8 ou 16 emplacements de concurrence pour smallrc, mediumrc, largerc et xlargerc, respectivement. Vous pouvez rechercher ces valeurs dans le graphique précédent pour connaître l’importance de chaque classe de ressources.

### Mappage d’importance des DW500 aux classes de ressources

| Classe de ressources | Groupe de charges de travail | Emplacements de concurrence utilisés | Mo / Distribution | Importance |
| :------------- | :------------- | :--------------------: | :---------------: | :--------- |
| smallrc | SloDWGroupC00 | 1 | 100 | Moyenne |
| mediumrc | SloDWGroupC02 | 4 | 400 | Moyenne |
| largerc | SloDWGroupC03 | 8 | 800 | Moyenne |
| xlargerc | SloDWGroupC04 | 16 | 1 600 | Élevé |


Vous pouvez utiliser la requête DMV suivante pour examiner en détail les différences d’allocation des ressources mémoire du point de vue du gouverneur de ressources, ou analyser l’utilisation active et historique des groupes de charges de travail lors de la résolution des problèmes.

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

## Requêtes qui honorent les limites de concurrence

La plupart des requêtes sont régies par des classes de ressource. Ces requêtes doivent figurer à la fois dans le seuil des requêtes de concurrence et dans le seuil des emplacements de concurrence. Un utilisateur ne peut pas choisir d’exclure une requête du modèle d’emplacement de concurrence.

Les instructions suivantes honorent les classes de ressources :

- INSERT-SELECT
- UPDATE
- SUPPRIMER
- SELECT (lors de l’interrogation des tables d’utilisateur)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT (CTAS)
- Chargement de données
- Opérations de déplacement de données effectuées par le Service le déplacement des données (DMS)

## Exceptions de requêtes aux limites de concurrence

Certaines requêtes ne tiennent pas compte de la classe de ressources à laquelle l’utilisateur est affecté. Ces exceptions aux limites de concurrence s’appliquent lorsque les ressources nécessaires à une commande particulière sont insuffisantes, souvent parce que la commande est une opération de métadonnées. L’objectif de ces exceptions est d’éviter que des quantités de mémoire plus importantes soient allouées à des requêtes qui ne le nécessitent pas. Dans ces cas-là, la petite classe de ressources par défaut (smallrc) est toujours utilisée, quelle que soit la classe de ressources réellement affectée à l’utilisateur. Par exemple, `CREATE LOGIN` s’exécute toujours dans smallrc. Les ressources nécessaires pour accomplir cette opération sont très faibles. Par conséquent, il est inutile d’inclure la requête dans le modèle d’emplacement de concurrence. Ces requêtes n’étant pas non plus restreintes par la limite de concurrence de 32 utilisateurs, un nombre illimité de ces requêtes peuvent s’exécuter jusqu’à la limite de 1 024 sessions.

Les instructions suivantes ne respectent pas les classes de ressources :

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

## Exemple de modification d’une classe de ressources utilisateur

1. **Créer une connexion :** ouvrez une connexion à votre base de données **master** sur le serveur SQL hébergeant votre base de données SQL Data Warehouse et exécutez les commandes suivantes.

	```sql
	CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
	CREATE USER newperson for LOGIN newperson;
	```

	> [AZURE.NOTE] Il est judicieux de créer un utilisateur dans la base de données master pour les utilisateurs d’Azure SQL Data Warehouse. La création d’un utilisateur dans la base de données master permet à un utilisateur de se connecter à l’aide d’outils tels que SSMS sans spécifier un nom de base de données. Cela permet également d’utiliser l’Explorateur d’objets pour afficher toutes les bases de données sur un serveur SQL. Pour plus d’informations sur la création et la gestion des utilisateurs, consultez [Sécuriser une base de données dans SQL Data Warehouse][].

2. **Créer un utilisateur SQL Data Warehouse :** ouvrez une connexion à la base de données **SQL Data Warehouse** et exécutez la commande suivante.

	```sql
	CREATE USER newperson FOR LOGIN newperson;
	```

3. **Accorder des autorisations :** l’exemple suivant accorde `CONTROL` sur la base de données **SQL Data Warehouse**. Au niveau de la base de données, `CONTROL` est l’équivalent de db\_owner dans SQL Server.

	```sql
	GRANT CONTROL ON DATABASE::MySQLDW to newperson;
	```

4. **Augmenter la classe de ressources :** exécutez la requête suivante pour ajouter un utilisateur à un rôle de gestion des charges de travail plus élevé.

	```sql
	EXEC sp_addrolemember 'largerc', 'newperson'
	```

5. **Diminuer la classe de ressources :** exécutez la requête suivante pour supprimer un utilisateur d’un rôle de gestion des charges de travail.

	```sql
	EXEC sp_droprolemember 'largerc', 'newperson';
	```

	> [AZURE.NOTE] Il n’est pas possible de supprimer un utilisateur dans smallrc.

## Détection des requêtes en file d’attente et autres vues de gestion dynamique

Vous pouvez utiliser la DMV `sys.dm_pdw_exec_requests` pour identifier les requêtes en attente dans une file d’attente de concurrence. Les requêtes en attente pour un emplacement de concurrence auront le statut **suspendu**.

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
SELECT	 r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse offre les types d’attente suivants :

- **LocalQueriesConcurrencyResourceType** : requêtes qui figurent à l’extérieur de l’infrastructure d’emplacements de concurrence. Les requêtes DMV et les fonctions système telles que `SELECT @@VERSION` sont des exemples de requête locale.
- **UserConcurrencyResourceType** : requêtes qui figurent à l’intérieur de l’infrastructure d’emplacements de concurrence. Les requêtes exécutées sur des tables d’utilisateurs finaux sont des exemples de requêtes qui doivent utiliser ce type de ressource.
- **DmsConcurrencyResourceType** : attentes résultant d’opérations de déplacement de données.
- **BackupConcurrencyResourceType** : cette attente indique qu’une base de données est en cours de sauvegarde. La valeur maximale de ce type de ressource est égale à 1. Si plusieurs sauvegardes ont été demandées en même temps, les autres sont placées en file d’attente.

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

La DMV `sys.dm_pdw_resource_waits` affiche uniquement les attentes de ressources consommées par une requête donnée. Le temps d’attente d’une ressource mesure uniquement le délai nécessaire à la fourniture de la ressource, par opposition au temps d’attente de signal qui correspond au délai requis par les serveurs SQL sous-jacents pour planifier la requête dans le processeur.

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
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Reconstruire des index pour améliorer la qualité de segment]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Sécuriser une base de données dans SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0928_2016-->