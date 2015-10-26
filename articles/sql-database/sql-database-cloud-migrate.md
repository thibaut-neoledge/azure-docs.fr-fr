<properties
   pageTitle="Migration d’une base de données SQL Server vers une base de données SQL Azure"
   description="Microsoft Azure SQL Database, déployer base de données, migration base de données, importer base de données, exporter base de données, assistant de migration"
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
   ms.date="10/12/2015"
   ms.author="carlrab"/>

# Migration d’une base de données SQL Server vers une base de données SQL Azure

Azure SQL Database V12 offre une compatibilité moteur quasi-totale avec SQL Server 2014 et SQL Server 2016. Pour les bases de données compatibles, la migration vers une base de données SQL Azure s’effectue en toute simplicité. Quant à l’opération de déplacement de données, elle ne nécessite que peu, voire aucune modification du schéma et ne requiert aucune refonte des applications. Si les bases de données doivent être modifiées, l’étendue de ces modifications est plus limitée qu’avec Azure SQL Database V11.

Par conception, les fonctions de d’étendue de serveur SQL Server ne sont pas prises en charge par la base de données V12 SQL Azure. Les bases de données et applications qui reposent sur ces fonctionnalités ont besoin d’une nouvelle ingénierie avant de pouvoir être migrées.

>[AZURE.NOTE]Pour migrer d’autres types de bases de données, notamment Microsoft Access, Sybase, MySQL Oracle et DB2, vers une base de données SQL Azure, consultez l’[Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/).

