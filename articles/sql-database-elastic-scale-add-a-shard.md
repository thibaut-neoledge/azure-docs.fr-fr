<properties 
	pageTitle="Ajout d'une partition à une application avec infrastructure élastique" 
	description="Utilisation des API avec infrastructure élastique pour ajouter de nouvelles partitions à un ensemble de partitions." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="torsteng@microsoft.com"/>

# Ajout d'une partition à une application avec infrastructure élastique 


## Ajout d'une partition à une nouvelle plage ou clé  

Souvent, les applications n'ont qu'à ajouter de nouvelles partitions pour gérer des données prévues à partir de nouvelles clés ou plages de clés, pour une carte de partitions qui existe déjà. Par exemple, une application partitionnée par un ID de client peut requérir l'approvisionnement d'une nouvelle partition pour un nouveau client, ou des données partitionnées mensuellement peuvent requérir l'approvisionnement d'une nouvelle partition avant le début de chaque mois. 

Si la nouvelle plage de valeurs de clé n'appartient pas déjà à un mappage existant, il est très simple d'ajouter la nouvelle partition et d'associer la nouvelle clé ou la plage à cette partition. 

### Exemple :  Ajout d'une partition et de sa plage à un mappage de partition existant
Dans l'exemple ci-dessous, une base de données nommée **sample_shard_2** et tous les objets de schéma nécessaires qu'elle contient ont été créés pour contenir la plage [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


## Ajout d'une partition pour une partie vide d'une plage existante  

Il peut arriver que vous ayez déjà mappé une plage à une partition et l'ayez partiellement remplie avec des données, mais que vous souhaitiez maintenant que les données futures soient dirigées vers une autre partition. Par exemple, vous partitionnez par plage de jours et avez déjà alloué 50 jours à une partition, mais le jour 24, vous souhaitez que les données futures soient dirigées vers une autre partition. Le [service de fusion et de fractionnement](./sql-database-elastic-scale-overview-split-and-merge.md) de la version préliminaire de l'infrastructure élastique peut effectuer cette opération, mais s'il n'est pas nécessaire de déplacer des données (par exemple, les données de la plage de jours [25, 50], c'est-à-dire le jour 25 inclus et le jour 50 exclu, qui n'existe pas encore) vous pouvez effectuer cela entièrement en utilisant directement les API de gestion de carte de partition.

### Exemple :  Fractionnement d'une plage et affectation de la partie vide à une partition nouvellement ajoutée

Une base de données nommée " sample_shard_2 " et tous les objets de schéma nécessaires qu'elle contient ont été créés.  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Important** :  Utilisez cette technique seulement si vous êtes certain que la plage de mappage mis à jour est vide.  Les méthodes ci-dessus ne vérifient pas les données de la plage déplacée, il est donc préférable d'inclure des vérifications dans votre code.  S'il existe des lignes dans la plage déplacée, la distribution des données réelle ne correspondra pas à la carte des partitions mise à jour. Dans ces cas, utilisez plutôt le [Service de fusion et de fractionnement](./sql-database-elastic-scale-overview-split-and-merge.md) pour effectuer cette opération.  


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--HONumber=47-->
