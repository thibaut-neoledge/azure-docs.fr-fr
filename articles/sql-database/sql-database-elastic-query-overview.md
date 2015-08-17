<properties
    pageTitle="Vue d’ensemble de la requête de base de données élastique Azure SQL Database"
    description="Vue d’ensemble de la fonctionnalité de requête élastique"
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/09/2015"
    ms.author="sidneyh" />

# Vue d’ensemble de la requête de base de données élastique Azure SQL Database (en version préliminaire)

Le **fonctionnalité de requête de base de données élastique**, en version préliminaire, vous permet d’exécuter une requête Transact-SQL qui s’étend sur plusieurs bases de données dans Azure SQL Database. Elle permet de connecter des outils Microsoft et tiers (Excel, PowerBI, Tableau, etc.) à des couches Données avec plusieurs bases de données, en particulier lorsque ces bases de données partagent un même schéma (également appelé partitionnement horizontal). Cette fonctionnalité permet de mettre à l’échelle des requêtes à des couches Données de grande taille dans la base de données SQL et de visualiser les résultats dans les rapports de business intelligence (BI).

Pour créer une application de requête de base de données élastique, consultez [Prise en main des requêtes de base de données élastique](sql-database-elastic-query-getting-started.md).

## Requêtes de base de données élastique : scénarios

L’objectif d’une requête de base de données élastique est de simplifier les scénarios de création de rapports dans lesquels plusieurs bases de données fournissent des lignes, regroupées au sein d’un résultat global unique. Cette requête peut être composée de manière directe par l’utilisateur ou l’application, ou de manière indirecte, via des outils connectés à la base de données élastique associée aux requêtes. Ceci est particulièrement utile lors de la création de rapports, à l’aide des outils commerciaux d’intégration BI ou de données, ou tout logiciel qui ne peut pas être modifié. Avec une requête de base de données élastique, vous pouvez interroger facilement plusieurs bases de données à l’aide de l’expérience de connectivité SQL Server familière des outils tels qu’Excel, PowerBI, Tableau ou Cognos.

La requête de base de données élastique permet également d’accéder facilement à un ensemble complet de bases de données via des requêtes émises par SQL Server Management Studio ou Visual Studio, et facilite l’interrogation des diverses bases de données à partir d’Entity Framework ou d’autres environnements ORM. La figure 1 illustre un scénario dans lequel une application cloud existante (qui utilise la bibliothèque d’outils de base de données élastique) s’appuie sur une couche Données mise à l’échelle et une requête de base de données élastique est utilisée pour les rapports entre plusieurs bases de données.

**Figure 1**

![Requête de base de données élastique utilisée sur la couche Données montée en charge][1]

La couche Données est répartie sur plusieurs bases de données à l’aide d’un schéma commun. Cette approche est également connue sous le terme « partitionnement horizontal ». Le partitionnement peut être effectué et géré (1) à l’aide de la [bibliothèque de client de base de données élastique](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) ou (2) en utilisant un modèle spécifique à l’application pour la distribution des données entre plusieurs bases de données. Avec cette topologie, les rapports doivent souvent s’étendre sur plusieurs bases de données. Avec une requête de base de données élastique, vous pouvez désormais vous connecter à une seule base de données SQL et les résultats de la requête provenant de bases de données distantes s’affichent comme s’ils étaient générés à partir d’une base de données virtuelle unique.

