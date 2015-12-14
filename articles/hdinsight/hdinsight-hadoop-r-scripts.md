<properties
	pageTitle="Utilisation de R dans HDInsight pour personnaliser des clusters| Microsoft Azure"
	description="Découvrez comment installer R à l’aide d’une action de script et utiliser R sur des clusters HDInsight."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="jgao"/>

# Installation et utilisation de R sur des clusters HDInsight Hadoop


Découvrez comment personnaliser un cluster HDInsight basé sur Windows avec R à l'aide d'une action de script, et comment utiliser R sur des clusters HDInsight. Pour plus d’informations sur l’utilisation de R avec un cluster Linux, consultez [Installation et utilisation de R sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md).
 
Vous pouvez installer R sur n’importe quel type de cluster (Hadoop, Storm, HBase, Spark) sur Azure HDInsight à l’aide d’une *action de script*. Pour obtenir un exemple de script pour installer R sur un cluster HDInsight, téléchargez l'objet blob de stockage Azure en lecture seule à l'adresse [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

**Articles connexes**

- [Installation et utilisation de R sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Création de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md) : informations générales sur la création de clusters HDInsight
- [Personnalisation d’un cluster HDInsight à l’aide d’une action de script][hdinsight-cluster-customize] : informations générales sur la personnalisation de clusters HDInsight à l'aide d’actions de script
- [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions.md)

## Qu'est-ce que R ?

Le <a href="http://www.r-project.org/" target="_blank">projet R pour le calcul de statistiques</a> se compose d’un langage et d’un environnement open source destinés au calcul de statistiques. R intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues. R est l'environnement de programmation préféré des statisticiens professionnels et des scientifiques dans de nombreux domaines.

R est compatible avec le stockage d'objets Blob Azure (WASB), ce qui permet aux données stockées à cet emplacement d'être traitées avec R sur HDInsight.

## Installation de R

Un [exemple de script](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) d’installation de R sur un cluster HDInsight est disponible à partir d’un objet blob en lecture seule dans Azure Storage. Cette section explique comment utiliser l'exemple de script dans le cadre de la création du cluster à l'aide du portail Azure.

> [AZURE.NOTE]L'exemple de script a été introduit dans la version 3.1 du cluster HDInsight. Pour plus d’informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](../hdinsight-component-versioning/).

1. Quand vous créez un cluster HDInsight à partir du portail, cliquez sur **Configuration facultative**, puis cliquez sur **Actions de Script**.
2. Dans la page **Actions de Script**, entrez les valeurs suivantes :

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Utilisation d’une action de script pour personnaliser un cluster")

	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom</td>
		<td>Indiquez un nom pour l’action de script, par exemple, <b>Installer&#160;R</b>.</td></tr>
	<tr><td>URI du script</td>
		<td>Spécifiez l’URI du script appelé pour personnaliser le cluster, par exemple, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i>.</td></tr>
	<tr><td>Type de nœud</td>
		<td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> et <b>Nœuds de travail uniquement</b>.
	<tr><td>Paramètres</td>
		<td>Spécifiez les paramètres, si le script le demande. Cependant, le script d’installation de&#160;R ne nécessite aucun paramètre, vous pouvez donc laisser ce champ vide.</td></tr>
</table>Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Une fois que vous avez ajouté les scripts, cliquez sur la coche pour démarrer la création du cluster.

Vous pouvez également utiliser le script pour installer R sur HDInsight à l’aide d’Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Vous trouverez des instructions relatives à ces procédures dans la suite de cet article.

## Exécuter des scripts R
Cette section décrit comment exécuter un script R sur le cluster Hadoop avec HDInsight.

1. **Établissez une connexion Bureau à distance au cluster :** dans le portail, activez le Bureau à distance pour le cluster que vous avez créé et sur lequel vous avez installé R, puis accédez à distance au cluster. Pour la marche à suivre, consultez [Connexion à des clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

2. **Ouvrez la console R** : l’installation de R crée un lien vers la console R sur le Bureau du nœud principal. Cliquez sur celui-ci pour ouvrir la console R.

3. **Exécutez le script R** : pour exécuter directement le script R à partir de la console R, collez-le dans la console, sélectionnez-le, puis appuyez sur Entrée. L'exemple simple de script suivant génère les nombres de 1 à 100, puis les multiplie par 2.

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

Les deux premières lignes appellent les bibliothèques RHadoop installées avec R. La dernière ligne imprime les résultats dans la console. La sortie doit ressembler à ceci :

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200


## Installation de R en cas d'utilisation d'Azure PowerShell

Consultez [Personnalisation de clusters HDInsight à l'aide d'une action de script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell). L'exemple montre comment installer Spark avec Azure PowerShell. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## Installation de R à l'aide du Kit de développement logiciel (SDK) .NET

Consultez [Personnalisation de clusters HDInsight à l'aide d'une action de script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). L'exemple montre comment installer Spark à l'aide du Kit de développement logiciel (SDK) .NET. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).


## Voir aussi

- [Installation et utilisation de R sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Création de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md) : informations générales sur la création de clusters HDInsight
- [Personnalisation d’un cluster HDInsight à l’aide d’une action de script][hdinsight-cluster-customize] : informations générales sur la personnalisation de clusters HDInsight à l'aide d’actions de script
- [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions.md)
- [Installation et utilisation de Spark sur les clusters HDInsight][hdinsight-install-spark] : exemple d’action de script sur l'installation de Spark
- [Installation de Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md) : exemple d’action de script sur l'installation de Giraph
- [Installation de Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install-linux.md) : exemple d’action de script sur l'installation de Solr.

[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md

<!---HONumber=AcomDC_1203_2015-->