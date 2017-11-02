---
title: "Architecture MPP - Azure SQL Data Warehouse ? | Microsoft Docs"
description: "Découvrez comment Azure SQL Data Warehouse combine un traitement massivement parallèle (MPP, Massively Parallel Processing) avec un stockage Azure pour obtenir des performances et une extensibilité élevées."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: architecture
ms.date: 10/23//2017
ms.author: jrj;barbkess
ms.openlocfilehash: 39092028d8317f8881b4e0772dcb87b05c064b6a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Azure SQL Data Warehouse - Architecture MPP (Massively Parallel Processing).
Découvrez comment Azure SQL Data Warehouse combine un traitement massivement parallèle (MPP, Massively Parallel Processing) avec un stockage Azure pour obtenir des performances et une extensibilité élevées. 

## <a name="mpp-architecture-components"></a>Composants de l’architecture de MPP
SQL Data Warehouse tire parti d’une architecture permettant d’adapter l’échelle pour répartir le traitement informatique des données sur plusieurs nœuds. L’unité d’échelle est une abstraction de la puissance de calcul, qui est connue comme Data Warehouse Unit (unité d’entrepôt de données). SQL Data Warehouse sépare de calcul du stockage, ce qui vous permet, en tant qu’utilisateur, d’adapter l’échelle du calcul indépendamment des données présentes dans votre système.

