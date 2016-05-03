 <properties
	pageTitle="Créer des applications Spark Scala à l’aide du plug-in HDInsight pour IntelliJ IDEA | Microsoft Azure"
	description="Apprenez à créer une application Spark autonome à exécuter sur un cluster HDInsight Spark."
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
	ms.date="04/14/2016" 
	ms.author="nitinme"/>


# Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA afin de créer des applications Spark Scala pour un cluster Linux HDInsight Spark (version préliminaire)

Cet article fournit des instructions pas à pas sur le développement d’applications Spark écrites en Scala et leur envoi à un cluster HDInsight Spark à l’aide du plug-in HDInsight pour IntelliJ IDEA. Vous pouvez utiliser le plug-in de différentes manières :

* Pour développer une application Spark Scala et l’envoyer à un cluster HDInsight Spark
* Pour accéder à vos ressources de cluster Azure HDInsight Spark
* Pour développer et exécuter une application Spark Scala localement

Vous pouvez également visionner une vidéo [ici](https://mix.office.com/watch/1nqkqjt5xonza) pour commencer.

>[AZURE.IMPORTANT] Cet outil peut être utilisé pour créer des applications et les envoyer à un cluster HDInsight Spark sur Linux uniquement.


**Configuration requise**

* Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight Linux. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de développement logiciel (SDK) Oracle Java. Vous pouvez l’installer [ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. Cet article utilise la version 15.0.1. Vous pouvez l’installer [ici](https://www.jetbrains.com/idea/download/).

## Installer le plug-in Outils HDInsight pour IntelliJ IDEA

1. Si vous vous trouvez de nouveau dans l’écran d’accueil IDEA, cliquez sur **Configure**, puis sur **Plugins**.

2. Dans l’écran suivant, cliquez sur **Browse Repositories** dans le coin inférieur gauche. Dans la boîte de dialogue **Browse Repositories** qui s’affiche, recherchez **HDInsight**, sélectionnez **Microsoft Azure HDInsight Tools for IntelliJ (Preview)**, puis cliquez sur **Install**. Le plug-in est dépendant du plug-in Scala ; par conséquent, si le plug-in Scala n’est pas installé, vous serez également invité à installer ce dernier.

	![Installer le plug-in HDInsight](./media/hdinsight-apache-spark-intellij-tool-plugin/install-hdinsight-plugin.png)

3. Quand vous y êtes invité, cliquez sur le bouton **Restart IntelliJ IDEA** pour redémarrer l’environnement de développement intégré (IDE).


## Exécuter une application Scala Spark sur un cluster HDInsight Spark

1. Lancez IntelliJ IDEA et créez un nouveau projet. Dans la boîte de dialogue Nouveau projet, choisissez les options suivantes, puis cliquez sur **Suivant**.

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* Dans le volet gauche, sélectionnez **HDInsight**.
	* Dans le volet droit, sélectionnez **Spark on HDInsight (Scala)**.
	* Cliquez sur **Next**.

2. Dans la fenêtre suivante, fournissez les informations de projet.

	* Fournissez un nom de projet et un emplacement de projet.
	* Pour **Project SDK**, prenez soin d’indiquer une version de Java supérieure à la version 7.
	* Pour **Scala SDK**, cliquez sur **Create**, cliquez sur **Download**, puis sélectionnez la version de Scala à utiliser. **Assurez-vous que vous n’utilisez pas la version 2.11.x**. Cet exemple utilise la version **2.10.6**.

		![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Pour **Spark SDK**, téléchargez et utilisez le Kit de développement logiciel (SDK) disponible [ici](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Vous pouvez également ignorer ce dernier et utiliser le [référentiel Spark Maven](http://mvnrepository.com/search?q=spark) à la place ; toutefois, vérifiez que vous disposez du référentiel Maven approprié pour développer vos applications Spark. (Par exemple, vous devez vous assurer que le module Spark Streaming est installé si vous utilisez la fonctionnalité de diffusion en continu de Spark. En outre, vérifiez que vous utilisez le référentiel libellé Scala 2.10 ; n’utilisez pas le référentiel Scala 2.11.)

		![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* Cliquez sur **Terminer**.

3. Le projet Spark crée automatiquement un artefact à votre intention. Vous pouvez également créer votre propre artefact en procédant comme suit :

	1. Dans le menu **Fichier**, cliquez sur **Structure de projet**.
	2. Dans la boîte de dialogue **Project Structure** (Structure de projet), cliquez sur **Artifacts** (Artefacts), puis sur le signe plus. Dans la boîte de dialogue contextuelle, cliquez sur **JAR**, puis sur **Empty**.

		![Créer un fichier JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Entrez un nom pour le fichier JAR (par exemple, **MyClusterApp**). Dans le volet Available Elements, cliquez avec le bouton droit sur **’MyClusterApp’ compile output**, puis cliquez sur **Put into Output Root**.

		![Créer un fichier JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-2.png)

	4. Cliquez sur **Apply**, puis sur **OK**.

4. Ajoutez le code source de votre application.

	1. Dans l’**Explorateur de projets**, cliquez avec le bouton droit sur **src**, pointez sur **New**, puis cliquez sur **Scala class**.

		![Ajouter le code source](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. Dans la boîte de dialogue **Create New Scala Class**, indiquez un nom, dans la zone **Kind**, sélectionnez **Object**, puis cliquez sur **OK**.

		![Ajouter le code source](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. Dans le fichier **MyClusterApp.scala**, collez le code suivant. Ce code lit les données du fichier HVAC.csv (disponible sur tous les clusters HDInsight Spark), récupère les lignes qui contiennent uniquement un chiffre dans la septième colonne du fichier CSV et écrit la sortie dans **/HVACOut**, sous le conteneur de stockage par défaut du cluster.


			import org.apache.spark.SparkConf
			import org.apache.spark.SparkContext

			object MyClusterApp{
			  def main (arg: Array[String]): Unit = {
			    val conf = new SparkConf().setAppName("MyClusterApp")
			    val sc = new SparkContext(conf)

			    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

			    //find the rows which have only one digit in the 7th column in the CSV
				val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

			    rdd1.saveAsTextFile("wasb:///HVACOut")
			  }

			}

5. Exécutez l’application sur un cluster HDInsight Spark.

	1. Dans l’**Explorateur de projets**, cliquez avec le bouton droit sur le nom du projet et sélectionnez **Submit Spark Application to HDInsight**.

		![Envoyer l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Vous êtes invité à entrer les informations d’identification de votre abonnement Azure. Dans la boîte de dialogue **Spark Submission**, entrez les valeurs suivantes.

		![Envoyer l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* Dans la zone **Spark clusters (Linux only)**, sélectionnez le cluster HDInsight Spark sur lequel vous souhaitez exécuter votre application.

		* Vous devez sélectionnez un artefact à partir du projet IntelliJ, ou en choisir un sur le disque dur.

		* En regard de la zone de texte **Main class name**, cliquez sur le bouton de sélection (![points de suspension (bouton de sélection)](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), sélectionnez la classe principale dans le code source de votre application, puis cliquez sur **OK**.

			![Envoyer l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

		* Étant donné que le code d’application dans cet exemple n’exige aucun argument de ligne de commande et qu’il ne référence pas de fichiers JAR ou d’autres fichiers, vous pouvez laisser les autres zones de texte vides.

		* Cliquez sur **Envoyer**.

	3. L’onglet **Spark Submission** en bas de la fenêtre doit commencer à afficher la progression. Vous pouvez également arrêter l’application en cliquant sur le bouton rouge dans la fenêtre « Spark Submission ».

        ![Résultat de l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

    Dans la section suivante, vous allez apprendre à accéder à la sortie du travail à l’aide du plug-in HDInsight pour IntelliJ IDEA.


## Accéder aux clusters HDInsight Spark et gérer ceux-ci à l’aide du plug-in HDInsight pour IntelliJ

Vous pouvez effectuer diverses opérations à l’aide du plug-in HDInsight.

### Accéder au conteneur de stockage du cluster

1. Dans le menu **View**, pointez sur **Tool Windows**, puis cliquez sur **HDInsight Explorer**. Si vous y êtes invité, entrez les informations d’identification nécessaires pour accéder à votre abonnement Azure.

2. Développez le nœud racine **HDInsight** pour afficher la liste des clusters HDInsight Spark disponibles.

3. Développez le nom de cluster pour voir le compte de stockage et le conteneur de stockage par défaut du cluster.

	![Accéder à l’espace de stockage du cluster](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. Cliquez sur le nom du conteneur de stockage associé au cluster. Le volet droit doit comporter un dossier appelé **HVACOut**. Double-cliquez sur ce dossier pour l’ouvrir et visualiser une série de fichiers **part-***. Ouvrez un de ces fichiers pour afficher la sortie de l’application.

### Accéder au serveur d’historique Spark

1. Dans l’**Explorateur HDInsight**, cliquez avec le bouton droit sur le nom de votre cluster Spark et sélectionnez **Open Spark History UI**. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous devez les avoir spécifiées au moment de l’approvisionnement du cluster.

2. Dans le tableau de bord Serveur d’historique Spark, vous pouvez rechercher le nom de l’application que vous venez d’exécuter. Dans le code ci-dessus, vous avez défini le nom de l’application en utilisant la syntaxe `val conf = new SparkConf().setAppName("MyClusterApp")`. Le nom de votre application Spark était donc **MyClusterApp**.

### Lancez le portail Ambari

Dans l’**Explorateur HDInsight**, cliquez avec le bouton droit sur le nom de votre cluster Spark et sélectionnez **Open Cluster Management Portal (Ambari)**. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous devez les avoir spécifiées au moment de l’approvisionnement du cluster.

### Gérer les abonnements Azure

Par défaut, le plug-in HDInsight répertorie les clusters Spark à partir de tous vos abonnements Azure. Si nécessaire, vous pouvez spécifier les abonnements pour lesquels vous souhaitez accéder au cluster. Dans l’**Explorateur HDInsight**, cliquez avec le bouton droit sur le nœud racine **HDInsight** et sélectionnez **Manage Subscriptions**. Dans la boîte de dialogue, décochez les cases concernant l’abonnement auquel vous ne souhaitez pas accéder, puis cliquez sur **Close**. Vous pouvez également cliquer sur **Sign Out** si vous souhaitez vous déconnecter de votre abonnement Azure.


## Exécuter une application Spark Scala localement

Vous pouvez utiliser le plug-in Outils HDInsight pour IntelliJ IDEA afin d’exécuter des applications Spark Scala localement sur votre poste de travail. En règle générale, ces applications n’ont pas besoin d’accéder aux ressources de cluster telles que le conteneur de stockage et peuvent être exécutées et testées localement.

### Configuration requise

Quand vous exécutez l’application Spark Scala locale sur un ordinateur Windows, vous pouvez obtenir une exception liée à l’absence d’un fichier WinUtils.exe sur Windows, comme expliqué dans le document [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Pour résoudre cette erreur, vous devez [télécharger le fichier exécutable à partir d’ici](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) vers un emplacement tel que **C:\\WinUtils\\bin**. Vous devez ensuite ajouter une variable d’environnement **HADOOP\_HOME** et définir la valeur de la variable sur **C\\WinUtils**.

### Exécuter une application Spark Scala locale	 

1. Lancez IntelliJ IDEA et créez un nouveau projet. Dans la boîte de dialogue Nouveau projet, choisissez les options suivantes, puis cliquez sur **Suivant**.

	![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

	* Dans le volet gauche, sélectionnez **HDInsight**.
	* Dans le volet droit, sélectionnez **Spark on HDInsight Local Run Sample (Scala)**.
	* Cliquez sur **Next**.

2. Suivez les étapes de la section « Exécuter une application Scala Spark sur un cluster HDInsight Spark » pour configurer les paramètres requis.

3. Exécutez l’application localement sur votre poste de travail. Cliquez avec le bouton droit sur l’application Scala et sélectionnez **Run 'LogQuery'**. Une sortie semblable à celle-ci doit apparaître dans l’onglet **Exécuter** en bas de l’écran.

	![Résultat de l’exécution locale de l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## Convertir des applications IntelliJ IDEA existantes pour qu’elles utilisent le plug-in Outils HDInsight

Vous pouvez également convertir vos applications Spark Scala existantes créées dans IntelliJ IDEA pour qu’elles soient compatibles avec le plug-in Outils HDInsight. Cette opération vous permet d’utiliser l’outil pour envoyer les applications à un cluster HDInsight Spark. Pour ce faire, vous pouvez procéder comme suit :

1. Pour une application Spark Scala existante créée à l’aide d’IntelliJ IDEA, ouvrez le fichier .iml associé.
2. Au niveau de la racine se trouve un élément **module** tel que celui-ci :

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. Ajoutez `UniqueKey="HDInsightTool"` à la fin de l’élément **module**, qui doit alors ressembler à ceci :

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. Enregistrez les modifications. Votre application doit maintenant être compatible avec le plug-in Outils HDInsight. Vous pouvez effectuer un test en cliquant avec le bouton droit sur le nom du projet dans l’explorateur de projets. Le menu contextuel doit désormais comporter l’option **Submit Spark Application to HDInsight**.

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

* [Exécuter des tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Outils et extensions

* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gérer des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0420_2016-->