Procédure à suivre pour migrer une base de données SQL Server vers une base de données SQL Azure :

 1. [Déterminer la compatibilité de votre base de données](#determine-if-your-database-is-compatible)
 2. [Si elle n’est pas compatible, résoudre les problèmes de compatibilité de base de données](#fix-database-compatibility-issues)
 3. [Migrer une base de données compatible](#options-to-migrate-a-compatible-database-to-azure-sql-database)

## Déterminer la compatibilité de votre base de données
Vous pouvez vous appuyer sur deux méthodes pour déterminer si votre base de données source est compatible. -Exporter une application de la couche Données : cette méthode utilise un assistant dans Management Studio et affiche les messages d’erreur sur la console. -SQLPackage.exe : [sqlpackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) est un utilitaire de ligne de commande fourni avec Visual Studio et SQL Server. Cette méthode permet de générer un rapport.

> [AZURE.NOTE]Il existe une troisième méthode, qui utilise également les fichiers de trace pour tester la compatibilité des bases de données. Il s’agit de l’[Assistant Migration SQL Azure](http://sqlazuremw.codeplex.com/), un outil gratuit disponible sur Codeplex. Cet outil peut toutefois détecter des erreurs de compatibilité qui posaient problème sur la version 11 de la base de données SQL Azure mais qui ont été résolues dans la version 12.

Si des incompatibilités de base de données sont détectées, vous devrez les corriger pour pouvoir migrer votre base de données vers une base de données SQL Azure. Pour obtenir des instructions sur la résolution des problèmes de compatibilité de base de données, consultez la page [Résoudre les problèmes de compatibilité de base de données](#fix-database-compatibility-issues).

> [AZURE.IMPORTANT]Ces options ne permettent pas d’intercepter tous les problèmes de compatibilité entre les différents niveaux de bases de données SQL Server (c’est-à-dire les niveaux 90, 100 et 110). Si vous effectuez la migration à partir d’une base de données d’ancienne génération (niveaux 80, 90, 100 et 110), vous devrez dans un premier temps procéder à une mise à niveau (au moins dans l’environnement de développement). Une fois que vous aurez installé SQL Server 2014 ou une version ultérieure, vous pourrez migrer vers la base de données SQL Azure.

## Déterminer la compatibilité de votre base de données à l’aide de l’utilitaire sqlpackage.exe

1. Ouvrez une invite de commandes et modifiez le répertoire contenant la version la plus récente de sqlpackage.exe. Cet utilitaire est fourni avec Visual Studio et SQL Server.
2. Exécutez la commande ci-dessous en remplaçant les arguments suivants : < server_name >, < database_name >, < target_file >, < schema_name.table_name > et < output_file >. L’argument /p:TableName est utilisé afin de tester uniquement la compatibilité de base de données en vue d’une exportation vers Azure SQL DB V12. Nous ne cherchons pas en effet à exporter les données de toutes les tables. Malheureusement, l’argument d’exportation de sqlpackage.exe ne permet pas de n’extraire aucune table. Vous devrez donc spécifier une petite table. Le fichier < output_file > contiendra le rapport d’erreurs.

	’sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1’

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Ouvrez le fichier de sortie et passez en revue les erreurs de compatibilité, le cas échéant. Pour obtenir des instructions sur la résolution des problèmes de compatibilité de base de données, consultez la page [Résoudre les problèmes de compatibilité de base de données](#fix-database-compatibility-issues).

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Déterminer la compatibilité de votre base de données à l’aide de l’option Exporter une application de la couche Données

1. Vérifiez que vous disposez de la version 13.0.600.65 ou d’une version ultérieure de SQL Server Management Studio. Les nouvelles versions de Management Studio sont mises à jour tous les mois afin de refléter les mises à jour publiées sur le portail Azure.

 	 >[AZURE.IMPORTANT]Téléchargez la [dernière](https://msdn.microsoft.com/library/mt238290.aspx) version de SQL Server Management Studio. Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio.

2. Ouvrez Management Studio et connectez-vous à votre base de données source dans l’Explorateur d’objets.
3. Dans l’Explorateur d’objets, cliquez avec le bouton droit de la souris sur la base de données source, pointez sur **Tâches**, puis cliquez sur **Exporter une application de la couche Données**.

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Dans l’Assistant Exportation, sous l’onglet **Paramètres**, configurez l’exportation de manière à enregistrer le fichier BACPAC sur le disque local ou dans un objet blob Azure. L’enregistrement du fichier BACPAC ne pourra s’effectuer que si vous ne rencontrez aucun problème de compatibilité de base de données. Si des problèmes de compatibilité sont détectés, ils s’afficheront sur la console.

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Cliquez sur l’**onglet Avancé** et décochez la case **Sélectionner tout** pour ignorer l’exportation des données. À ce stade, nous ne cherchons qu’à tester la compatibilité.

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Cliquez sur **Suivant** puis sur **Terminer**. Les éventuels problèmes de compatibilité de base de données s’afficheront une fois le schéma validé par l’Assistant.

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Si aucune erreur n’apparaît, votre base de données est compatible : vous êtes donc prêt pour la migration. Si des erreurs sont détectées, vous devez les corriger. Pour afficher les erreurs, cliquez sur **Erreur** au niveau de **Schéma de validation**. Pour savoir comment résoudre ces erreurs, consultez la page [Résoudre les problèmes de compatibilité de base de données](#fix-database-compatibility-issues).

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

## Options de migration d’une base de données compatible vers la base de données SQL Azure

- Pour les bases de données petites à moyennes, la migration d’une base de données SQL Server 2005 ou version ultérieure [compatible](#determine-if-your-database-is-compatible) revient à exécuter l’[Assistant de déploiement de base de données dans une base de données Microsoft Azure](#use-the-deploy-database-to-microsoft-azure-database-wizard) sous SQL Server Management Studio. Si vous avez des problèmes de connectivité (absence de connectivité, faible bande passante ou problèmes de délai d’expiration), vous pouvez [utiliser un fichier BACPAC pour effectuer la migration](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database) d’une base de données SQL Server vers la base de données SQL Azure.
- Pour les bases de données plus volumineuses, vous pouvez [utiliser un fichier BACPAC pour effectuer la migration](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database) d’une base de données SQL Server vers la base de données SQL Azure. Cette méthode s’applique également si vous rencontrez des problèmes de connectivité. Cette méthode vous permet d’utiliser SQL Server Management Studio pour exporter les données et le schéma dans un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (enregistré en local ou dans un objet blob Azure), puis d’importer le fichier BACPAC dans votre instance SQL Azure. Si vous enregistrez le fichier BACPAC dans un objet blob Azure, vous pouvez également importer ce fichier à partir du [portal Azure](sql-database-import.md) ou [à l’aide de PowerShell](sql-database-import-powershell.md).
- Pour les bases de données plus volumineuses, vous obtiendrez les meilleures performances en faisant migrer séparément le schéma et les données. Avec cette méthode, vous pouvez écrire le script du schéma à l’aide de SQL Server Management Studio ou créer un projet de base de données dans Visual Studio avant de déployer le schéma dans la base de données SQL Azure. Une fois le schéma importé dans la base de données SQL Azure, vous pouvez utiliser l’utilitaire [BCP](https://msdn.microsoft.com/library/ms162802.aspx) pour extraire les données dans des fichiers plats, puis importer ces fichiers dans la base de données SQL Azure.

 ![Diagramme de migration de SSMS](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

## Utiliser l’Assistant de déploiement de base de données dans une base de données Microsoft Azure

Cet Assistant, disponible dans SQL Server Management Studio, permet de migrer une base de données SQL Server 2005 ou version ultérieure [compatible](#determine-if-your-database-is-compatible) directement dans votre instance de serveur logique SQL Azure.

> [AZURE.NOTE]Les étapes ci-dessous supposent que vous avez déjà déployé votre instance logique SQL Azure et que vous disposez des informations de connexion.

1. Vérifiez que vous disposez de la version 13.0.600.65 ou d’une version ultérieure de SQL Server Management Studio. Les nouvelles versions de Management Studio sont mises à jour tous les mois afin de refléter les mises à jour publiées sur le portail Azure.

	 >[AZURE.IMPORTANT]Téléchargez la [dernière](https://msdn.microsoft.com/library/mt238290.aspx) version de SQL Server Management Studio. Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio.

2. Ouvrez Management Studio et connectez-vous à votre base de données source dans l’Explorateur d’objets.
3. Dans l’Explorateur d’objets, cliquez avec le bouton droit de la souris sur la base de données source, pointez sur **Tâches**, puis cliquez sur **Déployer une base de données sur Microsoft Azure SQL Database...**

	![Déployer vers Azure à partir du menu Tâches](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	Dans l’Assistant déploiement, configurez la connexion à votre serveur de base de données SQL Azure.
5.	Renseignez le **Nouveau nom de la base de données** résidant sur la base de données SQL Azure, définissez l’**Édition de Microsoft Azure SQL Database** (niveau de service), la **Taille maximale de base de données**, l’**Objectif de service** (niveau de performance) et le **Nom de fichier temporaire** à attribuer au fichier BACPAC qui sera créé par cet Assistant au cours de la migration. Pour plus d’informations sur les niveaux de service et les niveaux de performance, voir [Niveaux de service de base de données SQL Azure](sql-database-service-tiers.md).

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	Terminez l’Assistant pour migrer la base de données. Selon la taille et la complexité de la base de données, le déploiement peut durer de quelques minutes à plusieurs heures.
7.	À l’aide de l’Explorateur d’objets, connectez-vous à la base de données que vous venez de déployer sur votre serveur de base de données SQL Azure.
8.	Dans le portail Azure, vous pouvez afficher votre base de données et ses propriétés.

## Utiliser un fichier BACPAC pour migrer une base de données SQL Server vers une base de données SQL Azure

Si vous utilisez des bases de données volumineuses ou si vous rencontrez des problèmes de connectivité, vous pouvez décomposer le processus de migration en deux étapes distinctes. Vous pouvez exporter le schéma et ses données dans un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) en procédant de l’une des manières suivantes :

- [Exporter vers un fichier BACPAC à l’aide de SQL Server Management Studio](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio)
- [Exporter vers un fichier BACPAC à l’aide de SqlPackage](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sqlpackage)

Vous pouvez stocker ce fichier BACPAC en local ou dans un objet blob Azure. Vous disposez ensuite de plusieurs méthodes pour l’importer dans la base de données SQL Azure.

- [Importer un fichier BACPAC dans la base de données SQL Azure à l’aide de SQL Server Management Studio](#import-from-a-bacpac-file-into-azure-sql-database-using-sql-server-management-studio)
- [Importer un fichier BACPAC dans la base de données SQL Azure à l’aide de SqlPackage](#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage)
- [Importer un fichier BACPAC dans la base de données SQL Azure à l’aide du portail Azure](sql-database-import.md)
- [Importer un fichier BACPAC dans la base de données SQL Azure à l’aide de PowerShell](sql-database-import-powershell.md)

## Exporter une base de données SQL Server compatible vers un fichier BACPAC à l’aide de SQL Server Management Studio

Procédez comme suit pour exporter une base de données SQL Server [compatible](#determine-if-your-database-is-compatible) vers un fichier BACPAC à l’aide de Management Studio.

1. Vérifiez que vous disposez de la version 13.0.600.65 ou d’une version ultérieure de SQL Server Management Studio. Les nouvelles versions de Management Studio sont mises à jour tous les mois afin de refléter les mises à jour publiées sur le portail Azure.

	 >[AZURE.IMPORTANT]Téléchargez la [dernière](https://msdn.microsoft.com/library/mt238290.aspx) version de SQL Server Management Studio. Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio.

2. Ouvrez Management Studio et connectez-vous à votre base de données source dans l’Explorateur d’objets.

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Dans l’Explorateur d’objets, cliquez avec le bouton droit de la souris sur la base de données source, pointez sur **Tâches**, puis cliquez sur **Exporter une application de la couche Données**.

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Dans l’Assistant Exportation, configurez l’exportation de manière à enregistrer le fichier BACPAC sur le disque local ou dans un objet blob Azure. Le fichier BACPAC exporté inclut toujours le schéma de base de données complet et, par défaut, les données de toutes les tables. Utilisez l’onglet Avancé si vous souhaitez exclure les données de toutes les tables ou de certaines d’entre elles uniquement. Vous pouvez, par exemple, choisir d’exporter uniquement les données des tables de référence au lieu d’exporter toutes les tables.

	![Paramètres d’exportation](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## Exporter une base de données SQL Server compatible vers un fichier BACPAC à l’aide de SqlPackage

Procédez comme suit pour exporter une base de données [compatible](#determine-if-your-database-is-compatible) vers un fichier BACPAC à l’aide de l’utilitaire de ligne de commande [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).

> [AZURE.NOTE]Les étapes ci-dessous supposent que vous avez déjà configuré un serveur de base de données SQL Azure, que vous disposez des informations de connexion et que vous avez vérifié la compatibilité de votre base de données source.

1. Ouvrez une invite de commandes et modifiez un répertoire contenant l’utilitaire de ligne de commande sqlpackage.exe Cet utilitaire est fourni avec Visual Studio et SQL Server.
2. Exécutez la commande suivante, en remplaçant les arguments suivants : < server_name >, < database_name > et < target_file >.

	’sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Importer un fichier BACPAC dans une base de données SQL Azure à l’aide de SQL Server Management Studio

Procédez comme suit pour importer un fichier BACPAC dans une base de données SQL Azure.

> [AZURE.NOTE]Les étapes ci-dessous supposent que vous avez déjà déployé votre instance logique SQL Azure et que vous disposez des informations de connexion.

1. Vérifiez que vous disposez de la version 13.0.600.65 ou d’une version ultérieure de SQL Server Management Studio. Les nouvelles versions de Management Studio sont mises à jour tous les mois afin de refléter les mises à jour publiées sur le portail Azure.

	 >[AZURE.IMPORTANT]Téléchargez la [dernière](https://msdn.microsoft.com/library/mt238290.aspx) version de SQL Server Management Studio. Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio.

2. Ouvrez Management Studio et connectez-vous à votre base de données source dans l’Explorateur d’objets.

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

    Une fois le fichier BACPAC créé, connectez-vous à votre serveur de base de données SQL Azure, cliquez avec le bouton droit de la souris sur le dossier **Bases de données**, puis cliquez sur **Importer une application de la couche Données**.

    ![Importer l’élément de menu de l’application de la couche Données](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.	Dans l’Assistant d’importation, sélectionnez le fichier BACPAC que vous venez d’exporter pour créer la base de données dans Azure SQL Database.

    ![Paramètres d’importation](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

4.	Renseignez le **Nouveau nom de la base de données** résidant sur la base de données SQL Azure, définissez l’**Édition de Microsoft Azure SQL Database** (niveau de service), la **Taille maximale de base de données** et l’**Objectif de service** (niveau de performance).

    ![Paramètres de base de données](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.	Cliquez sur **Suivant**, puis cliquez sur **Terminer** pour importer le fichier BACPAC dans une nouvelle base de données sur le serveur de base de données SQL Azure.

6. À l’aide de l’Explorateur d’objets, connectez-vous à la base de données que vous venez de déployer sur votre serveur de base de données SQL Azure.

7.	Dans le portail Azure, vous pouvez afficher votre base de données et ses propriétés.

## Importer un fichier BACPAC dans une base de données SQL Azure à l’aide de SqlPackage

Procédez comme suit pour importer une base de données SQL Server compatible à partir d’un fichier BACPAC à l’aide de l’utilitaire de ligne de commande [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).

> [AZURE.NOTE]Les étapes ci-dessous supposent que vous avez déjà configuré un serveur de base de données SQL Azure et que vous disposez des informations de connexion.

1. Ouvrez une invite de commandes et modifiez un répertoire contenant l’utilitaire de ligne de commande sqlpackage.exe Cet utilitaire est fourni avec Visual Studio et SQL Server.
2. Exécutez la commande suivante, en remplaçant les arguments suivants : < server_name >, < database_name >, < user_name >, < password > et < source_file >.

	’sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< target_file >

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)


## Résoudre les problèmes de compatibilité de base de données

Si vous constatez que votre base de données SQL Server source n’est pas compatible, vous disposez de plusieurs options pour corriger les problèmes de compatibilité que vous avez [identifiés précédemment](#determine-if-your-database-is-compatible).

- Utiliser l’[Assistant Migration SQL Azure](http://sqlazuremw.codeplex.com/). Vous pouvez utiliser cet outil Codeplex pour générer un script T-SQL à partir d’une base de données source incompatible, qui sera ensuite transformé par l’Assistant de manière à garantir sa compatibilité avec la base de données SQL. Il vous suffit alors de vous connecter à la base de données SQL Azure pour exécuter le script. Cet outil analyse également les fichiers de trace afin de déterminer les problèmes de compatibilité. Ce script peut être généré avec un schéma uniquement, ou peut inclure des données au format BCP. Pour obtenir une documentation supplémentaire, y compris des instructions pas à pas, consultez la rubrique [SQL Azure Migration Wizard](http://sqlazuremw.codeplex.com/) sur le site Codeplex.  

 ![Schéma de migration SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

 >[AZURE.NOTE]Remarque : les schémas non compatibles que l’assistant est susceptible de détecter ne sont pas tous traités par ses transformations intégrées. Un script non compatible qui ne peut pas être traité est signalé comme une erreur et des commentaires sont insérés dans le script généré. Si de nombreuses erreurs sont détectées, utilisez Visual Studio ou SQL Server Management Studio pour examiner et résoudre chaque erreur que l’Assistant Migration SQL Server ne permet pas de corriger.

- Utilisez Visual Studio. Vous pouvez utiliser Visual Studio pour importer le schéma de base de données dans un projet de base de données Visual Studio à des fins d’analyse. Pour l’analyse, vous devez désigner la plate-forme cible du projet en tant que base de données V12, puis réalisez le projet. Si la version est réussie, la base de données est compatible. Si la version échoue, vous pouvez résoudre les erreurs dans SQL Server Data Tools pour Visual Studio (« SSDT »). Une fois la génération du projet réussie, vous pouvez de nouveau publier ce dernier en tant que copie de base de données source, puis utiliser la fonction de comparaison des données de SSDT pour copier les données de la base de données source vers la base de données Azure SQL V12 compatible. Cette base de données ainsi mise à jour est ensuite déployée dans la base de données SQL Azure selon les méthodes [abordées précédemment](#options-to-migrate-a-compatible-database-to-azure-sql-database).

 ![Schéma de migration VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

 >[AZURE.NOTE]Si seule la migration de schéma est requise, vous pouvez publier le schéma directement depuis Visual Studio vers la base de données SQL Azure. Utilisez cette méthode lorsque le schéma de base de données nécessite un plus grand nombre de modifications qui peuvent être gérées par l’Assistant seul.

- SQL Server Management Studio. Vous pouvez résoudre les problèmes dans Management Studio à l’aide de différentes commandes Transact-SQL, telles que **ALTER DATABASE**.

<!---HONumber=Oct15_HO3-->