<properties 
	pageTitle="Vue d’ensemble d’Apache Spark dans HDInsight | Microsoft Azure" 
	description="Présentation d’Apache Spark dans HDInsight et des scénarios dans lesquels utiliser Spark sur HDInsight dans vos applications." 
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

# Vue d’ensemble : Apache Spark sur Azure HDInsight (Linux)
 
<a href="http://spark.apache.org/" target="_blank">Apache Spark</a> est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d’analyse de données volumineuses. Le moteur de traitement Spark est élaboré pour permettre des analyses rapides, simples d’utilisation et sophistiquées. De par ses capacités de calcul en mémoire, Spark constitue le choix idéal pour les algorithmes itératifs utilisés dans les calculs d'apprentissage machine et de graphiques. Spark est également compatible avec le stockage des objets blob Azure (WASB) pour que vos données existantes stockées dans Azure puissent être facilement traitées via Spark.

Quand vous créez un cluster Spark dans HDInsight, vous créez des ressources de calcul Azure avec Spark installé et configuré. La création d’un cluster Spark dans HDInsight ne prend que dix minutes environ. Les données à traiter sont stockées dans le stockage des objets blob Azure. Consultez [Utiliser le stockage d’objets blob Azure avec HDInsight][hdinsight-storage].

![Apache Spark sur Azure HDInsight](./media/hdinsight-apache-spark-overview/hdispark.architecture.png "Apache Spark sur Azure HDInsight")


