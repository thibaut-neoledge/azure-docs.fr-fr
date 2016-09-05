<properties
	pageTitle="Toutes les rubriques relatives au service Base de données SQL Azure | Microsoft Azure"
	description="Tableau de toutes les rubriques du service Azure nommé Base de données SQL qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="genemi"/>


# Toutes les rubriques relatives au service Base de données SQL Azure

Cette rubrique répertorie toutes les rubriques qui s’appliquent directement au service de **Base de données SQL** d’Azure. Pour trouver les rubriques qui vous intéressent, vous pouvez rechercher des mots clés sur cette page web à l'aide de la combinaison de touches **Ctrl+F**.




## Nouveau

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 1 | [Prise en main des fonctionnalités JSON dans Base de données SQL Azure](sql-database-json-features.md) | Base de données SQL Azure vous permet d’analyser, d’interroger et de mettre en forme des données dans une notation JavaScript Object Notation (JSON). |
| 2 | [Prise en charge de SSMS pour Azure AD MFA avec la base de données SQL et SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) | Utilisez l’authentification multi-facteur avec SSMS pour la base de données SQL et SQL Data Warehouse. |


## Articles mis à jour

Cette section répertorie les articles qui ont été mis à jour récemment et dont la mise à jour a été volumineuse ou importante. Pour chaque article mis à jour, un extrait de code approximatif du texte markdown ajouté s’affiche. Les articles ont été mis à jour dans la plage de dates allant du **2016-07-26** au **2016-08-21**.

