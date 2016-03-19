<properties
    pageTitle="Requête de base de données élastique pour les requêtes de bases de données croisées (partitionnement vertical) | Microsoft Azure"
    description="configuration de requêtes de bases de données croisées sur les partitions verticales"    
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/06/2016"
    ms.author="torsteng;sidneyh" />

# Requête de base de données élastique pour les requêtes de base de données croisée (partitionnement vertical)

Ce document explique comment configurer des requêtes élastiques pour les scénarios de requête de base de données croisées (partitionnement vertical). Pour avoir une définition du scénario de partitionnement horizontal, consultez [Vue d’ensemble d’une requête de base de données élastique SQL Azure (version préliminaire)](sql-database-elastic-query-overview.md).

![Requête sur plusieurs tables dans des bases de données différentes][1]

## Création d’objets de base de données

Pour les scénarios partitionnés verticalement, la requête élastique étend la DDL T-SQL actuelle afin de faire référence à des tables stockées dans des bases de données distantes. Cette section fournit une vue d’ensemble des instructions DDL servant à configurer une requête élastique pour un accès transparent aux tables distantes. Ces instructions DDL permettent de créer une représentation des métadonnées de vos tables distantes dans la base de données locale.

**Remarque** : Contrairement au partitionnement horizontal, ces instructions DDL ne dépendent pas de la définition d’une couche de données avec un mappage de partition via la bibliothèque client de base de données élastique.

La définition des objets de base de données pour une requête de base de données élastique s’appuie sur les instructions T-SQL suivantes, expliquées plus loin, qui exécutent le scénario de partitionnement vertical ci-dessous :

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
    
Pour supprimer les informations d'identification :
    
    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
Assurez-vous que *< username>* ne contient pas le suffixe *“@servername”*.

### 1\.2 Sources de données externes

Vous devez fournir les informations sur vos bases de données à distance pour la requête élastique en définissant des sources de données externes. La syntaxe pour créer et supprimer des sources de données externes est définie comme suit :

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

Notez le paramètre TYPE qui définit cette source de données en tant que système de gestion de base de données relationnelle (SGBDR).

Vous pouvez utiliser l’instruction suivante pour supprimer une source de données externe :

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

#### Autorisations de CREATE/DROP EXTERNAL DATA SOURCE 

L’utilisateur doit posséder l’autorisation ALTER ANY EXTERNAL DATA SOURCE. Cette autorisation est incluse dans l’autorisation ALTER DATABASE.

**Exemple**

L’exemple suivant illustre l’utilisation de l’instruction CREATE pour les sources de données externes.

	CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
	WITH 
	( 
		TYPE=RDBMS, 
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ReferenceData', 
		CREDENTIAL= SqlUser 
	); 
 
Pour récupérer la liste des sources de données externes actuelles à partir de la vue de catalogue suivante :

    select * from sys.external_data_sources; 

### 1\.3 Tables externes 

La requête élastique étend la syntaxe de la table externe existante pour définir des tables externes qui utilisent des sources de données externes de type SGBDR. Une définition de table externe pour le partitionnement vertical couvre les aspects suivants :

* **Schéma** : la table externe DDL définit un schéma que vos requêtes peuvent utiliser. Le schéma fourni dans votre définition de la table externe doit correspondre au schéma des tables appartenant à la base de données externe sur lesquelles sont stockées les données réelles. 

* **Base de données distante** : la table externe DDL fait référence à une source de données externe. La source de données externe spécifie le nom du serveur logique et le nom de la base de données distante dans laquelle sont stockées les données réelles du tableau.

La syntaxe permettant de créer des tables externes à l’aide de sources de données externes comme indiqué dans la section précédente est la suivante :

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
	{ WITH ( <rdbms_external_table_options> ) } 
	)[;] 
	
	<rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

La clause DATA\_SOURCE définit la source de données externe (par exemple, la base de données distante en cas de partitionnement horizontal) utilisée pour la table externe.

Les clauses SCHEMA\_NAME et OBJECT\_NAME offrent la possibilité de mapper une définition de table externe sur une table dans un autre schéma base de données, ou sur une table portant un autre nom, respectivement. Cela s’avère utile si vous souhaitez définir une table externe sur une vue de catalogue ou une DMV sur votre base de données distante, ou dans toute autre situation dans laquelle le nom de table distant est déjà utilisé en local.