**Vous souhaitez prendre en main Apache Spark sur Azure HDInsight ?** Consultez [Démarrage rapide : créer un cluster Spark sur HDInsight Linux et exécuter des exemples d’application à l’aide de Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

>[AZURE.NOTE] Pour obtenir la liste des problèmes connus et des limitations de la version actuelle, consultez [Problèmes connus d’Apache Spark dans Azure HDInsight (Linux)](hdinsight-apache-spark-jupyter-spark-sql.md).


## Pourquoi utiliser Spark sur Azure HDInsight ? 

Azure HDInsight propose un service Spark entièrement géré. Les avantages liés à l’utilisation de Spark sur HDInsight sont les suivants :

| Fonctionnalité | Description |
|-------------------------------------|-------------------|
| Facilité de création | Vous pouvez créer un cluster Spark sur HDInsight en quelques minutes en utilisant le portail de gestion Azure, Azure PowerShell ou le Kit de développement logiciel (SDK) .NET HDInsight. Consultez [Prise en main des clusters Spark dans HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Simplicité d'utilisation | Les clusters Spark dans HDInsight incluent des blocs-notes Jupyter préconfigurés. Vous pouvez les utiliser pour le traitement interactif et la visualisation des données. L’URL est https://CLUSTERNAME.azurehdinsight.net/jupyter. Remplacez __CLUSTERNAME__ par le nom de votre cluster HDInsight Spark.|
| API REST | Spark dans HDInsight comprend [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), un serveur de tâches Spark basé sur une API REST, qui permet de soumettre et de surveiller à distance des tâches en cours d’exécution. |
| Requêtes simultanées | Spark dans HDInsight prend en charge les requêtes simultanées. Ainsi, plusieurs requêtes d’un même utilisateur ou plusieurs requêtes de différents utilisateurs et applications peuvent partager les mêmes ressources de cluster. |
| Mise en cache sur des disques SSD | Vous pouvez choisir de mettre en cache des données en mémoire ou dans les disques SSD attachés aux nœuds de cluster. La mise en cache en mémoire fournit les meilleures performances de requêtes, mais peut s’avérer coûteuse. La mise en cache sur des disques SSD constitue une très bonne option pour améliorer les performances des requêtes sans nécessité de créer un cluster d’une taille requise pour tenir l’ensemble du jeu de données en mémoire.|
| Intégration aux services Azure | Spark sur HDInsight est fourni avec un connecteur à Azure Event Hubs. Les clients peuvent créer des applications de diffusion en continu en utilisant Event Hubs, en plus de [Kafka](http://kafka.apache.org/), qui est déjà disponible dans Spark. |
| Intégration aux outils décisionnels | Spark pour HDInsight fournit des connecteurs pour les outils décisionnels les plus utilisés, comme [Power BI](http://www.powerbi.com/) et [Tableau](http://www.tableau.com/products/desktop), pour l’analyse des données.|
| Bibliothèques Anaconda préchargées | Les clusters Spark sur HDInsight sont fournis avec des bibliothèques Anaconda préinstallées. [Anaconda](http://docs.continuum.io/anaconda/) fournit près de 200 bibliothèques pour l’apprentissage automatique, l’analyse des données, la visualisation, etc.|
| Extensibilité | Bien que vous puissiez spécifier le nombre de nœuds dans votre cluster lors de sa création, vous pourriez souhaiter augmenter ou réduire la taille du cluster pour l’adapter à votre charge de travail. Tous les clusters HDInsight vous permettent de modifier le nombre de nœuds du cluster. En outre, les clusters Spark peuvent être supprimés sans perte de données puisque toutes les données sont stockées dans le stockage des objets blob Azure. |
| Support technique 24 heures sur 24, 7 jours sur 7 | Spark sur HDInsight est fourni avec un support technique de niveau d’entreprise assuré 24 heures sur 24, 7 jours sur 7 et un contrat SLA de 99,9 % de temps d’activité.|



## Quels sont les cas d’utilisation de Spark sur HDInsight ?

Apache Spark dans HDInsight autorise les principaux scénarios suivants.

### Analyse des données interactive et Power BI

[Consulter un didacticiel](hdinsight-apache-spark-use-bi-tools.md)

Apache Spark dans HDInsight stocke les données dans des objets blob Azure. Des experts et des décideurs clés peuvent analyser les données, générer les rapports correspondants et utiliser Microsoft Power BI pour créer des rapports interactifs à partir des données analysées. Les analystes peuvent démarrer à partir des données non structurées/semi-structurées dans le stockage Azure, définir un schéma pour les données à l’aide des blocs-notes, puis générer des modèles de données à l’aide de Microsoft Power BI. Spark dans HDInsight prend également en charge plusieurs outils décisionnels tiers comme Tableau, Qlikview et SAP Lumira, grâce auxquels il est une plateforme idéale pour les analystes de données, les experts et les décideurs clés.

### Apprentissage automatique itératif

[Consulter un didacticiel : prédiction des températures d’un bâtiment en utilisant des données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Consulter un didacticiel : prédiction des résultats d’inspections alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark est fourni avec[MLlib](http://spark.apache.org/mllib/), bibliothèque d’apprentissage automatique basée sur Spark. Spark sur HDInsight inclut également Anaconda, une distribution de Python avec une variété de packages pour l’apprentissage automatique. Ajoutez à cela la prise en charge intégrée des blocs-notes Jupyter, et vous disposez d’un environnement haut de gamme pour la création d’applications d’apprentissage automatique.

### Analyse des données de diffusion en continu et en temps réel

[Consulter un didacticiel](hdinsight-apache-spark-eventhub-streaming.md)

L’analyse des données en temps réel est utilisée pour les scénarios allant de la réduction de la durée d’analyse des données en les traitant à leur arrivée à la génération d’une véritable solution de diffusion en continu. Spark dans HDInsight offre une prise en charge améliorée de la création de solutions d’analyse en temps réel. Si Spark possède déjà des connecteurs pour la réception des données provenant de nombreuses sources telles que les sockets Kafka, Flume, Twitter, ZeroMQ ou TCP, Spark dans HDInsight ajoute la prise en charge de première classe de la réception des données provenant d’Azure Event Hubs. La fonctionnalité Event Hubs est le service de file d’attente le plus largement utilisé sur Azure. Grâce au support prêt à l’emploi de la fonctionnalité Event Hubs, Spark dans HDInsight est une plateforme idéale pour créer un pipeline d’analyse en temps réel.

##<a name="next-steps"></a>Quels sont les composants inclus dans un cluster Spark ?

Spark dans HDInsight inclut les composants suivants qui sont disponibles dans les clusters par défaut.

- [Spark Core](https://spark.apache.org/docs/1.5.1/). Comprend Spark Core, Spark SQL, les API de diffusion en continu Spark, GraphX et MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Bloc-notes Jupyter](https://jupyter.org)

Spark dans HDInsight fournit également un [pilote ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) pour la connectivité aux clusters Spark dans HDInsight à partir d’outils décisionnels comme Microsoft Power BI et Tableau.

## Par où commencer ?

Commencez par créer un cluster Spark sur HDInsight Linux. Consultez [Démarrage rapide : créer un cluster Spark sur HDInsight Linux et exécuter des exemples d’application à l’aide de Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

## Étapes suivantes

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

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gérer des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

<!---HONumber=AcomDC_0218_2016-->