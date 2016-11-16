# Vue d'ensemble
## [Définition de la base de données SQL](sql-database-technical-overview.md)
## [Forum Aux Questions de base de données SQL](sql-database-faq.md)
## Caractéristiques
### [Niveaux de service](sql-database-service-tiers.md)
### [Unités de transaction de base de données (DTU)](sql-database-what-is-a-dtu.md)
### [Présentation des tests d’évaluation DTU](sql-database-benchmark-overview.md)
### [Outils de gestion](sql-database-manage-overview.md)
## Considérations et limitations
### [SQL Database/SQL sur une machine virtuelle](sql-database-paas-vs-sql-server-iaas.md)
### [Différences de T-SQL](sql-database-transact-sql-information.md)
### [Limites des ressources](sql-database-resource-limits.md)
### [Limitations générales](sql-database-general-limitations.md)
### [Conseils de sécurité](sql-database-security-guidelines.md)

## Avantages
### [Apprentissages et adaptations](sql-database-learn-and-adapt.md)
### [Mises à l’échelle immédiates](sql-database-scale-on-the-fly.md)
### [Création d’applications multi-locataires](sql-database-build-multi-tenant-apps.md)
### [Sécurité et protection](sql-database-helps-secures-and-protects.md)
### [Adapté à votre environnement](sql-database-works-in-your-environment.md)

## Scénarios

### Serveurs, pools, bases de données et pare-feu
#### [Quand utiliser un pool de base de données élastique](sql-database-elastic-pool-guidance.md)
#### [Pool de bases de données élastique](sql-database-elastic-pool.md)
#### [Automation](sql-database-manage-automation.md)

### Bases de données montées en charge
#### [Vue d’ensemble](sql-database-elastic-scale-introduction.md)
#### [Créer des bases de données cloud évolutives](sql-database-elastic-database-client-library.md)
#### [Travaux entre plusieurs bases de données](sql-database-elastic-jobs-overview.md)
#### [Glossaire des outils de base de données élastique](sql-database-elastic-scale-glossary.md)
#### [FAQ](sql-database-elastic-scale-faq.md)

### Accès et autorisations
#### [Vue d’ensemble](sql-database-security.md)
#### [Azure Security Center pour Azure SQL Database](../security-center/security-center-sql-database.md?toc=%2fazure%2fsql-database%2ftoc.json)
#### [Centre de sécurité SQL](https://msdn.microsoft.com/library/azure/bb510589)

### Continuité de l’activité
#### [Vue d’ensemble](sql-database-business-continuity.md)
#### [Sauvegardes de base de données](sql-database-automated-backups.md)
#### [Récupération de base de données à l’aide de sauvegardes](sql-database-recovery-using-backups.md)
#### [Exigences d’authentification pour la récupération d’urgence](sql-database-geo-replication-security-config.md)
#### [Scénarios de conception de la continuité d’activité](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Stratégies de récupération d’urgence avec les pools élastiques](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Mises à niveau propagées](sql-database-manage-application-rolling-upgrade.md)

### [Développement de base de données](sql-database-develop-overview.md)
### [Migrer des bases de données SQL Server](sql-database-cloud-migrate.md)

## Implémentations clientes
### [Daxko/CSI Software](sql-database-implementation-daxko.md)
### [GEP](sql-database-implementation-gep.md)
### [SnelStart](sql-database-implementation-snelstart.md)
### [Umbraco](sql-database-implementation-umbraco.md)


# Prise en main
## [Démarrages rapides de solution](sql-database-solution-quick-starts.md)
## Création d’une base de données SQL
### [Portail Azure](sql-database-get-started.md)
### [C#](sql-database-get-started-csharp.md)
### [PowerShell](sql-database-get-started-powershell.md)
## Créer et gérer des bases de données avec montée en charge
### [Mise à l’échelle élastique](sql-database-elastic-scale-get-started.md)
### [Tâches élastiques](sql-database-elastic-jobs-getting-started.md)
### [Entre les rapports de base de données](sql-database-elastic-query-getting-started.md)
### [Entre les requêtes de base de données](sql-database-elastic-query-getting-started-vertical.md)
## Surveiller et régler des bases de données
### [Présentation de SQL Database Advisor](sql-database-advisor.md)
### [Informations sur la charge de travail dans le portail Azure](sql-database-performance.md)
## [Créer et gérer les accès et les autorisations](sql-database-get-started-security.md)
## [Optimisation en mémoire](sql-database-in-memory.md)
## [Synchronisation des données](sql-database-get-started-sql-data-sync.md)

# Procédure

