---
title: "Utiliser les outils de BI avec Apache Spark sur Azure HDInsight | Microsoft Docs"
description: "Des instructions pas à pas expliquent comment utiliser des blocs-notes avec Apache Spark pour créer des schémas basés sur les données brutes, comment les enregistrer dans des tables, et comment utiliser des outils décisionnels dans la table pour analyser les données."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 36b7aaf99db48efa1b56b84ac0616cf9ee2830ac
ms.lasthandoff: 03/18/2017


---
# <a name="use-bi-tools-with-apache-spark-cluster-on-azure-hdinsight"></a>Utiliser les outils de BI avec un cluster Apache Spark sur Azure HDInsight

Découvrez comment utiliser Apache Spark dans Azure HDInsight pour analyser un ensemble d’exemples de données brutes, puis utiliser des outils décisionnels pour visualiser les données. Cet article explique comment utiliser les outils décisionnels comme Power BI et Tableau avec les clusters HDInsight Spark.

> [!NOTE]
> La connectivité avec les outils décisionnels décrite dans cet article n’est pas prise en charge sur Spark 2.1 avec Azure HDInsight 3.6 version préliminaire. Seules les versions Spark 1.6 et 2.0 (HDInsight 3.4, 3.5 respectivement) sont prises en charge.
>

Ce didacticiel est également disponible en tant que bloc-notes Jupyter sur un cluster Spark (Linux) que vous créez dans HDInsight. L’interface du bloc-notes vous permet d’exécuter des extraits de code Python à partir du bloc-notes lui-même. Pour effectuer le didacticiel au sein d’un bloc-notes, créez un cluster Spark, lancez un bloc-notes Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), puis exécutez le bloc-notes **Use BI tools with Apache Spark on HDInsight.ipynb** sous le dossier **Python**.

## <a name="prerequisites"></a>Composants requis

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="hivetable"></a>Enregistrer des données brutes en tant que table

