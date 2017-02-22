---
title: "Vue d’ensemble des fonctionnalités d’Azure SQL Database | Microsoft Docs"
description: "Cette page donne une vue d’ensemble des bases de données et des serveurs logiques Azure SQL Database et inclut une matrice de prise en charge des fonctionnalités avec des liens vers chaque fonctionnalité répertoriée."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: 21be71a1b4c79ecec8af02d08f65c41128c5ef73
ms.openlocfilehash: 50a465f314909c10bc3c3f95be2d9dc377d433a7


---
# <a name="azure-sql-database-features"></a>Fonctionnalités Azure SQL Database
Cette rubrique donne une vue d’ensemble des bases de données et des serveurs logiques Azure SQL Database et inclut une matrice de prise en charge des fonctionnalités avec des liens vers chaque fonctionnalité répertoriée. 

## <a name="what-is-an-azure-sql-database-logical-server"></a>Qu’est-ce qu’un serveur logique Azure SQL Database ?
Un serveur logique Azure SQL Database agit comme un point d’administration central pour plusieurs bases de données. Dans SQL Database, un serveur est une construction logique distincte d’une instance SQL Server que vous connaissez peut-être dans le monde local. Plus précisément, le service SQL Database n’offre aucune garantie en ce qui concerne l’emplacement des bases de données par rapport à leurs serveurs logiques et ne présente aucun accès ni aucune fonctionnalité au niveau de l’instance. Pour plus d’informations sur les serveurs logiques SQL Azure, consultez [Serveurs logiques](sql-database-server-overview.md). 

## <a name="what-is-an-azure-sql-database"></a>Qu’est-ce qu’une base de données SQL Azure ?
Chaque base de données d’Azure SQL Database est associée à un serveur logique. La base de données peut être :

- Une base de données unique avec son [propre ensemble de ressources](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU)
- Une partie d’un [pool de bases de données](sql-database-elastic-pool.md) qui [partage un ensemble de ressources](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU)
- Une partie d’un [ensemble de bases de données partitionnées dont la taille des instances a été augmentée](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling). Il peut s’agir de bases de données uniques ou mises en pool
- Une partie d’un ensemble de bases de données participant à un [modèle de conception SaaS partagé au sein d’une architecture mutualisée ](sql-database-design-patterns-multi-tenancy-saas-applications.md), et dont les bases de données peuvent être uniques ou mises en pool (ou les deux) 

Pour plus d’informations sur les bases de données SQL Azure, consultez [Bases de données SQL Azure](sql-database-overview.md).

## <a name="what-features-are-supported"></a>Quelles fonctionnalités sont prises en charge ?

Les tableaux suivants répertorient les principales fonctionnalités d’Azure SQL Database et de SQL Server, spécifient sa prise en charge et fournissent un lien vers des informations relatives à la fonctionnalité sur chaque plateforme. Pour les fonctionnalités Transact-SQL, suivez le lien dans la table pour la catégorie de la fonctionnalité. Voir aussi [Différences dans le langage Transact-SQL pour Azure SQL Database](sql-database-transact-sql-information.md) pour plus d’informations sur les raisons du manque de prise en charge de certains types de fonctionnalités.

Nous continuons d’ajouter des fonctionnalités à la version 12 (V12). Par conséquent, nous vous encourageons à consulter notre page web sur les mises à jour des services pour Azure et à utiliser ses filtres :

