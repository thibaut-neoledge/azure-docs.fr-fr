 <properties
	pageTitle="Créer des applications Spark Scala à l’aide d’HDInsight Tools dans le kit de ressources pour Eclipse | Microsoft Azure"
	description="Apprenez à créer une application Spark autonome à exécuter sur un cluster HDInsight Spark."
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
	ms.date="08/30/2016"
	ms.author="nitinme"/>


# Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse afin de créer des applications Spark pour un cluster Linux HDInsight Spark

Cet article fournit des instructions pas à pas sur le développement d’applications Spark écrites en Scala et leur envoi à un cluster HDInsight Spark à l’aide d’HDInsight Tools dans le kit de ressources Azure pour Eclipse. Vous pouvez utiliser ces outils de différentes manières :

* Pour développer une application Spark Scala et l’envoyer à un cluster HDInsight Spark
* Pour accéder à vos ressources de cluster Azure HDInsight Spark
* Pour développer et exécuter une application Spark Scala localement

>[AZURE.IMPORTANT] Cet outil peut être utilisé pour créer des applications et les envoyer à un cluster HDInsight Spark sur Linux uniquement.


##Conditions préalables

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un cluster Apache Spark sur HDInsight Linux. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

* Kit de développement Java d’Oracle versions 7 et 8.
	* Le **Kit de développement logiciel (SDK) Java version 7** est utilisé pour la compilation de projets Spark, car les clusters HDInsight prennent en charge Java version 7. Vous pouvez télécharger le Kit de développement logiciel (SDK) Java version 7 [ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).
	* Le **Kit de développement logiciel (SDK) Java version 8** est utilisé pour l’exécution IDE d’Eclipse. Vous pouvez le télécharger [ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* IDE Eclipse. Cet article utilise Eclipse Neon. Vous pouvez l’installer [ici](https://www.eclipse.org/downloads/).

* IDE Scala pour Eclipse.
	* **Si vous avez l’IDE Eclipse installé**, vous pouvez ajouter le plug-in IDE Scala en accédant à **Help** (Aide) -> **Install New Software** (Installer un nouveau logiciel) et en ajoutant [http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site) en tant que source pour télécharger le plug-in Scala pour Eclipse.
	* **Si l’IDE Eclipse n’est pas installé**, vous pouvez installer l’IDE Scala directement à partir de [cet emplacement](http://scala-ide.org/download/sdk.html). Vous pouvez télécharger le fichier .zip à partir de ce lien, l’extraire, accéder au dossier **/eclipse**, puis exécuter le fichier **eclipse.exe** à partir de cet emplacement.
	
	>[AZURE.NOTE] Les étapes décrites dans ce document reposent sur l’utilisation de l’IDE Eclipse avec le plug-in Scala installé.

* Kit de développement logiciel (SDK) Spark. Vous pouvez le télécharger [ici](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).

* Installez e(fx)clipse à partir de [https://www.eclipse.org/efxclipse/install.html](https://www.eclipse.org/efxclipse/install.html).


## Installation d’HDInsight Tools dans le kit de ressources Azure pour Eclipse

HDInsight Tools pour Eclipse est disponible dans le cadre du kit de ressources Azure pour Eclipse. Pour obtenir des instructions sur l’installation du kit de ressources Azure, consultez [Installation du kit de ressources Azure pour Eclipse](../azure-toolkit-for-eclipse-installation.md).

## Connexion à votre abonnement Azure

1. Lancez l’IDE Eclipse et ouvrez Azure Explorer. Dans le menu **Window** (Fenêtre) de l’IDE, cliquez sur **Show View** (Afficher la vue), puis cliquez sur **Other** (Autre). Dans la boîte de dialogue qui s’ouvre, développez **Azure**, cliquez sur **Azure Explorer**, puis cliquez sur **OK**.

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)

2. Cliquez avec le bouton droit sur le nœud **Azure** dans **Azure Explorer**, puis cliquez sur **Gérer les abonnements**.

3. Dans la boîte de dialogue **Gérer les abonnements**, cliquez sur **Se connecter** et entrez vos informations d’identification Azure.

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)

4. Une fois que vous êtes connecté, la boîte de dialogue **Gérer les abonnements** répertorie tous les abonnements Azure associés aux informations d’identification. Cliquez sur **Fermer** dans la boîte de dialogue.

5. Dans l’onglet Azure Explorer, développez **HDInsight** pour afficher les clusters HDInsight Spark de votre abonnement.

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)

6. Vous pouvez développer davantage un nœud de nom de cluster pour voir les ressources (par exemple, les comptes de stockage) associées au cluster.

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## Configuration d’un projet Spark Scala pour un cluster HDInsight Spark

1. À partir de l’espace de travail IDE d’Eclipse, cliquez sur **File** (Fichier), sur **New** (Nouveau), puis sur **Project** (Projet).

2. Dans l’Assistant **New Project** (Nouveau projet), développez **HDInsight**, sélectionnez **Spark on HDInsight (Scala)** (Spark sur HDInsight (Scala)), puis cliquez sur **Next** (Suivant).

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

3. Dans la boîte de dialogue **New HDInsight Scala Project** (Nouveau projet HDInsight Scala), entrez ou sélectionnez les valeurs comme l’indique l’image ci-dessous, puis cliquez sur **Next** (Suivant).

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

	* Entrez un nom pour le projet.
	* Dans la zone **JRE**, vérifiez que l’option **Use an execution environment JRE** (Utiliser un environnement d’exécution JRE) est définie sur **JavaSE-1.7**.
	* Vérifiez que le Kit de développement logiciel (SDK) Spark est défini à l’emplacement où vous avez téléchargé le Kit de développement logiciel (SDK). Le lien vers l’emplacement de téléchargement est inclus dans les [Conditions préalables](#prerequisites) plus haut dans cette rubrique. Vous pouvez également télécharger le Kit de développement logiciel (SDK) à partir du lien inclus dans cette boîte de dialogue, comme illustré dans l’image ci-dessus.

4. Dans la boîte de dialogue suivante, cliquez sur l’onglet **Libraries** (Bibliothèques), puis double-cliquez sur **JRE System Library [JavaSE-1.7]**.

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)

5. Dans la boîte de dialogue **Edit Library** (Modifier la bibliothèque), vérifiez que l’option **Execution Environment** (Environnement d’exécution) est définie sur **JavaSE-1.7(jdk1.7.0\_79)**. Si cette option n’est pas disponible, procédez comme suit.

	1. Sélectionnez l’option **Alternate JRE** (Autre JRE) et voyez si **JavaSE-1.7(jdk1.7.0\_79)** est disponible.
	2. Dans le cas contraire, cliquez sur le bouton **Installed JREs** (JRE installés).

		  ![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-5.png)

	3. Dans la boîte de dialogue **Installed JREs** (JRE installés), cliquez sur **Add** (Ajouter).

		  ![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-6.png)

	4. Dans la boîte de dialogue **JRE Type** (Type JRE), sélectionnez **Standard VM** (Machine virtuelle standard), puis cliquez sur **Next** (Suivant).

		  ![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-7.png)

	5. Dans la boîte de dialogue **JRE Definition** (Définition JRE), cliquez sur Directory (Répertoire), puis accédez à l’emplacement d’installation de JDK 7 et sélectionnez le dossier racine de **jdk1.7.0\_79**.

		  ![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-8.png)

	6. Cliquez sur **Terminer**. Dans la boîte de dialogue **Installed JREs** (JRE installés), sélectionnez l’environnement JRE nouvellement ajouté, puis cliquez sur **OK**.

		   ![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-9.png)

	7. L’environnement JRE récemment ajouté doit être répertorié sous **Execution Environment** (Environnement d’exécution). Cliquez sur **Finish**.

	  	   ![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-10.png)

6. Dans l’onglet **Libraries** (Bibliothèques), double-cliquez sur **Scala Library Container[2.11.8]** (Conteneur de bibliothèques Scala[2.11.8]). Dans la boîte de dialogue **Edit Library** (Modifier la bibliothèque), sélectionnez **Fixed Scala Library container:2.10.6** (Conteneur de bibliothèques Scala fixe:2.10.6).

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-11.png)

	Cliquez sur **Finish** (Terminer) pour quitter la boîte de dialogue des paramètres du projet.

