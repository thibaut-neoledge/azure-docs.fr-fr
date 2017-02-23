---        
title: "Explorer les didacticiels relatifs à Azure SQL Database | Microsoft Docs"
description: "En savoir plus sur les fonctions et capacités de la base de données SQL"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 04c0fd7f-d260-4e43-a4f0-41cdcd5e3786
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 60bcd02d24e2084b9020ce56ef6a9f8268c6b1b5
ms.openlocfilehash: 23d4c00f9e4c719e2124ad3c8733269712a796f0

        
---
 
# <a name="explore-azure-sql-database-tutorials"></a>Exploration des didacticiels relatifs à la base de données SQL Azure
Utilisez les liens des tableaux suivants pour obtenir une vue d’ensemble de chaque fonctionnalité répertoriée et accéder à un didacticiel de démarrage simple pas à pas pour chaque domaine abordé. Pour consulter des démarrages rapides de solutions qui illustrent l’utilisation de la base de données SQL dans une solution complète à partir de scénarios réels, consultez [Démarrages rapides de la solution de base de données SQL Azure](sql-database-solution-quick-starts.md).

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>Créer des serveurs, des bases de données et des règles de pare-feu au niveau du serveur
Les didacticiels ci-après vous apprennent à créer des serveurs, des bases de données et des règles de pare-feu au niveau du serveur, ainsi qu’à vous connecter et adresser des requêtes aux serveurs et aux bases de données.

