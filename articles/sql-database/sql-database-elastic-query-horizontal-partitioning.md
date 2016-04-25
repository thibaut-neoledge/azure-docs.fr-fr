<properties
    pageTitle="Requêtes de base de données élastiques pour le partitionnement (partitionnement horizontal) | Microsoft Azure"
    description="comment configurer des requêtes élastiques sur les partitions horizontales"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/28/2016"
    ms.author="torsteng;sidneyh" />

# Requêtes de base de données élastiques pour le partitionnement (partitionnement horizontal)

Ce document explique comment configurer des requêtes de base de données élastiques pour les scénarios de partitionnement horizontal et effectuer vos requêtes. Pour avoir une définition du scénario de partitionnement horizontal, consultez [vue d’ensemble de requête élastique de la base de données (version préliminaire)](sql-database-elastic-query-overview.md).

![Requête sur plusieurs partitions][1]

La fonctionnalité fait partie de l’[ensemble de fonctionnalités de base de données élastiques](sql-database-elastic-scale-introduction.md) d’Azure SQL.
 
## Création d’objets de base de données

Une requête de base de données élastique étend la syntaxe T-SQL pour faire référence à des couches de données qui utilisent le partitionnement (ou partitionnement horizontal) pour répartir les données entre plusieurs bases de données. Cette section fournit une vue d’ensemble des instructions DDL associées aux requêtes sur les tables partitionnées. Ces instructions créent une représentation des métadonnées de votre couche de données partitionnées dans la base de données de requête élastique. Une des conditions préalables de l’exécution de ces instructions consiste à créer un mappage de la partition à l’aide de la bibliothèque cliente de base de données élastique. Pour plus d’informations, consultez [Gestion de mappage de partition](sql-database-elastic-scale-shard-map-management.md) ; ou utilisez l’exemple présent dans la rubrique suivante pour en créer un : [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).

La définition des objets de base de données pour une requête de base de données élastique s’appuie sur les instructions T-SQL suivantes qui sont expliquées plus loin pour le scénario de partitionnement horizontal ci-dessous :

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) 

* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)

* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)

* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### 1\.1 Clé principale et informations d’identification de la base de données 

Les informations d’identification sont constituées de l’ID utilisateur et du mot de passe que la requête de base de données élastique va utiliser pour se connecter à vos bases de données distantes dans Azure SQL DB. Pour créer la clé principale requise et les informations d’identification, utilisez la syntaxe suivante :

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = ’password’;
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = ‘<username>’,  
    SECRET = ‘<password>’
    [;]

Ou pour annuler la clé et les informations d’identification :

    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
**Remarque** Veillez à ce que *< username>* ne contienne pas le suffixe *« @servername »*.

### 1\.2 Sources de données externes