## Création d’une application Scala pour un cluster HDInsight Spark

1. Dans l’IDE Eclipse déjà ouvert, dans **Package Explorer** (Explorateur de packages), développez le projet créé précédemment, cliquez sur **src**, pointez sur **New** (Nouveau), puis cliquez sur **Other** (Autre).

2. Dans la boîte de dialogue **Select a wizard** (Sélectionner un Assistant), développez **Scala Wizards** (Assistants Scala), cliquez sur **Scala Object** (Objet Scala), puis cliquez sur **Next** (Suivant).

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)

3. Dans la boîte de dialogue **Create New File** (Créer un nouveau fichier), entrez un nom pour l’objet, puis cliquez sur **Finish** (Terminer).

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)

4. Collez le code suivant dans l’éditeur de texte.

		import org.apache.spark.SparkConf
		import org.apache.spark.SparkContext
	
		object MyClusterApp{
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("MyClusterApp")
		    val sc = new SparkContext(conf)
		
		    val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
		
		    rdd1.saveAsTextFile("wasbs:///HVACOut")
		  }		
		}


5. Exécutez l’application sur un cluster HDInsight Spark.

	1. Dans **Package Explorer** (Explorateur de packages), cliquez avec le bouton droit sur le nom du projet et sélectionnez **Submit Spark Application to HDInsight** (Envoyer l’application Spark à HDInsight).

	2. Dans la boîte de dialogue **Spark Submission** (Envoi Spark), entrez les valeurs suivantes.

		* Pour **Cluster Name** (Nom du cluster), sélectionnez le cluster HDInsight Spark sur lequel vous souhaitez exécuter votre application.

		* Vous devez sélectionnez un artefact à partir du projet Eclipse ou en choisir un sur le disque dur.

		* En regard de la zone de texte **Main class name** (Nom de la classe principale), entrez le nom de l’objet spécifié dans le code (voir l’image ci-dessous).

			![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)

		* Étant donné que le code d’application dans cet exemple n’exige aucun argument de ligne de commande et qu’il ne référence pas de fichiers JAR ou d’autres fichiers, vous pouvez laisser les autres zones de texte vides.

		* Cliquez sur **Envoyer**.

	3. L’onglet **Spark Submission** (Envoi Spark) doit commencer à afficher la progression. Vous pouvez arrêter l’application en cliquant sur le bouton rouge dans la fenêtre « Spark Submission ». Vous pouvez également afficher les journaux pour cette exécution d’application spécifique en cliquant sur l’icône en forme de globe (indiquée par la zone bleue dans l’image).

        ![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

    Dans la section suivante, vous allez apprendre à accéder à la sortie du travail à l’aide d’HDInsight Tools dans le kit de ressources Azure pour Eclipse.


## Accéder aux clusters HDInsight Spark et les gérer à l’aide d’HDInsight Tools dans le kit de ressources Azure pour Eclipse

Vous pouvez effectuer diverses opérations à l’aide d’HDInsight Tools.

### Accéder au conteneur de stockage du cluster

1. Dans Azure Explorer, développez le nœud racine **HDInsight** pour afficher la liste des clusters HDInsight Spark disponibles.

3. Développez le nom de cluster pour voir le compte de stockage et le conteneur de stockage par défaut du cluster.

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)