| Didacticiel | Description |
| --- | --- | 
| [Didacticiel de démarrage rapide : Votre première solution Azure SQL Database](sql-database-get-started.md) | Une fois ce didacticiel de démarrage rapide terminé, vous aurez un exemple de base de données et une base de données vide s’exécutant dans un groupe de ressources Azure, le tout attaché à un serveur logique. Vous créerez également deux règles de pare-feu de niveau serveur configurées pour permettre au principal au niveau du serveur de se connecter au serveur à partir de deux adresses IP spécifiées. Enfin, vous apprendrez à interroger une base de données dans le portail Azure, ainsi qu’à vous connecter et à effectuer des requêtes à l’aide de SQL Server Management Studio. |
| [Didacticiel : Approvisionner une base de données Azure SQL et y accéder à l’aide de PowerShell](sql-database-get-started-powershell.md) | Lorsque vous aurez terminé ce didacticiel, vous aurez un exemple de base de données et une base de données vide en cours d’exécution dans un groupe de ressources Azure et attachés à un serveur logique. Vous aurez également une règle de pare-feu de niveau serveur configurée pour permettre au principal de niveau serveur de se connecter au serveur à partir d’une adresse IP spécifiée (ou d’une plage d’adresses IP). |
| [Utiliser C# pour créer une Base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md)| Dans ce didacticiel, vous utilisez C# pour créer un serveur SQL Database, une règle de pare-feu et une base de données SQL. Vous créez également une application Active Directory (AD) et le principal du service requis pour authentifier l’application C#. |
|  | |

## <a name="backups-long-term-retention-and-database-recovery"></a>Sauvegardes, rétention à long terme et récupération de base de données
Dans les didacticiels suivants, vous apprendrez à utiliser les [sauvegardes de base de données](sql-database-automated-backups.md), la [rétention de sauvegarde à long terme](sql-database-long-term-retention.md), et la [restauration de base de données à l’aide de sauvegardes](sql-database-recovery-using-backups.md).

| Didacticiel | Description |
| --- | --- | 
| [Sauvegarde et restauration à l’aide du portail Azure](sql-database-get-started-backup-recovery.md) | Dans ce didacticiel, vous apprendrez à utiliser le portail Azure pour afficher les sauvegardes, restaurer une version antérieure, configurer une rétention de sauvegarde à long terme et restaurer à partir d’une sauvegarde du coffre Azure Recovery Services.
| [Sauvegarde et restauration à l’aide de PowerShell](sql-database-get-started-backup-recovery-powershell.md) | Dans ce didacticiel, vous apprendrez à utiliser PowerShell pour afficher les sauvegardes, restaurer une version antérieure, configurer une rétention de sauvegarde à long terme et restaurer à partir d’une sauvegarde du coffre Azure Recovery Services.
|  | |

## <a name="sharded-databases"></a>Bases de données partitionnées
Dans les didacticiels suivants, vous apprendrez comment [monter en charge les bases de données avec le Gestionnaire de cartes de partitions](sql-database-elastic-scale-shard-map-management.md).

| Didacticiel | Description |
| --- | --- | 
| [Créer une application partitionnée](sql-database-elastic-scale-get-started.md) |Ce didacticiel vous apprend à utiliser les fonctionnalités des outils de bases de données élastiques à l’aide d’une simple application partitionnée. |
| [Déployer un service de fractionnement et de fusion](sql-database-elastic-scale-configure-deploy-split-and-merge.md) |Ce didacticiel vous apprend à déplacer des données entre plusieurs bases de données partitionnées. |
|  | |

## <a name="elastic-database-jobs"></a>Tâches de base de données élastiques
Dans les didacticiels suivants, vous apprendrez à utiliser les [tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md).

| Didacticiel | Description |
| --- | --- | 
| [Prise en main de tâches de bases de données élastiques](sql-database-elastic-jobs-getting-started.md) |Ce didacticiel vous apprend à créer et gérer des tâches permettant de gérer un groupe de bases de données associées. |
|  | |

## <a name="elastic-queries"></a>Requêtes élastiques
Dans les didacticiels suivants, vous apprendrez à exécuter des [requêtes élastiques](sql-database-elastic-query-overview.md).

| Didacticiel | Description |
| --- | --- | 
| [Créer des requêtes élastiques](sql-database-elastic-query-getting-started-vertical.md) | Ce didacticiel vous apprend à exécuter des requêtes T-SQL sur plusieurs bases de données à l’aide d’un seul point de connexion. |
| [Signaler des bases de données cloud étendues](sql-database-elastic-query-getting-started.md) |Ce didacticiel vous apprend à créer des rapports à partir de toutes les bases de données contenues dans une base de données partitionnée horizontalement. |
| [Interroger des bases de données cloud de schémas différents](sql-database-elastic-query-vertical-partitioning.md) | Ce didacticiel vous apprend à exécuter des requêtes T-SQL sur plusieurs bases de données avec des schémas différents. |
| [Créer des rapports sur des bases de données cloud mises à l’échelle](sql-database-elastic-query-horizontal-partitioning.md) |Ce didacticiel vous apprend à créer des rapports qui couvrent toutes les bases de données d’une base de données partitionnée. |
|  | |

## <a name="database-authentication-and-authorization"></a>Authentification et autorisation des bases de données
Les didacticiels suivants portent sur [la création et la gestion des connexions et des utilisateurs](sql-database-manage-logins.md).

| Didacticiel | Description |
| --- | --- | --- |
| [Authentification et autorisation SQL](sql-database-control-access-sql-authentication-get-started.md) | Dans ce didacticiel, vous allez découvrir comment créer des connexions et des utilisateurs à l’aide de l’authentification SQL Server, les ajouter à des rôles et leur octroyer des autorisations. |
| [Authentification et autorisation Azure AD](sql-database-control-access-aad-authentication-get-started.md) | Dans ce didacticiel, vous allez découvrir comment créer des connexions et des utilisateurs à l’aide de l’authentification Azure Active Directory. |
|  | |

## <a name="secure-and-protect-data"></a>Sécuriser et protéger des données
Les didacticiels suivants portent sur la [sécurité des données d’une base de données SQL Azure](sql-database-security-overview.md).

| Didacticiel | Description |
| --- | --- | 
| [Protéger les données sensibles à l’aide d’Always Encrypted ](sql-database-always-encrypted-azure-key-vault.md) |Ce didacticiel vous explique comment utiliser l’assistant Always Encrypted pour sécuriser les données sensibles dans une base de données SQL Azure. |
|  | |

## <a name="develop"></a>Développement
Dans les didacticiels suivants, vous découvrirez le développement d’applications et de bases de données.

| Didacticiel | Description |
| --- | --- | 
| [Créer un rapport à l’aide d’Excel](sql-database-connect-excel.md) |Ce didacticiel vous apprend à connecter Excel à une base de données SQL dans le cloud afin de pouvoir importer des données et créer des tables et des graphiques basés sur des valeurs de la base de données. |
| [Développer une application à l’aide de SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) |Ce didacticiel vous apprend à développer une application à l’aide de SQL Server. |
| [Utiliser Entity Framework avec des outils élastiques](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) |Ce didacticiel présente les modifications d’une application Entity Framework requises pour l’intégration avec les outils des bases de données élastiques. |
| [Adopter OLTP In-Memory](sql-database-in-memory-oltp-migration.md) | Ce didacticiel vous apprend à utiliser [OLTP In-Memory](sql-database-in-memory.md) pour améliorer les performances du traitement des transactions. |
|  | |

## <a name="data-sync"></a>Synchronisation des données
Ce didacticiel fournit des informations relatives à la [synchronisation des données](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Didacticiel | Description |
| --- | --- | 
| [Prise en main d’Azure SQL Data Sync (version préliminaire)](sql-database-get-started-sql-data-sync.md) |Dans ce didacticiel, vous allez vous familiariser avec les concepts de base d’Azure SQL Data Sync avec le portail Azure. |
|  | |

## <a name="next-steps"></a>Étapes suivantes
[Explorer les démarrages rapides de la solution de base de données SQL Azure](sql-database-solution-quick-starts.md)



<!--HONumber=Feb17_HO2-->


