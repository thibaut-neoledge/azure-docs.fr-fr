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
   	ms.date="11/29/2015"
   	ms.author="nitinme"/>

# Didacticiel Hadoop : prise en main de Hadoop avec Hive dans HDInsight sur Linux

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Ce document vous permet de vous familiariser rapidement avec Azure HDInsight sur Linux, en vous montrant comment créer un cluster Hadoop Linux, vous connecter au cluster à l’aide de Secure Shell (SSH), puis exécuter une requête Hive sur les exemples de données inclus dans le cluster.

> [AZURE.NOTE]Si vous ne connaissez pas Hadoop et le Big Data, vous pouvez en savoir plus sur les termes [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086), [HDFS (Hadoop Distributed File System)](http://go.microsoft.com/fwlink/?LinkId=510087) et [Hive](http://go.microsoft.com/fwlink/?LinkId=510085). Pour comprendre la manière dont HDInsight prend en charge Hadoop dans Azure, consultez la rubrique [Présentation de Hadoop dans HDInsight](hdinsight-hadoop-introduction.md).

## Composants requis

Avant de commencer ce didacticiel Linux pour Hadoop, vous devez disposer des éléments suivants :

- **Un abonnement Azure** : consultez [Obtention d’un essai gratuit Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un client SSH (Secure Shell)** : les systèmes Linux, Unix et OS X fournissent un client SSH par le biais de la commande `ssh`. Pour les systèmes Windows, nous vous recommandons [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    > [AZURE.NOTE]Les étapes décrites dans ce document utilisent SSH pour se connecter au cluster HDInsight, car SSH est disponible pour tous les systèmes d’exploitation clients. Pour les autres méthodes de connexion au cluster HDInsight, telles que l’utilisation des outils HDInsight pour Visual Studio ou des API REST, consultez les liens Hive, Pig et MapReduce dans la section [Étapes suivantes](#nextsteps) de ce document.
    
- **Clés SSH (Secure Shell) (facultatif)** : vous pouvez sécuriser le compte SSH utilisé pour se connecter au cluster à l’aide d’un mot de passe ou d’une clé publique. Avec un mot de passe, vous êtes rapidement opérationnel. Utilisez cette option pour pouvoir rapidement approvisionner un cluster et exécuter certaines tâches de test. L’utilisation d’une clé est une méthode plus sécurisée, mais qui demande une installation supplémentaire. Elle convient dans le cadre de l’approvisionnement d’un cluster de production. Dans cet article, nous utilisons la méthode du mot de passe. Pour savoir comment créer et utiliser des clés SSH avec HDInsight, consultez les articles suivants :

	-  Pour un ordinateur Linux : [Utilisation de SSH avec HDInsight Linux (Hadoop) à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    
	-  Pour un ordinateur Windows : [Utilisation de SSH avec HDInsight Linux (Hadoop) à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="provision"></a>Approvisionnement d’un cluster HDInsight sous Linux

Quand vous approvisionnez un cluster, vous créez les ressources de calcul Azure qui contiennent les services et ressources Hadoop. Dans cette section, vous approvisionnez un cluster HDInsight version 3.2, qui contient la version 2.2 de Hadoop. Pour en savoir plus sur les différentes versions de HDInsight et leurs contrats SLA, consultez la page [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md). Pour plus d’informations sur la création d’un cluster HDInsight, consultez [Approvisionnement de clusters HDInsight à l’aide d’options personnalisées][hdinsight-provision].

>[AZURE.NOTE]Vous pouvez également créer des clusters Hadoop exécutant le système d'exploitation Windows Server. Pour obtenir des instructions, consultez [Prise en main de HDInsight sur Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

Procédez comme suit pour créer un cluster :

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).
2. Cliquez sur **NOUVEAU**, sur **Analyse des données**, puis sur **HDInsight**.

    ![Création d’un cluster dans le portail Azure](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.1.png "Création d’un cluster dans le portail Azure")

3. Entrez un **Nom de cluster**, sélectionnez **Hadoop** comme **Type de cluster** et, dans le menu déroulant **Système d’exploitation de cluster**, sélectionnez **Ubuntu**. Une coche verte apparaît en regard du nom de cluster s'il est disponible.

	![Saisir le nom et le type du cluster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.2.png "Saisir le nom et le type du cluster")

4. Si vous possédez plusieurs abonnements, cliquez sur l’entrée **Abonnement** pour sélectionner l’abonnement Azure qui sera utilisé pour le cluster.

5. Cliquez sur **Groupe de ressources** pour afficher une liste des groupes de ressources existants, puis sélectionnez celui dans lequel créer le cluster. Vous pouvez également cliquer sur **Créer un nouveau**, puis entrer le nom du nouveau groupe de ressources. Une coche verte s’affiche pour indiquer si le nouveau nom de groupe est disponible.

	> [AZURE.NOTE]Cette entrée ira par défaut dans l'un des groupes de ressources existants, si l'un d'eux est disponible.

6. Cliquez sur **Informations d’identification**, puis entrez un mot de passe pour l’utilisateur admin. Vous devez également entrer un **nom d’utilisateur SSH**. Pour **Type d’authentification SSH**, cliquez sur **MOT DE PASSE** et spécifiez un mot de passe pour l’utilisateur SSH. Cliquez sur **Sélectionner** en bas de l’écran pour enregistrer la configuration des informations d’identification.

	![Fournir les informations d’identification du cluster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.3.png "Fournir les informations d’identification du cluster")

    > [AZURE.NOTE]SSH permet d’accéder à distance au cluster HDInsight à l’aide d’une ligne de commande. Le nom d’utilisateur et le mot de passe que vous employez ici sont utilisés pour la connexion au cluster via SSH.

	Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez l’un des articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Cliquez sur **Source de données** pour choisir une source de données existante pour le cluster ou en créer une. Lors de l'approvisionnement d'un cluster Hadoop dans HDInsight, vous spécifiez un compte Azure Storage. Un conteneur de stockage d'objets blob spécifique de ce compte est désigné en tant que système de fichiers par défaut, comme dans un système de fichiers distribués Hadoop (HDFS). Par défaut, le cluster HDInsight est approvisionné dans le même centre de données que le compte de stockage que vous spécifiez. Pour plus d'informations, consultez [Utilisation du stockage d'objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md).

	![Panneau Source de données](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.4.png "Fournir la configuration de la source de données")

	Actuellement, vous pouvez sélectionner un compte de stockage Azure comme source de données pour un cluster HDInsight. Lisez ce qui suit pour comprendre à quoi correspondent les entrées du panneau **Source de données**.

	- **Méthode de sélection** : définissez cette propriété sur la valeur **De tous les abonnements** pour permettre l’exploration des comptes de stockage de tous vos abonnements. Affectez-lui la valeur **Clé d’accès** si vous souhaitez entrer le **nom de stockage** et la **clé d’accès** d’un compte de stockage existant.

	- **Sélectionner le compte de stockage/Créer un compte** : cliquez sur **Sélectionner le compte de stockage** pour rechercher et sélectionner un compte de stockage existant à associer au cluster. Vous pouvez également cliquer sur **Créer un nouveau** pour créer un nouveau compte de stockage. Utilisez le champ qui s’affiche pour saisir le nom du compte de stockage. Une coche verte s’affiche si le nom est disponible.

	- **Choisir un conteneur par défaut** : utilisez cette option pour entrer le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir n’importe quel nom, mais nous vous conseillons d’utiliser le même nom que le cluster pour pouvoir facilement reconnaître le conteneur utilisé pour ce cluster spécifique.

	- **Emplacement** : zone géographique où se trouve le compte de stockage ou dans laquelle il sera créé.

		> [AZURE.IMPORTANT]La sélection de l’emplacement de la source de données par défaut définira également l’emplacement du cluster HDInsight. Le cluster et la source de données par défaut doivent se trouver dans la même région.

	Cliquez sur **Sélectionner** pour enregistrer la configuration de la source de données.

8. Cliquez sur **Niveaux tarifaires de nœud** pour afficher des informations sur les nœuds qui seront créés pour ce cluster. Définissez le nombre de nœuds worker dont vous avez besoin pour le cluster. Le coût estimé du cluster s’affiche dans le panneau.

	![Panneau Niveaux de tarification du nœud](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.5.png "Spécification du nombre de nœuds de cluster")
    
    > [AZURE.IMPORTANT]Si vous envisagez d’utiliser plus de 32 nœuds worker lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
    >
    > Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez la rubrique [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

	Cliquez sur **Sélectionner** pour enregistrer la configuration de tarification du nœud.

9. Dans le panneau **Nouveau cluster HDInsight**, assurez-vous que l’option **Épingler au tableau d’accueil** est sélectionnée, puis cliquez sur **Créer**. Le cluster est créé et la vignette correspondante ajoutée au tableau d’accueil de votre portail Azure. L'icône indique que le cluster est en cours de configuration et sera modifiée pour représenter l'icône HDInsight une fois la configuration terminée.

Pendant l’approvisionnement|Approvisionnement terminé
------------------|---------------------
	![Indicateur d’approvisionnement sur le tableau d'accueil](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioning.png)|![Vignette de cluster approvisionné](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioned.png)

> [AZURE.NOTE]La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la vignette du Tableau d’accueil, ou l’entrée **Notifications** à gauche de la page pour vérifier le processus d’approvisionnement.

Une fois la configuration terminée, cliquez sur la vignette du cluster dans le tableau d'accueil pour lancer le panneau du cluster.

## <a name="connect"></a> Pour vous connecter au cluster

Vous pouvez vous connecter à un cluster HDInsight sous Linux à partir d'un ordinateur Linux ou Windows à l'aide de SSH.

###Se connecter à partir d'un ordinateur Linux

1. Ouvrez le terminal et exécutez la commande suivante :

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Remplacez &lt;username> par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez &lt;clustername> par le nom de votre cluster.

2. À l'invite, entrez le mot de passe que vous avez donné lors de l'approvisionnement du cluster. Lorsque vous êtes bien connecté, l'invite sera modifiée comme suit :

		hdiuser@hn0-clustername:~$

    > [AZURE.NOTE]La partie `@hn0-clustername` de l’invite de commandes peut être différente sur votre cluster.

###Pour se connecter à partir d'un ordinateur Windows

1. Téléchargez [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) pour les clients Windows.

2. Ouvrez PuTTY. Dans **Catégorie**, cliquez sur **Session**. Dans l’écran **Options de base pour votre session PuTTY**, entrez l'adresse SSH de votre serveur HDInsight dans le champ **Nom d'hôte (ou adresse IP)**. L'adresse SSH est le nom de votre cluster, suivie de **-ssh.azurehdinsight.net**. Par exemple, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Connexion à un cluster HDInsight sous Linux à l'aide de PuTTY](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.linux.connect.putty.png)

3. Pour enregistrer les identifiants de connexion en vue d’une utilisation future, entrez un nom pour cette connexion sous **Sessions enregistrées**, puis cliquez sur **Enregistrer**. La connexion sera ajoutée à la liste des sessions enregistrées.

4. Cliquez sur **Ouvrir** pour vous connecter au cluster. Lorsque vous êtes invité à indiquer le nom d’utilisateur, entrez le nom d’utilisateur SSH utilisé lors de la création du cluster. Pour le mot de passe, entrez le mot de passe que vous avez spécifié lors de la création du cluster. Lorsque vous êtes bien connecté, l'invite sera modifiée comme suit :

		hdiuser@hn0-clustername:~$

##<a name="hivequery"></a>Exécution d’une requête Hive

Une fois que vous êtes connecté au cluster via SSH, utilisez les commandes suivantes pour exécuter une requête Hive :

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

		Query ID = username_20150116000202_cceb9c6b-4356-4931-b9a7-2c373ebba493
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

Dans ce document, vous avez appris à créer un cluster HDInsight Linux à l’aide du portail Azure, à vous connecter au cluster à l’aide de SSH et à effectuer des requêtes Hive de base.

Pour en savoir plus sur l’analyse des données avec HDInsight, consultez les articles suivants :

- Pour en savoir plus sur l’utilisation de Hive avec HDInsight, y compris comment exécuter des requêtes Hive à partir de Visual Studio, consultez la page [Utilisation de Hive avec HDInsight][hdinsight-use-hive].

- Pour en savoir plus sur Pig, un langage utilisé pour transformer les données, consultez la page [Utilisation de Pig avec HDInsight][hdinsight-use-pig].

- Pour en savoir plus sur MapReduce, un moyen d’écrire des programmes pour traiter les données sur Hadoop, consultez la page [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce].

- Pour en savoir plus sur l’utilisation des outils HDInsight pour Visual Studio pour analyser les données sur HDInsight, consultez la page [Prise en main des outils Hadoop de Visual Studio pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Si vous êtes prêt à commencer à utiliser vos propres données et que vous avez besoin d’en savoir plus sur la façon dont HDInsight stocke les données ou sur l’ajout de données dans HDInsight, consultez les articles suivants :

- Pour plus d’informations sur la façon dont HDInsight utilise le stockage d’objets blob Azure, consultez la page [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md).

- Pour plus d’informations sur le téléchargement de données dans HDInsight, consultez la page [Téléchargement de données dans HDInsight][hdinsight-upload-data].

Si vous voulez en savoir plus sur la création ou la gestion d’un cluster HDInsight, consultez les rubriques suivantes :

- Pour en savoir plus sur la gestion de votre cluster HDInsight Linux, consultez la page [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md).

- Pour en savoir plus sur les options que vous pouvez sélectionner pendant la création d’un cluster HDInsight, consultez la page [Approvisionnement de HDInsight sur Linux à l’aide des options personnalisées](hdinsight-hadoop-provision-linux-clusters.md).

- Si vous maîtrisez Linux et Hadoop, mais que vous souhaitez connaître les spécificités de Hadoop sur HDInsight, consultez la page [Utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md). Cette rubrique vous fournit des informations telles que :

	* les URL correspondant aux services hébergés sur le cluster, tels qu'Ambari et WebHCat
	* l'emplacement des fichiers Hadoop et des exemples sur le système de fichiers local
	* l'utilisation de stockage d'Azure Storage (WASB) au lieu de HDFS, en tant que stockage de données par défaut


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

<!---HONumber=AcomDC_1203_2015-->