Fournir les informations de mappage de la partition et votre couche de données en définissant une source de données externe. La source de données externe fait référence au mappage de partitions. Une requête élastique utilise ensuite la source de données externe et le mappage de la partition sous-jacents pour énumérer les bases de données qui interviennent dans les couches de données. La syntaxe servant à créer une source de données externe est définie comme suit :

	<External_Data_Source> ::=    
	CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                               	           
			(TYPE = SHARD_MAP_MANAGER,
                   	LOCATION = '<fully_qualified_server_name>',
			DATABASE_NAME = ‘<shardmap_database_name>',
			CREDENTIAL = <credential_name>, 
			SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 
 
ou à abandonner une source de données externe :

	DROP EXTERNAL DATA SOURCE <data_source_name>[;] 

#### Autorisations de CREATE/DROP EXTERNAL DATA SOURCE 

L’utilisateur doit posséder l’autorisation ALTER ANY EXTERNAL DATA SOURCE. Cette autorisation est incluse dans l’autorisation ALTER DATABASE.

**Exemple**

L’exemple suivant illustre l’utilisation de l’instruction CREATE pour les sources de données externes.

	CREATE EXTERNAL DATA SOURCE MyExtSrc 
	WITH 
	( 
		TYPE=SHARD_MAP_MANAGER,
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ShardMapDatabase', 
		CREDENTIAL= SMMUser, 
		SHARD_MAP_NAME='ShardMap' 
	);
 
Vous pouvez récupérer la liste des sources de données externes actuelles à partir de la vue de catalogue suivante :

	select * from sys.external_data_sources; 

Notez que les mêmes informations d’identification sont utilisées pour lire le mappage de partition et accéder aux données présentes sur les partitions pendant le traitement d’une requête élastique.

### 1\.3 Tables externes 
 
Une requête élastique étend la table externe DDL pour faire référence à des tables externes qui sont partitionnées horizontalement sur plusieurs bases de données de table. La définition de table externe traite les aspects suivants :

* **Schéma** : la table externe DDL définit un schéma que vos requêtes peuvent utiliser. Le schéma fourni dans votre définition de la table externe doit correspondre au schéma des tables sur les partitions sur lesquelles sont stockées les données réelles. 

* **Distribution des données** : la table externe DDL définit la distribution des données utilisée pour répartir ces dernières sur l’ensemble de votre couche de données. Notez que base de données SQL Azure ne valide pas la distribution que vous définissez sur la table externe par rapport à la distribution réelle sur les partitions. Il est de votre responsabilité de vous assurer que la distribution de données sur les partitions effective correspond à votre définition de table externe.

* **Référence de couche de données** : la table externe DDL fait référence à une source de données externe. La source de données externe spécifie un mappage de partition qui fournit à la table externe les informations nécessaires à la localisation de toutes les bases de données de votre couche de données.

La syntaxe permettant de créer et supprimer des tables externes à l’aide de sources de données externes comme indiqué dans la section précédente est la suivante :

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
	    { WITH ( <sharded_external_table_options> ) }
	) [;]  
	
	<sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
	  [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

La clause DATA\_SOURCE définit la source de données externe (un mappage de la partition dans le cas d’un partitionnement horizontal) qui est utilisée pour la table externe.

Les clauses SCHEMA\_NAME et OBJECT\_NAME offrent la possibilité de mapper une définition de table externe sur une table d’un autre schéma de la partition, ou une table portant un autre nom, respectivement. En cas d’omission, le schéma de l’objet distant est supposé de type « dbo » et son nom est supposé être identique au nom de la table externe en cours de définition.

Les clauses SCHEMA\_NAME et OBJECT\_NAME sont particulièrement utiles si le nom de votre table distante est déjà utilisé dans la base de données dans laquelle vous souhaitez créer la table externe. Vous pouvez voir un exemple de ce problème lorsque vous souhaitez définir une table externe pour obtenir une vue agrégée des affichages de catalogue ou de vues de gestion dynamiques sur la couche des données mise à l’échelle. Dans la mesure où les affichages catalogue et les vues de gestion dynamique existent déjà localement, vous ne pouvez pas utiliser leur nom pour la définition de la table externe. Vous devez utiliser un autre nom et le nom de la vue de catalogue ou de la vue de gestion dynamique dans les clauses SCHEMA\_NAME et/ou OBJECT\_NAME. (Voir l’exemple ci-dessous.)

La clause DISTRIBUTION spécifie la distribution des données utilisée pour cette table :

* PARTITIONNÉ signifie que les données de cette table sont partitionnées horizontalement entre les bases de données de votre table de partition. La clé de partitionnement pour la distribution des données est capturée dans le paramètre <sharding_column_name>.  

* RÉPLIQUÉ signifie que des copies identiques de la table sont présentes sur chaque base de données de votre table de partition. Azure SQL DB ne conserve pas les copies de la table. La responsabilité de vous assurer que les réplicas sont identiques d’une base de données à l’autre vous incombe.

* ROUND\_ROBIN signifie que la table est distribuée par le biais d’un partitionnement horizontal. Cependant, une distribution dépendante de l’application a été utilisée.

Le processeur de requêtes utilise les informations fournies dans la clause DISTRIBUTION pour créer les plans de requête les plus efficaces.

Vous pouvez utiliser l’instruction qui suit pour supprimer les tables externes :

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

Les autorisations **CREATE/DROP EXTERNAL TABLE** : ALTER ANY EXTERNAL DATA SOURCE sont nécessaires. Elles le sont également pour faire référence à la source de données sous-jacente.

**Considérations de sécurité :** les utilisateurs ayant accès à la table externe acquièrent un accès automatique aux tables distantes sous-jacentes avec les informations d’identification fournies dans la définition de source de données externe. Vous devez gérer l’accès à la table externe avec beaucoup d’attention pour éviter une élévation de privilèges non souhaitée par le biais d’informations d’identification de la source de données externe. Les autorisations SQL standard permettent de GRANT (OCTROYER) ou de REVOKE (RÉVOQUER) l’accès à une table externe comme s’il s’agissait d’une table normale.

**Exemple** : l’exemple suivant montre la procédure de création d’une table externe :

	CREATE EXTERNAL TABLE [dbo].[order_line]( 
		 [ol_o_id] int NOT NULL, 
		 [ol_d_id] tinyint NOT NULL,
		 [ol_w_id] int NOT NULL, 
		 [ol_number] tinyint NOT NULL, 
		 [ol_i_id] int NOT NULL, 
		 [ol_delivery_d] datetime NOT NULL, 
		 [ol_amount] smallmoney NOT NULL, 
		 [ol_supply_w_id] int NOT NULL, 
		 [ol_quantity] smallint NOT NULL, 
		 [ol_dist_info] char(24) NOT NULL 
	) 
	
	WITH 
	( 
		DATA_SOURCE = MyExtSrc, 
	 	SCHEMA_NAME = 'orders', 
	 	OBJECT_NAME = 'order_details', 
		DISTRIBUTION=SHARDED(ol_w_id)
	); 

L’exemple suivant illustre comment récupérer la liste des tables externes à partir de la base de données en cours :

	select * from sys.external_tables; 

## Interrogation 

### 2\.1 Requêtes haute-fidélité T-SQL 

Une fois votre table externe et votre source de données externe définies, vous pouvez utiliser l’ensemble T-SQL complet sur vos tables externes.

**Exemple de partitionnement horizontal** : la requête suivante effectue une jointure tridirectionnelle entre les entrepôts, les commandes et les lignes de commande, et utilise plusieurs agrégats et un filtre sélectif. Il suppose (1) un partitionnement horizontal (partitionnement) et (2) que les entrepôts, les commandes et lignes de commande sont partitionnées par la colonne d’id de l’entrepôt et que la requête élastique peut placer des jointures sur les partitions et traiter la partie coûteuse de la requête sur les partitions en parallèle.

	select  
		 w_id as warehouse,
		 o_c_id as customer,
		 count(*) as cnt_orderline,
		 max(ol_quantity) as max_quantity,
		 avg(ol_amount) as avg_amount, 
		 min(ol_delivery_d) as min_deliv_date
	from warehouse 
	join orders 
	on w_id = o_w_id
	join order_line 
	on o_id = ol_o_id and o_w_id = ol_w_id 
	where w_id > 100 and w_id < 200 
	group by w_id, o_c_id 
 
### 2\.2 Procédure stockée pour l’exécution de T-SQL à distance

La requête élastique introduit également une procédure stockée qui offre un accès direct aux partitions. La procédure stockée est appelée sp\_execute\_remote et peut être utilisée pour exécuter le code T-SQL ou les procédures stockées distantes sur des bases de données distantes. Les paramètres suivants sont pris en compte :
* Nom de la source de données (nvarchar) : nom de la source de données externe de type SGBDR. 
* Requête (nvarchar) : requête T-SQL à exécuter sur chaque partition. 
* Déclaration de paramètre (nvarchar) : facultatif : chaîne contenant des définitions de type de données correspondant aux paramètres utilisés dans le paramètre de requête (par exemple, sp\_executesql). 
* Liste de valeurs de paramètre : facultative : valeurs de paramètre de liste séparées par des virgules (par exemple, sp\_executesql).

sp\_execute\_remote utilise la source de données externe fournie dans les paramètres d’appel pour exécuter l’instruction T-SQL donnée sur toutes les bases de données distantes. Il utilise les informations d’identification de la source de données externe pour se connecter à la base de données shardmap et aux bases de données distantes.

Exemple :

	EXEC sp_execute_remote
		N'MyExtSrc',
		N'select count(w_id) as foo from warehouse' 

## Connectivité des outils  

Utilisez des chaînes de connexion SQL Server standard pour connecter votre application, vos outils d’intégration BI et des données de la base de données avec vos définitions de table externe. Assurez-vous que SQL Server est pris en charge comme source de données pour votre outil. Référencez la base de données de requête élastique comme n’importe quelle autre base de données SQL Server connectée à l’outil et utilisez des tables externes à partir de votre outil ou votre application comme s’il s’agissait de tables locales.

## Meilleures pratiques 

* Assurez-vous que la base de données du point de terminaison de requête élastique est autorisée à accéder à la base de données de mappage de partition et à toutes les partitions via les pare-feu de base de données SQL.  

* Une requête élastique ne valide ni n’applique la distribution de données définie par la table externe. Si la distribution réelle des données est différente de la distribution spécifiée dans la définition de votre table, vos requêtes peuvent donner des résultats inattendus.

* La requête élastique n’effectue pas d’élimination de partition lorsque les prédicats de clé de partitionnement permettent d’exclure en toute sécurité certaines partitions du traitement.

* Une requête élastique est mieux adaptée aux requêtes dont la plus grande partie du calcul peut être effectuée sur les partitions. De manière générale, vous obtenez les meilleures performances de requête avec des prédicats de filtres sélectifs pouvant être évalués sur des partitions ou des jonctions via les clés de partitionnement qui peuvent être effectuées de manière alignée sur toutes les partitions. D’autres modèles de requête peuvent nécessiter le chargement de grandes quantités de données dans le nœud principal, à partir des partitions, ce qui peut nuire aux performances.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->

<!---HONumber=AcomDC_0413_2016-->