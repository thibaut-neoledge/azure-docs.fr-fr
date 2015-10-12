<properties
	pageTitle="Utilisation d’une action de script dans HDInsight pour installer Solr sur un cluster Hadoop| Microsoft Azure"
	description="Découvrez comment personnaliser un cluster HDInsight avec Solr. Vous allez utiliser une option de configuration Action de script afin d’utiliser un script pour installer Solr."
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

# Installation et utilisation de Solr sur des clusters HDInsight Hadoop

Vous pouvez installer Solr sur n’importe quel type de cluster dans Hadoop sur Azure HDInsight à l’aide de la personnalisation de cluster **Action de script**. Une action de script vous permet d'exécuter des scripts pour personnaliser un cluster, uniquement au moment de sa création. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script][hdinsight-cluster-customize].

[AZURE.INCLUDE [version préliminaire du portail hdinsight azure](../../includes/hdinsight-azure-preview-portal.md)]

* [Installation de Solr sur des clusters HDInsight](hdinsight-hadoop-solr-install.md)

Dans cette rubrique, vous allez apprendre à installer Solr à l’aide d’une action de script. Solr est une puissante plateforme de recherche qui fournit des fonctionnalités de recherche au niveau de l'entreprise pour les données gérées par Hadoop. Après avoir installé Solr sur un cluster HDInsight, vous apprendrez également comment rechercher des données à l’aide de Solr.

> [AZURE.NOTE]L’exemple de script utilisé dans cette rubrique crée un cluster Solr Windows avec une configuration spécifique. Si vous souhaitez configurer le cluster Solr avec d'autres collections, partitions, schémas, réplicas, etc., vous devez modifier le script et les fichiers binaires Solr en conséquence.
>
> Pour plus d’informations sur l’utilisation de Solr avec un cluster Linux, consultez [Installation et utilisation de Solr sur des clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).

## <a name="whatis"></a>Présentation de Solr

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> est une plateforme de recherche d’entreprise qui permet d’effectuer de puissantes opérations de recherche en texte intégral sur des données. Alors que Hadoop permet de stocker et de gérer de grandes quantités de données, Apache Solr fournit les fonctionnalités de recherche nécessaires pour les récupérer rapidement. Cette rubrique contient les instructions de personnalisation d'un cluster HDInsight pour installer Solr.

## <a name="install"></a>Installation de Solr

Pour obtenir un exemple de script pour installer Solr sur un cluster HDInsight, téléchargez l’objet blob de stockage Azure en lecture seule à l’adresse [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). Cette section explique comment utiliser l'exemple de script dans le cadre de l'approvisionnement du cluster à l'aide du portail Azure.


> [AZURE.NOTE]L'exemple de script fonctionne uniquement avec un cluster HDInsight version 3.1. Pour plus d’informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](hdinsight-component-versioning.md).


