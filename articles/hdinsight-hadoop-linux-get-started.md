<properties 
   pageTitle="Prise en main de Hadoop avec Hive dans HDInsight sur Linux| Azure" 
   description="Prise en main de Hadoop dans HDInsight sur Linux. Apprenez comment approvisionner les clusters Hadoop dans HDInsight qui s'exécutent sur Linux et interroger les données avec Hive." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>

# Prise en main de Hadoop avec Hive dans HDInsight sur Linux (aperçu)

Ce didacticiel vous offre une prise en main rapide de HDInsight sur Linux en vous montrant comment approvisionner un cluster Hadoop dans HDInsight sur Linux et exécuter une requête Hive pour l'extraction d'informations utiles à partir de données non structurées. Vous pourrez ensuite analyser les résultats dans outil décisionnel tel que Tableau.


> [AZURE.NOTE] Si vous ne connaissez pas Hadoop et les données volumineuses (Big Data), vous pouvez vous familiariser avec les termes <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS</a> et <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Pour comprendre la manière dont HDInsight prend en charge Hadoop dans Azure, consultez la page [Présentation de Hadoop dans HDInsight](../hdinsight-hadoop-introduction/).


## Objectif de ce didacticiel ##

Supposons que vous disposiez d'un vaste jeu de données non structurées et que vous souhaitiez y exécuter des requêtes pour extraire des informations significatives. Voici comment procéder :

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Tableau.](./media/hdinsight-hadoop-linux-get-started/HDI.Linux.GetStartedFlow.png)


**Conditions préalables :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :


- Un abonnement Azure. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les pages traitant des <a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">formules d'abonnement</a>, des <a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">offres spéciales membres</a> ou de la <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">version d'évaluation gratuite</a>.

**Durée estimée :** 30 minutes

## Dans ce didacticiel

