---
title: Utiliser des composants Python dans une topologie Storm sur HDinsight | Microsoft Docs
description: "Découvrez comment utiliser des composants Python avec Apache Storm sur Azure HDInsight. Vous y apprendrez à utiliser les composants Python à partir d’une topologie Storm basée sur Java et Clojure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: f6bc08230384b637f6b7b83fe32915ab09647d37
ms.lasthandoff: 01/18/2017


---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Développer des topologies Storm Apache à l’aide de Python sur HDInsight

Apache Storm prend en charge plusieurs langages et vous permet de combiner des composants de plusieurs langages dans une même topologie. Dans cet article, vous apprendrez à utiliser les composants de Python dans vos topologies Storm basées sur Java et Clojure à partir de HDInsight.

> [!IMPORTANT]
> Ce document explique l’utilisation des clusters HDInsight sous Windows et Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Composants requis

* Python 2.7 ou ultérieure
* Java JDK 1.7 ou ultérieure
* [Leiningen](http://leiningen.org/)

## <a name="storm-multi-language-support"></a>Prise en charge multi-langage de Storm

Storm a été conçu pour prendre en charge des composants codés à l’aide de n’importe quel langage de programmation. Cette prise en charge suppose toutefois que les composants apprennent à fonctionner avec la [définition de Thrift pour Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Pour Python, un module est fourni dans le cadre du projet Apache Storm pour vous permettre de communiquer facilement avec Storm. Ce module est disponible à l’adresse [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Apache Storm est un processus Java qui s’exécute sur la machine virtuelle Java (JVM). Autrement dit, les composants codés dans d’autres langues sont exécutés sous la forme de sous-processus. Les bits Storm exécutés dans la JVM communiquent avec ces sous-processus à l’aide de messages JSON envoyées via stdin/stdout. Vous trouverez plus de détails sur la communication entre les composants dans la documentation relative au [Protocole multi-langage](https://storm.apache.org/documentation/Multilang-protocol.html).

### <a name="the-storm-module"></a>Le module Storm
Le module storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py), fournit les éléments nécessaires pour créer des composants Python qui fonctionnent avec Storm.

Il génère par exemple des bits `storm.emit` pour émettre des uplets ou des bits `storm.logInfo` pour écrire des données dans des fichiers journaux. Je vous encourage à lire ce fichier pour comprendre ce qu’il apporte.

## <a name="challenges"></a>Défis
Le module **storm.py** vous permet de créer des « spouts » Python qui consomment des données, ainsi que des « bolts » qui traitent les données. Mais la définition globale de la topologie Storm qui établit la communication entre les composants est toujours écrite à l’aide de Java ou Clojure. En outre, si vous utilisez Java, vous devez également créer des composants Java qui font office d’interface avec les composants de Python.

Puisque les clusters Storm sont exécutés en mode distribué, vous devez vous assurer que tous les modules requis par vos composants Python sont disponibles sur tous les nœuds de travail du cluster. Storm ne vous permet pas de le faire facilement avec des ressources codées à l’aide de plusieurs langages. Vous devez donc soit inclure toutes les dépendances dans le fichier jar de la topologie, soit installer manuellement les dépendances sur chaque nœud de travail dans le cluster.

### <a name="java-vs-clojure-topology-definition"></a>Définition des topologies Java et Clojure
Entre les deux méthodes de définition d’une topologie, Clojure constitue de loin l’approche la plus simple et la plus propre, puisque vous pouvez directement référencer les composants Python dans la définition de la topologie. Pour les définitions de topologie basées sur Java, vous devez également définir les composants Java qui traitent certaines opérations, telles que la déclaration des champs dans les tuples renvoyés à partir des composants Python.

Ce document décrit les deux méthodes et fournit quelques exemples de projets.

## <a name="python-components-with-a-java-topology"></a>Composants de Python avec une topologie Java
> [!NOTE]
> Cet exemple est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) , dans le répertoire **JavaTopology** . Il s’agit d’un projet Maven. Si vous n’êtes pas familiarisé avec Maven, consultez la page [Développement de topologies Java pour une application de base de comptage du nombre de mots avec Apache Storm et Maven sur HDInsight](hdinsight-storm-develop-java-topology.md) pour plus d’informations sur la création d’un projet Maven dans le cadre d’une topologie Storm.
> 
> 

Une topologie basée sur Java qui utilise Python (ou d’autres composants de langage JVM) semble utiliser initialement des composants Java. Mais en observant chacun des spouts/bolts Java, vous découvrirez un code semblable au suivant :

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Ce code marque le point où Java appelle Python et exécute le script qui contient la logique bolt réelle. Les spouts/bolts Java (dans cet exemple) déclarent simplement les champs dans le tuple qui sera émis par le composant sous-jacent de Python.

Dans cet exemple, les fichiers Python réels sont stockés dans le répertoire `/multilang/resources` . Le répertoire `/multilang` est référencé dans le fichier **pom.xml**:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir}/multilang</directory>
    </resource>
</resources>

Il regroupe tous les fichiers du dossier `/multilang` dans le fichier jar qui sera créé à partir de ce projet.

> [!IMPORTANT]
> Notez que ceci spécifie uniquement le répertoire `/multilang` et non `/multilang/resources`. Storm attend des ressources non JVM dans un répertoire `resources` ; la recherche s’effectue donc déjà en interne. Le regroupement des composants dans ce dossier vous permet simplement d’effectuer des références par nom dans le code Java. Par exemple : `super("python", "countbolt.py");`. Autrement dit, Storm voit le répertoire `resources` à la racine (/) lorsqu’il accède aux ressources multi-langage.
> 
> Pour cet exemple de projet, le module `storm.py` est inclus dans le répertoire `/multilang/resources`.
> 
> 

### <a name="build-and-run-the-project"></a>Créer et exécuter le projet
Pour exécuter ce projet en local, utilisez la commande Maven suivante pour générer et exécuter en mode local :

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Utilisez les touches ctrl + c pour arrêter le processus.

Pour déployer le projet sur un cluster HDInsight exécutant Apache Storm, procédez comme suit :

1. Créez un fichier uber jar:
   
        mvn package
   
    Vous obtiendrez un fichier nommé **WordCount--1.0-SNAPSHOT.jar** dans le répertoire `/target` de ce projet.
2. Téléchargez le fichier jar dans le cluster Hadoop en procédant de l’une des manières suivantes :
   
   * Pour les clusters HDInsight **basés sur Linux** : utilisez `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` pour copier le fichier jar sur le cluster, en remplaçant USERNAME par votre nom d’utilisateur SSH et CLUSTERNAME par le nom du cluster HDInsight.
     
       Une fois le téléchargement du fichier terminé, connectez-vous au cluster à l’aide de SSH et démarrez la topologie à l’aide de `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`
   * Pour des clusters HDInsight **basés sur Windows**: connectez-vous au tableau de bord Storm en accédant à HTTPS://CLUSTERNAME.azurehdinsight.net/ dans votre navigateur. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight et indiquez le nom et le mot de passe d’administrateur lorsque vous y êtes invité.
     
       À l’aide du formulaire, exécutez les étapes suivantes :
     
     * **Fichier jar** : cliquez sur **Parcourir**, puis sélectionnez le fichier **WordCount-1.0-SNAPSHOT.jar**
     * **Nom de la classe** : entrez `com.microsoft.example.WordCount`
     * **Paramètres supplémentaires** : entrez un nom convivial tel que `wordcount` pour identifier la topologie
       
       Enfin, sélectionnez **Soumettre** pour démarrer la topologie.

> [!NOTE]
> Une fois démarrée, la topologie Storm s’exécute jusqu’à ce qu’elle soit arrêtée (supprimée). Pour arrêter la topologie, utilisez la commande `storm kill TOPOLOGYNAME` à partir de la ligne de commande (session SSH sur un cluster Linux, par exemple) ou à l’aide de l’interface utilisateur Storm, sélectionnez la topologie, puis cliquez sur **Supprimer**.
> 
> 

## <a name="python-components-with-a-clojure-topology"></a>Composants de Python avec une topologie Clojure
> [!NOTE]
> Cet exemple est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) , dans le répertoire **ClojureTopology** .
> 
> 