4. Cliquez sur le nom du conteneur de stockage associé au cluster. Le volet droit doit comporter un dossier appelé **HVACOut**. Double-cliquez sur ce dossier pour l’ouvrir et visualiser une série de fichiers **part-***. Ouvrez un de ces fichiers pour afficher la sortie de l’application.

### Accéder au serveur d’historique Spark

1. Dans **Azure Explorer**, cliquez avec le bouton droit sur le nom de votre cluster Spark et sélectionnez **Open Spark History UI** (Ouvrir l’interface utilisateur de l’historique Spark). Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous devez les avoir spécifiées au moment de l’approvisionnement du cluster.

2. Dans le tableau de bord Serveur d’historique Spark, vous pouvez rechercher le nom de l’application que vous venez d’exécuter. Dans le code ci-dessus, vous avez défini le nom de l’application en utilisant la syntaxe `val conf = new SparkConf().setAppName("MyClusterApp")`. Le nom de votre application Spark était donc **MyClusterApp**.

### Lancez le portail Ambari

Dans **Azure Explorer**, cliquez avec le bouton droit sur le nom de votre cluster Spark et sélectionnez **Open Cluster Management Portal (Ambari)** (Ouvrir le portail de gestion des clusters (Ambari)). Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous devez les avoir spécifiées au moment de l’approvisionnement du cluster.

