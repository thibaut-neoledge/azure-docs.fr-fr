<properties
	pageTitle="Utilisation des outils Hadoop Visual Studio pour HDInsight | Microsoft Azure"
	description="Découvrez comment installer et utiliser les outils de Hadoop Visual Studio pour HDInsight pour vous connecter à un cluster Hadoop et exécuter une requête Hive."
	services="HDInsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="07/28/2015"
	ms.author="jgao"/>

# Prise en main des outils Hadoop de Visual Studio pour HDInsight pour exécuter une requête Hive

Découvrez comment utiliser HDInsight Tools pour Visual Studio pour vous connecter à des clusters HDInsight et envoyer des requêtes Hive. Pour plus d’informations sur l’utilisation de HDInsight, consultez les rubriques [Présentation de HDInsight][hdinsight.introduction] et [Prise en main de HDInsight][hdinsight.get.started]. Pour plus d’informations sur la connexion au cluster Storm, consultez [Développement de topologies C# pour Apache Storm sur HDInsight à l’aide de Visual Studio][hdinsight.storm.visual.studio.tools].

>[AZURE.NOTE]La version la plus récente introduit quelques nouvelles fonctionnalités, comme la prise en charge de l’éditeur Hive, la validation locale des scripts Hive et l’accès aux journaux YARN.


## Composants requis

Pour suivre ce didacticiel et utiliser les outils Hadoop dans Visual Studio, vous avez besoin des éléments suivants :

- un cluster Azure HDInsight : un cluster Linux ou Windows fonctionne avec les étapes décrites dans ce document. Consultez l’une des rubriques suivantes pour plus d’informations sur la création d’un cluster :

	- [Prise en main de HDInsight sur Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
	- [Prise en main de HDInsight sur Windows](hdinsight-hadoop-tutorial-get-started-windows.md)

- une station de travail avec les logiciels suivants :

	- Windows 8.1, Windows 8 ou Windows 7
	- Visual Studio (l'une des versions suivantes) :
		- Visual Studio 2012 Professional/Premium/Ultimate avec [mise à jour 4](http://www.microsoft.com/download/details.aspx?id=39305)
		- Visual Studio 2013 Community/Professional/Premium/Ultimate avec [mise à jour 4](https://www.microsoft.com/download/details.aspx?id=44921)
		- Visual Studio 2015 (Community/Enterprise)

	>[AZURE.NOTE]Actuellement, HDInsight Tools pour Visual Studio n’est fourni qu’avec la version anglaise.


## Installer les outils HDInsight pour Visual Studio

HDInsight Tools pour Visual Studio est packagé avec le Kit de développement logiciel (SDK) Microsoft Azure pour .NET version 2.5.1 ou ultérieure. Il peut être installé à l’aide de [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386). Vous devez choisir celui qui correspond à votre version de Visual Studio. Le package d’outils Hadoop installe également le pilote Microsoft Hive ODBC 32 bits et 64 bits.

![Outils Hadoop : Web Platform Installer de HDinsight Tools pour Visual Studio.][1]


>[AZURE.NOTE]Si vous disposez de Visual Studio 2015 ou 2012 et que la version 2.5 du Kit de développement logiciel (SDK) Windows Azure est installée, vous devez supprimer manuellement la version antérieure avant d’installer la version la plus récente. Visual Studio 2013 prend en charge la mise à jour directe.

## Se connecter aux abonnements Azure
Avec HDInsight Tools pour Visual Studio, vous pouvez vous connecter à vos clusters HDInsight, effectuer des opérations de gestion de base et exécuter des requêtes Hive.

>[AZURE.NOTE]Pour plus d’informations sur la connexion à l'émulateur HDInsight, consultez la rubrique [Prise en main de l'émulateur HDInsight](../hdinsight-get-started-emulator.md/#vstools).

>[AZURE.NOTE]Pour plus d'informations sur la connexion à un cluster Hadoop générique (aperçu), consultez [Écriture et soumission de requêtes Hive à l’aide de Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).


**Pour vous connecter à votre abonnement Azure**

1.	Ouvrez Visual Studio.
2.	Dans le menu **Affichage**, cliquez sur **Explorateur de serveurs** pour ouvrir la fenêtre du même nom.
3.	Développez **Azure**, puis **HDInsight**.

	>[AZURE.NOTE]Notez que la fenêtre **Liste des tâches HDInsight** doit s’ouvrir. Si vous ne le voyez pas, cliquez sur **Autres fenêtres** à partir du menu **Affichage**, puis cliquez sur **Fenêtre de liste des tâches HDInsight**.  
4.	Entrez les informations d’identification de votre abonnement Azure, puis cliquez sur **Connexion**. Cette étape n’est nécessaire que si vous ne vous êtes jamais connecté à l’abonnement Azure à partir de Visual Studio sur cette station de travail.
5.	Dans l’explorateur de serveurs, vous verrez une liste des clusters HDInsight existants. Si vous ne voyez aucun cluster, vous pouvez en configurer un à l’aide du portail Aperçu d’Azure, d’Azure PowerShell ou du kit de développement logiciel (SDK) HDInsight. Pour plus d’informations, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision].

	![Outils Hadoop : liste de clusters de l’explorateur de serveurs de HDInsight Tools pour Visual Studio][5]
6.	Développez un cluster HDInsight. Vous verrez les **bases de données Hive**, un compte de stockage par défaut, les comptes de stockage et le **journal Hadoop Service**. Vous pouvez développer davantage les entités.

Une fois connecté à votre abonnement Azure, vous serez en mesure d’effectuer les opérations suivantes :

**Pour vous connecter au Portail de gestion à partir de Visual Studio**

- Dans l’Explorateur de serveurs, développez **Azure** > **HDInsight**, cliquez avec le bouton droit sur un cluster HDInsight, puis cliquez sur **Gérer le cluster dans le portail Azure**.

**Pour poser des questions et envoyer des commentaires à partir de Visual Studio**

- Dans le menu **Outils**, cliquez sur **HDInsight**, puis sur **Forum MSDN** pour poser des questions ou sur **Envoyer des commentaires**.

## Accéder aux ressources liées

Dans l’Explorateur de serveurs, vous pouvez voir le compte de stockage par défaut et les éventuels comptes de stockage liés. Développez le compte de stockage par défaut pour afficher les conteneurs dans le compte de stockage. Le compte de stockage par défaut et le conteneur par défaut sont marqués. Vous pouvez aussi cliquer avec le bouton droit sur n’importe quel conteneur pour afficher son contenu.

![Liste de clusters de l’explorateur de serveurs de HDInsight Tools pour Visual Studio][2]

## Exécution d'une tâche Hive
[Apache Hive][apache.hive] est une infrastructure d’entrepôt de données basée sur Hadoop qui permet de synthétiser, d’interroger et d’analyser des données. HDInsight Tools pour Visual Studio prend en charge l’exécution de requêtes Hive à partir de Visual Studio. Pour plus d'informations sur Hive, consultez l'article [Utilisation de Hive avec HDInsight][hdinsight.hive].

Le test du script Hive sur un cluster HDInsight est chronophage. Il peut durer plusieurs minutes. HDInsight Tools pour Visual Studio est capable de valider le script Hive localement, sans connexion à un cluster activé.

HDInsight Tools pour Visual Studio permet également aux utilisateurs de consulter le contenu de la tâche Hive en collectant et en exposant les journaux YARN de tâches Hive données.

### Afficher la table Hive **hivesampletable**
Les clusters HDInsight sont fournis avec un exemple de table Hive qui porte le nom *hivesampletable*. Nous allons utiliser ce tableau pour vous montrer comment répertorier des tables Hive, afficher des schémas de table et répertorier les lignes de la table Hive.



**Pour répertorier les tables Hive et afficher le schéma de la table Hive**

1.	À partir de l’**Explorateur de serveurs**, développez **Azure** > **HDInsight** > le cluster de votre choix > **Bases de données Hive** > **Par défaut** > **hivesampletable** pour afficher le schéma de table.
4.	Cliquez avec le bouton droit sur **hivesampletable**, puis cliquez sur **Afficher les 100 premières lignes** pour répertorier les lignes. Cela revient à exécuter la requête Hive suivante à l’aide du pilote ODBC Hive :

		SELECT * FROM hivesampletable LIMIT 100

	Vous pouvez personnaliser le nombre de lignes.

	![Outils Hadoop : requête de schéma Hive HDinsight Visual Studio][6]

### Créer des tables Hive

Vous pouvez utiliser des requêtes Hive ou utiliser la GUI pour créer une table Hive. Pour plus d’informations relatives à l’utilisation de requêtes Hive, consultez [Exécution de requêtes Hive](#run.queries).

**Pour créer une table Hive**

1. Dans l’**Explorateur de serveurs**, développez successivement **Azure** > **Clusters HDInsight** un cluster HDInsight > **Bases de données Hive**, puis cliquez avec le bouton droit sur **Par défaut** et cliquez sur **Créer une table**.
2. Configurez la table.
3. Cliquez sur **Créer une table** pour envoyer la tâche de création d’une table Hive.

	![Outils Hadoop : outils HDInsight Visual Studio pour la création d’une table Hive][7]

### <a name="run.queries"></a>Validation et exécution de requêtes Hive
Vous pouvez créer et exécuter des requêtes Hive de deux manières :

- Création de requêtes ad hoc
- Création d’une application Hive

**Pour créer, valider et exécuter des requêtes ad hoc**

1. Dans l’**Explorateur de serveurs**, développez **Azure**, puis **Clusters HDInsight**.
2. Cliquez avec le bouton droit sur le cluster dans lequel vous souhaitez exécuter la requête, puis cliquez sur **Écrire une requête Hive**.
3. Entrez les requêtes Hive. Notez que l’éditeur Hive prend en charge IntelliSense. Les outils HDInsight pour Visual Studio prennent en charge le chargement des métadonnées distantes pendant la modification d’un script Hive. Par exemple, lorsque vous tapez « SELECT * FROM », IntelliSense répertorie tous les noms de table suggérés. Lorsqu’un nom de table est spécifié, les noms de colonne sont répertoriés par IntelliSense. L’outil prend en charge quasiment toutes les instructions DML, sous-requêtes et fonctions définies par l’utilisateur intégrées de Hive.

	![Outils Hadoop : IntelliSense dans Visual Studio Tools pour HDInsight][13]

	![Outils Hadoop : IntelliSense dans Visual Studio Tools pour HDInsight][14]

	> [AZURE.NOTE]Seules les métadonnées des clusters sélectionnés dans la barre d'outils HDInsight sont proposées.
4. (Facultatif) : cliquez sur **Valider le script** pour vérifier l’absence d’erreurs de syntaxe dans le script.

	![Outils Hadoop : validation locale des outils HDInsight pour Visual Studio.][10]

4. Cliquez sur **Envoyer** ou sur **Envoyer (Avancé)**. Avec l’option d’envoi avancé, vous allez configurer les éléments **Nom de la tâche**, **Arguments**, **Configurations supplémentaires** et **Répertoire d’états** pour le script :

	![requête hdinsight hadoop hive][9]

	Après avoir soumis la tâche, la fenêtre **Récapitulatif de la tâche Hive** s’affiche.

	![Résumé d'une requête HDInsight Hadoop Hive][8]
5. Utilisez le bouton **Actualiser** pour mettre à jour l’état jusqu’à ce qu’il passe à **Terminé**.
6. Cliquez sur les liens situés dans la partie inférieure pour afficher les éléments suivants : la **Requête de tâche**, la **Sortie de la tâche**, le **Journal de la tâche** ou le **Journal Yarn**.



**Pour créer et exécuter une solution Hive**

1. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
2. Sélectionnez **HDInsight** dans le volet gauche, sélectionnez **Application Hive** dans le volet central, entrez les propriétés, puis cliquez sur **OK**.

	![Outils Hadoop : nouveau projet Hive dans HDInsight Visual Studio][11]
3. Dans l’**Explorateur de solutions**, double-cliquez sur **Script.hql** pour l’ouvrir.
4. Pour valider le script Hive, vous pouvez cliquer sur le bouton **Valider le script**, ou cliquer avec le bouton droit sur le script dans l’éditeur Hive, puis sur **Valider le script** dans le menu contextuel.


### Afficher les tâches Hive
Vous pouvez afficher les requêtes, la sortie, le journal et le journal Yarn des tâches Hive. Pour plus d'informations, consultez la capture d’écran précédente.

La version la plus récente de l’outil permet de consulter le contenu de vos tâches Hive en collectant et en exposant les journaux YARN. Le journal YARN peut vous aider à examiner les problèmes de performances. Pour plus d’informations sur la collection des journaux YARN par HDInsight, consultez [Accès par programme aux journaux des applications HDInsight][hdinsight.access.application.logs].

**Pour afficher les tâches Hive**

1. Dans l’**Explorateur de serveurs**, développez **Azure**, puis **HDInsight**.
2. Cliquez avec le bouton droit sur un cluster HDInsight, puis cliquez sur **Afficher les tâches Hive**. Vous verrez une liste des tâches Hive exécutées sur le cluster.
3. Cliquez sur une tâche dans la liste des tâches pour la sélectionner, puis utilisez la fenêtre **Résumé des tâches Hive** pour ouvrir **Requête de tâche**, **Sortie de la tâche**, **Journal de la tâche** ou **Journal Yarn**.

	![Outils Hadoop : affichage des tâches Hive dans HDInsight Visual Studio][12]

### Graphique de performances des travaux Hive sur Tez

Visual Studio Tools pour HDInsight prend en charge l’affichage des graphiques de performances pour les travaux Hive exécutés par le moteur d’exécution Tez. Pour plus d’informations sur l’activation de Tez, consultez [Utiliser Hive dans HDInsight][hdinsight.hive]. Après avoir soumis une tâche Hive dans Visual Studio, ce dernier affiche le graphique lorsque cette tâche est terminée. Vous devrez peut-être cliquer sur le bouton **Actualiser** pour obtenir le dernier état du travail.

> [AZURE.NOTE]Cette fonctionnalité est uniquement disponible pour les clusters HDInsight dont la version est supérieure à la version 3.2.4.593 et elle ne peut fonctionner que pour les travaux terminés. Elle fonctionne pour les clusters basés sur Windows et Linux.

![hadoop hive tez graphique de performances](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png)

## Exécuter des scripts Pig

Les outils HDInsight pour Visual Studio prennent en charge la création et la soumission de scripts Pig aux clusters HDInsight. Les utilisateurs peuvent créer un projet Pig à partir d’un modèle, puis soumettre le script à des clusters HDInsight.

## Étapes suivantes
Dans cet article, vous avez appris à établir une connexion à des clusters HDInsight à partir de Visual Studio, à l’aide du package d’outils Hadoop, et à exécuter des requêtes Hive. Pour plus d'informations, consultez les pages suivantes :

- [Utilisation de Hadoop Hive dans HDInsight][hdinsight.hive]
- [Prise en main de Hadoop dans HDInsight][hdinsight.get.started]
- [Envoi de tâches Hadoop dans HDInsight][hdinsight.submit.jobs]
- [Analyse des données Twitter avec Hadoop dans HDInsight][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Installation]: #installation
[Connect to your Azure subscription]: #connect-to-your-azure-subscription
[Navigate the linked resources]: #navigate-the-linked-resources
[Run Hive queries]: #run-hive-queries
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight.introduction]: ../hdinsight-introduction.md
[hdinsight.get.started]: ../hdinsight-get-started.md
[hdinsight.hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight.submit.jobs]: ../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: ../hdinsight/hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: ../hdinsight/hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org

<!---HONumber=August15_HO9-->