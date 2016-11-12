# Vue d'ensemble
## [Définition de la base de données SQL](sql-database-technical-overview.md)
## Caractéristiques
### [Niveaux de service](sql-database-service-tiers.md)
### [Qu’est-ce qu’une DWU ?](sql-database-what-is-a-dtu.md)
### [Présentation des tests d’évaluation DTU](sql-database-benchmark-overview.md)
### [Pare-feu de SQL Database et règles de pare-feu](sql-database-firewall-configure.md)
### [Outils de gestion](sql-database-manage-overview.md)
## Considérations et limitations
### [SQL Database/SQL sur une machine virtuelle](sql-database-paas-vs-sql-server-iaas.md)
### [Différences de T-SQL](sql-database-transact-sql-information.md)
### [Limites des ressources](sql-database-resource-limits.md)
### [Limitations générales](sql-database-general-limitations.md)
## [Tarification](https://azure.microsoft.com/pricing/details/sql-database/)
## [Nouveautés](https://azure.microsoft.com/updates/?service=sql-database)
## [Forum Aux Questions de base de données SQL](sql-database-faq.md)

## Avantages
### [Apprentissages et adaptations](sql-database-learn-and-adapt.md)
### [Mises à l’échelle immédiates](sql-database-scale-on-the-fly.md)
### [Création d’applications multi-locataires](sql-database-build-multi-tenant-apps.md)
### [Sécurité et protection](sql-database-helps-secures-and-protects.md)
### [Adapté à votre environnement](sql-database-works-in-your-environment.md)

## Scénarios
### Créer et gérer des serveurs, pools, bases de données et pare-feu
#### Créer et gérer des pools de bases de données élastiques
#### [Quand utiliser un pool de base de données élastique](sql-database-elastic-pool-guidance.md)
#### [Conseils de sécurité](sql-database-security-guidelines.md)
#### [Pool de bases de données élastique](sql-database-elastic-pool.md)
#### [Automation](sql-database-manage-automation.md)
#### Modifier les niveaux de service et les niveaux de performances
##### [[portail Azure](sql-database-scale-up.md)
##### [PowerShell](sql-database-scale-up-powershell.md)
### Créer et gérer des bases de données avec montée en charge
#### [Vue d'ensemble](sql-database-elastic-scale-introduction.md)
#### [Conception de bases de données cloud évolutives](sql-database-elastic-database-client-library.md)
#### [Monter en charge avec le Gestionnaire des cartes de partitions](sql-database-elastic-scale-shard-map-management.md)
#### [Migration de bases de données existantes pour une mise à l’échelle](sql-database-elastic-convert-to-use-elastic-tools.md)
#### [Déplacer les données entre les bases de données cloud avec montée en charge](sql-database-elastic-scale-overview-split-and-merge.md)
#### [Utiliser Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Utilisation de Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Compteurs de performances pour le Gestionnaire de cartes de partitions](sql-database-elastic-database-perf-counters.md)
#### Entre les requêtes et les travaux de base de données
##### [Entre l’interrogation de la base de données](sql-database-elastic-query-overview.md)
##### [Entre l’interrogation de la base de données avec des schémas différents](sql-database-elastic-query-vertical-partitioning.md)
##### [Entre la génération de rapports de base de données](sql-database-elastic-query-horizontal-partitioning.md)
##### [Entre les travaux de base de données](sql-database-elastic-jobs-overview.md)
##### [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)
##### [Interrogation sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md)
##### [Sécurité au niveau des lignes multi-locataires](sql-database-elastic-tools-multi-tenant-row-level-security.md)
##### [Transactions distribuées entre bases de données cloud](sql-database-elastic-transactions-overview.md)
####[Glossaire des outils de base de données élastique](sql-database-elastic-scale-glossary.md)
#### [FAQ](sql-database-elastic-scale-faq.md)
### Créer et gérer les accès et les autorisations
#### [Vue d'ensemble](sql-database-security.md)
#### [Conseils de sécurité](sql-database-security-guidelines.md)
#### [Gérer les connexions](sql-database-manage-logins.md)
#### [Azure Security Center pour Azure SQL Database](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
#### [Centre de sécurité SQL](https://msdn.microsoft.com/library/azure/bb510589)
### [Développement d’application et de base de données](sql-database-develop-overview.md)
### Migration de base de données
#### [Migrer une base de données SQL Server](sql-database-cloud-migrate.md)
### Fourniture pour la continuité des activités
#### [Vue d'ensemble](sql-database-business-continuity.md)
#### [Sauvegardes de base de données](sql-database-automated-backups.md) 
#### [Récupération de base de données à l’aide de sauvegardes](sql-database-recovery-using-backups.md)
#### [Récupérer après une panne de centre de données](sql-database-disaster-recovery.md)
#### [Exigences d’authentification pour la récupération d’urgence](sql-database-geo-replication-security-config.md)
#### [Scénarios de conception de la continuité d’activité](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Stratégies de récupération d’urgence avec les pools élastiques](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Mises à niveau propagées](sql-database-manage-application-rolling-upgrade.md)
### Surveiller et régler des bases de données
#### [Bases de données uniques](sql-database-single-database-monitor.md)
#### [Conseils sur les bases de données uniques](sql-database-performance-guidance.md)
#### [Informations sur la charge de travail dans le portail Azure](sql-database-performance.md)

