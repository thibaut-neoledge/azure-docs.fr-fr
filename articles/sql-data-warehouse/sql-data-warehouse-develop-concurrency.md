<properties
   pageTitle="Gestion de la concurrence et des charges de travail dans SQL Data Warehouse | Microsoft Azure"
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
   ms.date="03/04/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gestion de la concurrence et des charges de travail dans SQL Data Warehouse
Pour offrir des performances prévisibles à grande échelle, SQL Data Warehouse implémente des mécanismes de gestion de la concurrence des charges de travail et de l’affectation des ressources de calcul.

Cet article vous présente les concepts de gestion de la concurrence et de gestion des charges de travail, en expliquant comment ces deux fonctionnalités ont été implémentées, ainsi que la procédure à suivre pour les contrôler dans votre entrepôt de données.

>[AZURE.NOTE] SQL Data Warehouse prend en charge les charges de travail multi-utilisateur, mais pas les charges de travail mutualisées.

## Accès concurrentiel
Il est important de noter que la concurrence dans SQL Data Warehouse est régie par deux concepts : **requêtes concurrentes** et **emplacements de concurrence**.

Les requêtes concurrentes correspondent au nombre de requêtes s’exécutant simultanément. SQL Data Warehouse prend en charge jusqu’à 32 **requêtes concurrentes**. Chaque exécution de requête est considérée comme une requête, qu’il s’agisse d’une requête en série (monothread) ou d’une requête parallèle (multithread). Il s’agit là d’une limite fixe qui s’applique à tous les niveaux de service et à toutes les requêtes.

La notion d’emplacement de concurrence est un concept plus dynamique lié à l’objectif de niveau de service Data Warehouse Unit (DWU) pour votre entrepôt de données. Lorsque vous augmentez le nombre de DWU alloué à SQL Data Warehouse, cette opération a une incidence sur d’autres ressources de calcul. Toutefois, l’augmentation du nombre de DWU augmente également le nombre d’**emplacements de concurrence** disponibles.

En règle générale, chaque requête concurrente consomme un ou plusieurs emplacements de concurrence. Le nombre exact d’emplacements dépend de trois facteurs :

1. Valeur DWU pour SQL Data Warehouse
2. **Classe de ressource** à laquelle appartient l’utilisateur
3. Si la requête ou l'opération est régie par le modèle d'emplacement d'accès concurrentiel ou non

