<properties 
	pageTitle="Prise en main de HDInsight Tools pour Visual Studio | Azure" 
	description="Découvrez comment installer et utiliser HDInsight Tools pour Visual Studio pour vous connecter à HDInsight et exécuter des requêtes Hive." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="11/12/2014" 
	ms.author="jgao" 
	editor="cgronlun"/>

# Prise en main de HDInsight Hadoop Tools pour Visual Studio

Découvrez comment installer et utiliser HDInsight Tools pour Visual Studio pour vous connecter à HDInsight et exécuter des requêtes Hive. Pour plus d'informations sur l'utilisation de HDInsight, consultez les rubriques [Présentation de HDInsight][hdinsight.introduction] et [Prise en main de HDInsight][hdinsight.get.started].

+ [Installation] 
+ [Se connecter à un abonnement Azure]
+ [Accéder aux ressources liées]
+ [Exécuter des requêtes Hive]
+ [Étapes suivantes]


##Conditions préalables

- Station de travail avec :

	- Windows 7 ou Windows 8
	- Visual Studio 2012 avec [Update 4](http://www.microsoft.com/fr-fr/download/details.aspx?id=39305), Visual Studio 2013 avec [Update 3](http://www.microsoft.com/fr-fr/download/details.aspx?id=43721) ou [Visual Studio Express 2013](http://www.microsoft.com/fr-fr/download/details.aspx?id=43722).

	>[AZURE.NOTE] À l'heure actuelle, les outils sont uniquement disponibles en anglais. 


## Installation

HDInsight Tools pour Visual Studio est fourni avec le Kit de développement logiciel (SDK) Microsoft Azure. Vous pouvez utiliser [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386) pour effectuer l'installation.

![HDinsight Tools for Visual Studio Web Platform installer][1]


## Se connecter à un abonnement Azure
Avec HDInsight Tools pour Visual Studio, vous pouvez vous connecter à vos clusters HDInsight, effectuer des opérations de gestion de base et exécuter des requêtes Hive.

**Se connecter à un abonnement Azure**

1.	Exécutez Visual Studio.
2.	Dans le menu **Affichage**, cliquez sur **Explorateur de serveurs** pour ouvrir la fenêtre du même nom.
3.	Développez **Azure**, puis cliquez sur **HDInsight Clusters** (Clusters HDInsight). 

	>[AZURE.NOTE]Notez que la fenêtre **HDInsight Task List** (Liste des tâches HDInsight) s'ouvre. Si vous ne la voyez pas, cliquez sur **Other Windows** (Autres fenêtres) dans le menu **VIEW** (Affichage), puis sur **HDInsight Task List Window** (Fenêtre Liste des tâches HDInsight) pour l'ouvrir.  
4.	Entrez les informations d'identification de votre abonnement Azure, puis cliquez sur **Sign In** (Connexion). Cette étape n'est nécessaire que si vous ne vous êtes pas encore connecté à l'abonnement Azure à partir de Visual Studio sur cette station de travail.
5.	L'Explorateur de serveurs dresse la liste des clusters HDInsight existants. Si vous ne possédez aucun cluster, vous pouvez en approvisionner un dans le Portail de gestion, avec Azure PowerShell ou à l'aide du SDK HDInsight.  Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision].

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	Développez un cluster HDInsight. Vous pouvez voir **Hive Databases** (Bases de données Hive), le compte de stockage par défaut et les comptes de stockage liés. Vous pouvez développer davantage les entités. 

Une fois connecté à votre abonnement Azure, vous pouvez effectuer les tâches suivantes :

**Pour vous connecter au Portail de gestion à partir de Visual Studio**

- Dans l'Explorateur de serveurs, développez **Azure**, **HDInsight Clusters** (Clusters HDInsight), cliquez avec le bouton droit sur un cluster HDInsight, puis cliquez sur **Manage Cluster in Azure Portal** (Gérer le cluster dans le portail Azure).

**Pour poser des questions et envoyer des commentaires à partir de Visual Studio**

- Dans le menu **TOOLS** (OUTILS), cliquez sur **HDInsight**, puis sur **MSDN Forum** (Forum MSDN) pour poser des questions ou sur **Give Feedback** (Envoyer des commentaires).

## Accéder aux ressources liées 

Dans l'Explorateur de serveurs, vous pouvez voir le compte de stockage par défaut et les éventuels comptes de stockage liés. Développez le compte de stockage par défaut pour afficher les conteneurs dans le compte de stockage. Le compte de stockage par défaut et le conteneur par défaut sont marqués. Vous pouvez aussi cliquer avec le bouton droit sur n'importe quel conteneur pour l'afficher.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Exécuter des requêtes Hive
[Apache Hive][apache.hive] est une infrastructure d'entrepôt de données basée sur Hadoop qui permet de synthétiser, d'interroger et d'analyser des données. HDInsight Tools pour Visual Studio prend en charge l'exécution de requêtes Hive à partir de Visual Studio. Pour plus d'informations sur Hive, consultez la rubrique [Utilisation de Hive avec HDInsight][hdinsight.hive].

###Afficher la table Hive hivesampletable
Tous les clusters HDInsight sont fournis avec un exemple de table Hive nommé  *hivesampletable*. Nous allons utiliser cette table pour vous montrer comment répertorier les tables Hive, afficher les schémas de table et examiner les lignes de la table Hive.



