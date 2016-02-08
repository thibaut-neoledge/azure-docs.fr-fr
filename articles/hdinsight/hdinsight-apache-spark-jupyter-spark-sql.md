<properties
	pageTitle="Créer un cluster Spark sur HDInsight Linux et utiliser Spark SQL à partir de Jupyter pour effectuer une analyse interactive | Microsoft Azure"
	description="Des instructions pas à pas expliquent comment créer rapidement un cluster Apache Spark dans HDInsight, puis comment utiliser Spark SQL à partir des blocs-notes Jupyter pour exécuter des requêtes interactives."
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
	ms.date="01/13/2016"
	ms.author="nitinme"/>


# Prise en main : Créer un cluster Apache Spark sur Azure HDInsight (Linux) et exécuter des requêtes interactives à l’aide de Spark SQL

Découvrez comment créer un cluster Apache Spark dans HDInsight, puis comment utiliser le bloc-notes [Jupyter](https://jupyter.org) pour exécuter des requêtes interactives Spark SQL sur le cluster Spark.

   ![Prendre en main Apache Spark dans HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Didacticiel de prise en main d’Apache Spark dans HDInsight. Étapes illustrées : créer un compte de stockage ; créer un cluster ; exécuter des instructions Spark SQL")

**Configuration requise :**

- **Un abonnement Azure**. Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un client SSH (Secure Shell)** : les systèmes Linux, Unix et OS X fournissent un client SSH par le biais de la commande `ssh`. Pour les systèmes Windows, nous vous recommandons [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Clés SSH (Secure Shell) (facultatif)** : vous pouvez sécuriser le compte SSH utilisé pour se connecter au cluster à l’aide d’un mot de passe ou d’une clé publique. Avec un mot de passe, vous êtes rapidement opérationnel. Utilisez cette option pour pouvoir rapidement créer un cluster et exécuter certaines tâches de test. L’utilisation d’une clé est une méthode plus sécurisée, mais qui demande une installation supplémentaire. Elle convient dans le cadre de la création d’un cluster de production. Dans cet article, nous utilisons la méthode du mot de passe. Pour savoir comment créer et utiliser des clés SSH avec HDInsight, consultez les articles suivants :

	-  Depuis un ordinateur Linux : [Utilisation de HDInsight Linux (Hadoop) depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    
	-  Depuis un ordinateur Windows : [Utilisation de SSH avec HDInsight Linux (Hadoop) depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


## Créer un cluster Spark sur HDInsight Linux

Dans cette section, vous allez créer un cluster HDInsight version 3.3, qui est basé sur la version 1.5.1 de Spark. Pour en savoir plus sur les différentes versions de HDInsight et leurs contrats SLA, consultez la page [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE] Les étapes décrites dans cet article permettent de créer un cluster Apache Spark dans HDInsight à l’aide des paramètres de configuration de base. Pour plus d’informations sur les autres paramètres de configuration de cluster (comme l’utilisation d’un espace de stockage supplémentaire, d’un réseau virtuel Azure ou d’un metastore pour Hive), consultez [Créer des clusters Spark HDInsight à l’aide d’options personnalisées](hdinsight-hadoop-provision-linux-clusters.md).


**Pour créer un cluster Spark**

1. Connectez-vous au [portail Azure en version préliminaire](https://ms.portal.azure.com/).

2. Cliquez sur **NOUVEAU**, sur **Analyse des données**, puis sur **HDInsight**.

    ![Création d'un nouveau cluster dans la version préliminaire du portail Azure](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.1.png "Création d'un nouveau cluster dans la version préliminaire du portail Azure")

3. Entrez un **Nom de cluster**, sélectionnez **Hadoop** comme **Type de cluster**, à partir du menu déroulant **Système d’exploitation du cluster**, sélectionnez **Ubuntu**, puis la version de Spark. Une coche verte s’affiche en regard du nom de cluster s’il est disponible.

	![Saisir le nom et le type du cluster](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.2.png "Saisir le nom et le type du cluster")

4. Si vous avez plusieurs abonnements, cliquez sur l’entrée **Abonnement** pour sélectionner l’abonnement Azure à utiliser pour le cluster.

5. Cliquez sur **Groupe de ressources** pour afficher une liste des groupes de ressources existants, puis sélectionnez l’endroit où créer le cluster. Vous pouvez également cliquer sur **Créer un nouveau**, puis saisir le nom du nouveau groupe de ressources. Une coche verte s’affiche pour indiquer si le nouveau nom de groupe est disponible.

	> [AZURE.NOTE] Cette entrée correspond par défaut à l’un des groupes de ressources existants, si l’un d’eux est disponible.

6. Cliquez sur **Informations d’identification**, puis entrez un mot de passe pour l’utilisateur administrateur. Vous devez également entrer un **Nom d’utilisateur SSH**. Pour **Type d’authentification SSH**, cliquez sur **MOT DE PASSE** et spécifiez un mot de passe pour l’utilisateur SSH. Cliquez sur **Sélectionner** au bas de l’écran pour enregistrer la configuration des informations d’identification.

	![Fournir les informations d’identification du cluster](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.3.png "Fournir les informations d’identification du cluster")

    > [AZURE.NOTE] SSH permet d’accéder à distance au cluster HDInsight à l’aide d’une ligne de commande. Le nom d’utilisateur et le mot de passe que vous employez ici sont utilisés pour la connexion au cluster via SSH. Par ailleurs, le nom d’utilisateur SSH doit être unique, car il crée un compte d’utilisateur sur tous les nœuds du cluster HDInsight. Voici quelques-uns des noms de compte dont l’usage est réservé aux services sur le cluster et qui ne peuvent pas être utilisés en tant que nom d’utilisateur SSH :
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	Pour plus d'informations sur l'utilisation de SSH avec HDInsight, consultez l'un des articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Cliquez sur **Source de données** pour choisir une source de données existante pour le cluster ou en créer une. Lors de la création d’un cluster Hadoop dans HDInsight, vous spécifiez un compte Azure Storage. Un conteneur de stockage d'objets blob spécifique de ce compte est désigné en tant que système de fichiers par défaut, comme dans un système de fichiers distribués Hadoop (HDFS). Par défaut, le cluster HDInsight est créé dans le même centre de données que le compte de stockage que vous spécifiez. Pour plus d'informations, consultez [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

	![Panneau Source de données](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.4.png "Fournir la configuration de la source de données")

	Actuellement, vous pouvez sélectionner un compte de stockage Azure comme source de données pour un cluster HDInsight. Lisez ce qui suit pour comprendre à quoi correspondent les entrées du panneau **Source de données**.

	- **Méthode de sélection** : définissez cette propriété sur la valeur **De tous les abonnements** pour permettre l’exploration des comptes de stockage de tous vos abonnements. Affectez-lui la valeur **Clé d’accès** si vous souhaitez saisir le **Nom de stockage** et la **Clé d’accès** d’un compte de stockage existant.

	- **Sélectionner le compte de stockage / Créer un nouveau** : cliquez sur **Sélectionner le compte de stockage** pour parcourir et sélectionner un compte de stockage existant à associer au cluster. Vous pouvez également cliquer sur **Créer un nouveau** pour créer un nouveau compte de stockage. Utilisez le champ qui s’affiche pour saisir le nom du compte de stockage. Une coche verte s’affiche si le nom est disponible.

	- **Choisir le conteneur par défaut** : utilisez cette option pour saisir le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir n’importe quel nom, mais nous vous conseillons d’utiliser le même nom que le cluster pour pouvoir facilement reconnaître le conteneur utilisé pour ce cluster spécifique.

	- **Emplacement** : zone géographique dans laquelle le compte de stockage se trouve ou dans laquelle il sera créé.

		> [AZURE.IMPORTANT] La sélection de l’emplacement de la source de données par défaut définit également l’emplacement du cluster HDInsight. Le cluster et la source de données par défaut doivent se trouver dans la même zone géographique.

	Cliquez sur **Sélectionner** pour enregistrer la configuration de source de données.

8. Cliquez sur **Niveaux tarifaires de nœud** pour afficher des informations sur les nœuds qui seront créés pour ce cluster. Définissez le nombre de nœuds worker dont vous avez besoin pour le cluster. Le coût estimé du cluster s’affiche dans le panneau.

	![Panneau Niveaux de tarification du nœud](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.5.png "Spécification du nombre de nœuds de cluster")

	Cliquez sur **Sélectionner** pour enregistrer la configuration de tarification du nœud.

9. Dans le panneau **Nouveau cluster HDInsight**, vérifiez que l’option **Épingler au tableau d’accueil** est sélectionnée, puis cliquez sur **Créer**. Le cluster est créé et la vignette correspondante ajoutée au Tableau d’accueil de votre portail Azure. L'icône indique que le cluster est en cours de création et sera modifiée pour représenter l'icône HDInsight une fois la création terminée.

	| En cours de création | création terminée |
	| ------------------ | --------------------- |
	| ![Indicateur de création sur le tableau d’accueil](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioning.png) | ![Vignette de cluster approvisionné](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioned.png) |

	> [AZURE.NOTE] La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la vignette du tableau d’accueil ou l’entrée **Notifications** à gauche de la page pour vérifier le processus de création.

10. Une fois la création terminée, cliquez sur la vignette du cluster Spark dans le tableau d’accueil pour lancer le panneau du cluster.



## <a name="jupyter"></a>Exécuter des requêtes Spark SQL à l’aide d’un bloc-notes Jupyter

Dans cette section, vous allez utiliser un bloc-notes Jupyter pour exécuter des requêtes Spark SQL sur un cluster Spark.

1. Dans le tableau d’accueil du [portail Azure en version préliminaire](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous avez épinglé ce dernier au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   

2. Dans le panneau du cluster Spark, cliquez sur **Liens rapides**, puis dans le panneau **Tableau de bord du cluster**, cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

	> [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez __CLUSTERNAME__ par le nom de votre cluster.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis sur **Python 2**.

	![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

3. Un nouveau bloc-notes est créé et ouvert sous le nom Untitled.pynb. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial.

	![Fournir un nom pour le bloc-notes](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour le bloc-notes")

4. Importez les modules requis et créez les contextes Spark et SQL. Collez l’exemple de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *
		
		# Create Spark and SQL contexts
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)

	À chaque exécution d’une tâche dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **Python 2** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

	 ![État d’un travail de bloc-notes Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "État d’un travail de bloc-notes Jupyter")

4. Chargez un exemple de données dans une table temporaire. Lorsque vous créez un cluster Spark dans HDInsight, l’exemple de fichier de données **hvac.csv** est copié vers le compte de stockage associé dans **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**.

	Dans une cellule vide, collez l’exemple de code suivant puis appuyez sur **MAJ + ENTRÉE**. Cet exemple de code enregistre les données dans une table temporaire appelée **hvac**.

		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. Une fois le travail terminé, le résultat suivant s’affiche.

		+----------+---------+------+
		|buildingID|temp_diff|  date|
		+----------+---------+------+
		|         4|        8|6/1/13|
		|         3|        2|6/1/13|
		|         7|      -10|6/1/13|
		|        12|        3|6/1/13|
		|         7|        9|6/1/13|
		|         7|        5|6/1/13|
		|         3|       11|6/1/13|
		|         8|       -7|6/1/13|
		|        17|       14|6/1/13|
		|        16|       -3|6/1/13|
		|         8|       -8|6/1/13|
		|         1|       -1|6/1/13|
		|        12|       11|6/1/13|
		|         3|       14|6/1/13|
		|         6|       -4|6/1/13|
		|         1|        4|6/1/13|
		|        19|        4|6/1/13|
		|        19|       12|6/1/13|
		|         9|       -9|6/1/13|
		|        15|      -10|6/1/13|
		+----------+---------+------+


6. Une fois que vous avez terminé l’exécution de l’application, vous devez arrêter le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette opération permet d’arrêter et de fermer le bloc-notes.


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

### Extensions

* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gérer des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

### Problèmes connus

* [Problèmes connus d’Apache Spark dans Azure HDInsight (Linux)](hdinsight-apache-spark-known-issues.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0128_2016-->