> [AZURE.NOTE] Il est important de noter que toutes les requêtes ne sont pas régies par la règle de requête d'emplacement d'accès concurrentiel. Toutefois, la plupart des requêtes utilisateur le sont. Certaines requêtes et opérations n'utilisent pas les emplacements de concurrence. Ces requêtes et opérations restent limitées par la limite de requêtes simultanées, c'est pourquoi les deux règles sont décrites. Reportez-vous à la section [exceptions de classe de ressource](#exceptions) ci-dessous pour plus de détails.

Le tableau ci-dessous décrit les limites de requêtes simultanées et d’emplacements de concurrence ; en supposant que votre requête est régie par les ressources.

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

Les charges de travail de requête SQL Data Warehouse doivent respecter ces seuils. S’il existe plus de 32 requêtes concurrentes ou que vous dépassez le nombre d’emplacements de concurrence, la requête est mise en file d’attente jusqu’à ce que les deux seuils puissent être satisfaits.

## Gestion des charges de travail

Dans le cadre de son implémentation de la gestion des charges de travail, SQL Data Warehouse expose quatre classes de ressource distinctes sous la forme de **rôles de base de données**.

Ces rôles sont les suivants :

- smallrc
- mediumrc
- largerc
- xlargerc

Les classes de ressource sont une partie essentielle de la gestion de la charge de travail de SQL Data Warehouse. Elles déterminent les ressources de calcul allouées à la requête.

Par défaut, chaque utilisateur appartient à la classe de ressource smallrc. Toutefois, tout utilisateur peut être ajouté à une ou plusieurs classes de ressource supérieures. En règle générale, SQL Data Warehouse prend en compte l’appartenance au rôle le plus élevé pour l’exécution des requêtes. L’ajout d’un utilisateur à une classe de ressource plus élevée augmente les ressources pour cet utilisateur, mais consomme également davantage d’emplacements de concurrence, ce qui risque de limiter votre capacité de concurrence. Ceci est dû au fait que lorsque le nombre de ressources alloué à une requête augmente, le système doit limiter les ressources consommées par les autres requêtes. Rien n’est jamais gratuit.

La ressource la plus importante régie par la classe de ressource supérieure est la mémoire. La plupart des tables d’entrepôt de données d’une taille significative utilisent des index columnstore cluster. Bien que cette approche offre généralement les meilleures performances pour les charges de travail d’entrepôt de données, la gestion de ces index est une opération qui utilise beaucoup de mémoire. Il est souvent très avantageux d’utiliser les classes de ressource supérieures pour les opérations de gestion des données, comme les reconstructions d’index.

SQL Data Warehouse implémente les classes de ressource au moyen de rôles de base de données. Pour devenir membre d'une classe de ressource supérieure et augmenter votre mémoire de façon simple et en priorité, il suffit d’ajouter votre utilisateur de base de données à l'un des rôles/classes de ressources mentionnés ci-dessus.

### Membres de classe de ressource

Vous pouvez ajouter ou supprimer votre nom au niveau du rôle de base de données de gestion des charges de travail en utilisant les procédures `sp_addrolemember` et `sp_droprolemember`. Notez que vous devez disposer d’une autorisation `ALTER ROLE` pour effectuer cette opération. Vous n’avez pas la possibilité d’utiliser la syntaxe DDL ALTER ROLE. Vous devez utiliser les procédures stockées mentionnées ci-dessus. Un exemple complet montrant comment créer des connexions et des utilisateurs est fourni dans la section [gestion des utilisateurs)[#gestion-des-utilisateurs] à la fin de cet article.

> [AZURE.NOTE] Plutôt que d’ajouter ou de supprimer un utilisateur dans un groupe de gestion des charges de travail, il est souvent plus simple de lancer ces opérations plus intensives par le biais d’une connexion ou d’un utilisateur distincts affectés en permanence à la classe de ressource supérieure.

### Allocation de mémoire

Le tableau ci-après détaille l’augmentation de mémoire disponible pour chaque requête, tributaire de la classe de ressource appliquée à l’utilisateur qui exécute cette requête :

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |
-->

| Mémoire disponible (par dist.) | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc (valeur par défaut) (s) | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo | 100 Mo |
| mediumrc (m) | 100 Mo | 200 Mo | 200 Mo | 400 Mo | 400 Mo | 400 Mo | 800 Mo | 800 Mo | 800 Mo | 1 600 Mo |
| largerc (l) | 200 Mo | 400 Mo | 400 Mo | 800 Mo | 800 Mo | 800 Mo | 1 600 Mo | 1 600 Mo | 1 600 Mo | 3 200 Mo |
| xlargerc (xl) | 400 Mo | 800 Mo | 800 Mo | 1 600 Mo | 1 600 Mo | 1 600 Mo | 3 200 Mo | 3 200 Mo | 3 200 Mo | 6 400 Mo |

### Consommation des emplacements de concurrence

En outre, comme indiqué ci-dessus, plus la classe de ressource affectée à l’utilisateur est élevée, plus la consommation des emplacements de concurrence est importante. Le tableau ci-dessous présente la consommation des emplacements de concurrence par les requêtes pour une classe de ressource donnée.

<!--
| Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| Consommation | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| Nombre maximal de requêtes concurrentes | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| Nombre maximal d’emplacements de concurrence | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc (valeur par défaut) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

### Exceptions

Il existe des cas où l'appartenance à une classe de ressource plus élevée ne modifie pas les ressources affectées à la requête ou l'opération. Cela se produit généralement lorsque les ressources nécessaires pour accomplir l'action sont faibles. Dans ces cas, la classe de ressource par défaut ou small (smallrc) est toujours utilisée, quelle que soit la classe de ressource affectée à l'utilisateur. Par exemple, `CREATE LOGIN` s'exécute toujours en smallrc. Les ressources nécessaires pour accomplir cette opération sont très faibles. Par conséquent, il serait inutile d'inclure la requête dans le modèle d'emplacement d'accès concurrentiel. Il serait contre-productif de pré-allouer de grandes quantités de mémoire pour cette action. En excluant `CREATE LOGIN` du modèle d'emplacement d'accès concurrentiel, SQL Data Warehouse peut être beaucoup plus efficace.

Vous trouverez ci-dessous une liste des instructions et des opérations qui **sont** régies par les classes de ressource :

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

Les instructions suivantes ne respectent **pas** les classes de ressource :

- CREATE TABLE
- ALTER TABLE ... SWITCH PARTITION
- ALTER TABLE ... SPLIT PARTITION
- ALTER TABLE ... MERGE PARTITION
- DROP TABLE
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE STATISTICS
- UPDATE STATISTICS
- DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE USER
- ALTER USER
- DROP USER
- CREATE PROCEDURE
- ALTER PROCEDURE
- DROP PROCEDURE
- CREATE VIEW
- DROP VIEW
- INSERT VALUES
- SELECT (depuis les affichages du système et les DMV)
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

> [AZURE.NOTE] Il est important de souligner que les requêtes `SELECT` s'exécutant exclusivement sur les affichages catalogue et de gestion dynamique ne sont **pas** régies par les classes de ressource.

Il est important de noter que la majorité des requêtes utilisateur sont susceptibles d'être régies par les classes de ressource. La règle générale est que la charge de travail de la requête active doit respecter les seuils de requêtes simultanées et d’emplacement d’accès concurrentiel, sauf si elle a été spécifiquement exclue par la plateforme. En tant qu'utilisateur final, vous ne pouvez pas choisir d'exclure une requête du modèle d'emplacement d'accès concurrentiel. Dès que l’un de ces seuils est dépassé, les requêtes sont placées en file d’attente. Les requêtes mises en file d’attente sont alors traitées dans l’ordre de priorité suivi par l’heure de la requête.

### Éléments internes

En réalité, la gestion de la charge de travail de SQL Data Warehouse est un peu plus compliquée. Les classes de ressources sont mappées dynamiquement à un ensemble générique de groupes de gestion des charges de travail au sein du gouverneur de ressources. Les groupes utilisés dépendent de la valeur DWU de l’entrepôt. Toutefois, il existe un total de huit groupes de charges de travail utilisés par SQL Data Warehouse. Il s’agit des étapes suivantes :

- SloDWGroupC00
- SloDWGroupC01
- SloDWGroupC02
- SloDWGroupC03
- SloDWGroupC04
- SloDWGroupC05
- SloDWGroupC06
- SloDWGroupC07

Ces 8 groupes sont mappés à la consommation d’emplacements d’accès concurrentiel

| Groupe de charges de travail | Mappage d’emplacement d’accès concurrentiel | Mappage de priorité |
| :------------  | :----------------------- | :--------------- |
| SloDWGroupC00 | 1 | Moyenne |
| SloDWGroupC01 | 2 | Moyenne |
| SloDWGroupC02 | 4 | Moyenne |
| SloDWGroupC03 | 8 | Moyenne |
| SloDWGroupC04 | 16 | Élevé |
| SloDWGroupC05 | 32 | Élevé |
| SloDWGroupC06 | 64 | Élevé |
| SloDWGroupC07 | 128 | Élevé |

Par exemple, si DW500 est la valeur actuelle du paramètre DWU de SQL Data Warehouse, les groupes de charges de travail actives sont mappés aux classes de ressources comme suit :

| Classe de ressource | Groupe de charges de travail | Emplacements d’accès concurrentiel utilisés | Importance |
| :------------- | :------------- | :---------------------   | :--------- |
| smallrc | SloDWGroupC00 | 1 | Moyenne |
| mediumrc | SloDWGroupC02 | 4 | Moyenne |
| largerc | SloDWGroupC03 | 8 | Moyenne |
| xlargerc | SloDWGroupC04 | 16 | Élevé |

Pour examiner en détail les différences dans l’allocation des ressources mémoire dans la perspective du gouverneur de ressources, utilisez la requête suivante :

```
WITH rg
AS
(   SELECT  pn.name									AS node_name
	,		pn.[type]								AS node_type
	,		pn.pdw_node_id							AS node_id
	,		rp.name									AS pool_name
    ,       rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,       wg.name									AS group_name
    ,       wg.importance							AS group_importance
    ,       wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,       wg.max_dop								AS group_max_dop
    ,       wg.effective_max_dop					AS group_effective_max_dop
	,		wg.total_request_count					AS group_total_request_count
	,		wg.total_queued_request_count			AS group_total_queued_request_count
	,		wg.active_request_count					AS group_active_request_count
	,		wg.queued_request_count					AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
															        AND wg.pool_id      = rp.pool_id
	JOIN	sys.dm_pdw_nodes pn										ON	wg.pdw_node_id	= pn.pdw_node_id
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

> [AZURE.NOTE] La requête ci-dessus permet également d’analyser l’utilisation active et de l’historique des groupes de charges de travail lors du dépannage.

## Exemples de gestion des charges de travail

Cette section fournit quelques exemples supplémentaires à étudier pour la gestion des utilisateurs et la détection de requêtes en file d’attente.

### Gestion des utilisateurs

Un utilisateur a d’abord besoin d’une connexion pour que vous puissiez lui accorder l’accès à SQL Data Warehouse.

Ouvrez une connexion à la base de données MASTER de votre SQL Data Warehouse et exécutez les commandes suivantes :

```
CREATE LOGIN newperson WITH PASSWORD = 'mypassword'

CREATE USER newperson for LOGIN newperson
```

> [AZURE.NOTE] Il est judicieux de créer des utilisateurs pour les connexions dans la base de données MASTER, au sein d’Azure SQL Database et d’Azure SQL Data Warehouse. Deux rôles de serveur sont disponibles à ce niveau et nécessitent que la connexion ait un utilisateur dans la base de données MASTER afin d’accorder l’appartenance. Il s’agit des rôles `Loginmanager` et `dbmanager`. Dans la base de données SQL Azure et SQL Data Warehouse, ces rôles octroient des droits de gestion des connexions et de création des bases de données. Ce n’est pas le cas de SQL Server. Pour plus d’informations, consultez l’article [Gestion des bases de données et des connexions dans la base de données SQL Azure].

Une fois que la connexion a été créée, un compte d’utilisateur doit être ajouté.

Ouvrez une connexion à la base de données SQL Data Warehouse et exécutez la commande suivante :

```
CREATE USER newperson FOR LOGIN newperson
```

Lorsque vous avez terminé, vous devez accorder des autorisations à l’utilisateur. L’exemple indiqué ci-dessous accorde `CONTROL` dans la base de données SQL Data Warehouse. Au niveau de la base de données, `CONTROL` est l’équivalent de db\_owner dans SQL Server.

```
GRANT CONTROL ON DATABASE::MySQLDW to newperson
```

Pour afficher les rôles de gestion des charges de travail, utilisez la requête suivante :

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Pour ajouter un utilisateur à un rôle de gestion des charges de travail, utilisez la requête suivante :

```
EXEC sp_addrolemember 'largerc', 'newperson'
```

Pour supprimer un utilisateur d’un rôle de gestion des charges de travail, utilisez la requête suivante :

```
EXEC sp_droprolemember 'largerc', 'newperson'
```

> [AZURE.NOTE] Il n’est pas possible de supprimer un utilisateur dans la classe smallrc.

Pour afficher les utilisateurs membres d’un rôle donné, utilisez la requête suivante :

```
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc')
;
```

### Détection des requêtes en file d’attente
Pour identifier les requêtes qui sont placées dans une file d’attente de concurrence, vous pouvez toujours consulter la vue de gestion dynamique (DMV) `sys.dm_pdw_exec_requests`.

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,r.resource_class                               AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

SQL Data Warehouse intègre des types d’attente spécifiques pour mesurer la concurrence.

Il s'agit de :

- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

Le type LocalQueriesConcurrencyResourceType se réfère aux requêtes qui se trouvent à l’extérieur de l’infrastructure d’emplacements de concurrence. Les requêtes DMV et les fonctions système telles que `SELECT @@VERSION` sont des exemples de requête locale.

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
[Gestion des bases de données et des connexions dans la base de données SQL Azure]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->