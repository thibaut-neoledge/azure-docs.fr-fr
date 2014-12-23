<properties title="Azure Elastic Scale Glossary" pageTitle="Glossaire de l'infrastructure élastique Azure" description="Explanation of terms used for Elastic Scale feature of Azure SQL Database" metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Glossaire de l'infrastructure élastique
Les termes suivants sont définis pour la fonctionnalité d'infrastructure élastique de base de données SQL Azure.

![Elastic Scale terms][1]

**Base de données** : base de données SQL Azure. 

**Routage dépendant des données** : fonctionnalité qui permet à une application de se connecter à une partition en fonction d'une clé de partitionnement spécifique. Comparez à la **requête sur plusieurs partitions**.

**Carte de partitions globale** : jeu de mappages composés de clés de partitionnement et de leurs partitions respectives dans un **ensemble de partitions**. La carte de partitions globale est stockée dans le **gestionnaire des cartes de partitions**. Comparez à la **carte de partitions locale**.

**Carte de partitions de liste** : carte de partitions dans laquelle les clés de partitionnement sont mappées individuellement. Comparez à la **carte de partitions de plage**.   

**Carte de partitions locale** : stockée sur une partition, la carte de partitions locale contient des mappages pour les shardlets se trouvant sur la partition.

**Requête sur plusieurs partitions** : possibilité d'émettre une requête sur plusieurs partitions ; les jeux de résultats sont retournés à l'aide de la sémantique UNION ALL (également appelée " requête fan-out "). Comparez au **routage dépendant des données**.

**Carte de partitions de plage** : carte de partitions dans laquelle la stratégie de distribution des partitions est basée sur plusieurs plages de valeurs contiguës. 

**Tables de référence** : tables qui ne sont pas partitionnées, mais sont répliquées sur plusieurs partitions. 

**Partition** : base de données SQL Azure qui stocke les données provenant d'un jeu de données partitionnées. 

**Élasticité des partitions** : possibilité d'effectuer une **mise à l'échelle horizontale** et une **mise à l'échelle verticale**.

**Tables partitionnées** : tables qui sont partitionnées, c'est-à-dire dont les données sont distribuées entre des partitions en fonction de la valeur de leur clé de partitionnement. 

**Clé de partitionnement** : valeur de colonne qui détermine comment les données sont réparties entre les partitions. Le type de valeur peut être : int, bigint, varbinary ou uniqueidentifier. 

**Ensemble de partitions** : collection de partitions qui sont attribuées à la même carte de partitions dans le gestionnaire des cartes de partitions.  

**Shardlet** : toutes les données associées à la valeur unique d'une clé de partitionnement sur une partition. Un shardlet est la plus petite unité de transfert de données possible lors de la redistribution des tables partitionnées. 

**Carte de partitions** : jeu de mappages composé de clés de partitionnement et de leurs partitions respectives.

**Gestionnaire des cartes de partitions** : objet de gestion et magasin de données qui comporte les cartes de partitions, les emplacements de partition et les mappages pour un ou plusieurs ensembles de partitions.


##Verbes et adverbes

**Mise à l'échelle horizontale** : mise à l'échelle (augmentation ou réduction) d'une collection de partitions en ajoutant ou supprimant des partitions à une carte de partitions.

**Fusionner** : action de déplacement de shardlets de deux partitions vers une seule partition et de mise à jour de la carte de partitions en conséquence.

**Déplacer des shardlets** : action de déplacement d'un seul shardlet vers une autre partition. 

**Partitionner** : action de partitionnement horizontal de données structurées de façon identique sur plusieurs bases de données en fonction d'une clé de partitionnement.

**Fractionner** : action de déplacement de plusieurs shardlets d'une partition vers une autre partition (généralement nouvelle). Une clé de partitionnement est fournie par l'utilisateur comme point de fractionnement.

**Mise à l'échelle verticale** : mise à l'échelle (augmentation ou réduction) du niveau de performances d'une partition. Par exemple, remplacer une partition de l'édition Standard vers l'édition Premium (comme requis pour des raisons de performances). 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
