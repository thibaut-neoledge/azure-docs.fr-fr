<properties 
	pageTitle="Utiliser le gestionnaire de ressources pour allouer des ressources au cluster Apache Spark dans HDInsight | Microsoft Azure" 
	description="Découvrez comment utiliser le gestionnaire de ressources pour les clusters Spark sur HDInsight pour obtenir de meilleures performances." 
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
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Gérer des ressources pour le cluster Apache Spark dans Azure HDInsight (Windows)

> [AZURE.NOTE] HDInsight fournit maintenant des clusters Spark sur Linux. Pour plus d’informations sur la gestion des ressources pour un cluster Spark sur HDInsight Linux, consultez [Gérer les ressources pour le cluster Apache Spark dans Azure HDInsight (Linux)](hdinsight-apache-spark-resource-manager.md).

Le gestionnaire de ressources est un composant du tableau de bord du cluster Spark qui vous permet de gérer les ressources, comme les cœurs et la mémoire RAM, utilisées par chaque application s’exécutant sur le cluster.

## <a name="launchrm"></a>Comment lancer Resource Manager ?

1. Dans le tableau d’accueil du [portail Azure en version préliminaire](https://ms.portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous avez épinglé ce dernier au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**. 
 
2. Dans le panneau du cluster Spark, cliquez sur **Tableau de bord**. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster Spark.

	![Lancer le gestionnaire de ressources](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.cluster.launch.dashboard.png "Démarrer le gestionnaire de ressources")

##<a name="scenariosrm"></a>Comment résoudre ces problèmes avec Resource Manager ?

Voici quelques scénarios courants que vous pouvez rencontrer avec le cluster Spark, ainsi que les instructions relatives à leur résolution à l’aide du gestionnaire de ressources.

### Mon cluster Spark sur HDInsight est lent

Le cluster Apache Spark dans HDInsight est conçu pour une architecture mutualisée. Les ressources sont donc réparties entre plusieurs composants (blocs-notes, serveur de travaux, etc.). Cela vous permet d’utiliser simultanément tous les composants Spark sans vous soucier d’un composant qui n’est pas en mesure d’obtenir des ressources pour l’exécution, mais chacun d’entre eux est plus lent, puisque les ressources sont fragmentées. Cela peut être ajusté en fonction de vos besoins.


### J’utilise uniquement le bloc-notes Jupyter avec le cluster Spark. Comment puis-je lui allouer toutes les ressources ?

1. Dans le **Tableau de bord Spark**, cliquez sur l’onglet **Interface utilisateur Spark** pour trouver le nombre maximal de cœurs et la mémoire RAM maximale que vous pouvez allouer aux applications.

	![Allocation des ressources](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.resource.png "Rechercher des ressources allouées à un cluster Spark")

	D’après la capture d’écran ci-dessus, vous pouvez allouer au plus 7 cœurs (total de 8 cœurs dont 1 est utilisé) et une mémoire RAM de 9 Go (total de 12 Go, dont 2 doivent être réservés pour l’utilisation du système, et 1 qui est utilisé par d’autres applications).

	Vous devez également tenir compte des applications qui s’exécutent. Vous pouvez examiner les applications en cours d’exécution dans l’onglet **Interface utilisateur Spark**.

	![Applications en cours d’exécution](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.running.apps.png "Applications exécutées sur le cluster")

	
2. Dans le tableau de bord HDInsight Spark, cliquez sur l’onglet **Resource Manager**, puis spécifiez les valeurs des paramètres **Nombre de cœurs par défaut de l’application** et **Mémoire d’exécution par défaut par nœud de travail**. Définissez les autres propriétés sur 0.

	![Allocation des ressources](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.allocate.resources.png "Allouer des ressources à vos applications")

### Je n’utilise pas d’outils décisionnels avec le cluster Spark. Comment puis-je reprendre des ressources ? 

Indiquez 0 pour le nombre de cœurs et la mémoire d’exécution du serveur Thrift. Sans cœur ni mémoire alloués, le serveur Thrift passe à l’état **ATTENTE**.

![Allocation des ressources](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.no.thrift.png "Aucune ressource allouée au serveur Thrift")

##<a name="seealso"></a>Voir aussi

* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Créer un cluster Spark sur HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Utiliser Spark sur HDInsight pour créer des applications d’apprentissage automatique](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Utiliser Spark sur HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->