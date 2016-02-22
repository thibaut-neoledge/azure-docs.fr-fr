<properties 
	pageTitle="Noyaux disponibles avec les blocs-notes Jupyter sur des clusters HDInsight Spark sur Linux | Microsoft Azure" 
	description="En savoir plus sur les noyaux de bloc-notes Jupyter supplémentaires disponibles avec le cluster Spark sur HDInsight Linux." 
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
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Noyaux disponibles pour les blocs-notes Jupyter avec les clusters Spark sur HDInsight (Linux)

Le cluster Apache Spark sur HDInsight (Linux) comprend des blocs-notes Jupyter qui vous permettent de tester vos applications. Par défaut, le bloc-notes Jupyter comprend un noyau **Python2**. Les clusters HDInsight Spark fournissent deux noyaux supplémentaires que vous pouvez utiliser avec le bloc-notes Jupyter. Il s’agit de :

1. **Spark** (pour les applications écrites en Scala)
2. **PySpark** (pour les applications écrites en Python)

Dans cet article, vous allez découvrir comment utiliser ces noyaux ainsi que leurs avantages.

**Configuration requise :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark sur HDInsight Linux. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Utilisation des noyaux 

1. Dans le tableau d’accueil du [portail Azure en version préliminaire](https://portal.azure.com/), cliquez sur la mosaïque de votre cluster Spark (si vous avez épinglé ce dernier au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   

2. Dans le panneau du cluster Spark, cliquez sur **Liens rapides**, puis dans le panneau **Tableau de bord du cluster**, cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

	> [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez __CLUSTERNAME__ par le nom de votre cluster.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créez un nouveau bloc-notes avec les nouveaux noyaux. Cliquez sur **Nouveau**, puis sur **PySpark** ou **Spark**. Utilisez le noyau Spark pour les applications Scala et le noyau PySpark pour les applications Python.

	![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Créer un bloc-notes Jupyter")

3. Un nouveau bloc-notes s’ouvre avec le noyau que vous avez sélectionné.

## Pourquoi utiliser les nouveaux noyaux ?

Voici quelques avantages de l'utilisation des nouveaux noyaux.

1. Avec le noyau **Python2** par défaut, vous devez définir les contextes Spark, SQL ou Hive avant de commencer à travailler avec l'application que vous développez. Si vous utilisez les nouveaux noyaux (**Spark** ou **PySpark**), ces contextes sont disponibles par défaut. Ces contextes sont les suivants :

	* **sc** : pour le contexte Spark
	* **sqlContext** : pour le contexte SQL
	* **hiveContext** : pour le contexte Hive


	Par conséquent, vous n’avez pas à exécuter d’instructions telles que les suivantes pour définir les contextes :

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)
		hiveContext = HiveContext(sc)

	En revanche, vous pouvez utiliser directement les contextes prédéfinis dans votre application.
	
2. Vous pouvez avoir recours directement aux méthodes magiques **%sql** et **%hive** pour utiliser respectivement les requêtes SQL ou Hive. Par conséquent, quelque chose de similaire fonctionnerait directement sans aucune configuration ni instructions de code principales.

		%hive
		SELECT * FROM hivesampletable LIMIT 10

## Points à prendre en compte lors de l'utilisation des nouveaux noyaux

Quel que soit le noyau que vous utilisez (Python2, PySpark ou Spark), laisser les blocs-notes s’exécuter consomme vos ressources de cluster. Avec le bloc-notes Python2, étant donné que vous créez les contextes explicitement, vous pouvez également supprimer ces contextes lorsque vous quittez l'application.

Toutefois, avec les noyaux PySpark et Spark, étant donné que les contextes sont prédéfinis, vous ne pouvez pas supprimer explicitement le contexte. Par conséquent, si vous fermez simplement le bloc-notes, le contexte peut toujours être en cours d'exécution, consommant ainsi vos ressources de cluster. Une bonne pratique avec les noyaux PySpark et Spark consiste à utiliser l’option **Fermer et arrêter** à partir du menu **Fichier** du bloc-notes. Cela supprime le contexte puis ferme le bloc-notes.


## Voici quelques exemples :

Lorsque vous ouvrez un bloc-notes Jupyter, deux dossiers sont disponibles au niveau racine.

* Le dossier **Python** comprend des exemples de blocs-notes qui utilisent le noyau **Python2** par défaut.
* Le dossier **Scala** comprend des exemples de blocs-notes qui utilisent le nouveau noyau **Spark**.

Vous pouvez ouvrir le même (par exemple, **GUIDE DE DÉMARRAGE – Découvrez les principes fondamentaux de Spark sur HDInsight**) bloc-notes à partir des deux dossiers pour voir comment le bloc-notes Python2 commence toujours par définir les contextes requis, tandis que le bloc-notes Spark utilise simplement les contextes prédéfinis.

## Commentaires

Les nouveaux noyaux n’en sont qu’à leurs balbutiements et évolueront au fil du temps. Les API pourront également être amenés à évoluer au fur et à mesure des évolutions des noyaux. Nous aimerions recevoir vos commentaires concernant l'utilisation de ces nouveaux noyaux. Cela nous sera très utile pour préparer la version finale de ces noyaux. Vous pouvez laisser vos commentaires/remarques sous la section **Commentaires** en dessous de cet article.


## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scénarios

* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Créer et exécuter des applications

* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécuter des tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Outils et extensions

* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

### Gestion des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0211_2016-->