### Gérer les abonnements Azure

Par défaut, HDInsight Tools dans le kit de ressources Azure pour Eclipse répertorie les clusters Spark à partir de tous vos abonnements Azure. Si nécessaire, vous pouvez spécifier les abonnements pour lesquels vous souhaitez accéder au cluster. Dans **Azure Explorer**, cliquez avec le bouton droit sur le nœud racine **Azure**, puis cliquez sur **Manage Subscriptions** (Gérer les abonnements). Dans la boîte de dialogue, décochez les cases concernant l’abonnement auquel vous ne souhaitez pas accéder, puis cliquez sur **Close** (Fermer). Vous pouvez également cliquer sur **Sign Out** (Déconnexion) si vous souhaitez vous déconnecter de votre abonnement Azure.


## Exécuter une application Spark Scala localement

Vous pouvez utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse afin d’exécuter des applications Spark Scala localement sur votre poste de travail. En règle générale, ces applications n’ont pas besoin d’accéder aux ressources de cluster telles que le conteneur de stockage et peuvent être exécutées et testées localement.

### Configuration requise

Quand vous exécutez l’application Spark Scala locale sur un ordinateur Windows, vous pouvez obtenir une exception liée à l’absence d’un fichier **WinUtils.exe** sur le système d’exploitation Windows, comme l’explique le document [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Pour résoudre cette erreur, vous devez [télécharger le fichier exécutable à partir d’ici](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) vers un emplacement tel que **C:\\WinUtils\\bin**. Vous devez ensuite ajouter une variable d’environnement **HADOOP\_HOME** et définir la valeur de la variable sur **C\\WinUtils**.

### Exécuter une application Spark Scala locale	 

1. Lancez Eclipse et créez un nouveau projet. Dans la boîte de dialogue Nouveau projet, choisissez les options suivantes, puis cliquez sur **Suivant**.

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

	* Dans le volet gauche, sélectionnez **HDInsight**.
	* Dans le volet droit, sélectionnez **Spark on HDInsight Local Run Sample (Scala)** (Exemple d’exécution locale de Spark sur HDInsight (Scala)).
	* Cliquez sur **Suivant**.

2. Pour fournir les détails du projet, suivez les étapes 3 à 6 comme indiqué précédemment dans la section [Configuration d’un projet d’application Spark Scala pour un cluster HDInsight Spark](#set-up-a-spark-scala-application-project-for-an-hdinsight-spark cluster).

3. Le modèle ajoute un exemple de code (**LogQuery**) sous le dossier **src** que vous pouvez exécuter localement sur votre ordinateur.

	![Résultat de l’exécution locale de l’application Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)

4.  Cliquez avec le bouton droit sur l’application **LogQuery**, pointez sur **Run As** (Exécuter en tant que), puis cliquez sur **1 Scala Application**. Une sortie semblable à celle-ci doit apparaître dans l’onglet **Console** en bas de l’écran.

	![Résultat de l’exécution locale de l’application Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## Commentaires et problèmes connus

Pour l’instant, la visualisation directe des sorties Spark n’est pas prise en charge. Nous travaillons à la résolution de ce problème.

Si vous avez des suggestions ou des commentaires, ou que vous rencontrez des problèmes lors de l’utilisation de cet outil, n’hésitez pas à nous envoyer un e-mail à l’adresse hdivstool@microsoft.com.

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

* [Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gestion des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->