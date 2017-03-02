---
title: "Gérer une base de données SQL Azure à l’aide du portail Azure | Microsoft Docs"
description: "Référence rapide sur l’utilisation du portail Azure pour gérer une base de données relationnelle dans le cloud."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3a56e9de-c21a-40ba-9a35-958172cb4e5b
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 130f2341eca570a982ab5c22ba429f6b48006f90
ms.lasthandoff: 02/17/2017


---
# <a name="manage-azure-sql-databases-using-the-azure-portal"></a>Gérer des bases de données SQL Azure au moyen du portail Azure

Le [portail Azure](https://portal.azure.com/) permet de créer, de surveiller et de gérer des serveurs et des bases de données SQL Azure. Cet article offre une description rapide des tâches les plus courantes et des liens vers des informations détaillées.

> [!TIP]
> Pour savoir comment créer un serveur ou un pare-feu basé sur un serveur, afficher les propriétés d’un serveur, se connecter à l’aide de SQL Server Management Studio, interroger la base de données master, créer un exemple de base de données et une base de données vide, demander les propriétés d’une base de données, se connecter à l’aide de SQL Server Management Studio et interroger l’exemple de base de données, consultez le didacticiel [Prise en main des serveurs Azure SQL Database, des bases de données et des règles de pare-feu à l’aide du portail Azure et de SQL Server Management Studio](sql-database-get-started.md).

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Afficher vos bases de données, serveurs et pools SQL Azure
Pour afficher les services SQL Database disponibles, cliquez sur **Plus de services**, puis tapez **SQL** dans la zone de recherche :

![Base de données SQL](./media/sql-database-manage-portal/sql-services.png)

## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Comment créer ou afficher des bases de données SQL Azure ?
Pour ouvrir le panneau **Bases de données SQL**, cliquez sur **Bases de données SQL**, puis cliquez sur la base de données avec laquelle vous souhaitez travailler, ou cliquez sur **+Ajouter** pour créer une base de données SQL. Pour plus d’informations, consultez [Votre première base de données Azure SQL à l’aide du portail Azure](sql-database-get-started.md).

![Bases de données SQL](./media/sql-database-manage-portal/sql-databases.png)

## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Comment créer ou afficher des serveurs SQL Azure ?
Pour ouvrir le panneau **Serveurs SQL**, cliquez sur **Serveurs SQL**, puis cliquez sur le serveur avec lequel vous souhaitez travailler, ou cliquez sur **+Ajouter** pour créer un serveur SQL. Pour plus d’informations, consultez [Votre première base de données Azure SQL à l’aide du portail Azure](sql-database-get-started.md).

![Serveurs SQL](./media/sql-database-manage-portal/sql-servers.png)

## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Comment créer ou afficher des pools élastiques SQL ?
Pour ouvrir le panneau **Pools élastiques SQL**, cliquez sur **Pools élastiques SQL**, puis cliquez sur le pool avec lequel vous souhaitez travailler, ou cliquez sur **+Ajouter** pour créer un pool. Pour en savoir plus, voir [Créer un pool de base de données élastique avec le portail Azure](sql-database-elastic-pool-manage-portal.md).

![Pools élastiques SQL](./media/sql-database-manage-portal/elastic-pools.png)

## <a name="how-do-i-update-or-view-sql-database-settings"></a>Comment mettre à jour ou afficher les paramètres de base de données SQL ?
Pour afficher ou mettre à jour vos paramètres de base de données, cliquez sur le paramètre souhaité dans le panneau Base de données SQL :

![Paramètres de base de données SQL](./media/sql-database-manage-portal/settings.png)

## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Comment trouver le nom complet d’un serveur de bases de données SQL ?
Pour afficher le nom de votre serveur de bases de données, cliquez sur **Vue d’ensemble** dans le panneau **Base de données SQL**. Notez le nom du serveur :

![Paramètres de base de données SQL](./media/sql-database-manage-portal/server-name.png)

## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Comment gérer les règles de pare-feu pour contrôler l’accès à mon serveur et à ma base de données SQL ?
Pour afficher, créer ou mettre à jour les règles de pare-feu, cliquez sur **Définir le pare-feu du serveur** dans le panneau **Base de données SQL**. Pour plus d’informations, voir [Configurer une règle de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md).

![règles de pare-feu](./media/sql-database-manage-portal/sql-database-firewall.png)

## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Comment modifier le niveau de service ou de performances de ma base de données SQL ?
Pour mettre à jour le niveau de service ou de performances d’une base de données SQL, cliquez sur **Niveau tarifaire (mise à l’échelle de DTU)** dans le panneau **Base de données SQL**. Pour plus d’informations, voir [Modifier les niveaux de service et de performances (niveau tarifaire) d’une base de données SQL](sql-database-service-tiers.md).

![Niveaux tarifaires](./media/sql-database-manage-portal/pricing-tier.png)

## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Comment configurer l’audit et la détection des menaces pour une base de données SQL ?
Pour configurer l’audit et la détection des menaces pour une base de données SQL, cliquez sur **Audit et détection des menaces** dans le panneau **Base de données SQL**. Pour plus d’informations, voir [Audit de base de données](sql-database-auditing.md) et [Prise en main de Threat Detection pour la base de données SQL](sql-database-threat-detection.md).

## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Comment configurer le masquage dynamique des données pour une base de données SQL ?
Pour configurer le masquage dynamique des données pour une base de données SQL, cliquez sur **Masquage dynamique des données** dans le panneau **Base de données SQL**. Pour plus d’informations, voir [Prise en main du masquage de données dynamiques de base de données SQL](sql-database-dynamic-data-masking-get-started.md).

## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Comment configurer le chiffrement transparent des données (TDE) pour une base de données SQL ?
Pour configurer le chiffrement transparent des données pour une base de données SQL, cliquez sur **Chiffrement transparent des données** dans le panneau **Base de données SQL**. Pour plus d’informations, voir [Activation du chiffrement transparent des données sur une base de données à l’aide du portail](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Comment afficher ou modifier la taille maximale d’une base de données SQL ?
Pour afficher ou modifier la taille d’une base de données SQL, cliquez sur **Taille de la base de données** dans le panneau **Base de données SQL**. Mettez à jour la taille maximale d’une base de données en modifiant le niveau de service ou de performances. Pour plus d’informations, voir [Modifier les niveaux de service et de performances (niveau tarifaire) d’une base de données SQL](sql-database-service-tiers.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Comment surveiller et améliorer les performances d’une base de données SQL ?
Pour surveiller et améliorer les caractéristiques de performances d’une base de données SQL, cliquez sur **Vue d’ensemble des performances** dans le panneau **Base de données SQL**. Pour plus d’informations, voir [Informations sur les performances des bases de données SQL](sql-database-performance.md).

## <a name="how-do-i-configure-geo-replication"></a>Comment configurer la géoréplication ?
Pour configurer la géoréplication pour une base de données SQL, cliquez sur **Géo-réplication** dans le panneau **Base de données SQL**. Pour plus d’informations, voir [Configurer la géoréplication pour Base de données SQL Azure avec le portail Azure](sql-database-geo-replication-portal.md).

## <a name="how-do-i-fail-over-to-a-geo-replicated-sql-database"></a>Comment basculer vers une base de données SQL géorépliquée ?
Pour basculer vers une base de données secondaire géo-répliquée, cliquez sur **Géo-réplication** dans le panneau **Base de données SQL**, puis sélectionnez **Basculement**. Pour plus d’informations, voir [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec le portail Azure](sql-database-geo-replication-failover-portal.md).

## <a name="how-do-i-copy-a-sql-database"></a>Comment copier une base de données SQL ?
Pour copier une base de données SQL, cliquez sur **Copier** dans le panneau **Base de données SQL**. Pour plus d’informations, voir [Copie d’une base de données SQL Azure à l’aide du portail Azure](sql-database-copy-portal.md).

![Paramètres de base de données SQL](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Comment archiver une base de données SQL Azure dans un fichier BACPAC ?
Pour créer un fichier BACPAC d’une base de données SQL, cliquez sur **Exporter** dans le panneau **Base de données SQL**. Pour plus d’informations, voir [Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure](sql-database-export.md).

![Exportation de base de données SQL](./media/sql-database-manage-portal/sql-database-export.png)

## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Comment restaurer une base de données SQL à un point antérieur dans le temps ?
Pour restaurer une base de données SQL, cliquez sur **Restaurer** dans le panneau **Base de données SQL**. Pour plus d’informations, voir [Restaurer une base de données SQL Azure à un point antérieur dans le temps avec le portail Azure](sql-database-point-in-time-restore-portal.md).

![Paramètres de base de données SQL](./media/sql-database-manage-portal/sql-database-restore.png)

## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Comment créer une base de données SQL Azure à partir d’un fichier BACPAC ?
Pour créer une base de données SQL à partir d’un fichier BACPAC, cliquez sur **Importer la base de données** dans le panneau **Serveur SQL**. Pour plus d’informations, voir [Importer un fichier BACPAC pour créer une base de données SQL Azure](sql-database-import-portal.md).

![Serveur SQL](./media/sql-database-manage-portal/server-commands.png)

## <a name="how-do-i-restore-a-deleted-sql-database"></a>Comment restaurer une base de données SQL supprimée ?
Pour restaurer une base de données SQL supprimée, cliquez sur **Bases de données supprimées** dans le panneau **Serveur SQL** (le serveur SQL qui contenait la base de données supprimée). Pour plus d’informations, voir [Restaurer une base de données SQL Azure supprimée à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Comment supprimer une base de données SQL ?
Pour supprimer une base de données SQL, cliquez sur **Supprimer** dans le panneau **Base de données SQL**. 

![Paramètres de base de données SQL](./media/sql-database-manage-portal/sql-database-delete.png)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Base de données SQL](sql-database-technical-overview.md)
* [Surveiller et gérer un pool de bases de données élastique avec le portail Azure](sql-database-elastic-pool-manage-portal.md)


