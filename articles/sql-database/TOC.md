# Vue d'ensemble
## [Définition de la base de données SQL](sql-database-technical-overview.md)
### [Niveaux de service](sql-database-service-tiers.md)
### [DTU et eDTU](sql-database-what-is-a-dtu.md)
### [Présentation des tests d’évaluation DTU](sql-database-benchmark-overview.md)
### [Limites des ressources](sql-database-resource-limits.md)
### [Caractéristiques](sql-database-features.md)
### [Forum Aux Questions de base de données SQL](sql-database-faq.md)
## Avantages
### [Apprentissages et adaptations](sql-database-learn-and-adapt.md)
### [Mises à l’échelle immédiates](sql-database-scale-on-the-fly.md)
### [Création d’applications multi-locataires](sql-database-build-multi-tenant-apps.md)
### [Sécurité et protection](sql-database-helps-secures-and-protects.md)
### [Adapté à votre environnement](sql-database-works-in-your-environment.md)
## Comparaisons
### [SQL Database/SQL sur une machine virtuelle](sql-database-paas-vs-sql-server-iaas.md)
### [Différences de T-SQL](sql-database-transact-sql-information.md)
### [SQL/NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [Outils de base de données SQL](sql-database-manage-overview.md)
## [Didacticiels relatifs à la base de données SQL](sql-database-explore-tutorials.md)
## [Démarrages rapides de solution](sql-database-solution-quick-starts.md)
## Sécurité
### [Azure Security Center pour Azure SQL Database](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [Centre de sécurité SQL](https://msdn.microsoft.com/library/azure/bb510589)
# Prise en main
## Bases de données et serveurs
### Découvrir
#### [Serveurs](sql-database-server-overview.md)
#### [Bases de données uniques](sql-database-overview.md)
#### [Bases de données multiples](sql-database-elastic-scale-introduction.md)
##### Mappage des locataires
###### [Bibliothèque cliente élastique](sql-database-elastic-database-client-library.md)
###### [Gestionnaire des cartes de partitions](sql-database-elastic-scale-shard-map-management.md)
###### [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)
###### [Gérer les informations d’identification](sql-database-elastic-scale-manage-credentials.md)
###### [Interrogation sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md)
##### Pools élastiques (listes de ressources partagées)
###### [Présentation d’un pool élastique](sql-database-elastic-pool.md)
###### [Quand utiliser un pool élastique](sql-database-elastic-pool-guidance.md)
###### [Tarification du pool élastique](sql-database-elastic-pool-price.md)
##### Bases de données partitionnées
###### [Glossaire des outils élastiques](sql-database-elastic-scale-glossary.md)
###### [Déplacement des données entre les partitions](sql-database-elastic-scale-overview-split-and-merge.md)
###### [FAQ sur les outils élastiques](sql-database-elastic-scale-faq.md)
##### Requête élastique (requêtes entre plusieurs bases de données)
###### [Présentation d’une requête élastique](sql-database-elastic-query-overview.md)
##### Transactions élastiques (transactions distribuées)
###### [Transactions entre bases de données cloud](sql-database-elastic-transactions-overview.md)
##### Travaux élastiques (travaux entre plusieurs bases de données)
###### [Présentation d’un travail élastique](sql-database-elastic-jobs-overview.md)
#### [Connexion à SQL Database à l’aide d’Azure RemoteApp](sql-database-ssms-remoteapp.md)
#### [Gestion des bases de données SQL à l’aide du service Azure Automation](sql-database-manage-automation.md)
### À faire
#### [Créer une base de données unique à l’aide du portail Azure](sql-database-get-started.md)
#### [Créer une base de données unique à l’aide de PowerShell](sql-database-get-started-powershell.md)
#### [Créer une base de données unique en C#](sql-database-get-started-csharp.md)
#### [Créer une application partitionnée](sql-database-elastic-scale-get-started.md)
#### [Déplacer des données entre les partitions](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Bien démarrer avec les travaux élastiques](sql-database-elastic-jobs-getting-started.md)
#### [Bien démarrer avec les requêtes élastiques](sql-database-elastic-query-getting-started-vertical.md)
#### [Créer des rapports à l’aide d’une requête élastique](sql-database-elastic-query-getting-started.md)
#### [Interroger des bases de données de schémas différents](sql-database-elastic-query-vertical-partitioning.md)
#### [Création de rapports sur des bases de données montées en charge](sql-database-elastic-query-horizontal-partitioning.md)
## Migrer et déplacer des données
### Découvrir
#### [Migrer une base de données](sql-database-cloud-migrate.md)
#### [Réplication transactionnelle](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Synchronisation des données](sql-database-get-started-sql-data-sync.md)
#### [Copier une base de données SQL](sql-database-copy.md)
## Règles de pare-feu, authentification et autorisation
### Découvrir
#### [Vue d’ensemble](sql-database-security.md)
#### [Conseils de sécurité](sql-database-security-guidelines.md)
#### [Pare-feu](sql-database-firewall-configure.md)
#### [Gérer les connexions](sql-database-manage-logins.md)
### À faire
#### [Authentification et autorisation SQL](sql-database-get-started-security.md)
## Sécuriser et protéger des données
### Découvrir
#### Audit
##### [Audit](sql-database-auditing-get-started.md)
##### [Prise en charge des clients de niveau inférieur et modification des points de terminaison IP à des fins d’audit](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Détection de menaces](sql-database-threat-detection-get-started.md)
#### Chiffrer les données
##### [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
##### [Chiffrement transparent des données](https://msdn.microsoft.com/library/azure/dn948096)
##### [Chiffrement de colonne](https://msdn.microsoft.com/library/azure/ms179331)
#### Masquer les données
##### Masquage des données dynamiques
###### [portail Azure](sql-database-dynamic-data-masking-get-started.md)
### À faire
#### [Masquage des données dynamiques à l’aide du portail Azure](sql-database-dynamic-data-masking-get-started.md)
##### [Always Encrypted à l’aide du magasin de certificats Windows](sql-database-always-encrypted.md)
## Continuité de l’activité
### Découvrir
#### [Vue d'ensemble](sql-database-business-continuity.md)
#### [Sauvegardes de base de données](sql-database-automated-backups.md)
#### [Rétention à long terme](sql-database-long-term-retention.md)
#### [Récupération de base de données à l’aide de sauvegardes](sql-database-recovery-using-backups.md)
#### [Récupérer une seule table](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Récupérer après une panne de centre de données](sql-database-disaster-recovery.md)
#### [Exigences d’authentification pour la récupération d’urgence](sql-database-geo-replication-security-config.md)
#### [Scénarios de conception de la continuité d’activité](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Stratégies de récupération d’urgence avec les pools élastiques](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Mises à niveau propagées](sql-database-manage-application-rolling-upgrade.md)
#### [Effectuer une simulation de récupération d’urgence](sql-database-disaster-recovery-drills.md)
#### [Présentation de la géo-réplication active](sql-database-geo-replication-overview.md)
### À faire
#### [Bien démarrer avec la sauvegarde et la restauration SQL Database](sql-database-get-started-backup-recovery.md)
## Développement d’applications
### Découvrir
#### [Vue d’ensemble du développement d’applications de base de données](sql-database-develop-overview.md)
#### [Bibliothèques de connectivité](sql-database-libraries.md)
#### [Applications SaaS multi-locataires](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Mise à l’échelle des applications SaaS multi-locataires avec la sécurité au niveau de la ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Utiliser des ports au-delà de 1433 pour ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Obtenir les valeurs requises pour l’authentification d’une application](sql-database-client-id-keys.md)
### À faire
#### Se connecter à une application
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C et C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.JS](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Se connecter avec Visual Studio](sql-database-connect-query.md)
#### [Créer une application cliente](https://www.microsoft.com/sql-server/developer-get-started)
#### [Utiliser des messages d’erreur](sql-database-develop-error-messages.md)
#### [Utiliser Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Utiliser une bibliothèque cliente avec Dapper](sql-database-elastic-scale-working-with-dapper.md)
### Implémentations clientes
#### [Daxko/CSI Software](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## Développement de base de données
### Découvrir
#### Tables temporelles
##### [Tables temporelles](sql-database-temporal-tables.md)
##### [Stratégies de rétention](sql-database-temporal-tables-retention-policy.md)
#### [Données JSON](sql-database-json-features.md)
#### [Optimisation en mémoire](sql-database-in-memory.md)
### À faire
#### [Développement de SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
#### [Adopter OLTP In-Memory](sql-database-in-memory-oltp-migration.md)
## Analyse et paramétrage
### Découvrir
#### [Bases de données uniques](sql-database-single-database-monitor.md)
#### [Présentation de SQL Database Advisor](sql-database-advisor.md)
#### [Conseils sur les bases de données uniques](sql-database-performance-guidance.md)
#### [Informations sur la charge de travail dans le portail Azure](sql-database-performance.md)
#### [Utiliser le traitement par lot](sql-database-use-batching-to-improve-performance.md)
#### [Événements étendus](sql-database-xevent-db-diff-from-svr.md)
## SQL Database V11
### [Disparition des éditions Web et Business](sql-database-web-business-sunset-faq.md)
### [Service Tier Advisor](sql-database-service-tier-advisor.md)
### [Outil d’évaluation de pool élastique](sql-database-elastic-pool-database-assessment-powershell.md)
### [Mise à niveau vers la version V12](sql-database-v12-plan-prepare-upgrade.md)
#### [Mise à niveau à l’aide du portail Azure](sql-database-upgrade-server-portal.md)
#### [Mise à niveau à l’aide de PowerShell](sql-database-upgrade-server-powershell.md)
# Procédure
## Créer et gérer
### [Gérer SQL Database à l’aide du portail Azure](sql-database-manage-portal.md)
### [Gérer SQL Database à l’aide de PowerShell](sql-database-manage-powershell.md)
### [Gérer SQL Database à l’aide de SSMS](sql-database-manage-azure-ssms.md)
### Serveurs
#### [Créer des serveurs](sql-database-create-servers.md)
#### [Afficher ou mettre à jour les paramètres du serveur](sql-database-view-update-server-settings.md)
### Bases de données uniques
#### [Créer des bases de données uniques](sql-database-create-databases.md)
#### [Afficher ou mettre à jour les paramètres de base de données](sql-database-view-update-database-settings.md)
### Règles de pare-feu
#### [Créer des règles de pare-feu à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
#### [Créer des règles de pare-feu à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [Créer des règles de pare-feu à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)
#### [Créer des règles de pare-feu à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Bases de données multiples
#### [Mettre à niveau une bibliothèque cliente dans des applications clientes](sql-database-elastic-scale-upgrade-client-library.md)
#### Bases de données partitionnées
##### [Configuration de la sécurité](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Ajouter une partition](sql-database-elastic-scale-add-a-shard.md)
##### [Résoudre les problèmes de carte de partitions](sql-database-elastic-database-recovery-manager.md)
##### [Migrer des bases de données montées en charge existantes vers des bases de données partitionnées](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Créer des compteurs de performances pour le Gestionnaire de cartes de partitions](sql-database-elastic-database-perf-counters.md)
#### Tâches élastiques
##### [Installation du service des travaux élastiques](sql-database-elastic-jobs-service-installation.md)
##### [Créer et gérer des tâches élastiques à l’aide de PowerShell](sql-database-elastic-jobs-powershell.md) 
##### [Créer et gérer des travaux élastiques dans le portail Azure](sql-database-elastic-jobs-create-and-manage.md)
##### [Désinstallation des travaux élastiques](sql-database-elastic-jobs-uninstall.md)
#### Pools élastiques
##### [Création à l’aide du portail Azure](sql-database-elastic-pool-create-portal.md)
##### [Création à l’aide de PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Création à l’aide de C#](sql-database-elastic-pool-create-csharp.md)
##### [Gestion à l’aide du portail Azure](sql-database-elastic-pool-manage-portal.md)
##### [Gestion à l’aide de PowerShell](sql-database-elastic-pool-manage-powershell.md)
##### [Gestion à l’aide de C#](sql-database-elastic-pool-manage-csharp.md)
##### [Gestion à l’aide de T-SQL](sql-database-elastic-pool-manage-tsql.md)
##  Authentifier et autoriser
### [Authentification Azure AD](sql-database-aad-authentication.md)
### [Azure Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
## Chiffrer les données
### [Chiffrement transparent des données](https://msdn.microsoft.com/library/azure/dn948096)
### [Chiffrement de colonne](https://msdn.microsoft.com/library/azure/ms179331)
## Migrer des bases de données
### Déterminer la compatibilité
#### [Déterminer la compatibilité à l’aide de l’utilitaire de package SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [Déterminer la compatibilité à l’aide de SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Correction des problèmes de compatibilité
#### [Résoudre les problèmes de compatibilité à l’aide de SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [Résoudre les problèmes de compatibilité à l’aide de SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Résoudre les problèmes de compatibilité à l’aide de SMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Migrer à l’aide de l’Assistant Migration SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
## Surveiller et régler
### [Query Performance Insight](sql-database-query-performance.md)
### [SQL Database Advisor](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [Niveaux de compatibilité](sql-database-compatibility-level-query-performance-130.md)
### [Conseils sur le réglage des performances](sql-database-troubleshoot-performance.md)
### Modifier les niveaux de service et de performances
#### [Modifier les niveaux de service à l’aide du portail Azure](sql-database-scale-up.md)
#### [Modifier les niveaux de service à l’aide de PowerShell](sql-database-scale-up-powershell.md)
### [Créer des alertes](sql-database-insights-alerts-portal.md)
#### [Surveiller le stockage OLTP In-Memory](sql-database-in-memory-oltp-monitoring.md)
### Magasin de requêtes
#### [Analyse des performances à l’aide du magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Scénarios d’utilisation du magasin de requêtes](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Fonctionnement du magasin de requête](sql-database-operate-query-store.md)
### Événements étendus
#### [Code cible du fichier d’événements](sql-database-xevent-code-event-file.md)
#### [Code cible de mémoire tampon en anneau](sql-database-xevent-code-ring-buffer.md)
## Transfert de données
### Copier une base de données SQL
#### [Copier à l’aide du portail Azure](sql-database-copy-portal.md)
#### [Copier à l’aide de PowerShell](sql-database-copy-powershell.md)
#### [Copier à l’aide de T-SQL](sql-database-copy-transact-sql.md)
### Exporter la base de données vers un fichier BACPAC
#### [Exporter via le portail Azure](sql-database-export.md)
#### [Exporter via SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Exporter à l’aide de l’utilitaire de package SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [Exporter à l’aide de PowerShell](sql-database-export-powershell.md)
### Importer la base de données à partir d’un fichier BACPAC
#### [Importer via le portail Azure](sql-database-import.md)
#### [Importer à l’aide de PowerShell](sql-database-import-powershell.md)
#### [Importer via SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Importer à l’aide de l’utilitaire de package SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Charger à partir d’un fichier CSV à l’aide de BCP](sql-database-load-from-csv-with-bcp.md)
## Requête
### [Requête via SSMS](sql-database-connect-query-ssms.md)
## Sauvegarde et restauration
### Rétention des sauvegardes à long terme
#### [Configurer la rétention des sauvegardes à long terme](sql-database-configure-long-term-retention.md)
#### [Afficher les sauvegardes dans un coffre Recovery Services](sql-database-view-backups-in-vault.md)
#### [Restaurer à partir d’une rétention des sauvegardes à long terme](sql-database-restore-from-long-term-retention.md)
### Restauration d’une base de données supprimée
#### [Restaurer une base de données supprimée à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md)
#### [Restaurer une base de données supprimée à l’aide de PowerShell](sql-database-restore-deleted-database-powershell.md)
### Restauration dans le temps
#### [Restaurer à un point dans le temps](sql-database-point-in-time-restore.md)
#### [Afficher le point de restauration le plus ancien](sql-database-view-oldest-restore-point.md)
### Géo-restauration
#### [Géo-restauration à l’aide du portail Azure](sql-database-geo-restore-portal.md)
#### [Géo-restauration à l’aide de PowerShell](sql-database-geo-restore-powershell.md)
## Géoréplication active
### [Configurer à l’aide du portail Azure](sql-database-geo-replication-portal.md)
### [Configurer à l’aide de PowerShell](sql-database-geo-replication-powershell.md)
### [Configurer à l’aide de T-SQL](sql-database-geo-replication-transact-sql.md)
### [Basculement via le portail Azure](sql-database-geo-replication-failover-portal.md)
### [Basculement à l’aide de PowerShell](sql-database-geo-replication-failover-powershell.md)
### [Basculement à l’aide de T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Résolution des problèmes
### [Problèmes de connexion](sql-database-troubleshoot-common-connection-issues.md)
### [Erreur de connexion temporaire](sql-database-troubleshoot-connection.md)
### [Diagnostiquer et prévenir](sql-database-connectivity-issues.md)
### [Autorisations](sql-database-troubleshoot-permissions.md)
### [Déplacement de bases de données](sql-database-troubleshoot-moving-data.md)
# Référence
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (base de données élastique)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.JS](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST](https://msdn.microsoft.com/library/azure/mt163571.aspx)

# Rubriques connexes
## Bibliothèque de gestion SQL Database
### [Obtenir le package de bibliothèque de gestion SQL Database](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Pilotes SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)

# les ressources
## [Tarification](https://azure.microsoft.com/pricing/details/sql-database/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-azure)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [Mises à jour de service](https://azure.microsoft.com/updates/?service=sql-database)
## [Outils SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [Outils SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)


<!--HONumber=Jan17_HO3-->


