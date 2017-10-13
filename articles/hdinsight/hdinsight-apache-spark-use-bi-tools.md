---
title: "BI Spark utilisant des outils de visualisation des données sur Azure HDInsight | Documents Microsoft"
description: "Utiliser des outils de visualisation de données à des fins d’analyse à l’aide d’Apache Spark BI sur des clusters HDInsight"
keywords: "apache spark bi,spark bi,visualisation de données spark,décisionnel spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.openlocfilehash: 869a000909813e607620c47ef802b4043e37dfa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI utilisant des outils de visualisation de données avec Azure HDInsight

Découvrez comment utiliser des outils de visualisation de données tels que Power BI et Tableau pour analyser un exemple de jeu de données brutes à l’aide d’Apache Spark BI sur des clusters HDInsight.

> [!NOTE]
> La connectivité avec les outils décisionnels décrite dans cet article n’est pas prise en charge sur Spark 2.1 avec Azure HDInsight 3.6 version préliminaire. Seules les versions Spark 1.6 et 2.0 (HDInsight 3.4, 3.5 respectivement) sont prises en charge.
>

Ce didacticiel est également disponible en tant que bloc-notes Jupyter sur un cluster Spark HDInsight. L’interface du bloc-notes vous permet d’exécuter des extraits de code Python à partir du bloc-notes lui-même. Pour effectuer le didacticiel au sein d’un bloc-notes, créez un cluster Spark, lancez un bloc-notes Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), puis exécutez le bloc-notes **Use BI tools with Apache Spark on HDInsight.ipynb** sous le dossier **Python**.

## <a name="prerequisites"></a>Composants requis

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="hivetable"></a>Préparer les données pour la visualisation de données Spark

