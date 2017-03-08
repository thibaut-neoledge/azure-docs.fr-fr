---
title: "Utiliser les outils de BI avec Apache Spark sur Azure HDInsight | Microsoft Docs"
description: "Des instructions pas à pas expliquent comment utiliser des blocs-notes avec Apache Spark pour créer des schémas basés sur les données brutes, comment les enregistrer dans des tables Hive, et comment utiliser des outils décisionnels dans la table Hive pour analyser les données."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: de252e1d2945f236a4192c5737ed8ec88a6f7444
ms.openlocfilehash: 9ec5d45a38aefe24454f8673d5754d65b3800c17
ms.lasthandoff: 03/01/2017


---
# <a name="use-bi-tools-with-apache-spark-cluster-on-azure-hdinsight"></a>Utiliser les outils de BI avec un cluster Apache Spark sur Azure HDInsight

Découvrez comment utiliser Apache Spark dans Azure HDInsight pour effectuer l’opération suivante :

* Prendre un exemple de données brutes et l’enregistrer dans une table Hive
* Utilisez des outils décisionnels tels que Power BI et Tableau pour analyser et visualiser les données.

Ce didacticiel est également disponible en tant que bloc-notes Jupyter sur un cluster Spark (Linux) que vous créez dans HDInsight. L’interface du bloc-notes vous permet d’exécuter des extraits de code Python à partir du bloc-notes lui-même. Pour effectuer le didacticiel au sein d’un bloc-notes, créez un cluster Spark, lancez un bloc-notes Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), puis exécutez le bloc-notes **Use BI tools with Apache Spark on HDInsight.ipynb** sous le dossier **Python**.

> [!NOTE]
> La connectivité avec les outils décisionnels décrite dans cet article n’est pas prise en charge sur Spark 2.1 avec Azure HDInsight 3.6 version préliminaire. Seules les versions Spark 1.6 et 2.0 (HDInsight 3.4, 3.5 respectivement) sont prises en charge.
>

**Configuration requise :**

