---
title: "Charge de travail de l’entrepôt de données"
description: 'SQL Data Warehouse''s elasticity lets you grow, shrink, or pause compute power by using a sliding scale of data warehouse units (DWUs). This article explains the data warehouse metrics and how they relate to DWUs. '
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: cadffa9c-589d-4db7-888a-1f202a753bc5
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess;mausher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 43ee395fab826549ddd9ede3c3aa45aa8e97c7dd


---
# <a name="data-warehouse-workload"></a>Charge de travail de l’entrepôt de données
Le concept de « charge de travail d’entrepôt de données » fait référence à l’ensemble des opérations se produisant dans un entrepôt de données. La charge de travail de l’entrepôt de données comprend l’ensemble du processus de chargement des données dans l’entrepôt, d’exécution de l’analyse et de génération des rapports sur l’entrepôt de données, de gestion des données dans l’entrepôt de données et d’exportation des données de l’entrepôt de données. La complexité et l’étendue de ces composants sont bien souvent proportionnées par rapport au niveau de maturité de l’entrepôt de données.

## <a name="new-to-data-warehousing"></a>Vous découvrez l’entrepôt de données ?
Un entrepôt de données est une collection de données qui est chargée d’une ou plusieurs sources de données et qui est utilisée pour l’exécution de tâches décisionnelles, comme la génération de rapports ou l’analyse de données.

Les entrepôts de données incluent des requêtes qui analysent un grand nombre de lignes, de grands volumes de données et qui peuvent renvoyer des résultats relativement volumineux à des fins d’analyse et de génération de rapports. Les entrepôts de données sont également caractérisés par des charges de données relativement importantes, alors que les opérations d’insertion/de mise à jour/de suppression au niveau transactionnel sont de petite taille.

* Un entrepôt de données est davantage performant lorsque la configuration de stockage des données est idéale pour les requêtes qui doivent analyser un grand nombre de lignes ou de gros volumes de données. Ce type de tâches d’analyse est plus efficace lorsque les données sont stockées et classées par colonnes, non en lignes.

> [!NOTE]
> L’index Columnstore en mémoire, qui utilise le stockage par colonne, procure une compression jusqu'à 10 fois supérieure et des requêtes jusqu'à 100 fois supérieures par rapport aux arborescences binaires traditionnelles dédiées aux requêtes de rapports et d'analyse. Nous considérons les index Columnstore comme la norme en matière de stockage et d’analyse de gros volumes de données dans un entrepôt de données.
> 
> 

* Un entrepôt de données présente des exigences différentes d’un système ; il est configuré pour le traitement transactionnel en ligne (OLTP, online transaction processing). Le système OLTP propose de multiples opérations d’insertion, de mise à jour et de suppression. Ces opérations sont dirigées vers des lignes spécifiques de la table. Les recherches de table aboutissent à de meilleurs résultats lorsque les données sont stockées par lignes. Les données peuvent être triées et recherchées rapidement à l’aide d’une approche de type « Diviser pour régner », appelée recherche par arborescence binaire.

## <a name="data-loading"></a>Chargement de données
Le chargement des données est une phase importante de la charge de travail de l’entrepôt de données. Les entreprises sont généralement équipées d’un système OLTP chargé qui effectue le suivi des modifications tout au long de la journée, à mesure que les clients génèrent des transactions commerciales. Régulièrement, souvent le soir durant une période de maintenance, les transactions sont déplacées ou copiées sur l’entrepôt de données. Une fois que les données sont dans l’entrepôt de données, les analystes peuvent procéder à l’analyse et prendre des décisions métiers relatives aux données.

* Traditionnellement, le processus de chargement est appelé ETL (Extract, Transform and Load, pour extraction, transformation et chargement). Les données doivent généralement être transformées, ceci pour garantir une cohérence avec les autres données de l’entrepôt de données. Auparavant, les entreprises effectuaient les transformations à l’aide de serveurs ETL dédiés. Désormais, en valorisant le traitement massivement parallèle, vous commencez par charger les données dans SQL Data Warehouse, puis effectuez les transformations. Il s’agit du processus ELT (Extract, Load and Transform pour extraction, chargement et transformation), qui devient la norme associée aux charges de travail d’entrepôts de données.

