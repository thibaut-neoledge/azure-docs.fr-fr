<properties
   pageTitle="Didacticiel Linux : prise en main de Hadoop et Hive | Microsoft Azure"
   description="Suivez ce didacticiel Linux pour apprendre à utiliser Hadoop dans HDInsight. Découvrez comment approvisionner des clusters Linux et interroger des données avec Hive."
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/13/2015"
   ms.author="nitinme"/>

# Didacticiel Hadoop : prise en main de Hadoop avec Hive dans HDInsight sur Linux (version préliminaire)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Ce didacticiel Hadoop vous permet de vous familiariser rapidement avec Azure HDInsight sous Linux en vous montrant comment approvisionner un cluster Hadoop sous Linux et exécuter une requête Hive pour extraire des informations utiles à partir des données non structurées.


> [AZURE.NOTE]Si vous ne connaissez pas Hadoop et les données volumineuses, vous pouvez en savoir plus sur les termes <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS (Hadoop Distributed File System)</a> et <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Pour comprendre la manière dont HDInsight prend en charge Hadoop dans Azure, consultez la rubrique [Présentation de Hadoop dans HDInsight](hdinsight-hadoop-introduction.md).


## Objectif de ce didacticiel ##

Supposons que vous disposiez d'un vaste jeu de données non structurées et que vous souhaitiez y exécuter des requêtes pour extraire des informations significatives. Voici comment obtenir ce résultat :

   ![Étapes du didacticiel Hadoop : Création d’un compte de stockage ; Configuration d’un cluster Hadoop ; Interrogation des données avec Hive.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.Linux.GetStartedFlow.png)


## Composants requis

Avant de commencer ce didacticiel Linux pour Hadoop, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

**Durée estimée :** 30 minutes

## Dans ce didacticiel

