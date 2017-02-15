---
title: "Migration de base de données SQL Server vers Azure SQL Database | Microsoft Docs"
description: "Découvrez comment migrer une base de données SQL Server locale vers Azure SQL Database dans le cloud. Utilisez les outils de migration de base de données pour tester la compatibilité avant de procéder à la migration de la base de données."
keywords: "migration de base de données, migration de base de données sql server, outils de migration de base de données, migrer la base de données, migrer la base de données sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: 61fb027dfdd5830d87fe4fcfff57f685db71475e


---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migration de base de données SQL Server vers SQL Database dans le cloud
Cet article vous montre comment migrer une base de données SQL Server 2005 ou version ultérieure locale vers Azure SQL Database. Dans ce processus de migration de base de données, vous migrez votre schéma et vos données à partir de la base de données SQL Server dans votre environnement actuel vers SQL Database. À condition que la base de données existante passe les tests de compatibilité. Avec SQL Database V12, nous approchons de la [parité des fonctionnalités](sql-database-features.md), à l’exception des problèmes liés aux opérations au niveau du serveur et sur plusieurs bases de données. Les bases de données et les applications qui reposent sur des [fonctions partiellement ou pas du tout prises en charge](sql-database-transact-sql-information.md) ont besoin d’une nouvelle ingénierie pour corriger ces incompatibilités avant de pouvoir migrer la base de données SQL Server.

Procédez comme suit pour effectuer la migration :

* **Test de compatibilité** : validez la compatibilité de la base de données avec SQL Database. 
* **Résoudre les problèmes de compatibilité, le cas échéant**: si la validation échoue, vous devez corriger les erreurs de validation.  
* **Effectuer la migration** : une fois que votre base de données est compatible, vous pouvez utiliser une ou plusieurs méthodes pour effectuer la migration. 

