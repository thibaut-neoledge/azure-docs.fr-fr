<properties title="Data Dependent Routing" pageTitle="Élasticité des partitions" description="Explains concepts and gives examples behind shard elasticity, the ability to scale out Azure SQL databases easily." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale, elasticity" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Élasticité des partitions 

**L'élasticité des partitions** permet aux développeurs d'applications d'augmenter et de réduire dynamiquement les ressources des bases de données selon les besoins, permettant ainsi l'optimisation des performances des applications ainsi que la réduction des coûts. La combinaison de l'infrastructure élastique de base de données SQL Azure avec les [niveaux de service De base, Standard et Premium](http://msdn.microsoft.com/fr-fr/library/azure/dn741340.aspx) fournit des scénarios d'élasticité très intéressants.  L'infrastructure élastique permet une **mise à l'échelle horizontale**. Il s'agit d'un modèle de conception dans lequel des bases de données (nommées " partitions " dans le [glossaire consacré à l'infrastructure élastique](sql-database-elastic-scale-glossary.md)) sont ajoutées ou supprimées à partir d'un **ensemble de partitions** afin d'augmenter ou de réduire la capacité. De même, les niveaux de service des bases de données SQL fournissent des fonctionnalités de **mise à l'échelle verticale** permettant d'augmenter ou de diminuer les ressources d'une seule base de données afin de correspondre à la demande.  Ensemble, la mise à l'échelle verticale d'une partition unique et la mise à l'échelle horizontale de plusieurs partitions offrent aux développeurs d'applications un environnement très flexible capable d'évoluer pour répondre aux besoins de performances, de capacité et d'optimisation des coûts.

### Exemple de mise à l'échelle horizontale : pic lors de la vente de billets de concert

Un scénario complet de mise à l'échelle horizontale consiste en une application qui traite les transactions de tickets de concert. Avec un volume client normal, l'application utilise des ressources de bases de données minimales pour gérer les transactions d'achat.  Toutefois, lorsque les tickets sont mis en vente pour un concert populaire, une seule base de données ne peut pas gérer le pic de demande des clients. 

Pour traiter l'augmentation considérable de transactions, l'application évolue horizontalement. L'application peut désormais distribuer la charge de transactions entre plusieurs partitions. Lorsque les ressources supplémentaires ne sont plus nécessaires, la couche Base de données est réduite, pour une utilisation normale. Ici la mise à l'échelle horizontale permet à une application de monter en charge pour répondre à la demande des clients et de réduire lorsque les ressources ne sont plus nécessaires.   

### Exemple de mise à l'échelle verticale : télémétrie

Un scénario complet pour la mise à l'échelle verticale consiste en une application qui utilise un **ensemble de partitions** pour stocker la télémétrie opérationnelle. Dans ce scénario, il est préférable de colocaliser toutes les données de télémétrie d'une journée sur une partition unique. Dans cette application, les données de la date du jour sont réceptionnées dans une partition et une nouvelle partition est approvisionnée pour les jours suivants. Les données opérationnelles peuvent ensuite être classées dans la catégorie " anciennes " et interrogées selon les besoins. 

Pour recevoir les données de télémétrie à des charges élevées, il est préférable d'employer un niveau de service plus élevé. En d'autres termes, une base de données Premium est préférable à une base de données De base. Une fois que la base de données a atteint sa capacité maximale, elle bascule de la réception à l'analyse et la création de rapports. Pour ce faire, les performances du niveau Standard sont égales à la tâche. Il est donc possible de mettre à l'échelle verticalement vers le bas le niveau de service sur les partitions (hormis celui créé le plus récemment) afin d'ajuster les exigences de performances inférieures pour les données plus anciennes. 

La mise à l'échelle verticale permet également d'augmenter les performances d'une base de données unique afin d'optimiser les performances. Exemple : une application d'enregistrement des taxes. Au moment de l'enregistrement, il est préférable de conserver toutes les données d'un seul client sur la même base de données et d'augmenter les performances de cette partition. Selon l'application, la mise à l'échelle verticale des ressources est avantageuse pour optimiser les coûts et performances requis. 

Ensemble, les mises à l'échelle horizontale et verticale de la couche Base de données sont au cœur des applications d'infrastructure élastique et de l'élasticité des partitions. 

Ce document fournit le contexte pour les scénarios d'élasticité des partitions, ainsi que des références aux exemples d'implémentation des mises à l'échelle horizontale et verticale. 

## Mécanismes d'élasticité des partitions 

La mise à l'échelle horizontale ou verticale est une fonction à trois éléments : 

1. **Télémétrie**
2. **Règle**
3. **Action**   

## <a name="telemetry"> </a>Télémétrie

**L'élasticité pilotée par les données** est au cœur d'une application d'infrastructure élastique. En fonction des exigences de performances, utilisez la télémétrie pour prendre des décisions pilotées par les données de mise à l'échelle verticale ou horizontale.  

#### Sources des données de télémétrie
Dans le contexte de la base de données SQL Azure, il existe un certain nombre de sources clés pouvant être utilisées comme sources de données pour l'élasticité des partitions. 

1. **La télémétrie des performances** est exposée sous la forme de durées de cinq minutes dans la vue **sys.resource_stats**
2. La télémétrie de capacité de base de données **horaire** est exposée via la vue **sys.resource_usage**.  

Vous pouvez analyser l'utilisation des ressources de performances en interrogeant la base de données MASTER à l'aide de la requête suivante où " Shard_20140623 " est le nom de la base de données cible. 

    SELECT TOP 10 *  
    FROM sys.resource_stats  
    WHERE database_name = 'Shard_20140623'  
    ORDER BY start_time DESC 

**La télémétrie des performances** peut être résumée sur une période de temps (sept jours dans la requête ci-dessous) afin de supprimer les comportements temporaires. 

    SELECT  
    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
        max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
        avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent', 
        max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent', 
        avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
        max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
        avg(active_session_count) AS 'Average # of Sessions', 
        max(active_session_count) AS 'Maximum # of Sessions', 
        avg(active_worker_count) AS 'Average # of Workers', 
        max(active_worker_count) AS 'Maximum # of Workers' 
    FROM sys.resource_stats  
    WHERE database_name = ' Shard_20140623' AND start_time > DATEADD(day, -7, GETDATE()); 

**La capacité de la base de données** peut être mesurée avec une requête similaire par rapport à la vue **sys.resource_usage**. Le nombre maximal de la colonne **storage_in_megabytes** donne la taille actuelle de la base de données. Ce type de télémétrie est utile pour la mise à l'échelle horizontale d'une application lorsqu'une partition spécifique atteint sa capacité maximale. 

    SELECT TOP 10 * 
    FROM [sys].[resource_usage] 
    WHERE database_name = 'Shard_20140623'  
    ORDER BY time DESC 

Comme les données sont réceptionnées dans une partition spécifique, il est utile de choisir un jour dans le futur et de déterminer si la partition a une capacité suffisante pour gérer la charge de travail de ce jour. Bien qu'il ne s'agisse pas d'une véritable implémentation d'une régression linéaire, la requête suivante retourne le delta maximal de la capacité de la base de données entre deux jours consécutifs.  Ce type de télémétrie peut ensuite être appliqué à une règle qui déclenche une action (ou non action). 

    WITH MaxDatabaseDailySize AS( 
        SELECT 
            ROW_NUMBER() OVER (ORDER BY convert(date, [time]) DESC) as [Order], 
            CONVERT(date,[time]) as [date],  
            MAX(storage_in_megabytes) as [MaxSizeDay] 
        FROM [sys].[resource_usage] 
        WHERE  
            database_name = 'Shard_20140623' 
        GROUP BY CONVERT(date,[time]) 
        ) 
    
    SELECT 
        MAX(ISNULL(Size.[MaxSizeDay] - PreviousDaySize.[MaxSizeDay], 0)) 
    FROM  
        MaxDatabaseDailySize Size INNER JOIN 
        MaxDatabaseDailySize PreviousDaySize ON Size.[order]+1 = PreviousDaySize.[order] 
    WHERE 
        Size.[order] < 8 

## <a name="rule"></a>Règle  

La règle est le moteur de décision qui détermine si une action est entreprise. Certaines règles sont très simples, mais d'autres sont beaucoup plus complexes. Comme indiqué dans l'extrait de code ci-dessous, une règle axée sur la capacité peut être configurée afin que lorsqu'une partition atteint $SafetyMargin, par exemple 80 % de sa capacité maximale, une nouvelle partition est approvisionnée.

    # Déterminer si la taille actuelle de la base de données ajoutée à la taille maximale du delta quotidien est supérieure au seuil 
    if( ($CurrentDbSizeMb + $MaxDbDeltaMb) -gt ($MaxDbSizeMb * $SafetyMargin))  
    {#provision new shard} 

Étant donné les sources de données ci-dessus, plusieurs règles peuvent être formulées afin d'effectuer de nombreux scénarios d'élasticité des partitions. 

## <a name="action"></a>Action  

En fonction du résultat de la règle, l'action (ou non action) est le résultat. Les deux actions les plus courantes sont :

* L'augmentation ou la diminution du niveau de service ou du niveau de performances de la partition 
* L'ajout d'une partition à un ensemble de partitions ou sa suppression.

Notez que, dans les solutions de mises à l'échelle horizontale et verticale, le résultat d'une action n'est pas immédiat. Lors de la mise à l'échelle verticale, par exemple, l'émission de la commande ALTER DATABASE pour augmenter le niveau de service d'une base de données De base à une base de données Premium prend un temps variable. La durée dépend en grande partie de la taille de la base de données (pour plus d'informations, consultez la rubrique [Modification des niveaux de service et des niveaux de performances](http://msdn.microsoft.com/library/azure/dn369872.aspx). De même, l'allocation ou l'approvisionnement d'une nouvelle partition n'est pas instantané. Par conséquent, pour les mises à l'échelle horizontale et verticale, les applications doivent prendre en compte le temps différent de zéro pour la modification ou l'approvisionnement d'une nouvelle base de données.  

## Exemple de scénario d'élasticité des partitions 

L'exemple illustré dans la figure ci-dessous présente deux scénarios d'infrastructure élastique : 
1. mise à l'échelle horizontale d'une carte de partitions 
2. mise à l'échelle verticale d'une partition spécifique.  

![Operationl Data Ingestion][1]

Pour mettre à l'échelle horizontalement, une règle (basée sur la date ou la taille de la base de données) est utilisée pour approvisionner une nouvelle partition et l'inscrire avec la carte de partitions, augmentant ainsi horizontalement la couche Base de données. Pour mettre à l'échelle verticalement, une deuxième règle est implémentée selon laquelle toute partition qui est antérieure à un jour repasse de l'édition Premium à l'édition Standard ou De base. 

Revenez au scénario de télémétrie : les partitions de l'application par date. Le scénario collecte les données de télémétrie en permanence, nécessitant une édition hautes performances au moment du chargement, mais des performances inférieures lorsque les données deviennent anciennes. Les données du jour [Tnow] sont écrites dans une base de données hautes performances (Premium). Lorsque l'horloge atteint minuit, le partitionnement du jour précédent (désormais [T-1]) n'est plus utilisé pour la réception. Les données en cours sont réceptionnées par le [Tnow] en cours. Avant le jour suivant, une nouvelle partition doit être approvisionnée et inscrite avec la carte de partitions ([T+1]).  

Cette opération peut être réalisée par l'approvisionnement d'une nouvelle partition avant chaque nouveau jour ou d'une nouvelle partition lorsque la partition actuelle ([Tnow]) approche de sa capacité maximale. L'utilisation de ces méthodes permet de conserver la localité des données pour toute télémétrie écrite pour un jour spécifique. Le partitionnement par heure peut également être appliqué pour une granularité plus fine. Une fois qu'une nouvelle partition est approvisionnée, et étant donné que [T-1] est utilisé pour l'interrogation et la création de rapports, il est souhaitable de diminuer le niveau de performances de la base de données afin de réduire les coûts. Lorsque le contenu des bases de données devient ancien, le niveau de performances peut être davantage réduit et/ou le contenu des bases de données peut être archivé dans Azure Storage ou supprimé en fonction de l'application. 

## Exécution des scénarios d'élasticité des partitions  

Pour faciliter l'implémentation réelle des scénarios de mise à l'échelle horizontale ou verticale, des [exemples de scripts d'élasticité des partitions](http://go.microsoft.com/?linkid=9862617) ont été créés et publiés dans le centre de scripts. Conçus pour s'exécuter dans le service Azure Automation, les Runbook PowerShell fournissent plusieurs méthodes qui interagissent avec les bibliothèques clientes de l'infrastructure élastique et la base de données SQL Azure.  En s'appuyant sur ces exemples de code ou en les extrayant, vous pouvez concevoir les scripts requis pour automatiser les scénarios de mise à l'échelle horizontale et/ou verticale pour votre application. 


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-elasticity/data-ingestion.png

<!--anchors-->
[Telemetry]:#telemetry
[Rule]:#rule
[Action]:#action