| &nbsp; | Article | Texte mis à jour, extrait de code |
| --: | :-- | :-- |
| 3 | [Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell](sql-database-export-powershell.md) | $subscriptionId = "YOUR AZURE SUBSCRIPTION ID" Login-AzureRmAccount Set-AzureRmContext -SubscriptionId $subscriptionId Database to export $DatabaseName = "DATABASE-NAME" $ResourceGroupName = "RESOURCE-GROUP-NAME" $ServerName = "SERVER-NAME" $serverAdmin = "ADMIN-NAME" $serverPassword = "ADMIN-PASSWORD" $securePassword = ConvertTo-SecureString ΓÇôString $serverPassword ΓÇôAsPlainText -Force $creds = New-Object ΓÇôTypeName System.Management.Automation.PSCredential ΓÇôArgumentList $serverAdmin, $securePassword Generate a unique filename for the BACPAC $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac" Storage account info for the BACPAC $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/" $BacpacUri = $BaseStorageUri + $bacpacFilename $StorageKeytype = "StorageAccessKey" $StorageKey = "YOUR STORAGE KEY" $exportRequest = New-AzureRmSqlDatabaseExpo |
| 4 | [Options et performances de la base de données SQL : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md) | ** Choix d’un niveau de service** Pour choisir un niveau de service, commencez par déterminer si la base de données sera autonome ou fera partie d’un pool élastique. ** Choix d’un niveau de service pour une base de données autonome** Pour choisir un niveau de service pour une base de données autonome, commencez par déterminer les caractéristiques de base de données dont vous avez besoin afin de savoir pour quelle édition du service Base de données SQL opter : / Taille de la base de données (5 Go maximum pour l’édition De base, 250 Go maximum pour l’édition Standard et 500 Go à 1 To pour l’édition Premium, selon le niveau de performances) / Période de rétention des sauvegardes de base de données (7 jours pour l’édition De base, 35 jours pour l’édition Standard et 35 jours pour l’édition Premium). Une fois que vous avez choisi l’édition du service Base de données SQL, vous êtes prêt à déterminer le niveau de performances (nombre de DTU) requis pour la base de données. Vous pouvez évaluer approximativement, puis augmenter ou réduire dynamiquement l’échelle (sql-database-scale-up.md) en fonction de l’expérience réelle. Vous pouvez également utiliser le Calculateur DTU (http://dtucalculator.azurewebsites.net/) pour estimer le nombre de DTU nécessaire. ** C |





## Prise en main

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 5 | [Créer des applications mutualisées isolées et efficaces avec la base de données SQL Azure](sql-database-build-multi-tenant-apps.md) | Découvrez comment créer des applications mutualisées avec le service Base de données SQL Azure |
| 6 | [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md) | Découvrez comment vous connecter à Base de données SQL sur Azure avec SQL Server Management Studio (SSMS). Ensuite, exécutez un exemple de requête à l’aide de Transact-SQL (T-SQL). |
| 7 | [Connexion à SQL Database à l’aide de .NET (C#)](sql-database-develop-dotnet-simple.md) | Utilisez l'exemple de code dans cette prise en main pour créer une application moderne avec C# et soutenue par une base de données relationnelle puissante dans le cloud avec la base de données SQL Azure. |
| 8 | [Créer un pool de base de données élastique avec le portail Azure](sql-database-elastic-pool-create-portal.md) | Comment ajouter un pool de base de données élastique évolutive à votre configuration de base de données SQL pour faciliter l’administration et le partage des ressources entre plusieurs bases de données. |
| 9 | [Exploration des didacticiels relatifs à la base de données SQL Azure](sql-database-explore-tutorials.md) | En savoir plus sur les fonctions et capacités de la base de données SQL |
| 10 | [Didacticiel sur la base de données SQL : Créer une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md) | Découvrez comment configurer un serveur logique de base de données SQL, une règle de pare-feu de serveur, une base de données SQL, des exemples de données, mais également comment vous connecter avec des outils clients, configurer les utilisateurs et des règles de pare-feu de base de données. |
| 11 | [Sécurité et protection avec la base de données SQL Azure](sql-database-helps-secures-and-protects.md) | Découvrez comment le service Base de données SQL renforce la sécurité et la protection des données |
| 12 | [Le service Base de données SQL Azure apprend et s’adapte](sql-database-learn-and-adapt.md) | Découvrez comment le service Base de données SQL Azure apprend et s’adapte |
| 13\. | [Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Découvrez l’option SQL Server cloud adaptée à votre application : Azure SQL (PaaS) Database ou SQL Server sur les machines virtuelles Azure. |
| 14 | [Mises à l’échelle instantanées de bases de données SQL Azure](sql-database-scale-on-the-fly.md) | Apprenez comment mettre à l’échelle de manière instantanée les bases de données SQL |
| 15 | [Explorer les démarrages rapides de la solution de base de données SQL Azure](sql-database-solution-quick-starts.md) | Découvrez les solutions de base de données SQL Azure |
| 16 | [Base de données SQL Azure adaptée à votre environnement](sql-database-works-in-your-environment.md) | Découvrez comment les bases de données SQL améliorent, sécurisent et protègent vos données |



## Générer une application

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 17 | [Diagnostiquer, résoudre et empêcher les erreurs de connexion SQL et les erreurs temporaires de Base de données SQL](sql-database-connectivity-issues.md) | Découvrez comment diagnostiquer, résoudre et empêcher une erreur de connexion SQL ou une erreur temporaire dans Base de données SQL Azure. |
| 18 | [Se connecter à la base de données SQL avec Visual Studio](sql-database-connect-query.md) | Écrivez un programme en C# pour interroger et vous connecter à une base de données SQL. Informations sur les adresses IP, chaînes de connexion, connexion sécurisée et Visual Studio gratuit. |
| 19 | [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md) | Parfois, les connexions clientes entre ADO.NET et Azure SQL Database V12 ignorent le proxy et interagissent directement avec la base de données. Les ports autres que le port 1433 deviennent importants. |
| 20 | [Codes d’erreur SQL pour les applications clientes SQL Database : erreur de connexion à la base de données et autres problèmes](sql-database-develop-error-messages.md) | En savoir plus sur les codes d’erreur SQL pour les applications clientes SQL Database, tels que les erreurs de connexion de base de données courantes, les problèmes de copie de base de données et les erreurs générales. |
| 21 | [Connexion à la base de données SQL à l'aide de PHP sous Windows](sql-database-develop-php-simple.md) | Présente un exemple de programme PHP qui se connecte à la base de données SQL Azure à partir d'un client Windows et fournit des liens vers les composants logiciels nécessaires requis par le client. |
| 22 | [Essayer la Base de données SQL : Utiliser C# pour créer une Base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md) | Essayez la base de données SQL pour développer des applications SQL et C# et créez une base de données SQL Azure avec C# à l’aide de la bibliothèque de base de données SQL pour .NET. |
| 23 | [Résoudre des problèmes de connexion à la base de données SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Opérations servant à identifier et résoudre les erreurs de connexion courantes pour Azure SQL Database. |
| 24 | [Erreur « La base de données sur le serveur n’est pas disponible actuellement » lors de la connexion à SQL Database](sql-database-troubleshoot-connection.md) | Dépannage de l’erreur « La base de données sur le serveur n’est pas disponible actuellement » quand une application se connecte à SQL Database. |



## Développement

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 25 | [Obtenir l’ID client et la clé pour la connexion à une base de données SQL à partir du code](sql-database-client-id-keys.md) | Obtenez l’ID client et la clé pour l’accès à la base de données SQL à partir du code. |
| 26 | [Connexion à la base de données SQL à l’aide de Java avec JDBC sous Windows](sql-database-develop-java-simple.md) | Cette rubrique présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L'exemple utilise JDBC et s'exécute sur un ordinateur client Windows. |
| 27 | [Connexion à SQL Database à l’aide de Node.js](sql-database-develop-nodejs-simple.md) | Cette rubrique présente un exemple de code Node.js que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. |
| 28 | [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) | En savoir plus sur les bibliothèques de connectivité disponibles et les meilleures pratiques pour les applications qui utilisent une connexion à la base de données SQL. |
| 29 | [Connexion à SQL Database à l’aide de Python](sql-database-develop-python-simple.md) | Cette rubrique présente un exemple de code Python que vous pouvez utiliser pour vous connecter à une base de données SQL Azure. |
| 30 | [Connexion à SQL Database à l’aide de Ruby](sql-database-develop-ruby-simple.md) | Offre un exemple de code Ruby que vous pouvez exécuter pour vous connecter à la base de données SQL Azure. |
| 31 | [Prise en main des tables temporelles dans Azure SQL Database](sql-database-temporal-tables.md) | Découvrez comment prendre en main les tables temporelles dans Azure SQL Database. |



## Performances et mise à l’échelle

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 32 | [SQL Database Advisor](sql-database-advisor.md) | Azure SQL Database Advisor fournit des recommandations pour vos bases de données SQL existantes afin d’améliorer les performances actuelles des requêtes. |
| 33 | [SQL Database Advisor](sql-database-advisor-portal.md) | Vous pouvez utiliser Azure SQL Database Advisor dans le portail Azure afin d’examiner et d’implémenter des recommandations pour vos bases de données SQL existantes et améliorer ainsi les performances actuelles des requêtes. |
| 34 | [Vue d’ensemble du test d’évaluation de la base de données SQL Azure](sql-database-benchmark-overview.md) | Cette rubrique décrit le test d’évaluation de Base de données SQL Azure utilisé pour mesurer les performances de Base de données SQL Azure. |
| 35 | [Quand utiliser un pool de base de données élastique ?](sql-database-elastic-pool-guidance.md) | Un pool de bases de données élastique est un ensemble de ressources disponibles partagé par un groupe de bases de données élastiques. Ce document fournit des conseils pour vous aider à évaluer la pertinence de l'utilisation d'un pool de bases de données élastique pour un groupe de bases de données. |
| 36 | [Prise en main des outils de base de données élastiques](sql-database-elastic-scale-get-started.md) | Explication de base de la fonctionnalité des outils de bases de données élastiques d’Azure SQL Database, y compris un exemple d’application simple à exécuter. |
| 37 | [Forum Aux Questions de base de données SQL](sql-database-faq.md) | Réponses fréquemment posées sur les bases de données du cloud et Azure SQL Database, le système de gestion de base de données relationnelle Microsoft (SGBDR) et la base de données en tant que service dans cloud. |
| 38 | [Prise en main de In-Memory (version préliminaire) dans la base de données SQL](sql-database-in-memory.md) | Les technologies SQL In-Memory améliorent considérablement les performances des charges de travail transactionnelles et analytiques. Apprenez à tirer parti de ces technologies. |
| 39 | [Utiliser OLTP en mémoire (version préliminaire) pour améliorer les performances de votre base de données SQL](sql-database-in-memory-oltp-migration.md) | Adopter In-Memory OLTP pour améliorer les performances transactionnelles dans une base de données SQL existante. |
| 40 | [Surveiller le stockage OLTP In-Memory](sql-database-in-memory-oltp-monitoring.md) | Estimer et surveiller la capacité et l’utilisation du stockage en mémoire XTP ; résoudre l’erreur de capacité 41823 |
| 41 | [Utilisation du magasin de requêtes dans la base de données SQL Azure](sql-database-operate-query-store.md) | Découvrez comment utiliser le magasin de requêtes dans la base de données SQL Azure |
| 42 | [Informations sur les performances des bases de données SQL](sql-database-performance.md) | La base de données SQL Azure fournit des outils de performances pour vous aider à identifier les zones susceptibles d’améliorer les performances actuelles des requêtes. |
| 43 | [Guide des performances de base de données SQL Azure pour les bases de données uniques](sql-database-performance-guidance.md) | Cette rubrique fournit des conseils pour vous aider à déterminer le niveau de service adapté à votre application et fournit des recommandations pour le paramétrage de votre application afin de tirer le meilleur parti de votre base de données SQL Azure. |
| 44 | [Query Performance Insight pour base de données SQL Azure](sql-database-query-performance.md) | La surveillance des performances des requêtes identifie les requêtes consommant le plus d’UC pour une base de données SQL Azure. |
| 45 | [Modifier les niveaux de service et de performances (niveau tarifaire) d’une base de données SQL](sql-database-scale-up.md) | Cet article explique comment faire monter ou descendre en puissance une base de données SQL. Modification du niveau de tarification d’une base de données SQL Azure |
| 46 | [Analyse des performances d’une base de données dans une base de données SQL Azure](sql-database-single-database-monitor.md) | Découvrez les options d’analyse de votre base de données à l’aide des outils Azure et des vues de gestion dynamique. |
| 47 | [Conseils pour le réglage des performances de SQL Database](sql-database-troubleshoot-performance.md) | Conseils pour le réglage des performances dans Azure SQL Database par le biais de l’évaluation et de l’amélioration. |
| 48 | [Comment utiliser le traitement par lots pour améliorer les performances des applications de base de données SQL](sql-database-use-batching-to-improve-performance.md) | Cette rubrique explique comment le traitement par lots des opérations de base de données contribue à améliorer considérablement la rapidité et l’évolutivité de vos applications de Base de données SQL Azure. Bien que ces techniques de traitement par lot fonctionnent pour les bases de données SQL Server, cet article porte exclusivement sur Azure. |



## Outils pour la montée en charge

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 49 | [Modèles de conception pour les applications SaaS mutualisées et Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) | Cet article décrit la configuration requise et les modèles d’architecture de données les plus courants pour les applications de base de données SaaS mutualisées s’exécutant dans un environnement cloud, ainsi que les compromis associés à ces modèles. Il explique également comment Base de données SQL Azure, avec ses outils et ses pools de base de données élastiques, permet de satisfaire ces exigences sans aucun compromis. |
| 50 | [Migration de bases de données existantes pour une mise à l’échelle](sql-database-elastic-convert-to-use-elastic-tools.md) | Conversion de bases de données partitionnées pour utiliser les outils de base de données élastique en créant un gestionnaire de cartes de partitions |
| 51 | [Conception de bases de données cloud évolutives](sql-database-elastic-database-client-library.md) | Créez des applications de base de données .NET évolutives avec la bibliothèque cliente de bases de données élastiques |
| 52 | [Compteurs de performances pour le Gestionnaire de cartes de partitions](sql-database-elastic-database-perf-counters.md) | Classe ShardMapManager et compteurs de performances pour le routage dépendant des données |
| 53 | [Ajout d’une partition à l’aide des outils de base de données élastique](sql-database-elastic-scale-add-a-shard.md) | Utilisation des API avec infrastructure élastique pour ajouter de nouvelles partitions à un ensemble de partitions. |
| 54 | [Déployer un service de fractionnement et de fusion](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Fractionnement et fusion avec les outils de bases de données élastiques |
| 55 | [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) | Utilisation de la classe ShardMapManager dans des applications .NET pour le routage dépendant des données, une fonctionnalité de base de données élastique pour la base de données SQL Azure |
| 56 | [FAQ sur les outils de bases de données élastiques](sql-database-elastic-scale-faq.md) | Frequently Asked Questions about Azure SQL Database Elastic Scale. |
| 57 | [Glossaire des outils de base de données élastique](sql-database-elastic-scale-glossary.md) | Explication des termes utilisés pour les outils de base de données élastique |
| 58 | [Montée en charge avec Base de données SQL Azure](sql-database-elastic-scale-introduction.md) | Les développeurs de Software as a Service (SaaS) peuvent facilement créer des bases de données élastiques et évolutives dans le cloud à l'aide de ces outils |
| 59 | [Informations d’identification utilisées pour accéder à la bibliothèque cliente de la base de données élastique](sql-database-elastic-scale-manage-credentials.md) | Définition du niveau d’informations d’identification, de celui d’administrateur à celui de la lecture seule, pour les applications de base de données élastique. |
| 60 | [Requête sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md) | Exécutez des requêtes en utilisant la bibliothèque cliente des bases de données élastiques. |
| 61 | [Déplacement de données entre des bases de données cloud mises à l’échelle](sql-database-elastic-scale-overview-split-and-merge.md) | Explique comment manipuler les partitions et déplacer les données via un service auto-hébergé, à l'aide des API de base de données élastique. |
| 62 | [Monter en charge les bases de données avec le Gestionnaire de cartes de partitions](sql-database-elastic-scale-shard-map-management.md) | Utilisation de ShardMapManager, la bibliothèque cliente de base de données élastique |
| 63 | [Configuration de la sécurité du fractionnement et de la fusion](sql-database-elastic-scale-split-merge-security-configuration.md) | Définissez les certificats x 409 pour le chiffrement |
| 64 | [Mettre à niveau une application pour utiliser la dernière version de la bibliothèque cliente de bases de données élastiques](sql-database-elastic-scale-upgrade-client-library.md) | Mettre à niveau des applications et des bibliothèques à l’aide de Nuget |
| 65 | [Bibliothèque cliente de la base de données élastique avec Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Utilisez la bibliothèque cliente de la base de données élastique et Entity Framework pour le codage de bases de données |
| 66 | [Utilisation de la bibliothèque cliente de la base de données élastique avec Dapper](sql-database-elastic-scale-working-with-dapper.md) | Utilisation de la bibliothèque cliente de la base de données élastique avec Dapper. |
| 67 | [Applications multi-locataires avec des outils de base de données élastique et la sécurité au niveau des lignes](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Découvrez comment utiliser les outils de base de données élastique avec la fonction de sécurité au niveau des lignes (RLS) pour générer une application présentant une couche Données hautement évolutive sur la base de données SQL Microsoft Azure qui prend en charge les partitions multi-locataires. |



## Tâches élastiques

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 68 | [Créer et gérer des bases de données SQL Azure avec montée en charge à l’aide de tâches élastiques (version préliminaire)](sql-database-elastic-jobs-create-and-manage.md) | Passez en revue la création et la gestion d'une tâche de base de données élastique. |
| 69 | [Prise en main de Tâches de bases de données élastiques](sql-database-elastic-jobs-getting-started.md) | Utilisation des tâches de bases de données élastiques |
| 70 | [Gestion des bases de données cloud avec montée en charge](sql-database-elastic-jobs-overview.md) | Illustre le service de tâche de base de données élastique |
| 71 | [Création et gestion des tâches de bases de données SQL élastiques à l'aide de PowerShell (version préliminaire)](sql-database-elastic-jobs-powershell.md) | Gérer la base de données SQL Azure avec PowerShell |
| 72 | [Vue d’ensemble de l’installation de Tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md) | Passez en revue l'installation de la fonctionnalité de tâche élastique. |
| 73 | [Désinstallation des composants de Tâches de bases de données élastiques.](sql-database-elastic-jobs-uninstall.md) | Désinstaller l’outil de tâche de base de données élastique |



## Pools élastiques

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 74 | [Qu’est-ce qu’un pool de base de données élastique ?](sql-database-elastic-pool.md) | Gérez des centaines voire des milliers de bases de données à l’aide d’un pool. Un seul prix pour un ensemble d’unités de performances peut être distribué dans le pool. Déplacez les bases de données à votre convenance. |
| 75 | [Créer un pool de base de données élastique avec C#](sql-database-elastic-pool-create-csharp.md) | Utilisez les techniques de développement de bases de données C# pour créer un pool de base de données élastique évolutif dans la base de données SQL Azure afin de pouvoir partager des ressources entre plusieurs bases de données. |
| 76 | [Créer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md) | Découvrez comment utiliser PowerShell pour augmenter la taille des instances de ressources de Base de données SQL Azure en créant un pool de base de données élastique évolutif afin de gérer plusieurs bases de données. |
| 77 | [Développement de base de données en C# : créer et configurer un pool de base de données élastique pour une base de données SQL](sql-database-elastic-pool-csharp.md) | Utilisez les techniques de développement de bases de données C# pour créer un pool de base de données élastique SQL Azure afin de pouvoir partager des ressources entre plusieurs bases de données. |
| 78 | [Script PowerShell pour identifier les bases de données adaptées à un pool de bases de données élastique](sql-database-elastic-pool-database-assessment-powershell.md) | Un pool de bases de données élastique est un ensemble de ressources disponibles partagé par un groupe de bases de données élastiques. Ce document fournit un script PowerShell pour vous aider à évaluer la pertinence de l’utilisation d’un pool de bases de données élastique pour un groupe de bases de données. |
| 79 | [Surveiller et gérer un pool de bases de données élastique avec C#](sql-database-elastic-pool-manage-csharp.md) | Utilisez les techniques de développement de bases de données C# pour gérer un pool de bases de données élastique Azure SQL Database. |
| 80 | [Surveiller et gérer un pool de bases de données élastique avec le portail Azure](sql-database-elastic-pool-manage-portal.md) | Découvrez comment utiliser le portail Azure et l’intelligence intégrée de la base de données SQL pour gérer, surveiller et redimensionner un pool de bases de données élastique évolutif pour optimiser les performances de la base de données et gérer les coûts. |
| 81 | [Surveiller et gérer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-manage-powershell.md) | Découvrez comment utiliser PowerShell pour gérer un pool de bases de données élastique. |
| 82 | [Surveiller et gérer un pool de base de données élastique avec Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | Utilisez T-SQL pour créer une base de données SQL Azure dans un pool élastique. Ou utilisez T-SQL pour déplacer la base de données dans et en dehors des pools. |
| 83 | [Informations sur la tarification et la facturation du pool de base de données élastique](sql-database-elastic-pool-price.md) | Informations de tarification propres aux pools de bases de données élastiques. |



## Requête élastique

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 84 | [Créer des rapports sur des bases de données cloud avec montée en charge (version préliminaire)](sql-database-elastic-query-getting-started.md) | comment utiliser des requêtes entre plusieurs bases de données |
| 85 | [Prise en main des requêtes de bases de données croisées (partitionnement vertical) (version préliminaire)](sql-database-elastic-query-getting-started-vertical.md) | comment utiliser une requête de base de données élastique avec des bases de données partitionnées verticalement |
| 86 | [Création de rapports sur des bases de données cloud mises à l’échelle (version préliminaire)](sql-database-elastic-query-horizontal-partitioning.md) | comment configurer des requêtes élastiques sur les partitions horizontales |
| 87 | [Vue d’ensemble de la requête de base de données élastique Azure SQL Database (version préliminaire)](sql-database-elastic-query-overview.md) | Vue d’ensemble de la fonctionnalité de requête élastique |
| 88 | [Interroger des bases de données cloud de schémas différents (version préliminaire)](sql-database-elastic-query-vertical-partitioning.md) | configuration de requêtes de bases de données croisées sur les partitions verticales |



## Transaction élastique

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 89 | [Transactions distribuées entre bases de données cloud](sql-database-elastic-transactions-overview.md) | Vue d’ensemble des transactions de bases de données élastiques avec la base de données SQL Azure |



## Sauvegarde et récupération

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 90 | [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md) | Découvrez-en plus sur les sauvegardes intégrées SQL Database qui vous permettent de restaurer Azure SQL Database à une version antérieure ou de copier une base de données vers une nouvelle base de données dans une zone géographique (jusqu’à 35 jours). |
| 91 | [Vue d’ensemble de la continuité de l’activité avec la base de données Azure SQL](sql-database-business-continuity.md) | Découvrez comment Azure SQL Database prend en charge la continuité des activités cloud et la récupération de base de données et vous aide à maintenir les applications cloud opérationnelles. |
| 92 | [Comment restaurer une table unique à partir d’une sauvegarde Azure SQL Database](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Découvrez comment restaurer une table unique à partir d’une sauvegarde Base de données SQL Azure. |
| 93 | [Concevoir une application pour la récupération d’urgence cloud à l’aide de la géo-réplication active dans une base de données SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Apprenez à concevoir des solutions de récupération d’urgence cloud pour la planification de la continuité des activités à l’aide de la géo-réplication pour la sauvegarde de données d’application avec Azure SQL Database. |
| 94 | [Restaurer une base de données SQL Azure ou basculer vers une base de données secondaire](sql-database-disaster-recovery.md) | Découvrez comment récupérer une base de données en cas de panne d’un centre de données régional grâce aux fonctionnalités de géo-réplication active et de restauration géographique du service Base de données SQL Azure. |
| 95 | [Exécution d'un exercice de récupération d'urgence](sql-database-disaster-recovery-drills.md) | Découvrez des conseils et des meilleures pratiques en matière d'utilisation de la base de données Azure SQL pour effectuer des exercices de récupération d'urgence qui vous aideront à maintenir la résistance aux pannes de vos applications métier stratégiques en cas de défaillances. |
| 96 | [Stratégies de récupération d’urgence pour les applications utilisant le pool élastique de base de données SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Apprenez à concevoir votre solution cloud pour la récupération d’urgence en choisissant le modèle de basculement approprié. |
| 97 | [Utilisation de la classe RecoveryManager pour résoudre les problèmes de correspondance de partitionnement](sql-database-elastic-database-recovery-manager.md) | Utiliser la classe RecoveryManager pour résoudre les problèmes des mappages de partition |
| 98 | [Importer un fichier BACPAC pour créer une nouvelle base de données SQL Azure](sql-database-import.md) | Créer une base de données SQL Azure en important un fichier BACPAC existant |
| 99 | [Restaurer une base de données SQL Azure à un point antérieur dans le temps avec le portail Azure](sql-database-point-in-time-restore-portal.md) | Restaurez une base de données SQL Azure à un point antérieur dans le temps. |
| 100 | [Restaurer une base de données SQL Azure à un point dans le temps avec PowerShell](sql-database-point-in-time-restore-powershell.md) | Restaurer une base de données SQL Azure à un point antérieur dans le temps |
| 101 | [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md) | Limite de restauration dans le temps, base de données SQL Microsoft Azure, restaurer une base de données, récupérer une base de données, portail Azure Classic, portail Azure Classic |
| 102 | [Récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées d’une base de données](sql-database-recovery-using-backups.md) | Apprenez-en plus sur la limite de restauration dans le temps, qui vous permet de restaurer une base de données SQL Azure à un point antérieur dans le temps (jusqu’à 35 jours). |
| 103 | [Restaurer une base de données SQL Azure supprimée à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md) | Restaurez une base de données SQL Azure supprimée (portail Azure). |
| 104 | [Restaurer une base de données SQL Azure supprimée avec PowerShell](sql-database-restore-deleted-database-powershell.md) | Restaurez une base de données SQL Azure supprimée (PowerShell). |
| 105 | [Restaurer une base de données à un état antérieur, restaurer une base de données supprimée ou à la suite d’une panne du centre de données](sql-database-troubleshoot-backup-and-restore.md) | Découvrez comment restaurer une base de données suite à des erreurs ou des bannes en utilisant des sauvegardes et des réplicas dans Azure SQL DatabaseBase de données SQL. |



## Migrer

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 106 | [Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Base de données SQL Microsoft Azure, migration de base de données, exporter une base de données, exporter un fichier BACPAC, sqlPackage |
| 107 | [Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Base de données SQL Microsoft Azure, migration de base de données, exportation de base de données, exportation de fichier BACPAC, Assistant d’exportation d’application de couche Données |
| 108 | [Importation vers Base de données SQL à partir d’un fichier BACPAC à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, importer un fichier BACPAC, sqlpackage |
| 109 | [Importation depuis BACPAC vers Base de données SQL avec SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Microsoft Azure SQL Database, déployer base de données, migration base de données, importer base de données, exporter base de données, assistant de migration |
| 110 | [Migration d’une base de données SQL Server vers Base de données SQL à l’aide de l’assistant de déploiement de base de données vers Microsoft Azure Database](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Base de données SQL Microsoft Azure, migration de base de données, Assistant de base de données Microsoft Azure |
| 111 | [Migrer une base de données SQL Server vers une Base de données SQL Azure à l’aide de la réplication transactionnelle](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, réplication transactionnelle |
| 112 | [Déterminer la compatibilité de Base de données SQL à l’aide de SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, SqlPackage |
| 113 | [Utilisez SQL Server Management Studio afin de déterminer la compatibilité de la base de données SQL avant la migration vers une base de données SQL Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, Assistant d’exportation d’application de couche Données |
| 114 | [Utilisation de l’Assistant Migration SQL Azure pour résoudre les problèmes de compatibilité de base de données SQL Server avant la migration vers une base de données SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) | Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure |
| 115 | [Utilisation de SQL Server Data Tools pour Visual Studio afin de migrer une base de données SQL Server vers une base de données SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure, SSDT |
| 116 | [Résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration vers Base de données SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Base de données SQL Microsoft Azure, migration de base de données, compatibilité, assistant de migration SQL Azure |
| 117 | [Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell](sql-database-export-powershell.md) | Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell |
| 118 | [Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell](sql-database-import-powershell.md) | Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell |
| 119 | [Charger des données à partir d’un fichier CSV dans Azure SQL Data Warehouse (fichiers plats)](sql-database-load-from-csv-with-bcp.md) | Pour les données de taille réduite, utilise l’utilitaire de ligne de commande BCP pour importer les données dans la base de données SQL Azure. |
| 120 | [Déplacer des bases de données entre des serveurs, entre des abonnements, au sein et hors d’Azure](sql-database-troubleshoot-moving-data.md) | Étapes à suivre pour copier, déplacer et migrer des données et des bases de données dans Azure SQL Database. |



## Déplacer les données

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 121 | [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md) | Découvrez comment migrer une base de données SQL Server locale vers Azure SQL Database dans le cloud. Utilisez les outils de migration de base de données pour tester la compatibilité avant de procéder à la migration de la base de données. |
| 122 | [Copie d'une base de données SQL Azure](sql-database-copy.md) | Création d'une copie d'une base de données SQL Azure |
| 123 | [Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure](sql-database-export.md) | Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure |



## Sécurité

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 124 | [Connexion au service Base de données SQL ou SQL Data Warehouse avec l’authentification Azure Active Directory](sql-database-aad-authentication.md) | Apprenez comment vous connecter à la base de données SQL en utilisant l’authentification Azure Active Directory |
| 125 | [Chiffrement intégral : protéger les données sensibles dans Base de données SQL et stocker vos clés de chiffrement dans le magasin de certificats Windows](sql-database-always-encrypted.md) | Protéger les données sensibles de votre base de données SQL en quelques minutes. |
| 126 | [Chiffrement intégral : Protéger les données sensibles dans Base de données SQL et stocker vos clés de chiffrement dans Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) | Protéger les données sensibles de votre base de données SQL en quelques minutes. |
| 127 | [Base de données SQL - Prise en charge des clients de niveau inférieur et modification des points de terminaison IP à des fins d’audit](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | En savoir plus sur la prise en charge des clients de niveau inférieur de la base de données SQL et sur la modification des points de terminaison IP à des fins d’audit. |
| 128 | [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure. |
| 129 | [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure. |
| 130 | [Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données sur une base de données SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure. |
| 131 | [Prise en main du masquage de données dynamiques de base de données SQL (portail Azure)](sql-database-dynamic-data-masking-get-started.md) | Prise en main du masquage des données dynamiques de base de données SQL dans le portail Azure |
| 132 | [Prise en main du masquage des données dynamiques de base de données SQL (portail Azure Classic)](sql-database-dynamic-data-masking-get-started-portal.md) | Comment prendre en main le masquage de données dynamiques de base de données SQL dans le portail Azure Classic |
| 133 | [Configuration des règles de pare-feu de la base de données SQL Azure - Vue d’ensemble](sql-database-firewall-configure.md) | Apprenez à configurer un pare-feu de base de données SQL avec des règles de pare-feu au niveau du serveur et au niveau de la base de données pour gérer les accès. |
| 134 | [Didacticiel sur la base de données SQL : créer des comptes d’utilisateurs de base de données SQL pour accéder et gérer une base de données](sql-database-get-started-security.md) | Apprenez à créer des comptes d'utilisateurs pour accéder et gérer une base de données. |
| 135 | [Authentification et autorisation de base de données SQL : octroi de l’accès](sql-database-manage-logins.md) | Découvrez la gestion de la sécurité SQL Database, en particulier la façon de gérer la sécurité d’accès et de connexion aux bases de données par le biais du compte du principal au niveau du serveur. |
| 136 | [Consignes et limitations de sécurité de base de données Azure SQL](sql-database-security-guidelines.md) | Découvrez les instructions et limitations de Microsoft Azure SQL Database relatives à la sécurité. |
| 137 | [Prise en main de Threat Detection pour la base de données SQL](sql-database-threat-detection-get-started.md) | Prise en main l’outil Threat Detection de la base de données SQL dans le portail Azure |
| 138 | [Comment effectuer des tâches d’administration courantes telles que la réinitialisation de mot de passe d’administrateur dans Azure SQL Database](sql-database-troubleshoot-permissions.md) | Explique comment effectuer des tâches d’administration courantes dans SQL Database. Par exemple, la réinitialisation de mot de passe administrateur, l’octroi et la suppression de l’accès. |



## Gérer votre base de données

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 139 | [Prise en main de l’audit de base de données SQL](sql-database-auditing-get-started.md) | Prise en main de l’audit de base de données SQL |
| 140 | [Configurer une règle de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent au serveur SQL Azure. |
| 141 | [Copie d’une base de données SQL Azure à l’aide du portail Azure](sql-database-copy-portal.md) | Création d'une copie d'une base de données SQL Azure |
| 142 | [Gestion des bases de données SQL Azure à l'aide d'Azure Automation](sql-database-manage-automation.md) | Découvrez comment le service Azure Automation peut être utilisé pour gérer les bases de données SQL Azure à grande échelle. |
| 143 | [Vue d’ensemble des outils de gestion de la base de données SQL](sql-database-manage-overview.md) | Compare les outils et les options de gestion de bases de données SQL Microsoft Azure. |
| 144 | [Analyse d’une base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md) | Apprenez à détecter et à diagnostiquer des problèmes de performances courants à l’aide de vues de gestion dynamique pour surveiller une base de données SQL Microsoft Azure. |
| 145 | [Sécurisation de votre base de données SQL](sql-database-security.md) | Découvrez plus d’informations sur la base de données SQL Microsoft Azure et la sécurité de la base de données SQL, y compris les différences entre le cloud et l’installation SQL Server sur site en termes d’authentification, d’autorisations, de sécurité des connexions, de chiffrement et de conformité. |



## Événements étendus

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 146 | [Code cible du fichier d’événements pour les événements étendus dans la base de données SQL](sql-database-xevent-code-event-file.md) | Fournit PowerShell et Transact-SQL pour un exemple de code en deux phases qui montre l’utilisation de la cible du fichier d’événements dans un événement étendu sur Azure SQL Database. Le service Azure Storage est nécessaire pour ce scénario. |
| 147 | [Code cible de la mémoire tampon en anneau pour les événements étendus dans la base de données SQL](sql-database-xevent-code-ring-buffer.md) | Fournit un exemple de code Transact-SQL simple et rapide en utilisant la cible de la mémoire tampon en anneau, dans Azure SQL Database. |
| 148 | [Événement étendus dans la base de données SQL](sql-database-xevent-db-diff-from-svr.md) | Décrit les événements étendus (XEvents) dans la base de données SQL Azure et les différences entre les sessions d’événements dans la base de données SQL Azure et dans Microsoft SQL Server. |



## Géo-réplication

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 149 | [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec le portail Azure](sql-database-geo-replication-failover-portal.md) | Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec le portail Azure |
| 150 | [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec PowerShell](sql-database-geo-replication-failover-powershell.md) | Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec PowerShell |
| 151 | [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL |
| 152 | [Vue d’ensemble : Géo-réplication active de base de données SQL](sql-database-geo-replication-overview.md) | La géo-réplication active permet de configurer 4 réplicas de votre base de données dans un des centres de données Azure. |
| 153 | [Configurer la géoréplication pour Base de données SQL Azure avec le portail Azure](sql-database-geo-replication-portal.md) | Configurer la géoréplication pour Base de données SQL Azure avec le portail Azure |
| 154 | [Configurer la géoréplication pour Base de données SQL Azure avec PowerShell](sql-database-geo-replication-powershell.md) | Configurer la géoréplication active pour Base de données SQL Azure avec PowerShell |
| 155 | [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) | Cette rubrique décrit des considérations relatives à la gestion de la sécurité après un basculement ou une restauration de base de données. |
| 156 | [Configurer la géoréplication pour Base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configurer la géoréplication pour Base de données SQL Azure avec Transact-SQL |
| 157 | [Géo-restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide du portail Azure](sql-database-geo-restore-portal.md) | Géo-restaurez une base de données SQL Azure à partir d’une sauvegarde géo-redondante (portail Azure). |
| 158 | [Restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide de PowerShell](sql-database-geo-restore-powershell.md) | Restaurer une base de données SQL Azure sur un nouveau serveur à partir d’une sauvegarde géo-redondante |



## Mise à niveau

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 159 | [Amélioration des performances des requêtes avec le niveau de compatibilité 130 dans Base de données SQL Azure](sql-database-compatibility-level-query-performance-130.md) | Procédure et outils pour déterminer le niveau de compatibilité adapté à votre base de données sur Base de données SQL Azure ou Microsoft SQL Server |
| 160 | [Gestion des mises à niveau propagées des applications cloud à l’aide de la géo-réplication active de la base de données SQL](sql-database-manage-application-rolling-upgrade.md) | Découvrez comment utiliser la géo-réplication de la base de données SQL pour prendre en charge les mises à niveau en ligne de votre application cloud. |
| 161 | [Mise à niveau vers Azure SQL Database V12 à l’aide du portail Azure](sql-database-upgrade-server-portal.md) | Explique comment effectuer une mise à niveau vers Azure SQL Database V12, notamment la mise à niveau des bases de données Web et Business et la mise à niveau d’un serveur V11 migrant ses bases de données directement dans un pool de base de données élastique à l’aide du portail Azure. |
| 162 | [Mise à niveau vers Azure SQL Database V12 à l’aide de PowerShell](sql-database-upgrade-server-powershell.md) | Explique comment effectuer une mise à niveau vers Azure SQL Database V12, notamment la mise à niveau des bases de données Web et Business et la mise à niveau d’un serveur V11 migrant ses bases de données directement dans un pool de base de données élastique à l'aide de PowerShell. |
| 163 | [Planifier et préparer la mise à niveau vers la version V12 de la base de données SQL](sql-database-v12-plan-prepare-upgrade.md) | Décrit la préparation et les limitations relatives à la mise à niveau vers la version V12 d’Azure SQL Database. |
| 164 | [Nouveautés de SQL Database V12](sql-database-v12-whats-new.md) | Explique pourquoi les systèmes d’entreprise qui utilisent Azure SQL Database dans le cloud profitent de la mise à niveau vers la version 12 (V12). |
| 165 | [Forum aux questions sur la disparition des éditions Web et Business](sql-database-web-business-sunset-faq.md) | Découvrez les fonctionnalités des nouveaux niveaux de service qui remplacent les bases de données Azure SQL Web et Business. |



## Outils

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 166 | [Gérer la base de données SQL Azure avec PowerShell](sql-database-command-line-tools.md) | Gestion d’Azure SQL Database avec PowerShell. |
| 167 | [Didacticiel sur la base de données SQL : Connectez Excel à une base de données SQL Azure et créez un rapport](sql-database-connect-excel.md) | Découvrez comment connecter Microsoft Excel à la base de données SQL Azure dans le cloud. Importez des données dans Excel pour les rapports et l’exploration des données. |
| 168 | [Créer une base de données SQL et effectuer des tâches courantes d’installation de base de données avec les applets de commande PowerShell](sql-database-get-started-powershell.md) | Apprenez dès maintenant à créer une base de données SQL avec PowerShell. Les tâches courantes d’installation de base de données peuvent être gérées via les applets de commande PowerShell. |
| 169 | [Prise en main d’Azure SQL Data Sync (version préliminaire)](sql-database-get-started-sql-data-sync.md) | Ce didacticiel présente la synchronisation des données SQL Azure (aperçu). |
| 170 | [Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Découvrez comment utiliser SQL Server Management Studio pour gérer des serveurs et les bases de données de la base de données SQL. |
| 171 | [Gestion des bases de données SQL Azure au moyen du portail Azure](sql-database-manage-portal.md) | Découvrez comment utiliser le portail Azure pour gérer une base de données relationnelle dans le cloud. |
| 172 | [Modification des niveaux de service et de performances (niveau de tarification) d’une base de données SQL avec PowerShell](sql-database-scale-up-powershell.md) | Cet article explique comment faire monter ou descendre en puissance une base de données SQL avec PowerShell. Modification du niveau de tarification d’une base de données SQL Azure avec PowerShell. |
| 173 | [Connexion à une base de données SQL à l’aide de SQL Server Management Studio dans Azure RemoteApp](sql-database-ssms-remoteapp.md) | Utilisez ce didacticiel pour apprendre à utiliser SQL Server Management Studio dans Azure RemoteApp pour la sécurité et les performances lors de la connexion à la base de données SQL |



## Référence

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 174 | [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md) | Répertorie le numéro de version minimal pour chaque pilote que les programmes clients peuvent utiliser lors de la connexion à Azure SQL Database ou à Microsoft SQL Server. Un lien est fourni pour les informations sur les versions des pilotes publiés par la communauté, et non par Microsoft. |
| 175 | [Limites de ressources de base de données SQL Azure](sql-database-resource-limits.md) | Cette page décrit certaines limites de ressources courantes pour une base de données SQL Azure. |
| 176 | [Différences dans le langage Transact-SQL Azure SQL Database.](sql-database-transact-sql-information.md) | Instructions Transact-SQL qui ne sont pas entièrement prises en charge dans Azure SQL Database |



## Divers

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 177 | [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) | Création d'une copie d'une base de données SQL Azure à l'aide de PowerShell |
| 178 | [Copier une base de données SQL Azure à l’aide de Transact-SQL](sql-database-copy-transact-sql.md) | Création d'une copie d'une base de données SQL Azure à l'aide de Transact-SQL |
| 179 | [Consignes et limitations générales de base de données SQL Azure](sql-database-general-limitations.md) | Cette page décrit certaines limitations générales de la base de données SQL Azure, ainsi que les zones d’interopérabilité et de prise en charge. |
| 180 | [Recommandations relatives aux niveaux tarifaires des bases de données SQL](sql-database-service-tier-advisor.md) | Lorsque vous modifiez les niveaux tarifaires dans le portail Azure, vous pouvez consulter les recommandations fournies, notamment le niveau le mieux adapté à l’exécution de la charge de travail d’une base de données SQL Azure existante. Les niveaux tarifaires décrivent les niveaux de service et de performances d’une base de données SQL. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## Extras

<a name="extra_related_articles"></a>

### Articles connexes d’autres services Azure

- [Sauvegarde de votre application Azure App Service dans Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### Outils de la documentation

- [Mises à jour annoncées pour la base de données SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

- [Recherche de tous les types de documentation Microsoft Azure à l’aide de filtres](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### Graphique du parcours d’apprentissage

- [Graphique du parcours d’apprentissage de tous les services Microsoft Azure](http://azure.microsoft.com/documentation/learning-paths/)

- [Parcours d’apprentissage : utilisation de la base de données SQL Azure](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Parcours d’apprentissage : infrastructure élastique de la base de données SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->

<!---HONumber=AcomDC_0824_2016-->