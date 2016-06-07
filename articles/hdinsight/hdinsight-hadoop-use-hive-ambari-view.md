<properties
   pageTitle="Utiliser les vues Ambari pour travailler avec Hive sur HDInsight (Hadoop) | Microsoft Azure"
   description="Découvrez comment utiliser la vue Hive à partir de votre navigateur web pour envoyer des requêtes Hive. La vue Hive fait partie de l’interface utilisateur web Ambari fournie avec votre cluster HDInsight sous Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/20/2016"
   ms.author="larryfr"/>

#Utilisez la vue Hive avec Hadoop dans HDInsight

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ambari est un utilitaire de gestion et de surveillance fourni avec les clusters HDInsight sous Linux. L’une des fonctionnalités offertes par Ambari est une interface utilisateur web qui peut être utilisée pour exécuter des requêtes Hive. Il s’agit de la __vue Hive__, qui fait partie des vues Ambari fournies avec votre cluster HDInsight.

> [AZURE.NOTE] Ambari offre de nombreuses fonctionnalités qui ne seront pas traitées dans ce document. Pour plus d’informations, consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md).

##Composants requis

- Un cluster HDInsight sous Linux Pour plus d’informations sur la création d’un cluster, consultez [Prise en main de HDInsight sous Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

##Ouvrir la vue Hive

Pour accéder à Ambari Views à partir du portail Azure, sélectionnez votre cluster HDInsight, puis sélectionnez __Vues Ambari__ dans la section __Liens rapides__.

![section liens rapides](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

Vous pouvez également accéder directement à Ambari en accédant à https://CLUSTERNAME.azurehdinsight.net dans un navigateur web (où __CLUSTERNAME__ est le nom de votre cluster HDInsight), puis sélectionner l’ensemble de carrés dans le menu de la page (en regard du lien __Admin__ et du bouton à gauche de la page) pour dresser la liste des vues disponibles. Sélectionnez la __vue Hive__.

![Sélection des vues ambari](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [AZURE.NOTE] En accédant à Ambari, vous êtes invité à vous authentifier sur le site : Saisissez l’administrateur (`admin` par défaut), le nom du compte et le mot de passe que vous avez utilisés lors de la création du cluster.

Une page similaire à celle ci-dessous doit s'afficher :

![Image de la page de vue hive, contenant une section de l’éditeur de requête](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

##Afficher les tables

Dans la section __Explorateur de bases de données__ de la page, sélectionnez l’entrée __par défaut__ dans l’onglet __Bases de données__. Cette opération affiche une liste de tables dans la base de données par défaut. Pour un nouveau cluster HDInsight, une seule table doit exister : __hivesampletable__.

![explorateur de base de données avec la base de données par défaut étendue](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

Étant donné que les nouvelles tables sont ajoutées à travers la procédure décrite dans ce document, vous pouvez utiliser l’icône Actualiser dans l’angle supérieur droit de l’Explorateur de base de données pour actualiser la liste des tables disponibles.

##<a name="hivequery"></a>Éditeur de requête

Pour exécuter une requête Hive sur les données incluses avec le cluster, suivez la procédure décrite ci-dessous à partir de la vue Hive.

1. Dans la section __Éditeur de requête__ de la page, collez les instructions HiveQL suivantes dans la feuille de calcul :

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Ces instructions effectuent les opérations suivantes :

	- **DROP TABLE** : supprime la table et le fichier de données, au cas où la table existe déjà.
	- **CREATE EXTERNAL TABLE** : crée une nouvelle table « externe » dans Hive. Les tables externes stockent uniquement la définition de table dans Hive ; les données restent dans l'emplacement d'origine.
	- **ROW FORMAT** : indique à Hive comment les données mises en forme. Dans ce cas, les champs de chaque journal sont séparés par un espace.
	- **STORED AS TEXTFILE LOCATION** : indique à Hive où sont stockées les données (répertoire example/data) et qu'elles sont stockées sous forme de texte.
	- **SELECT** : sélectionne toutes les lignes dont la colonne t4 contient la valeur [ERROR].

	>[AZURE.NOTE] Les tables externes doivent être utilisées lorsque les données sous-jacentes sont censées être mises à jour par une source externe (comme, par exemple, dans le cas d’un processus de téléchargement automatique des données) ou par une autre opération MapReduce, mais vous souhaitez toujours que les requêtes Hive utilisent les données les plus récentes. La suppression d'une table externe ne supprime *pas* les données, mais seulement la définition de table.

2. Utilisez le bouton __Exécuter__ au bas de l’éditeur de requête pour démarrer la requête. Le bouton doit s’afficher en orange et indiquer le libellé __Arrêter l’exécution__. Une section __Résultats du processus de requête__ doit apparaître en dessous de l’éditeur de requête et afficher des informations sur la tâche.

    > [AZURE.IMPORTANT] Certains navigateurs peuvent ne pas actualiser correctement le fichier journal ou les informations de résultats. Si vous exécutez une tâche et que celle-ci semble s’exécuter indéfiniment sans mettre à jour le journal ou renvoyer des résultats, essayez d’utiliser Mozilla FireFox ou Google Chrome.

3. Une fois la requête terminée, la section __Résultats du processus de requête__ affiche les résultats de l’opération. Le bouton __Arrêter l’exécution__ s’affichera de nouveau en vert avec le libellé __Exécuter__. Les informations suivantes devraient s’afficher dans l’onglet __Résultats__ :

        sev       cnt
        [ERROR]   3

    L’onglet __Journaux__ peut être utilisé pour afficher les informations de journalisation créées par la tâche. Vous pouvez les utiliser pour résoudre des problèmes sur une requête.

    > [AZURE.TIP] Notez la présence de la boîte de dialogue déroulante __Enregistrer les résultats__ en haut à gauche de la section __Résultats du processus de requête__ ; vous pouvez utiliser ce menu pour télécharger les résultats ou pour les enregistrer dans un stockage HDInsight sous la forme d’un fichier CSV.

3. Sélectionnez les quatre premières lignes de cette requête, puis sélectionnez __Exécuter__. Notez qu’aucun résultat n’est renvoyé à la fin de la tâche. En effet, si vous utilisez le bouton __Exécuter__ alors que vous avez sélectionné une partie de la requête, seules les instructions sélectionnées seront exécutées. Dans ce cas, la sélection n’inclut pas l’instruction finale qui consiste à extraire des lignes de la table. Si vous sélectionnez uniquement cette ligne et que vous utilisez le bouton __Exécuter__, vous devriez obtenir les résultats attendus.

3. Utilisez le bouton __Nouvelle feuille de calcul__ au bas de l’__Éditeur de requête__ pour créer une feuille de calcul. Dans la nouvelle feuille de calcul, entrez les instructions HiveQL suivantes :

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

	Ces instructions effectuent les opérations suivantes :

	- **CREATE TABLE IF NOT EXISTS** : crée une table, si elle n'existe pas déjà. Le mot-clé **EXTERNAL** n’étant pas utilisé, il s’agit d’une table interne, stockée dans l’entrepôt de données Hive et gérée intégralement par Hive. Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.
	- **STORED AS ORC** : stocke les données dans un format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
	- **INSERT OVERWRITE ... SELECT** : sélectionne les lignes de la table **log4jLogs** qui contiennent [ERROR], puis insère les données dans la table **errorLogs**.

    Utilisez le bouton __Exécuter__ pour exécuter cette requête. L’onglet __Résultats__ ne contiendra aucune information puisque cette requête ne renvoie aucune ligne. En revanche, l’état __OPÉRATION RÉUSSIE__ devrait s’afficher.

###Paramètres Hive

Sélectionnez l’icône __Paramètres__ à droite de l’éditeur.

![icônes](./media/hdinsight-hadoop-use-hive-ambari-view/settings.png)

Les paramètres peuvent être utilisés pour modifier différents paramètres Hive, par exemple, passer du moteur d’exécution Tez (par défaut) à MapReduce pour Hive.

###Visual Explain

Sélectionnez l’icône __Visual Explain__ à droite de l’éditeur.

![icônes](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplainicon.png)

Il s’agit de la vue __Visual Explain__ de la requête, qui peut être utile pour comprendre le déroulement de requêtes complexes. Vous pouvez afficher un équivalent textuel de cette vue à l’aide du bouton __Expliquer__ de l’éditeur de requête.

![image de visual explain](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

###Tez

Sélectionnez l'icône __Tez__ à droite de l'éditeur.

![icônes](./media/hdinsight-hadoop-use-hive-ambari-view/tez.png)

Cette opération affiche le graphe orienté acyclique (DAG) utilisé par Tez pour cette requête, s’il est disponible. Si vous souhaitez afficher le DAG pour les requêtes que vous avez exécutées par le passé ou déboguer le processus Tez, utilisez plutôt la [vue Tez](hdinsight-debug-ambari-tez-view.md).

###Notifications

Sélectionnez l’icône __Notifications__ à droite de l’éditeur.

![icônes](./media/hdinsight-hadoop-use-hive-ambari-view/notifications.png)

Les notifications sont des messages qui sont générés lors de l’exécution des requêtes. Par exemple, vous recevez une notification lorsqu’une requête est soumise, ou lorsqu’une erreur se produit.

##Requêtes enregistrées

1. À partir de l’éditeur de requête, créez une feuille de calcul et entrez la requête suivante :

        SELECT * from errorLogs;

    Exécutez la requête pour vérifier qu’elle fonctionne. Vous obtenez les résultats suivants :

        errorlogs.t1 	errorlogs.t2 	errorlogs.t3 	errorlogs.t4 	errorlogs.t5 	errorlogs.t6 	errorlogs.t7
        2012-02-03 	18:35:34 	SampleClass0 	[ERROR] 	incorrect 	id 	
        2012-02-03 	18:55:54 	SampleClass1 	[ERROR] 	incorrect 	id 	
        2012-02-03 	19:25:27 	SampleClass4 	[ERROR] 	incorrect 	id

2. Cliquez sur le bouton __Enregistrer sous__ au bas de l’éditeur. Nommez cette requête __Errorlogs__ et sélectionnez __OK__. Notez que la feuille de calcul prend le nom __Errorlogs__.

3. Sélectionnez l’onglet __Requêtes enregistrées__ en haut de la page Vue Hive. Notez que la requête __Errorlogs__ est désormais répertoriée comme une requête enregistrée. Elle restera dans la liste jusqu’à ce que vous la supprimiez. Sélectionnez le nom pour ouvrir la requête dans l’éditeur de requête.

##Historique des requêtes

Le bouton __Historique__ en haut de la vue Hive vous permet d’afficher les requêtes que vous avez exécutées précédemment. Utilisez-le maintenant et sélectionnez certaines des requêtes que vous avez exécutées précédemment. Lorsque vous sélectionnez une requête, elle s’ouvre dans l’éditeur de requête.

##Fonctions définies par l’utilisateur (UDF)

Hive peut également être étendu via des **fonctions définies par l'utilisateur (UDF)**. Une fonction UDF vous permet d'implémenter une fonctionnalité ou une logique qui n'est pas facilement modelée en HiveQL.

Lorsque vous ajoutez une fonction UDF dans le cadre des instructions HiveQL dans votre requête, l’onglet UDF en haut de la vue Hive vous permet de déclarer et d’enregistrer un ensemble d’UDF qui peuvent être utilisées avec l’__éditeur de requête__.

Une fois que vous avez ajouté une fonction UDF à la vue Hive, un bouton __Insérer des fonctions UDF__ s’affiche en bas de l’__éditeur de requête__. La sélection de ce bouton affiche une liste déroulante des UDF définies dans la vue Hive. La sélection d’une fonction UDF ajoute des instructions HiveQL à votre requête pour activer l’UDF.

Par exemple, si vous avez défini une fonction UDF avec les propriétés suivantes :

* Nom de ressource : myudfs
* Chemin d’accès à la ressource : wasb:///myudfs.jar
* Nom de la fonction UDF : myawesomeudf
* Nom de la classe UDF : com.myudfs.Awesome

L’utilisation du bouton __Insérer des fonctions UDF__ va afficher une entrée nommée __myudfs__, avec une autre liste déroulante pour chaque fonction UDF définie pour cette ressource. Dans le cas présent, __myawesomeudf__. La sélection de cette entrée va ajouter le code suivant au début de la requête :

    add jar wasb:///myudfs.jar;

    create temporary function myawesomeudf as 'com.myudfs.Awesome';

Vous pouvez ensuite utiliser la fonction UDF dans votre requête. Par exemple, `SELECT myawesomeudf(name) FROM people;`.

Pour plus d’informations sur l’utilisation des fonctions UDF avec Hive sur HDInsight, consultez les articles suivants :

* [Utilisation de Python avec Hive et Pig dans HDInsight](hdinsight-python.md)

* [Comment ajouter une UDF personnalisée Hive à HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0525_2016-->