> [AZURE.NOTE]Une requête de base de données élastique est mieux adaptée aux scénarios de création de rapports occasionnels où la plus grande partie du traitement peut s’effectuer sur la couche données. Pour les charges de travail de création de rapports intensives ou les scénarios d’entreposage de données avec des requêtes plus complexes, pensez aussi à utiliser [Azure SQL Data Warehouse](http://azure.microsoft.com/services/sql-data-warehouse/).


## Topologie de requête de base de données élastique

Dans le cadre d’une requête de base de données élastique pour effectuer des tâches de création de rapports sur une couche Données partitionnée horizontalement, la carte de partition de l’infrastructure élastique doit représenter les bases de données de la couche Données. En règle générale, une seule carte de partitions est utilisée dans ce scénario et une base de données dotée de capacités de requête de base de données élastique sert de point d’entrée pour les requêtes avec création de rapport. Seule cette base de données dédiée doit être configurée avec des objets de requête de base de données élastique, comme décrit ci-dessous. La figure 2 illustre cette topologie et sa configuration avec la base de données de requête de base de données élastique et la carte de partitions.

> [AZURE.NOTE]La base de données de requête de la base de données élastique dédiée doit être une base de données SQL DB v12 et seul le niveau Premium est initialement pris en charge. Il n’existe aucune restriction concernant les partitions.

**Figure 2**

![Utiliser une requête de base de données élastique pour la création de rapports sur des niveaux partitionnés][2]

(Un **shardlet** est l’ensemble de toutes les données associées à une valeur unique d’une clé de partitionnement sur une partition. Une **clé de partitionnement** est une valeur de colonne qui détermine comment les données sont réparties entre les partitions. Par exemple, la clé de partitionnement des données distribuées par régions peut être des identifiants de région. Pour plus d’informations, consultez le [Glossaire d’infrastructure élastique](sql-database-elastic-scale-glossary.md).)


Au fil du temps, des topologies supplémentaires seront prises en charge par la fonctionnalité de requête de base de données élastique. Cet article sera mis à jour de manière à refléter les nouvelles fonctionnalités dès qu’elles seront disponibles.

## Activation des requêtes élastiques grâce à la configuration d’une carte de partitionnement

La création d’une solution de requête de base de données élastique nécessite que la [**carte de partitions**](sql-database-elastic-scale-shard-map-management.md) des outils de base de données élastique représente les bases de données à distance en fonction d’une requête de base de données élastique. Si vous utilisez déjà la bibliothèque cliente de base de données élastique, vous pouvez utiliser votre carte de partitions existante. Autrement, vous devez créer une carte de partitions à l’aide d’outils de base de données élastique.

L’exemple de code C# suivant illustre comment créer une carte de partitions avec une seule base de données à distance ajoutée comme partition.

    ShardMapManagerFactory.CreateSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerCreateMode.ReplaceExisting, RetryBehavior.DefaultRetryBehavior);
    smm = ShardMapManagerFactory.GetSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerLoadPolicy.Lazy,
      RetryBehavior.DefaultRetryBehavior);
    map = smm.CreateRangeShardMap<int>("yourshardmapname");
    shard = map.CreateShard(new ShardLocation("yoursqldbserver", "yoursqldbdatabasename"));

Pour plus d’informations sur les cartes de partitions, reportez-vous à la rubrique [Gestion Shardmap](sql-database-elastic-scale-shard-map-management.md).

Pour utiliser la fonctionnalité de requête de base de données élastique, vous devez tout d’abord créer la carte de partitions et enregistrer vos bases de données distantes en tant que partitions. Cette opération ne doit être exécutée qu’une seule fois. Vous devez simplement modifier votre carte de partitions lors de l’ajout ou de la suppression des bases de données à distance. Il s’agit d’opérations incrémentielles sur une carte de partitions existante.


## Création d’objets de base de données de requête de base de données élastique

Pour décrire les tables distantes accessibles à partir d’un point de terminaison de requête de base de données élastique, nous offrons désormais la possibilité de définir des tables externes qui apparaissent comme des tables locales pour les outils de votre application et les outils tiers. Les requêtes peuvent être envoyées par rapport à ces objets de base de données implicitement via les outils ou explicitement à partir de SQL Server Management Studio, Visual Studio Data Tools ou à partir d’une application. La requête de base de données élastique s’exécute comme toute autre instruction Transact-SQL. La différence notable est que cette requête permet de distribuer le traitement sur de nombreuses bases de données distantes susceptibles de contenir des objets externes sous-jacents.

