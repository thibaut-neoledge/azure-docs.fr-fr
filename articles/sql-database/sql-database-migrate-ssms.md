<properties
   pageTitle="Migration vers la base de données SQL via SSMS"
	description="Microsoft Azure SQL Database, migrer une base de données SQL, migration via SSMS"
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
	ms.date="08/24/2015"
	ms.author="carlrab"/>

#Migration d’une base de données compatible à l’aide de SSMS

![Diagramme de migration de SSMS](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

Si un schéma de base de données est compatible avec la base de données SQL Azure, la migration exige uniquement l’importation de la base de données dans Azure. Ce processus peut être effectué soit en une seule étape via SSMS par le « déploiement » de la base de données sur Azure SQL, soit en deux étapes (exportation d’un fichier BACPAC de la base de données, puis importation du dit fichier sur BACPAC dans le serveur SQL Azure en tant que nouvelle base de données).

Lorsque vous exportez un fichier BACPAC, vous pouvez soit l’exporter vers un fichier local ou directement vers un objet blob Azure. Si vous l’exportez localement, vous pouvez télécharger le fichier BACPAC exporté vers un objet blob Azure. Une fois le fichier BACPAC stocké dans un objet blob Azure, vous pouvez ensuite importer le fichier BACPAC en tant que base de données à l’aide du portail Azure. L’exécution du fichier importé dans le portail Azure permet de réduire la latence à l’étape d’importation, afin d’améliorer les performances et la fiabilité de la migration des bases de données volumineuses.

Le déploiement direct depuis SSMS déploiera l’ensemble du schéma et toutes les données, tandis que l’exportation à l’aide d’un fichier BACPAC vous permet de sélectionner le sous-ensemble d’objets à exporter vers le fichier BACPAC. Le fichier BACPAC exporté inclut toujours le schéma de base de données complet et, par défaut, les données de toutes les tables. Que vous gériez le déploiement à partir de SSMS ou utilisiez le processus d’exportation, puis d’importation des données depuis SSMS (ou le portail Azure), la même technologie DAC est utilisée en arrière-plan et le résultat est le même.

Cette option est également utilisée en tant qu’étape finale de l’option n° 2 pour migrer les bases de données après leur mise à jour, afin qu’elles soient compatibles avec la base de données SQL Azure.

## Obtenir la dernière version de SQL Server Management Studio

Utiliser la dernière version de Microsoft SQL Server Management Studio pour un serveur SQL pour vous assurer que les dernières mises à jour pour les outils dans SSMS et d’interagir avec le portail Azure. Pour obtenir la toute dernière version de Microsoft SQL Server Management Studio pour SQL Server, [téléchargez-la](https://msdn.microsoft.com/library/mt238290.aspx) et installez-la sur un ordinateur client avec une connexion à la base de données que vous prévoyez de faire migrer et à Internet.

##Utilisation de SSMS pour déployer sur Azure SQL Database
1.	Configuration d’un serveur logique via le portail de gestion Azure
2.	Localisez la base de données source dans l’Explorateur d’objets SSMS et exécutez la tâche **Exporter l’application de la couche Données...**

	![Déployer vers Azure à partir du menu Tâches](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	Dans l’assistant de déploiement, configurez la connexion avec le serveur logique de base de données SQL Microsoft Azure cible approvisionné lors de l’étape 1.
4.	Indiquez le **nom** de la base de données et définissez les valeurs des options **Édition** (niveau de service) et **Objectif de service** (niveau de performances). Pour plus d’informations sur la configuration de ces paramètres, consultez la rubrique [Niveaux du service Azure SQL Database](sql-database-service-tiers.md).

	![Paramètres d’exportation](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	Terminez l’Assistant pour migrer la base de données. Selon la taille et la complexité de la base de données, le déploiement peut durer de quelques minutes à plusieurs heures. En cas d’incompatibilité, les routines de validation de schéma vont rapidement détecter les erreurs avant tout déploiement d’Azure. Si des erreurs indiquent que le schéma de base de données n’est pas compatible avec la base de données SQL, utiliser l’option de migration #2. Voir [Mettre à jour la base de données existante et la déployer dans la base de données SQL.](sql-database-migrate-visualstudio-ssdt.md)

##Utilisez SSMS pour exporter un fichier BACPAC, puis importez-le à nouveau dans la base de données SQL.
Le processus de déploiement peut être divisé en deux étapes : l’exportation et l’importation. Lors de la première étape, un fichier BACPAC est créé, puis utilisé en tant qu’entrée à la deuxième étape.

1.	Configuration d’un serveur logique via le portail de gestion Azure
2.	Localisez la base de données source dans l’Explorateur d’objets SSMS et sélectionnez la tâche **Déployer une base de données vers une base de données SQL Azure...**

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. Dans l’Assistant Exportation, configurez l’exportation afin d’enregistrer le fichier BACPAC dans un emplacement de disque local ou dans un objet blob Azure. Le fichier BACPAC exporté inclut toujours le schéma de base de données complet et, par défaut, les données de toutes les tables. Utilisez l’onglet Avancé si vous souhaitez exclure les données de toutes les tables ou de certaines d’entre elles uniquement. Par exemple, vous pouvez choisir d’exporter uniquement les données des tables de référence.

	![Paramètres d’exportation](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	Une fois le fichier BACPAC créé, connectez-vous au serveur créé à l’étape 1, cliquez avec le bouton droit de la souris sur le dossier **Bases de données** et sélectionnez l’option ** Importer une application de la couche Données...**
	
	>[AZURE.NOTE]Remarque : vous pouvez également importer le fichier BACPAC stocké dans un objet blob Azure au sein du portail de gestion Azure.

	![Importer l’élément de menu de l’application de la couche Données](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	Dans l’Assistant d’importation, sélectionnez le fichier BACPAC que vous venez d’exporter pour créer la base de données dans Azure SQL Database.

	![Paramètres d’importation](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	Indiquez le nom de la base de données et définissez les valeurs des options Édition (niveau de service) et Objectif de service (niveau de performances).

7.	Effectuez les étapes de l’assistant pour importer le fichier BACPAC et créer la base de données dans Azure SQL Database.

	![Paramètres de base de données](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)

##Autres solutions
Vous pouvez également utiliser l’utilitaire de ligne de commande appelé sqlpackage.exe pour déployer la base de données, ou exporter et importer un fichier BACPAC. L’utilitaire sqlpackage.exe utilise la même technologie DAC que le logiciel SSMS ; le résultat est donc le même. Pour plus d’informations, consultez [SqlPackage.exe sur MSDN](https://msdn.microsoft.com/library/hh550080.aspx).

<!---HONumber=August15_HO9-->