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
	ms.date="04/14/2016" 
	ms.author="nitinme"/>


# Noyaux disponibles pour les blocs-notes Jupyter avec les clusters HDInsight Spark Linux sur HDInsight (version préliminaire)

Le cluster Apache Spark sur HDInsight (Linux) comprend des blocs-notes Jupyter qui vous permettent de tester vos applications. Un noyau est un programme qui exécute et interprète votre code. Les clusters HDInsight Spark fournissent deux noyaux que vous pouvez utiliser avec le bloc-notes Jupyter. Ces composants sont les suivants :

1. **PySpark** (pour les applications écrites en Python)
2. **Spark** (pour les applications écrites en Scala)

Dans cet article, vous allez découvrir comment utiliser ces noyaux ainsi que leurs avantages.

**Configuration requise :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark sur HDInsight Linux. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Utilisation des noyaux 

1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   

2. Dans le panneau du cluster Spark, cliquez sur **Liens rapides**, puis dans le panneau **Tableau de bord du cluster**, cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

	> [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez __CLUSTERNAME__ par le nom de votre cluster.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créez un nouveau bloc-notes avec les nouveaux noyaux. Cliquez sur **Nouveau**, puis sur **PySpark** ou **Spark**. Utilisez le noyau Spark pour les applications Scala et le noyau PySpark pour les applications Python.

	![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Créer un bloc-notes Jupyter")

3. Un nouveau bloc-notes s’ouvre avec le noyau que vous avez sélectionné.

## Pourquoi utiliser les noyaux PySpark ou Spark ?

Voici quelques avantages de l’utilisation des nouveaux noyaux.

1. **Contextes prédéfinis**. Avec les noyaux **PySpark** ou **Spark** fournis avec les blocs-notes Jupyter, vous n’avez pas besoin de définir les contextes Spark ou Hive explicitement avant de commencer à utiliser l’application que vous développez ; ils sont disponibles par défaut. Ces contextes sont les suivants :

	* **sc** : pour le contexte Spark
	* **sqlContext** : pour le contexte Hive


	Par conséquent, vous n’avez pas à exécuter d’instructions telles que les suivantes pour définir les contextes :

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = HiveContext(sc)

	En revanche, vous pouvez utiliser directement les contextes prédéfinis dans votre application.
	
2. **Commandes magiques de cellule**. Le noyau PySpark fournit certaines « commandes magiques » prédéfinies, qui sont des commandes spéciales que vous pouvez appeler avec `%%` (par exemple, `%%MAGIC` <args>). La commande magique doit se trouver au tout début d’une cellule de code et autoriser plusieurs lignes de contenu. Le mot magic doit être le premier mot de la cellule. Ajouter quoi que ce soit avant la commande magique, même des commentaires, provoque une erreur. Pour plus d’informations sur les commandes magiques, cliquez [ici](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

	Le tableau ci-dessous répertorie les différentes commandes magiques disponibles par le biais des noyaux.

	| Commande magique | Exemple | Description |
	|-----------|---------------------------------|--------------|
	| help | `%%help` | Génère une table de toutes les commandes magiques disponibles, accompagnées d’un exemple et d’une description |
	| info | `%%info` | Génère des informations de session pour le point de terminaison Livy actuel |
	| configurer | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Configure les paramètres de création d’une session. L’indicateur de forçage (-f) est obligatoire si une session déjà créée doit être supprimée et recréée. Consultez la section [POST /sessions Request Body de Livy](https://github.com/cloudera/livy#request-body) pour obtenir la liste des paramètres valides. Les paramètres doivent être passés en tant que chaîne JSON et être spécifiés sur la ligne suivant la commande magique, comme indiqué dans l’exemple de colonne. |
	| sql | `%%sql -o <variable name>`<br> `SHOW TABLES` | Exécute une requête Hive sur sqlContext. Si le paramètre `-o` est passé, le résultat de la requête est conservé dans le contexte Python %%local en tant que trame de données [Pandas](http://pandas.pydata.org/). |
	| local | `%%local`<br>`a=1` | Tout le code dans les lignes suivantes est exécuté localement. Le code doit être du code Python valide. |
	| journaux | `%%logs` | Génère les journaux de la session Livy en cours. |
	| delete | `%%delete -f -s <session number>` | Supprime une session spécifique du point de terminaison Livy actuel. Notez que vous ne pouvez pas supprimer la session qui est lancée pour le noyau lui-même. |
	| cleanup | `%%cleanup -f` | Supprime toutes les sessions pour le point de terminaison Livy actuel, y compris la session de ce bloc-notes. L’indicateur de forçage -f est obligatoire. |

3. **Visualisation automatique**. Le noyau **Pyspark** visualise automatiquement la sortie des requêtes Hive et SQL. Vous pouvez choisir entre plusieurs types de visualisations, notamment tableau, secteurs, courbes, aires et barres.

## Paramètres pris en charge avec la commande magique %%sql

La commande magique %%sql prend en charge différents paramètres qui vous permettent de contrôler le type de sortie que vous recevez quand vous exécutez des requêtes. Le tableau suivant répertorie les paramètres de sortie.

| Paramètre | Exemple | Description |
|-----------|---------------------------------|--------------|
| -o | `-o <VARIABLE NAME>` | Utilisez ce paramètre pour conserver le résultat de la requête dans le contexte Python %%local en tant que trame de données [Pandas](http://pandas.pydata.org/). Le nom de la variable dataframe est le nom de variable que vous spécifiez. |
| -q | `-q` | Utilisez ce paramètre pour désactiver les visualisations pour la cellule. Si vous ne voulez pas visualiser automatiquement le contenu d’une cellule et préférez simplement capturer le contenu comme une trame de données, utilisez `-q -o <VARIABLE>`. Si vous souhaitez désactiver les visualisations sans capturer les résultats (par exemple, pour exécuter une requête SQL avec effets secondaires, comme une instruction `CREATE TABLE`), utilisez `-q` sans spécifier d’argument `-o`. |
| -m | `-m <METHOD>` | **METHOD** prend la valeur **take** ou **sample** (**take** est la valeur par défaut). Si la méthode est **take**, le noyau sélectionne des éléments à partir du haut du jeu de données de résultats spécifié par la valeur MAXROWS (décrite plus bas dans ce tableau). Si la méthode est **sample**, le noyau échantillonne de façon aléatoire les éléments du jeu de données en fonction du paramètre `-r` (décrit ci-après dans ce tableau). |
| -r | `-r <FRACTION>` | Ici **FRACTION** est un nombre à virgule flottante compris entre 0,0 et 1,0. Si l’exemple de méthode pour la requête SQL est `sample`, le noyau échantillonne automatiquement, de façon aléatoire, la fraction spécifiée des éléments du jeu de résultats. Par exemple, si vous exécutez une requête SQL avec les arguments `-m sample -r 0.01`, 1 % des lignes de résultat sont échantillonnées aléatoirement. |
| -n | `-n <MAXROWS>` | **MAXROWS** est une valeur entière. Le noyau limite le nombre de lignes de la sortie au nombre défini par **MAXROWS**. Si **MAXROWS** est un nombre négatif comme **-1**, le nombre de lignes dans le jeu de résultats n’est pas limité. |

**Exemple :**

	%%sql -q -m sample -r 0.1 -n 500 -o query2 
	SELECT * FROM hivesampletable

L’instruction ci-dessus effectue les actions suivantes :

* Elle sélectionne tous les enregistrements présents dans **hivesampletable**.
* Comme nous utilisons le paramètre - q, elle désactive la visualisation automatique.
* Comme nous utilisons `-m sample -r 0.1 -n 500`, elle échantillonne de façon aléatoire 10 % des lignes présentes dans hivesampletable et limite la taille du jeu de résultats à 500 lignes.
* Enfin, comme nous avons utilisé `-o query2`, elle enregistre également la sortie dans une trame de données appelée **query2**.
	

## Points à prendre en compte lors de l'utilisation des nouveaux noyaux

Quel que soit le noyau que vous utilisez (PySpark ou Spark), laisser les blocs-notes s’exécuter consomme vos ressources de cluster. Avec ces noyaux, les contextes étant prédéfinis, le simple fait de quitter les blocs-notes n’arrête pas le contexte. Par conséquent, les ressources du cluster restent en cours d'utilisation. Une bonne pratique avec les noyaux PySpark et Spark consiste à utiliser l’option **Fermer et arrêter** à partir du menu **Fichier** du bloc-notes. Cela supprime le contexte puis ferme le bloc-notes.


## Voici quelques exemples :

Lorsque vous ouvrez un bloc-notes Jupyter, deux dossiers sont disponibles au niveau racine.

* Le dossier **PySpark** contient des exemples de blocs-notes qui utilisent le nouveau noyau **Python**.
* Le dossier **Scala** comprend des exemples de blocs-notes qui utilisent le nouveau noyau **Spark**.

Vous pouvez ouvrir le bloc-notes **00 - [READ ME FIRST] Spark Magic Kernel Features** à partir du dossier **PySpark** ou **Spark** pour en savoir plus sur les différentes commandes magiques disponibles. Vous pouvez également recourir aux autres exemples de blocs-notes disponibles sous les deux dossiers pour savoir comment utiliser concrètement des blocs-notes Jupyter avec des clusters HDInsight Spark.

## Où sont stockés les blocs-notes ?

Les blocs-notes Jupyter sont enregistrés dans le compte de stockage associé au cluster, dans le dossier **/HdiNotebooks**. Les blocs-notes, les fichiers texte et les dossiers que vous créez dans Jupyter sont accessibles à partir de WASB. Par exemple, si vous utilisez Jupyter pour créer un dossier **myfolder** et un bloc-notes **myfolder/mynotebook.ipynb**, vous pouvez accéder à ce bloc-notes dans `wasb:///HdiNotebooks/myfolder/mynotebook.ipynb`. L’inverse est également vrai : si vous chargez un bloc-notes directement dans votre compte de stockage dans `/HdiNotebooks/mynotebook1.ipynb`, le bloc-notes est également accessible à partir de Jupyter. Les blocs-notes sont conservés dans le compte de stockage même après la suppression du cluster.

Les blocs-notes sont enregistrés dans le compte de stockage dans un mode compatible avec HDFS. Si vous utilisez SSH dans le cluster, vous pouvez donc exécuter des commandes de gestion des fichiers telles que celles qui suivent :

	hdfs dfs -ls /HdiNotebooks             				  # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
	hdfs dfs –copyToLocal /HdiNotebooks    				# Download the contents of the HdiNotebooks folder
	hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


En cas de problèmes d’accès au compte de stockage pour le cluster, les blocs-notes sont également enregistrés sur le nœud principal `/var/lib/jupyter`.

## Navigateur pris en charge
Les blocs-notes Jupyter s’exécutant sur des clusters HDInsight Spark sont pris en charge uniquement sur Google Chrome.

## Commentaires

Les nouveaux noyaux sont en phase d’évolution et gagneront en maturité avec le temps. Les API pourront également être amenés à évoluer au fur et à mesure des évolutions des noyaux. Nous aimerions recevoir vos commentaires concernant l'utilisation de ces nouveaux noyaux. Cela nous sera très utile pour préparer la version finale de ces noyaux. Vous pouvez laisser vos commentaires/remarques sous la section **Commentaires** en dessous de cet article.


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

<!---HONumber=AcomDC_0420_2016-->