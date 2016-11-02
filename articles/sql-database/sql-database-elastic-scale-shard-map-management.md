<properties 
	pageTitle="Gestion des cartes de partitions | Microsoft Azure" 
	description="Utilisation de ShardMapManager, la bibliothèque cliente de base de données élastique" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="ddove" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/25/2016" 
	ms.author="ddove"/>

# Monter en charge les bases de données avec le Gestionnaire de cartes de partitions

Pour monter facilement en charge les bases de données sur SQL Azure, utilisez un Gestionnaire de cartes de partitions. Le Gestionnaire de cartes de partitions est une base de données spéciale qui gère les informations de mappage global sur toutes les partitions (bases de données) dans un ensemble de partitions. Les métadonnées permettent à une application de se connecter à la base de données qui convient en fonction de la valeur de la **clé de partitionnement**. En outre, chaque partition de l’ensemble contient les cartes qui suivent les données de partitions locales (appelées **shardlets**).

![Gestion des cartes de partitions](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Il est essentiel de comprendre comment ces mappages sont construits pour la gestion du mappage de partition. Utilisez pour cela la [classe ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) située dans la [bibliothèque cliente de bases de données élastiques](sql-database-elastic-database-client-library.md) pour gérer les cartes de partitions.


## Cartes de partitions et mappages de partitions

Pour chaque partition, vous devez sélectionner le type de carte de partitions à créer. Votre choix dépend de l’architecture de la base de données :

1. Client unique par base de données  
2. Plusieurs clients par base de données (deux types) :
	3. Mappage de liste
	4. Mappage de plage
 
Pour un modèle de client unique, créez une carte de partitions de **mappage de liste**. Le modèle à un seul client attribue une base de données par client. Il s’agit d’un modèle efficace pour les développeurs SaaS, car il simplifie la gestion.

![Mappage de liste][1]

Le modèle mutualisé affecte plusieurs clients à une seule base de données (et vous pouvez distribuer des groupes de clients sur plusieurs bases de données). Utilisez ce modèle lorsque vous pensez que chaque client va avoir de faibles besoins en termes de données. Dans ce modèle, nous attribuons une plage de clients à une base de données à l’aide du **mappage de plage**.
 

![Mappage de plage][2]

Vous pouvez également implémenter un modèle de base de données mutualisée à l’aide d’un *mappage de liste* pour affecter plusieurs clients à une base de données unique. Par exemple, DB1 est utilisée pour stocker les informations d’id client 1 et 5 et DB2 stocke les données pour les clients 7 et 10.

![Plusieurs clients sur une base de données unique][3]
 
### Types .Net pris en charge pour les clés de partitionnement

L'infrastructure élastique prend en charge les types .Net Framework suivants en tant que clés de partitionnement :

* integer
* long
* guid
* byte  
* datetime
* timespan
* datetimeoffset

### Liste et cartes de partitions de plage
Vous pouvez construire des cartes de partition en utilisant des **listes de valeurs de clés de partitionnement individuelles** ou des **plages de valeurs de clés de partitionnement**.

###Cartes de partition de liste
Les **partitions** contiennent des **shardlets** (micro-partitions) et le mappage de ces shardlets en partitions est tenu à jour par une carte de partitions. Une **carte de partitions de liste** est une association entre les valeurs de clés individuelles identifiant les shardlets et les bases de données qui servent de partitions. Les **mappages de liste** sont explicites et plusieurs valeurs de clés peuvent être mappées à la même base de données. Par exemple, la clé 1 est mappée à la base de données A et les valeurs de clés 3 et 6 font toutes deux référence à la base de données B.

| Clé | Emplacement de partition |
|-----|----------------|
| 1 | Database\_A |
| 3 | Database\_B |
| 4 | Database\_C |
| 6 | Database\_B |
| ... | ... |
 

### Cartes de partitions de plage 
Dans une **carte de partitions de plage**, la plage de clé est décrite par une paire **[valeur inférieure, valeur supérieure)**, où la *valeur inférieure* correspond à la clé minimale de la plage, tandis que la *valeur supérieure* correspond à la première valeur supérieure à la plage.

Par exemple, **[0, 100)** inclut tous les entiers égaux ou supérieurs à 0 et inférieurs à 100. Notez que plusieurs plages peuvent pointer vers la même base de données et que les plages disjointes sont prises en charge (par exemple, [100,200) et [400,600) pointent vers la base de données C dans l’exemple ci-dessous.)

| Clé | Emplacement de partition |
|-----------|----------------|
| [1,50) | Database\_A |
| [50,100) | Database\_B |
| [100,200) | Database\_C |
| [400,600) | Database\_C |
| ... | ...            

Chacune des tables présentées ci-dessus correspond à un exemple conceptuel d'un objet **ShardMap**. Chaque ligne correspond à un exemple simplifié d'un objet **PointMapping** (pour la carte de partitions de liste) ou **RangeMapping** (pour la carte de partitions de plage).

## Gestionnaire des cartes de partitions 

Dans la bibliothèque cliente, le gestionnaire des cartes de partitions correspond à une collection de cartes de partitions. Les données gérées par une instance **ShardMapManager** sont conservées à trois emplacements :

1. **Carte de partitions globale (GSM)** : vous spécifiez une base de données en tant que référentiel pour l’ensemble de ses cartes et mappages de partitions. Les tables spéciales et les procédures stockées sont automatiquement créées pour gérer les informations. Il s'agit généralement d'une petite base de données à laquelle vous pouvez accéder rapidement, et elle ne doit pas être utilisée pour d'autres besoins de l'application. Les tables sont stockées dans un schéma spécifique nommé **\_\_ShardManagement**.

2. **Carte de partitions locale (LSM)** : chaque base de données que vous définissez en tant que partition est modifiée pour contenir des petites tables et des procédures stockées spécifiques qui renferment et gèrent les informations de carte de partitions propres à cette partition. Ces informations sont redondantes avec les informations contenues dans la GSM et elles permettent aux applications de valider les informations de carte de partitions mises en cache sans placer aucune charge sur la GSM. L'application utilise la LSM pour déterminer la validité d'un mappage mis en cache. Les tables correspondant à la LSM de chaque partition sont également stockées dans le schéma **\_\_ShardManagement**.

3. **Cache d’application** : chaque instance d’application accédant à un objet **ShardMapManager** conserve un cache local en mémoire de ses mappages. Elle stocke les informations de routage récupérées récemment.

## Construction d'un objet ShardMapManager

Un objet **ShardMapManager** est construit à l’aide d’un modèle [factory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). La méthode **[ShardMapManagerFactory.GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)** récupère des informations d’identification (incluant le nom du serveur et le nom de base de données contenant la GSM) sous la forme d’un objet **ConnectionString** et renvoie l’instance d’un objet **ShardMapManager**.

**Remarque** : l’objet **ShardMapManager** doit être instancié une seule fois par domaine d’application, dans le code d’initialisation d’une application. La création d’instances supplémentaires de ShardMapManager dans le même appdomain entraîne l’augmentation de l’utilisation de la mémoire et du processeur de l’application. Un **ShardMapManager** peut contenir n’importe quel nombre de cartes de partitions. Si une carte de partitions peut suffire à de nombreuses applications, il arrive que différents ensembles de bases de données soient utilisés pour différents schémas ou pour des objectifs uniques. Lorsque cela se produit, il est préférable d'employer plusieurs cartes de partitions.

Dans ce code, une application tente d’ouvrir un objet **ShardMapManager** existant avec la [méthode TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx). Si des objets représentant un objet **ShardMapManager** global (GSM) n’existent pas encore dans la base de données, ils y sont créés par la bibliothèque cliente à l’aide de la [méthode CreateSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx).

    // Try to get a reference to the Shard Map Manager 
 	// via the Shard Map Manager database.  
    // If it doesn't already exist, then create it. 
    ShardMapManager shardMapManager; 
    bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

    if (shardMapManagerExists) 
     { 
        Console.WriteLine("Shard Map Manager already exists");
    } 
    else
    {
        // Create the Shard Map Manager. 
        ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
        Console.WriteLine("Created SqlShardMapManager"); 

        shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 
 
Comme alternative, vous pouvez utiliser PowerShell pour créer un gestionnaire de cartes de partitions. Un exemple est disponible [ici](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## Obtenir un objet RangeShardMap ou ListShardMap

Après avoir créé un Gestionnaire de cartes de partition, vous pouvez obtenir l’objet [RangeShardMap](https://msdn.microsoft.com/library/azure/dn807318.aspx) ou [ListShardMap](https://msdn.microsoft.com/library/azure/dn807370.aspx) à l’aide de la méthode [TryGetRangeShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), [TryGetListShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx) ou [GetShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx).

	/// <summary>
    /// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
    /// </summary>
    public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
    {
        // Try to get a reference to the Shard Map.
        RangeShardMap<T> shardMap;
        bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

        if (shardMapExists)
        {
            ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
        }
        else
        {
            // The Shard Map does not exist, so create it
            shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
            ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
        }

        return shardMap;
    } 

### Informations d'identification de l'administration de carte de partitions

Les applications d'administration et de manipulation de cartes de partitions diffèrent des applications acheminant des connexions grâce aux cartes de partitions.

Pour administrer des cartes de partitions (ajouter ou modifier des partitions, cartes de partitions, mappages de partitions, etc.), vous devez instancier l’objet **ShardMapManager** en utilisant des **informations d’identification dotées de privilèges de lecture/écriture sur la base de données GSM et sur chaque base de données servant de partition**. Les informations d'identification doivent autoriser l'écriture sur les tables dans la CPG et la CPL à chaque saisie ou modification des informations de carte de partitions, ainsi qu'à chaque création d'une table CPL sur une nouvelle partition.

Consultez [Informations d’identification utilisées pour accéder à la bibliothèque cliente de la base de données élastique](sql-database-elastic-scale-manage-credentials.md).

### Seules les métadonnées sont affectées 

Les méthodes utilisées pour remplir ou modifier les données d'un objet **ShardMapManager** n'affectent pas les données utilisateur stockées dans les partitions. Par exemple, des méthodes telles que **CreateShard**, **DeleteShard**, **UpdateMapping**, etc. affectent uniquement les métadonnées de carte de partitions. Elles ne suppriment pas, n’ajoutent pas ou ni ne modifient les données utilisateur contenues dans les partitions. En revanche, ces méthodes sont conçues pour être utilisées conjointement avec des opérations distinctes effectuées pour créer ou supprimer des bases de données réelles ou qui déplacent des lignes d'une partition à l'autre pour rééquilibrer un environnement partitionné. (L’outil de **fractionnement/fusion** inclus avec les outils de base de données élastique utilise ces applications avec l’orchestration des mouvements de données réels entre les partitions.) Consultez [Mise à l’échelle utilisant l’outil de fractionnement et de fusion de bases de données élastiques](sql-database-elastic-scale-overview-split-and-merge.md).

## Exemple de remplissage d'une carte de partitions
 
Vous trouverez ci-dessous un exemple de séquence d'opérations pour remplir une carte de partitions spécifique. Le code suit cette procédure :

1. Une carte de partitions est créée dans un gestionnaire des cartes de partitions. 
2. Les métadonnées de deux partitions différentes sont ajoutées à la carte de partitions. 
3. Différents mappages de plages de clés sont ajoutés et le contenu global de la carte de partitions s’affiche. 

Le code est écrit de sorte que la méthode peut être réexécutée si une erreur se produit. Chaque requête vérifie si une partition ou un mappage existe déjà, avant d'essayer de le créer. Le code part du principe que les bases de données nommées **sample\_shard\_0**, **sample\_shard\_1** et **sample\_shard\_2** ont déjà été créées dans le serveur référencé par la chaîne **shardServer**.

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // Check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(
	                                 shardServer, 
	                                 "sample_shard_0"), 
	                                 out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(
	                                        shardServer, 
	                                        "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(
	                                shardServer, 
	                                "sample_shard_1"), 
	                                out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(
	                                         shardServer, 
	                                        "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                      new RangeMappingCreationInfo<long>
	                      (new Range<long>(0, 50), 
	                      shard0, 
	                      MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(50, 100), 
	                     shard1, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long>
                         (new Range<long>(100, 150), 
	                     shard0, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(150, 200), 
	                     shard1, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(200, 300), 
	                     shard0, 
	                     MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                         .OrderBy(s => s.Location.DataSource)
                         .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 
 
En guise d'alternative, vous pouvez utiliser des scripts PowerShell pour obtenir le même résultat. Certains exemples d’extraits de codes PowerShell sont disponibles [ici](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Une fois les cartes de partitions remplies, vous pouvez créer ou adapter des applications pour accéder aux données, afin d'utiliser les cartes. Le remplissage ou la manipulation des cartes ne doit pas survenir tant que la **disposition de la carte** n'a pas été modifiée.

## Routage dépendant des données 

Le gestionnaire des cartes de partitions est principalement utilisé par des applications devant se connecter à une base de données pour exploiter leurs données. Ces connexions doivent être associées à la base de données correcte. Cette opération est nommée **Routage dépendant des données**. Pour ces applications, instanciez un objet Gestionnaire des cartes de partitions à partir de la fabrique à l'aide des informations d'identification ayant un accès en lecture seule sur la base de données CPG. Les requêtes de connexion individuelles ultérieures fournissent les informations d'identification requises pour la connexion à la base de données de partitions adéquate.

Ces applications (utilisant **ShardMapManager** ouvert avec des informations d’identification en lecture seule) ne peuvent pas modifier les cartes ni les mappages. Pour cela, vous devez créer des applications d'administration ou des scripts PowerShell qui fourniront des informations d'identification dotées de privilèges élevés comme indiqué précédemment. Consultez [Informations d’identification utilisées pour accéder à la bibliothèque cliente de la base de données élastique](sql-database-elastic-scale-manage-credentials.md).

Pour plus d'informations, consultez [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md).

## Modification d'une carte de partitions 

Vous disposez de plusieurs méthodes pour modifier une carte de partitions. Toutes les méthodes suivantes modifient les métadonnées décrivant les partitions et leurs mappages, mais elles ne modifient pas physiquement les données dans les partitions et ne créent ni ne suppriment les bases de données réelles. Il est possible que certaines opérations de carte de partitions décrites ci-dessous requièrent une coordination à l’aide d’actions d’administration qui déplacent physiquement les données ou qui ajoutent et suppriment des bases de données servant de partitions.

Ces méthodes fonctionnent ensemble en tant que blocs de construction disponibles pour la modification de la distribution globale des données dans votre environnement de base de données partitionnée.

* Pour ajouter ou supprimer des partitions : utilisez **[CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)** et **[DeleteShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)** de la [classe Shardmap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx). 
    
    Le serveur et la base de données représentant la partition cible doivent déjà exister pour pouvoir exécuter ces opérations. Ces méthodes n’ont pas d’incidence sur les bases de données elles-mêmes. Elles affectent uniquement les métadonnées de la carte de partitions.

* Pour créer ou supprimer des points ou des plages mappés sur les partitions : utilisez **[CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn841993.aspx)**, **[DeleteMapping](https://msdn.microsoft.com/library/azure/dn824200.aspx)** de la [classe RangeShardMapping](https://msdn.microsoft.com/library/azure/dn807318.aspx), et **[CreatePointMapping](https://msdn.microsoft.com/library/azure/dn807218.aspx)** de [ListShardMap](https://msdn.microsoft.com/library/azure/dn842123.aspx)
    
    Vous pouvez mapper différents points ou plages vers la même partition. Ces méthodes affectent uniquement les métadonnées. Elles n'affectent pas les données qui peuvent déjà être présentes dans les partitions. Si vous devez supprimer des données de la base de données par souci de cohérence avec les opérations **DeleteMapping**, vous devez effectuer ces opérations séparément, mais en utilisant ces méthodes.

* Pour diviser des plages existantes en deux ou pour fusionner des plages adjacentes en une seule : utilisez **[SplitMapping](https://msdn.microsoft.com/library/azure/dn824205.aspx)** et **[MergeMappings](https://msdn.microsoft.com/library/azure/dn824201.aspx)**.

    Notez que les opérations de fractionnement/fusion **ne changent pas la partition sur laquelle les valeurs de clé sont mappées**. Un fractionnement divise une plage existante en deux parties, qui sont chacune mappées vers la même partition. Une fusion s'applique sur deux plages adjacentes qui sont déjà mappées vers la même partition, afin d'en faire une plage unique. Le déplacement des points ou des plages entre des partitions doit être coordonné à l’aide de l’objet **UpdateMapping**, conjointement au déplacement des données réelles. Vous pouvez utiliser le service de **fractionnement/fusion**, compris dans les outils de base de données élastique, pour coordonner les modifications de la carte de partitions avec le mouvement des données, lorsque celui-ci est nécessaire.

* Pour remapper (ou déplacer) des points ou des plages vers différentes partitions : utilisez **[UpdateMapping](https://msdn.microsoft.com/library/azure/dn824207.aspx)**.

    Comme il est possible que les données soient déplacées d'une partition à l'autre afin d'être cohérentes avec les opérations **UpdateMapping**, vous devez effectuer ces déplacements séparément tout en utilisant ces méthodes.

* Pour effectuer des mappages en ligne et hors connexion : utilisez **[MarkMappingOffline](https://msdn.microsoft.com/library/azure/dn824202.aspx)** et **[MarkMappingOnline](https://msdn.microsoft.com/library/azure/dn807225.aspx)** pour contrôler l’état en ligne d’un mappage.

    Certaines opérations des mappages de partitions sont autorisées uniquement lorsque l'état d'un mappage est « hors connexion », notamment **UpdateMapping** et **DeleteMapping**. Lorsqu'un mappage est hors connexion, une demande dépendant des données basée sur une clé incluse dans ce mappage renvoie une erreur. En outre, lorsqu'une plage est d'abord mise hors connexion, toutes les connexions vers la partition concernée sont supprimées automatiquement afin d'éviter des résultats incohérents ou incomplets pour les requêtes émises vers les plages en cours de modification.

Les mappages sont des objets immuables dans .Net. Toutes les méthodes ci-dessus qui modifient les mappages invalident également les références à ceux-ci dans votre code. Pour faciliter l’exécution des séquences d'opérations qui modifient l'état d'un mappage, toutes les méthodes qui modifient un mappage renvoient une nouvelle référence de mappage, permettant aux opérations d’être chaînées. Par exemple, pour supprimer un mappage existant dans shardmap sm qui contient la clé 25, vous pouvez exécuter les éléments suivants :

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## Ajout d'une partition 

Souvent, les applications n'ont qu'à ajouter de nouvelles partitions pour gérer des données prévues à partir de nouvelles clés ou plages de clés, pour une carte de partitions qui existe déjà. Par exemple, une application partitionnée par un ID de client peut requérir l'approvisionnement d'une nouvelle partition pour un nouveau client, ou des données partitionnées mensuellement peuvent requérir l'approvisionnement d'une nouvelle partition avant le début de chaque mois.

Si la nouvelle plage de valeurs de clé n'appartient pas déjà à un mappage existant et qu'aucun déplacement de données n'est nécessaire, il est très simple d'ajouter la nouvelle partition et d'associer la nouvelle clé ou la plage à cette partition. Pour plus d’informations sur l’ajout de nouvelles partitions, consultez [Ajout d’une nouvelle partition](sql-database-elastic-scale-add-a-shard.md).

Cependant, pour les scénarios requérant le déplacement de données, l’outil de fusion/fractionnement est requis pour orchestrer le déplacement des données entre les partitions conjointement aux mises à jour nécessaires de la carte de partitions. Pour plus d'informations sur l'utilisation de l'outil de fractionnement/fusion, consultez [Présentation du service de fractionnement/fusion](sql-database-elastic-scale-overview-split-and-merge.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png

<!---HONumber=AcomDC_0525_2016-->