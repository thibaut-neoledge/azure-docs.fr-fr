<properties
   	pageTitle="Didacticiel Linux : prise en main de Hadoop et Hive | Microsoft Azure"
	description="Suivez ce didacticiel Linux pour apprendre à utiliser Hadoop dans HDInsight. Découvrez comment approvisionner des clusters Linux et interroger des données avec Hive."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="08/07/2015"
	ms.author="nitinme"/>

# Didacticiel Hadoop : prise en main de Hadoop avec Hive dans HDInsight sur Linux (version préliminaire)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Ce didacticiel Hadoop vous permet de vous familiariser rapidement avec Azure HDInsight sur Linux en vous montrant comment approvisionner un cluster Hadoop sur Linux et comment exécuter une requête Hive.


> [AZURE.NOTE]Si vous ne connaissez pas Hadoop et les données volumineuses, vous pouvez en savoir plus sur les termes <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS (Hadoop Distributed File System)</a> et <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Pour comprendre la manière dont HDInsight prend en charge Hadoop dans Azure, consultez la rubrique [Présentation de Hadoop dans HDInsight](hdinsight-hadoop-introduction.md).


## Objectif de ce didacticiel

Supposons que vous disposiez d'un vaste jeu de données non structurées et que vous souhaitiez y exécuter des requêtes pour extraire des informations significatives. Voici comment obtenir ce résultat :

   ![Étapes du didacticiel Hadoop : Création d’un compte de stockage ; Configuration d’un cluster Hadoop ; Interrogation des données avec Hive.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.Linux.GetStartedFlow.png)


## Composants requis