SQL Server fournit plusieurs méthodes pour accomplir chacune de ces tâches. Cet article fournit une vue d’ensemble des méthodes disponibles pour chaque tâche. Le diagramme suivant illustre les étapes et les méthodes.

  ![Schéma de migration VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> Pour migrer une base de données non SQL Server, notamment Microsoft Access, Sybase, MySQL Oracle et DB2, vers une base de données SQL Azure, consultez l’ [Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/).
> 
> 

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Les outils de migration de base de données testent la compatibilité de la base de données SQL Server avec SQL Database
Pour tester les problèmes de compatibilité de la base de données SQL avant de commencer le processus de migration de la base de données, utilisez l’une des méthodes suivantes :

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* [SQL Server Data Tools pour Visual Studio (« SSDT »)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT utilise les règles de compatibilité les plus récentes pour détecter les incompatibilités de la base de données SQL V12. Si des incompatibilités sont détectées, vous pouvez corriger les problèmes détectés directement dans cet outil. Il s’agit de la méthode recommandée pour tester et corriger les problèmes de compatibilité de la base de données SQL V12. 
* [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage est un utilitaire de ligne de commande qui teste d’éventuels problèmes de compatibilité et, le cas échéant, génère un rapport contenant les problèmes détectés. Si vous utilisez cet outil, assurez-vous qu’il s’agit de la version la plus récente qui inclut les dernières règles de compatibilité. Si des erreurs sont détectées, vous devez utiliser un autre outil pour corriger les problèmes de compatibilité détectés - SSDT est recommandé.  
* [Assistant d’application Exportation de la couche Données dans SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): cet assistant détecte les erreurs et les signale à l’écran. Si aucune erreur n’est détectée, vous pouvez continuer et terminer la migration vers la base de données SQL. Si des erreurs sont détectées, vous devez utiliser un autre outil pour corriger les problèmes de compatibilité détectés - SSDT est recommandé.
* [Assistant Migration SQL Azure (« SAMW »)](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW est un outil codeplex qui utilise les règles de compatibilité de la base de données SQL Azure V11 pour détecter les incompatibilités de la base de données SQL Azure V12. Si des incompatibilités sont détectées, certains problèmes peuvent être corrigés directement dans cet outil. Cet outil peut détecter des incompatibilités ne devant pas être résolues. Il s’agit du premier outil d’assistance à la migration de la base de données SQL Azure disponible, et il est activement pris en charge par la communauté SQL Server. Par ailleurs, cet outil peut terminer la migration lui-même. 

## <a name="fix-database-migration-compatibility-issues"></a>Résoudre les problèmes de compatibilité de migration de la base de données
Si des problèmes de compatibilité sont détectés, vous devez les corriger avant de procéder à la migration de la base de données SQL Server. Vous pouvez rencontrer une grande variété de problèmes de compatibilité, selon la version de SQL Server dans la base de données source et la complexité de la base de données que vous êtes en train de migrer. Les versions antérieures de SQL Server ont plus de problèmes de compatibilité. Utilisez les ressources suivantes en plus d’une recherche Internet ciblée dans le moteur de recherche de votre choix :

* [Fonctionnalités de base de données SQL Server non prises en charge dans Azure SQL Database](sql-database-transact-sql-information.md)
* [Discontinued Database Engine Functionality in SQL Server 2016 (Fonctionnalités du moteur de base de données supprimées dans SQL Server 2016)](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Discontinued Database Engine Functionality in SQL Server 2014 (Fonctionnalités du moteur de base de données non disponibles dans SQL Server 2014)](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Discontinued Database Engine Functionality in SQL Server 2012 (Fonctionnalités du moteur de base de données non disponibles dans SQL Server 2012)](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Discontinued Database Engine Functionality in SQL Server 2008 R2 (Fonctionnalités du moteur de base de données non disponibles dans SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Discontinued Database Engine Functionality in SQL Server 2005 (Fonctionnalités du moteur de base de données supprimées dans SQL Server 2005)](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Outre les recherches sur Internet et ces ressources, utilisez les [forums de communauté MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou [StackOverflow](http://stackoverflow.com/).

Utilisez l’un des outils de migration de base de données suivants pour corriger les problèmes détectés :

> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

* Utilisez [SQL Server Data Tools pour Visual Studio (« SSDT »)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): pour utiliser SSDT, vous importez votre schéma de base de données dans SQL Server Data Tools pour Visual Studio (« SSDT ») et générez le projet pour un déploiement de base de données SQL V12. Vous corrigez ensuite tous les problèmes de compatibilité détectés dans SSDT. Lorsque vous avez terminé, vous synchronisez les modifications dans la base de données source (ou une copie de la base de données source). SSDT est la méthode actuellement recommandée pour tester et corriger les problèmes de compatibilité de la base de données SQL V12. Cliquez sur le lien pour accéder à une [procédure pas à pas pour l’utilisation de SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
* Utilisez [SQL Server Management Studio (« SSMS »)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): pour utiliser SSMS, exécutez des commandes Transact-SQL pour corriger les erreurs détectées à l’aide d’un autre outil. Cette méthode est destinée principalement aux utilisateurs avancés, afin qu’ils modifient le schéma de base de données directement dans la base de données source. 
* Utilisez [Assistant Migration SQL Azure (« SAMW »)](sql-database-cloud-migrate-fix-compatibility-issues.md): pour utiliser l’Assistant Migration SQL AZURE, vous générez un script Transact-SQL à partir de la base de données source. L’Assistant transforme le script, le cas échéant, pour rendre le schéma compatible avec la base de données SQL V12. Une fois terminé, SAMW peut se connecter à la base de données SQL V12 pour exécuter le script. Cet outil analyse également les fichiers de trace afin de déterminer les problèmes de compatibilité. Ce script peut être généré avec un schéma uniquement, ou peut inclure des données au format BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Migrez une base de données SQL Server compatible vers une base de données SQL
Pour migrer une base de données SQL Server compatible, Microsoft fournit plusieurs méthodes de migration pour divers scénarios. La méthode que vous choisissez dépend de votre tolérance pour les temps d'arrêt, de la taille et de la complexité de votre base de données SQL Server ainsi que de la connectivité vers le cloud Microsoft Azure.  

> [!div class="op_single_selector"]
> * [Assistant Migration SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [Exporter vers un fichier BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [Importer depuis un fichier BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [Réplication transactionnelle](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

Pour choisir votre méthode de migration, demandez-vous d’abord si vous pouvez vous permettre de sortir la base de données de la production pendant la migration. La migration d'une base de données alors que des transactions actives sont en cours peut entraîner des incohérences et la corruption éventuelle de la base de données. Il existe de nombreuses méthodes pour suspendre une base de données, que ce soit en désactivant la connectivité des clients ou en créant un [instantané de base de données](https://msdn.microsoft.com/library/ms175876.aspx).

Pour une migration avec un temps d’arrêt minimal, utilisez la [réplication transactionnelle SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) si votre base de données remplit les conditions requises pour la réplication transactionnelle. Si vous pouvez vous permettre un temps d'arrêt ou si vous effectuez un test de migration d'une base de données de production que vous envisagez de migrer, examinez l'une des trois méthodes suivantes :

* [Assistant Migration SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) : pour les bases de données de petite et moyenne taille, la migration d’une base de données SQL Server 2005 ou version ultérieure compatible revient à exécuter [l’Assistant de déploiement de base de données dans une base de données Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) dans SQL Server Management Studio.
* [Exportation vers un fichier BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md), puis [Importation à partir du fichier BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) : si vous rencontrez des problèmes de connectivité (aucune connectivité, faible bande passante ou problèmes d’expiration) et pour les bases de données moyennes et volumineuses, utilisez un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Avec cette méthode, vous exportez le schéma SQL Server et les données vers un fichier BACPAC. Vous importez ensuite le fichier BACPAC dans la base de données SQL à l'aide de l'Assistant Exportation d'une application de la couche Données dans SQL Server Management Studio ou de l’utilitaire d’invite de commandes [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .
* Utilisation simultanée de BACPAC et BCP : utilisez un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) et [BCP](https://msdn.microsoft.com/library/ms162802.aspx) pour les bases de données plus volumineuses afin de permettre une meilleure parallélisation pour améliorer les performances, malgré une complexité plus importante. Avec cette méthode, migrez le schéma et les données séparément.
  
  * [Exportez uniquement le schéma vers un fichier BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
  * [Importez uniquement le schéma à partir du fichier BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) dans la base de données SQL.
  * Utilisez [BCP](https://msdn.microsoft.com/library/ms162802.aspx) pour extraire les données dans des fichiers plats, puis [chargez ces fichiers en parallèle](https://technet.microsoft.com/library/dd425070.aspx) dans l’Azure SQL Database.
    
     ![Migration de base de données SQL Server - migrer une base de données SQL vers le cloud.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Étapes suivantes
* [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Fonctionnalités SQL Database](sql-database-features.md)
  [Fonctions Transact-SQL partiellement ou non prises en charge](sql-database-transact-sql-information.md)
* [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


