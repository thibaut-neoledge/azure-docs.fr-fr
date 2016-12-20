---
title: "Requête sur plusieurs partitions | Microsoft Docs"
description: "Exécutez des requêtes en utilisant la bibliothèque cliente des bases de données élastiques."
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: a4379c15-f213-4026-ab6f-a450ee9d5758
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2016
ms.author: torsteng
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d616160e8547e256da7f445e79f0949496ca943f


---
# <a name="multi-shard-querying"></a>Requête sur plusieurs partitions
## <a name="overview"></a>Vue d'ensemble
Avec les [outils des bases de données élastiques](sql-database-elastic-scale-introduction.md), vous pouvez créer des solutions de base de données partitionnée. **Requête sur plusieurs partitions** est utilisée pour des tâches telles que la collecte de données / la création de rapports qui nécessitent l'exécution d'une requête qui s'étend sur plusieurs partitions. (Comparez cela au [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)qui effectue tout le travail sur une partition unique.) 

## <a name="overview"></a>Vue d’ensemble
1. Obtenez un objet [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) ou [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) à l’aide de la méthode [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx) ou [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx). Consultez les rubriques [**Construction d’un objet ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) et [**Obtenir un objet RangeShardMap ou ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Créez un objet **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)**.
3. Créez un objet **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
4. Définissez la **[propriété CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** sur une commande T-SQL.
5. Exécutez la commande en appelant la **[méthode ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
6. Affichez les résultats à l’aide de la **[classe MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Exemple
Le code suivant illustre l'utilisation de la requête sur plusieurs partitions à l'aide d'une **ShardMap** donnée nommée *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                 } 
           } 
    } 


La principale différence est la construction de connexions à plusieurs partitions. Lorsque **SqlConnection** fonctionne sur une base de données unique, l’objet **MultiShardConnection** utilise une ***collection de partitions*** comme entrée. Remplissez la collection de partitions à partir d'une carte de partitions. La requête est ensuite exécutée sur la collection de partitions à l'aide de la sémantique **UNION ALL** pour assembler un seul résultat global. Le nom de la partition d'où provient la ligne peut éventuellement être ajouté à la sortie à l'aide de la propriété sur la commande **ExecutionOptions** . 

Notez l'appel à **myShardMap.GetShards()**. Cette méthode récupère toutes les partitions de la carte de partitions et offre un moyen facile d’exécuter une requête sur toutes les bases de données concernées. La collection de partitions pour une requête sur plusieurs partitions peut être affinée davantage en effectuant une requête LINQ sur la collection retournée par l'appel à **myShardMap.GetShards()**. En combinaison avec la stratégie de résultats partiels, la capacité actuelle de l'interrogation de plusieurs partitions a été conçue pour fonctionner correctement avec des dizaines, voire des centaines, de partitions.

Une limitation de l'interrogation de plusieurs partitions est actuellement le manque de validation des partitions et shardlets interrogés. Tandis que le routage dépendant des données vérifie qu'une partition donnée fait partie de la carte de partitions au moment de l'interrogation, les requêtes sur plusieurs partitions n'effectuent pas cette vérification. De ce fait, les requêtes sur plusieurs partitions peuvent s’exécuter sur des bases de données qui ont été supprimées de la carte de partitions.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Requêtes sur plusieurs partitions et opérations de fractionnement et de fusion
Les requêtes sur plusieurs partitions ne vérifient pas si les shardlets de la base de données interrogée participent à des opérations de fractionnement et de fusion en cours. (Consultez [Mise à l’échelle utilisant l’outil de fractionnement et de fusion de bases de données élastiques](sql-database-elastic-scale-overview-split-and-merge.md).) Cela peut entraîner des incohérences avec des lignes du même shardlet qui s’affichent pour plusieurs bases de données dans la même requête sur plusieurs partitions. Tenez compte de ces limitations et envisagez de purger les opérations de fractionnement et de fusion en cours et les modifications apportées à la carte de partitions lors de l’exécution de requêtes sur plusieurs partitions.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Voir aussi
Classes et méthodes **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)**.

Gérez les partitions en utilisant la [bibliothèque cliente des bases de données élastiques](sql-database-elastic-database-client-library.md). Inclut un espace de noms appelé [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) qui permet d’interroger plusieurs partitions à l’aide d’une requête et d’un résultat uniques. Elle fournit une abstraction de requête sur une collection de partitions. Elle fournit également des stratégies d'exécution alternatives, en particulier des résultats partiels, permettant de gérer les échecs d'interrogation sur plusieurs partitions.  




<!--HONumber=Nov16_HO3-->


