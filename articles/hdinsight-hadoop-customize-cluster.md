<properties 
	pageTitle="Personnaliser les clusters HDInsight à l'aide d'une action de script| Azure" 
	description="Découvrez comment personnaliser des clusters HDInsight à l'aide d'une action de script." 
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
	ms.date="01/15/2015" 
	ms.author="nitinme"/> 

# Personnaliser des clusters HDInsight à l'aide d'une action de script

Vous pouvez personnaliser un cluster Azure HDInsight pour y installer des logiciels supplémentaires ou modifier la configuration des applications qui résident sur celui-ci. HDInsight fournit une option de configuration intitulée **Action de script**. Celle-ci appelle des scripts personnalisés qui définissent la personnalisation à effectuer sur le cluster. Ces scripts peuvent être utilisés pour personnaliser un cluster  *as it is being deployed*.  

Il est également possible de personnaliser les clusters HDInsight de bien d'autres façons, notamment en ajoutant des comptes de stockage supplémentaires, en modifiant les fichiers de configuration hadoop (core-site.xml, hive-site.xml, etc.) ou encore en ajoutant des bibliothèques partagées (comme Hive ou Oozie) dans des emplacements communs du cluster. Cette personnalisation peut être effectuée à l'aide de HDInsight PowerShell, du Kit de développement logiciel (SDK) .NET ou du Portail de gestion Azure. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight à l'aide d'options personnalisées 
][hdinsight-provision-cluster].



> [AZURE.NOTE] L'utilisation d'une action de script pour personnaliser un cluster est uniquement prise en charge sur un cluster HDInsight version 3.1. Pour plus d'informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).



## Dans cet article

