<properties
	pageTitle="Envoi de travaux Spark à distance à l’aide de Livy | Microsoft Azure"
	description="Découvrez comment utiliser Livy avec les clusters HDInsight pour soumettre les travaux Spark à distance."
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
	ms.date="07/25/2016"
	ms.author="nitinme"/>


# Envoi de tâches Spark à distance à un cluster Apache Spark sous HDInsight Linux à l'aide de Livy

Un cluster Apache Spark sur Azure HDInsight inclut Livy, une interface REST permettant de soumettre des travaux à distance à un cluster Spark. Consultez la documentation détaillée sur Livy [ici](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Vous pouvez utiliser Livy pour exécuter des interpréteurs de commandes Spark interactifs ou soumettre des traitements par lots à exécuter sur Spark. Cet article traite de l’utilisation de Livy pour soumettre des traitements par lots. La syntaxe ci-dessous utilise Curl pour effectuer des appels REST au point de terminaison Livy.

**Configuration requise :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark sur HDInsight Linux. Pour obtenir des instructions, consultez [Créer des clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Soumettre un traitement par lots au cluster

Avant de soumettre un traitement par lots, vous devez télécharger le fichier .jar d’application sur le stockage associé au cluster. Pour ce faire, vous pouvez utiliser l’utilitaire de ligne de commande [**AzCopy**](../storage/storage-use-azcopy.md). De nombreux autres clients permettent également de télécharger des données. Pour en savoir plus à leur sujet, consultez [Téléchargement de données pour les travaux Hadoop dans HDInsight](hdinsight-upload-data.md).

	curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Exemples :**

* Si le fichier .jar se trouve sur le stockage de cluster (WASB)

		curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Si vous souhaitez transmettre le nom de fichier .jar et le nom de classe dans un fichier d’entrée (dans cet exemple, input.txt)

		curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## Obtenir des informations sur les lots exécutés sur le cluster

	curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exemples :**

* Si vous souhaitez récupérer tous les lots en cours d’exécution sur le cluster :

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Si vous souhaitez récupérer un lot spécifique avec un ID de lot donné

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## Supprimer un traitement par lots

	curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exemple** :

	curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## Livy et haute disponibilité

Livy assure une haute disponibilité des travaux Spark exécutés sur le cluster. Voici quelques exemples :

* Si le service Livy tombe en panne après avoir soumis un travail à distance à un cluster Spark, l’exécution du travail se poursuit en arrière-plan. La sauvegarde de Livy entraîne la restauration de l’état du travail et la création d’un rapport à ce sujet.

* Les blocs-notes Jupyter pour HDInsight sont alimentés par Livy sur le serveur principal. Si un bloc-notes exécute un travail Spark et que le service Livy est redémarré, le bloc-notes poursuit l’exécution des cellules de code.

## Afficher un exemple

Dans cette section, nous étudions des exemples sur l’utilisation de Livy pour soumettre une application Spark, surveiller la progression de l’application, puis supprimer le travail. L’application que nous utilisons dans cet exemple est décrite dans l’article [Créer une application Scala autonome et l’exécuter sur un cluster HDInsight Spark](hdinsight-apache-spark-create-standalone-application.md). Les étapes suivantes partent des suppositions ci-après :

* Vous avez déjà copié le fichier .jar de l’application dans le compte de stockage associé au cluster.
* CuRL est installé sur l’ordinateur sur lequel vous effectuez la procédure.

Procédez comme suit.

1. Vérifions tout d’abord que Livy est en cours d’exécution sur le cluster. Pour ce faire, nous pouvons obtenir une liste des lots en cours d’exécution. S’il s’agit de la première fois que vous exécutez un travail à l’aide de Livy, 0 doit être la valeur renvoyée.

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

	La sortie doit ressembler à ce qui suit :

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:47:53 GMT
		< Content-Length: 34
		<
		{"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	Notez la dernière ligne qui indique **total:0**, ce qui suggère qu’aucun lot n’est en cours d’exécution.

2. Soumettons à présent un traitement par lots. L’extrait de code ci-dessous utilise un fichier d’entrée (input.txt) pour transmettre le nom du fichier .jar et le nom de classe en tant que paramètres. Il s’agit de l’approche recommandée si vous exécutez ces étapes sur un ordinateur Windows.

		curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

	Les paramètres du fichier **input.txt** sont définis comme suit :

		{ "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

	Le résultat doit ressembler à ce qui suit :

		< HTTP/1.1 201 Created
		< Content-Type: application/json; charset=UTF-8
		< Location: /0
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:51:30 GMT
		< Content-Length: 36
		<
		{"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	Notez la dernière ligne de la sortie qui indique **state:starting**. Elle indique également**id:0**. Il s’agit de l’ID du lot.

3. Vous pouvez maintenant récupérer l’état de ce lot à l’aide de l’ID correspondant.

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

	Le résultat doit ressembler à ce qui suit :

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:54:42 GMT
		< Content-Length: 509
		<
		{"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	La sortie indique maintenant **state:success**, ce qui suggère que le travail a été exécuté correctement.

4. Si vous le souhaitez, vous pouvez maintenant supprimer le lot.

		curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

	Le résultat doit ressembler à ce qui suit :

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Sat, 21 Nov 2015 18:51:54 GMT
		< Content-Length: 17
		<
		{"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	La dernière ligne de la sortie indique que le lot a été supprimé. Si vous supprimez un travail pendant son exécution, celui-ci va s’arrêter. Si vous supprimez un travail qui s’est terminé, les informations du travail sont entièrement supprimées.

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

### Outils et extensions

* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gestion des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0727_2016-->