* Filtrez sur [Service SQL Database](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrez sur [annonces](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) de disponibilité générale pour les fonctionnalités SQL Database.

> [!TIP]
> Pour tester la compatibilité d’une base de données existante avec Azure SQL Database, consultez [Migrate a SQL Server database to Azure](sql-database-cloud-migrate.md) (Migrer une base de données SQL Server vers Azure).
>

| **Fonctionnalité** | **SQL Server** | **Base de données SQL Azure** | 
| --- | :---: | :---: | 
| Géoréplication active | Non pris en charge - voir [Groupes à haute disponibilité AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) | [Pris en charge](sql-database-geo-replication-overview.md)
| Toujours chiffré | [Pris en charge](https://msdn.microsoft.com/library/mt163865.aspx) | [Pris en charge](sql-database-always-encrypted.md) |
| Groupes de disponibilité AlwaysOn | [Pris en charge](https://msdn.microsoft.com/library/hh510230.aspx) | Non pris en charge - Voir [Géoréplication active](sql-database-geo-replication-overview.md) |
| Attacher une base de données | [Pris en charge](https://msdn.microsoft.com/library/ms190209.aspx) | Non pris en charge |
| Rôles d’application | [Pris en charge](https://msdn.microsoft.com/library/ms190998.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ms190998.aspx) |
| Mise à l’échelle automatique | Non pris en charge | [Pris en charge](sql-database-scale-up.md) |
| Azure Active Directory | Non pris en charge | [Pris en charge](sql-database-aad-authentication.md) |
| Azure Data Factory | Non pris en charge - voir [SQL Server Integration Services (SSIS)](https://msdn.microsoft.com/library/ms141026.aspx) | [Pris en charge](https://azure.microsoft.com/services/data-factory/) |
| Audit | [Pris en charge](https://msdn.microsoft.com/library/cc280386.aspx) | [Pris en charge](sql-database-auditing-get-started.md) |
| Fichier BACPAC (exporter) | [Pris en charge](https://msdn.microsoft.com/library/hh213241.aspx) | [Pris en charge](sql-database-export.md) |
| Fichier BACPAC (importer) | [Pris en charge](https://msdn.microsoft.com/library/hh710052.aspx) | [Pris en charge](sql-database-import.md) |
| Instructions BACKUP et RESTORE | [Pris en charge](https://msdn.microsoft.com/library/ff848768.aspx) | Non pris en charge |
| Fonctions intégrées | [Pris en charge](https://msdn.microsoft.com/library/ms174318.aspx) | [La plupart](https://msdn.microsoft.com/library/ms174318.aspx) |
| Modifier la capture de données | [Pris en charge](https://msdn.microsoft.com/library/cc645937.aspx) | Non pris en charge |
| Suivi des modifications | [Pris en charge](https://msdn.microsoft.com/library/bb933875.aspx) | [Pris en charge](https://msdn.microsoft.com/library/bb933875.aspx) |
| Instructions de classement | [Pris en charge](https://msdn.microsoft.com/library/ff848763.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ff848763.aspx) |
| Index Columnstore | [Pris en charge](https://msdn.microsoft.com/library/gg492088.aspx) | [Édition Premium uniquement](https://msdn.microsoft.com/library/gg492088.aspx) |
| Common Language Runtime (CLR) | [Pris en charge](https://msdn.microsoft.com/library/ms131102.aspx) | Non pris en charge |
| Bases de données à relation contenant-contenu | [Pris en charge](https://msdn.microsoft.com/library/ff929071.aspx) | Intégration |
| Utilisateurs contenus | [Pris en charge](https://msdn.microsoft.com/library/ff929188.aspx) | [Pris en charge](sql-database-manage-logins.md#non-administrator-users) |
| Contrôle des mots clés de langage de flux | [Pris en charge](https://msdn.microsoft.com/library/ms174290.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ms174290.aspx) |
| Requêtes entre plusieurs bases de données | [Pris en charge](https://msdn.microsoft.com/library/dn584627.aspx) | [Requêtes élastiques](sql-database-elastic-query-overview.md) |
| Curseurs | [Pris en charge](https://msdn.microsoft.com/library/ms181441.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ms181441.aspx) | 
| Compression des données | [Pris en charge](https://msdn.microsoft.com/library/cc280449.aspx) | [Pris en charge](https://msdn.microsoft.com/library/cc280449.aspx) |
| Sauvegardes de base de données | [Présentées aux utilisateurs](https://msdn.microsoft.com/library/ms187048.aspx) | [Intégré](sql-database-automated-backups.md) |
| Messagerie de base de données | [Pris en charge](https://msdn.microsoft.com/library/ms189635.aspx) | Non pris en charge |
| Mise en miroir de bases de données | [Pris en charge](https://msdn.microsoft.com/library/ms189852.aspx) | Non pris en charge |
| Options de configuration de bases de données | [Pris en charge](https://msdn.microsoft.com/library/mt629158.aspx) | [Pris en charge](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services (DQS) | [Pris en charge](https://msdn.microsoft.com/library/ff877925.aspx) | Non pris en charge |
| Instantanés de base de données | [Pris en charge](https://msdn.microsoft.com/library/ms175158.aspx) | Non pris en charge |
| Types de données | [Pris en charge](https://msdn.microsoft.com/library/ms187752.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ms187752.aspx) |  
| Instructions DBCC | [Tout](https://msdn.microsoft.com/library/ms188796.aspx) | [Certains](https://msdn.microsoft.com/library/ms188796.aspx) |
| Instructions DDL | [Pris en charge](https://msdn.microsoft.com/library/ff848799.aspx) | [La plupart](https://msdn.microsoft.com/library/ff848799.aspx)
| Déclencheurs DDL | [Pris en charge](https://msdn.microsoft.com/library/ms175941.aspx) | [Base de données uniquement](https://msdn.microsoft.com/library/ms175941.aspx) |
| Transactions distribuées | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | Scénarios de base de données intra-SQL limités uniquement |
| Instructions DML | [Pris en charge](https://msdn.microsoft.com/library/ff848766.aspx) | [La plupart](https://msdn.microsoft.com/library/ff848766.aspx) |
| Déclencheurs DML | [Pris en charge](https://msdn.microsoft.com/library/ms178110.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ms178110.aspx) |
| DMV | [Tout](https://msdn.microsoft.com/library/ms188754.aspx) | [Certains](https://msdn.microsoft.com/library/ms188754.aspx) |
| pools élastiques | Non pris en charge | [Pris en charge](sql-database-elastic-pool.md) |
| Tâches élastiques | Non pris en charge - voir [SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx) | [Pris en charge](sql-database-elastic-jobs-getting-started.md) | 
| Requêtes élastiques | Non pris en charge - voir [Requêtes entre plusieurs bases de données](https://msdn.microsoft.com/library/dn584627.aspx) | [Pris en charge](sql-database-elastic-query-overview.md) |
| Notifications d’événement | [Pris en charge](https://msdn.microsoft.com/library/ms186376.aspx) | [Pris en charge](sql-database-insights-alerts-portal.md) |
| Expressions | [Pris en charge](https://msdn.microsoft.com/library/ms190286.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ms190286.aspx) |
| Événements étendus | [Pris en charge](https://msdn.microsoft.com/library/bb630282.aspx) | [Certains](sql-database-xevent-db-diff-from-svr.md) |
| Procédures stockées étendues | [Pris en charge](https://msdn.microsoft.com/library/ms164627.aspx) | Non pris en charge |
| Groupes de fichiers | [Pris en charge](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [Principal uniquement](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| Filestream | [Pris en charge](https://msdn.microsoft.com/library/gg471497.aspx) | Non pris en charge |
| Recherche en texte intégral | [Pris en charge](https://msdn.microsoft.com/library/ms142571.aspx) | [Analyseurs lexicaux tiers non pris en charge](https://msdn.microsoft.com/library/ms142571.aspx) |
| Fonctions | [Pris en charge](https://msdn.microsoft.com/library/ms174318.aspx) | [La plupart](https://msdn.microsoft.com/library/ms174318.aspx) |
| Optimisation en mémoire | [Pris en charge](https://msdn.microsoft.com/library/dn133186.aspx) | [Édition Premium uniquement](https://msdn.microsoft.com/library/dn133186.aspx) |
| Travaux | [SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx) | [Pris en charge](sql-database-elastic-jobs-getting-started.md) |
| Prise en charge des données JSON | [Pris en charge](https://msdn.microsoft.com/library/dn921897.aspx) | [Pris en charge](sql-database-json-features.md) |
| Éléments de langage | [Pris en charge](https://msdn.microsoft.com/library/ff848807.aspx) | [La plupart](https://msdn.microsoft.com/library/ff848807.aspx) |  
| Services liés | [Pris en charge](https://msdn.microsoft.com/library/ms188279.aspx) | Non pris en charge - voir [Requête élastique](sql-database-elastic-query-horizontal-partitioning.md) |
| Copie des journaux de transaction | [Pris en charge](https://msdn.microsoft.com/library/ms187103.aspx) | Non pris en charge - Voir [Géoréplication active](sql-database-geo-replication-overview.md) |
| Commandes de gestion | [Pris en charge](https://msdn.microsoft.com/library/ms190286.aspx)| [Non pris en charge](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services (MDS) | [Pris en charge](https://msdn.microsoft.com/library/ff487003.aspx) | Non pris en charge |
| Journalisation minimale dans l’importation en bloc | [Pris en charge](https://msdn.microsoft.com/library/ms190422.aspx) | Non pris en charge |
| Modification des données système | [Pris en charge](https://msdn.microsoft.com/library/ms178028.aspx) | Non pris en charge |
| Opérations d’index en ligne | [Pris en charge](https://msdn.microsoft.com/library/ms177442.aspx) | [Taille des transactions limitée par le niveau de service](https://msdn.microsoft.com/library/ms177442.aspx) |
| Opérateurs | [Pris en charge](https://msdn.microsoft.com/library/ms174986.aspx) | [La plupart](https://msdn.microsoft.com/library/ms174986.aspx) |
| Limite de restauration dans le temps d’une base de données | [Pris en charge](https://msdn.microsoft.com/library/ms179451.aspx) | [Pris en charge](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [Pris en charge](https://msdn.microsoft.com/library/mt143171.aspx) | [Non pris en charge]
| Gestion basée sur des stratégies | [Pris en charge](https://msdn.microsoft.com/library/bb510667.aspx) | Non pris en charge |
| Prédicats | [Pris en charge](https://msdn.microsoft.com/library/ms189523.aspx) | [La plupart](https://msdn.microsoft.com/library/ms189523.aspx)
| Gouverneur de ressources | [Pris en charge](https://msdn.microsoft.com/library/bb933866.aspx) | [Intégré](sql-database-service-tiers.md) |
| Restauration de la base de données à partir de la sauvegarde | [Pris en charge](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [À partir des sauvegardes intégrées uniquement](sql-database-recovery-using-backups.md) |
| Sécurité au niveau des lignes | [Pris en charge](https://msdn.microsoft.com/library/dn765131.aspx) | [Pris en charge](https://msdn.microsoft.com/library/dn765131.aspx) |
| Instructions de sécurité | [Pris en charge](https://msdn.microsoft.com/library/ff848791.aspx) | [Certains](https://msdn.microsoft.com/library/ff848791.aspx) |
| Recherche sémantique | [Pris en charge](https://msdn.microsoft.com/library/gg492075.aspx) | Non pris en charge |
| Numéros de séquence | [Pris en charge](https://msdn.microsoft.com/library/ff878058.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ff878058.aspx) |
| Service Broker | [Pris en charge](https://msdn.microsoft.com/library/bb522893.aspx) | Non pris en charge |
| Options de configuration de serveur | [Pris en charge](https://msdn.microsoft.com/library/ms189631.aspx) | Non pris en charge - voir [Options de configuration de bases de données](https://msdn.microsoft.com/library/mt629158.aspx) |
| Instructions SET | [Pris en charge](https://msdn.microsoft.com/library/ms190356.aspx) | [La plupart](https://msdn.microsoft.com/library/ms190356.aspx) 
| spatial | [Pris en charge](https://msdn.microsoft.com/library/bb933790.aspx) | [Pris en charge](https://msdn.microsoft.com/library/bb933790.aspx) |
| Agent SQL Server | [Pris en charge](https://msdn.microsoft.com/library/ms189237.aspx) | Non pris en charge - voir [Travaux élastiques](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [Pris en charge](https://msdn.microsoft.com/library/bb522607.aspx) | Non pris en charge - voir [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Pris en charge](https://msdn.microsoft.com/library/ms141026.aspx) | Non pris en charge - voir [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [Pris en charge](https://msdn.microsoft.com/library/hh245198.aspx) | [Pris en charge](https://msdn.microsoft.com/library/hh245198.aspx) |
| Générateur de profils SQL Server | [Pris en charge](https://msdn.microsoft.com/library/ms181091.aspx) | Non pris en charge - voir [Événements étendus](https://msdn.microsoft.com/library/ms181091.aspx) |
| Réplication SQL Server | [Pris en charge](https://msdn.microsoft.com/library/ms151198.aspx) | [Abonné à la réplication transactionnelle et de capture instantanée uniquement](sql-database-cloud-migrate-compatible-using-transactional-replication.md) |
| SQL Server Reporting Services (SSRS) | [Pris en charge](https://msdn.microsoft.com/library/ms159106.aspx) | Non pris en charge |
| Procédures stockées | [Pris en charge](https://msdn.microsoft.com/library/ms190782.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ms190782.aspx) |
| Fonctions stockées système | [Pris en charge](https://msdn.microsoft.com/library/ff848780.aspx) | [Certains](https://msdn.microsoft.com/library/ff848780.aspx) |
| Procédures stockées système | [Pris en charge](https://msdn.microsoft.com/library/ms187961.aspx) | [Certains](https://msdn.microsoft.com/library/ms187961.aspx) |
| Tables système | [Pris en charge](https://msdn.microsoft.com/library/ms179932.aspx) | [Certains](https://msdn.microsoft.com/library/ms179932.aspx) |
| Vues système | [Pris en charge](https://msdn.microsoft.com/library/ms177862.aspx) | [Certains](https://msdn.microsoft.com/library/ms177862.aspx)
| Partitionnement de tables | [Pris en charge](https://msdn.microsoft.com/library/ms190787.aspx) | [Groupe de fichiers principal uniquement](https://msdn.microsoft.com/library/ms190787.aspx) |
| Tables temporaires | [Locales et globales](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [Locales uniquement](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| Tables temporelles | [Pris en charge](https://msdn.microsoft.com/library/dn935015.aspx) | [Pris en charge](sql-database-temporal-tables.md) |
| Instructions de transaction | [Pris en charge](https://msdn.microsoft.com/library/ms174377.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ms174377.aspx) |
| Variables | [Pris en charge](https://msdn.microsoft.com/library/ff848809.aspx) | | [Pris en charge](https://msdn.microsoft.com/library/ff848809.aspx) | 
| Chiffrement transparent des données (TDE)  | [Pris en charge](https://msdn.microsoft.com/library/bb934049.aspx) | [Pris en charge](https://msdn.microsoft.com/dn948096.aspx) |
| Clustering de basculement Windows Server | [Pris en charge](https://msdn.microsoft.com/library/hh270278.aspx) | Non pris en charge - Voir [Géoréplication active](sql-database-geo-replication-overview.md) |
| Index XML | [Pris en charge](http://msdn.microsoft.com/library/bb934097.aspx) | [Pris en charge](http://msdn.microsoft.com/library/bb934097.aspx) |
| Instructions XML | [Pris en charge](https://msdn.microsoft.com/library/ff848798.aspx) | [Pris en charge](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le service Azure SQL Database, consultez [What is SQL Database?](sql-database-technical-overview.md) (Qu’est-ce qu’une base de données SQL ?)
- Pour une vue d’ensemble des serveurs logiques SQL Azure, consultez [SQL Database logical server overview](sql-database-server-overview.md) (Vue d’ensemble du serveur logique SQL Database)
- Consultez la page [Vue d’ensemble des bases de données SQL](sql-database-overview.md) pour obtenir une vue d’ensemble des bases de données Azure SQL.
- Pour plus d’informations sur la prise en charge de Transact-SQL et les différences, consultez la page [Différences dans le langage Transact-SQL pour Azure SQL Database](sql-database-transact-sql-information.md).
- Pour plus d’informations sur les quotas de ressources spécifiques et les limitations basées sur votre **niveau de service**. Pour obtenir une présentation des niveaux de service, consultez [Niveaux de service de Base de données SQL](sql-database-service-tiers.md).
- Pour une vue d’ensemble de la sécurité, consultez [Azure SQL Database Security Overview (Vue d’ensemble de la sécurité de Azure SQL Database)](sql-database-security-overview.md).
- Pour plus d'informations sur la disponibilité des pilotes et sur la prise en charge de la base de données SQL, consultez [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md).



<!--HONumber=Feb17_HO2-->


