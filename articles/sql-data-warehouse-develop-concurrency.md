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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Gestion de la concurrence et des charges de travail dans SQL Data Warehouse
Pour offrir des performances prévisibles à grande échelle, SQL Data Warehouse implémente des mécanismes de gestion de la concurrence des charges de travail et de l’affectation des ressources de calcul.

Cet article vous présente les concepts de gestion de la concurrence et de gestion des charges de travail, en expliquant la façon dont ces deux fonctionnalités ont été implémentées, ainsi que la procédure à suivre pour les contrôler dans votre entrepôt de données.

## Accès concurrentiel
Il est important de noter que la concurrence dans SQL Data Warehouse est régie par deux concepts : **requêtes concurrentes** et **emplacements de concurrence**.

Les requêtes concurrentes correspondent au nombre de requêtes s’exécutant simultanément. SQL Data Warehouse prend en charge jusqu’à 32 **requêtes concurrentes**. Chaque exécution de requête est considérée comme une requête, qu’il s’agisse d’une requête en série (monothread) ou d’une requête parallèle (multithread). Il s’agit là d’une limite fixe qui s’applique à tous les niveaux de service.

La notion d’emplacement de concurrence est un concept plus dynamique lié à l’objectif de niveau de service Data Warehouse Unit (DWU) pour votre entrepôt de données. Lorsque vous augmentez le nombre de DWU alloué à SQL Data Warehouse, cette opération a une incidence sur d’autres ressources de calcul. Toutefois, l’augmentation du nombre de DWU augmente également le nombre d’**emplacements de concurrence** disponibles.

SQL Data Warehouse doit respecter les deux seuils. S’il existe plus de 32 requêtes concurrentes ou que vous dépassez le nombre d’emplacements de concurrence, la requête est mise en file d’attente jusqu’à ce que les deux seuils puissent être satisfaits.

Chaque requête concurrente consomme un ou plusieurs emplacements de concurrence. Le nombre exact d’emplacements dépend de deux facteurs :

1. Valeur DWU pour SQL Data Warehouse
2. **Classe de ressource** à laquelle appartient l’utilisateur 

<!--
| Concurrency Slot Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
-->

| Consommation des emplacements de concurrence | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | 
| Nombre maximal de requêtes concurrentes | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 
| Nombre maximal d’emplacements de concurrence | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 

Les classes de ressource constituent un élément essentiel de la gestion des charges de travail SQL Data Warehouse, car elles régissent également les ressources de calcul allouées à la requête. Ces classes sont abordées dans la section ci-dessous consacrée à la gestion des charges de travail.

## Gestion des charges de travail

Dans le cadre de son implémentation de la gestion des charges de travail, SQL Data Warehouse expose quatre classes de ressource distinctes sous la forme de **rôles de base de données**.

Ces rôles sont les suivants :

- smallrc
- mediumrc
- largerc
- xlargerc

Vous pouvez visualiser les rôles qui vous correspondent à l’aide de la requête ci-après.

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Par défaut, chaque utilisateur appartient à la classe de ressource smallrc. Toutefois, tout utilisateur peut être ajouté à une ou plusieurs classes de ressource supérieures. SQL Data Warehouse prend en compte l’appartenance au rôle le plus élevé pour l’exécution des requêtes. L’ajout d’un utilisateur à une classe de ressource plus élevée augmente les ressources pour cet utilisateur, mais consomme également davantage d’emplacements de concurrence, ce qui risque de limiter votre capacité de concurrence. Ceci est dû au fait que lorsque le nombre de ressources alloué à une requête augmente, le système doit limiter les ressources consommées par les autres requêtes. Rien n’est jamais gratuit.

La ressource la plus importante régie par la classe de ressource supérieure est la mémoire. La plupart des tables d’entrepôt de données d’une taille significative utilisent des index columnstore cluster. Bien que cette approche offre généralement les meilleures performances pour les charges de travail d’entrepôt de données, la gestion de ces index est une opération qui utilise beaucoup de mémoire. Il est souvent très avantageux d’utiliser les classes de ressource supérieures pour les opérations de gestion des données, comme les reconstructions d’index.

Pour augmenter votre mémoire, il vous suffit d’ajouter l’utilisateur de votre base de données à l’un des rôles mentionnés ci-dessus.

Vous pouvez ajouter ou supprimer votre nom au niveau du rôle de base de données de gestion des charges de travail en utilisant les procédures `sp_addrolemember` et `sp_droprolemember`. Notez que vous devez disposer d’une autorisation `ALTER ROLE` pour effectuer cette opération. Vous n’avez pas la possibilité d’utiliser la syntaxe DDL ALTER ROLE. Vous devez utiliser les procédures stockées mentionnées ci-dessus.

