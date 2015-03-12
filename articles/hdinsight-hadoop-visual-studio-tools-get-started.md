<properties 
	pageTitle="Prise en main de HDInsight Tools pour Visual Studio | Azure" 
	description="Découvrez comment installer et utiliser HDInsight Tools pour Visual Studio pour vous connecter à HDInsight et exécuter des requêtes Hive." 
	services="HDInsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="2/17/2015" 
	ms.author="jgao"/>

# Prise en main de HDInsight Hadoop Tools pour Visual Studio

Découvrez comment utiliser HDInsight Tools pour Visual Studio pour vous connecter à des clusters HDInsight et envoyer des requêtes Hive. Pour plus d'informations sur l'utilisation de HDInsight, consultez les rubriques [Présentation de HDInsight][hdinsight.introduction] et [Prise en main de HDInsight][hdinsight.get.started]. Pour plus d'informations sur la connexion au cluster Storm, consultez [Développement de topologies C# pour Apache Storm sur HDInsight à l'aide de Visual Studio][hdinsight.storm.visual.studio.tools]. 

>[WACOM.NOTE] Toutes les fonctionnalités des outils HDInsight disponibles pour les clusters Windows fonctionnent également dans les clusters Linux, à l'exception de quelques fonctionnalités non prises en charge actuellement dans Humboldt, comme l'affichage des journaux YARN d'une tâche Hive donnée. 

>La version la plus récente a introduit quelques nouvelles fonctionnalités, comme la prise en charge d'Intellisense par l'éditeur Hive, la validation locale des scripts Hive et l'accès aux journaux Yarn.

##Dans cet article
+ [Installation] 
+ [Se connecter à un abonnement Azure]
+ [Accéder aux ressources liées]
+ [Exécuter des requêtes Hive]
+ [Étapes suivantes]


##Conditions préalables

