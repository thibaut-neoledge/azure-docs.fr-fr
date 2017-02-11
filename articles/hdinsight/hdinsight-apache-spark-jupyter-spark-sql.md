---
title: "Créer un cluster Spark dans Azure HDInsight et utiliser Spark SQL à partir de Jupyter pour effectuer une analyse interactive | Microsoft Docs"
description: "Des instructions pas à pas expliquent comment créer rapidement un cluster Apache Spark dans HDInsight, puis comment utiliser Spark SQL à partir des blocs-notes Jupyter pour exécuter des requêtes interactives."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 791b6a5a07bb87302cb382290a355c9a14c63ff0
ms.openlocfilehash: cc1d484d40dce0b1c64f2e8cdebb9377a38705cb


---
# <a name="get-started-create-apache-spark-cluster-in-azure-hdinsight-and-run-interactive-queries-using-spark-sql"></a>Prise en main : Créer le cluster Apache Spark sur Azure HDInsight et exécuter des requêtes interactives à l’aide de Spark SQL
Découvrez comment créer un cluster [Apache Spark](hdinsight-apache-spark-overview.md) dans HDInsight, puis comment utiliser le bloc-notes [Jupyter](https://jupyter.org) pour exécuter des requêtes interactives Spark SQL sur le cluster Spark.

   ![Prise en main d’Apache Spark dans HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Suivez notre didacticiel HDInsight pour prendre en main Apache Spark. Étapes illustrées : créer un compte de stockage ; créer un cluster ; exécuter des instructions SQL Spark")

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Composants requis
* **Un abonnement Azure**. Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Voir [Créez votre compte Azure gratuit](https://azure.microsoft.com/free).
* **Un client SSH (Secure Shell)** : les systèmes Linux, Unix et OS X fournissent un client SSH par le biais de la commande `ssh`. Pour les systèmes Windows, consultez [Utilisation de SSH Hadoop Linux sur HDInsight à partir de Windows avec PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) ; pour Linux, Unix ou OS X, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix, ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!NOTE]
> Cet article utilise un modèle Azure Resource Manager pour créer un cluster Spark qui utilise les [objets Azure Storage Blob en tant que cluster de stockage](hdinsight-hadoop-use-blob-storage.md). Vous pouvez également créer un cluster Spark qui utilise [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) comme stockage supplémentaire, en plus des objets Blob Azure Storage utilisés en tant que stockage par défaut. Pour plus d’informations, voir [Créer un cluster HDInsight avec Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-spark-cluster"></a>Création d’un cluster Spark
Dans cette section, vous créez un cluster Spark dans HDInsight à l’aide d’un [modèle Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Pour en savoir plus sur les différentes versions de HDInsight et leurs contrats SLA, consultez la page [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md). Pour obtenir d’autres méthodes de création de cluster, consultez [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Saisissez les valeurs suivantes :

    ![Créer un cluster Spark dans HDInsight à l’aide d’un modèle Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Créer un cluster Spark dans HDInsight à l’aide d’un modèle Azure Resource Manager")

   * **Abonnement** : sélectionnez votre abonnement Azure pour ce cluster.
   * **Groupe de ressources** : créez un nouveau groupe de ressources ou sélectionnez un groupe existant. Le groupe de ressources est utilisé pour gérer des ressources Azure pour vos projets.
   * **Emplacement** : sélectionnez un emplacement pour le groupe de ressources.  Cet emplacement est également utilisé pour le stockage de cluster par défaut et le cluster HDInsight.
   * **ClusterName**: entrez un nom pour le cluster Hadoop que vous allez créer.
   * **Nom d’utilisateur et mot de passe de cluster**: le nom de connexion par défaut est admin.
   * **Nom d’utilisateur et mot de passe SSH**.

   Veuillez noter ces valeurs.  Vous en aurez besoin plus loin dans le didacticiel.

3. Sélectionnez **J’accepte les termes et conditions mentionnés ci-dessus** et **Épingler au tableau de bord**, puis cliquez sur **Acheter**. Vous pouvez voir une nouvelle vignette intitulée Envoi du déploiement pour Déploiement de modèle. La création du cluster prend environ 20 minutes.

## <a name="run-spark-sql-queries-using-a-jupyter-notebook"></a>Exécuter des requêtes Spark SQL à l’aide d’un bloc-notes Jupyter
Dans cette section, vous allez utiliser un bloc-notes Jupyter pour exécuter des requêtes Spark SQL sur le cluster Spark. Les clusters HDInsight Spark fournissent deux noyaux que vous pouvez utiliser avec le bloc-notes Jupyter. Ces étapes sont les suivantes :

* **PySpark** (pour les applications écrites en Python)
* **Spark** (pour les applications écrites en Scala)

Dans cet article, vous allez utiliser le noyau PySpark. L’article [Noyaux disponibles sur les ordinateurs portables Jupyter avec clusters Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-pyspark-or-spark-kernels) présente en détail les avantages de l’utilisation du noyau PySpark. Cependant, voici deux principaux avantages de l’utilisation du noyau PySpark :

* Il est inutile de définir les contextes pour Spark et Hive. Ils sont automatiquement définis pour vous.
* Vous pouvez utiliser des cell magics, notamment `%%sql`, pour exécuter directement vos requêtes SQL ou Hive, sans aucun extrait de code précédent.
* Le résultat des requêtes SQL ou Hive est automatiquement affiché.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Création d’un bloc-notes Jupyter avec un noyau PySpark

1. Ouvrez le [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, cliquez sur **Groupes de ressources**.
3. Cliquez sur le groupe de ressources que vous avez créé dans la dernière section. S’il y a trop de groupes de ressources, vous pouvez utiliser la fonction de recherche. Vous pouvez voir deux ressources dans le groupe, le cluster HDInsight et le compte de stockage par défaut.
4. Cliquez sur le cluster pour l’ouvrir.
 
2. À partir de **Liens rapides**, cliquez sur **Tableaux de bord de Cluster**, puis sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

   ![Tableaux de bord de cluster HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-azure-portal-cluster-dashboards.png "Tableaux de bord de cluster HDInsight")

   > [!NOTE]
   > Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis sur **PySpark**.

   ![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

   Un nouveau bloc-notes est créé et ouvert sous le nom Untitled(Untitled.pynb). 

4. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial si vous le souhaitez.

    ![Donnez un nom au bloc-notes](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Donnez un nom au bloc-notes")
5. Collez l’exemple de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE** pour exécuter le code. Le code importe les types requis pour ce scénario :

        from pyspark.sql.types import *

    Comme vous avez créé un bloc-notes à l’aide du noyau PySpark, il est inutile de créer des contextes explicitement. Les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code.

    ![État d’une tâche de bloc-notes Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "État d’une tâche de bloc-notes Jupyter")

    À chaque exécution d’une tâche dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

6. Exécutez le code suivant pour enregistrer des exemples de données dans une table temporaire appelée **hvac**.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

    Les clusters Spark dans HDInsight sont fournis avec un exemple de fichier de données, **hvac.csv**, sous **\HdiSamples\HdiSamples\SensorSampleData\hvac**.
    
7. Exécutez le code suivant pour interroger les données :

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Étant donné que vous utilisez un noyau PySpark, vous pouvez maintenant exécuter directement une requête SQL sur la table temporaire **hvac** que vous venez de créer à l’aide de la méthode magique `%%sql`. Pour plus d’informations sur la méthode magique `%%sql` , ainsi que les autres méthodes magiques disponibles avec le noyau PySpark, consultez [Noyaux disponibles sur les blocs-notes Jupyter avec clusters Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-pyspark-or-spark-kernels).

   La sortie sous forme de tableau suivante s’affiche par défaut.

     ![Table de sortie des résultats de la requête](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Table de sortie des résultats de la requête")

    Vous pouvez également voir les résultats dans d’autres visualisations. Par exemple, un graphique en aires pour le même résultat se présenterait comme suit.

    ![Graphique en aires des résultats de la requête](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Graphique en aires des résultats de la requête")

9. Une fois l’exécution de l’application terminée, vous pouvez arrêter le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette opération permet d’arrêter et de fermer le bloc-notes.

## <a name="delete-the-cluster"></a>Suppression du cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="see-also"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetry data analysis using Spark in HDInsight (Analyse des données de télémétrie Application Insight à l’aide de Spark dans HDInsight)](hdinsight-spark-analyze-application-insight-logs.md)

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



<!--HONumber=Jan17_HO2-->