La fonctionnalité de requête de base de données élastique repose sur ces quatre instructions DDL. En règle générale, ces instructions DDL sont utilisées une seule fois ou très rarement, lorsque le schéma de votre application change.

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
*    [CREATE CREDENTIAL](https://msdn.microsoft.com/library/ms189522.aspx)
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### Clé principale et informations d’identification de la base de données

Les informations d’identification sont constituées de l’ID utilisateur et du mot de passe que la requête de base de données élastique utilise pour se connecter à votre carte de partitions de l’infrastructure élastique et à vos bases de données distantes dans Azure SQL DB. Vous pouvez créer la clé principale requise et les informations d’identification à l’aide de la syntaxe suivante :

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';  
    CREATE CREDENTIAL <credential_name> ON DATABASE
    WITH IDENTITY = '<shard_map_username>',
    SECRET = '<shard_map_password>'
     [;]
Veillez à ce que &lt;shard\_map\_username> ne contienne pas le suffixe « @servername ».

Vous pouvez utiliser la syntaxe suivante pour supprimer la clé principale et les informations d’identification :

    DROP CREDENTIAL <credential_name> ON DATABASE;
    DROP MASTER KEY;  

### Sources de données externes

À l’étape suivante, vous devez enregistrer votre carte de partitions comme source de données externe. La syntaxe pour créer et supprimer des sources de données externes est définie comme suit :

      CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
                     (TYPE = SHARD_MAP_MANAGER,
                     LOCATION = '<fully_qualified_server_name>',
                     DATABASE_NAME = '<shardmap_database_name>',
                     CREDENTIAL = <credential_name>,
                     SHARD_MAP_NAME = '<shardmapname>'
    ) [;]

Vous pouvez utiliser l’instruction suivante pour supprimer une source de données externe :

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

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

Notez que les mêmes informations d’identification sont utilisées pour lire la carte de partitions et pour accéder aux données sur les bases de données à distance lors du traitement de la requête.


### Tables externes

Grâce à la requête de base de données élastique, nous étendons la syntaxe de la table externe existante jusqu’à des tables qui sont partitionnées sur plusieurs bases de données à distance dans Azure SQL DB. En utilisant le concept source de données externe susmentionné, la syntaxe permettant la création et la suppression de tables externes se définit comme suit :

    CREATE EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    )[;]

    <sharded_external_table_options> ::=
          DATA_SOURCE = <External_Data_Source>,
          DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED | ROUND_ROBIN

La stratégie de partitionnement vérifie si une table est traitée comme une table partitionnée ou comme une table répliquée. Avec une table partitionnée, les données de différentes partitions ne se chevauchent pas. Les tables répliquées, quant à elles, possèdent les mêmes données sur chaque partition. Le processeur de requêtes s’appuie sur ces informations pour traiter correctement et plus efficacement les requêtes. La distribution par tourniquet (Round Robin) indique qu’une méthode spécifique d’application pour la distribution des données de cette table est utilisée.

    DROP EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name[;]

Les autorisations **CREATE/DROP EXTERNAL TABLE**: ALTER ANY EXTERNAL DATA SOURCE sont nécessaires. Elles le sont également pour faire référence à la source de données sous-jacente.

**Exemple** : l’exemple suivant montre comment créer une table externe :

    CREATE EXTERNAL TABLE [dbo].[order_line](
        [ol_o_id] [int] NOT NULL,
        [ol_d_id] [tinyint] NOT NULL,
        [ol_w_id] [int] NOT NULL,
        [ol_number] [tinyint] NOT NULL,
        [ol_i_id] [int] NOT NULL,
        [ol_delivery_d] [datetime] NOT NULL,
        [ol_amount] [smallmoney] NOT NULL,
        [ol_supply_w_id] [int] NOT NULL,
        [ol_quantity] [smallint] NOT NULL,
        [ol_dist_info] [char](24) NOT NULL
    )
    WITH
    (
        DATA_SOURCE = MyExtSrc,
        DISTRIBUTION=SHARDED(ol_w_id)
    );

