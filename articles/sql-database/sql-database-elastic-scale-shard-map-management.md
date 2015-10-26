<properties 
	pageTitle="Gestion des cartes de partitions" 
	description="Utilisation de ShardMapManager, la bibliothèque cliente de base de données élastique" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="sidneyh"/>

# Gestion des cartes de partitions
Dans un environnement de base de données partitionné, une **carte de partitions** conserve les informations permettant à une application de se connecter à la base de données adéquate, désignée par la valeur de la **clé de partitionnement**. Il est essentiel de comprendre comment ces cartes sont construites pour gérer les partitions avec la bibliothèque cliente des bases de données élastiques.

## Cartes de partitions et mappages de partitions
 
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
Vous pouvez construire des cartes de partition en utilisant des **listes de valeurs de clés de partitionnement individuelles**, ou des **plages de valeurs de clés de partitionnement**.

###Cartes de partition de liste
Les **partitions** contiennent des **shardlets** (micro-partitions) et le mappage de ces shardlets en partitions est tenu à jour par une carte de partitions. Une **carte de partitions de liste** est une association entre les valeurs de clés individuelles identifiant les shardlets et les bases de données qui servent de partitions. Les **mappages de liste** sont explicites (par exemple, la clé 1 mappe vers la base de données A) et différentes valeurs de clés peuvent être mappées vers la même base de données (les valeurs de clés 3 et 6 font toutes deux référence à la base de données B).

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

Chacune des tables présentées ci-dessus correspond à un exemple conceptuel d’un objet **ShardMap**. Chaque ligne correspond à un exemple simplifié d’un objet **PointMapping** (pour la carte de partitions de liste) ou **RangeMapping** (pour la carte de partitions de plage).

## Gestionnaire des cartes de partitions 

Dans la bibliothèque cliente, le gestionnaire des cartes de partitions correspond à une collection de cartes de partitions. Les données gérées par un objet **ShardMapManager** .Net sont conservées dans trois emplacements :

1. **Carte de partitions globale (CPG)** : lorsque vous créez un objet **ShardMapManager**, vous spécifiez une base de données en tant que référentiel pour l’ensemble de ses cartes et mappages de partitions. Les tables spéciales et les procédures stockées sont automatiquement créées pour gérer les informations. Il s'agit généralement d'une petite base de données à laquelle vous pouvez accéder rapidement, mais elle ne doit pas être utilisée pour d'autres besoins de l'application. Les tables sont stockées dans un schéma spécifique nommé **\_\_ShardManagement**.

2. **Carte de partitions locale (CPL)** : chaque base de données que vous définissez en tant que partition au sein d’une carte de partitions sera modifiée pour contenir des petites tables et des procédures stockées spécifiques qui contiennent et gèrent les informations de carte de partitions propres à cette partition. Ces informations sont redondantes pour les informations contenues dans la CPG, mais elles permettent aux applications de valider les informations de carte de partitions mises en cache sans placer aucune charge sur la CPG. L'application utilise la CPL pour déterminer la validité d'un mappage mis en cache. Les tables correspondant à la CPL de chaque partition sont stockées dans le schéma **\_\_ShardManagement**.

3. **Cache d’application** : chaque instance d’application accédant à un objet **ShardMapManager** conserve un cache local en mémoire de ses mappages. Elle stocke les informations de routage récupérées récemment.

## Construction d'un objet ShardMapManager
Un objet **ShardMapManager** est instancié dans l’application à l’aide d’un modèle de fabrique. La méthode **ShardMapManagerFactory.GetSqlShardMapManager** récupère des informations d’identification (incluant le nom du serveur et le nom de base de données contenant la CPG) sous la forme d’un objet **ConnectionString** et renvoie l’instance d’un objet **ShardMapManager**.

L’objet **ShardMapManager** doit être instancié une seule fois par domaine d’application, dans le code d’initialisation d’une application. Un **ShardMapManager** peut contenir n’importe quel nombre de cartes de partitions. Si une carte de partitions peut suffire à de nombreuses applications, il arrive que différents ensembles de bases de données soient utilisés pour différents schémas ou pour des objectifs uniques. Lorsque cela se produit, il est préférable d'employer plusieurs cartes de partitions.

Dans ce code, une application tente d’ouvrir un objet **ShardMapManager** existant. Si des objets représentant un objet **ShardMapManager global** (CPG) n’existent pas encore dans la base de données, ils sont créés ici par la bibliothèque cliente.

    // Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
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

### Informations d'identification de l'administration de carte de partitions

En règle générale, les applications d'administration et de manipulation de cartes de partitions diffèrent des applications acheminant des connexions grâce aux cartes de partitions.