> [!NOTE]
> En vous dotant de SQL Server 2016, vous pouvez effectuer des analyses en temps réel sur une table OLTP. Si cette solution ne constitue pas une alternative au stockage et à l’analyse de données dans un entrepôt de données, elle procure un moyen d’exécuter l’analyse en temps réel.
> 
> 

### <a name="reporting-and-analysis-queries"></a>Requêtes de rapports et d’analyse
Les requêtes de rapports et d’analyse sont bien souvent classées en tant que requêtes réduites, intermédiaires ou importantes en fonction du nombre de critères, mais elles sont généralement basées sur des intervalles de temps. La plupart des entrepôts de données comportent une charge de travail hybride, comprenant à la fois des requêtes à courte échéance et à longue échéance. Pour chaque cas, il est important d’identifier ce mélange et de déterminer sa fréquence (horaire, quotidienne, fin du mois, fin de trimestre, etc.). Il est essentiel de comprendre que cette charge de travail hybride de requêtes, couplée à l’accès concurrentiel, permet de planifier de manière appropriée la capacité d’un entrepôt de données.

* La planification de capacité peut s’avérer complexe lorsqu’elle est appliquée à une charge de travail hybride de requêtes, plus particulièrement lorsque l’ajout de capacité dans l’entrepôt de données est associé à un délai d’exécution important. SQL Data Warehouse élimine l’urgence de la planification de capacité, dans la mesure où vous pouvez augmenter et réduire à tout moment la capacité de calcul et que les capacités de stockage et de calcul sont dimensionnées de manière indépendante.

### <a name="data-management"></a>Gestion des données
La gestion des données est essentielle, plus particulièrement si vous prévoyez de manquer d’espace disque dans l’avenir proche. Les entrepôts de données répartissent généralement les données en plages pertinentes, qui sont stockées en tant que partitions dans une table. Tous les produits basés sur SQL Server prennent en charge l’extraction et l’ajout de partitions dans les tables. Grâce à ce déplacement de partitions, vous êtes en mesure de déplacer des données plus anciennes vers un modèle de stockage moins coûteux et de conserver les données les plus récentes sur l’espace de stockage en ligne.

* Les index Columnstore prennent en charge les tables partitionnées. Le cas échéant, les tables partitionnées sont utilisées pour la gestion et l’archivage des données. Dans les tables stockées ligne par ligne, les partitions jouent un rôle plus important dans l’amélioration des performances des requêtes.  
* PolyBase est essentiel à la gestion des données. Si vous recourez à PolyBase, vous avez la possibilité d’archiver des données plus anciennes sur Hadoop ou dans des objets Blob Microsoft Azure.  Les données étant dans ce cas conservées en ligne, vous disposez d’un nombre conséquent d’options.  La récupération des données de Hadoop peut prendre davantage de temps, mais les économies de stockage réalisées surpassent cet inconvénient.

### <a name="exporting-data"></a>Exportation de données
Un des moyens de rendre les données disponibles pour les rapports et l’analyse est d’envoyer les données conservées sur l’entrepôt de données vers des serveurs dédiés aux rapports et à l’analyse. Ces serveurs sont appelés mini-Data Warehouses. Par exemple, vous pouvez procéder au pré-traitement des données de rapports, puis les exporter de l’entrepôt de données vers différents serveurs du monde entier afin de les rendre largement disponibles aux clients et aux analystes.

* Pour générer les rapports, vous pouvez, par exemple, joindre chaque soir un instantané des données journalières à des serveurs de rapports en lecture seule. Cela procure une bande passante plus importante aux clients, tout en allégeant les besoins en ressources de calcul au niveau de l’entrepôt de données. Du point de vue de la sécurité, notez que les mini-data Warehouses vous permettent de réduire le nombre d’utilisateurs disposant d’un accès à l’entrepôt de données.
* Pour l’analyse, vous pouvez générer un cube d’analyse sur l’entrepôt de données puis exécuter l’analyse dans l’entrepôt de données, ou procéder au pré-traitement des données avant de les exporter vers le serveur d’analyse.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous en savez un peu plus sur SQL Data Warehouse, découvrez comment [créer rapidement un SQL Data Warehouse][créer rapidement un SQL Data Warehouse] et [charger des exemples de données][charger des exemples de données].

<!--Image references-->

<!--Article references-->
[charger des exemples de données]: ./sql-data-warehouse-load-sample-databases.md
[créer rapidement un SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->



<!--HONumber=Nov16_HO3-->