L’exemple suivant illustre comment récupérer la liste des tables externes à partir de la base de données en cours :

    select * from sys.external_tables;


## Rapports et requête

### Requêtes
Après avoir défini votre source de données externe et vos tables externes, vous pouvez utiliser les chaînes de connexion familières de la base de données SQL pour vous connecter à la base de données dont la fonction de requête de base de données élastique est activée. Vous pouvez maintenant exécuter des requêtes complètes en lecture seule sur vos tables externes, avec certaines restrictions expliquées dans la [section limitations](#preview-limitations) ci-dessous.

**Exemple** : la requête suivante effectue une jonction tridirectionnelle entre les entrepôts, les commandes et les lignes de commande, et utilise plusieurs agrégats et un filtre sélectif. En supposant que les entrepôts, les commandes et les lignes de commande sont partitionnés en fonction de la colonne d’identification des entrepôts, une requête de base de données élastique peut rassembler les jonctions sur les bases de données distantes et monter en charge le traitement de la partie coûteuse de la requête.

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

### Procédure stockée SP\_ EXECUTE\_FANOUT

SP\_EXECUTE\_FANOUT est une procédure stockée qui fournit l’accès aux bases de données représentées par une carte de partitions. La procédure stockée accepte les paramètres suivants :

-    **Nom du serveur** (nvarchar) : nom qualifié complet du serveur logique hébergeant la carte de partitions.
-    **Nom de la base de données de la carte de partitions** (nvarchar) : nom de la base de données de la carte de partitions.
-    **Nom d’utilisateur** (nvarchar) : nom d’utilisateur pour vous connecter à la base de données de la carte de partitions et aux bases de données distantes.
-    **Mot de passe** (nvarchar) : mot de passe de l’utilisateur.
-    **Nom de la carte de partitions** (nvarchar) : nom de la carte de partitions à utiliser pour la requête.
-    **Requête** : requête à exécuter sur chaque partition.

Les informations de la carte de partitions fournies dans les paramètres d’appel sont utilisées pour exécuter l’instruction donnée sur toutes les partitions enregistrées avec la carte de partitions. Tous les résultats sont fusionnés à l’aide de la sémantique UNION ALL semblable aux requêtes sur plusieurs partitions. Le résultat inclut également la colonne « virtuelle » supplémentaire avec le nom de la base de données distante.

Notez que les mêmes informations d’identification sont utilisées pour se connecter à la base de données de la carte de partitions et aux partitions.

**Exemple** :

    sp_execute_fanout 'myserver.database.windows.net', N'ShardMapDb', N'myuser', N'MyPwd', N'ShardMap', N'select count(w_id) as foo from warehouse'

## Connectivité des outils
Vous pouvez utiliser des chaînes de connexion SQL DB familières pour votre base de données de requête de base de données élastique qui vous permettront de connecter vos outils d’intégration BI et de données. Assurez-vous que SQL Server est pris en charge comme source de données pour votre outil. Puis utilisez les objets externes dans la base de données de requête la base de données élastique, comme pour n’importe quelle autre base de données SQL Server à laquelle vous vous connecteriez avec votre outil.

## Meilleures pratiques
*    Assurez-vous que la base de données du Gestionnaire de cartes de partitions et les bases de données définies dans la carte de partitions permettent l’accès à partir de Microsoft Azure dans leurs règles de pare-feu. Cela est nécessaire pour que la base de données de la requête de base de données élastique puisse s’y connecter. Pour en savoir plus, consultez [Pare-feu Azure SQL DB](https://msdn.microsoft.com/library/azure/ee621782.aspx).
*    Une requête de base de données élastique ne valide ni n’applique la distribution de données définie par la table externe. Si la distribution réelle des données est différente de la distribution spécifiée dans la définition de votre table, vos requêtes peuvent donner des résultats inattendus.
*    Une requête de base de données élastique est mieux adaptée aux requêtes dont la plus grande partie du calcul peut être effectuée sur les partitions. De manière générale, vous obtenez les meilleures performances de requête avec des prédicats de filtres sélectifs pouvant être évalués sur des partitions ou des jonctions via les clés de partitionnement qui peuvent être effectuées de manière alignée sur toutes les partitions. D’autres modèles de requête peuvent nécessiter le chargement de grandes quantités de données dans le nœud principal, à partir des partitions, ce qui peut nuire aux performances.

## Coût

La requête de base de données élastique est incluse dans le coût des bases de données Azure SQL Database. Notez que les topologies sont prises en charge lorsque les bases de données distantes se trouvent dans un centre de données autre que le point de terminaison de requête de base de données élastique. Cependant, le chargement de sortie des données depuis les bases de données distantes correspond à un taux de sortie Azure régulier.

## Limitations de la version préliminaire

La version préliminaire nécessite de tenir compte de certains éléments :

*    La fonctionnalité de requête de base de données élastique n’est initialement disponible que pour le niveau de performances SQL DB v12 Premium. Cependant, les bases de données distantes accessibles via une requête de base de données élastique peuvent être de n’importe quel niveau.
* Les tables externes référencées par la source de données externe ne prennent en charge que les opérations de lecture sur les bases de données distantes. Vous pouvez toutefois pointer les fonctionnalités Transact-SQL complètes au niveau de la base de données de la requête de base de données élastique où réside la définition de la table externe. Cela peut être utile, par exemple, pour conserver les résultats temporaires à l’aide de SELECT column\_list INTO local\_table ou pour définir des procédures stockées dans la base de données de requête de la base de données élastique qui font référence à des tables externes.
*    À l’heure actuelle, les paramètres dans les requêtes ne peuvent pas être distribués aux bases de données distantes. Les requêtes paramétrables doivent placer toutes les données dans le nœud de tête et peuvent pâtir de mauvaises performances en fonction de la taille des données. Une solution temporaire consiste à éviter les paramètres dans vos requêtes ou à utiliser l’option RECOMPILE pour remplacer automatiquement les paramètres par leurs valeurs actuelles.
* À l’heure actuelle, les statistiques des colonnes via les tables externes ne sont pas prises en charge.
* Actuellement, la requête de base de données élastique n’effectue pas d’élimination de partition lorsque les prédicats de clé de partitionnement permettent d’exclure en toute sécurité certaines bases de données distantes du traitement. Par conséquent, les requêtes concerneront toujours toutes les bases de données distantes représentées par les sources de données externes de la requête.
* Toutes les requêtes qui impliquent des jonctions entre les tables de bases de données différentes peuvent ramener un grand nombre de lignes vers la base de données de la requête de base de données élastique afin d’être traitées. Cela entraîne un coût au niveau des performances. Il est préférable de développer des requêtes qui peuvent être traitées localement sur chaque base de données distante ou d’utiliser des clauses WHERE pour limiter les lignes de chaque base de données avant d’effectuer la jonction.
*    La syntaxe utilisée pour la définition de métadonnées de requête de la base de données élastique change pendant la version préliminaire.
*    À l’heure actuelle, la fonction de script de Transact-SQL dans SSMS ou SSDT ne fonctionne pas avec les objets de requête de base de données élastique.

## Commentaires
Veuillez nous faire part de vos commentaires et de votre expérience sur Disqus ou Stackoverflow. Nous souhaitons connaître votre avis concernant le service (défauts, améliorations possibles, lacunes).

## Étapes suivantes
Pour commencer à explorer une requête élastique, essayez notre didacticiel par étape et découvrez un exemple opérationnel complet en quelques minutes : [Prise en main des requêtes de base de données élastique](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png

<!--anchors-->

<!---HONumber=August15_HO6-->