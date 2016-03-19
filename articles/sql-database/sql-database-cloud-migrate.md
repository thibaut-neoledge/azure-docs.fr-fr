<properties
   pageTitle="Migration de base de données SQL Server vers SQL Database | Microsoft Azure"
   description="Découvrez comment migrer une base de données SQL Server locale vers Azure SQL Database dans le cloud. Utilisez les outils de migration de base de données pour tester la compatibilité avant de procéder à la migration de la base de données."
   keywords="migration de base de données, migration de base de données sql server, outils de migration de base de données, migrer la base de données, migrer la base de données sql"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="01/05/2016"
   ms.author="carlrab"/>

# Migration de base de données SQL Server vers SQL Database dans le cloud

Cet article vous montre comment migrer une base de données SQL Server 2005 ou version ultérieure locale vers la base de données SQL Azure. Dans ce processus de migration de base de données, vous migrez votre schéma et vos données à partir de la base de données SQL Server dans votre environnement actuel vers SQL Database, à condition que la base de données existante réussisse les tests de compatibilité. Avec [la version 12 de la base de données SQL](sql-database-v12-whats-new.md), il existe très peu de problèmes de compatibilité autres que les opérations au niveau du serveur et de bases de données croisées. Les bases de données et applications qui reposent sur des [fonctions partiellement ou pas du tout prises en charge](sql-database-transact-sql-information.md) ont besoin d'une nouvelle ingénierie pour [corriger ces incompatibilités](sql-database-cloud-migrate-fix-compatibility-issues.md) avant de pouvoir migrer la base de données SQL Server.

> [AZURE.NOTE] Pour migrer une base de données non SQL Server, notamment Microsoft Access, Sybase, MySQL Oracle et DB2, vers une base de données SQL Azure, consultez l’[Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/).

## Les outils de migration de base de données testent la compatibilité de la base de données SQL Server avec SQL Database

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

Pour tester les problèmes de compatibilité de la base de données SQL avant de commencer le processus de migration de la base de données, utilisez l’une des méthodes suivantes :

- [Utilisation de SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) : SqlPackage est un utilitaire d’invite de commandes qui recherche d’éventuels problèmes de compatibilité et, le cas échéant, génère un rapport sur lequel figurent les problèmes détectés.
- [Utilisation de SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md) : l’Assistant Exportation de l’application de la couche Données dans SQL Server Management Studio affiche les erreurs détectées à l'écran.

## Résoudre les problèmes de compatibilité de migration de la base de données

Si des problèmes de compatibilité sont détectés, vous devez les corriger avant de procéder à la migration de la base de données SQL Server. Utilisez les outils de migration de base de données suivants :

- Utilisez l’[Assistant Migration SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Utilisez [SQL Server Data Tools pour Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Utilisez [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Migrez une base de données SQL Server compatible vers une base de données SQL

Pour migrer une base de données SQL Server compatible, Microsoft fournit plusieurs méthodes de migration pour divers scénarios. La méthode que vous choisissez dépend de votre tolérance pour les temps d'arrêt, de la taille et de la complexité de votre base de données SQL Server ainsi que de la connectivité vers le cloud Microsoft Azure.

> [AZURE.SELECTOR]
- [SSMS Migration Wizard](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Export to BACPAC File](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Import from BACPAC File](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactional Replication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Pour choisir votre méthode de migration, demandez-vous d’abord si vous pouvez vous permettre de sortir la base de données de la production pendant la migration. La migration d'une base de données alors que des transactions actives sont en cours peut entraîner des incohérences et la corruption éventuelle de la base de données. Il existe de nombreuses méthodes pour suspendre une base de données, que ce soit en désactivant la connectivité des clients ou en créant un [instantané de base de données](https://msdn.microsoft.com/library/ms175876.aspx).

Pour une migration avec un temps d’arrêt minimal, utilisez la [réplication transactionnelle SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) si votre base de données remplit les conditions requises pour la réplication transactionnelle. Si vous pouvez vous permettre un temps d'arrêt ou si vous effectuez un test de migration d'une base de données de production que vous envisagez de migrer, examinez l'une des trois méthodes suivantes :

- [Assistant Migration SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) : Pour les petites et moyennes bases de données, la migration d’une base de données SQL Server 2005 ou version ultérieure compatible revient à exécuter l’[Assistant de déploiement de base de données dans une base de données Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) dans SQL Server Management Studio.
- [Exportation vers un fichier BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) puis [Importation à partir du fichier BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) : Si vous rencontrez des problèmes de connectivité (aucune connectivité, faible bande passante ou problèmes de délai) et pour les bases de données moyennes et volumineuses, utilisez un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Avec cette méthode, vous exportez le schéma et les données SQL Server vers un fichier BACPAC puis importez le fichier BACPAC dans la base de données SQL à l'aide de l'Assistant Exportation d'une application de la couche Données dans SQL Server Management Studio ou de l’utilitaire d’invite de commandes [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).
- Utilisation simultanée de BACPAC et BCP : utilisez un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) et [BCP](https://msdn.microsoft.com/library/ms162802.aspx) pour les bases de données plus volumineuses afin de permettre une meilleure parallélisation pour améliorer les performances, avec une complexité plus importante. Avec cette méthode, migrez le schéma et les données séparément.
 - [Exportez uniquement le schéma vers un fichier BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importez uniquement le schéma à partir du fichier BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) dans la base de données SQL.
 - Utilisez [BCP](https://msdn.microsoft.com/library/ms162802.aspx) pour extraire les données dans des fichiers plats, puis [importez ces fichiers en parallèle](https://technet.microsoft.com/library/dd425070.aspx) dans la base de données SQL Azure.

	 ![Migration de base de données SQL Server - migrer une base de données SQL vers le cloud.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_0128_2016-->