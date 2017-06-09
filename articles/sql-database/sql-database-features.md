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
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 9daf8be93216aefbcf02e3fcba44d048ab95b43d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017


---
# <a name="azure-sql-database-features"></a>Fonctionnalités Azure SQL Database

Les tableaux suivants répertorient les principales fonctionnalités d’Azure SQL Database et les fonctionnalités équivalentes dans SQL Server. Ils fournissent des informations sur la prise en charge de chaque fonctionnalité, ainsi qu’un lien vers davantage d’informations sur la fonctionnalité sur chaque plateforme. Pour connaître les différences de Transact-SQL à prendre en compte lors de la migration d’une solution de base de données existante, consultez [Résolution des différences de Transact-SQL durant la migration vers SQL Database](sql-database-transact-sql-information.md).

Nous continuons d’ajouter des fonctionnalités à Azure SQL Database. Par conséquent, nous vous encourageons à consulter notre page web sur les mises à jour des services pour Azure et à utiliser ses filtres :

* Filtrez sur [Service SQL Database](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrez sur [annonces](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) de disponibilité générale pour les fonctionnalités SQL Database.

| **Fonctionnalité** | **SQL Server** | **Base de données SQL Azure** | 
| --- | :---: | :---: | 
| Géo-réplication active | Non pris en charge - voir [Groupes à haute disponibilité Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Pris en charge](sql-database-geo-replication-overview.md)
| Toujours chiffré | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Pris en charge - voir [Magasin de certificats](sql-database-always-encrypted.md) et [Coffre de clés](sql-database-always-encrypted-azure-key-vault.md)|
| Groupes de disponibilité AlwaysOn | [Pris en charge](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Non pris en charge, voir [Géo-réplication active](sql-database-geo-replication-overview.md) |
| Attacher une base de données | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Non pris en charge |
| Rôles d’application | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| Mise à l’échelle automatique | Non pris en charge | Pris en charge - voir [Niveaux de service](sql-database-service-tiers.md) |
| Azure Active Directory | Non pris en charge | [Pris en charge](sql-database-aad-authentication.md) |
| Azure Data Factory | [Pris en charge](../data-factory/data-factory-introduction.md) | [Pris en charge](../data-factory/data-factory-introduction.md) |
| Audit | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Pris en charge](sql-database-auditing.md) |
| Fichier BACPAC (exporter) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [Pris en charge](sql-database-export.md) |
| Fichier BACPAC (importer) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [Pris en charge](sql-database-import.md) |
| SAUVEGARDE | [Pris en charge](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Non pris en charge |
| Fonctions intégrées | [Pris en charge](https://docs.microsoft.com/sql/t-sql/functions/functions) | La plupart - voir [fonctions individuelles] (https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Modifier la capture de données | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Non pris en charge |
| Suivi des modifications | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| Instructions de classement | [Pris en charge](https://docs.microsoft.com/sql/t-sql/statements/collations) | [Pris en charge](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| Index Columnstore | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [Édition Premium uniquement](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| Common Language Runtime (CLR) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Non pris en charge |
| Bases de données à relation contenant-contenu | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| Utilisateurs contenus | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [Pris en charge](sql-database-manage-logins.md#non-administrator-users) |
| Contrôle des mots clés de langage de flux | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| Requêtes entre plusieurs bases de données | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Partiellement - voir [Requêtes élastiques](sql-database-elastic-query-overview.md) |
| Curseurs | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| Compression des données | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| Sauvegardes de base de données | [Gérées par l’utilisateur](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [Gérées par le service SQL Database](sql-database-automated-backups.md) |
| Messagerie de base de données | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Non pris en charge |
| Mise en miroir de bases de données | [Pris en charge](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Non pris en charge |
| Paramètres de configuration de base de données | [Pris en charge](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [Pris en charge](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services (DQS) | [Pris en charge](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Non pris en charge |
| Instantanés de base de données | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Non pris en charge |
| Types de données | [Pris en charge](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [Pris en charge](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| Instructions DBCC | [Pris en charge](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | La plupart - voir [Instructions individuelles](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) |
| Instructions DDL | [Pris en charge](https://docs.microsoft.com/sql/t-sql/statements/statements) | La plupart - voir [Instructions individuelles](https://docs.microsoft.com/sql/t-sql/statements/statements)
| Déclencheurs DDL | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [Base de données uniquement](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| Transactions distribuées | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Scénarios de base de données intra-SQL limités uniquement |
| Instructions DML | [Pris en charge](https://docs.microsoft.com/sql/t-sql/queries/queries) | La plupart - voir [Instructions individuelles] (https://docs.microsoft.com/sql/t-sql/queries/queries) |
| Déclencheurs DML | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| DMV | [Tout](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Certains - voir [DMV individuels](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) |
| Pools élastiques | Non pris en charge | [Pris en charge](sql-database-elastic-pool.md) |
| Tâches élastiques | Non pris en charge - voir [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | [Pris en charge](sql-database-elastic-jobs-getting-started.md) | 
| Requêtes élastiques | Non pris en charge - voir [Requêtes entre plusieurs bases de données](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | [Pris en charge](sql-database-elastic-query-overview.md) |
| Notifications d’événement | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [Pris en charge](sql-database-insights-alerts-portal.md) |
| Expressions | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| Événements étendus | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Certains - voir [Événements individuels](sql-database-xevent-db-diff-from-svr.md) |
| Procédures stockées étendues | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Non pris en charge |
| Fichiers et groupes de fichiers | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [Principal uniquement](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| Filestream | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Non pris en charge |
| Recherche en texte intégral | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [Analyseurs lexicaux tiers non pris en charge](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| Fonctions | [Pris en charge](https://docs.microsoft.com/sql/t-sql/functions/functions) | La plupart - voir [Fonctions individuelles](https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Optimisation en mémoire | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [Édition Premium uniquement](sql-database-in-memory.md) |
| Travaux | Voir [Agent SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Voir [Tâches élastiques](sql-database-elastic-jobs-getting-started.md) |
| Prise en charge des données JSON | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Pris en charge](sql-database-json-features.md) |
| Éléments de langage | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | La plupart - voir [Éléments individuels](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) |  
| Services liés | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Non pris en charge - voir [Requête élastique](sql-database-elastic-query-horizontal-partitioning.md) |
| Copie des journaux de transaction | [Pris en charge](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Non pris en charge, voir [Géo-réplication active](sql-database-geo-replication-overview.md) |
| Master Data Services (MDS) | [Pris en charge](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Non pris en charge |
| Journalisation minimale dans l’importation en bloc | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Non pris en charge |
| Modification des données système | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Non pris en charge |
| Opérations d’index en ligne | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [Pris en charge - taille des transactions limitée par le niveau de service](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| Opérateurs | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | La plupart - voir [Opérateurs individuels](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) |
| Limite de restauration dans le temps d’une base de données | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [Pris en charge](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Non pris en charge
| Gestion basée sur des stratégies | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Non pris en charge |
| Prédicats | [Pris en charge](https://docs.microsoft.com/sql/t-sql/queries/predicates) | La plupart - voir [Prédicats individuels](https://docs.microsoft.com/sql/t-sql/queries/predicates)
| R Services | [Pris en charge](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| Gouverneur de ressources | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Non pris en charge |
| Instructions RESTORE | [Pris en charge](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Non pris en charge | 
| Restauration de la base de données à partir de la sauvegarde | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [À partir des sauvegardes intégrées uniquement](sql-database-recovery-using-backups.md) |
| Sécurité au niveau des lignes | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| Recherche sémantique | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Non pris en charge |
| Numéros de séquence | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Service Broker | [Pris en charge](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Non pris en charge |
| Paramètres de configuration du serveur | [Pris en charge](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Non pris en charge - voir [Options de configuration de bases de données](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Instructions SET | [Pris en charge](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | La plupart - voir [Instructions individuelles](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) 
| spatial | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| Agent SQL Server | [Pris en charge](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Non pris en charge - voir [Travaux élastiques](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [Pris en charge](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Non pris en charge - voir [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Pris en charge](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Non pris en charge - voir [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| Générateur de profils SQL Server | [Pris en charge](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Non pris en charge - voir [Événements étendus](sql-database-xevent-db-diff-from-svr.md) |
| Réplication SQL Server | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Abonné à la réplication transactionnelle et de capture instantanée uniquement](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [Pris en charge](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Non pris en charge |
| Procédures stockées | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| Fonctions stockées système | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | La plupart - voir [Fonction individuelle](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) |
| Procédures stockées système | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Certaines - voir [Procédure stockée individuelle](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Tables système | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Certaines - voir [Table individuelle](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Vues catalogue système | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Certaines - voir [Vues individuelles](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)
| Partitionnement de tables | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Pris en charge - [Groupe de fichiers principal uniquement](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) |
| Tables temporaires | [Locales et globales](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [Locales uniquement](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| Tables temporelles | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Pris en charge](sql-database-temporal-tables.md) |
| Transactions | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| Variables | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [Pris en charge](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| Chiffrement transparent des données (TDE)  | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [Pris en charge](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Clustering de basculement Windows Server | [Pris en charge](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Non pris en charge, voir [Géo-réplication active](sql-database-geo-replication-overview.md) |
| Index XML | [Pris en charge](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [Pris en charge](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le service Azure SQL Database, consultez [What is SQL Database?](sql-database-technical-overview.md) (Qu’est-ce qu’une base de données SQL ?)
- Pour plus d’informations sur la prise en charge de Transact-SQL et les différences, consultez la page [Résolution des différences de Transact-SQL durant la migration vers SQL Database](sql-database-transact-sql-information.md).

