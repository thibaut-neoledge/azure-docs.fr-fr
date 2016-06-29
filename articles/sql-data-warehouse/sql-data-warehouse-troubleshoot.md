<properties
   pageTitle="Résolution des problèmes d’Azure SQL Data Warehouse | Microsoft Azure"
   description="Résolution des problèmes d’Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/13/2016"
   ms.author="mausher;sonyama;barbkess"/>

# Résolution des problèmes d’Azure SQL Data Warehouse
Cette rubrique répertorie certains des problèmes les plus courants auxquels vous pourriez être confronté avec Azure SQL Data Warehouse.

## Connectivité
Exemples de problèmes de connectivité courants :

- Les règles du pare-feu ne sont pas définies
- Les outils/protocoles ne sont pas pris en charge

### Règles de pare-feu
Les bases de données SQL Azure sont protégées par des pare-feu au niveau du serveur et de la base de données pour s’assurer que seules les adresses IP connues ont accès à une base de données. Les pare-feu sont sécurisés par défaut, ce qui signifie que vous devez activer explicitement une adresse IP ou une plage d’adresses avant de vous connecter. Pour configurer votre pare-feu pour l’accès, suivez les étapes décrites dans la section de [configuration de l’accès au pare-feu du serveur pour l’adresse IP de votre client][] des [instructions d’approvisionnement][].

### Les outils/protocoles ne sont pas pris en charge
SQL Data Warehouse recommande l’utilisation de [Visual Studio 2013 ou 2015][] pour interroger vos données. Pour la connectivité client, il est recommandé d’utiliser [SQL Server Native Client 10/11 (ODBC)][]. SQL Server Management Studio (SSMS) n’est pas encore pris en charge et, bien qu’elle fonctionne partiellement, l’arborescence de l’Explorateur d’objets ne fonctionne pas avec SQL Data Warehouse ; la requête peut également s’exécuter si vous ignorez certains messages d’erreur.

## Performances des requêtes

Vous pouvez apporter quelques modifications de base à votre conception de base de données pour obtenir des performances de requête optimales avec votre instance SQL Data Warehouse. Pour bien comprendre les performances de vos requêtes, vous pouvez consulter l’article relatif à la [surveillance de vos requêtes][]. Pour accélérer l’exécution d’une requête, il suffit parfois simplement d’ajouter davantage de puissance de calcul à vos requêtes en procédant à une [mise à l’échelle de votre base de données SQL Data Warehouse][]. Nous vous invitons à lire l’article [Meilleures pratiques relatives à SQL Data Warehouse][], qui décrit l’ensemble de ces solutions d’optimisation.

Voici quelques-unes des causes les plus courantes des problèmes de performances de requêtes.

### Statistiques

Les [statistiques][] de vos tables contiennent des informations sur la plage et la fréquence de valeurs dans une colonne ou une combinaison de colonnes de base de données. Le moteur de requête utilise ces statistiques pour optimiser l'exécution des requêtes et améliorer leurs performances. Contrairement à SQL Server ou la base de données SQL, SQL Data Warehouse ne prend pas en charge les statistiques de création ou de mise à jour automatiques. Les statistiques doivent être manuellement gérées sur toutes les tables. Pour savoir comment gérer vos statistiques et identifier des tables qui requièrent des statistiques, consultez l’article [Gérer des statistiques dans SQL Data Warehouse][].

### Conception de tables

Lors de la conception de votre base de données SQL Data Warehouse, vous devez principalement veiller à [choisir une clé de distribution par hachage adaptée à votre table][] et à choisir la [conception de vos tables][]. Dès que vous serez bien familiarisé avec SQL Data Warehouse et que vous chercherez à en optimiser les performances, veillez à comprendre les concepts présentés dans ces articles.

### Qualité du segment Columnstore en cluster

La qualité du segment Columnstore en cluster est importante pour garantir des performances de requête optimales au niveau des tables Columnstore en cluster. La qualité du segment peut être mesurée par le nombre de lignes dans un groupe de lignes compressé. La requête suivante identifiera les tables comportant un segment d’index Columnstore dégradé et générera le code T-SQL pour recréer l'index Columnstore sur ces tables. La première colonne de ce résultat de requête vous donnera le code T-SQL pour reconstruire chaque index. La seconde colonne fournira une recommandation concernant la classe minimum de ressources à utiliser pour optimiser la compression.
 
**ÉTAPE 1 :** exécuter cette requête sur chaque base de données SQL Data Warehouse afin d’identifier tous les index Columnstore en cluster non optimaux. Si aucune ligne n'est renvoyée, cette régression ne vous a pas affecté et aucune autre action n'est nécessaire.

```sql
SELECT 
     'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;' AS [T-SQL to Rebuild Index]
    ,CASE WHEN n.nbr_nodes < 3 THEN 'xlargerc' WHEN n.nbr_nodes BETWEEN 4 AND 6 THEN 'largerc' ELSE 'mediumrc' END AS [Resource Class Recommendation]
    ,s.name AS [Schema Name]
    ,t.name AS [Table Name]
    ,AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) AS [Ave Rows in Compressed Row Groups]
FROM 
    sys.pdw_nodes_column_store_row_groups rg
    JOIN sys.pdw_nodes_tables pt 
        ON rg.object_id = pt.object_id AND rg.pdw_node_id = pt.pdw_node_id AND pt.distribution_id = rg.distribution_id
    JOIN sys.pdw_table_mappings tm 
        ON pt.name = tm.physical_name
    INNER JOIN sys.tables t 
        ON tm.object_id = t.object_id
INNER JOIN sys.schemas s
    ON t.schema_id = s.schema_id
CROSS JOIN (SELECT COUNT(*) nbr_nodes  FROM sys.dm_pdw_nodes WHERE type = 'compute') n
GROUP BY 
    n.nbr_nodes, s.name, t.name
HAVING 
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000 OR
    AVG(CASE WHEN rg.State = 0 THEN rg.Total_rows ELSE NULL END) < 100000

ORDER BY 
    s.name, t.name
```
 