## Implémentations clientes
### [Daxko/CSI Software](sql-database-implementation-daxko.md)
### [GEP](sql-database-implementation-gep.md)
### [SnelStart](sql-database-implementation-snelstart.md)
### [Umbraco](sql-database-implementation-umbraco.md)

# Prise en main
## Créer des serveurs, pools, bases de données et pare-feu
### [Portail Azure](sql-database-get-started.md)
### [PowerShell](sql-database-get-started-powershell.md)
### [C#](sql-database-get-started-csharp.md)
## Données de requête
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
## Gérer des serveurs, pools, bases de données et pare-feu
### [portail Azure](sql-database-manage-portal.md)
### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### [PowerShell](sql-database-manage-powershell.md)
## [Créer et gérer les accès et les autorisations](sql-database-get-started-security.md)
## Sécuriser et protéger des données
### [Audit](sql-database-auditing-get-started.md)
### [Détection de menaces](sql-database-threat-detection-get-started.md)
### Masquage des données dynamiques
#### [Portail Azure](sql-database-dynamic-data-masking-get-started-portal.md)
## Créer et gérer des bases de données avec montée en charge
### [Mise à l’échelle élastique](sql-database-elastic-scale-get-started.md)
### [Tâches élastiques](sql-database-elastic-jobs-getting-started.md)
### Requêtes élastiques
### [Entre les rapports de base de données](sql-database-elastic-query-getting-started.md)
### [Entre les requêtes de base de données](sql-database-elastic-query-getting-started-vertical.md)
## [Optimisation en mémoire](sql-database-in-memory.md)
## [Déplacement de bases de données](sql-database-troubleshoot-moving-data.md)
## [Synchronisation des données](sql-database-get-started-sql-data-sync.md)
##Surveiller et régler des bases de données
### [Présentation de SQL Database Advisor](sql-database-advisor.md)
### [Informations sur la charge de travail dans le portail Azure](sql-database-performance.md)
## [Démarrages rapides de solution](sql-database-solution-quick-starts.md)
# Procédure
## [Audit](sql-database-auditing-get-started.md)
## Créer
### Groupe de ressources
#### [PowerShell](sql-database-manage-powershell.md#create-a-resource-group)
### Serveurs
#### [portail Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-server)
### Pool de bases de données élastique
#### [Portail Azure](sql-database-elastic-pool-create-portal.md)
#### [C#](sql-database-elastic-pool-create-csharp.md)
#### [PowerShell](sql-database-elastic-pool-create-powershell.md)
### Bases de données
#### Bases de données uniques
##### [portail Azure](sql-database-get-started.md)
##### [C#](sql-database-get-started-csharp.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-get-started-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
#### Bases de données partitionnées
##### [Utiliser le Gestionnaire des cartes de partitions](sql-database-elastic-scale-shard-map-management.md)
##### [Configuration de sécurité de la fusion et du fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Utilisation de Dapper](sql-database-elastic-scale-working-with-dapper.md)
##### [Utiliser Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
##### [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)
##### [Sécurité au niveau des lignes multi-locataires](sql-database-elastic-tools-multi-tenant-row-level-security.md)
### Règles de pare-feu
#### Serveur
##### [Portail Azure](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [API REST](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### Base de données
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### Travaux
#### [Installation du service](sql-database-elastic-jobs-service-installation.md)
#### [Portail Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
### Connexions
#### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-logins.md)
## Développement
### [Vue d'ensemble](https://msdn.microsoft.com/library/mt763826.aspx)
### Scénarios
#### [Applications SaaS multi-locataires](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### Tables temporelles
##### [Tables temporelles](sql-database-temporal-tables.md)
##### [Stratégies de rétention](sql-database-temporal-tables-retention-policy.md)
#### [Données JSON](sql-database-json-features.md)
#### [En mémoire](sql-database-in-memory.md)
###Prise en main
#### [Bibliothèques de connectivité](sql-database-libraries.md)
#### Se connecter à une application
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.JS](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Se connecter avec Visual Studio](sql-database-connect-query.md)
### Procédures
#### Créer des serveurs
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### Créer des pools élastiques
###### [PowerShell](sql-database-elastic-pool-create-powershell.md)
###### [C#](sql-database-elastic-pool-create-csharp.md)
#### Créer des bases de données
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### Créer des règles de pare-feu
###### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
###### [API REST](sql-database-configure-firewall-settings-rest.md)
#### Gérer des serveurs, pools, bases de données et pare-feu
##### [PowerShell](sql-database-manage-powershell.md)
##### Gérer les pools élastiques
###### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
###### [C#](sql-database-elastic-pool-manage-csharp.md)
##### [Modifier les niveaux de service et de performances](sql-database-scale-up-powershell.md)
#### Transfert de données
##### [Exporter la base de données vers un fichier BACPAC](sql-database-export-powershell.md)
##### [Importer la base de données à partir d’un fichier BACPAC](sql-database-import-powershell.md)
##### [Copier une base de données vers un autre emplacement Azure](sql-database-copy-powershell.md)
#### [Obtenir les valeurs requises pour l’authentification d’une application](sql-database-client-id-keys.md)
#### [Tâches élastiques](sql-database-elastic-jobs-powershell.md)
#### Restaurer et récupérer une base de données
##### Restauration d’une base de données supprimée
###### [PowerShell](sql-database-restore-deleted-database-powershell.md)
##### Limite de restauration dans le temps d’une base de données
###### [PowerShell](sql-database-point-in-time-restore-powershell.md)
##### Restauration géographique
###### [PowerShell](sql-database-geo-restore-powershell.md)
#### Répliquer des données avec la géo-réplication active
##### Configuration
###### [PowerShell](sql-database-geo-replication-powershell.md)
##### Basculement
###### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [Utiliser des ports au-delà de 1433 pour ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Utiliser des messages d’erreur](sql-database-develop-error-messages.md)
#### [Utiliser le traitement par lot](sql-database-use-batching-to-improve-performance.md)
### Référence
#### [Transact-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
#### [Fournisseur de données .NET Framework pour SQL Server (concepts)](https://msdn.microsoft.com/library/kb9s9ks0.aspx)
#### [Fournisseur de données .NET Framework pour SQL Server (référence d’API)](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)
#### SQL PowerShell
##### [Applets de commande Azure SQL Database (gestion des ressources)] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
##### [Applets de commande Azure SQL Database (gestion des services)] (https://msdn.microsoft.com/library/azure/dn546723(v=azure.300\).aspx)
##### [Applets de commande SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
#### API REST SQL Database
##### [API REST (gestion des ressources)](https://msdn.microsoft.com/library/azure/mt420159)
##### [API REST (gestion des services)](https://msdn.microsoft.com/library/azure/dn505719.aspx)
#### Bibliothèque de gestion SQL Database
##### [Référence de la bibliothèque de gestion SQL Database](https://msdn.microsoft.com/library/azure/mt349017.aspx)
##### [Obtenir le package de bibliothèque de gestion SQL Database](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
#### Entity Framework
##### [Obtenir le package d’Entity Framework](https://www.nuget.org/packages/EntityFramework/)
#### [Pilotes SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
##### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
##### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
##### [Node.JS](https://msdn.microsoft.com/library/mt652093.aspx)
##### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
##### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
##### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
##### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
#### [Kit de développement logiciel (SDK) Azure (téléchargement)](https://www.visualstudio.com/vs/azure-tools/)
#### [SDK Azure (documentation)](https://azure.microsoft.com/documentation/articles/dotnet-sdk/)
### les ressources
#### [Outils SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
#### [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
#### [Outils SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
#### [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
#### [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
#### [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)
## Supprimer
### Base de données
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database)
### Serveur
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database-server)
## Détecter les menaces
### [Détection de menaces](sql-database-threat-detection-get-started.md)
### [Pare-feu](sql-database-firewall-configure.md)
## Chiffrer les données
### Always Encrypted
#### [Présentation d’Always Encrypted](sql-database-always-encrypted.md)
#### [Azure Key Vault Always Encrypted](sql-database-always-encrypted-azure-key-vault.md)
### [Chiffrement transparent des données](https://msdn.microsoft.com/library/azure/dn948096)
### [Chiffrement de colonne](https://msdn.microsoft.com/library/azure/ms179331)
## Gérer
###  Authentification
#### Authentification SQL
#### [Authentification Azure Active Directory](sql-database-aad-authentication.md)
#### [Azure Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### Serveurs
### Pools élastiques
#### [Portail Azure](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### Bases de données
#### Bases de données uniques
##### [Portail Azure](sql-database-manage-portal.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-blank)
#### Modifier les niveaux de service et de performances
#### [[portail Azure](sql-database-scale-up.md)
#### [PowerShell](sql-database-manage-powershell.md#change-the-performance-level-of-a-sql-database)
#### Bases de données partitionnées
##### [Migrer des bases de données existantes avec montée en charge vers des bases de données mises à l’échelle](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Gérer les informations d’identification](sql-database-elastic-scale-manage-credentials.md)
##### [Déplacement de données entre des bases de données cloud mises à l’échelle](sql-database-elastic-scale-overview-split-and-merge.md)
##### [Déployer un service de fractionnement et de fusion](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
##### [Ajouter une partition](sql-database-elastic-scale-add-a-shard.md)
##### [Utilisation de la classe RecoveryManager pour résoudre les problèmes de correspondance de partitionnement](sql-database-elastic-database-recovery-manager.md)
### Règles de pare-feu
#### Serveur
##### [Portail Azure](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [API REST](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### Base de données
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### Travaux
#### [Installation du service](sql-database-elastic-jobs-service-installation.md)
#### [portail Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Mettre à niveau la bibliothèque cliente](sql-database-elastic-scale-upgrade-client-library.md)
### Connexions
#### [Gérer les connexions](sql-database-manage-logins.md)
## Masquer les données
### Masquage des données dynamiques
#### [portail Azure](sql-database-dynamic-data-masking-get-started-portal.md)
#### [Clients de niveau inférieur](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
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
### Exporter la base de données vers un fichier BACPAC
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Utilitaire de package SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importer la base de données à partir d’un fichier BACPAC
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Utilitaire de package SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Portail Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)