Dans le cas des applications d’administration de cartes de partitions (ajout ou modification de partitions, cartes de partitions, mappages de partitions, etc.), vous devez instancier l’objet **ShardMapManager** en utilisant des **informations d’identification dotées de privilèges de lecture/écriture sur la base de données CPG et sur chaque base de données servant de partition**. Les informations d'identification doivent autoriser l'écriture sur les tables dans la CPG et la CPL à chaque saisie ou modification des informations de carte de partitions, ainsi qu'à chaque création d'une table CPL sur une nouvelle partition.

### Seules les métadonnées sont affectées 

Les méthodes utilisées pour remplir ou modifier les données d’un objet **ShardMapManager** n’affectent pas les données utilisateur stockées dans les partitions. Par exemple, des méthodes telles que **CreateShard**, **DeleteShard**, **UpdateMapping**, etc. affectent uniquement les métadonnées de carte de partitions. Elles ne suppriment pas, n’ajoutent pas ou ni ne modifient les données utilisateur contenues dans les partitions. En revanche, ces méthodes sont conçues pour être utilisées conjointement avec des opérations distinctes effectuées pour créer ou supprimer des bases de données réelles ou qui déplacent des lignes d'une partition à l'autre pour rééquilibrer un environnement partitionné. (L’outil de **fractionnement/fusion** inclus avec les outils de base de données élastique utilise ces applications avec l’orchestration des mouvements de données réels entre les partitions.)

## Remplissage d’une carte de partitions : exemple
 
Vous trouverez ci-dessous un exemple de séquence d'opérations pour remplir une carte de partitions spécifique. Le code suit cette procédure :

1. Une carte de partitions est créée dans un gestionnaire des cartes de partitions. 
2. Les métadonnées de deux partitions différentes sont ajoutées à la carte de partitions. 
3. Différents mappages de plages de clés sont ajoutés et le contenu global de la carte de partitions s’affiche. 

Le code est écrit de façon à ce que l'intégralité de la méthode puisse être réexécutée en toute sécurité en cas d'erreur inattendue. Chaque demande vérifie si une partition ou un mappage existe déjà, avant de démarrer sa création. Le code ci-dessous part du principe que les bases de données nommées **sample\_shard\_0**, **sample\_shard\_1** et **sample\_shard\_2** ont déjà été créées dans le serveur référencé par la chaîne **shardServer**.

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_0"), out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_1"), out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(0, 50), shard0, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(50, 100), shard1, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(100, 150), shard0, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(150, 200), shard1, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                   (new Range<long>(200, 300), shard0, MappingStatus.Online)); 
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

Une fois les cartes de partitions remplies, vous pouvez créer ou adapter des applications pour accéder aux données, afin d'utiliser les cartes. Le remplissage ou la manipulation des cartes ne doit pas survenir tant que la **disposition de la carte** n’a pas été modifiée.

## Routage dépendant des données 

La plupart du temps, le gestionnaire des cartes de partitions est utilisé par des applications devant se connecter à une base de données pour exploiter leurs données. Dans une application partitionnée, ces connexions doivent être associées à la base de données cible adéquate. Cette opération est nommée **Routage dépendant des données**. Pour ces applications, instanciez un objet Gestionnaire des cartes de partitions à partir de la fabrique à l'aide des informations d'identification ayant un accès en lecture seule sur la base de données CPG. Les demandes de connexion individuelles fourniront ultérieurement les informations d’identification requises pour la connexion à la base de données de partitions adéquate.

Notez que ces applications (utilisant **ShardMapManager** ouvert avec des informations d’identification en lecture seule) ne pourront pas modifier les cartes ni les mappages. Pour cela, vous devez créer des applications d'administration ou des scripts PowerShell qui fourniront des informations d'identification dotées de privilèges élevés comme indiqué précédemment.

Pour plus d’informations, consultez [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md).

## Modification d'une carte de partitions 

Vous disposez de plusieurs méthodes pour modifier une carte de partitions. Toutes les méthodes suivantes modifient les métadonnées décrivant les partitions et leurs mappages, mais elles ne modifient pas physiquement les données dans les partitions et ne créent ni ne suppriment les bases de données réelles. Il est possible que certaines opérations de carte de partitions décrites ci-dessous requièrent une coordination à l’aide d’actions d’administration qui déplacent physiquement les données ou qui ajoutent et suppriment des bases de données servant de partitions.

Ces méthodes fonctionnent ensemble en tant que blocs de construction disponibles pour la modification de la distribution globale des données dans votre environnement de base de données partitionnée.

