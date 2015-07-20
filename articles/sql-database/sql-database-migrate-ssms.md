<properties
   pageTitle="Migration vers la base de données SQL via SSMS"
   description="Base de données SQL Microsoft Azure, migrer une base de données SQL, migration via SSMS"
   services="sql-database"
   documentationCenter=""
   authors="pehteh"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="04/14/2015"
   ms.author="pehteh"/>

#Migration d’une base de données compatible à l’aide de SSMS 

![Diagramme de migration de SSMS](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

Si un schéma de base de données est déjà compatible avec la base de données SQL Microsoft Azure, la migration est simple. Comme aucune transformation n’est nécessaire, le processus de migration requiert uniquement l’importation de la base de données dans Microsoft Azure. Ce processus peut être effectué en une seule étape, via SSMS (« déploiement » de la base de données dans la base de données SQL Microsoft Azure), ou en deux étapes (exportation d’un fichier BACPAC, puis importation de ce fichier sur le serveur de base de données SQL Microsoft Azure, afin de créer une base de données).

Vous pouvez charger le fichier BACPAC exporté sur Microsoft Azure Storage, puis l’importer à l’aide du portail. L’exécution du fichier importé dans le cloud permet de réduire la latence à l’étape d’importation, afin d’améliorer les performances et la fiabilité de la migration des bases de données volumineuses.

En effectuant un déploiement direct depuis SSMS, vous déployez systématiquement le schéma et les données, alors que les opérations d’exportation et d’importation déploient systématiquement le schéma et proposent une option de déploiement des données depuis tout ou partie des tables. Que vous gériez le déploiement à partir de SSMS ou le processus d’exportation, puis d’importation des données depuis SSMS (ou le portail, par la suite), la même technologie DAC est utilisée en arrière-plan ; le résultat est le même.

Cette option est également utilisée en tant qu’étape finale de l’option n° 3 pour migrer les bases de données après leur mise à jour, afin qu’elles soient compatibles avec la base de données SQL Microsoft Azure.

##Utilisation de SSMS pour déployer la base de données SQL Microsoft Azure
1.	Configurez un serveur via le portail Microsoft Azure, en suivant les instructions de la section relative à la création du serveur cible pour la base de données migrée.
2. Localisez la base de données source dans l’Explorateur d’objets SSMS et exécutez la tâche **Déployer une base de données vers la base de données SQL Azure...**

	![Déployer vers Azure à partir du menu Tâches](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	Dans l’assistant de déploiement, configurez la connexion avec le serveur de base de données SQL Microsoft Azure cible paramétré au cours de l’étape.
4.	Indiquez le **nom** de la base de données et définissez les valeurs des options **Édition** (niveau de service) et **Objectif de service** (niveau de performances). Pour en savoir plus sur la configuration de ces paramètres, consultez la section relative à la sélection d’un niveau de tarification et de performances pour la base de données lors de la migration. 

	![Paramètres d’exportation](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	Effectuez les étapes de l’assistant pour exécuter la migration de la base de données. Selon la taille et la complexité du déploiement de la base de données, l’opération de déploiement peut prendre quelques minutes ou plusieurs heures. S’il survient des erreurs indiquant que le schéma de base de données n’est pas compatible avec la base de données SQL, vous devez choisir une autre option.

##Utilisez SSMS pour exporter un fichier BACPAC, puis importez-le à nouveau dans la base de données SQL.
Le processus de déploiement peut être divisé en deux étapes : l’exportation et l’importation. Lors de la première étape, un fichier BACPAC est créé, puis utilisé en tant qu’entrée à la deuxième étape.

1.	Configurez un serveur via la dernière mise à jour de la base de données SQL, en suivant les instructions de la section relative à la création du serveur cible pour la base de données migrée.
2.	Localisez la base de données source dans l’Explorateur d’objets SSMS et sélectionnez la tâche **Déployer une base de données vers la base de données SQL Azure...**

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. Dans l’assistant d’exportation, configurez l’exportation pour enregistrer le fichier BACPAC en local. Le fichier BACPAC exporté inclut toujours le schéma de base de données complet et, par défaut, les données de toutes les tables. Utilisez l’onglet Avancé si vous souhaitez exclure les données de toutes les tables ou de certaines d’entre elles uniquement. Par exemple, vous pouvez choisir d’exporter uniquement les données des tables de référence.
	>[AZURE.NOTE]Remarque : lorsque le portail de gestion de Microsoft Azure prend en charge l’importation en cours d’exécution dans Microsoft Azure, vous pouvez opter pour l’enregistrement du fichier BACPAC exporté dans Microsoft Azure Storage, puis exécuter le fichier importé dans le cloud.

	![Paramètres d’exportation](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	Une fois le fichier BACPAC créé, connectez-vous au serveur créé à l’étape 1, cliquez avec le bouton droit de la souris sur le dossier Bases de données et sélectionnez l’option Importer une application de la couche Données...

	![Importer l’élément de menu de l’application de la couche Données](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	Dans l’assistant d’importation, sélectionnez le fichier BACPAC que vous venez d’exporter pour créer la base de données dans la base de données SQL Microsoft Azure.

	![Paramètres d’importation](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	Indiquez le nom de la base de données et définissez les valeurs des options Édition (niveau de service) et Objectif de service (niveau de performances).
	 
7.	Effectuez les étapes de l’assistant pour importer le fichier BACPAC et créer la base de données dans la base de données SQL Microsoft Azure.

	![Paramètres de base de données](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)
 
##Autres solutions
Vous pouvez également utiliser l’utilitaire de ligne de commande appelé sqlpackage.exe pour déployer la base de données, ou exporter et importer un fichier BACPAC. L’utilitaire sqlpackage.exe utilise la même technologie DAC que le logiciel SSMS ; le résultat est donc le même. Pour plus d’informations, consultez [SqlPackage.exe sur MSDN](https://msdn.microsoft.com/library/hh550080.aspx).

<!---HONumber=July15_HO2-->