Avant de commencer ce didacticiel Linux pour Hadoop, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Clés Secure Shell (SSH)**. Si vous souhaitez gérer à distance un cluster Linux à l'aide de SSH avec une clé plutôt qu'un mot de passe. L'utilisation d'une clé constitue la méthode recommandée, car elle est plus sécurisée. Pour savoir comment générer des clés SSH, consultez les articles suivants :
	-  Depuis un ordinateur Linux : [Utilisation de HDInsight Linux (Hadoop) depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	-  Depuis un ordinateur Windows : [Utilisation de SSH avec HDInsight Linux (Hadoop) depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

**Durée estimée :** 30 minutes

## <a name="provision"></a>Approvisionnement d’un cluster HDInsight sous Linux

Quand vous approvisionnez un cluster, vous approvisionnez les ressources de calcul Azure contenant Hadoop et ses applications associées. Dans cette section, vous allez approvisionner un cluster HDInsight version 3.2. Vous pouvez également créer des clusters Hadoop pour d'autres versions. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d'options personnalisées][hdinsight-provision]. Pour en savoir plus sur les différentes versions de HDInsight et leurs contrats SLA, consultez la page [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE]Vous pouvez également créer des clusters Hadoop exécutant le système d'exploitation Windows Server. Pour obtenir des instructions, consultez [Prise en main de HDInsight sur Windows](hdinsight-hadoop-tutorial-get-started-windows.md).


**Pour approvisionner un cluster HDInsight**

1. Connectez-vous à la [version préliminaire du portail Azure](https://ms.portal.azure.com/).
2. Cliquez sur **NOUVEAU**, sur **Analyse de données**, puis sur **HDInsight**.

    ![Création d'un nouveau cluster dans la version préliminaire du portail Azure](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.1.png "Création d'un nouveau cluster dans la version préliminaire du portail Azure")

3. Saisissez un **Nom de Cluster**, sélectionnez **Hadoop** comme **Type de cluster**, et à partir du menu déroulant **Système d'exploitation du cluster**, sélectionnez **Ubuntu**. Une coche verte apparaît en regard du nom de cluster s'il est disponible.

	![Saisissez le nom et le type de cluster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.2.png "Saisissez le nom et le type de cluster")

4. Si vous avez plusieurs abonnements, cliquez sur l'entrée **Abonnement** pour sélectionner l'abonnement Azure qui sera utilisé pour le cluster.

5. Cliquez sur **Groupe de ressources** pour afficher une liste des groupes de ressources existants, puis sélectionnez celui dans lequel créer le cluster. Vous pouvez également cliquer sur **Créer un nouveau**, puis saisir le nom du nouveau groupe de ressources. Une coche verte s'affiche pour indiquer si le nouveau nom de groupe est disponible.

	> [AZURE.NOTE]Cette entrée ira par défaut dans l'un des groupes de ressources existants, si l'un d'eux est disponible.

6. Cliquez sur **Informations d'identification**, puis saisissez un mot de passe pour l'utilisateur admin. Vous devez également saisir un **nom d'utilisateur SSH** et un **MOT DE PASSE** ou **CLÉ PUBLIQUE**, qui seront utilisés pour authentifier l'utilisateur SSH. L'utilisation d'une clé publique est l'approche recommandée. Cliquez sur **Sélectionner** en bas pour enregistrer la configuration des informations d'identification.

	![Fournissez les informations d'identification du cluster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.3.png "Fournissez les informations d'identification du cluster")

	Pour plus d'informations sur l'utilisation de SSH avec HDInsight, consultez l'un des articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Cliquez sur **Source de données** pour choisir une source de données existante pour le cluster ou en créer une. Lors de l'approvisionnement d'un cluster Hadoop dans HDInsight, vous spécifiez un compte Azure Storage. Un conteneur de stockage d'objets blob spécifique de ce compte est désigné en tant que système de fichiers par défaut, comme dans un système de fichiers distribués Hadoop (HDFS). Par défaut, le cluster HDInsight est approvisionné dans le même centre de données que le compte de stockage que vous spécifiez. Pour plus d'informations, consultez [Utilisation du stockage d'objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md).

	![Panneau Source de données](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.4.png "Fournissez la configuration de source de données")

	Actuellement, vous pouvez sélectionner un compte de stockage Azure comme source de données pour un cluster HDInsight. Lisez ce qui suit pour comprendre les entrées du panneau **Source de données**.

	- **Méthode de sélection** : définissez cette propriété sur la valeur **De tous les abonnements** pour permettre l'exploration des comptes de stockage de tous vos abonnements. Affectez-lui la valeur **Clé d'accès** si vous souhaitez saisir le **nom de stockage** et la **clé d'accès** d'un compte de stockage existant.

	- **Sélectionner le compte de stockage/Créer un compte** : cliquez sur **Sélectionner le compte de stockage** pour parcourir et sélectionner un compte de stockage existant à associer au cluster. Vous pouvez également cliquer sur **Créer** pour créer un nouveau compte de stockage. Utilisez le champ qui s'affiche pour saisir le nom du compte de stockage. Une coche verte s'affiche si le nom est disponible.

	- **Choisir le conteneur par défaut** : utilisez cette option pour saisir le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir ensuite n'importe quel nom, mais nous vous conseillons d'utiliser le même nom que celui du cluster pour que vous puissiez facilement reconnaître le conteneur utilisé pour ce cluster spécifique.

	- **Emplacement** : zone géographique dans laquelle le compte de stockage se trouve ou dans laquelle il sera créé.

		> [AZURE.IMPORTANT]La sélection de l'emplacement de la source de données par défaut définit également l'emplacement du cluster HDInsight. Le cluster et la source de données par défaut doit se trouver dans la même zone.

	Cliquez sur **Sélectionner** pour enregistrer la configuration de la source de données.

8. Cliquez sur **Niveaux de tarification du nœud** pour afficher des informations sur les nœuds qui seront créés pour ce cluster. Définissez le nombre de nœuds Worker dont vous avez besoin pour le cluster. Le coût estimé du cluster s'affiche dans le panneau.

	![Panneau Niveaux de tarification du nœud](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.5.png "Spécification du nombre de nœuds de cluster")

	Cliquez sur **Sélectionner** pour enregistrer la configuration de tarification du nœud.

9. Dans le panneau **Nouveau cluster HDInsight**, assurez-vous que l'option **Épingler au tableau d'accueil** est sélectionnée, puis cliquez sur **Créer**. Le cluster est créé et la vignette correspondante ajoutée au tableau d'accueil de votre portail Azure. L'icône indique que le cluster est en cours de configuration et sera modifiée pour représenter l'icône HDInsight une fois la configuration terminée.

Pendant la configuration|Approvisionnement terminé
------------------|---------------------
	![Indicateur de configuration sur le tableau d'accueil](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioning.png)|![Vignette de cluster approvisionné](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioned.png)

> [AZURE.NOTE]La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la vignette du tableau d'accueil ou l'entrée **Notifications** à gauche de la page pour suivre la progression du processus d'approvisionnement.

Une fois la configuration terminée, cliquez sur la vignette du cluster dans le tableau d'accueil pour lancer le panneau du cluster.

## <a name="hivequery"></a>Envoi d’une tâche Hive sur le cluster
À présent qu'un cluster HDInsight sur Linux a été approvisionné, la prochaine étape consiste à exécuter un exemple de tâche Hive afin d'interroger un exemple de données (sample.log) fourni avec les clusters HDInsight. L’exemple de données contient contient des informations de journal : trace, avertissements, informations, erreurs, etc. Nous interrogeons ces données pour récupérer tous les journaux d'erreurs avec une gravité spécifique. Vous devez effectuer les étapes suivantes pour exécuter une requête Hive sur un cluster HDInsight Linux :

- Connexion à un cluster Linux
- Exécution d'une tâche Hive



### Pour se connecter à un cluster

Vous pouvez vous connecter à un cluster HDInsight sous Linux à partir d'un ordinateur Linux ou Windows à l'aide de SSH.

**Connexion à partir d'un ordinateur Linux**

1. Ouvrez le terminal et exécutez la commande suivante :

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Étant donné que vous avez approvisionné un cluster avec l'option Création rapide, le nom d'utilisateur SSH par défaut est **hdiuser**. Ainsi, la commande doit être :

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. À l'invite, entrez le mot de passe que vous avez donné lors de l'approvisionnement du cluster. Lorsque vous êtes bien connecté, l'invite sera modifiée comme suit :

		hdiuser@headnode-0:~$


**Pour se connecter à partir d'un ordinateur Windows**

1. Téléchargez <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a> pour les clients Windows.

2. Ouvrez PuTTY. Dans **Catégorie**, cliquez sur **Session**. Dans l’écran **Options de base pour votre session PuTTY**, entrez l'adresse SSH de votre serveur HDInsight dans le champ **Nom d'hôte (ou adresse IP)**. L'adresse SSH est le nom de votre cluster, suivie de **-ssh.azurehdinsight.net**. Par exemple, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Connexion à un cluster HDInsight sous Linux à l'aide de PuTTY](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.linux.connect.putty.png)

3. Pour enregistrer les identifiants de connexion en vue d’une utilisation future, entrez un nom pour cette connexion sous **Sessions enregistrées**, puis cliquez sur **Enregistrer**. La connexion sera ajoutée à la liste des sessions enregistrées.

4. Cliquez sur **Ouvrir** pour vous connecter au cluster. Lorsque le nom d’utilisateur vous est demandé, entrez **hdiuser**. Pour le mot de passe, entrez le mot de passe que vous avez spécifié lors de l’approvisionnement du cluster. Lorsque vous êtes bien connecté, l'invite sera modifiée comme suit :

		hdiuser@headnode-0:~$

### Exécution d'une tâche Hive

Une fois que vous êtes connecté au cluster via SSH, utilisez les commandes suivantes pour exécuter une requête Hive.

1. Démarrez l'interface de ligne de commande (CLI) Hive à l'aide de la commande suivante à l'invite de commandes :

		hive

2. À l'aide de l'interface CLI, entrez les instructions suivantes pour créer une nouvelle table nommée **log4jLogs** à l'aide de l'exemple de données déjà disponible sur le cluster :

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

	>[AZURE.NOTE]Les tables externes doivent être utilisées lorsque les données sous-jacentes sont censées être mises à jour par une source externe (comme, par exemple, dans le cas d’un processus de téléchargement automatique des données) ou par une autre opération MapReduce, mais vous souhaitez toujours que les requêtes Hive utilisent les données les plus récentes. La suppression d'une table externe ne supprime *pas* les données, mais seulement la définition de table.

	Cet exemple renvoie la sortie suivante :

		Query ID = hdiuser_20150116000202_cceb9c6b-4356-4931-b9a7-2c373ebba493
		Total jobs = 1
		Launching Job 1 out of 1
		Number of reduce tasks not specified. Estimated from input data size: 1
		In order to change the average load for a reducer (in bytes):
		  set hive.exec.reducers.bytes.per.reducer=<number>
		In order to limit the maximum number of reducers:
		  set hive.exec.reducers.max=<number>
		In order to set a constant number of reducers:
		  set mapreduce.job.reduces=<number>
		Starting Job = job_1421200049012_0006, Tracking URL = <URL>:8088/proxy/application_1421200049012_0006/
		Kill Command = /usr/hdp/2.2.1.0-2165/hadoop/bin/hadoop job  -kill job_1421200049012_0006
		Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
		2015-01-16 00:02:40,823 Stage-1 map = 0%,  reduce = 0%
		2015-01-16 00:02:55,488 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.32 sec
		2015-01-16 00:03:05,298 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 5.62 sec
		MapReduce Total cumulative CPU time: 5 seconds 620 msec
		Ended Job = job_1421200049012_0006
		MapReduce Jobs Launched:
		Stage-Stage-1: Map: 1  Reduce: 1   Cumulative CPU: 5.62 sec   HDFS Read: 0 HDFS Write: 0 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 620 msec
		OK
		[ERROR]    3
		Time taken: 60.991 seconds, Fetched: 1 row(s)

	Notez que la sortie contient **[ERROR 3]** puisque trois lignes contiennent cette valeur.

3. Utilisez les instructions suivantes pour créer une nouvelle table « interne » nommée **errorLogs** :

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	Ces instructions effectuent les opérations suivantes :

	- **CREATE TABLE IF NOT EXISTS** : crée une table, si elle n'existe pas déjà. Le mot-clé **EXTERNAL** n’étant pas utilisé, il s’agit d’une table interne, stockée dans l’entrepôt de données Hive et gérée intégralement par Hive. Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.
	- **STORED AS ORC** : stocke les données dans un format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
	- **INSERT OVERWRITE ... SELECT** : sélectionne les lignes de la table **log4jLogs** qui contiennent [ERROR], puis insère les données dans la table **errorLogs**.

4. Pour vérifier que seules les lignes contenant [ERROR] dans la colonne t4 ont été stockées dans la table **errorLogs**, utilisez l’instruction suivante afin de renvoyer toutes les lignes à partir de **errorLogs** :

		SELECT * from errorLogs;

	La sortie suivante doit s'afficher sur la console :

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	Les données renvoyées doivent toutes correspondre aux journaux [ERROR].

## <a name="nextsteps"></a>Étapes suivantes
Dans ce didacticiel Linux, vous avez appris à approvisionner un cluster Hadoop sous Linux avec HDInsight et à exécuter une requête Hive sur ce cluster à l’aide de SSH. Pour en savoir plus, consultez les articles suivants :

- [Gestion des clusters HDInsight à l'aide d'Ambari](hdinsight-hadoop-manage-ambari.md) : les clusters HDInsight sous Linux utilisent Ambari pour la gestion et la surveillance des services Hadoop. L'interface utilisateur Web Ambari est disponible sur chaque cluster à https://CLUSTERNAME.azurehdinsight.net.

	> [AZURE.IMPORTANT]De nombreuses sections du site web Ambari sont directement accessibles via Internet, l'interface utilisateur web des services Hadoop tels que le gestionnaire de ressources ou l'historique des travaux nécessitent l'utilisation d'un tunnel SSH. Pour plus d'informations sur l'utilisation d’un tunnel SSH avec HDInsight, consultez l'un des articles suivants :
	>
	> * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md#tunnel)
	> * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel)

- [Configuration de HDInsight sous Linux à l'aide des options personnalisées](hdinsight-hadoop-provision-linux-clusters.md) : en savoir plus sur la façon de configurer des clusters HDInsight.

- [Utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md) : Si vous êtes déjà familiarisé avec Hadoop sur les plates-formes Linux, ce document fournit des informations Azure spécifiques telles que :

	* les URL correspondant aux services hébergés sur le cluster, tels qu'Ambari et WebHCat
	* l'emplacement des fichiers Hadoop et des exemples sur le système de fichiers local
	* l'utilisation de stockage d'Azure Storage (WASB) au lieu de HDFS, en tant que stockage de données par défaut

- Pour plus d'informations sur Hive, ou pour en savoir plus sur Pig et MapReduce, consultez les rubriques suivantes :

	- [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce]
	- [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
	- [Utilisation de Pig avec HDInsight][hdinsight-use-pig]

- Pour plus d'informations sur la façon de travailler avec le stockage Azure utilisé par votre cluster HDInsight, consultez les rubriques suivantes :

	- [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md)
	- [Téléchargement de données vers HDInsight][hdinsight-upload-data]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=August15_HO8-->