Vous devez disposer des éléments suivants :

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Un ordinateur équipé du pilote ODBC de Microsoft Spark (permet à Spark sur HDInsight de fonctionner avec Tableau). Pour installer le pilote, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=616229).
* Des outils décisionnels tels que [Power BI](http://www.powerbi.com/) ou [Tableau Desktop](http://www.tableau.com/products/desktop). Vous pouvez obtenir un abonnement gratuit à la version préliminaire de Power BI à l’adresse [http://www.powerbi.com/](http://www.powerbi.com/).

## <a name="hivetable"></a>Enregistrer des données brutes dans une table Hive
Dans cette section, nous allons utiliser le bloc-notes [Jupyter](https://jupyter.org) associé à un cluster Apache Spark dans HDInsight pour exécuter les travaux traitant vos exemples de données brutes et les enregistrer dans une table Hive. L’exemple de données est un fichier .csv (hvac.csv), qui est disponible par défaut sur tous les clusters.

Une fois vos données enregistrées dans une table Hive, nous allons nous connecter, dans la prochaine section, à la table Hive à l’aide d’outils décisionnels comme Power BI et Tableau.

1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   
2. Dans le panneau du cluster Spark, cliquez sur **Tableau de bord du cluster**, puis sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

   > [!NOTE]
   > Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis sur **PySpark**.

    ![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")
4. Un nouveau bloc-notes est créé et ouvert sous le nom Untitled.pynb. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial.

    ![Donnez un nom au bloc-notes](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.notebook.name.png "Donnez un nom au bloc-notes")
5. Comme vous avez créé un bloc-notes à l’aide du noyau PySpark, il est inutile de créer des contextes explicitement. Les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code. Vous pouvez commencer par importer les types requis pour ce scénario. Pour ce faire, placez le curseur dans la cellule, puis appuyez sur **MAJ + ENTRÉE**.

        from pyspark.sql import *
6. Chargez un exemple de données dans une table temporaire. Lorsque vous créez un cluster Spark dans HDInsight, l’exemple de fichier de données **hvac.csv** est copié vers le compte de stockage associé dans **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Dans une cellule vide, collez l’extrait suivant, puis appuyez sur **MAJ + ENTRÉE**. Cet extrait enregistre les données dans une table Hive appelée **hvac**.

        # Create an RDD from sample data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))

        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

1. Vérifiez que la table a bien été créée. Vous pouvez utiliser `%%sql` pour exécuter directement des interrogations Hive. Pour plus d’informations sur la méthode magique `%%sql` , ainsi que les autres méthodes magiques disponibles avec le noyau PySpark, consultez [Noyaux disponibles sur les blocs-notes Jupyter avec clusters Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#choose-between-the-kernels).

        %%sql
        SHOW TABLES

    Un résultat semblable à ce qui suit doit s’afficher :

        +-----------+---------------+
        |isTemporary|tableName        |
        +-----------+---------------+
        |       true|hvactemptable  |
        |      false|hivesampletable|
        |      false|hvac            |
        +-----------+---------------+

    Seules les tables dont la colonne **isTemporary** est définie sur False sont des tables Hive qui sont stockées dans le metastore et qui sont accessibles à partir des outils décisionnels. Dans ce didacticiel, nous allons nous connecter à la table **hvac** que nous venons de créer.

1. Vérifiez que la table contient les données prévues. Dans une cellule vide du bloc-notes, collez l’extrait suivant, puis appuyez sur **MAJ + ENTRÉE**.

        %%sql
        SELECT * FROM hvac LIMIT 10
2. Vous pouvez à présent arrêter le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette opération permet d’arrêter et de fermer le bloc-notes.

## <a name="powerbi"></a>Utilisation de Power BI pour analyser les données de la table Hive
Une fois que vous avez enregistré les données dans une table Hive, vous pouvez utiliser Power BI pour vous connecter aux données et les visualiser afin de créer des rapports, des tableaux de bord, etc.

1. Connectez-vous à [Power BI](http://www.powerbi.com/).
2. Dans l’écran d’accueil, cliquez sur **Bases de données et autres**.

    ![Obtenir des données dans Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.get.data.png "Obtenir des données dans Power BI")
3. Dans l’écran suivant, cliquez sur **Spark sur HDInsight**, puis sur **Se connecter**. Lorsque vous y êtes invité, entrez l’URL du cluster (`mysparkcluster.azurehdinsight.net`) et les informations d’identification pour la connexion au cluster.

    Une fois la connexion établie, Power BI commence à importer les données du cluster Spark sur HDInsight.
4. Power BI importe les données et ajoute un nouveau jeu de données Spark sous l’en-tête **Jeux de données** . Cliquez sur le jeu de données pour ouvrir une nouvelle feuille de calcul afin de visualiser les données. Vous pouvez également enregistrer la feuille de calcul en tant que rapport. Pour enregistrer une feuille de calcul, dans le menu **Fichier**, cliquez sur **Enregistrer**.

    ![Vignette Spark sur le tableau de bord Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.tile.png "Vignette Spark sur le tableau de bord Power BI")
5. Notez que la liste **Champs** sur la droite répertorie la table **hvac** créée précédemment. Développez la table pour en afficher les champs, comme vous les avez définis précédemment dans le bloc-notes.

      ![Répertorier les tables Hive](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.display.tables.png "Répertorier les tables Hive")
6. Générez une visualisation pour afficher l’écart entre la température cible et la température réelle de chaque bâtiment. Sélectionnez **Graphique en aires** (affiché en rouge) pour visualiser vos données. Pour définir l’axe, effectuez un glisser-déplacer du champ **BuildingID** situé sous **Axe** et des champs **ActualTemp**/**TargetTemp** situés sous **Valeur**.

    ![Créer des visualisations](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual1.png "Créer des visualisations")
7. Par défaut, la visualisation affiche la somme des valeurs des champs **ActualTemp** et **TargetTemp**. Pour les deux champs, sélectionnez **Moyenne** dans la liste déroulante afin d’obtenir la moyenne des températures réelles et cibles des deux bâtiments.

    ![Créer des visualisations](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual2.png)
8. La visualisation des données doit être semblable à ce qui suit. Déplacez le curseur sur la visualisation pour obtenir des info-bulles contenant des données pertinentes.

    ![Créer des visualisations](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual3.png)
9. Dans le menu supérieur, cliquez sur **Enregistrer** et indiquez un nom de rapport. Vous pouvez également épingler le visuel. Si vous épinglez une visualisation, elle est stockée sur votre tableau de bord pour que vous puissiez suivre les dernières valeurs en un clin d’œil.

   Vous pouvez ajouter autant de visualisations que vous le souhaitez pour un même jeu de données et les épingler au tableau de bord pour obtenir un instantané de vos données. En outre, les clusters Spark sur HDInsight sont connectés directement à Power BI. En d’autres termes, Power BI dispose toujours des données les plus récentes de votre cluster. Vous n’avez donc pas besoin de planifier des actualisations du jeu de données.

## <a name="tableau"></a>Utiliser Tableau Desktop pour analyser les données de la table Hive

> [!NOTE]
> Cette section s’applique uniquement aux clusters Spark 1.5.2 créés dans Azure HDInsight.
>
>

1. Lancez Tableau Desktop. Dans le volet gauche, dans la liste de serveurs auxquels se connecter, cliquez sur **Spark SQL**. Si Spark SQL n’est pas répertorié par défaut dans le volet gauche, cliquez sur **Autres serveurs**pour l’afficher.
2. Dans la boîte de dialogue de connexion à Spark SQL, entrez les valeurs indiquées ci-dessous, puis cliquez sur **OK**.

    ![Se connecter à un cluster Spark](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.connect.png "Se connecter à un cluster Spark")

    La liste déroulante d’authentification contient l’option **Windows Microsoft Azure HDInsight Service** uniquement si vous avez installé le [Pilote ODBC de Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) sur l’ordinateur.
3. Dans l’écran suivant, dans la liste déroulante **Schéma**, cliquez sur l’icône **Rechercher**, puis sur **default**.

    ![Rechercher un schéma](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.schema.png "Rechercher un schéma")
4. Pour le champ **Table**, cliquez de nouveau sur l’icône **Rechercher** pour dresser la liste de toutes les tables Hive disponibles dans le cluster. La table **hvac** créée précédemment à l’aide du bloc-notes doit être indiquée.

    ![Rechercher des tables](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.table.png "Rechercher des tables")
5. Effectuez un glisser-déplacer de la table vers la zone supérieure située à droite. Tableau importe les données et affiche le schéma mis en exergue par l’encadré rouge.

    ![Ajouter des tables à Tableau](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.table.png "Ajouter des tables à Tableau")
6. Cliquez sur l’onglet **Sheet1** en bas à gauche. Réalisez une visualisation qui affiche la moyenne des températures cibles et réelles de tous les bâtiments à chaque date. Faites glisser **Date** et **Building ID** vers **Columns** et **Actual Temp**/**Target Temp** vers **Rows**. Sous **Marks**, sélectionnez **Area** pour utiliser une visualisation par carte des zones.

     ![Ajouter des champs pour la visualisation](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.fields.png "Ajouter des champs pour la visualisation")
7. Par défaut, les champs de température sont affichés en tant qu’agrégat. Si vous préférez afficher les températures moyennes, vous pouvez le faire à partir de la liste déroulante, comme indiqué ci-dessous.

    ![Prendre la moyenne des températures](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.temp.avg.png "Prendre la moyenne des températures")
8. Vous pouvez également superposer les cartes des températures pour mieux comprendre la différence entre les températures cibles et réelles. Déplacez la souris vers le coin de la carte inférieure jusqu’à ce qu’une poignée s’affiche dans un cercle rouge. Faites glisser la carte vers l’autre carte située au-dessus, puis relâchez la souris lorsque la poignée s’affiche dans un rectangle rouge.

    ![Fusionner des cartes](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.merge.png "Fusionner des cartes")

     La visualisation des données doit changer comme suit :

    ![Visualisation](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.final.visual.png "Visualisation")
9. Cliquez sur **Save** pour enregistrer la feuille de calcul. Vous pouvez créer des tableaux de bord et leur ajouter une ou plusieurs feuilles.

## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température des bâtiments à l’aide des données des systèmes HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications
* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely) (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

