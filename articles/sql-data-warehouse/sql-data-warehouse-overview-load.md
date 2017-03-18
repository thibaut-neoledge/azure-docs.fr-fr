---
title: "Chargement de données dans Azure SQL Data Warehouse | Microsoft Docs"
description: "Découvrez les scénarios courants de chargement des données dans SQL Data Warehouse. Ceux-ci incluent l’utilisation de PolyBase, d’Azure Blob Storage et de fichiers plats ainsi que l’envoi de disques. Vous pouvez également utiliser des outils tiers."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 9366bae67be7d1abf932d07971d2062c7bd29f3c
ms.openlocfilehash: 49825cc7455ae082ef750a2a31abbd2d71693c79
ms.lasthandoff: 02/27/2017


---
# <a name="load-data-into-azure-sql-data-warehouse"></a>Chargement de données dans Azure SQL Data Warehouse
Résumé des options de scénario et des recommandations applicables au chargement de données dans SQL Data Warehouse.

L’étape la plus difficile du chargement de données consiste généralement en la préparation des données à charger. Azure simplifie le processus de chargement en utilisant Azure Blob Storage comme magasin de données commun pour la plupart des services, et en utilisant Azure Data Factory pour orchestrer la communication et le déplacement des données entre les services Azure. Ces processus sont intégrés à la technologie PolyBase qui utilise le traitement massivement parallèle (MPP) pour charger des données en parallèle dans SQL Data Warehouse à partir d’Azure Blob Storage. 

Pour accéder à des didacticiels décrivant le chargement d’exemples de bases de données, consultez l’article [Charger des exemples de données dans SQL Data Warehouse][Load sample databases].

## <a name="load-from-azure-blob-storage"></a>Chargement à partir d’Azure Blob Storage
Le moyen le plus rapide d’importer des données dans SQL Data Warehouse consiste à utiliser PolyBase pour charger des données à partir d’Azure Blob Storage. PolyBase utilise le traitement massivement parallèle (MPP) de SQL Data Warehouse pour charger des données en parallèle à partir d’Azure Blob Storage. Pour utiliser PolyBase, vous pouvez faire appel aux commandes T-SQL ou à un pipeline Azure Data Factory.

### <a name="1-use-polybase-and-t-sql"></a>1. Utilisation de PolyBase et de T-SQL
Résumé du processus de chargement :

1. Déplacez vos données vers Azure Blob Storage ou Azure Data Lake Store et enregistrez-les dans des fichiers texte.
2. Configurez les objets externes dans SQL Data Warehouse pour définir l’emplacement et le format des données.
3. Exécutez une commande T-SQL pour charger les données en parallèle dans une nouvelle table de base de données.

<!-- 5. Schedule and run a loading job. --> 

Pour accéder à un didacticiel, consultez [Charger les données de stockage d’objets blob Azure dans SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="2-use-azure-data-factory"></a>2. Utilisation d’Azure Data Factory
Pour utiliser PolyBase plus simplement, vous pouvez créer un pipeline Azure Data Factory qui utilise PolyBase pour charger des données dans SQL Data Warehouse à partir d’Azure Blob Storage. Cette approche permet une configuration plus rapide, car vous n’avez pas à définir les objets T-SQL. Si vous avez besoin d’interroger les données externes sans les importer, utilisez T-SQL. 

Résumé du processus de chargement :

1. Déplacez vos données vers Azure Blob Storage et enregistrez-les dans des fichiers texte. (Azure Data Factory ne prend pas en charge la connectivité ADLS avec PolyBase pour le moment.)
2. Créez un pipeline Azure Data Factory pour recevoir les données. Utilisez l’option PolyBase.
4. Planifiez et exécutez le pipeline.

Pour accéder à un didacticiel, consultez [Charger les données conservées dans un stockage d’objets blob Azure dans Azure SQL Data Warehouse (Azure Data Factory)][Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)].

## <a name="load-from-sql-server"></a>Chargement à partir de SQL Server
Pour charger des données dans SQL Data Warehouse à partir de SQL Server, vous pouvez utiliser Integration Services (SSIS), transférer des fichiers plats ou envoyer des disques à Microsoft. Poursuivez la lecture de ce document pour obtenir un résumé des différents processus de chargement et accéder à des liens vers des didacticiels.

Pour planifier une migration complète des données dans SQL Data Warehouse à partir de SQL Server, consultez la [vue d’ensemble de la migration][Migration overview]. 

### <a name="use-integration-services-ssis"></a>Utilisation d’Integration Services (SSIS)
Si vous utilisez déjà les packages Integration Services (SSIS) pour le chargement de données dans SQL Server, vous pouvez mettre à jour vos packages afin d’utiliser SQL Server comme source et SQL Data Warehouse comme destination. Facile et rapide, cette approche est idéale si vous ne voulez pas migrer votre processus de chargement pour utiliser des données déjà stockées dans le cloud. La charge sera toutefois plus lente que lorsque vous utilisez PolyBase, car SSIS n’effectue pas de chargement en parallèle.