* [Création d'un compte Azure Storage](#storage)
* [Approvisionnement d'un cluster HDInsight sur Linux](#provision)
* [Envoi d'une tâche Hive](#hivequery)
* [Étapes suivantes](#nextsteps)

## <a name="storage"></a>Création d'un compte Azure Storage

HDInsight utilise le stockage d'objets blob Azure pour stocker des données. On utilise l'appellation  *WASB* ou  *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft du HDFS sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight](../hdinsight-use-blob-storage/).

Lors de l'approvisionnement d'un cluster HDInsight, vous spécifiez un compte Azure Storage. Un conteneur de stockage d'objets blob spécifique de ce compte est désigné en tant que système de fichiers par défaut, comme dans HDFS. Le cluster HDInsight est approvisionné par défaut dans le même centre de données que le compte de stockage que vous spécifiez.

En plus de ce compte de stockage, vous pouvez ajouter des comptes de stockage supplémentaires lorsque vous configurez de façon personnalisée un cluster HDInsight. Ce compte de stockage supplémentaire peut être ajouté à partir du même abonnement Azure ou à partir d'autres abonnements Azure. Pour plus d'informations, consultez la page [Approvisionnement de clusters HDInsight sur Linux au moyen d'options personnalisées](../hdinsight-hadoop-provision-linux-clusters). 

Afin de simplifier ce didacticiel, seuls le conteneur d'objets blob par défaut et le compte de stockage par défaut sont utilisés. En pratique, les fichiers de données sont généralement stockés dans un compte de stockage précis.

**Création d'un compte de stockage Azure**

1. Connectez-vous au <a href="https://manage.windowsazure.com/" target="_blank">portail de gestion Azure</a>.
2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

	![Azure portal where you can use Quick Create to set up a new storage account.](./media/hdinsight-hadoop-linux-get-started/HDI.StorageAccount.QuickCreate.png)

3. Entrez l'**URL**, l'**EMPLACEMENT** et la **RÉPLICATION**, puis cliquez sur **CRÉER UN COMPTE DE STOCKAGE**. Les groupes d'affinités ne sont pas pris en charge. Le nouveau compte de stockage figure dès lors dans la liste de stockage.

	>[AZURE.NOTE]  L'option de création rapide permettant d'approvisionner un cluster HDInsight sur Linux, comme celui que nous utilisons dans ce didacticiel, ne demande pas d'emplacement pendant l'approvisionnement du cluster. Il colocalise par défaut le cluster dans le même centre de données que le compte de stockage. Par conséquent, veillez à créer votre compte de stockage dans les emplacements pris en charge pour le cluster, à savoir :  **Asie orientale**, **Asie du Sud-Est**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Nord du centre des États-Unis**, **Sud du centre des États-Unis**.

4. Attendez que le **STATUT** du nouveau compte de stockage passe à **Online**.
5. Sélectionnez le nouveau compte de stockage dans la liste et cliquez sur **GÉRER LES CLÉS D'ACCÈS** au bas de la page.
7. Notez le **NOM DU COMPTE DE STOCKAGE** et la **CLÉ D'ACCÈS PRIMAIRE** (ou la **CLÉ D'ACCÈS SECONDAIRE**. Les deux clés fonctionnent).  Vous en aurez besoin plus loin dans le didacticiel.


Pour plus d'informations, consultez les pages
[Création d'un compte de stockage](../storage-create-storage-account/) et [Utilisation du stockage d'objets blob Azure avec HDInsight](../hdinsight-use-blob-storage/).
	
## <a name="provision"></a>Approvisionnement d'un cluster HDInsight sur Linux

Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez des ressources de calcul Azure contenant Hadoop et des applications connexes. Dans cette section, vous approvisionnez un cluster HDInsight sur Linux à l'aide de l'option de création rapide. Cette option utilise par défaut les noms d'utilisateurs et les conteneurs de stockage Azure et configure un cluster avec HDInsight version 3.2 (Hadoop version 2.5, HDP version 2.2) exécutée sur Ubuntu 12.04 LTS. Pour plus d'informations sur les différentes versions de HDInsight et leurs contrats SLA, consultez la page [Contrôle de version des composants HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

>[AZURE.NOTE]  Vous pouvez également créer des clusters Hadoop qui s'exécutent sur le système d'exploitation Windows Server. Pour obtenir des instructions, consultez [Prise en main de HDInsight sur Windows](../hdinsight-get-started/).


**Pour approvisionner un cluster HDInsight**

1. Connectez-vous au <a href="https://manage.windowsazure.com/" target="_blank">portail de gestion Azure</a>. 

2. Dans la partie inférieure gauche de l'écran, cliquez sur **NOUVEAU**, sur **Services de données**, sur **HDInsight**, puis sur **Hadoop sur Linux**.

	![Creation of a Hadoop cluster in HDInsight.](./media/hdinsight-hadoop-linux-get-started/HDI.QuickCreateCluster.png)

4. Entrez ou sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td>Nom du cluster</td><td>Nom du cluster</td></tr>
	<tr><td>Taille du cluster</td><td>Nombre de nœuds de données que vous souhaitez déployer. La valeur par défaut est 4, mais le menu déroulant propose également 1 ou 2 nœuds de données. Vous pouvez spécifier n'importe quel quantité de clusters en utilisant l'option <strong>Création personnalisée</strong>. Des détails de tarification sur les taux de facturation en fonction des tailles de cluster sont disponibles. Cliquez sur le symbole <strong>?</strong> juste au-dessus de la zone déroulante et suivez le lien dans la fenêtre contextuelle.</td></tr>
	<tr><td>Mot de passe</td><td>Le mot de passe pour le compte <i>HTTP</i> (nom d'utilisateur par défaut : admin) et le compte <i>SSH</i> (nom d'utilisateur par défaut : hdiuser). Notez qu'il ne s'agit PAS des comptes d'administrateur pour les machines virtuelles sur lesquelles les clusters sont approvisionnés. </td></tr>
	
	<tr><td>Compte de stockage</td><td>Sélectionnez le compte de stockage que vous avez créé dans la zone déroulante. <br/>

	Dès lors qu'un compte de stockage est sélectionné, il ne peut plus être modifié. Si le compte de stockage est supprimé, le cluster ne peut plus être utilisé.

	Le cluster HDInsight est colocalisé dans le même centre de données que le compte de stockage. 
	</td></tr>
	</table>

	Conservez une copie du nom de cluster. Vous en aurez besoin plus loin dans le didacticiel.

	
5. Cliquez sur **Créer un cluster HDInsight**. À l'issue de l'approvisionnement, la colonne de statut indique **En cours d'exécution**.

	>[AZURE.NOTE] La procédure ci-dessus crée un cluster Linux avec l'option de création rapide qui utilise le nom d'utilisateur SSH par défaut et les conteneurs de stockage Azure. Pour créer un cluster avec des options personnalisées telles que l'utilisation d'une clé SSH pour l'authentification ou l'utilisation de comptes de stockage supplémentaires, consultez [Approvisionnement des clusters HDInsight sur Linux à l'aide des options personnalisées](../hdinsight-hadoop-provision-linux-clusters).


## <a name="hivequery"></a>Envoi d'une tâche Hive sur le cluster
À présent qu'un cluster HDInsight sur Linux a été approvisionné, la prochaine étape consiste à exécuter un exemple de tâche Hive afin d'interroger un exemple de données (sample.log) fourni avec les clusters HDInsight. L'exemple de données contient les informations de journal y compris le suivi, les avertissements, les informations, les erreurs, etc. Nous interrogeons ces données pour récupérer tous les journaux d'erreurs avec une gravité spécifique. Vous devez effectuer les étapes suivantes pour exécuter la requête Hive sur un cluster HDInsight sur Linux.

- Connexion à un cluster Linux
- Exécution d'une tâche Hive



### Pour se connecter à un cluster

Vous pouvez vous connecter à un cluster HDInsight sur Linux à partir d'un ordinateur Linux ou d'un ordinateur Windows à l'aide du SSH.

**Se connecter à partir d'un ordinateur Linux**

1. Ouvrez le terminal et exécutez la commande suivante :

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Puisque vous avez approvisionné un cluster avec l'option de création rapide, le nom d'utilisateur SSH par défaut est **hdiuser**. La commande doit donc être

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. À l'invite, entrez le mot de passe que vous avez donné lors de l'approvisionnement du cluster. Lorsque vous êtes bien connecté, l'invite sera modifiée comme suit :

		hdiuser@headnode-0:~$


**Se connecter à partir d'un ordinateur Windows**

1. Télécharger **PuTTY** pour les clients Windows. Ce programme est disponible depuis <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

2. Ouvrez **PuTTY**. Dans **Catégorie**, cliquez sur **Session**. À partir de l'écran **Options de base pour votre session PuTTy**, entrez l'adresse SSH de votre serveur HDInsight dans le champ **nom d'hôte (adresse IP)**. L'adresse SSH est le nom de votre cluster suivi de**-ssh.azurehdinsight.net**. Par exemple, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Connect to an HDInsight cluster on Linux using PuTTY](./media/hdinsight-hadoop-linux-get-started/HDI.linux.connect.putty.png)

3. Pour enregistrer les informations de connexion en vue d'une utilisation future, entrez un nom pour cette connexion sous **Sessions enregistrées**, puis cliquez sur **Enregistrer**. La connexion sera ajoutée à la liste des sessions enregistrées.

4. Cliquez sur **Ouvrir** pour vous connecter au cluster. Quand vous êtes invité à entrer le nom d'utilisateur, entrez  *hdiuser*. Pour le mot de passe, entrez le mot de passe que vous avez indiqué lors de l'approvisionnement du cluster. Lorsque vous êtes bien connecté, l'invite sera modifiée comme suit :

		hdiuser@headnode-0:~$

### Exécuter une tâche Hive

Lorsque vous êtes connecté au cluster à l'aide du SSH, utilisez les commandes suivantes pour exécuter une requête Hive.

1. Démarrez l'interface en ligne de commande Hive à l'aide de la commande suivante à l'invite :

		hive

2. À l'aide de l'interface en ligne de commande, entrez les instructions suivantes pour créer une nouvelle table appelée log4jLogs au moyen de l'exemple de données déjà disponible sur le cluster.

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Ces instructions effectuent les opérations suivantes :

	- **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.
	- **CREATE EXTERNAL TABLE** : crée une table externe dans Hive. Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à leur emplacement d'origine.
	- **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.
	- **STORED AS TEXTFILE LOCATION** : indique à Hive l'emplacement des données (le répertoire exemple/données) et précise qu'elles sont stockées sous la forme de texte.
	- **SELECT** : sélectionne toutes les lignes dont la colonne t4 contient la valeur [ERROR].

	>[WACOM.NOTE] Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, ou par une autre opération MapReduce, mais souhaitez toujours que les requêtes Hive utilisent les données les plus récentes. La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

	Cela renvoie la sortie suivante.

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

	Notez que la sortie contient **[ERROR]  3** puisque trois lignes contiennent cette valeur.

3. Utilisez les instructions suivantes pour créer une nouvelle table " interne " appelée **errorLogs**.

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	Ces instructions effectuent les opérations suivantes :

	- **CREATE TABLE IF NOT EXISTS** : crée une table, le cas échéant. Le mot-clé EXTERNAL n'étant pas utilisé, il s'agit d'une table interne, stockée dans l'entrepôt de données Hive et gérée intégralement par Hive. Contrairement aux tables **EXTERNAL**, la suppression d'une table interne entraîne également la suppression des données sous-jacentes.
	- **STORED AS ORC** : stocke les données au format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
	- **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contiennent [ERROR], puis insère les données dans la table **errorLogs**.

4. Pour vérifier que seules les lignes contenant **[ERROR]** dans la colonne t4 ont été stockées dans la table **errorLogs**, utilisez l'instruction suivante pour renvoyer toutes les lignes à partir d'errorLogs.

		SELECT * from errorLogs;

	The following output should be displayed on the console.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	The returned data should all correspond to [ERROR] logs.


## <a name="nextsteps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à approvisionner un cluster Hadoop sur Linux avec HDInsight et à exécuter une requête Hive sur celui-ci en utilisant le SSH. Pour en savoir plus, consultez les articles suivants :

- [Approvisionnement de HDInsight sur Linux à l'aide des options personnalisées](../hdinsight-hadoop-provision-linux-clusters)
- [Utilisation de HDInsight sur Linux](../hdinsight-hadoop-linux-information)
- [Gestion des clusters HDInsight à l'aide d'Ambari](../hdinsight-hadoop-manage-ambari)
- [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce]
- [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
- [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
- [Utilisation du stockage d'objets blob Azure avec HDInsight](../hdinsight-use-blob-storage)
- [Téléchargement de données vers HDInsight][hdinsight-upload-data]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install

[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=47-->