* [Création d’un compte de stockage Azure](#storage)
* [Approvisionnement d’un cluster HDInsight Linux](#provision)
* [Envoi d’une tâche Hive sur le cluster](#hivequery)
* [Étapes suivantes](#nextsteps)

## <a name="storage"></a>Création d’un compte de stockage Azure

HDInsight utilise le stockage d’objets blob Azure pour stocker les données. Pour plus d'informations, consultez la page [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).

Lors de l'approvisionnement d'un cluster HDInsight, vous spécifiez un compte Azure Storage. Un conteneur de stockage d'objets blob spécifique de ce compte est désigné en tant que système de fichiers par défaut, comme dans HDFS. Le cluster HDInsight est, par défaut, approvisionné dans le même centre de données que le compte de stockage que vous spécifiez.

En plus de ce compte de stockage, vous pouvez ajouter les autres comptes de stockage lorsque vous personnalisez la configuration d’un cluster HDInsight. Ces comptes de stockage supplémentaires peuvent provenir du même abonnement Azure ou de différents abonnements Azure. Pour obtenir des instructions, consultez la page [Approvisionnement de clusters HDInsight Linux à l'aide d'options personnalisées](hdinsight-hadoop-provision-linux-clusters.md).

Pour simplifier ce didacticiel, seul le conteneur d'objets blob par défaut et le compte de stockage par défaut sont utilisés. Dans la pratique, les fichiers de données sont généralement stockées dans un compte de stockage désigné.

**Pour créer un compte de stockage Azure**

1. Connectez-vous au <a href="https://manage.windowsazure.com/" target="_blank">portail Azure</a>.
2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **DATA SERVICES**, pointez sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

	![Portail Azure où vous pouvez utiliser Création rapide pour configurer un nouveau compte de stockage.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.StorageAccount.QuickCreate.png)

3. Entrez les informations pour **URL**, **EMPLACEMENT** et **RÉPLICATION**, puis cliquez sur **CRÉER UN COMPTE DE STOCKAGE**. Les groupes d'affinités ne sont pas pris en charge. Le nouveau compte de stockage s’affiche dans la liste de stockage.

	>[AZURE.NOTE]L'option Création rapide pour l’approvisionnement d’un cluster HDInsight Linux, tel celui que nous utilisons dans ce didacticiel, ne demande pas un emplacement lors de l’approvisionnement du cluster. Par défaut, elle colocalise le cluster dans le même centre de données que le compte de stockage. Par conséquent, veillez à créer votre compte de stockage dans les emplacements pris en charge pour le cluster, à savoir : **Asie de l’Est**, **Asie du Sud-Est**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Nord du centre des États-Unis**, **Sud du centre des États-Unis**.

4. Attendez que l’**ÉTAT** du nouveau compte de stockage indique **En ligne**.
5. Sélectionnez le nouveau compte de stockage dans la liste et cliquez sur **GÉRER LES CLÉS D’ACCÈS** au bas de la page.
7. Prenez note des informations pour **NOM DU COMPTE DE STOCKAGE** et **CLÉ D’ACCÈS PRIMAIRE** (ou **CLÉ D’ACCÈS SECONDAIRE** ; les deux clés fonctionnent). Vous en aurez besoin plus loin dans le didacticiel.


Pour plus d'informations, consultez les pages [Création d'un compte de stockage](../storage-create-storage-account.md) et [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).

## <a name="provision"></a>Approvisionnement d’un cluster HDInsight sous Linux

Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez les ressources de calcul Azure contenant Hadoop et ses applications associées. Dans cette section, vous allez approvisionner un cluster HDInsight sous Linux à l'aide de l'option Création rapide. Cette option utilise les noms d'utilisateur et les conteneurs de stockage Azure par défaut, et configure un cluster avec HDInsight version 3.2 (Hadoop version 2.6, Hortonworks Data Platform version 2.2 Hadoop) s'exécutant sur Ubuntu 12.04 LTS (prise en charge à long terme). Pour plus d'informations sur les différentes versions de HDInsight et leurs contrats de niveau de service, consultez la page [Versions de HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE]Vous pouvez également créer des clusters Hadoop exécutant le système d'exploitation Windows Server. Pour obtenir des instructions, consultez la page [Prise en main de HDInsight](../hdinsight-get-started.md).


**Pour approvisionner un cluster HDInsight**

1. Connectez-vous au <a href="https://manage.windowsazure.com/" target="_blank">portail Azure</a>.

2. Cliquez sur **NOUVEAU** sur le côté inférieur gauche, sur **DATA SERVICES**, sur **HDINSIGHT**, puis sur **HADOOP SOUS LINUX**.

	![Création d'un cluster Hadoop dans HDInsight.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.QuickCreateCluster.png)

4. Entrez ou sélectionnez les valeurs suivantes :

	<table border="1">
<tr><th>Nom</th><th>Valeur</th></tr>
<tr><td>Nom du cluster</td><td>Nom du cluster.</td></tr>
<tr><td>Cluster Size</td><td>Nombre de nœuds de données que vous souhaitez déployer. La valeur par défaut est 4. mais le menu déroulant propose également 1 ou 2&#160;nœuds de données. Vous pouvez spécifier n'importe quel nombre de clusters en utilisant l'option <strong>Création personnalisée</strong>. Des détails de tarification sur les taux de facturation en fonction des tailles de cluster sont disponibles. Cliquez sur le symbole <strong>?</strong> juste au-dessus de la zone de liste déroulante et suivez le lien dans le menu contextuel.</td></tr>
<tr><td>Mot de passe</td><td>Le mot de passe du compte <i>HTTP</i> (nom d'utilisateur par défaut&#160;: administrateur) et du compte <i>SSH</i> (nom d'utilisateur par défaut&#160;: hdiuser). Notez que ces comptes ne sont PAS les comptes d'administrateur pour les machines virtuelles sur lesquelles les clusters sont approvisionnés. </td></tr>

<tr><td>Compte de stockage</td><td>Sélectionnez le compte de stockage que vous avez créé dans la zone de liste déroulante. <br/>

Une fois qu'un compte de stockage est choisi, il ne peut pas être modifié. Si le compte de stockage est supprimé, le cluster n’est plus disponible. Le cluster HDInsight est colocalisé dans le même centre de données que le compte de stockage.
</td></tr>
</table>Conservez une copie du nom de cluster. Vous en aurez besoin plus loin dans le didacticiel.


5. Cliquez sur **CRÉER UN CLUSTER HDINSIGHT**. Lorsque l’approvisionnement est terminé, la colonne d’état indique **En cours d'exécution**.

	>[AZURE.NOTE]La procédure ci-dessus crée un cluster Linux avec l'option Création rapide qui utilise le nom d'utilisateur SSH et les conteneurs de stockage Azure par défaut. Pour créer un cluster avec des options personnalisées, par exemple, en utilisant une clé SSH pour l'authentification ou en utilisant des comptes de stockage supplémentaires, consultez la page [Approvisionnement de clusters HDInsight Linux à l'aide d'options personnalisées](hdinsight-hadoop-provision-linux-clusters.md).


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

- [Approvisionnement de HDInsight sous Linux à l'aide d’options personnalisées](hdinsight-hadoop-provision-linux-clusters.md)
- [Utilisation de HDInsight sous Linux](hdinsight-hadoop-linux-information.md)
- [Gestion des clusters HDInsight à l'aide d'Ambari](hdinsight-hadoop-manage-ambari.md)
- [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce]
- [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
- [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
- [Utilisation du stockage d'objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md)
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
 

<!---HONumber=62-->