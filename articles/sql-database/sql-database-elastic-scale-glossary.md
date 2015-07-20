<properties 
    pageTitle="Glossaire des outils de base de données élastique" 
    description="Explication des termes utilisés pour les outils de base de données élastique" 
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
    ms.date="07/08/2015" 
    ms.author="sidneyh"/>

#Glossaire des outils de base de données élastique
Les termes suivants sont définis pour les outils de base de données élastique, une fonction de Base de données SQL Azure. Les outils incluent la bibliothèque cliente, l’outil de fusion et de fractionnement, les pools élastiques et les requêtes.

![Termes liés ç l’infrastructure flexible][1]

**Base de données** : une base de données SQL Azure.

**Routage dépendant des données** : fonctionnalité qui permet à une application de se connecter à une partition en fonction d’une clé de partitionnement spécifique. Comparer à la **requête sur plusieurs partitions**.

**Carte de partitions globale** : la carte correspondant aux clés de partitionnement et à leurs partitions respectives au sein d’un **jeu de partitions**. La carte de partitions globale est stockée dans le **gestionnaire des cartes de partitions**. Comparer à la **carte de partitions locale**.

**Carte de partitions de liste** : carte de partitions dans laquelle les clés de partitionnement sont mappées individuellement. Comparer à la **carte de partitions de plage**.

**Carte de partitions locale** : stockée sur une partition, la carte de partitions locale contient des mappages pour les shardlets se trouvant sur la partition.

**Requête sur plusieurs partitions** : possibilité d’émettre une requête sur plusieurs partitions ; les ensembles de résultats sont retournés à l’aide de la sémantique UNION ALL (également appelée « requête de distribution ramifiée »). Comparer au **routage dépendant des données**.

**Carte de partitions de plage** : carte de partitions dans laquelle la stratégie de distribution des partitions est basée sur plusieurs plages de valeurs contiguës.

**Tables de référence** : tables qui ne sont pas partitionnées, mais qui sont répliquées sur plusieurs partitions. Par exemple, les codes postaux peuvent être stockés dans une table de référence.

**Partition** : base de données SQL Azure qui stocke les données provenant d’un jeu de données partitionnées.

**Élasticité des partitions** : capacité à effectuer une **mise à l’échelle horizontale** et une **mise à l’échelle verticale**.

**Tables partitionnées** : tables qui sont partitionnées, c’est-à-dire dont les données sont distribuées entre des partitions en fonction de la valeur de leur clé de partitionnement.

**Clé de partitionnement** : valeur de colonne qui détermine comment les données sont réparties entre les partitions. Les types de valeur disponibles sont les suivants : **int**, **bigint**, **varbinary** ou **uniqueidentifier**.

**Ensemble de partitions** : collection de partitions qui sont attribuées à la même carte de partitions dans le gestionnaire des cartes de partitions.

**Shardlet** : toutes les données associées à la valeur unique d’une clé de partitionnement sur une partition. Un shardlet est la plus petite unité de transfert de données possible lors de la redistribution des tables partitionnées.

**Carte de partitions** : jeu de mappages composé de clés de partitionnement et de leurs partitions respectives.

**Gestionnaire des cartes de partitions** : objet de gestion et magasin de données qui comporte les cartes de partitions, les emplacements des partitions et les mappages pour un ou plusieurs ensembles de partitions.

![Mappages][2]


##Verbes et adverbes

**Mise à l’échelle horizontale** : montée en charge (augmentation ou réduction) d’une collection de partitions en ajoutant ou supprimant des partitions dans une carte de partitions, comme dans l’exemple ci-dessous.

![Mise à l’échelle horizontale et verticale][3]

**Fusionner** : action de déplacer les shardlets de deux partitions vers une seule partition et de mettre à jour la carte de partitions en conséquence.

**Déplacement de shardlet** : action de déplacer un shardlet unique vers une autre partition.

**Partitionner** : action qui consiste à partitionner horizontalement des données structurées de façon identique sur plusieurs bases de données en fonction d’une clé de partitionnement.

**Fractionner** : action de déplacer plusieurs shardlets d’une partition vers une autre (généralement nouvelle). Une clé de partitionnement est fournie par l'utilisateur comme point de fractionnement.

**Mise à l’échelle verticale** : mise à l’échelle (augmentation ou réduction) du niveau de performances d’une partition individuelle. Par exemple, modifier une partition standard vers l’édition Premium (qui génère plus de ressources informatiques).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png
 

<!---HONumber=July15_HO2-->