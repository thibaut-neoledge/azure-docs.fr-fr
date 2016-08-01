<properties
	pageTitle="Installer et utiliser Giraph sur HDInsight sous Linux (Hadoop) | Microsoft Azure"
	description="Découvrez comment installer Giraph sur des clusters HDInsight basés sur Linux à l’aide des actions de script. Les actions de script vous permettent de personnaliser le cluster pendant la création, en modifiant la configuration du cluster ou en installant des services et des utilitaires."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="larryfr"/>

# Installation de Giraph sur les clusters Hadoop HDInsight et utilisation de Giraph pour traiter des graphiques à grande échelle

Vous pouvez installer Giraph sur n’importe quel type de cluster dans Hadoop sur Azure HDInsight à l’aide d’une **action de script** pour personnaliser un cluster.

Dans cette rubrique, vous allez apprendre à installer Giraph à l'aide d'une action de script. Après avoir installé Giraph, vous apprendrez également comment l'utiliser pour la plupart des applications courantes, à savoir traiter des graphiques à grande échelle.

> [AZURE.NOTE] Les informations présentes dans cet article sont spécifiques aux clusters HDInsight sous Linux. Pour plus d’informations sur l’utilisation des clusters basés sur Windows, voir [Installation de Giraph sur les clusters HDInsight Hadoop (Windows)](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>Présentation de Giraph

[Apache Giraph](http://giraph.apache.org/) permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight. Les graphiques modélisent les relations entre les objets, telles que les connexions entre routeurs sur un grand réseau comme Internet ou les relations entre individus sur les réseaux sociaux (parfois appelés graphiques sociaux). Le traitement des graphiques permet d'examiner les relations entre les objets d'un graphique, par exemple :

- identifier les amis potentiels en fonction de vos relations actuelles ;
- identifier le chemin le plus court entre deux ordinateurs d'un réseau ;
- calculer le classement de pages web.

> [AZURE.WARNING] Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés, tels que Giraph, bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=hdinsight) ou [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org). Par exemple : [Hadoop](http://hadoop.apache.org/).

##Ce que fait le script

Le script effectue les opérations suivantes :

* Installe Giraph sur `/usr/hdp/current/giraph`
* Copie le fichier `giraph-examples.jar` dans le stockage par défaut (WASB) de votre cluster : `/example/jars/giraph-examples.jar`

## <a name="install"></a>Installez Giraph à l’aide des actions de script

Un exemple de script d’installation de Giraph sur un cluster HDInsight est disponible à l’emplacement suivant.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Cette section explique comment utiliser l’exemple de script dans le cadre de la création du cluster à l’aide du portail Azure.

> [AZURE.NOTE] Azure PowerShell, l'interface de ligne de commande Azure (CLI), le Kit de développement logiciel (SDK) .NET HDInsight ou les modèles Azure Resource Manager peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer les actions de script aux clusters qui sont déjà en cours d’exécution. Pour plus d’informations, consultez la page [Personnaliser des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrez la création d’un cluster à l’aide de la procédure décrite dans [Création de clusters HDInsight sous Linux](hdinsight-provision-linux-clusters.md#portal), mais ne terminez pas la création.

2. Dans le panneau **Configuration facultative**, sélectionnez **Actions de script**, puis indiquez les informations ci-dessous :

	* __NAME__ : saisissez un nom convivial pour l’action de script.
	* __SCRIPT URI__ : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
	* __HEAD__ : cochez cette option
	* __WORKER__ : cochez cette option.
	* __ZOOKEEPER__ : cochez cette option pour installer le nœud ZooKeeper.
	* __PARAMETERS__ : laissez ce champ vide.

3. En bas de l’écran **Actions de script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** au bas du panneau **Configuration facultative** pour enregistrer les informations de configuration facultatives.

4. Continuez la création du cluster, comme décrit dans [Créer des clusters HDInsight sous Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Utilisation de Giraph dans HDInsight

Une fois le cluster créé, effectuez la procédure ci-après pour exécuter l’exemple SimpleShortestPathsComputation inclus avec Giraph. Cela met en oeuvre l’implémentation <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> qui permet de trouver le trajet le plus court entre des objets dans un schéma.

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Utilisez la commande suivante pour créer un fichier nommé __tiny\_graph.txt__ :

		nano tiny_graph.txt

	Utilisez les données suivantes comme contenu de ce fichier :

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Ces données décrivent une relation entre les objets d’un graphique dirigé, en utilisant le format [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Chaque ligne représente une relation entre un objet **source\_id** et un ou plusieurs objets **dest\_id**. La valeur **edge\\_value** (ou pondération) peut être considérée comme l’intensité ou la distance de la connexion entre **source\_id** et **dest\_id**.

	Dessinées en utilisant la valeur (ou la pondération) comme la distance entre les objets, les données ci-dessus peuvent ressembler à cela :

	![tiny\_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. Pour enregistrer le fichier, utilisez __Ctrl + X__, puis __Y__, et enfin __Entrée__ pour accepter le nom de fichier.

3. Pour stocker les données dans le stockage principal de votre cluster HDInsight, utilisez les éléments suivants :

		hadoop fs -copyFromLocal tiny_graph.txt /example/data/tiny_graph.txt

3. Pour obtenir le nom de domaine complet (FQDN) du nœud principal du cluster, utilisez les éléments suivants :

        hostname -f
        
4. Exécutez l’exemple SimpleShortestPathsComputation en utilisant la commande suivante : Remplacez __HEADNODE__ par le nom de domaine complet renvoyé à l’étape précédente :

		 hadoop jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=HEADNODE:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

	Les paramètres utilisés avec cette commande sont décrits dans le tableau suivant.

	| Paramètre | Résultat |
	| --------- | ------------ |
	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | Fichier jar contenant des exemples. |
	| `org.apache.giraph.GiraphRunner` | Classe utilisée pour démarrer les exemples. |
	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | L’exemple sera exécuté. Dans ce cas, il calcule le chemin le plus court entre l’ID 1 et tous les autres identifiants dans le graphique. |
	| `-ca mapred.job.tracker=HEADNODE:9010` | Nœud principal du cluster. |
	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | Format d’entrée à utiliser pour les données d’entrée. |
	| `-vip /example/data/tiny_graph.txt` | Fichier de données d’entrée. |
	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | Format de sortie. Dans ce cas, l’ID et la valeur sous forme de texte brut. |
	| `-op /example/output/shortestpaths` | Emplacement de sortie. |
	| `-w 2` | Le nombre de workers à utiliser. Dans ce cas, 2. |

	Pour plus d’informations sur ces éléments et d’autres paramètres utilisés avec des exemples Giraph, consultez la section [Démarrage rapide de Giraph](http://giraph.apache.org/quick_start.html).

5. Une fois la tâche terminée, les résultats sont stockés dans le répertoire __wasb:///example/out/shotestpaths__. Les fichiers créés commencent par __part-m-\_\_ et se terminent par un nombre indiquant s’il s’agit du premier, du deuxième fichier, etc. Utilisez ce qui suit pour afficher le résultat :

		hadoop fs -text /example/output/shortestpaths/*

	Le résultat doit ressembler à ce qui suit :

		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	L'exemple SimpleShortestPathComputation est codé en dur de façon à commencer avec l'ID d'objet 1 et trouver le chemin le plus court vers les autres objets. Le résultat doit être lu comme `destination_id distance`, où distance est la valeur (ou la pondération) des bords parcourus entre l’ID d’objet 1 et l’ID cible.

	En visualisant cela, vous pouvez vérifier les résultats en parcourant les chemins les plus courts entre l'ID 1 et tous les autres objets. Notez que le chemin le plus court entre ID 1 et ID 4 est 5. Il s’agit de la distance totale entre <span style="color:orange">ID 1 et 3</span>, puis entre <span style="color:red">ID 3 et 4</span>.

	![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## Étapes suivantes

- [Installer et utiliser Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md). Hue est une interface utilisateur web qui permet de facilement créer, exécuter et enregistrer des tâches Pig et Hive, ainsi que de parcourir le stockage par défaut pour votre cluster HDInsight.

- [Installation de R sur des clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md) : instructions sur l’utilisation de la personnalisation des clusters pour installer et utiliser R sur des clusters Hadoop HDInsight. R se compose d'un langage et d'un environnement open source destinés au calcul de statistiques. Il intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues.

- [Installation de Solr sur des clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Solr vous permet d'effectuer de puissantes opérations de recherche sur des données stockées.

<!---HONumber=AcomDC_0720_2016-->