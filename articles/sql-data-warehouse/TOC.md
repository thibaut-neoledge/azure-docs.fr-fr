# Vue d'ensemble

## [En quoi consiste la solution SQL Data Warehouse ?](sql-data-warehouse-overview-what-is.md)
## [Charge de travail de l’entrepôt de données](sql-data-warehouse-overview-workload.md)
## [Données distribuées](sql-data-warehouse-distributed-data.md)

# Prise en main

## [Didacticiel du débutant](sql-data-warehouse-get-started-tutorial.md)
## [meilleures pratiques](sql-data-warehouse-best-practices.md)
## [Gérer](sql-data-warehouse-overview-manage.md)
## [Obtenir de l'aide](sql-data-warehouse-get-started-create-support-ticket.md)

# Procédure

## Sauvegarde et restauration

### [Présentation de la sauvegarde](sql-data-warehouse-backups.md)
### [Présentation de la restauration](sql-data-warehouse-restore-database-overview.md)
#### [Portail Azure](sql-data-warehouse-restore-database-portal.md)
#### [PowerShell](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## Connecter

### [Vue d'ensemble](sql-data-warehouse-connect-overview.md)
### [Chaînes de connexion](sql-data-warehouse-connection-strings.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [Installation de Visual Studio](sql-data-warehouse-install-visual-studio.md)

## Créer
### [Portail Azure](sql-data-warehouse-get-started-provision.md)
### [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)

## Développement

### [Vue d'ensemble](sql-data-warehouse-overview-develop.md)

### Tables

#### [Vue d'ensemble](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Types de données](sql-data-warehouse-tables-data-types.md)
#### [Tables distribuées](sql-data-warehouse-tables-distribute.md)
#### [Index](sql-data-warehouse-tables-index.md)
#### [Partitions](sql-data-warehouse-tables-partition.md)
#### [Statistiques](sql-data-warehouse-tables-statistics.md)
#### [Temporaire](sql-data-warehouse-tables-temporary.md)

### Requêtes

#### [SQL dynamique](sql-data-warehouse-develop-dynamic-sql.md)
#### [Options de regroupement](sql-data-warehouse-develop-group-by-options.md)
#### [Étiquettes](sql-data-warehouse-develop-label.md)

### Éléments de langage T-SQL

#### [Boucles](sql-data-warehouse-develop-loops.md)
#### [Procédures stockées](sql-data-warehouse-develop-stored-procedures.md)
#### [Transactions](sql-data-warehouse-develop-transactions.md)
#### [Bonnes pratiques relatives aux transactions](sql-data-warehouse-develop-best-practices-transactions.md)
#### [Schémas définis par l’utilisateur](sql-data-warehouse-develop-user-defined-schemas.md)
#### [Attribution de variables](sql-data-warehouse-develop-variable-assignment.md)
#### [Views](sql-data-warehouse-develop-views.md)

## Integrate

### [Vue d'ensemble](sql-data-warehouse-overview-integrate.md)
### [Data Factory](sql-data-warehouse-integrate-azure-data-factory.md)
### [Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md)
### [Didacticiel sur Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI](sql-data-warehouse-integrate-power-bi.md)
### [Visualisation Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
### [Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)

## charger

### [Vue d'ensemble](sql-data-warehouse-overview-load.md)
### [Exemples de données](sql-data-warehouse-load-sample-databases.md)
### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [AZCopy](sql-data-warehouse-load-from-sql-server-with-azcopy.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Charger - bcp depuis SQL Server](sql-data-warehouse-load-from-sql-server-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
### [Guide PolyBase](sql-data-warehouse-load-polybase-guide.md)
### [Charger - PolyBase depuis le stockage blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [Charger - PolyBase depuis SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## Migrer

### [Vue d'ensemble](sql-data-warehouse-overview-migrate.md)
### [Utilitaire de migration](sql-data-warehouse-migrate-migration-utility.md)
### [Migration du schéma](sql-data-warehouse-migrate-schema.md)
### [Migrer du code](sql-data-warehouse-migrate-code.md)
### [Migration des données](sql-data-warehouse-migrate-data.md)
### [Migrer vers le stockage Premium](sql-data-warehouse-migrate-to-premium-storage.md)

## Suspendre et mettre à l’échelle

### [Vue d'ensemble](sql-data-warehouse-manage-compute-overview.md)
### [portail Azure](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [API REST](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)

## Performances

### [Vue d'ensemble](sql-data-warehouse-overview-manage-user-queries.md)
### [Compression columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Requêtes](sql-data-warehouse-manage-monitor.md)
### [Charge de travail](sql-data-warehouse-develop-concurrency.md)
### [Résolution des problèmes](sql-data-warehouse-troubleshoot.md)

## Sécurité

### [Vue d'ensemble](sql-data-warehouse-overview-manage-security.md)
### [Audit](sql-data-warehouse-auditing-overview.md)
### [Audit pour les clients de niveau inférieur](sql-data-warehouse-auditing-downlevel-clients.md)
### [Authentification](sql-data-warehouse-authentication.md)
### [Chiffrement](sql-data-warehouse-encryption-tde.md)
### [Chiffrement avec T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Détection de menaces](sql-data-warehouse-security-threat-detection.md)

# Référence

## [Limites de capacité](sql-data-warehouse-service-capacity-limits.md)
## [Éléments de langage T-SQL](sql-data-warehouse-reference-tsql-language-elements.md)
## [Instructions T-SQL](sql-data-warehouse-reference-tsql-statements.md)
## [Vues système T-SQL](sql-data-warehouse-reference-tsql-system-views.md)
## [Applets de commande PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)

# les ressources
## [Tarification](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Partenaires
### [Décisionnel](sql-data-warehouse-partner-business-intelligence.md)
### [Intégration des données](sql-data-warehouse-partner-data-integration.md)
### [Gestion des données](sql-data-warehouse-partner-data-management.md)


<!--HONumber=Feb17_HO1-->