## Créer et gérer
### Serveurs et bases de données
#### [Bases de données uniques](sql-database-manage-portal.md)
#### [Portail Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Pool de bases de données élastique
#### [portail Azure](sql-database-elastic-pool-create-portal.md)
#### [PowerShell](sql-database-elastic-pool-create-powershell.md)
#### [C#](sql-database-elastic-pool-create-csharp.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
### Bases de données partitionnées
#### [Utiliser le Gestionnaire des cartes de partitions](sql-database-elastic-scale-shard-map-management.md)
#### [Configuration de sécurité de la fusion et du fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [Utilisation de Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Utiliser Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)
#### [Sécurité au niveau des lignes multi-locataires](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Gérer les informations d’identification](sql-database-elastic-scale-manage-credentials.md)
#### [Déployer un service de fractionnement et de fusion](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Ajouter une partition](sql-database-elastic-scale-add-a-shard.md)
#### [Utilisation de la classe RecoveryManager pour résoudre les problèmes de correspondance de partitionnement](sql-database-elastic-database-recovery-manager.md)
###  Authentification
#### [Authentification Azure AD](sql-database-aad-authentication.md)
#### [Azure Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### Règles de pare-feu
#### [portail Azure](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [API REST](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Travaux
#### [Installation du service](sql-database-elastic-jobs-service-installation.md)
#### [portail Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Mettre à niveau la bibliothèque cliente](sql-database-elastic-scale-upgrade-client-library.md)
### [Connexions](sql-database-manage-logins.md)

## Développement
### [Vue d’ensemble](https://msdn.microsoft.com/library/mt763826.aspx)
### [Bibliothèques de connectivité](sql-database-libraries.md)
### Scénarios
#### [Applications SaaS multi-locataires](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Données JSON](sql-database-json-features.md)
#### [Tables temporelles](sql-database-temporal-tables.md)
#### [Stratégies de rétention](sql-database-temporal-tables-retention-policy.md)
### Se connecter à une application
#### [.NET](sql-database-develop-dotnet-simple.md)
#### [Java](sql-database-develop-java-simple.md)
#### [Node.JS](sql-database-develop-nodejs-simple.md)
#### [PHP](sql-database-develop-php-simple.md)
#### [Python](sql-database-develop-python-simple.md)
#### [Ruby](sql-database-develop-ruby-simple.md)
#### [Excel](sql-database-connect-excel.md)
#### [Visual Studio](sql-database-connect-query.md)

### [Créer des bases de données](sql-database-get-started-powershell.md)
### Gérer les pools élastiques
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### [Obtenir les valeurs requises pour l’authentification d’une application](sql-database-client-id-keys.md)
### [Utiliser des ports au-delà de 1433 pour ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
### [Utiliser des messages d’erreur](sql-database-develop-error-messages.md)
### [Utiliser le traitement par lot](sql-database-use-batching-to-improve-performance.md)

### Référence
#### [.NET (concepts)](https://msdn.microsoft.com/library/kb9s9ks0.aspx)
#### [.NET (informations de référence sur l’API)](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)
#### [Entity Framework](https://www.nuget.org/packages/EntityFramework/)
#### [Kit de développement logiciel (SDK) Azure (téléchargement)](https://www.visualstudio.com/vs/azure-tools/)
#### [SDK Azure (documentation)](../dotnet-sdk.md)
#### [Applets de commande PowerShell Gestion des services](https://msdn.microsoft.com/library/azure/dn546723.aspx)
#### REST
##### [REST (gestion des ressources)](https://msdn.microsoft.com/library/azure/mt420159)
##### [REST (gestion des services)](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## Détecter les menaces
### [Détection de menaces](sql-database-threat-detection-get-started.md)
### [Pare-feu](sql-database-firewall-configure.md)

## Chiffrer les données
### [Présentation d’Always Encrypted](sql-database-always-encrypted.md)
### [Azure Key Vault Always Encrypted](sql-database-always-encrypted-azure-key-vault.md)
### [Chiffrement transparent des données](https://msdn.microsoft.com/library/azure/dn948096)
### [Chiffrement de colonne](https://msdn.microsoft.com/library/azure/ms179331)

## Masquer les données
### [Masquage des données dynamiques](sql-database-dynamic-data-masking-get-started-portal.md)
### [Clients de niveau inférieur](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)