L’instruction DDL suivante supprime une définition de table externe existante du catalogue local. Elle n’affecte pas la base de données distante.

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

Les autorisations **CREATE/DROP EXTERNAL TABLE** : ALTER ANY EXTERNAL DATA SOURCE sont nécessaires à la table DDL externe. Elles le sont également pour faire référence à la source de données sous-jacente.

**Considérations de sécurité :** les utilisateurs ayant accès à la table externe acquièrent un accès automatique aux tables distantes sous-jacentes avec les informations d’identification fournies dans la définition de source de données externe. Vous devez gérer l’accès à la table externe avec beaucoup d’attention pour éviter une élévation de privilèges non souhaitée par le biais d’informations d’identification de la source de données externe. Les autorisations SQL standard permettent de GRANT (OCTROYER) ou de REVOKE (RÉVOQUER) l’accès à une table externe comme s’il s’agissait d’une table normale.


 **Exemple** : l’exemple suivant montre la procédure de création d’une table externe :

	CREATE EXTERNAL TABLE [dbo].[customer]( 
		[c_id] int NOT NULL, 
		[c_firstname] nvarchar(256) NULL, 
		[c_lastname] nvarchar(256) NOT NULL, 
		[street] nvarchar(256) NOT NULL, 
		[city] nvarchar(256) NOT NULL, 
		[state] nvarchar(20) NULL, 
		[country] nvarchar(50) NOT NULL, 
	) 
	WITH 
	( 
	       DATA_SOURCE = RemoteReferenceData 
	); 

L’exemple suivant illustre comment récupérer la liste des tables externes à partir de la base de données en cours :

	select * from sys.external_tables; 

## Interrogation

### 2\.1 Requêtes haute-fidélité T-SQL 

Une fois votre table externe et votre source de données externe définies, vous pouvez utiliser l’ensemble T-SQL complet sur vos tables externes.

**Exemple de partitionnement vertical** : la requête suivante effectue une jointure tridirectionnelle entre les deux tables locales pour les commandes, et les lignes de commande et la table distante pour les clients. Voici un exemple de cas d’utilisation de données de référence pour requête élastique :

	SELECT  	
	 c_id as customer,
	 c_lastname as customer_name,
	 count(*) as cnt_orderline, 
	 max(ol_quantity) as max_quantity,
	 avg(ol_amount) as avg_amount,
	 min(ol_delivery_d) as min_deliv_date
	FROM customer 
	JOIN orders 
	ON c_id = o_c_id
	JOIN  order_line 
	ON o_id = ol_o_id and o_c_id = ol_c_id
	WHERE c_id = 100

  
## Connectivité des outils

Vous pouvez utiliser des chaînes de connexion SQL Server standard pour connecter votre BI et vos outils d’intégration aux bases de données sur le serveur SQL DB dont la requête élastique est activée et les tables externes définies. Assurez-vous que SQL Server est pris en charge comme source de données pour votre outil. Reportez-vous ensuite à la requête de base de données élastique et à ses tables externes comme s’il s’agissait de n’importe quelle autre base de données SQL Server à laquelle vous vous connectez avec votre outil.

## Meilleures pratiques 
 
* Assurez-vous que la base de données du point de terminaison de requête élastique est autorisée à accéder à la base de données distante en autorisant l’accès des Services Azure dans sa configuration de pare-feu SQL DB. Vérifiez également que les informations d’identification fournies dans la définition de source de données externe peuvent se connecter à la base de données distante et qu’elles bénéficient des autorisations d’accès à la table distante.  

* Une requête élastique est mieux adaptée aux requêtes dont la plus grande partie du calcul peut être effectuée sur les bases de données distantes. De manière générale, vous obtenez les meilleures performances de requête avec des prédicats de filtres sélectifs pouvant être évalués sur les bases de données ou des jointures distantes pouvant être exécutées en totalité sur la base de données distante. D’autres modèles de requête peuvent nécessiter le chargement de grandes quantités de données de la base de données distante et s’exécuter de façon médiocre.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->

<!---HONumber=AcomDC_0107_2016-->