---
title: Installer et utiliser Giraph sur HDInsight sous Linux (Hadoop) | Microsoft Docs
description: "Découvrez comment installer Giraph sur des clusters HDInsight basés sur Linux à l’aide des actions de script. Les actions de script vous permettent de personnaliser le cluster pendant la création, en modifiant la configuration du cluster ou en installant des services et des utilitaires."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9fcac906-8f06-4002-9fe8-473e42f8fd0f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 98008946357e3c4f15f43f7901ac5ffa2dab0b60
ms.lasthandoff: 04/12/2017


---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installation de Giraph sur les clusters Hadoop HDInsight et utilisation de Giraph pour traiter des graphiques à grande échelle

Vous pouvez installer Giraph sur n’importe quel type de cluster dans Hadoop sur Azure HDInsight à l’aide d’une **action de script** pour personnaliser un cluster.

Dans cette rubrique, vous apprenez à installer Giraph à l’aide d’une action de script. Après avoir installé Giraph, vous apprendrez également comment l'utiliser pour la plupart des applications courantes, à savoir traiter des graphiques à grande échelle.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="whatis"></a>Présentation de Giraph

[Apache Giraph](http://giraph.apache.org/) permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight. Les graphiques modélisent les relations entre les objets, telles que les connexions entre routeurs sur un grand réseau comme Internet ou les relations entre individus sur les réseaux sociaux (parfois appelés graphiques sociaux). Le traitement des graphiques permet d'examiner les relations entre les objets d'un graphique, par exemple :

* identifier les amis potentiels en fonction de vos relations actuelles ;

* identifier le chemin le plus court entre deux ordinateurs d'un réseau ;

* calculer le classement de pages web.

> [!WARNING]
> Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aide à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés, tels que Giraph, bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org). Par exemple : [Hadoop](http://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Ce que fait le script

Le script effectue les opérations suivantes :

* Installe Giraph sur `/usr/hdp/current/giraph`

* Copie le fichier `giraph-examples.jar` dans le stockage par défaut (WASB) de votre cluster : `/example/jars/giraph-examples.jar`

## <a name="install"></a>Installez Giraph à l’aide des actions de script

Un exemple de script d’installation de Giraph sur un cluster HDInsight est disponible à l’emplacement suivant.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Cette section explique comment utiliser l’exemple de script dans le cadre de la création du cluster à l’aide du portail Azure.

> [!NOTE]
> Azure PowerShell, l'interface de ligne de commande Azure (CLI), le Kit de développement logiciel (SDK) .NET HDInsight ou les modèles Azure Resource Manager peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer les actions de script aux clusters qui sont déjà en cours d’exécution. Pour plus d’informations, consultez la page [Personnaliser des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrez la création d’un cluster à l’aide de la procédure décrite dans [Création de clusters HDInsight sous Linux](hdinsight-hadoop-create-linux-clusters-portal.md), mais ne terminez pas la création.

2. Dans le panneau **Configuration facultative**, sélectionnez **Actions de script**, puis indiquez les informations ci-dessous :

   * **NAME**: saisissez un nom convivial pour l’action de script.

   * **URI du SCRIPT** : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: cochez cette option.

   * **WORKER** : laissez cette option désactivée.

   * **ZOOKEEPER** : laissez cette option désactivée.

   * **PARAMETERS**: laissez ce champ vide.

3. En bas de l’écran **Actions de script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** au bas du panneau **Configuration facultative** pour enregistrer les informations de configuration facultatives.

4. Continuez la création du cluster, comme décrit dans [Créer des clusters HDInsight sous Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Utilisation de Giraph dans HDInsight

Une fois le cluster créé, effectuez la procédure ci-après pour exécuter l’exemple SimpleShortestPathsComputation inclus avec Giraph. Elle utilise l’implémentation [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) qui permet de trouver le trajet le plus court entre des objets dans un schéma.

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilisez la commande suivante pour créer un fichier nommé **tiny_graph.txt** :

    ```
    nano tiny_graph.txt
    ```

    Utilisez les données suivantes comme contenu de ce fichier :

    ```
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Ces données décrivent une relation entre les objets d’un graphique dirigé à l'aide du format `[source_id, source_value,[[dest_id], [edge_value],...]]`. Chaque ligne représente une relation entre un objet `source_id` et un ou plusieurs objets `dest_id`. La valeur `edge_value` (ou pondération) peut être considérée comme l’intensité ou la distance de la connexion entre `source_id` et `dest\_id`.

    Dessinées en utilisant la valeur (ou la pondération) comme la distance entre les objets, les données ci-dessus peuvent ressembler à cela :

    ![tiny_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Pour enregistrer le fichier, utilisez **Ctrl+X**, puis **Y**, et enfin **Entrée** pour accepter le nom de fichier.

4. Pour stocker les données dans le stockage principal de votre cluster HDInsight, utilisez les éléments suivants :

    ```
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Exécutez l’exemple SimpleShortestPathsComputation en utilisant la commande suivante :

    ```
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Les paramètres utilisés avec cette commande sont décrits dans le tableau suivant.

   | Paramètre | Résultat |
   | --- | --- |
   | `jar /usr/hdp/current/giraph/giraph-examples.jar` |Fichier jar contenant des exemples. |
   | `org.apache.giraph.GiraphRunner` |Classe utilisée pour démarrer les exemples. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Exemple qui est utilisé. Dans ce cas, il calcule le chemin le plus court entre l’ID 1 et tous les autres identifiants dans le graphique. |
   | `-ca mapred.job.tracker=headnodehost:9010` |Nœud principal du cluster. |
   | `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` |Format d’entrée à utiliser pour les données d’entrée. |
   | `-vip /example/data/tiny_graph.txt` |Fichier de données d’entrée. |
   | `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` |Format de sortie. Dans ce cas, l’ID et la valeur sous forme de texte brut. |
   | `-op /example/output/shortestpaths` |Emplacement de sortie. |
   | `-w 2` |Le nombre de workers à utiliser. Dans ce cas, 2. |

    Pour plus d’informations sur ces éléments et d’autres paramètres utilisés avec des exemples Giraph, consultez la section [Démarrage rapide de Giraph](http://giraph.apache.org/quick_start.html).

6. Une fois la tâche terminée, les résultats sont stockés dans le répertoire **/example/out/shotestpaths**. Les noms de fichiers de sortie commencent par **part-m-** et se terminent par un nombre indiquant s’il s’agit du premier fichier, du deuxième fichier, etc. Utilisez ce qui suit pour afficher le résultat :

    ```
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Le résultat doit ressembler à ce qui suit :

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    L'exemple SimpleShortestPathComputation est codé en dur de façon à commencer avec l'ID d'objet 1 et trouver le chemin le plus court vers les autres objets. Le résultat doit être lu comme `destination_id distance`, où distance est la valeur (ou la pondération) des bords parcourus entre l’ID d’objet 1 et l’ID cible.

    En visualisant cela, vous pouvez vérifier les résultats en parcourant les chemins les plus courts entre l'ID 1 et tous les autres objets. Notez que le chemin le plus court entre ID 1 et ID 4 est 5. Il s’agit de la distance totale entre <span style="color:orange">ID 1 et 3</span>, puis entre <span style="color:red">ID 3 et 4</span>.

    ![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Étapes suivantes

* [Installer et utiliser Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md). Hue est une interface utilisateur web qui permet de facilement créer, exécuter et enregistrer des tâches Pig et Hive, ainsi que de parcourir le stockage par défaut pour votre cluster HDInsight.

* [Installation de R sur des clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md): instructions sur l’utilisation de la personnalisation des clusters pour installer et utiliser R sur des clusters Hadoop HDInsight. R se compose d'un langage et d'un environnement open source destinés au calcul de statistiques. Il intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues.

* [Installation de Solr sur des clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Solr vous permet d'effectuer de puissantes opérations de recherche sur des données stockées.