- Station de travail avec :

	- Windows 7, Windows 8 ou Windows 8,1.
	- Visual Studio avec les versions suivantes :
		- Visual Studio 2012 Professional/Premium/Ultimate avec [mise à jour 4](http://www.microsoft.com/fr-fr/download/details.aspx?id=39305)
		- Visual Studio 2013 Community/Professional/Premium/Ultimate avec [mise à jour 4](https://www.microsoft.com/fr-fr/download/details.aspx?id=44921)
		- Visual Studio 2015 Preview.

	>[WACOM.NOTE] À l'heure actuelle, les outils sont uniquement disponibles en anglais. 


## Installation

Les outils HDInsight pour Visual Studio peuvent être installés à l'aide de [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386). Vous pouvez interroger les outils à l'aide du mot-clé " hdinsight ". Il existe un seul package pour chaque version de Visual Studio. Vous devez choisir celui qui correspond à votre version de Visual Studio.  Le package installe également le pilote Microsoft Hive ODBC 32 bits et 64 bits.

![HDinsight Tools for Visual Studio Web Platform installer][1]


>[WACOM.NOTE] Si vous disposez de Visual Studio 2012 ou 2015 et que l'ancienne version du Kit de développement logiciel (SDK) Windows Azure est installée, vous devez supprimer manuellement la version antérieure avant d'installer la version la plus récente. Visual Studio 2013 prend en charge la mise à jour directe.

## Se connecter à un abonnement Azure
Avec HDInsight Tools pour Visual Studio, vous pouvez vous connecter à vos clusters HDInsight, effectuer des opérations de gestion de base et exécuter des requêtes Hive.

**Se connecter à un abonnement Azure**

1.	Exécutez Visual Studio.
2.	Dans le menu **Affichage**, cliquez sur **Explorateur de serveurs** pour ouvrir la fenêtre du même nom.
3.	Développez **Azure**, puis **HDInsight**. 

	>[WACOM.NOTE]Notez que la fenêtre **HDInsight Task List** (Liste des tâches HDInsight) s'ouvre. Si vous ne la voyez pas, cliquez sur **Other Windows** (Autres fenêtres) dans le menu **VIEW** (Affichage), puis sur **HDInsight Task List Window** (Fenêtre Liste des tâches HDInsight) pour l'ouvrir.  
4.	Entrez les informations d'identification de votre abonnement Azure, puis cliquez sur **Sign In** (Connexion). Cette étape n'est nécessaire que si vous ne vous êtes pas encore connecté à l'abonnement Azure à partir de Visual Studio sur cette station de travail.
5.	L'Explorateur de serveurs dresse la liste des clusters HDInsight existants. Si vous ne possédez aucun cluster, vous pouvez en approvisionner un dans le Portail de gestion, avec Azure PowerShell ou à l'aide du SDK HDInsight.  Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision].

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	Développez un cluster HDInsight. Vous verrez alors les **Bases de données Hive**, le compte de stockage par défaut, les comptes de stockage liés et le **journal du service Hadoop**. Vous pouvez développer davantage les entités. 

Une fois connecté à votre abonnement Azure, vous pouvez effectuer les tâches suivantes :

**Pour vous connecter au Portail de gestion à partir de Visual Studio**

- Dans l'Explorateur de serveurs, développez **Azure**, **HDInsight**, cliquez avec le bouton droit sur un cluster HDInsight, puis cliquez sur **Gérer le cluster dans le portail Azure**.

**Pour poser des questions et envoyer des commentaires à partir de Visual Studio**

- Dans le menu **TOOLS** (OUTILS), cliquez sur **HDInsight**, puis sur **MSDN Forum** (Forum MSDN) pour poser des questions ou sur **Give Feedback** (Envoyer des commentaires).

## Accéder aux ressources liées 

Dans l'Explorateur de serveurs, vous pouvez voir le compte de stockage par défaut et les éventuels comptes de stockage liés. Développez le compte de stockage par défaut pour afficher les conteneurs dans le compte de stockage. Le compte de stockage par défaut et le conteneur par défaut sont marqués. Vous pouvez aussi cliquer avec le bouton droit sur n'importe quel conteneur pour l'afficher.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Exécuter des requêtes Hive
[Apache Hive][apache.hive] est une infrastructure d'entrepôt de données basée sur Hadoop qui permet de synthétiser, d'interroger et d'analyser des données. HDInsight Tools pour Visual Studio prend en charge l'exécution de requêtes Hive à partir de Visual Studio. Pour plus d'informations sur Hive, consultez la rubrique [Utilisation de Hive avec HDInsight][hdinsight.hive].

Le test du script Hive sur un cluster HDInsight est chronophage. Il peut durer plusieurs minutes.  Les outils de HDInsight pour Visual Studio sont capables de valider la grammaire du script Hive localement, sans connexion à un cluster activé.

Les outils HDInsight pour Visual Studio permettent également aux utilisateurs de consulter le contenu de la tâche Hive en collectant et en exposant les journaux Yarn de tâches Hive données.

###Afficher la table Hive hivesampletable
Tous les clusters HDInsight sont fournis avec un exemple de table Hive nommé  *hivesampletable*. Nous allons utiliser cette table pour vous montrer comment répertorier les tables Hive, afficher les schémas de table et examiner les lignes de la table Hive.



**Pour répertorier les tables Hive et afficher le schéma de la table Hive**

1.	Dans l'**Explorateur de serveurs**, développez successivement **Azure**, **HDInsight** , le cluster, **Bases de données Hive**, **Par défaut**, puis **hivesampletable** pour afficher le schéma de la table.
4.	Cliquez avec le bouton droit sur **hivesampletable**, puis cliquez sur **View Top 100 Rows** (Afficher les 100 premières lignes) pour afficher les lignes. Cela revient à exécuter la requête Hive suivante à l'aide du pilote ODBC Hive :

		SELECT * FROM hivesampletable LIMIT 100

	Vous pouvez personnaliser le nombre de lignes. 
 
	![HDinsight Hive Visual Studio schema query][6]

###Créer des tables Hive

Vous pouvez soit utiliser le GUI pour créer une table Hive, soit utiliser des requêtes Hive. Pour utiliser des requêtes Hive, consultez [Exécuter des requêtes Hive](#run.queries).

**Pour créer une table Hive**

1. Dans l'**Explorateur de serveurs**, développez successivement **Azure**, **Clusters HDInsight**, un cluster HDInsight, **Bases de données Hive**, puis cliquez avec le bouton droit sur **Par défaut** et cliquez sur **Créer une table**.
2. Configurez la table.
3. Cliquez sur **Créer une table** pour envoyer la tâche de création d'une table Hive.

	![hdinsight visual studio tools create hive table][7]

###<a name="run.queries"></a>Validation et exécution de requêtes Hive
Vous pouvez créer et exécuter des requêtes Hive de deux manières :

- Création de requêtes ad hoc
- Créer une application Hive

**Pour créer, valider et exécuter des requêtes ad hoc**

1. Dans l'**Explorateur de services**, développez **Azure**, puis **HDInsight Clusters** (Clusters HDInsight).
2. Cliquez avec le bouton droit sur le cluster dans lequel vous souhaitez exécuter la requête, puis cliquez sur **Write a Hive Query** (Écrire une requête Hive). 
3. Entrez des requêtes Hive. Notez que l'éditeur Hive prend en charge Intellisense.
4. (facultatif) Cliquez sur **Valider le script** pour vérifier l'absence d'erreurs de syntaxe dans le script.

	![hdinsight tools for Visual Studio local validation][10]

4. Cliquez sur **Submit** (Envoyer) ou sur **Submit (Advanced)** (Envoyer [Avancé]). Avec l'envoi avancé, vous configurez le **Job Name** (Nom de la tâche), les **Arguments**, des **Additional Configurations** (Configurations supplémentaires) et le **Status Directory** (Répertoire d'état) pour le script :

	![hdinsight hadoop hive query][9]

	Une fois la tâche envoyée, la fenêtre **Hive Job Summary** (Résumé des tâches Hive) apparaît.

	![hdinsight hadoop hive query][8]
5. Utilisez le bouton **Refresh** (Actualiser) pour actualiser l'état jusqu'à ce qu'il passe à **Completed** (Terminé).
6. Cliquez sur les liens situés dans la partie inférieure pour afficher la **Requête de tâche**, la **Sortie de la tâche**, le **Journal de la tâche** ou le **Journal Yarn**.



**Pour créer/exécuter une solution Hive**

1. Dans le menu **FILE** (FICHIER), cliquez sur **New** (Nouveau), puis sur **Project** (Projet).
2. Sélectionnez **HDInsight** dans le volet gauche, sélectionnez **Hive Application** (Application Hive) dans le volet central, entrez les propriétés, puis cliquez sur **OK**.
3. Dans l'**Explorateur de solutions**, double-cliquez sur **Script.hql** pour l'ouvrir. 
4. Pour valider le script Hive, vous pouvez cliquer sur le bouton Valider le script, ou cliquer avec le bouton droit sur le script dans l'éditeur Hive, puis sur " Valider le script " dans le menu contextuel.

 
###Afficher les tâches Hive
Vous pouvez afficher la requête, la sortie, le journal et le journal Yarn des tâches Hive.  Consultez la capture d'écran précédente.

La version la plus récente de l'outil permet de consulter le contenu de vos tâches Hive en collectant et en exposant les journaux Yarn. Le journal Yarn peut vous aider à examiner les problèmes de performances. Pour plus d'informations sur la collection des journaux YARN par HDInsight, consultez [Accès par programme aux journaux des applications HDInsight][hdinsight.access.application.logs].

**Pour afficher les tâches Hive**

1. Dans l'**Explorateur de services**, développez **Azure**, puis **HDInsight**. 
2. Cliquez avec le bouton droit sur un cluster HDInsight, puis cliquez sur **View Hive Jobs** (Afficher les tâches Hive). La liste des tâches Hive exécutées sur le cluster s'affiche. 
3. Cliquez sur une tâche dans la liste des tâches pour la sélectionner, puis utilisez la fenêtre **Résumé des tâches Hive** pour ouvrir **Requête de tâche**, **Sortie de la tâche**, **Journal de la tâche** ou **Journal Yarn**.

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
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/
[hdinsight.storm.visual.studio.tools]: ../hdinsight-storm-develop-csharp-visual-studio-topology/
[hdinsight.access.application.logs]: ../hdinsight-hadoop-access-yarn-app-logs/

[apache.hive]: http://hive.apache.org
<!--HONumber=45--> 