## Surveiller et régler
### [Bases de données uniques](sql-database-performance-guidance.md)
### Pools élastiques
#### [portail Azure](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### [Query Performance Insight](sql-database-query-performance.md)
### SQL Database Advisor
#### [Portail Azure](sql-database-advisor-portal.md)
### Modifier les niveaux de service et de performances
#### [portail Azure](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
### [Conseils sur le réglage des performances](sql-database-troubleshoot-performance.md)
### OLTP In-Memory.
#### [Adopter OLTP In-Memory](sql-database-in-memory-oltp-migration.md)
#### [Surveiller le stockage OLTP In-Memory](sql-database-in-memory-oltp-monitoring.md)
### Magasin de requêtes
#### [Analyse des performances à l’aide du magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Scénarios d’utilisation du magasin de requêtes](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Fonctionnement du magasin de requête](sql-database-operate-query-store.md)
### [Niveaux de compatibilité](sql-database-compatibility-level-query-performance-130.md)
### [Audit d’évènement](sql-database-auditing-get-started.md)
### [Compteurs de performances pour le Gestionnaire de cartes de partitions](sql-database-elastic-database-perf-counters.md)
### Événements étendus
#### [Événements étendus](sql-database-xevent-db-diff-from-svr.md)
#### [Code cible du fichier d’événements](sql-database-xevent-code-event-file.md)
#### [Code cible de mémoire tampon en anneau](sql-database-xevent-code-ring-buffer.md)
### DMV
#### [DMV](sql-database-monitoring-with-dmvs.md)
#### [DMV](sql-database-manage-azure-ssms#monitor-sql-database-using-dynamic-management-views