## Migrer
### Déterminer la compatibilité
#### [Utilitaire de package SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Correction des problèmes de compatibilité
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Assistant Migration SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Utiliser l’Assistant Migration SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [Utiliser la réplication transactionnelle](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### [Migrer des bases de données existantes avec montée en charge vers des bases de données mises à l’échelle](sql-database-elastic-convert-to-use-elastic-tools.md)

## Surveiller et régler
### [Bases de données uniques](sql-database-performance-guidance.md)
### [Query Performance Insight](sql-database-query-performance.md)
### [SQL Database Advisor](sql-database-advisor-portal.md)
### [Performances de base de données](sql-database-single-database-monitor.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [Niveaux de compatibilité](sql-database-compatibility-level-query-performance-130.md)
### [Audit d’évènement](sql-database-auditing-get-started.md)
### [Compteurs de performances pour le Gestionnaire de cartes de partitions](sql-database-elastic-database-perf-counters.md)
### [Conseils sur le réglage des performances](sql-database-troubleshoot-performance.md)
### Modifier les niveaux de service et de performances
#### [portail Azure](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
### OLTP In-Memory.
#### [Adopter OLTP In-Memory](sql-database-in-memory-oltp-migration.md)
#### [Surveiller le stockage OLTP In-Memory](sql-database-in-memory-oltp-monitoring.md)
### Magasin de requêtes
#### [Analyse des performances à l’aide du magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Scénarios d’utilisation du magasin de requêtes](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Fonctionnement du magasin de requête](sql-database-operate-query-store.md)
### Événements étendus
#### [Événements étendus](sql-database-xevent-db-diff-from-svr.md)
#### [Code cible du fichier d’événements](sql-database-xevent-code-event-file.md)
#### [Code cible de mémoire tampon en anneau](sql-database-xevent-code-ring-buffer.md)

## Transfert de données
### [Copier une base de données SQL](sql-database-copy.md)
#### [Portail Azure](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Exporter la base de données vers un fichier BACPAC
#### [Portail Azure](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Utilitaire de package SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importer la base de données à partir d’un fichier BACPAC
#### [Portail Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Utilitaire de package SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Charger à partir d’un fichier CSV à l’aide de BCP](sql-database-load-from-csv-with-bcp.md)
### [Déplacement de données entre des bases de données cloud mises à l’échelle](sql-database-elastic-scale-overview-split-and-merge.md)

## Interroger
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Interrogation sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md)
### Requêtes entre plusieurs bases de données
#### [Vue d’ensemble](sql-database-elastic-query-overview.md)
#### [Entre l’interrogation de la base de données avec des schémas différents](sql-database-elastic-query-vertical-partitioning.md)
#### [Entre la génération de rapports de base de données](sql-database-elastic-query-horizontal-partitioning.md)
#### [Transactions distribuées entre bases de données cloud](sql-database-elastic-transactions-overview.md)

## Restauration
### Restauration d’une base de données supprimée
#### [Portail Azure](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### Restauration dans le temps
#### [Portail Azure](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### Restauration géographique
#### [Portail Azure](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Table unique](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [Récupérer après une panne de centre de données](sql-database-disaster-recovery.md)
### [Effectuer une simulation de récupération d’urgence](sql-database-disaster-recovery-drills.md)

## Réplication
### [Présentation de la géo-réplication active](sql-database-geo-replication-overview.md)
### Configuration
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### Basculement
#### [portail Azure](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## Résolution des problèmes
### [Problèmes de connexion](sql-database-troubleshoot-common-connection-issues.md)
### [Erreur de connexion temporaire](sql-database-troubleshoot-connection.md)
### [Diagnostiquer et prévenir](sql-database-connectivity-issues.md)
### [Autorisations](sql-database-troubleshoot-permissions.md)
### [Déplacement de bases de données](sql-database-troubleshoot-moving-data.md)

# Référence
## [PowerShell](/powershell/azureps-cmdlets-docs/)
## [PowerShell Classic](/powershell/servicemanagement/)
## [Java](/java/api/)
## [.NET](/dotnet/api/)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [Applets de commande Azure SQL Database] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
## [Applets de commande SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
## [REST](/rest/api/sql/)

## Bibliothèque de gestion SQL Database
### [Référence de la bibliothèque de gestion SQL Database](https://msdn.microsoft.com/library/azure/mt349017.aspx)
### [Obtenir le package de bibliothèque de gestion SQL Database](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Pilotes SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [Node.JS](https://msdn.microsoft.com/library/mt652093.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)

# Ressources
## [Tarification](https://azure.microsoft.com/pricing/details/sql-database/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?service=sql-database)
## [Outils SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [Outils SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)
## [Forum](https://social.msdn.microsoft.com/Forums/home?forum=ssdsgetstarted)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)


<!--HONumber=Nov16_HO2-->


