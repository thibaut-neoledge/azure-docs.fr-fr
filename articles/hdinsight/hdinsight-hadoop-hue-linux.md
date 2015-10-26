<properties
	pageTitle="Utilisez la teinte avec Hadoop sur les clusters HDInsight Linux | Microsoft Azure"
	description="Installer et utiliser Hue avec les clusters Hadoop sur HDInsight Linux"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2015" 
	ms.author="nitinme"/>

# Installation et utilisation de Hue sur des clusters HDInsight Hadoop

Apprenez comment installer Hue sur les clusters HDInsight Linux et utiliser le tunnel pour acheminer les demandes de Hue.

## Qu’est-ce que Hue ?

Hue est un ensemble d’applications web permettant d’interagir avec un cluster Hadoop. Vous pouvez utiliser Hue pour parcourir le stockage associé à un cluster Hadoop (WASB, dans le cas de clusters HDInsight), exécutez les tâches Hive et les scripts Pig, etc.. Les composants suivants sont pris en charge avec l’installation de Hue sur un cluster HDInsight Hadoop.

* Éditeur Beeswax Hive
* Pig
* Gestionnaire de Metastore
* Oozie
* FileBrowser (qui communique avec le conteneur WASB par défaut)
* Explorateur de travaux


## Installer Hue à l’aide d’actions de script

L’action de script [https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/r-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh) permet d’installer Hue sur un cluster HDInsight. Cette section explique comment utiliser le script lors de l’approvisionnement du cluster à l’aide du portail Azure.

