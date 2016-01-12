<properties 
	pageTitle="Utiliser le gestionnaire de ressources pour allouer des ressources au cluster Apache Spark dans HDInsight | Microsoft Azure" 
	description="Découvrez comment utiliser le gestionnaire de ressources pour les clusters Spark sur HDInsight pour obtenir de meilleures performances." 
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
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Gérer les ressources du cluster Apache Spark dans Azure HDInsight (Linux)

Spark sur Azure HDInsight (Linux) fournit l’interface utilisateur web Ambari qui permet de gérer les ressources de cluster et de surveiller l’intégrité du cluster. Vous pouvez également utiliser le serveur d’historique Spark pour effectuer le suivi des applications dont l’exécution sur le cluster est terminée. Vous pouvez utiliser l’interface utilisateur YARN pour surveiller l’exécution sur le cluster. Cet article explique comment accéder à ces interfaces utilisateur et comment les utiliser pour effectuer certaines tâches de gestion des ressources de base.

**Configuration requise :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Cluster Apache Spark sur HDInsight Linux Pour obtenir des instructions, consultez [Créer des clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Comment lancer l’interface utilisateur web Ambari ?

1. Dans le tableau d’accueil du [portail Azure en version préliminaire](https://ms.portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous avez épinglé ce dernier au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**. 
 
2. Dans le panneau du cluster Spark, cliquez sur **Tableau de bord**. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster Spark.

	![Lancer Ambari](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "Démarrer le gestionnaire de ressources")

3. Cela doit lancer l’interface utilisateur web Ambari comme indiqué ci-dessous.

	![Interface utilisateur web d'Ambari](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Interface utilisateur web d'Ambari")

## Comment lancer le serveur d’historique Spark ?

1. Dans le tableau d’accueil du [portail Azure en version préliminaire](https://ms.portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous avez épinglé ce dernier au tableau d’accueil).

2. Dans le panneau du cluster, sous **Liens rapides**, cliquez sur **Tableau de bord du cluster**. Dans le panneau **Tableau de bord du cluster**, cliquez sur **Serveur d’historique Spark**.

	![Serveur d’historique Spark](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Serveur d’historique Spark")

	Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster Spark.


## Comment lancer l’interface utilisateur web Yarn ?

Vous pouvez utiliser l’interface utilisateur YARN pour surveiller les applications en cours d’exécution sur le cluster Spark. Avant d’accéder à l’interface utilisateur Yarn, activez le tunneling SSH pour le cluster. Pour obtenir des instructions, consultez [Utilisation de SSH Tunneling pour accéder à l’interface web Ambari](hdinsight-linux-ambari-ssh-tunnel.md)

1. Lancez l’interface utilisateur web Ambari comme indiqué dans la section ci-dessus.

2. Dans l'interface utilisateur Web Ambari, sélectionnez YARN dans la liste située sur la gauche de la page.

3. 3\. Lorsque les informations de service YARN s’affichent, sélectionnez **Liens rapides**. Une liste des nœuds principaux du cluster s'affiche. Sélectionnez l’un des nœuds principaux, puis **Interface utilisateur ResourceManager**.

	![Lancer l’interface utilisateur Yarn](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png "Lancer l’interface utilisateur Yarn")

4. L’interface utilisateur Yarn doit s’ouvrir et une page similaire à celle ci-dessous doit s’afficher :

	![Interface utilisateur Yarn](./media/hdinsight-apache-spark-resource-manager/yarn-ui.png "Interface utilisateur Yarn")

##<a name="scenariosrm"></a>Comment gérer les ressources à l’aide de l’interface utilisateur web Ambari ?

Voici quelques scénarios courants que vous pouvez rencontrer avec le cluster Spark, ainsi que les instructions relatives à leur résolution à l’aide de l’interface utilisateur web Ambari.

### Je n’utilise pas d’outils décisionnels avec le cluster Spark. Comment reprendre des ressources ?

1. Lancez l’interface utilisateur web Ambari comme indiqué ci-dessus. Dans le volet de navigation gauche, cliquez sur **Spark**, puis sur **Configurations**.

2. Dans la liste des configurations disponibles, recherchez **Custom spark-thrift-sparkconff** et définissez les valeurs de **spark.executor.memory** et **spark.drivers.core** sur **0**.

	![Ressources relatives aux outils décisionnels](./media/hdinsight-apache-spark-resource-manager/spark-bi-resources.png "Ressources relatives aux outils décisionnels")

3. Cliquez sur **Enregistrer**. Entrez la description des modifications apportées, puis cliquez de nouveau sur **Enregistrer**.

4. En haut de la page, vous devez voir une invite de redémarrage du service Spark. Cliquez sur **Redémarrer** pour que les modifications prennent effet.


### Mes blocs-notes Jupyter ne s’exécutent pas comme prévu. Comment redémarrer le service ?

1. Lancez l’interface utilisateur web Ambari comme indiqué ci-dessus. Dans le volet de navigation gauche, cliquez sur **Jupyter**, sur **Actions de service**, puis sur **Redémarrer tout**. Cela permet au service Jupyter de démarrer sur tous les nœuds principaux.

	![Redémarrer Jupyter](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Redémarrer Jupyter")

	


## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scénarios

* [Spark avec les outils décisionnels : Effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](hdinsight-apache-spark-use-bi-tools.md)

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température des bâtiments à l’aide des données des systèmes HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark : Utiliser Spark sur HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Création et exécution d’applications

* [Création d’une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécution de tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Extensions

* [Utilisation de bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)



[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_1223_2015-->