**ÉTAPE 2 :** augmenter la classe de ressource d’un utilisateur qui dispose des autorisations pour reconstruire l’index sur cette table en fonction de la classe de ressource recommandée dans la colonne 2 de la requête ci-dessus.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

> [AZURE.NOTE]  La valeur LoadUser ci-dessus doit représenter un utilisateur valide que vous créez pour exécuter l'instruction ALTER INDEX. La classe de ressource de l'utilisateur db\_owner ne peut pas être modifiée. Vous trouverez plus d'informations sur les classes de ressource et sur la création d’un utilisateur en cliquant sur le lien ci-dessous.

 
**ÉTAPE 3 :** se connecter en tant que l’utilisateur à l’étape 2 (par exemple « LoadUser »), qui utilise maintenant une classe de ressource supérieure, puis exécuter les instructions ALTER INDEX générées par la requête à l’étape 1. N'oubliez pas que cet utilisateur possède l'autorisation ALTER pour les tables identifiées dans la requête de l'étape 1.
 
**ÉTAPE 4 :** réexécuter la requête de l’étape 1. Si les index sont correctement créés, aucune ligne ne devrait être retournée par cette requête. Si aucune ligne n'est retournée, vous avez terminé. Si vous avez plusieurs bases de données SQL DW, vous pouvez répéter cette procédure sur chacune de vos bases de données. Si des lignes sont retournées, passez à l'étape 5.
 
**ÉTAPE 5 :** si des lignes sont retournées lorsque vous réexécutez la requête de l’étape 1, vous utilisez peut-être des tables contenant des lignes très larges nécessitant de grandes quantités de mémoire pour construire de façon optimale des index Columnstore en cluster. Si c'est le cas, recommencez cette procédure pour ces tables en utilisant la classe xlargerc. Pour modifier la classe de ressource, répétez l'étape 2 en utilisant xlargerc. Puis répétez l'étape 3 pour les tables comportant toujours des index non optimaux. Si vous utilisez une base de données DW100 - DW300 et avez déjà exécuté xlargerc, vous pouvez laisser les index tels quels ou augmenter temporairement l’unité DWU afin de fournir plus de mémoire pour cette opération.
 
**DERNIÈRES ÉTAPES :** la classe de ressource ci-dessus est la classe de ressource minimale recommandée pour créer des index Columnstore de qualité optimale. Nous vous conseillons de conserver ce paramètre pour l'utilisateur qui charge les données. Mais si vous souhaitez annuler la modification de l'étape 2, vous pouvez le faire avec la commande suivante.

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```

La recommandation concernant la classe de ressource minimum pour les charges de travail dans une table CCI consiste à utiliser xlargerc pour DW100 à DW300, largerc pour DW400 à DW600 et mediumrc pour toute autre base de données supérieure ou égale à DW1000. Cette recommandation s’applique à la plupart des charges de travail. L'objectif est d’attribuer à chaque opération de création d'index 400 Mo de mémoire ou plus. Mais une taille unique ne convient pas à tous les scénarios. La mémoire nécessaire pour optimiser un index Columnstore dépend des données chargées, principalement influencées par la taille de la ligne. Les tables avec des lignes plus étroites utilisent moins de mémoire, tandis que les lignes plus larges en consomment plus. Si vous souhaitez faire quelques tests, vous pouvez utiliser la requête de l'étape 1 pour vérifier si vous obtenez des index Columnstore optimaux avec des allocations de mémoire inférieures. Vous devriez en moyenne obtenir au minimum plus de 100 000 lignes par groupe de lignes. Les résultats seront encore meilleurs avec 500 000 lignes. La valeur maximale affichée est 1 million de lignes par groupe de lignes. Pour plus d'informations sur la gestion des classes de ressource et la simultanéité, cliquez sur le lien ci-dessous.


## Étapes suivantes
Consultez l’article [Meilleures pratiques relatives à SQL Data Warehouse][] pour plus d’informations sur la façon d’optimiser votre solution SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[mise à l’échelle de votre base de données SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[conception de vos tables]: ./sql-data-warehouse-develop-table-design.md
[choisir une clé de distribution par hachage adaptée à votre table]: ./sql-data-warehouse-develop-hash-distribution-key
[development overview]: ./sql-data-warehouse-overview-develop.md
[surveillance de vos requêtes]: ./sql-data-warehouse-manage-monitor.md
[Gérer des statistiques dans SQL Data Warehouse]: ./sql-data-warehouse-develop-statistics.md
[instructions d’approvisionnement]: ./sql-data-warehouse-get-started-provision.md
[configuration de l’accès au pare-feu du serveur pour l’adresse IP de votre client]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 ou 2015]: ./sql-data-warehouse-get-started-connect.md
[Meilleures pratiques relatives à SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[statistiques]: ./sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other web references-->

<!---HONumber=AcomDC_0615_2016-->