<properties
	pageTitle="Disponibilité des clusters Hadoop dans HDInsight | Microsoft Azure"
	description="HDInsight déploie des clusters hautement disponibles et fiables avec un nœud principal supplémentaire."
	services="hdinsight"
	tags="azure-portal"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="04/27/2016"
	ms.author="jgao"/>


#Disponibilité et fiabilité des clusters Hadoop Windows dans HDInsight


>[AZURE.NOTE] Les étapes de cette procédure sont spécifiques aux clusters HDInsight basés sur Windows. Si vous utilisez un cluster Linux, consultez [Disponibilité et fiabilité des clusters Hadoop Linux dans HDInsight](hdinsight-high-availability-linux.md) pour plus d’informations spécifiques à Linux.

HDInsight permet aux clients de déployer de nombreux types de cluster pour différentes charges de travail d’analyse des données. Les types de cluster proposés actuellement sont les clusters Hadoop pour les charges de travail de requête et d’analyse, les clusters HBase pour les charges de travail NoSQL, et les clusters Storm pour les charges de travail de traitement des événements en temps réel. Un type de cluster donné comprend différent rôles correspondant aux différents nœuds. Par exemple :



- Les clusters Hadoop pour HDInsight sont déployés avec deux rôles :
	- Nœud principal (2 nœuds)
	- Nœud de données (au moins 1 nœud)

- Les clusters HBase pour HDInsight sont déployés avec trois rôles :
	- Serveurs principaux (2 nœuds)
	- Serveurs Region (au moins 1 nœud)
	- Nœuds Master/Zookeeper (3 nœuds)

- Les clusters Storm pour HDInsight sont déployés avec trois rôles :
	- Nœuds Nimbus (2 nœuds)
	- Serveurs Supervisor (au moins 1 nœud)
	- Nœuds Zookeeper (3 nœuds)

Les implémentations standard des clusters Hadoop ont normalement un seul nœud principal. HDInsight supprime ce point de défaillance unique avec l’ajout d’un nœud principal/serveur principal/nœud Nimbus secondaire pour augmenter la disponibilité et la fiabilité du service nécessaire à la gestion des charges de travail. Ces nœuds principaux/serveurs principaux/nœuds Nimbus sont conçus pour gérer en douceur la défaillance des nœuds de travail, mais toute interruption des services principaux exécutés sur le nœud principal provoquera l’arrêt du fonctionnement du cluster.


Les nœuds [ZooKeeper](http://zookeeper.apache.org/) (ZK) ont été ajoutés et sont utilisés pour le choix de l’instance responsable des nœuds principaux et pour s’assurer que les nœuds de travail et les passerelles (GW) savent à quel moment doit se faire le basculement vers le nœud principal secondaire (Head Node1) lorsque le nœud principal actif (Node0) devient inactif.

![Diagramme des nœuds principaux hautement fiables dans l’implémentation HDInsight Hadoop.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## Vérifier l’état des services du nœud principal actif
Pour déterminer quel est le nœud principal actif et vérifier le statut des services en cours d’exécution sur ce nœud principal, vous devez vous connecter au cluster Hadoop avec le protocole RDP (Remote Desktop Protocol). Pour obtenir des instructions sur le protocole RDP, consultez la page [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp). Une fois que vous avez accédé à distance au cluster, double-cliquez sur l’icône **Hadoop Service Available ** située sur le Bureau pour obtenir l’état concernant le nœud principal sur lequel les services Namenode, Jobtracker, Templeton, Oozieservice, Metastore et Hiveserver2 s’exécutent ou, pour HDI 3.0, les services Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore et Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

Dans la capture d’écran, le nœud principal actif est *headnode0*.

## Accéder aux fichiers journaux sur le nœud principal secondaire

Pour accéder aux fichiers journaux sur le nœud principal secondaire dans le cas où il est devenu le nœud principal actif, la navigation dans l’interface utilisateur du service JobTracker de suivi des tâches fonctionne de la même manière que pour le nœud primaire (actif). Pour accéder au dispositif de suivi des tâches (Job Tracker), vous devez vous connecter au cluster Hadoop avec le protocole RDP (Remote Desktop Protocol), comme décrit dans la section précédente. Une fois que vous avez accédé à distance au cluster, double-cliquez sur l'icône **Nœud de nom Hadoop** située sur le bureau, puis cliquez sur **Journal du nœud de nom** pour accéder au répertoire des journaux sur le nœud principal secondaire.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## Configuration de la taille du nœud principal
Par défaut, les nœuds principaux sont alloués en tant que machines virtuelles de taille importante. Cette taille est appropriée pour la gestion de la plupart des tâches Hadoop exécutées sur le cluster. Mais dans certains scénarios, des machines virtuelles de taille très importante peuvent être nécessaires pour les nœuds principaux. C'est le cas, par exemple, lorsque le cluster doit gérer un nombre important de petites tâches Oozie.

Les machines virtuelles de taille très importante peuvent être configurées soit avec des cmdlets Azure PowerShell soit avec le Kit de développement logiciel (SDK) HDInsight.

La création et l’approvisionnement d’un cluster avec Azure PowerShell sont documentés sur la page [Administration de HDInsight à l’aide de PowerShell](hdinsight-administer-use-powershell.md). La configuration d’un nœud principal de très grande taille nécessite l’ajout du paramètre `-HeadNodeVMSize ExtraLarge` à la cmdlet `New-AzureRmHDInsightcluster` utilisée dans ce code.

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
				-ResourceGroupName $resourceGroupName `
				-ClusterName $clusterName ` 
				-Location $location `
				-HeadNodeVMSize ExtraLarge `
				-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				-DefaultStorageAccountKey $storageAccountKey `
				-DefaultStorageContainerName $containerName  `
				-ClusterSizeInNodes $clusterNodes

Pour le Kit de développement logiciel (SDK), le scénario est similaire. La création et l’approvisionnement d’un cluster avec le Kit de développement logiciel (SDK) sont documentés sur la page [Utilisation du Kit de développement logiciel (SDK) HDInsight .NET](hdinsight-provision-clusters.md#sdk). La configuration d’un nœud principal de très grande taille nécessite l’ajout du paramètre `HeadNodeSize = NodeVMSize.ExtraLarge` à la méthode `ClusterCreateParameters()` utilisée dans ce code.

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
		Name = clustername,
		Location = location,
		HeadNodeSize = NodeVMSize.ExtraLarge,
		DefaultStorageAccountName = storageaccountname,
		DefaultStorageAccountKey = storageaccountkey,
		DefaultStorageContainer = containername,
		UserName = username,
		Password = password,
		ClusterSizeInNodes = clustersize
    };


## Étapes suivantes

- [Apache ZooKeeper](http://zookeeper.apache.org/)
- [Connexion à des clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Utilisation du Kit de développement logiciel (SDK) HDInsight .NET](hdinsight-provision-clusters.md#sdk)

<!---HONumber=AcomDC_0504_2016-->