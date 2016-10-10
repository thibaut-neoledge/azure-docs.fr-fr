<properties 
	pageTitle="Installer le bloc-notes Jupyter sur votre ordinateur et le connecter à un cluster HDInsight Spark | Microsoft Azure" 
	description="Découvrez comment installer le bloc-notes Jupyter localement sur votre ordinateur et le connecter à un cluster Apache Spark sur Azure HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="jhubbard" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/26/2016" 
	ms.author="nitinme"/>


# Installation du bloc-notes Jupyter sur votre ordinateur et connexion au cluster Apache Spark sur HDInsight Linux

Dans cet article, vous allez apprendre à installer le bloc-notes Jupyter avec les noyaux PySpark (pour Python) et Spark (pour Scala) personnalisés avec Spark magic et connecter le bloc-notes à un cluster HDInsight. Plusieurs raisons peuvent motiver l’installation de Jupyter sur votre ordinateur local. Différents défis peuvent également se présenter. La section [Pourquoi dois-je installer Jupyter sur mon ordinateur ?](#why-should-i-install-jupyter-on-my-computer), à la fin de cet article, présente une liste de ces raisons et défis.

L’installation de Jupyter et de Spark magic sur votre ordinateur comprend trois étapes essentielles.

* Installation du bloc-notes Jupyter
* Installation des noyaux PySpark et Spark avec Spark Magic
* Configuration de Spark Magic pour accéder au cluster Spark sur HDInsight

Pour plus d’informations sur les noyaux personnalisés et Spark Magic disponible pour les blocs-notes Jupyter avec le cluster HDInsight, consultez [Noyaux disponibles pour les blocs-notes Jupyter avec les clusters HDInsight Spark Linux sur HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

##Composants requis

La configuration requise indiquée ici ne concerne pas l’installation de Jupyter. Elle s’applique à la connexion du bloc-notes Jupyter à un cluster HDInsight une fois le bloc-notes installé.

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark sur HDInsight Linux. Pour obtenir des instructions, consultez [Créer des clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Installer le bloc-notes Jupyter sur votre ordinateur

Vous devez installer Python avant de pouvoir installer les blocs-notes Jupyter. Python et Jupyter font partie de la [distribution Ananconda](https://www.continuum.io/downloads). Lorsque vous installez Anaconda, vous installez en réalité une distribution de Python. Une fois Anaconda installé, vous ajoutez l’installation de Jupyter en exécutant une commande. Cette section fournit les instructions à suivre.

1. Téléchargez le [programme d’installation Anaconda](https://www.continuum.io/downloads) pour votre plateforme et exécutez le programme d’installation. Lors de l’exécution de l’assistant d’installation, veillez à sélectionner l’option permettant d’ajouter Anaconda à votre variable PATH.

2. Exécutez la commande suivante pour installer Jupyter.

		conda install jupyter

	Pour plus d’informations sur l’installation de Jupyter, consultez [Installing Jupyter using Anaconda](http://jupyter.readthedocs.io/en/latest/install.html) (Installation de Jupyter à l’aide d’Anaconda).

## Installer les noyaux et Spark Magic

Pour obtenir des instructions sur l’installation des noyaux Spark Magic, PySpark et Spark, consultez la [documentation sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) sur GitHub.

## Configurer Spark Magic pour accéder au cluster Spark sur HDInsight

Dans cette section, vous configurez Spark Magic que vous avez installé précédemment pour qu’il se connecte à un cluster Apache Spark que vous devez avoir déjà créé dans Azure HDInsight.

1. Les informations de configuration de Jupyter sont généralement stockées dans le répertoire de base des utilisateurs. Pour localiser votre répertoire de base sur n’importe quelle plateforme de système d’exploitation, tapez les commandes suivantes.

	Démarrez l’interface de Python. Dans la fenêtre de commande, tapez la commande suivante :

		python

	Dans l’interface de Python, entrez la commande suivante pour rechercher le répertoire de base.

		import os
		print os.path.expanduser('~')

2. Accédez au répertoire de base et créez un dossier nommé **.sparkmagic** s’il n’existe pas.

3. Dans le dossier, créez un fichier appelé **config.json** et ajoutez-y l’extrait de code JSON suivant.

		{
		  "kernel_python_credentials" : {
		    "username": "{USERNAME}",
		    "base64_password": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  },
		  "kernel_scala_credentials" : {
		    "username": "{USERNAME}",
		    "base64_password": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  }
		}

4. Remplacez **{USERNAME}**, **{CLUSTERDNSNAME}**, et **{BASE64ENCODEDPASSWORD}** par les valeurs appropriées. Vous pouvez utiliser un certain nombre d’utilitaires dans votre langage de programmation favori ou en ligne pour générer un mot de passe codé en base64 pour votre mot de passe actuel. Voici ce à quoi pourrait ressembler un extrait de code Python simple à exécuter à partir de l’invite de commandes :

		python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Démarrez Jupyter. Utilisez la commande suivante depuis l’invite de commandes :

		jupyter notebook

6. Vérifiez que vous pouvez vous connecter au cluster à l’aide du bloc-notes Jupyter et que vous pouvez utiliser Spark Magic disponible avec les noyaux. Procédez comme suit.

	1. Créer un nouveau bloc-notes. Dans le coin inférieur droit, cliquez sur **Nouveau**. Le noyau par défaut **Python2** doit s’afficher, ainsi que les deux nouveaux noyaux que vous installez : **PySpark** et **Spark**.

		![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Créer un bloc-notes Jupyter")

	
		Cliquez sur **PySpark**.


	2. Exécutez l’extrait de code suivant :

			%%sql
			SELECT * FROM hivesampletable LIMIT 5

		Votre connexion au cluster HDInsight sera testée, si vous parvenez à récupérer la sortie.

	>[AZURE.TIP] Si vous souhaitez mettre à jour la configuration du bloc-notes pour vous connecter à un autre cluster, mettez à jour le fichier config.json en tenant compte du nouveau jeu de valeurs, comme indiqué à l’étape 3 ci-dessus.

## Pourquoi dois-je installer Jupyter sur mon ordinateur ?

Plusieurs raisons peuvent motiver l’installation de Jupyter sur votre ordinateur et sa connexion à un cluster Spark sur HDInsight.

* Même si les blocs-notes Jupyter sont déjà disponibles sur le cluster Spark dans Azure HDInsight, l’installation de Jupyter sur votre ordinateur vous permet de créer vos blocs-notes localement, de tester votre application sur un cluster en cours d’exécution, puis de charger les blocs-notes sur le cluster. Vous pouvez charger les blocs-notes dans le cluster à l’aide du bloc-notes Jupyter en cours d’exécution ou du cluster, ou en les enregistrant dans le dossier /HdiNotebooks du compte de stockage associé au cluster. Pour plus d’informations sur le stockage des blocs-notes, consultez [Where are Jupyter notebooks stored](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored) (Où sont stockés les blocs-notes Jupyter) ?
* Les blocs-notes étant disponibles localement, vous pouvez vous connecter à différents clusters Spark selon les besoins de votre application.
* Vous pouvez utiliser GitHub pour implémenter un système de contrôle de code source et disposer du contrôle de version pour les blocs-notes. Vous pouvez également disposer d’un environnement de collaboration où plusieurs utilisateurs peuvent travailler avec le même bloc-notes.
* Vous pouvez travailler avec les blocs-notes localement sans avoir un cluster activé. Il vous suffit d’un cluster avec lequel tester vos blocs-notes, sans avoir à gérer manuellement vos blocs-notes ou à disposer d’un environnement de développement.
* Il peut être plus facile de configurer votre propre environnement de développement local que de configurer l’installation de Jupyter sur le cluster. Vous pouvez tirer parti de tous les logiciels que vous avez installés localement sans configurer un ou plusieurs clusters distants.

>[AZURE.WARNING] Lorsque Jupyter est installé sur votre ordinateur local, plusieurs utilisateurs peuvent exécuter le même bloc-notes sur le même cluster Spark en même temps. Dans ce cas, plusieurs sessions Livy sont créées. Si vous rencontrez un problème et que vous souhaitez effectuer un débogage, il sera difficile de savoir quelle session Livy appartient à quel utilisateur.




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

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely) (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### Gestion des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0928_2016-->