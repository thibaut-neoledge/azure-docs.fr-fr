<properties
	pageTitle="Didacticiel Apache Storm : prise en main de Storm sur HDInsight basé sur Linux | Microsoft Azure"
	description="Prise en main de l’analyse Big Data avec Apache Storm et des exemples Starter Storm sur HDInsight basé sur HDInsight. Découvrez comment utiliser Storm pour traiter les données en temps réel."
	keywords="apache storm,didacticiel apache storm,analyses big data,prise en main storm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/16/2016"
   ms.author="larryfr"/>


# Didacticiel Apache Storm : prise en main d'exemples Storm Starter pour l'analyse de données volumineuses (« Big Data ») sur HDInsight

Apache Storm est un système de calcul en temps réel, évolutif, distribué, à tolérance de panne, qui permet de traiter des flux de données. Avec Storm sur Azure HDInsight, vous pouvez créer un cluster Storm basé sur le cloud qui effectue l’analyse de données volumineuses en temps réel.

> [AZURE.NOTE] Les étapes décrites dans cet article créent un cluster HDInsight Linux. Pour savoir comment créer un cluster Storm Windows sur HDInsight, consultez le [Didacticiel Apache Storm : prise en main de l’exemple Storm Starter à l’aide de l’analyse de données sur HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

## Avant de commencer

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Vous devez disposer de ce qui suit pour suivre jusqu’au bout ce didacticiel Storm Apache :

- **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Des connaissances en SSH et SCP**. Pour plus d’informations sur l’utilisation de SSH et SCP avec HDInsight, consultez les articles suivants :

    - **Clients Linux, Unix ou OS X** : consultez la page [Utilisation de SSH avec Hadoop dans HDInsight sous Linux à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	- **Clients Windows** : consultez la page [Utilisation de SSH avec Hadoop Linux dans HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Créer un cluster Storm

Dans cette section, vous allez créer un cluster HDInsight version 3.2 (Storm version 0.9.3) à l’aide d’un modèle Azure Resource Manager. Pour en savoir plus sur les différentes versions de HDInsight et leurs contrats SLA, consultez la page [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md). Pour obtenir d’autres méthodes de création de cluster, consultez [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-storm-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Le modèle est situé dans un conteneur d’objets blob public, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*.
   
2. À partir du panneau Paramètres, saisissez les informations suivantes :

    - **ClusterName** : entrez un nom pour le cluster Hadoop que vous allez créer.
    - **Nom d’utilisateur et mot de passe de cluster** : le nom de connexion par défaut est admin.
    - **Nom d’utilisateur et mot de passe SSH**.
    
    Veuillez noter ces valeurs. Vous en aurez besoin plus loin dans le didacticiel.

    > [AZURE.NOTE] SSH permet d’accéder à distance au cluster HDInsight à l’aide d’une ligne de commande. Le nom d’utilisateur et le mot de passe que vous employez ici sont utilisés pour la connexion au cluster via SSH. Par ailleurs, le nom d’utilisateur SSH doit être unique, car il crée un compte d’utilisateur sur tous les nœuds du cluster HDInsight. Voici quelques-uns des noms de compte dont l’usage est réservé aux services sur le cluster et qui ne peuvent pas être utilisés en tant que nom d’utilisateur SSH :
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	> Pour plus d'informations sur l'utilisation de SSH avec HDInsight, consultez l'un des articles suivants :

	>* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	>* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3\.Cliquez sur **OK** pour enregistrer les paramètres.

4\.Dans le panneau **Déploiement personnalisé**, cliquez sur la zone de liste déroulante **Groupe de ressources**, puis cliquez sur **Nouveau** pour créer un nouveau groupe de ressources. Le groupe de ressources est un conteneur qui regroupe le cluster, le compte de stockage dépendant et une autre ressource liée.

5\.Cliquez sur **Conditions juridiques**, puis cliquez sur **Créer**.

6\.Cliquez sur **Créer**. Vous verrez une nouvelle vignette intitulée Envoi du déploiement pour Déploiement de modèle. La création du cluster et de la base de données SQL prend environ 20 minutes.


##Exécution d’un exemple Starter Storm sur HDInsight

Les exemples [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) sont inclus dans le cluster HDInsight. Dans les étapes suivantes, vous allez exécuter l’exemple WordCount.

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Si vous utilisez un mot de passe pour sécuriser votre compte utilisateur SSH, vous serez invité à le saisir. Si vous utilisez une clé publique, vous devrez peut-être utiliser le paramètre `-i` pour spécifier la clé privée correspondante. Par exemple : `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
		
	Pour plus d’informations sur l’utilisation de SSH avec HDInsight sous Linux, consultez les articles suivants :
	
	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows)

2. Utilisez la commande suivante pour démarrer un exemple de topologie :

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.10.0.2.4.2.4-5.jar storm.starter.WordCountTopology wordcount
		
	> [AZURE.NOTE] La partie `0.10.0.2.4.2.4-5` du nom de fichier peut varier si HDinsight est mis à jour avec des versions plus récentes de Storm.

    Cette opération va démarrer l’exemple de topologie WordCount sur le cluster, avec le nom convivial « wordcount ». Elle va générer des phrases de manière aléatoire et compter les occurrences de chaque mot dans ces phrases.

    > [AZURE.NOTE] Pendant l’envoi de la topologie au cluster, vous devez d’abord copier le fichier jar contenant le cluster avant d’utiliser la commande `storm`. Pour ce faire, vous pouvez utiliser la commande `scp` à partir du client où se trouve le fichier. Par exemple, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > L’exemple WordCount et d’autres exemples de Storm Starter sont déjà inclus dans votre cluster sous `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##Analyse de la topologie

L’interface utilisateur Storm fournit une interface web incluse dans votre cluster HDInsight pour utiliser les topologies en cours d’exécution.

Suivez la procédure ci-après pour surveiller la topologie à l’aide de l’interface utilisateur de Storm.

1. Ouvrez un navigateur web et accédez à https://CLUSTERNAME.azurehdinsight.net/stormui, où __CLUSTERNAME__ est le nom de votre cluster. L’interface utilisateur de Storm s’ouvre.

	> [AZURE.NOTE] Si vous êtes invité à fournir un nom d’utilisateur et un mot de passe, entrez l’administrateur de cluster (admin) et le mot de passe que vous avez utilisé pour la création du cluster.

2. Sous **Résumé de la topologie**, sélectionnez l’entrée **Statistiques** située dans la colonne **Nom**. Vous obtiendrez plus d’informations sur la topologie.

	![Tableau de bord Storm avec les informations sur la topologie Statistiques Storm Starter.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

	Cette page fournit les informations suivantes :

	* **Statistiques de topologie** : informations de base sur les performances de la topologie, organisées dans des fenêtres de temps.

		> [AZURE.NOTE] La sélection d’une fenêtre de temps spécifique modifie la fenêtre de temps pour les informations affichées dans d’autres sections de la page.

	* **Spouts** : informations de base sur les spouts, y compris la dernière erreur retournée par chaque spout.

	* **Bolts** : informations de base sur les bolts.

	* **Configuration de la topologie** : informations détaillées sur la configuration de la topologie.

	Cette page présente également les actions qui peuvent être effectuées sur la topologie :

	* **Activer** : reprend le traitement d’une topologie désactivée.

	* **Désactiver** : suspend une topologie en cours d’exécution.

	* **Rééquilibrer** : ajuste le parallélisme de la topologie. Il convient de rééquilibrer les topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Cela permet à la topologie d’ajuster le parallélisme pour compenser l’augmentation/la réduction du nombre de nœuds du cluster. Pour plus d’informations, consultez la rubrique [Présentation du parallélisme d’une topologie Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	* **Supprimer** : met fin à une topologie Storm après expiration du délai spécifié.

3. À partir de cette page, sélectionnez une entrée dans la section **Spouts** ou **Bolts**. Vous obtiendrez des informations relatives au composant sélectionné.

	![Tableau de bord Storm avec des informations sur les composants sélectionnés.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	Cette page affiche les informations suivantes :

	* **Statistiques du spout/bolt** : informations de base sur les performances de la topologie, organisées dans des fenêtres de temps.

		> [AZURE.NOTE] La sélection d’une fenêtre de temps spécifique modifie la fenêtre de temps pour les informations affichées dans d’autres sections de la page.

	* **Statistiques d’entrée** (bolt uniquement) : informations sur les composants qui produisent des données consommées par le bolt.

	* **Statistiques de sortie** : informations sur les données émises par ce bolt.

	* **Exécuteurs** : informations sur les instances de ce composant.

	* **Erreurs** : erreurs générées par ce composant.

4. Lorsque vous affichez les détails d’un spout ou d’un bolt, sélectionnez une entrée depuis la colonne **Port** située dans la section **Exécuteurs** pour afficher les détails d’une instance spécifique du composant.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	À partir de ces données, vous pouvez voir qu’il y a 1 493 957 occurrences du mot **seven**. C’est le nombre de fois où il a été détecté depuis le démarrage de cette topologie.

##Arrêt de la topologie

Retournez à la page **Résumé de la topologie**, puis sélectionnez le bouton **Supprimer** de la section**Actions de topologie**. Lorsque vous êtes invité à entrer le nombre de secondes à attendre avant l’arrêt de la topologie, entrez le nombre 10. Après le délai d’expiration, la topologie n’apparaîtra plus lorsque vous vous rendrez dans la section **Interface utilisateur Storm** du tableau de bord.

##Suppression du cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a id="next"></a>Étapes suivantes

Dans ce didacticiel sur Storm Apache, vous avez appris à créer un cluster Storm sur HDInsight à l’aide de Storm Starter et à déployer, surveiller et gérer des topologies Storm à l’aide du tableau de bord Storm. À présent, découvrez comment [développer des topologies basées sur Java à l’aide de Maven](hdinsight-storm-develop-java-topology.md).

Si vous êtes déjà familiarisé avec le développement de topologies basées sur Java et que vous souhaitez déployer une topologie existante dans HDInsight, consultez la page [Déploiement et gestion des topologies Apache Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md).

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0817_2016-->