<properties
   pageTitle="Conversion de bases de données existantes pour utiliser les outils de base de données élastique"
   description="Conversion de bases de données partitionnées pour utiliser les outils de base de données élastique en créant un gestionnaire de cartes de partitions"
   services="sql-database"
   documentationCenter=""
   authors="SilviaDoomra"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/29/2016"
   ms.author="SilviaDoomra"/>

# Conversion de bases de données existantes pour utiliser les outils de base de données élastique

Si vous disposez déjà d’une solution partitionnée mise à l’échelle, vous pouvez tirer parti des outils de base de données élastique en utilisant les techniques décrites ici. Après conversion, vous pouvez utiliser la [bibliothèque cliente de la base de données élastique](sql-database-elastic-database-client-library.md) et l’[outil de fusion et de fractionnement de la base de données élastique](sql-database-elastic-scale-overview-split-and-merge.md).

Ces techniques peuvent être implémentées à l’aide de la [bibliothèque cliente .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), ou les scripts PowerShell sur [Azure SQL DB - Scripts d’outils de base de données élastique](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Les exemples fournis ici utilisent les scripts PowerShell.

Il se compose de quatre étapes :

1. Préparer la base de données pour le gestionnaire de cartes de partitions.
2. Créer la carte de partitions.
3. Préparer les partitions individuelles.  
2. Ajouter les mappages à la carte de partitions.

Pour plus d’informations sur la classe ShardMapManager, consultez la page [Gestion des cartes de partitions](sql-database-elastic-scale-shard-map-management.md). Pour obtenir une présentation des outils de bases de données élastiques, consultez la rubrique [Vue d’ensemble des fonctionnalités de base de données élastique](sql-database-elastic-scale-introduction.md).

## Préparation de la base de données du gestionnaire de cartes de partitions
Vous pouvez utiliser une base de données nouvelle ou existante en tant que gestionnaire de cartes de partitions. Cette opération ne doit être exécutée qu’une seule fois.

## Étape 1 : créer un gestionnaire de cartes de partitions
Notez qu’une base de données agissant en tant que gestionnaire de cartes de partitions ne doit pas être la même base de données qu’une partition.

	# Create a shard map manager. 
	New-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 
	#<server_name> and <smm_db_name> are the server name and database name 
	# for the new or existing database that should be used for storing 
	# tenant-database mapping information.

### Pour récupérer le gestionnaire de cartes de partitions

Après la création, vous pouvez récupérer le gestionnaire de cartes de partitions avec cette applet de commande. Cette étape est nécessaire chaque fois que vous devez utiliser l’objet ShardMapManager.

	# Try to get a reference to the Shard Map Manager  
	$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 

  
## Étape 2 : créer une carte de partitions

Vous pouvez créer l’un des modèles suivants :

1. Client unique par base de données 
2. Plusieurs clients par base de données (deux types) :
	3. Mappage de plage
	4. Mappage de liste
 

Si vous utilisez un modèle de base de données à un seul client, utilisez le mappage de liste. Le modèle à un seul client attribue une base de données par client. Il s’agit d’un modèle efficace pour les développeurs SaaS, car il simplifie la gestion.

![Mappage de liste][1]

En revanche, le modèle de base de données mutualisée affecte plusieurs clients à une base de données (et vous pouvez distribuer des groupes de clients sur plusieurs bases de données). Il s’agit d’un modèle viable quand la quantité de données par client est supposée être faible. Dans ce modèle, nous attribuons une plage de clients à une base de données à l’aide du *mappage de plage*.
 

![Mappage de plage][2]

Vous pouvez également implémenter un modèle de base de données mutualisée à l’aide d’un mappage de liste pour affecter plusieurs clients à une base de données unique. Par exemple, DB1 est utilisée pour stocker les informations d’id client 1 et 5 et DB2 stocke les données pour les clients 7 et 10.

![Plusieurs clients sur une base de données unique][3]


## Étape 2, option 1 : créer une carte de partitions pour un mappage de liste
Créez une carte de partitions à l’aide de l’objet ShardMapManager.

	# $ShardMapManager is the shard map manager object. 
	$ShardMap = New-ListShardMap -KeyType $([int]) 
	-ListShardMapName 'ListShardMap' 
	-ShardMapManager $ShardMapManager 
 
 
## Étape 2, option 2 : créer une carte de partitions pour un mappage de plage

Notez que pour utiliser ce modèle de mappage, les valeurs d’id client doivent être des plages continues. De plus, il est raisonnable d’avoir un écart dans les plages en ignorant simplement la plage pendant la création de bases de données.

	# $ShardMapManager is the shard map manager object 
	# 'RangeShardMap' is the unique identifier for the range shard map.  
	$ShardMap = New-RangeShardMap 
	-KeyType $([int]) 
	-RangeShardMapName 'RangeShardMap' 
	-ShardMapManager $ShardMapManager 

## Étape 2, l’option 3 : mappages de liste sur une base de données unique
La configuration de ce modèle nécessite également la création d’un mappage de liste comme indiqué à l’étape 2, option 1.

## Étape 3 : préparer les partitions individuelles

Ajoutez chaque partition (base de données) dans le gestionnaire de cartes de partitions. Cela prépare les bases de données individuelles à stocker les informations de mappage. Exécutez cette méthode sur chaque partition.
	 
	Add-Shard 
	-ShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>'
	# The $ShardMap is the shard map created in step 2.
 

## Étape 4 : ajouter des mappages

L’ajout de mappages varie selon le type de carte de partitions que vous avez créé. Si vous avez créé un mappage de liste, vous ajoutez des mappages de liste. Si vous avez créé un mappage de plage, vous ajoutez des mappages de plage.

### Étape 4, option 1 : mapper les données pour un mappage de liste

Mappez les données en ajoutant un mappage de liste pour chaque client.

	# Create the mappings and associate it with the new shards 
	Add-ListMapping 
	-KeyType $([int]) 
	-ListPoint '<tenant_id>' 
	-ListShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 

### Étape 4, option 2 : mapper les données pour un mappage de plage

Ajoutez les mappages de plage pour la plage d’id client – associations de base de données :

	# Create the mappings and associate it with the new shards 
	Add-RangeMapping 
	-KeyType $([int]) 
	-RangeHigh '5' 
	-RangeLow '1' 
	-RangeShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 


### Étape 4, option 3 : mapper les données de plusieurs clients sur une base de données unique

Pour chaque client, exécutez la commande Add-ListMapping (option 1, ci-dessus).


## Vérification des mappages

Vous pouvez interroger les informations sur les partitions existantes et les mappages associés à l’aide des commandes suivantes :

	# List the shards and mappings 
	Get-Shards -ShardMap $ShardMap 
	Get-Mappings -ShardMap $ShardMap 

## Résumé

Une fois que vous avez terminé l’installation, vous pouvez commencer à utiliser la bibliothèque cliente de base de données élastique. Vous pouvez également utiliser le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) et la [requête sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md).

## Étapes suivantes


Obtenez les scripts PowerShell à partir de [scripts d’outils de base de données élastique Azure SQL DB](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Les outils sont également sur GitHub : [Azure/flexible-db-tools](https://github.com/Azure/elastic-db-tools).

Utilisez l’outil de fractionnement et de fusion pour déplacer des données, à partir d’un modèle mutualisé ou vers celui-ci, vers un modèle de client unique. Consultez la page [Outil de fractionnement et de fusion](sql-database-elastic-scale-get-started.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 

<!---HONumber=AcomDC_0330_2016-->