> [AZURE.NOTE]Vous pouvez également utiliser Azure PowerShell ou le Kit de développement logiciel (SDK) .NET HDInsight pour créer un cluster à l’aide de ce script. Pour plus d’informations sur ces méthodes, consultez [Personnaliser des clusters HDInsight à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrez l’approvisionnement d’un cluster à l’aide de la procédure décrite dans [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mais n’achevez pas l’approvisionnement.

	> [AZURE.NOTE]Pour installer Hue sur des clusters HDInsight, il est recommandé d’utiliser une taille de nœud principal égale ou supérieure à A4 (8 cœurs, 14 Go de mémoire).

2. Dans le panneau **Configuration facultative**, sélectionnez **Actions de script**, puis indiquez les informations ci-dessous :

	* __NAME__ : entrez un nom convivial pour l’action de script.
	* __SCRIPT URI__ : https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh
	* __HEAD__ : cochez cette option.
	* __WORKER__ : laissez ce champ vide.
	* __ZOOKEEPER__ : laissez ce champ vide.
	* __PARAMETERS__ : le script attend le **mot de passe de l’administrateur du cluster** en tant que paramètre. Il s’agit du mot de passe que vous avez indiqué lors de l’approvisionnement du cluster. Vous devez spécifier le mot de passe entre guillemets simples.


3. En bas de l’écran **Actions de script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** au bas du panneau **Configuration facultative** pour enregistrer les informations de configuration facultatives.

4. Continuez l’approvisionnement du cluster, comme indiqué dans [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## Utilisez Hue avec les clusters HDInsight

Le tunneling SSH est le seul moyen d’accéder à Hue sur le cluster une fois qu’il est en cours d’exécution. Le tunneling via SSH autorise le trafic à atteindre directement le nœud principal du cluster exécutant Hue. Une fois que le cluster a terminé l’approvisionnement, procédez comme suit pour utiliser Hue sur un cluster HDInsight Linux.

1. Utilisez les informations de la section [Utilisation de SSH Tunneling pour accéder à l'interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d'autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md) pour créer un tunnel SSH à partir de votre système client au cluster HDInsight, puis configurer votre navigateur Web pour utiliser le tunnel SSH en tant que proxy.

2. Une fois que vous avez créé un tunnel SSH et configuré votre navigateur pour y diriger le trafic, utilisez le navigateur pour ouvrir le portail Hue sur http://headnode0:8888.

    > [AZURE.NOTE]Lorsque vous vous connectez pour la première fois, vous êtes invité à créer un compte pour vous connecter au portail Hue. Les informations d’identification que vous spécifiez ici seront limitées au portail et ne sont pas liées à l’administrateur ou aux informations d’identification de l’utilisateur SSH spécifié lors de la configuration du cluster.

	![Se connecter au portail Hue](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Spécifiez les informations d’identification due portail Hue")

### Exécution d'une tâche Hive

1. À partir du portail Hue, cliquez sur **Éditeurs de requête**, puis sur **Hive** pour ouvrir l'éditeur Hive.

	![Utiliser Hive](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Utiliser Hive")

2. Sous l'onglet **Aide**, sous **Base de données**, vous devez voir **hivesampletable**. Il s’agit d’une table d’échantillon qui est livrée avec tous les clusters Hadoop sur HDInsight. Entrez un exemple de requête dans le volet de droite et vérifiez la sortie sous l'onglet **Résultats** dans le volet ci-dessous, comme illustré dans la capture d'écran.

	![Exécuter une requête Hive](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Exécuter une requête Hive")

	Vous pouvez également utiliser l'onglet **Graphique** pour afficher une représentation visuelle du résultat.

### Parcourir le stockage de cluster

1. À partir du portail Hue, cliquez sur **Explorateur de fichiers** dans l'angle supérieur droit de la barre de menus.

2. Par défaut, l'explorateur de fichiers s'ouvre sur le répertoire **/user/myuser**. Cliquez sur la barre oblique située avant le répertoire de l’utilisateur dans le chemin d’accès pour accéder à la racine du conteneur de stockage Azure associé au cluster.

	![Utiliser l’Explorateur de fichiers](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Utiliser l’Explorateur de fichiers")

3. Cliquez avec le bouton droit sur un fichier ou un dossier pour afficher les opérations disponibles. Utilisez le bouton **Télécharger** situé dans le coin droit pour télécharger des fichiers dans le répertoire actif. Utilisez le bouton **Nouveau** pour créer des fichiers ou des répertoires.

> [AZURE.NOTE]L’Explorateur de fichiers Hue peut afficher uniquement le contenu du conteneur par défaut associé au cluster HDInsight. Les comptes/conteneurs de stockage supplémentaires que vous avez peut-être associés au cluster ne seront pas accessibles via l’Explorateur de fichiers. Toutefois, les autres conteneurs associés au cluster seront toujours accessibles pour les travaux Hive. Par exemple, si vous entrez la commande `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` dans l'éditeur Hive, vous pouvez aussi voir le contenu de conteneurs supplémentaires. Dans cette commande, **newcontainer** n'est pas le conteneur par défaut associé à un cluster.

## Points importants à prendre en compte

1. Le script utilisé pour installer Hue exécute l’opération uniquement sur le HEADNODE0 du cluster.

2. Pendant l’installation, plusieurs services Hadoop (HDFS, fils, RM2, Oozie) sont redémarrés pour mettre à jour la configuration. Une fois que le script a terminé l’installation de Hue, le démarrage d’autres services Hadoop peut prendre du temps. Cela peut affecter dans un premier temps les performances de Hue. Une fois que tous les services ont démarré, Hue est complètement fonctionnel.

3.	Teinte ne comprend pas les travaux Tez, qui est la valeur par défaut de Hive. Si vous souhaitez utiliser MapReduce comme moteur d’exécution de Hive, mettez à jour le script pour utiliser la commande suivante dans votre script :

		set hive.execution.engine=mr;

4.	Avec les clusters Linux, vous pouvez avoir un scénario dans lequel vos services fonctionnent sur HEADNODE0 alors que le Gestionnaire de ressources s’exécute sur HEADNODE1. Un tel scénario peut entraîner des erreurs (illustrées ci-dessous) lors de l’utilisation de Hue pour afficher les détails des travaux EN COURS sur le cluster. Toutefois, vous pouvez afficher les détails du travail lorsque la tâche est terminée.

	![Erreur de portail Hue](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Erreur de portail Hue")

	Il s’agit d’un problème connu. Pour résoudre ce problème, modifiez Ambari afin que le Gestionnaire de ressources actif s’exécute également sur HEADNODE0.

5.	Hue comprend WebHDFS, tandis que les clusters HDInsight utilisent Azure Storage avec `wasb://`. Par conséquent, le script personnalisé utilisé avec l’action de script installe WebWasb, qui est un service compatible WebHDFS permettant de communiquer avec WASB. Donc, bien que le portail Hue indique HDFS à certains endroits (comme lorsque vous déplacez votre souris sur l'**Explorateur de fichiers**), il doit être compris comme WASB.


## Étapes suivantes

- [Installation et utilisation de Spark sur des clusters HDInsight](hdinsight-hadoop-spark-install-linux.md) pour obtenir des instructions sur la personnalisation de clusters pour installer et utiliser Spark sur des clusters Hadoop HDInsight. Spark est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d'analyse de « big data ».

- [Installation de Giraph sur des clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight.

- [Installation de Solr sur des clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Solr vous permet d’effectuer de puissantes opérations de recherche sur des données stockées.

- [Installation de R sur des clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md). Utilisez la personnalisation de clusters pour installer R sur des clusters HDInsight Hadoop. R se compose d'un langage et d'un environnement open source destinés au calcul de statistiques. Il intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md

<!---HONumber=Oct15_HO3-->