Dans cette section, nous allons utiliser le bloc-notes [Jupyter](https://jupyter.org) issu d’un cluster HDInsight Spark pour exécuter les travaux traitant vos exemples de données brutes et les enregistrer dans une table. L’exemple de données est un fichier .csv (hvac.csv), qui est disponible par défaut sur tous les clusters. Une fois vos données enregistrées sous forme de table, dans la section suivante, nous utilisons des outils décisionnels pour nous connecter à la table et générer des visualisations de données.

> [!NOTE]
> Si vous effectuez les étapes de cet article après avoir terminé les instructions dans [Run interactive queries on an HDInsight Spark cluster](hdinsight-apache-spark-load-data-run-query.md) (Exécuter des requêtes interactives sur un cluster HDInsight Spark), allez directement à l’étape 8.
>

1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   

2. Dans le panneau du cluster Spark, cliquez sur **Tableau de bord du cluster**, puis sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

   > [!NOTE]
   > Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Créez un bloc-notes. Cliquez sur **Nouveau**, puis sur **PySpark**.

    ![Créer un bloc-notes Jupyter pour Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/create-jupyter-notebook-for-spark-bi.png "Créer un bloc-notes Jupyter pour Apache Spark BI")

4. Un nouveau bloc-notes est créé et ouvert sous le nom Untitled.pynb. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial.

    ![Fournir un nom pour le bloc-notes pour Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/jupyter-notebook-name-for-spark-bi.png "Fournir un nom pour le bloc-notes pour Apache Spark BI")

5. Comme vous avez créé un bloc-notes à l’aide du noyau PySpark, il est inutile de créer des contextes explicitement. Les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code. Vous pouvez commencer par importer les types requis pour ce scénario. Pour ce faire, placez le curseur dans la cellule, puis appuyez sur **MAJ + ENTRÉE**.

        from pyspark.sql import *

6. Chargez un exemple de données dans une table temporaire. Lorsque vous créez un cluster Spark dans HDInsight, l’exemple de fichier de données **hvac.csv** est copié vers le compte de stockage associé dans **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Dans une cellule vide, collez l’extrait suivant, puis appuyez sur **MAJ + ENTRÉE**. Cet extrait enregistre les données dans une table appelée **hvac**.

        # Create an RDD from sample data
        hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

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
        |hvactemptable  |true        |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    Seules les tables dont la colonne **isTemporary** est définie sur False sont des tables Hive qui sont stockées dans le metastore et qui sont accessibles à partir des outils décisionnels. Dans ce didacticiel, nous nous connectons à la table **hvac** que nous avons créée.

8. Vérifiez que la table contient les données prévues. Dans une cellule vide du bloc-notes, collez l’extrait suivant, puis appuyez sur **MAJ + ENTRÉE**.

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Arrêtez le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**.

## <a name="powerbi"></a>Utiliser Power BI pour la visualisation de données Spark

> [!NOTE]
> Cette section s’applique uniquement à Spark 1.6 sur HDInsight 3.4 et Spark 2.0 sur HDInsight 3.5.
>
>

Une fois que vous avez enregistré les données dans une table, vous pouvez utiliser Power BI pour vous connecter aux données et les visualiser afin de créer des rapports, des tableaux de bord, etc.

1. Assurez-vous que vous avez accès à Power BI. Vous pouvez obtenir un abonnement gratuit à la version préliminaire de Power BI à l’adresse [http://www.powerbi.com/](http://www.powerbi.com/).

2. Connectez-vous à [Power BI](http://www.powerbi.com/).

3. En bas du volet gauche, cliquez sur **Obtenir les données**.

4. Sur la page **Obtenir les données** sous **Importer ou se connecter à des données**, pour **Bases de données**, cliquez sur **Obtenir**.

    ![Obtenir des données dans Power BI pour Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obtenir des données dans Power BI pour Apache Spark BI")

5. Dans l’écran suivant, cliquez sur **Spark sur HDInsight**, puis sur **Se connecter**. Lorsque vous y êtes invité, entrez l’URL du cluster (`mysparkcluster.azurehdinsight.net`) et les informations d’identification pour la connexion au cluster.

    ![Se connecter à Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Se connecter à Apache Spark BI")

    Une fois la connexion établie, Power BI commence à importer les données du cluster Spark sur HDInsight.

6. Power BI importe les données et ajoute un jeu de données **Spark** sous l’en-tête **Jeux de données**. Cliquez sur le jeu de données pour ouvrir une nouvelle feuille de calcul afin de visualiser les données. Vous pouvez également enregistrer la feuille de calcul en tant que rapport. Pour enregistrer une feuille de calcul, dans le menu **Fichier**, cliquez sur **Enregistrer**.

    ![Vignette Apache Spark BI sur le tableau de bord Power BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-tile-dashboard.png "Vignette Apache Spark BI sur le tableau de bord Power BI")
7. Notez que la liste **Champs** sur la droite répertorie la table **hvac** créée précédemment. Développez la table pour en afficher les champs, comme vous les avez définis précédemment dans le bloc-notes.

      ![épertorier des tables sur le tableau de bord Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "épertorier des tables sur le tableau de bord Apache Spark BI")

8. Générez une visualisation pour afficher l’écart entre la température cible et la température réelle de chaque bâtiment. Pour visualiser vos données, sélectionnez **Graphique en aires** (affiché en rouge). Pour définir l’axe, effectuez un glisser-déplacer du champ **BuildingID** situé sous **Axe** et des champs **ActualTemp**/**TargetTemp** situés sous **Valeur**.

    ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

9. Par défaut, la visualisation affiche la somme des valeurs des champs **ActualTemp** et **TargetTemp**. Pour les deux champs, sélectionnez **Moyenne** dans la liste déroulante afin d’obtenir la moyenne des températures réelles et cibles des deux bâtiments.

    ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

10. La visualisation des données doit être semblable à celle illustrée dans la capture d’écran. Déplacez le curseur sur la visualisation pour obtenir des info-bulles contenant des données pertinentes.

    ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

11. Dans le menu supérieur, cliquez sur **Enregistrer** et indiquez un nom de rapport. Vous pouvez également épingler le visuel. Si vous épinglez une visualisation, elle est stockée sur votre tableau de bord pour que vous puissiez suivre les dernières valeurs en un clin d’œil.

   Vous pouvez ajouter autant de visualisations que vous le souhaitez pour un même jeu de données et les épingler au tableau de bord pour obtenir un instantané de vos données. En outre, les clusters Spark sur HDInsight sont connectés directement à Power BI. Ceci permet de s’assurer que Power BI dispose toujours des données les plus récentes de votre cluster. Vous n’avez donc pas besoin de planifier des actualisations du jeu de données.

## <a name="tableau"></a>Utiliser Tableau Desktop pour la visualisation de données Spark

> [!NOTE]
> Cette section s’applique uniquement aux clusters Spark 1.5.2 créés dans Azure HDInsight.
>
>

1. Installez [Tableau Desktop](http://www.tableau.com/products/desktop) sur l’ordinateur que vous utilisez pour exécuter ce didacticiel Apache Spark BI.

2. Vérifiez que l’ordinateur est également équipé du pilote ODBC de Microsoft Spark. Pour installer le pilote, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Lancez Tableau Desktop. Dans le volet gauche, dans la liste de serveurs auxquels se connecter, cliquez sur **Spark SQL**. Si Spark SQL n’est pas répertorié par défaut dans le volet gauche, cliquez sur **Autres serveurs**pour l’afficher.
2. Dans la boîte de dialogue de connexion à Spark SQL, entrez les valeurs telles qu’indiquées dans la capture d’écran, puis cliquez sur **OK**.

    ![Se connecter à un cluster pour Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Se connecter à un cluster pour Apache Spark BI")

    La liste déroulante d’authentification contient l’option **Windows Microsoft Azure HDInsight Service** uniquement si vous avez installé le [Pilote ODBC de Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) sur l’ordinateur.
3. Dans l’écran suivant, dans la liste déroulante **Schéma**, cliquez sur l’icône **Rechercher**, puis sur **default**.

    ![Rechercher un schéma pour Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Rechercher un schéma pour Apache Spark BI")
4. Pour le champ **Table**, cliquez de nouveau sur l’icône **Rechercher** pour dresser la liste de toutes les tables Hive disponibles dans le cluster. La table **hvac** créée précédemment à l’aide du bloc-notes doit être indiquée.

    ![Rechercher un tableau pour Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Rechercher un tableau pour Apache Spark BI")
5. Effectuez un glisser-déplacer de la table vers la zone supérieure située à droite. Tableau importe les données et affiche le schéma mis en exergue par l’encadré rouge.

    ![Ajouter des tables à Tableau pour Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Ajouter des tables à Tableau pour Apache Spark BI")
6. Cliquez sur l’onglet **Sheet1** en bas à gauche. Réalisez une visualisation qui affiche la moyenne des températures cibles et réelles de tous les bâtiments à chaque date. Faites glisser **Date** et **Building ID** vers **Columns** et **Actual Temp**/**Target Temp** vers **Rows**. Sous **Marks**, sélectionnez **Area** pour utiliser une carte de zone pour la visualisation de données Spark.

     ![Ajouter des champs pour une visualisation de données Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Ajouter des champs pour une visualisation de données Spark")
7. Par défaut, les champs de température sont affichés en tant qu’agrégat. Si vous préférez afficher les températures moyennes, vous pouvez le faire à partir de la liste déroulante, comme indiqué ci-dessous.

    ![Prendre une moyenne de températures pour une visualisation de données Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Prendre une moyenne de températures pour une visualisation de données Spark")

8. Vous pouvez également superposer les cartes des températures pour mieux comprendre la différence entre les températures cibles et réelles. Déplacez la souris vers le coin de la carte inférieure jusqu’à ce qu’une poignée s’affiche dans un cercle rouge. Faites glisser la carte vers l’autre carte située au-dessus, puis relâchez la souris lorsque la poignée s’affiche dans un rectangle rouge.

    ![Fusionner des cartes pour une visualisation de données Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Fusionner des cartes pour une visualisation de données Spark")

     La visualisation des données doit changer comme illustré dans la capture d’écran :

    ![Sortie de tableau pour une visualisation de données Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Sortie de tableau pour une visualisation de données Spark")
9. Cliquez sur **Save** pour enregistrer la feuille de calcul. Vous pouvez créer des tableaux de bord et leur ajouter une ou plusieurs feuilles.

## <a name="next-steps"></a>Étapes suivantes

Jusqu’ici, vous avez appris à créer un cluster, des trames de données Spark pour interroger des données, et à accéder à ces données depuis des outils décisionnels. Vous pouvez maintenant suivre les instructions pour gérer les ressources du cluster et pour déboguer les tâches exécutées sur un cluster HDInsight Spark.

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](hdinsight-apache-spark-job-debugging.md)

