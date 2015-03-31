<properties 
	pageTitle="Requête sur plusieurs partitions" 
	description="Exécutez des requêtes entre partitions à l'aide des API de mise à l'échelle flexible." 
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

# Requête sur plusieurs partitions
## Vue d'ensemble
**La requête sur plusieurs partitions** est utilisée pour des tâches telles que la collecte de données/la création de rapports qui nécessitent l'exécution d'une requête qui s'étend sur plusieurs partitions. (Comparez cela au [Routage dépendant des données](./sql-database-elastic-scale-data-dependent-routing.md)qui effectue tout le travail sur une partition unique.) 

La bibliothèque cliente de l'infrastructure élastique introduit un nouvel espace de noms appelé **Microsoft.Azure.SqlDatabase.ElasticScale.Query** qui permet d'interroger plusieurs partitions à l'aide d'une requête et d'un résultat uniques. Elle fournit une abstraction de requête sur une collection de partitions. Elle fournit également des stratégies d'exécution alternatives, en particulier des résultats partiels, permettant de gérer les échecs d'interrogation sur plusieurs partitions.  

Le point d'entrée principal de l'interrogation de plusieurs partitions est la classe **MultiShardConnection**. Comme avec le routage dépendant des données, l'API suit l'expérience familière des classes et méthodes **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient(v=vs.110).aspx)**. Avec la bibliothèque **SqlClient**, la première étape consiste à créer un objet **SqlConnection**, puis à créer une commande **SqlCommand** pour la connexion, et enfin à exécuter la commande via l'une des méthodes **Execute**. Enfin, **SqlDataReader** itère sur les jeux de résultats retournés à partir de l'exécution de la commande. L'expérience avec les API de requête sur plusieurs partitions suit les étapes suivantes : 

1. Créez une **MultiShardConnection**.
2. Créez une commande **MultiShardCommand** pour une **MultiShardConnection**.
3. Exécutez la commande.
4. Utilisez les résultats via le **MultiShardDataReader**. 

La principale différence est la construction de connexions à plusieurs partitions. Où **SqlConnection** fonctionne sur une base de données unique, la **MultiShardConnection** utilise une ***collection de partitions*** comme entrée. Vous pouvez remplir la collection de partitions à partir d'une carte de partitions. La requête est ensuite exécutée sur la collection de partitions à l'aide de la sémantique **UNION ALL** pour assembler un seul résultat global. Le cas échéant, le nom de la partition d'où provient la ligne peut être ajouté à la sortie à l'aide de la propriété sur la commande **ExecutionOptions**. Le code suivant illustre l'utilisation de l'interrogation de plusieurs partitions à l'aide d'une **ShardMap** donnée nommée *myShardMap*. 

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
 

Notez l'appel à **myShardMap.GetShards()**. Cette méthode récupère toutes les partitions de la carte de partitions et offre un moyen facile d'exécuter une requête sur toutes les partitions à partir de cette carte. La collection de partitions pour une requête sur plusieurs partitions peut être affinée davantage en effectuant une requête LINQ sur la collection retournée par l'appel à **myShardMap.GetShards()**. En combinaison avec la stratégie de résultats partiels, la capacité actuelle de l'interrogation de plusieurs partitions a été conçue pour fonctionner correctement avec des dizaines, voire des centaines, de partitions.
Une limitation de l'interrogation de plusieurs partitions est actuellement le manque de validation des partitions et shardlets interrogés. Tandis que le routage dépendant des données vérifie qu'une partition donnée fait partie de la carte de partitions au moment de l'interrogation, les requêtes sur plusieurs partitions n'effectuent pas cette vérification. De ce fait, les requêtes sur plusieurs partitions peuvent s'exécuter sur des partitions qui ont été supprimées de la carte de partitions.

## Requêtes sur plusieurs partitions et opérations de fractionnement/fusion

Les requêtes sur plusieurs partitions ne vérifient pas si les shardlets de la partition interrogée participent à des opérations de fractionnement/fusion en cours. Cela peut entraîner des incohérences avec des lignes du même shardlet qui s'affichent pour plusieurs partitions dans la même requête sur plusieurs partitions. Tenez compte de ces limitations et envisagez de purger les opérations de fractionnement/fusion en cours et les modifications apportées à la carte de partitions lors de l'exécution de requêtes sur plusieurs partitions.

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--HONumber=47-->
