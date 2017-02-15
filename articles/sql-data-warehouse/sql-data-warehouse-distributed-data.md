---
title: "Données et tables distribuées pour les systèmes de traitement massivement parallèle (MPP) de SQL Data Warehouse et Parallel Data Warehouse | Documents Microsoft"
description: "Découvrez le mode de distribution des données pour le traitement massivement parallèle (MPP) ainsi que les options de distribution des tables dans Azure SQL Data Warehouse et Parallel Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: bae494a6-7ac5-4c38-8ca3-ab2696c63a9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1090c2156df11adc6f18dffe00a9d37921c0a3a3


---
# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Données et tables distribuées pour le traitement massivement parallèle (MPP)
Découvrez comment les données utilisateur sont distribuées dans Azure SQL Data Warehouse et Parallel Data Warehouse, qui sont des systèmes de traitement massivement parallèle (MPP) de Microsoft. En concevant votre entrepôt de données pour qu’il utilise les données distribuées, vous bénéficiez des avantages de l’architecture MPP en termes de traitement des requêtes. Certains choix de conception de base de données peuvent avoir un impact significatif sur l’amélioration des performances des requêtes.  

> [!NOTE]
> Azure SQL Data Warehouse et Parallel Data Warehouse utilisent le même traitement massivement parallèle (MPP), mais ils présentent quelques différences au niveau de la plateforme sous-jacente. SQL Data Warehouse est une plateforme PaaS (Platform as a Service) qui s’exécute sur Azure. Parallel Data Warehouse s’exécute sur Analytics Platform System (APS), une application locale qui s’exécute sur Windows Server.
> 
> 

## <a name="what-is-distributed-data"></a>Présentation des données distribuées
Dans SQL Data Warehouse et Parallel Data Warehouse, les données distribuées désignent les données utilisateur qui sont stockées dans plusieurs emplacements sur le système MPP. Chacun de ces emplacements fonctionne comme une unité de stockage et de traitement indépendante, qui exécute des requêtes sur sa partie des données. Les données distribuées sont essentielles pour exécuter des requêtes en parallèle et obtenir des performances de traitement élevées.

Pour distribuer des données, l’entrepôt de données affecte chaque ligne d’une table utilisateur à un emplacement distribué.  Vous pouvez distribuer des tables par hachage ou par tourniquet. La méthode de distribution est spécifiée dans l’instruction CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Tables distribuées par hachage
Le schéma suivant illustre comment une table complète (non distribuée) est stockée sous la forme d’une table distribuée par hachage. Une fonction déterministe attribue chaque ligne à une distribution. Dans la définition de la table, une des colonnes est désignée comme colonne de distribution. La fonction de hachage utilise valeur située dans la colonne de distribution pour allouer chaque ligne à une distribution.

Les critères de performances, comme la distinction, l’inclinaison de données et les types de requête exécutés sur le système, conditionne le choix de la colonne de distribution.

![Table distribuée](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Distributed table")  

* Chaque ligne appartient à une distribution.  
* Un algorithme de hachage déterministe attribue chaque ligne à une distribution.  
* Le nombre de lignes de table par distribution varie selon la taille des tables.

## <a name="round-robin-distributed-tables"></a>Tables distribuées par tourniquet
Une table distribuée par tourniquet distribue les lignes en les allouant à une distribution de façon séquentielle. Cette méthode permet de charger rapidement des données dans une table de type tourniquet, mais le traitement des requêtes peut être plus lent.  En général, la jointure d’une table de type tourniquet implique un remaniement des lignes pour permettre à la requête de produire un résultat exact, ce qui prend du temps.

## <a name="distributed-storage-locations-are-called-distributions"></a>Les emplacements de stockage distribués sont appelés des distributions.
Chaque emplacement distribué est appelé une distribution. Lorsqu’une requête s’exécute en parallèle, chaque distribution exécute une requête SQL sur sa partie des données. SQL Data Warehouse utilise SQL Database pour exécuter la requête. Parallel Data Warehouse utilise SQL Server pour exécuter la requête. Cette architecture sans partage est essentielle pour la montée en puissance du calcul parallèle.

### <a name="can-i-view-the-distributions"></a>Puis-je afficher les distributions ?
Chaque distribution possède un ID et s’affiche dans les vues système de SQL Data Warehouse et Parallel Data Warehouse. Vous pouvez utiliser l’ID de distribution pour résoudre les problèmes de performances des requêtes, entre autres. Pour obtenir la liste des vues système, cliquez [ici](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Différence entre une distribution et un nœud de calcul
La distribution est l’unité de base qui stocke les données distribuées et traite les requêtes en parallèle. Les distributions sont regroupées en nœuds de calcul. Chaque nœud de calcul gère une ou plusieurs distributions.  

* Analytique Platform System utilise les nœuds de calcul comme un composant central de l’architecture matérielle et des fonctionnalités de montée en puissance. Il inclut toujours huit distributions par nœud de calcul, comme indiqué dans le schéma des tables distribuées par hachage. Le nombre de nœuds de calcul, et donc de distributions, correspond au nombre de nœuds de calcul que vous achetez pour l’application. Par exemple, si vous achetez huit nœuds, vous obtenez 64 distributions (8 nœuds x 8 distributions/nœud). 
* SQL Data Warehouse a un nombre fixe de 60 distributions et un nombre variable de nœuds de calcul. Les nœuds de calcul sont implémentés avec les ressources de calcul et de stockage Azure. Le nombre de nœuds de calcul peut varier en fonction de la charge de travail du service principal et de la capacité de calcul (unités DWU) que vous spécifiez pour l’entrepôt de données. Lorsque le nombre de nœuds de calcul change, le nombre de distributions par nœud change également. 

### <a name="can-i-view-the-compute-nodes"></a>Puis-je afficher les nœuds de calcul ?
Chaque nœud de calcul possède un ID et s’affiche dans les vues système de SQL Data Warehouse et Parallel Data Warehouse.  Vous pouvez visualiser le nœud de calcul en effectuant une recherche dans la colonne node_id des vues système dont le nom commence par sys.pdw_nodes. Pour obtenir la liste des vues système, cliquez [ici](sql-data-warehouse-reference-tsql-statements.md).

## <a name="a-namereplicatedareplicated-tables-for-parallel-data-warehouse"></a><a name="Replicated"></a>Tables répliquées pour Parallel Data Warehouse
S’applique à : Parallel Data Warehouse

Outre l’utilisation des tables distribuées, Parallel Data Warehouse permet de répliquer des tables. Une *table répliquée* est une table stockée intégralement sur chaque nœud de calcul. La réplication d’une table évite le transfert de ses lignes entre des nœuds de calcul avant de l’utiliser dans une jointure ou une agrégation. Seules les tables de petite taille sont réplicables, en raison de l’espace de stockage supplémentaire requis pour les stocker intégralement sur chaque nœud de calcul.  

Le diagramme suivant illustre une table répliquée stockée sur chaque nœud de calcul. La table répliquée est stockée sur tous les disques alloués au nœud de calcul. Cette stratégie de disque met en œuvre des groupes de fichiers SQL Server.  

![Table répliquée](media/sql-data-warehouse-distributed-data/replicated-table.png "Replicated table") 

## <a name="next-steps"></a>Étapes suivantes
Pour utiliser efficacement les tables distribuées, consultez [Distributing tables in SQL Data Warehouse (Distribution de tables dans SQL Data Warehouse)](sql-data-warehouse-tables-distribute.md).  




<!--HONumber=Nov16_HO3-->