Résumé du processus de chargement :

1. Modifiez votre package Integration Services de sorte qu’il pointe vers l’instance SQL Server pour la source et vers la base de données SQL Data Warehouse pour la destination.
2. Migrez votre schéma vers SQL Data Warehouse si ce n’est déjà fait.
3. Modifiez le mappage dans vos packages et utilisez uniquement les types de données pris en charge par SQL Data Warehouse.
4. Planifiez et exécutez le package.

Pour accéder à un didacticiel, consultez [Charger des données à partir de SQL Server dans Azure SQL Data Warehouse (SSIS)][Load data from SQL Server to Azure SQL Data Warehouse (SSIS)].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Utilisation d’AZCopy (recommandé pour des données de moins de 10 To)
Si la taille de vos données est inférieure à 10 To, vous pouvez exporter les données à partir de SQL Server dans des fichiers plats, copier les fichiers vers Azure Blob Storage, puis utiliser PolyBase pour charger les données dans SQL Data Warehouse.

Résumé du processus de chargement :

1. Utilisez l’utilitaire de ligne de commande bcp pour exporter des données de SQL Server vers des fichiers plats.
2. Utilisez l’utilitaire de ligne de commande AZCopy pour copier les données de fichiers plats vers Azure Blob Storage.
3. Utilisez PolyBase pour charger des données dans SQL Data Warehouse.

Pour accéder à un didacticiel, consultez [Charger les données de stockage d’objets blob Azure dans SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="use-bcp"></a>Utilisation de bcp
Si vous disposez d’une petite quantité de données, vous pouvez utiliser l’utilitaire bcp pour charger des données directement dans Azure SQL Data Warehouse.

Résumé du processus de chargement :

1. Utilisez l’utilitaire de ligne de commande bcp pour exporter des données de SQL Server vers des fichiers plats.
2. Utilisez bcp pour charger des données de fichiers plats directement vers SQL Data Warehouse.

Pour accéder à un didacticiel, consultez [Charger des données à partir de SQL Server dans Azure SQL Data Warehouse (fichiers plats)][Load data from SQL Server to Azure SQL Data Warehouse (bcp)].

### <a name="use-importexport-recommended-for--10-tb-data"></a>Utilisation des fonctions d’importation/exportation (recommandé pour les données supérieures à 10 To)
Si la taille de vos données est supérieure à 10 To et que vous souhaitez les déplacer vers Azure, nous vous recommandons d’utiliser notre service d’expédition de disque [Import/Export][Import/Export]. 

Résumé du processus de chargement :

1. Utilisez l’utilitaire de ligne de commande bcp pour exporter des données de SQL Server vers des fichiers plats sur des disques transférables.
2. Expédiez les disques à Microsoft.
3. Microsoft charge les données dans SQL Data Warehouse.

## <a name="load-from-hdinsight"></a>Charger à partir de HDInsight
SQL Data Warehouse prend en charge le chargement des données à partir de HDInsight par le biais de PolyBase. Le processus est le même que pour le chargement des données à partir du stockage d’objets Blob Azure : utilisation de PolyBase pour se connecter à HDInsight pour charger des données. 

### <a name="1-use-polybase-and-t-sql"></a>1. Utilisation de PolyBase et de T-SQL
Résumé du processus de chargement :

1. Déplacer vos données vers HDInsight et les stocker dans des fichiers texte, au format ORC ou Parquet.
2. Configurez les objets externes dans SQL Data Warehouse pour définir l’emplacement et le format des données.
3. Exécutez une commande T-SQL pour charger les données en parallèle dans une nouvelle table de base de données.

Pour accéder à un didacticiel, consultez [Charger les données de stockage d’objets blob Azure dans SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

## <a name="recommendations"></a>Recommandations
La plupart de nos partenaires proposent des solutions de chargement. Pour en savoir plus, consultez la liste de nos [partenaires de solutions][solution partners]. 

Si vos données proviennent d’une source non relationnelle et que vous souhaitez les charger dans SQL Data Warehouse, vous devez transformer ces données en lignes et en colonnes avant de les charger. Il n’est pas nécessaire de stocker les données transformées dans une base de données ; de simples fichiers texte suffisent.

Créer des statistiques sur des données nouvellement chargées. Azure SQL Data Warehouse ne prend pas encore en charge les statistiques à création ou mise à jour automatique.  Pour optimiser les performances de vos requêtes, il est important de créer des statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou après toute modification substantielle dans les données.  Pour plus d’informations, consultez [Statistiques][Statistics].

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires sur le développement, consultez la [vue d’ensemble sur le développement][development overview].

<!--Image references-->

<!--Article references-->
[Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Load data from SQL Server to Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Load data from SQL Server to Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Load sample databases]: ./sql-data-warehouse-load-sample-databases.md
[Migration overview]: ./sql-data-warehouse-overview-migrate.md
[solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Import/Export]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/