* Pour ajouter ou supprimer des partitions : utilisez **CreateShard** et **DeleteShard**. 
    
    Le serveur et la base de données représentant la partition cible doivent déjà exister pour pouvoir exécuter ces opérations. Ces méthodes n’ont pas d’incidence sur les bases de données elles-mêmes. Elles affectent uniquement les métadonnées de la carte de partitions.

* Pour créer ou supprimer des points ou des plages mappés sur les partitions : utilisez **CreateRangeMapping**, **DeleteMapping**, **CreatePointMapping**.
    
    Vous pouvez mapper différents points ou plages vers la même partition. Ces méthodes affectent uniquement les métadonnées. Elles n'affectent pas les données qui peuvent déjà être présentes dans les partitions. Si vous devez supprimer des données de la base de données par souci de cohérence avec les opérations **DeleteMapping**, vous devez effectuer ces opérations séparément, mais en utilisant ces méthodes.

* Pour diviser des plages existantes en deux ou fusionner des plages adjacentes en une seule : utilisez **SplitMapping** et **MergeMappings**.

    Notez que les opérations de fractionnement/fusion **ne changent pas la partition sur laquelle les valeurs de clé sont mappées**. Un fractionnement divise une plage existante en deux parties, qui sont chacune mappées vers la même partition. Une fusion s'applique sur deux plages adjacentes qui sont déjà mappées vers la même partition, afin d'en faire une plage unique. Le déplacement des points ou des plages entre des partitions doit être coordonné à l’aide de l’objet **UpdateMapping**, conjointement au déplacement des données réelles. Vous pouvez utiliser le service de **fractionnement/fusion**, compris dans les outils de base de données élastique, pour coordonner les modifications de la carte de partitions avec le mouvement des données, lorsque celui-ci est nécessaire.

* Pour remapper (ou déplacer) des points ou des plages vers différentes partitions : utilisez **UpdateMapping**.

    Comme il est possible que les données soient déplacées d’une partition à l’autre afin d’être cohérentes avec les opérations **UpdateMapping**, vous devez effectuer ces déplacements séparément tout en utilisant ces méthodes.

* Pour effectuer des mappages en ligne et hors connexion : utilisez **MarkMappingOffline** et **MarkMappingOnline** pour contrôler l’état en ligne d’un mappage.

    Certaines opérations des mappages de partitions sont autorisées uniquement lorsque l’état d’un mappage est « hors connexion », notamment **UpdateMapping** et **DeleteMapping**. Lorsqu'un mappage est hors connexion, une demande dépendant des données basée sur une clé incluse dans ce mappage renvoie une erreur. En outre, lorsqu'une plage est d'abord mise hors connexion, toutes les connexions vers la partition concernée sont supprimées automatiquement afin d'éviter des résultats incohérents ou incomplets pour les requêtes émises vers les plages en cours de modification.

Les mappages sont des objets immuables dans .Net. Toutes les méthodes ci-dessus qui modifient les mappages invalident également les références à ceux-ci dans votre code. Pour faciliter l’exécution des séquences d'opérations qui modifient l'état d'un mappage, toutes les méthodes qui modifient un mappage renvoient une nouvelle référence de mappage, permettant aux opérations d’être chaînées. Par exemple, pour supprimer un mappage existant dans shardmap sm qui contient la clé 25, vous pouvez exécuter les éléments suivants :

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## Ajout d'une partition 

Souvent, les applications n'ont qu'à ajouter de nouvelles partitions pour gérer des données prévues à partir de nouvelles clés ou plages de clés, pour une carte de partitions qui existe déjà. Par exemple, une application partitionnée par un ID de client peut requérir l'approvisionnement d'une nouvelle partition pour un nouveau client, ou des données partitionnées mensuellement peuvent requérir l'approvisionnement d'une nouvelle partition avant le début de chaque mois.

Si la nouvelle plage de valeurs de clé n'appartient pas déjà à un mappage existant et qu'aucun déplacement de données n'est nécessaire, il est très simple d'ajouter la nouvelle partition et d'associer la nouvelle clé ou la plage à cette partition. Pour plus d’informations sur l’ajout de nouvelles partitions, consultez [Ajout d’une nouvelle partition](sql-database-elastic-scale-add-a-shard.md).

Cependant, pour les scénarios requérant le déplacement de données, l’outil de fusion/fractionnement est requis pour orchestrer le déplacement des données entre les partitions conjointement aux mises à jour nécessaires de la carte de partitions. Pour plus d’informations sur l’utilisation de l’outil de fractionnement/fusion, consultez [Présentation du service de fractionnement/fusion](sql-database-elastic-scale-overview-split-and-merge.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=Oct15_HO3-->