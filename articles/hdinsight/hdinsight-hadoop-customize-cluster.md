<properties
	pageTitle="Personnaliser des clusters HDInsight à l'aide d’actions de script | Microsoft Azure"
	description="Découvrez comment personnaliser des clusters HDInsight à l'aide d'une action de script."
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
	ms.date="08/07/2015"
	ms.author="nitinme"/>

# Personnaliser des clusters HDInsight à l'aide d'une action de script

[AZURE.INCLUDE [version-préliminaire-portail-hdinsight-azure](../../includes/hdinsight-azure-preview-portal.md)]

* [Personnaliser des clusters HDInsight à l'aide d'une action de script](hdinsight-hadoop-customize-cluster-v1.md)

HDInsight fournit une option de configuration intitulée **Action de script**, qui appelle des scripts personnalisés qui définissent la personnalisation à effectuer sur le cluster pendant l'approvisionnement. Ces scripts permettent d’installer des logiciels supplémentaires sur un cluster ou de modifier la configuration des applications d’un cluster.


> [AZURE.NOTE]Les informations présentes dans cet article sont spécifiques aux clusters HDInsight sous Windows. Pour obtenir une version de cet article spécifique aux clusters basés sur Linux, consultez [Personnaliser des clusters HDInsight à l’aide de l’action de script (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

Il est également possible de personnaliser les clusters HDInsight de bien d’autres façons, notamment en ajoutant des comptes de stockage supplémentaires, en modifiant les fichiers de configuration hadoop (core-site.xml, hive-site.xml, etc.) ou encore en ajoutant des bibliothèques partagées (comme Hive ou Oozie) dans des emplacements communs du cluster. Ces personnalisations peuvent être effectuées dans Azure PowerShell, le Kit de développement logiciel (SDK) Azure HDInsight .NET ou la version préliminaire du portail Azure. Pour plus d’informations, consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight à l’aide d’options personnalisées][hdinsight-provision-cluster].

## Action de script dans l’approvisionnement de cluster

L’action de script est utilisée uniquement pendant la création d’un cluster. Le diagramme suivant illustre le moment de l’exécution de l’action de script pendant l’approvisionnement :

![Personnalisation du cluster HDInsight et procédure d’approvisionnement d’un cluster][img-hdi-cluster-states]

Quand le script est en cours d’exécution, le cluster entre dans la phase **ClusterCustomization**. À ce stade, le script est exécuté sous le compte de l'administrateur système, en parallèle sur tous les nœuds spécifiés dans le cluster, et fournit des privilèges d'administrateur complets sur les nœuds.

> [AZURE.NOTE]Étant donné que vous disposez de privilèges d’administrateur sur les nœuds du cluster au cours de la phase **ClusterCustomization**, vous pouvez utiliser le script pour effectuer des opérations comme arrêter et démarrer des services, y compris des services liés à Hadoop. Vous devez donc vous assurer, dans le cadre du script, que les services Ambari et autres services liés à Hadoop sont en cours d’exécution avant la fin de l’exécution du script. Ces services sont requis pour établir correctement l'intégrité et l'état du cluster pendant sa création. Si vous modifiez la configuration d'un cluster d'une manière qui affecte ces services, vous devez utiliser les fonctions d'assistance fournies. Pour plus d’informations sur les fonctions d’assistance, consultez [Développer des scripts d’action de script pour HDInsight][hdinsight-write-script].

La sortie et les journaux des erreurs du script sont stockés dans le compte de stockage par défaut spécifié pour le cluster. Les journaux sont stockés dans une table nommée **u<\\cluster-name-fragment><\\time-stamp>setuplog**. Il s’agit de journaux d’agrégation provenant du script exécuté sur tous les nœuds (nœud principal et nœuds de travail) dans le cluster.


Chaque cluster peut accepter plusieurs actions de script qui sont appelées dans l’ordre spécifié. Un script peut être exécuté sur le nœud principal et/ou les nœuds de travail.

## Appeler des scripts d’action de script

Les scripts d'action de script peuvent être utilisés à partir de la version préliminaire du portail Azure, d'Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Cet article explique comment utiliser l'Action de Script à partir du portail. Pour savoir comment utiliser PowerShell et le Kit de développement .NETpour utiliser l'action de script, regardez les exemples répertoriés dans le tableau ci-dessous.

HDInsight propose plusieurs scripts pour installer les composants suivants sur des clusters HDInsight :

Nom | Script
----- | -----
**Installation de Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Consultez [Installer et utiliser Spark sur les clusters HDInsight][hdinsight-install-spark].
**Installation de R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Consultez [Installer et utiliser R sur les clusters HDInsight][hdinsight-install-r].
**Installation de Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Consultez [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install.md).
- **Installation de Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Consultez [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md).



**Dans le portail Azure en version préliminaire**

1. Lancez l’approvisionnement comme indiqué dans la rubrique [Configuration d’un cluster à l’aide d’options personnalisées](hdinsight-provision-clusters.md#portal).
2. Dans Configuration facultative, sur le panneau **Actions de script**, cliquez sur **ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Utilisation d’une action de script pour personnaliser un cluster")

	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom</td>
		<td>Indiquez un nom pour l'action de script.</td></tr>
	<tr><td>URI du script</td>
		<td>Spécifiez l’URI du script appelé pour personnaliser les clusters.</td></tr>
	<tr><td>Head/Worker</td>
		<td>Spécifiez les nœuds (**Head** ou **Worker**) sur lesquels le script de personnalisation est exécuté.</b>.
	<tr><td>Paramètres</td>
		<td>Spécifiez les paramètres, si le script le demande.</td></tr>
</table>Appuyez sur ENTRÉE pour ajouter plusieurs actions de script et installer plusieurs composants sur le cluster.

3. Cliquez sur **Sélectionner** pour enregistrer la configuration d’action de script et poursuivre l’approvisionnement du cluster.


## Prise en charge des logiciels open source utilisés sur les clusters HDInsight
Le service Microsoft Azure HDInsight est une plateforme flexible qui vous permet de créer des applications de données volumineuses (Big Data) dans le cloud à l’aide d’un écosystème de technologies open source articulées autour de Hadoop. Microsoft Azure fournit un niveau de support général pour les technologies open source, comme indiqué dans la section **Portée du support** du site web <a href="http://azure.microsoft.com/support/faq/" target="_blank">FAQ du support Azure</a>. Le service HDInsight fournit un niveau de support supplémentaire pour certains composants, comme indiqué ci-dessous.

Deux types de composant open source sont disponibles dans le service HDInsight :

- **Composants intégrés** : ces composants sont préinstallés sur les clusters HDInsight et fournissent la fonctionnalité principale du cluster. Par exemple, YARN ResourceManager, le langage de requête Hive (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. Une liste complète des composants de cluster est disponible sur la page <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">Nouveautés des versions de cluster Hadoop fournies par HDInsight</a>.
- **Composants personnalisés** : en tant qu’utilisateur du cluster, vous pouvez installer ou utiliser, dans votre charge de travail, tout composant qui est disponible dans la communauté ou que vous avez créé.

Les composants intégrés bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.

> [AZURE.WARNING]Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=hdinsight) ou [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org) ; par exemple, [Hadoop](http://hadoop.apache.org/) ou [Spark](http://spark.apache.org/).

Le service HDInsight fournit plusieurs méthodes d’utilisation de ces composants personnalisés. Quel que soit le mode d’utilisation ou d’installation du composant sur le cluster, le même niveau de support s’applique. Vous trouverez, ci-dessous, la liste des méthodes les plus courantes d’utilisation des composants personnalisés sur des clusters HDInsight.

1. Envoi de travaux : il est possible d’envoyer des travaux Hadoop ou d’autres types de travaux au cluster qui exécute ou utilise des composants personnalisés.
2. Personnalisation de cluster : lors de la création du cluster, vous pouvez spécifier des paramètres supplémentaires et des composants personnalisés qui seront installés sur les nœuds de cluster.
3. Exemples : pour des composants personnalisés fréquemment utilisés, il arrive que Microsoft et d’autres éditeurs proposent des exemples illustrant leur utilisation sur les clusters HDInsight. Ces exemples sont fournis sans support.

## Développer des scripts d’action de script

Consultez [Développer des scripts d’action de script pour HDInsight][hdinsight-write-script].


## Voir aussi

- [Approvisionnement de clusters Hadoop dans HDInsight à l’aide d’options personnalisées][hdinsight-provision-cluster] pour obtenir des instructions sur l’approvisionnement d’un cluster HDInsight à l’aide d’autres options personnalisées.
- [Développer des scripts d’action de script pour HDInsight][hdinsight-write-script]
- [Installer et utiliser Spark sur les clusters HDInsight][hdinsight-install-spark]
- [Installer et utiliser R sur les clusters HDInsight][hdinsight-install-r]
- [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install.md).
- [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Procédure d’approvisionnement d’un cluster"

<!---HONumber=Sept15_HO4-->