- [Comment le script est-il utilisé lors de la création d'un cluster ?](#lifecycle)
- [Comment écrire un script pour personnaliser un cluster ?](#writescript)
- [Comment utiliser l'action de script pour personnaliser un cluster ?](#howto)
- [Exemples de personnalisation de clusters](#example)
- [Prise en charge des logiciels Open Source utilisés sur un cluster HDInsight](#support)


## <a name="lifecycle"></a>Comment le script est-il utilisé lors de la création d'un cluster ?

Une action de script vous permet de personnaliser un cluster HDInsight quand celui-ci est en cours de création. Le processus de création d'un cluster HDInsight comprend les phases suivantes :

![HDInsight cluster customization and stages during cluster provisioning][img-hdi-cluster-states] 

Le script est appelé après la phase  *HDInsightConfiguration* et avant la phase  *ClusterOperational*. Chaque cluster peut accepter plusieurs actions de script qui sont appelées dans l'ordre spécifié.

> [AZURE.NOTE] L'option de personnalisation de clusters HDInsight est disponible dans le cadre des abonnements Azure HDInsight standard, sans frais supplémentaires.

### Comment fonctionne le script ?

Vous pouvez exécuter le script sur le nœud principal, sur les nœuds de travail ou bien sur les deux. Lorsque le script est en cours d'exécution, le cluster entre dans la phase  *ClusterCustomization*. À ce stade, le script est exécuté sous le compte de l'administrateur système, en parallèle sur tous les nœuds spécifiés dans le cluster, et fournit des privilèges d'administrateur complets sur les nœuds. 

> [AZURE.NOTE] Étant donné que vous disposez de privilèges d'administrateur sur les nœuds du cluster au cours de la phase *Cluster customization*, vous pouvez utiliser le script pour effectuer des opérations comme arrêter et démarrer des services, y compris des services liés à Hadoop. Vous devez donc vous assurer, dans le cadre du script, que les services Ambari et autres services liés à Hadoop sont en cours d'exécution avant la fin de l'exécution du script. Ces services sont requis pour établir correctement l'intégrité et l'état du cluster pendant sa création. Si vous modifiez la configuration d'un cluster d'une manière qui affecte ces services, vous devez utiliser les fonctions d'assistance fournies. Pour plus d'informations sur les fonctions d'assistance, consultez la rubrique [Développement d'une action de script avec HDInsight][hdinsight-write-script].

La sortie et les journaux des erreurs du script sont stockés dans le compte de stockage par défaut spécifié pour le cluster. Les journaux sont stockés dans une table avec le nom *u<\cluster-name-fragment><\time-stamp>setuplog*. Il s'agit de journaux d'agrégation provenant du script exécuté sur tous les nœuds (nœud principal et nœuds de travail) dans le cluster.

## <a name="writescript"></a>Comment écrire un script pour personnaliser un cluster ?

Pour plus d'informations sur l'écriture d'un script de personnalisation de cluster, consultez la rubrique [Développement d'une action de script avec HDInsight][hdinsight-write-script]. 

## <a name="howto"></a>Comment utiliser l'action de script pour personnaliser un cluster ?

Vous pouvez utiliser l'action de script à partir du portail de gestion Azure, de cmdlets PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET pour personnaliser un cluster. 

**À partir du portail de gestion**

1. Lancez l'approvisionnement d'un cluster à l'aide de l'option **CRÉATION PERSONNALISÉE**, comme il est décrit dans la rubrique [Approvisionnement d'un cluster à l'aide d'options personnalisées](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal). 
2. Sur la page Actions de script de l'Assistant, cliquez sur **ajouter l'action de script** pour fournir des informations sur l'action de script, comme illustré ci-dessous :

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom</td>
			<td>Indiquez un nom pour l'action de script.</td></tr>
		<tr><td>URI de script</td>
			<td>Indiquez l'URI vers le script qui est appelé pour personnaliser le cluster.</td></tr>
		<tr><td>Type de nœud</td>
			<td>Spécifie les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> ou <b>Nœuds de travail</b> uniquement.
		<tr><td>Paramètres</td>
			<td>Indiquez les paramètres, s'ils sont requis par le script.</td></tr>
	</table>

	Vous pouvez ajouter plusieurs actions de script afin d'installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster. 
  
**Utilisation des cmdlets PowerShell**

Utilisez des commandes HDInsight PowerShell pour exécuter une ou plusieurs actions de script. Vous pouvez utiliser la cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** pour appeler des scripts personnalisés. Pour utiliser ces cmdlets, Azure PowerShell doit être installé et configuré. Pour plus d'informations sur la configuration d'une station de travail pour exécuter des applets de commande HDInsight Powershell, consultez la page [Installation et configuration d'Azure PowerShell][powershell-install-configure].

Pour exécuter une action de script unique lors du déploiement d'un cluster HDInsight, utilisez la commande PowerShell suivante :

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName -Uri http://uri.to/scriptaction.ps1 -Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Pour exécuter plusieurs actions de script lors du déploiement d'un cluster HDInsight, utilisez la commande PowerShell suivante :

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName1 -Uri http://uri.to/scriptaction1.ps1 -Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName2 -Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**Utilisation du Kit de développement logiciel (SDK) HDInsight .NET**

Le Kit de développement logiciel (SDK) HDInsight .NET fournit une classe <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> pour appeler des scripts personnalisés. Pour utiliser le Kit de développement logiciel (SDK) HDInsight .NET :

1. Créez une application Visual Studio, puis installez le Kit de développement logiciel (SDK) à partir de Nuget. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**. Exécutez les commandes suivantes dans la console pour installer le package :

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. Créez un cluster à l'aide du Kit de développement logiciel (SDK). Pour plus d'informations, consultez la rubrique [Approvisionnement d'un cluster HDInsight à l'aide du Kit de développement logiciel (SDK) .NET](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#sdk).

3. Utilisez la classe **ScriptAction** pour appeler un script personnalisé, comme indiqué ci-dessous :

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// PROVIDE THE CLUSTER INFORMATION LIKE
			// NAME, STORAGE ACCOUNT, CREDENTIALS,
			// CLUSTER SIZE, and VERSION		    
			...
			...
		};

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script.
		));


## <a name="example"></a>Exemples de personnalisation de clusters

Pour vous aider à démarrer, HDInsight propose des exemples de scripts pour installer les composants suivants sur un cluster HDInsight.

- **Installation de Spark**. Consultez la rubrique [Installation de Spark sur des clusters HDInsight][hdinsight-install-spark].
- **Installation de R**. Consultez la rubrique [Installation de R sur des clusters HDInsight][hdinsight-install-r].
- **Installation de Solr**. [Installation et utilisation de Solr sur des clusters HDInsight](../hdinsight-hadoop-solr-install)
- **Installation de Giraph**. [Installation et utilisation de Giraph sur des clusters HDInsight](../hdinsight-hadoop-giraph-install)

## <a name="support"></a>Prise en charge des logiciels Open Source utilisés sur un cluster HDInsight
Le service Microsoft Azure HDInsight est une plateforme flexible qui vous permet de créer des applications de données volumineuses (Big Data) dans le cloud à l'aide de l'écosystème des technologies Open Source articulées autour de Hadoop. Microsoft Azure fournit un niveau de support général pour les technologies Open Source, comme indiqué dans la <a href="http://azure.microsoft.com/support/faq/" target="_blank">section " Portée du support " du site web FAQ du support Azure</a>. En outre, le service HDInsight fournit un niveau de support supplémentaire pour certains des composants, comme indiqué ci-dessous.

Deux types de composants Open Source sont disponibles dans le service HDInsight :

- **Composants intégrés**. Ces composants sont préinstallés sur les clusters HDInsight et fournissent la fonctionnalité principale du cluster. Le Gestionnaire de ressources Yarn, le langage de requête Hive et Mahout Library, par exemple, appartiennent à cette catégorie. La liste complète des composants de cluster est disponible <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">ici</a>.
- **Composants personnalisés**. En tant qu'utilisateur du cluster, vous pouvez installer ou utiliser, dans votre charge de travail, tout composant qui est disponible dans la communauté ou que vous avez créé.

Les composants intégrés bénéficient d'un support total, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.

Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Il existe, par exemple, de nombreux sites communautaires. En voici quelques-uns : <a href ="https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight" target="_blank">Forum MSDN sur HDInsight</a>, <a href="http://stackoverflow.com" target="_blank">http://stackoverflow.com</a>. Apache gère également des sites de projets à l'adresse <a href="http://apache.org" target="_blank">http://apache.org</a>, par exemple : <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a>, <a href="http://spark.apache.org/" target="_blank">Spark</a>.

Le service HDInsight offre plusieurs moyens d'utiliser des composants personnalisés. Quel que soit le mode d'utilisation ou d'installation du composant sur le cluster, le même niveau de support s'applique. Vous trouverez, ci-dessous, la liste des méthodes les plus courantes d'utilisation des composants personnalisés sur des clusters HDInsight.

1. Envoi de tâches. Des tâches Hadoop ou d'autres types de tâches peuvent être envoyés au cluster qui exécute ou utilise des composants personnalisés.
2. Personnalisation des clusters. Lors de la création du cluster, vous pouvez spécifier des paramètres supplémentaires et des composants personnalisés qui seront installés sur les nœuds de cluster.
3. Exemples. Pour des composants personnalisés fréquemment utilisés, il arrive que Microsoft et d'autres éditeurs proposent des exemples illustrant leur utilisation sur les clusters HDInsight. Ces exemples sont fournis sans support.


## Voir aussi##
[Approvisionnement de clusters Hadoop dans HDInsight à l'aide d'options personnalisées][hdinsight-provision-cluster] pour obtenir des instructions sur la configuration d'un cluster HDInsight à l'aide d'autres options personnalisées.

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-write-script]: ../hdinsight-hadoop-script-actions/
[hdinsight-provision-cluster]: ../hdinsight-provision-clusters/
[powershell-install-configure]: ../install-configure-powershell/


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster provisioning"
<!--HONumber=42-->
