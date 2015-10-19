<properties 
	pageTitle="Utiliser des outils de BI avec Apache Spark sur HDInsight | Microsoft Azure" 
	description="Des instructions pas à pas expliquent comment utiliser des blocs-notes avec Apache Spark pour créer des schémas basés sur les données brutes, comment les enregistrer dans des tables Hive, et comment utiliser des outils décisionnels dans la table Hive pour analyser les données." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2015" 
	ms.author="nitinme"/>


# Utiliser des outils décisionnels avec Apache Spark sur Azure HDInsight

Découvrez comment utiliser Apache Spark dans Azure HDInsight pour effectuer l’opération suivante :

* Prendre un exemple de données brutes et l’enregistrer dans une table Hive
* Utilisez des outils décisionnels tels que Power BI et Tableau pour analyser et visualiser les données.

**Configuration requise :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark. Pour obtenir les instructions, consultez [Approvisionner les clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Un ordinateur équipé du pilote ODBC de Microsoft Spark (permet à Spark sur HDInsight de fonctionner avec Tableau). Vous pouvez l’installer en cliquant [ici](http://go.microsoft.com/fwlink/?LinkId=616229).
- Des outils décisionnels tels que [Power BI](http://www.powerbi.com/) ou [Tableau Desktop](http://www.tableau.com/products/desktop). Vous pouvez obtenir un abonnement gratuit à la version d’évaluation de Power BI à l’adresse [http://www.powerbi.com/](http://www.powerbi.com/).

##<a name="hivetable"></a>Enregistrer des données brutes dans une table Hive

Dans cette section, nous allons utiliser le bloc-notes [Jupyter](https://jupyter.org) associé à un cluster Apache Spark dans HDInsight pour exécuter les travaux traitant vos exemples de données brutes et les enregistrer dans une table Hive. L’exemple de données est un fichier .csv (hvac.csv), qui est disponible par défaut sur tous les clusters.

Une fois vos données enregistrées dans une table Hive, nous allons nous connecter, dans la prochaine section, à la table Hive à l’aide d’outils décisionnels comme Power BI et Tableau.

1. Dans le tableau d’accueil du [portail Azure en version préliminaire](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous avez épinglé ce dernier au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   

2. Dans le panneau du cluster Spark, cliquez sur **Liens rapides**, puis dans le panneau **Tableau de bord du cluster**, cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

	> [AZURE.NOTE]Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez __CLUSTERNAME__ par le nom de votre cluster.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis sur **Python 2**.

	![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.CreateNotebook.png "Créer un bloc-notes Jupyter")

3. Un nouveau bloc-notes est créé et ouvert sous le nom Untitled.pynb. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial.

	![Fournir un nom pour le bloc-notes](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.Notebook.Name.png "Fournir un nom pour le bloc-notes")

4. Importez les modules requis et créez les contextes Spark et Hive. Collez l’extrait suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.

		from pyspark import SparkContext
		from pyspark.sql import *
		from pyspark.sql import HiveContext

		# Create Spark and Hive contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		hiveCtx = HiveContext(sc)

	À chaque exécution d’une tâche dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s'affiche également en regard du texte **Python 2** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

	 ![État d’un travail de bloc-notes Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Job.Status.png "État d’un travail de bloc-notes Jupyter")

4. Chargez un exemple de données dans une table temporaire. Lorsque vous approvisionnez un cluster Spark dans HDInsight, le fichier exemple de données, **hvac.csv**, est copié vers le compte de stockage associé dans **\\HdiSamples\\SensorSampleData\\hvac**.

	Dans une cellule vide, collez l’extrait suivant puis appuyez sur **MAJ + ENTRÉE**. Cet extrait enregistre les données dans une table Hive appelée **hvac**.


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date")
		hvac = hvacParts.map(lambda p: {"Date": str(p[0]), "Time": str(p[1]), "TargetTemp": int(p[2]), "ActualTemp": int(p[3]), "BuildingID": int(p[6])})
		
		# Infer the schema and create a table		
		hvacTable = hiveCtx.inferSchema(hvac)
		hvacTable.registerAsTable("hvactemptable")
		hvacTable.saveAsTable("hvac")

5. Vérifiez que la table a bien été créée. Dans une cellule vide du bloc-notes, collez l’extrait suivant puis appuyez sur **MAJ + ENTRÉE**.

		hiveCtx.sql("SHOW TABLES").show()

	Un résultat semblable à ce qui suit doit s’afficher :

		tableName       isTemporary
		hvactemptable   true       
		hivesampletable false      
		hvac            false

	Seules les tables dont la colonne **isTemporary** est définie sur False sont des tables Hive qui sont stockées dans le metastore et qui sont accessibles à partir des outils décisionnels. Dans ce didacticiel, nous allons nous connecter à la table **hvac** que nous venons de créer.

6. Vérifiez que la table contient les données prévues. Dans une cellule vide du bloc-notes, collez l’extrait suivant puis appuyez sur **MAJ + ENTRÉE**.

		hiveCtx.sql("SELECT * FROM hvac LIMIT 10").show()
	
7. À présent, vous pouvez quitter le bloc-notes en redémarrant le noyau. Dans la barre de menus supérieure, cliquez successivement sur **Kernel**, **Restart** et **Restart** à nouveau lorsque vous y êtes invité.

	![Redémarrer le noyau Jupyter](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Restart.Kernel.png "Redémarrer le noyau Jupyter")

##<a name="powerbi"></a>Utilisation de Power BI pour analyser les données de la table Hive

Une fois que vous avez enregistré les données dans une table Hive, vous pouvez utiliser Power BI pour vous connecter aux données et les visualiser afin de créer des rapports, des tableaux de bord, etc.

1. Connectez-vous à [Power BI](http://www.powerbi.com/).

2. Dans l’écran d’accueil, cliquez sur **Bases de données et autres**.

	![Injecter des données dans Power BI](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Get.Data.png "Injecter des données dans Power BI")

3. Dans l’écran suivant, cliquez sur **Spark**, puis sur **Se connecter**.

4. Dans la page Spark sur Azure HDInsight, indiquez les valeurs pour vous connecter à votre cluster Spark, puis cliquez sur **Se connecter**.

	![Se connecter à un cluster Spark sur HDInsight](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Connect.Spark.png "Se connecter à un cluster Spark sur HDInsight")

	Une fois la connexion établie, Power BI commence à importer les données du cluster Spark sur HDInsight.

5. Power BI importe les données et affiche le nouveau tableau de bord. Un nouveau jeu de données est également ajouté sous l’en-tête **Jeux de données**. Dans le tableau de bord, cliquez sur la vignette Spark pour ouvrir une feuille de calcul afin de visualiser les données.

	![Vignette Spark dans le tableau de bord Power BI](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Tile.png "Vignette Spark dans le tableau de bord Power BI")

6. Notez que la liste **Champs** sur la droite répertorie la table **hvac** créée précédemment. Développez la table pour en afficher les champs, comme vous les avez définis précédemment dans le bloc-notes.

	  ![Répertorier les tables Hive](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Display.Tables.png "Répertorier les tables Hive")

7. Générez une visualisation pour afficher l’écart entre la température cible et la température réelle de chaque bâtiment. Pour ce faire, effectuez un glisser-déplacer du champ **BuildingID** situé sous **Axe** et des champs **ActualTemp**/**TargetTemp** situés sous **Valeur**.

	![Créer des visualisations](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.1.png "Créer des visualisations")

	Sélectionnez également **Carte** (affiché en rouge) pour visualiser vos données.

8. Par défaut, la visualisation affiche la somme des valeurs des champs **ActualTemp** et **TargetTemp**. Pour les deux champs, sélectionnez **Moyenne** dans la liste déroulante afin d’obtenir la moyenne des températures réelles et cibles des deux bâtiments.

	![Créer des visualisations](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.2.png "Créer des visualisations")

9. La visualisation des données doit être semblable à ce qui suit. Déplacez le curseur sur la visualisation pour obtenir des info-bulles contenant des données pertinentes.

	![Créer des visualisations](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.3.png "Créer des visualisations")

10. Dans le menu supérieur, cliquez sur **Enregistrer** et indiquez un nom de rapport. Vous pouvez également épingler le visuel. Si vous épinglez une visualisation, elle est stockée sur votre tableau de bord pour que vous puissiez suivre les dernières valeurs en un clin d’œil.

	Vous pouvez ajouter autant de visualisations que vous le souhaitez pour un même jeu de données et les épingler au tableau de bord pour obtenir un instantané de vos données. En outre, les clusters Spark sur HDInsight sont connectés directement à Power BI. En d’autres termes, Power BI dispose toujours des données les plus récentes de votre cluster. Vous n’avez donc pas besoin de planifier des actualisations du jeu de données.

##<a name="tableau"></a>Utiliser Tableau Desktop pour analyser les données de la table Hive
	
1. Lancez Tableau Desktop. Dans le volet gauche, dans la liste de serveurs auxquels se connecter, cliquez sur **Spark SQL**.

2. Dans la boîte de dialogue de connexion à Spark SQL, entrez les valeurs indiquées ci-dessous, puis cliquez sur **OK**.

	![Se connecter à un cluster Spark](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Connect.png "Se connecter à un cluster Spark")

	La liste déroulante d’authentification contient l’option **Windows** **Azure HDInsight Service** uniquement si vous avez installé le [Pilote ODBC de Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) sur l’ordinateur.

3. Dans l’écran suivant, dans la liste déroulante **Schema**, cliquez sur l’icône **Find**, puis sur **default**.

	![Rechercher un schéma](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Schema.png "Rechercher un schéma")

4. Pour le champ **Table**, cliquez de nouveau sur l’icône **Find** pour dresser la liste de toutes les tables Hive disponibles dans le cluster. La table **hvac** que vous avez créée précédemment à l’aide du bloc-notes doit être indiquée.

	![Rechercher des tables](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Table.png "Rechercher des tables")

5. Effectuez un glisser-déplacer de la table vers la zone supérieure située à droite. Tableau importe les données et affiche le schéma mis en exergue par l’encadré rouge.

	![Ajouter des tables à Tableau](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Table.png "Ajouter des tables à Tableau")

6. Cliquez sur l’onglet **Sheet1** en bas à gauche. Réalisez une visualisation qui affiche la moyenne des températures cibles et réelles de tous les bâtiments à chaque date. Faites glisser **Date** et **Building ID** vers **Columns**, et **Actual Temp**/**Target Temp** vers **Rows**. Sous **Marks**, sélectionnez **Area** pour utiliser une visualisation par carte des zones.

	 ![Ajouter des champs pour la visualisation](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Fields.png "Ajouter des champs pour la visualisation")

7. Par défaut, les champs de température sont affichés en tant qu’agrégat. Si vous préférez afficher les températures moyennes, vous pouvez le faire à partir de la liste déroulante, comme indiqué ci-dessous.

	![Prendre la moyenne des températures](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Temp.Avg.png "Prendre la moyenne des températures")

8. Vous pouvez également superposer les cartes des températures pour mieux comprendre la différence entre les températures cibles et réelles. Déplacez la souris vers le coin de la carte inférieure jusqu’à ce qu’une poignée s’affiche dans un cercle rouge au-dessus. Faites glisser la carte vers l’autre carte située au-dessus, puis relâchez la souris lorsque la poignée s’affiche dans un rectangle rouge situé au-dessus.

	![Fusionner des cartes](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Merge.png "Fusionner des cartes")

	 La visualisation des données doit changer comme suit :

	![Visualisation](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Final.Visual.png "Visualisation")
	 
9. Cliquez sur **Save** pour enregistrer la feuille de calcul. Vous pouvez créer des tableaux de bord et leur ajouter une ou plusieurs feuilles.

##<a name="seealso"></a>Voir aussi

* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Démarrage rapide : Approvisionner Apache Spark sur HDInsight et exécuter des requêtes interactives à l’aide de Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Utiliser Spark sur HDInsight pour créer des applications d’apprentissage automatique](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Utiliser Spark sur HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=Oct15_HO2-->