Dans cette section, nous allons utiliser le bloc-notes [Jupyter](https://jupyter.org) issu d’un cluster HDInsight Spark pour exécuter les travaux traitant vos exemples de données brutes et les enregistrer dans une table. L’exemple de données est un fichier .csv (hvac.csv), qui est disponible par défaut sur tous les clusters.

Une fois que vos données sont enregistrées sous forme de table, dans la section suivante, nous utilisons des outils décisionnels pour nous connecter à la table et faire davantage de visualisations.

1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   
2. Dans le panneau du cluster Spark, cliquez sur **Tableau de bord du cluster**, puis sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

   > [!NOTE]
   > Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Créez un bloc-notes. Cliquez sur **Nouveau**, puis sur **PySpark**.

    ![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

4. Un nouveau bloc-notes est créé et ouvert sous le nom Untitled.pynb. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial.

    ![Donnez un nom au bloc-notes](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.notebook.name.png "Donnez un nom au bloc-notes")

5. Comme vous avez créé un bloc-notes à l’aide du noyau PySpark, il est inutile de créer des contextes explicitement. Les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code. Vous pouvez commencer par importer les types requis pour ce scénario. Pour ce faire, placez le curseur dans la cellule, puis appuyez sur **MAJ + ENTRÉE**.

        from pyspark.sql import *

6. Chargez un exemple de données dans une table temporaire. Lorsque vous créez un cluster Spark dans HDInsight, l’exemple de fichier de données **hvac.csv** est copié vers le compte de stockage associé dans **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Dans une cellule vide, collez l’extrait suivant, puis appuyez sur **MAJ + ENTRÉE**. Cet extrait enregistre les données dans une table appelée **hvac**.

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

7. Vérifiez que la table a bien été créée. Vous pouvez utiliser `%%sql` pour exécuter directement des interrogations Hive. Pour plus d’informations sur la méthode magique `%%sql` et d’autres méthodes magiques disponibles avec le noyau PySpark, consultez [Noyaux disponibles sur les blocs-notes Jupyter avec clusters Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SHOW TABLES

    Vous verrez une sortie identique à celle ci-dessous :

        +---------------+-------------+
        |tableName      |isTemporary  |
        +---------------+-------------+
        |hvactemptable  |true         |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    Seules les tables dont la colonne **isTemporary** est définie sur False sont des tables Hive qui sont stockées dans le metastore et qui sont accessibles à partir des outils décisionnels. Dans ce didacticiel, nous nous connectons à la table **hvac** que nous avons créée.

8. Vérifiez que la table contient les données prévues. Dans une cellule vide du bloc-notes, collez l’extrait suivant, puis appuyez sur **MAJ + ENTRÉE**.

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Arrêtez le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**.

## <a name="powerbi"></a>Utiliser Power BI

Une fois que vous avez enregistré les données dans une table, vous pouvez utiliser Power BI pour vous connecter aux données et les visualiser afin de créer des rapports, des tableaux de bord, etc.

1. Assurez-vous que vous avez accès à Power BI. Vous pouvez obtenir un abonnement gratuit à la version préliminaire de Power BI à l’adresse [http://www.powerbi.com/](http://www.powerbi.com/).

2. Connectez-vous à [Power BI](http://www.powerbi.com/).

3. En bas du volet gauche, cliquez sur **Obtenir les données**.

4. Sur la page **Obtenir les données** sous **Importer ou se connecter à des données**, pour **Bases de données**, cliquez sur **Obtenir**.

    ![Obtenir des données dans Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.get.data.png "Obtenir des données dans Power BI")

5. Dans l’écran suivant, cliquez sur **Spark sur HDInsight**, puis sur **Se connecter**. Lorsque vous y êtes invité, entrez l’URL du cluster (`mysparkcluster.azurehdinsight.net`) et les informations d’identification pour la connexion au cluster.

    ![Se connecter à Spark](./media/hdinsight-apache-spark-use-bi-tools/power-bi-connect-to-spark.png "Obtenir des données dans Power BI")

    Une fois la connexion établie, Power BI commence à importer les données du cluster Spark sur HDInsight.

6. Power BI importe les données et ajoute un jeu de données **Spark** sous l’en-tête **Jeux de données**. Cliquez sur le jeu de données pour ouvrir une nouvelle feuille de calcul afin de visualiser les données. Vous pouvez également enregistrer la feuille de calcul en tant que rapport. Pour enregistrer une feuille de calcul, dans le menu **Fichier**, cliquez sur **Enregistrer**.

    ![Vignette Spark sur le tableau de bord Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.tile.png "Vignette Spark sur le tableau de bord Power BI")
7. Notez que la liste **Champs** sur la droite répertorie la table **hvac** créée précédemment. Développez la table pour en afficher les champs, comme vous les avez définis précédemment dans le bloc-notes.

      ![Répertorier les tables Hive](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.display.tables.png "Répertorier les tables Hive")

8. Générez une visualisation pour afficher l’écart entre la température cible et la température réelle de chaque bâtiment. Sélectionnez **Graphique en aires** (affiché en rouge) pour visualiser vos données. Pour définir l’axe, effectuez un glisser-déplacer du champ **BuildingID** situé sous **Axe** et des champs **ActualTemp**/**TargetTemp** situés sous **Valeur**.

    ![Créer des visualisations](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual1.png "Créer des visualisations")

9. Par défaut, la visualisation affiche la somme des valeurs des champs **ActualTemp** et **TargetTemp**. Pour les deux champs, sélectionnez **Moyenne** dans la liste déroulante afin d’obtenir la moyenne des températures réelles et cibles des deux bâtiments.

    ![Créer des visualisations](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual2.png)

10. La visualisation des données doit être semblable à celle illustrée dans la capture d’écran. Déplacez le curseur sur la visualisation pour obtenir des info-bulles contenant des données pertinentes.

    ![Créer des visualisations](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual3.png)

11. Dans le menu supérieur, cliquez sur **Enregistrer** et indiquez un nom de rapport. Vous pouvez également épingler le visuel. Si vous épinglez une visualisation, elle est stockée sur votre tableau de bord pour que vous puissiez suivre les dernières valeurs en un clin d’œil.

   Vous pouvez ajouter autant de visualisations que vous le souhaitez pour un même jeu de données et les épingler au tableau de bord pour obtenir un instantané de vos données. En outre, les clusters Spark sur HDInsight sont connectés directement à Power BI. Ceci permet de s’assurer que Power BI dispose toujours des données les plus récentes de votre cluster. Vous n’avez donc pas besoin de planifier des actualisations du jeu de données.

## <a name="tableau"></a>Utiliser Tableau Desktop pour analyser les données de la table

> [!NOTE]
> Cette section s’applique uniquement aux clusters Spark 1.5.2 créés dans Azure HDInsight.
>
>

1. Installez [Tableau Desktop](http://www.tableau.com/products/desktop) sur l’ordinateur où vous exécutez ce didacticiel.

2. Vérifiez que l’ordinateur est également équipé du pilote ODBC de Microsoft Spark. Pour installer le pilote, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Lancez Tableau Desktop. Dans le volet gauche, dans la liste de serveurs auxquels se connecter, cliquez sur **Spark SQL**. Si Spark SQL n’est pas répertorié par défaut dans le volet gauche, cliquez sur **Autres serveurs**pour l’afficher.
2. Dans la boîte de dialogue de connexion à Spark SQL, entrez les valeurs telles qu’indiquées dans la capture d’écran, puis cliquez sur **OK**.

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

     La visualisation des données doit changer comme illustré dans la capture d’écran :

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
* [Utilisation du plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
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