## Transfert de données
### Copier une base de données SQL
#### [Vue d'ensemble](sql-database-copy.md)
#### [Portail Azure](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Exporter la base de données vers un fichier BACPAC
#### [Portail Azure](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importer la base de données à partir d’un fichier BACPAC
#### [Portail Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
### [Synchronisation des données](sql-database-get-started-sql-data-sync.md)
### [Charger à partir d’un fichier CSV à l’aide de BCP](sql-database-load-from-csv-with-bcp.md)

## Interroger
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Interrogation sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md)
### Entre les requêtes de base de données
#### [Entre l’interrogation de la base de données avec des schémas différents](sql-database-elastic-query-vertical-partitioning.md)
#### [Entre la génération de rapports de base de données](sql-database-elastic-query-horizontal-partitioning.md)
#### [Transactions distribuées entre bases de données cloud](sql-database-elastic-transactions-overview.md)
#### [Mettre à niveau la bibliothèque cliente](sql-database-elastic-scale-upgrade-client-library.md)
#### [Interrogation sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md)

## Restauration
### Restauration d’une base de données supprimée
### [Portail Azure](sql-database-restore-deleted-database-portal.md)
### [PowerShell](sql-database-restore-deleted-database-powershell.md)
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
### Connectivité
#### [Problèmes de connexion](sql-database-troubleshoot-common-connection-issues.md)
#### [Erreur de connexion temporaire](sql-database-troubleshoot-connection.md)
#### [Diagnostiquer et prévenir](sql-database-connectivity-issues.md)
### [Autorisations](sql-database-troubleshoot-permissions.md)

# Référence
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## SQL PowerShell
### [Applets de commande Azure SQL Database (gestion des ressources)] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
### [Applets de commande SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
## API REST SQL Database
### [API REST (gestion des ressources)](https://msdn.microsoft.com/library/azure/mt420159)
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

# les ressources
## [Outils SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [Outils SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)

<!--HONumber=Nov16_HO2-->