Cette topologie a été créée en utilisant [Leiningen](http://leiningen.org) pour [créer un nouveau projet Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Après quoi, les modifications suivantes ont été apportées au projet structuré :

* **project.clj**: ajout de dépendances pour Storm et d’exclusions pour les éléments qui peuvent poser un problème lors de leur déploiement vers le serveur HDInsight.
* **ressources/ressources** : Leiningen crée un répertoire `resources` par défaut. Cependant, les fichiers stockés à cet emplacement semblent ajoutés à la racine du fichier jar créé à partir de ce projet ; or Storm attend des fichiers dans un sous-répertoire nommé `resources`. Un sous-répertoire a donc été ajouté et les fichiers Python sont maintenant stockés dans `resources/resources`. Au moment de l’exécution, ce répertoire sera traité comme la racine (/) pour l’accès aux composants de Python.
* **src/wordcount/core.clj** : ce fichier contient la définition de la topologie et est référencé à partir du fichier **project.clj**. Pour plus d’informations sur l’utilisation de Clojure pour la définition d’une topologie Storm, consultez la page [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

### <a name="build-and-run-the-project"></a>Créer et exécuter le projet
**Pour générer et exécuter le projet en local**, utilisez la commande suivante :

    lein clean, run

Pour arrêter la topologie, utilisez les touches **Ctrl + C**.

**Pour générer un uberjar et effectuer le déploiement vers HDInsight**, procédez comme suit :

1. Créez un uberjar contenant la topologie et les dépendances requises :
   
        lein uberjar
   
    Créez un nouveau fichier nommé `wordcount-1.0-SNAPSHOT.jar` , dans le répertoire `target\uberjar+uberjar` .
2. Utilisez l’une des méthodes suivantes pour déployer et exécuter la topologie sur un cluster HDInsight :
   
   * **HDInsight Linux**
     
     1. Copiez le fichier sur le nœud principal du cluster HDInsight à l’aide de `scp`. Par exemple :
        
             scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
        
         Remplacez USERNAME par un utilisateur SSH de votre cluster et CLUSTERNAME par le nom de votre cluster HDInsight.
     2. Une fois le fichier copié dans le cluster, utilisez le protocole SSH pour vous connecter au cluster et soumettre la tâche. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez l’un des articles suivants :
        
        * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
     3. Une fois connecté, procédez comme suit pour démarrer la topologie :
        
             storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
   * **HDInsight Windows**
     
     1. Connectez-vous au tableau de bord Storm en accédant à HTTPS://CLUSTERNAME.azurehdinsight.net/ dans votre navigateur. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight et indiquez le nom et le mot de passe d’administrateur lorsque vous y êtes invité.
     2. À l’aide du formulaire, exécutez les étapes suivantes :
        
        * **Fichier jar** : cliquez sur **Parcourir**, puis sélectionnez le fichier **WordCount-1.0-SNAPSHOT.jar**
        * **Nom de la classe** : entrez `wordcount.core`
        * **Paramètres supplémentaires** : entrez un nom convivial tel que `wordcount` pour identifier la topologie
          
          Enfin, sélectionnez **Soumettre** pour démarrer la topologie.

> [!NOTE]
> Une fois démarrée, la topologie Storm s’exécute jusqu’à ce qu’elle soit arrêtée (supprimée). Pour arrêter la topologie, utilisez la commande `storm kill TOPOLOGYNAME` à partir de la ligne de commande (session SSH sur un cluster Linux) ou à l’aide de l’interface utilisateur Storm, sélectionnez la topologie, puis cliquez sur **Supprimer**.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à utiliser les composants de Python à partir d’une topologie Storm. Consultez les documents suivants pour découvrir d’autres façons de travailler avec HDInsight :

* [Développement de programmes de diffusion en continu Python pour HDInsight](hdinsight-hadoop-streaming-python.md)
* [Utilisation de Python avec Hive et Pig dans HDInsight](hdinsight-python.md)


