<properties 
	pageTitle="Problèmes connus d’Apache Spark dans HDInsight | Microsoft Azure" 
	description="Problèmes connus d’Apache Spark dans HDInsight" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/06/2016" 
	ms.author="nitinme"/>

# Problèmes connus avec les clusters Apache Spark sur Azure HDInsight Linux

Ce document fait le suivi de tous les problèmes connus pour la version Preview publique de HDInsight Spark.

##Livy divulgue une session interactive
 
Lorsque Livy est redémarré avec une session interactive (à partir d’Ambari ou à cause d’un redémarrage de la machine virtuelle du nœud principal 0) encore active, une session de travail interactive sera divulguée. Pour cette raison, de nouvelles tâches peuvent rester bloquées à l’état Accepté sans pouvoir être démarrées.

**Atténuation :**

Pour contourner ce problème, suivez la procédure ci-après :

1. SSH dans le nœud principal. 
2. Exécutez la commande suivante pour rechercher l’ID d’application des tâches interactives démarrées via Livy. 

        yarn application –list

    Le nom des tâches par défaut sera Livy si ces tâches ont été démarrées avec une session interactive Livy ne comportant aucun nom explicite spécifié. Pour la session Livy démarrée via le bloc-notes Jupyter, le nom de la tâche démarrera avec remotesparkmagics\_*.

3. Exécutez la commande suivante pour mettre fin à ces tâches.

        yarn application –kill <Application ID>

De nouvelles tâches commencent à être exécutées.

##Serveur d’historique Spark non démarré 

Le serveur d’historique Spark ne démarre pas automatiquement après la création d’un cluster.

**Atténuation :**

Démarrez manuellement le serveur d’historique à partir d’Ambari.

## Problème d’autorisation dans le répertoire des journaux Spark 

Lorsque hdiuser soumet une tâche avec spark-submit, il existe une erreur java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log (Autorisation refusée) et le journal du pilote n’est pas écrit.

**Atténuation :**
 
1. Ajoutez hdiuser au groupe Hadoop. 
2. Fournissez les autorisations 777 sur /var/log/spark après la création du cluster. 
3. Mettez à jour l’emplacement du journal Spark à l’aide d’Ambari pour obtenir un répertoire avec les autorisations 777.  
4. Exécutez spark-submit en tant que sudo.  

## Problèmes liés aux notebooks Jupyter

Voici certains problèmes connus liés aux notebooks Jupyter.

### Impossible de télécharger les blocs-notes Jupyter au format .ipynb

Si vous exécutez la dernière version des blocs-notes Jupyter pour HDInsight Spark et que vous essayez de télécharger une copie du bloc-notes sous forme de fichier **.ipynb** à partir de l'interface utilisateur du bloc-notes Jupyter, une erreur interne peut apparaître.

**Atténuation :**

1.	Le téléchargement du bloc-notes dans un format autre que .ipynb (par exemple, .txt) réussira.  
2.	Si vous avez besoin du fichier .ipynb, vous pouvez le télécharger à partir de votre conteneur de cluster dans votre compte de stockage sous **/HdiNotebooks**. Cela concerne uniquement la dernière version des blocs-notes Jupyter pour HDInsight, qui prend en charge les sauvegardes de bloc-notes dans le compte de stockage. Les versions précédentes des blocs-notes Jupyter pour HDInsight Spark n'ont pas ce problème.


### Notebooks avec des caractères non-ASCII dans les noms de fichiers

Les notebooks Jupyter qui peuvent être utilisés dans les clusters Spark HDInsight ne doivent pas contenir de caractères non-ASCII dans les noms de fichiers. Si vous essayez de télécharger un fichier, par le biais de l’interface utilisateur Jupyter, qui a un nom de fichier non-ASCII, l’opération échoue en mode silencieux (c’est-à-dire que Jupyter ne vous permet pas de télécharger le fichier, mais ne renvoie pas d’erreur visible).

### Erreur lors du chargement de notebooks de taille supérieure

Vous pouvez obtenir une erreur **`Error loading notebook`** lorsque vous tentez de charger un bloc-notes de taille supérieure.

**Atténuation :**

Si vous obtenez cette erreur, cela ne signifie pas que vos données sont endommagées ou perdues. Vos blocs-notes sont toujours sur le disque, sous `/var/lib/jupyter` et vous pouvez exécuter SSH dans le cluster pour y accéder. Vous pouvez copier les blocs-notes depuis le cluster vers votre ordinateur local (à l’aide de SCP ou WinSCP) pour en faire une sauvegarde afin d’éviter la perte de toutes les données importantes dans le bloc-notes. Vous pouvez ensuite créer un tunnel SSH dans votre nœud principal sur le port 8001, afin d’accéder à Jupyter sans avoir à passer par la passerelle. À partir de là, vous pouvez effacer la sortie de votre bloc-notes et l’enregistrer de nouveau pour réduire la taille du bloc-notes au minimum.

Pour éviter cette erreur à l’avenir, gardez en tête les conseils suivants :

* Il est important que la taille du bloc-notes reste réduite. Aucune sortie de vos travaux sous Spark qui est envoyée à Jupyter n’est conservée dans le bloc-notes. Pour Jupyter, il est en général recommandé d’éviter l’exécution de l’élément `.collect()` sur des RDD ou trames de données larges. En revanche, si vous souhaitez lire le contenu d’un RDD, pensez à exécuter `.take()` ou `.sample()` afin que votre sortie ne devienne pas trop volumineuse.
* En outre, lorsque vous enregistrez un bloc-notes, supprimez toutes les cellules de sortie pour réduire sa taille.

### Démarrage du bloc-notes plus long que prévu 

La première instruction de code du bloc-notes Jupyter avec Spark Magic peut nécessiter plusieurs minutes.

**Explication :**
 
Cela se produit lorsque la première cellule du code est exécutée. En arrière-plan, la configuration de la session est lancée, et les contextes Spark, SQL et Hive sont définis. Une fois ces contextes définis, la première instruction est exécutée et donne l'impression que l'instruction prend beaucoup de temps.

### Délai d’attente du bloc-notes Jupyter lors de la création de la session

Lorsque le cluster Spark manque de ressources, les noyaux Spark et Pyspark du bloc-notes Jupyter expirent en essayant de créer la session.

**Atténuations :**

1. Libérez certaines ressources de votre cluster Spark :

    - Arrêtez les autres blocs-notes Spark en allant dans le menu Fermer et Arrêter ou en cliquant sur Arrêter dans l’explorateur du bloc-notes.
    - Arrêtez les autres applications Spark à partir de YARN.

2. Redémarrez le bloc-notes que vous tentiez de démarrer. Un nombre suffisant de ressources devrait désormais être disponible pour vous permettre de créer une session.

### Le retour au point de contrôle peut échouer

Vous pouvez créer des points de contrôle dans les notebooks Jupyter au cas où vous avez besoin de revenir à une version antérieure du notebook. Toutefois, si l’état actuel des notebooks a une requête SQL avec une visualisation automatique, le retour à un point de contrôle précédemment stocké peut entraîner une erreur.

##Voir aussi

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

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gestion des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0608_2016-->