1. Commencez l’approvisionnement d’un cluster à l’aide de l’option **CRÉATION PERSONNALISÉE**, comme cela est décrit dans la rubrique [Approvisionnement d’un cluster à l’aide d’options personnalisées](hdinsight-provision-clusters.md#portal).
2. Sur la page **Actions de script** de l’Assistant, cliquez sur **Ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Utilisation d’une action de script pour personnaliser un cluster")

	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom</td>
		<td>Indiquez un nom pour l'action de script. Par exemple, <b>Installation Solr</b>.</td></tr>
	<tr><td>URI du script</td>
		<td>Spécifiez l'URI (Uniform Resource Identifier) du script appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
	<tr><td>Type de nœud</td>
		<td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> et <b>Nœuds de travail uniquement</b>.
	<tr><td>Paramètres</td>
		<td>Spécifiez les paramètres, si le script le demande. Le script d’installation de&#160;Solr ne nécessite aucun paramètre. Vous pouvez donc laisser ce champ vide.</td></tr>
</table>Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster.


## <a name="usesolr"></a>Utilisation de Solr dans HDInsight

Vous devez commencer par indexer Solr avec quelques fichiers de données. Vous pouvez ensuite utiliser Solr pour exécuter des requêtes de recherche sur les données indexées. Procédez comme suit pour utiliser Solr dans un cluster HDInsight :

1. **Utilisez le protocole RDP (Remote Desktop) pour vous connecter à distance au cluster HDInsight avec Solr installé**. À partir du portail Azure, activez le Bureau à distance pour le cluster que vous avez créé avec Solr installé, puis accédez à distance au cluster. Pour consulter des instructions à ce sujet, consultez la page <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Connexion à des clusters HDInsight à l’aide de RDP</a>.

2. **Indexez Solr en téléchargeant des fichiers de données**. Lorsque vous indexez Solr, vous y placez des documents sur lesquels vous devrez peut-être effectuer des recherches. Pour indexer Solr, utilisez le protocole RDP pour vous connecter à distance au cluster, accédez au Bureau, ouvrez la ligne de commande Hadoop et accédez à **C:\\apps\\dist\\solr-4.7.2\\example\\exampledocs**. Exécutez la commande suivante :

		java -jar post.jar solr.xml monitor.xml

	Le résultat suivant s’affiche sur la console :

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	L’utilitaire post.jar indexe Solr avec deux exemples de documents : **solr.xml** et **monitor.xml**. L'utilitaire post.jar et les exemples de documents sont disponibles avec l'installation Solr.

3. **Utilisez le tableau de bord Solr pour effectuer une recherche dans les documents indexés**. Dans la session RDP au cluster HDInsight, ouvrez Internet Explorer, puis lancez le tableau de bord Solr à l’adresse ****http://headnodehost:8983/solr/#/**. Dans la liste déroulante **Sélecteur de base** située dans le volet gauche, sélectionnez **collection1** et cliquez ensuite sur **Requête**. Par exemple, pour sélectionner et renvoyer tous les documents dans Solr, renseignez les valeurs suivantes :

	* Dans la zone de texte **q**, entrez ***:***. Tous les documents indexés dans Solr sont alors renvoyés. Si vous souhaitez rechercher une chaîne spécifique dans les documents, vous pouvez la saisir à cet emplacement.
	
	* Sélectionnez le format de sortie dans la zone de texte **wt**. La valeur par défaut est **json**. Cliquez sur **Exécuter la requête**.

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Exécution d’une requête sur le tableau de bord Solr")
	
	La sortie renvoie les deux documents que nous avions utilisés pour indexer Solr. La sortie se présente comme suit :

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }


4. **Recommandé : sauvegardez les données indexées à partir de Solr dans un objet WASB (Azure Storage Blob) associé au cluster HDInsight**. Il est conseillé de sauvegarder les données indexées à partir de nœuds de cluster Solr sur un stockage d’objets blob Azure. Pour ce faire, procédez comme suit :

	1. À partir de la session RDP, ouvrez Internet Explorer et accédez à l'URL suivante :

			http://localhost:8983/solr/replication?command=backup

		La réponse doit ressembler à ceci :

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. Dans la session à distance, accédez à {SOLR\_HOME}{Collection}\\data. Pour le cluster créé à l’aide de l’exemple de script, il doit s’agir de **C:\\apps\\dist\\solr-4.7.2\\example\\solr\\collection1\\data**. Un dossier d’instantanés créé avec un nom semblable à **snapshot.*timestamp*** doit normalement figurer à cet emplacement.

	3. Compressez le dossier d’instantanés et téléchargez-le vers le stockage d’objets blob Azure. À partir de la ligne de commande Hadoop, accédez à l’emplacement du dossier d’instantanés à l’aide de la commande suivante :

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Cette commande copie l’instantané dans /example/data/ sous le conteneur, dans le compte de stockage par défaut associé au cluster.


## Voir aussi

- [Installation et utilisation de Spark sur des clusters HDInsight][hdinsight-install-spark]. Utilisez la personnalisation de clusters pour installer Spark sur des clusters HDInsight Hadoop. Spark est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d'analyse de « big data ».
- [Installation de R sur des clusters HDInsight][hdinsight-install-r]. Utilisez la personnalisation de clusters pour installer R sur des clusters HDInsight Hadoop. R se compose d'un langage et d'un environnement open source destinés au calcul de statistiques. Il intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues.
- [Installation de Giraph sur des clusters HDInsight](hdinsight-hadoop-giraph-install.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques à l’aide de Hadoop et peut être utilisé avec Azure HDInsight.


[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=Oct15_HO1-->