![Architecture de SQL Data Warehouse](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Data Warehouse utilise une architecture basée sur des nœuds. Les applications se connectent et envoient des commandes T-SQL à un nœud de contrôle qui est le seul point d’entrée pour l’entrepôt de données. Le nœud de contrôle exécute le moteur MPP qui optimise les requêtes pour un traitement en parallèle, puis transmet les opérations à des nœuds de calcul qui accomplissent leur travail en parallèle. Les nœuds de calcul stockent toutes les données utilisateur dans un stockage Azure et exécutent les requêtes parallèles. Le service de déplacement de données (DMS) est un service interne de niveau système, qui déplace les données entre les nœuds en fonction des besoins pour exécuter des requêtes en parallèle et retourner des résultats précis. 

Avec le stockage découplé et le calcul, SQL Data Warehouse peut :

* Dimensionner de façon indépendante la puissance de calcul, sans prendre en considération vos besoins de stockage.
* Augmenter ou réduire la puissance de calcul sans déplacement de données.
* Suspendre la capacité de calcul tout en conservant les données intactes de façon à ce que vous payiez uniquement pour le stockage.
* Reprendre le calcul pendant les heures d’utilisation.

### <a name="azure-storage"></a>Stockage Azure
SQL Data Warehouse utilise un stockage Azure pour préserver vos données utilisateur.  Étant donné que vos données sont stockées et gérées sur un stockage Azure, SQL Data Warehouse facture séparément votre consommation de stockage. Les données proprement dites sont partitionnées en **distributions** pour optimiser les performances du système. Vous pouvez choisir le modèle de partitionnement à utiliser pour distribuer les données lorsque vous définissez la table. SQL Data Warehouse prend en charge les modèles de partitionnement suivants :

* Hachage
* Tourniquet
* Réplication

### <a name="control-node"></a>Nœud de contrôle

Le nœud de contrôle est le cerveau de l’entrepôt de données. Il s’agit du nœud frontal qui interagit avec toutes les applications et les connexions. Le moteur MPP s’exécute sur le nœud de contrôle pour optimiser et coordonner les requêtes parallèles. Lorsque vous envoyez une requête T-SQL à SQL Data Warehouse, le nœud de contrôle la transforme en plusieurs requêtes distinctes qui s’exécutent sur chaque distribution en parallèle.

### <a name="compute-nodes"></a>Nœuds de calcul

Les nœuds de calcul fournissent la puissance de calcul. Les distributions sont mappées aux nœuds de calcul pour traitement. À mesure que vous payez pour davantage de ressources de calcul, SQL Data Warehouse re-mappe les distributions aux nœuds de calcul disponibles. Le nombre de nœuds calcul (entre 1 et 60) est déterminé par le niveau de service de l’entrepôt de données.

Chaque nœud de calcul a un ID de nœud visible dans les vues système. Vous pouvez voir l’ID de nœud de calcul en effectuant une recherche dans la colonne node_id des vues système dont le nom commence par sys.pdw_nodes. Pour obtenir la liste de ces vues système, voir [Vues système MPP](sql-data-warehouse-reference-tsql-statements.md).

### <a name="data-movement-service"></a>Service de déplacement de données
Le service de déplacement des données (DMS) est la technologie de transport de données qui coordonne le déplacement de données entre les nœuds de calcul. Certaines requêtes nécessitent un déplacement de données pour garantir que les requêtes parallèles renvoient des résultats précis. Quand un déplacement de données est requis, le service de déplacement des données veille à ce que les bonnes données aillent au bon emplacement. 

## <a name="distributions"></a>Distributions

Une distribution est l’unité de base de stockage et de traitement pour des requêtes parallèles s’exécutant sur des données distribuées. Quand SQL Data Warehouse exécute une requête, le travail est divisé en 60 requêtes plus petites qui s’exécutent en parallèle. Chacune de ces 60 requêtes s’exécute sur l’une des distributions de données. Chaque nœud de calcul gère une ou plusieurs des 60 distributions. Un entrepôt de données disposant des ressources de calcul maximales a une distribution par nœud de calcul. Un entrepôt de données disposant des ressources de calcul minimales a toutes les distributions sur un seul nœud de calcul.  

## <a name="hash-distributed-tables"></a>Tables distribuées par hachage
Une table distribuée de hachage peut offrir les meilleures performances de requête pour des jointures et agrégations sur des tables volumineuses. 

Pour partitionner des données dans une table distribuée de hachage, SQL Data Warehouse utilise une fonction de hachage pour assigner de manière déterministe chaque ligne à une distribution. Dans la définition de la table, une des colonnes est désignée comme colonne de distribution. La fonction de hachage utilise les valeurs figurant dans la colonne de distribution pour assigner chaque ligne à une distribution.

Le schéma suivant illustre comment une table complète (non distribuée) est stockée sous la forme d’une table distribuée par hachage. 

![Table distribuée](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Table distribuée")  

* Chaque ligne appartient à une distribution.  
* Un algorithme de hachage déterministe attribue chaque ligne à une distribution.  
* Le nombre de lignes de table par distribution varie selon la taille des tables.

Des critères de performances, tels que la distinction, l’asymétrie des données et les types des requêtes exécutées sur le système, conditionnent le choix de la colonne de distribution.

## <a name="round-robin-distributed-tables"></a>Tables distribuées par tourniquet
Une table de type tourniquet (round robin) est la table la plus simple à créer. Elle offre des performances rapides quand elle est utilisée en tant que table intermédiaire pour les chargements.

Une table distribuée de type tourniquet (round robin) distribue uniformément les données sur la table, mais sans optimisation. Une distribution est tout d’abord choisie au hasard. Ensuite, des tampons de lignes sont affectés aux distributions de manière séquentielle. Le chargement de données dans une table de type tourniquet (round robin) est rapide, mais les performances des requête peuvent souvent être meilleures avec des tables distribuées de hachage. Des jointures sur des tables de type tourniquet (round robin) nécessitent un remaniement des données, qui nécessite un temps supplémentaire.


## <a name="replicated-tables"></a>Tables répliquées
Une table répliquée offre les performances de requête les plus rapides pour des petites tables.

Une copie complète d’une table répliquée est mise en cache sur chaque nœud de calcul. Par conséquent, la réplication d’une table a pour effet d’éviter la nécessité de transférer des données entre nœuds de calcul avant une jointure ou une agrégation. Des tables répliquées sont utilisées de façon optimale avec des tables de petite taille. Un espace de stockage supplémentaire est requis, et des traitements additionnels sont effectués lors de l’écriture de données, qui rendent peu pratique l’usage de tables volumineuses.  

Le diagramme suivant présente une table répliquée. Pour SQL Data Warehouse, la table répliquée est mise en cache sur la première distribution sur chaque nœud de calcul.  

![Table répliquée](media/sql-data-warehouse-distributed-data/replicated-table.png "Table répliquée") 

## <a name="next-steps"></a>Étapes suivantes
À présent que vous en savez un peu plus sur SQL Data Warehouse, découvrez comment [créer rapidement un SQL Data Warehouse][create a SQL Data Warehouse] et [charger des exemples de données][load sample data]. Si vous n’êtes pas encore familiarisé avec Azure, vous pouvez vous appuyer sur le [Glossaire Azure][Azure glossary] lorsque vous rencontrez de nouveaux termes. Ou bien, consultez ces autres ressources de SQL Data Warehouse.  

* [Témoignages de clients]
* [Blogs]
* [Demandes de fonctionnalités]
* [Vidéos]
* [Blogs de l’équipe de conseil clientèle]
* [Création d’un ticket de support]
* [Forum MSDN]
* [Forum Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Création d’un ticket de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Témoignages de clients]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe de conseil clientèle]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