> [AZURE.NOTE]Plutôt que d’ajouter ou de supprimer un utilisateur dans un groupe de gestion des charges de travail, il est souvent plus simple de lancer ces opérations plus intensives par le biais d’une connexion ou d’un utilisateur distincts affectés en permanence à la classe de ressource supérieure.

Le tableau ci-après détaille l’augmentation de mémoire disponible pour chaque requête, tributaire de la classe de ressource appliquée à l’utilisateur qui exécute cette requête :

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->
| Mémoire disponible (par dist.) | Priorité | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc (valeur par défaut) (s) | Moyenne | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo |
| mediumrc (m) | Moyenne | 100 Mo | 200 Mo | 200 Mo | 400 Mo | 400 Mo | 400 Mo | 800 Mo | 800 Mo | 800 Mo | 1 600 Mo |
| largerc (l) | Élevé | 200 Mo | 400 Mo | 400 Mo | 800 Mo | 800 Mo | 800 Mo | 1 600 Mo | 1 600 Mo | 1 600 Mo | 3 200 Mo |
| xlargerc (xl) | Élevé | 400 Mo | 800 Mo | 800 Mo | 1 600 Mo | 1 600 Mo | 1 600 Mo | 3 200 Mo | 3 200 Mo | 3 200 Mo | 6 400 Mo |


En outre, comme indiqué ci-dessus, plus la classe de ressource affectée à l’utilisateur est élevée, plus la consommation des emplacements de concurrence est importante. Le tableau ci-dessous présente la consommation des emplacements de concurrence par les requêtes pour une classe de ressource donnée.

<!--
| Concurrency slot consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| Consommation des emplacements de concurrence | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| Nombre maximal de requêtes concurrentes | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| Nombre maximal d’emplacements de concurrence | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc (valeur par défaut) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

Il est important de garder à l’esprit que la charge de travail des requêtes active doit respecter les seuils de requêtes concurrentes et d’emplacements de concurrence. Dès que l’un de ces seuils est dépassé, les requêtes sont placées en file d’attente. Les requêtes mises en file d’attente sont alors traitées dans l’ordre de priorité suivi par l’heure de la requête.

## Détection des requêtes en file d’attente
Pour identifier les requêtes qui sont placées dans une file d’attente de concurrence, vous pouvez toujours consulter la vue de gestion dynamique (DMV) `sys.dm_pdw_exec_requests`.

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
FROM    sys.dm_pdw_exec_requests r
;
```

SQL Data Warehouse intègre des types d’attente spécifiques pour mesurer la concurrence.

Il s'agit de :
 
- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

Le type LocalQueriesConcurrencyResourceType se réfère aux requêtes qui se trouvent à l’extérieur de l’infrastructure d’emplacements de concurrence. Les requêtes et les fonctions système DMV telles que SELECT @@VERSION sont des exemples de requêtes locales (localqueries).

Le type UserConcurrencyResourceType se rapporte aux requêtes qui figurent à l’intérieur de l’infrastructure d’emplacements de concurrence. Les requêtes exécutées sur des tables d’utilisateurs finaux sont des exemples de requêtes qui doivent utiliser ce type de ressource.

Le type DmsConcurrencyResourceType fait référence aux attentes résultant d’opérations de déplacement de données.

Le type BackupConcurrencyResourceType est visible lorsqu’une base de données est en cours de sauvegarde. La valeur maximale de ce type de ressource est égale à 1. Si plusieurs sauvegardes ont été demandées en même temps, les autres sont placées en file d’attente.


Pour effectuer l’analyse des requêtes actuellement placées en file d’attente afin de déterminer les ressources attendues par une requête, consultez la vue de gestion dynamique `sys.dm_pdw_waits`.

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]											AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,		SESSION_ID()										AS Current_session
,		s.[status]											AS Session_status
,		s.[login_name]
,		s.[query_count]
,		s.[client_id]
,		s.[sql_spid]
,		r.[command]											AS Request_command
,		r.[label]
,		r.[status]											AS Request_status
,		r.[submit_time]
,		r.[start_time]
,		r.[end_compile_time]
,		r.[end_time]
,		DATEDIFF(ms,r.[submit_time],r.[start_time])			AS Request_queue_time_ms
,		DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,		DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,		r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID()
;
```

Pour visualiser uniquement les temps d’attente de ressource consommés par une requête donnée, vous pouvez vous reporter à la vue de gestion dynamique `sys.dm_pdw_resource_waits`. Le temps d’attente d’une ressource mesure uniquement le délai nécessaire à la fourniture de la ressource, par opposition au temps d’attente de signal qui correspond au délai requis par le serveur SQL Server sous-jacent pour planifier la requête dans l’unité centrale.

```
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
WHERE	[session_id] <> SESSION_ID()
;
```

Enfin, pour l’analyse des tendances historiques des attentes, SQL Data Warehouse fournit la gestion de vue dynamique `sys.dm_pdw_wait_stats`.

```
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w
;
```

## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=July15_HO1-->