**Pour répertorier les tables Hive et afficher le schéma de la table Hive**

1.	Dans l'**Explorateur de serveurs**, développez successivement **Azure**, **HDInsight Clusters** (Clusters HDInsight), le cluster, **Hive Databases** (Bases de données Hive), **Default** (Par défaut), puis **hivesampletable** pour afficher le schéma de la table.
4.	Cliquez avec le bouton droit sur **hivesampletable**, puis cliquez sur **View Top 100 Rows** (Afficher les 100 premières lignes) pour afficher les lignes. Cela revient à exécuter la requête Hive suivante à l'aide du pilote ODBC Hive :

		SELECT * FROM hivesampletable LIMIT 100

	Vous pouvez personnaliser le nombre de lignes. 
 
	![HDinsight Hive Visual Studio schema query][6]

###Créer des tables Hive

Vous pouvez soit utiliser le GUI pour créer une table Hive, soit utiliser des requêtes Hive. Pour utiliser des requêtes Hive, consultez [Exécuter des requêtes Hive](#run.queries).

**Pour créer une table Hive**

1. Dans l'**Explorateur de serveurs**, développez successivement **Azure**, **HDInsight Clusters** (Clusters HDInsight), un cluster HDInsight, **Hive Databases** (Bases de données Hive), puis cliquez avec le bouton droit sur **default** (Par défaut) et cliquez sur **Create Table (Créer une table)**.
2. Configurez la table.
3. Cliquez sur **Create Table** (Créer une table) pour envoyer la tâche de création d'une table Hive.

	![hdinsight visual studio tools create hive table][7]

###<a name="run.queries"></a>Exécuter des requêtes Hive
Vous pouvez créer et exécuter des requêtes Hive de deux manières :

- Créer des requêtes ad hoc
- Créer une application Hive

**Pour créer/exécuter des requêtes ad hoc**

1. Dans l'**Explorateur de services**, développez **Azure**, puis **HDInsight Clusters** (Clusters HDInsight).
2. Cliquez avec le bouton droit sur le cluster dans lequel vous souhaitez exécuter la requête, puis cliquez sur **Write a Hive Query** (Écrire une requête Hive). 
3. Entrez des requêtes Hive.
4. Cliquez sur **Submit** (Envoyer) ou sur **Submit (Advanced)** (Envoyer [Avancé]). Avec l'envoi avancé, vous configurez le **Job Name** (Nom de la tâche), les **Arguments**, des **Additional Configurations** (Configurations supplémentaires) et le **Status Directory** (Répertoire d'état) pour le script :

	![hdinsight hadoop hive query][9]

	Une fois la tâche envoyée, la fenêtre **Hive Job Summary** (Résumé des tâches Hive) apparaît.

	![hdinsight hadoop hive query][8]
5. Utilisez le bouton **Refresh** (Actualiser) pour actualiser l'état jusqu'à ce qu'il passe à **Completed** (Terminé).
6. Cliquez sur les liens situés dans la partie inférieure pour afficher **Job Query** (Requête de tâche), **Job Output** (Sortie de la tâche) et **Job log** (Journal de la tâche).



**Pour créer/exécuter une solution Hive**

1. Dans le menu **FILE** (FICHIER), cliquez sur **New** (Nouveau), puis sur **Project** (Projet).
2. Sélectionnez **HDInsight** dans le volet gauche, sélectionnez **Hive Application** (Application Hive) dans le volet central, entrez les propriétés, puis cliquez sur **OK**.
3. Dans l'**Explorateur de solutions**, double-cliquez sur **Script.hql** pour l'ouvrir.

 
###Afficher les tâches Hive
Vous pouvez afficher la requête, la sortie et le journal de toutes les tâches Hive.

**Pour afficher les tâches Hive**

1. Dans l'**Explorateur de services**, développez **Azure**, puis **HDInsight**. 
2. Cliquez avec le bouton droit sur un cluster HDInsight, puis cliquez sur **View Hive Jobs** (Afficher les tâches Hive). La liste des tâches Hive exécutées sur le cluster s'affiche. 
3. Cliquez sur une tâche dans la liste des tâches pour la sélectionner, puis utilisez la fenêtre **Hive Job Summary** (Résumé des tâches Hive) pour ouvrir **Job Query** (Requête de tâche), **Job Output** (Sortie de la tâche) ou **Job Log** (Journal de la tâche).

## Étapes suivantes
Dans cet article, vous avez vu comment établir une connexion à des clusters HDInsight à partir de Visual Studio et comment exécuter des requêtes Hive. Pour plus d'informations, consultez les pages suivantes :

- [Utilisation de Hadoop Hive dans HDInsight][hdinsight.hive]
- [Prise en main de Hadoop dans HDInsight][hdinsight.get.started]
- [Envoi de tâches Hadoop dans HDInsight][hdinsight.submit.jobs]
- [Analyse des données Twitter avec Hadoop dans HDInsight][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Installation]: #installation
[Se connecter à un abonnement Azure]: #connect-to-your-azure-subscription
[Accéder aux ressources liées]: #navigate-the-linked-resources
[Exécuter des requêtes Hive]: #run-hive-queries
[Étapes suivantes]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/


[apache.hive]: http://hive.apache.org
<!--HONumber=42-->
