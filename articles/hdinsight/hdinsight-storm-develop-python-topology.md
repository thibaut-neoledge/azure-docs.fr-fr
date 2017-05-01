---
title: Utiliser des composants Python dans une topologie Storm sur HDinsight | Microsoft Docs
description: "Découvrez comment utiliser des composants Python avec Apache Storm sur Azure HDInsight."
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
ms.date: 04/04/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 39a8eb9ce6a5363f541f02c33cd46d56fdabcae8
ms.lasthandoff: 04/12/2017


---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Développer des topologies Storm Apache à l’aide de Python sur HDInsight

Découvrez comment utiliser les composants Python avec Storm sur HDInsight. Apache Storm prend en charge plusieurs langages et vous permet de combiner des composants de plusieurs langages dans une même topologie. L’infrastructure de Flux (introduite avec Storm 0.10.0) permet de créer facilement des solutions qui utilisent des composants Python.

> [!IMPORTANT]
> Les informations contenues dans ce document ont été testées avec Storm sur HDInsight 3.5. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez la page [Dépréciation d’HDInsight versions 3.3 et 3.4](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

Le code de ce projet est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Composants requis

* Python 2.7 ou ultérieure

* Java JDK 1.8 ou version ultérieure.

* Maven 3.

* (Facultatif) Un environnement de développement Storm local. Un environnement Storm local n’est nécessaire que si vous souhaitez exécuter la topologie localement. Pour plus d’informations, consultez la page [Configurer un environnement de développement](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Prise en charge multi-langage de Storm

Storm est conçu pour fonctionner avec des composants écrits dans n’importe quel langage de programmation. Les composants doivent comprendre comment travailler avec la [définition Thrift pour Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Pour Python, un module est fourni dans le cadre du projet Apache Storm pour vous permettre de communiquer facilement avec Storm. Ce module est disponible à l’adresse [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Apache Storm est un processus Java qui s’exécute sur la Machine virtuelle Java (JVM). Les composants écrits dans d’autres langages sont exécutés en tant que sous-processus. Storm communique avec ces sous-processus à l’aide de messages JSON envoyées par le biais de stdin/stdout. Vous trouverez plus de détails sur la communication entre les composants dans la documentation relative au [Protocole multi-langage](https://storm.apache.org/documentation/Multilang-protocol.html).

## <a name="python-and-the-flux-framework"></a>Python et l’infrastructure Flux

L’infrastructure Flux permet de définir des topologies Storm séparément des composants. Alors que les composants de cet exemple sont écrits en Python, la topologie est définie avec YAML. Le texte suivant est un exemple de référencement d’un composant Python dans le document YAML :

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

La classe `FluxShellSpout` est utilisée pour démarrer le script `sentencespout.py` qui implémente le Spout.

Flux s’attend à ce que les scripts Python se trouvent dans le répertoire `/resources` du fichier jar contenant la topologie. Cet exemple stocke donc les scripts Python dans le répertoire `/multilang/resources`. `pom.xml` inclut ce fichier selon le code XML suivant :

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Comme mentionné précédemment, il existe un fichier `storm.py` qui implémente la définition Thrift pour Storm. L’infrastructure Flux l’inclut automatiquement lors de la génération du projet : vous n’avez donc pas besoin de vous en occuper.

## <a name="build-the-project"></a>Créer le projet

À la racine du projet, utilisez la commande suivante :

```bash
mvn clean compile package
```

Cette commande crée un fichier `target/WordCount-1.0-SNAPSHOT.jar` qui contient la topologie compilée.

## <a name="run-the-topology-locally"></a>Exécuter la topologie localement

Pour exécuter la topologie localement, utilisez la commande suivante :

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]
> Cette commande requiert un environnement de développement Storm local. Pour plus d’informations, consultez la page [Configurer un environnement de développement](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html).

Une fois démarrée, la topologie émet des informations de ce type sur la console locale :

```
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
^C24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Utilisez les touches Ctrl+c pour arrêter la topologie.

## <a name="run-the-topology-on-hdinsight"></a>Exécuter la topologie sur HDInsight

1. Utilisez la commande suivante pour copier le fichier `WordCount-1.0-SNAPSHOT.jar` sur votre Storm dans votre cluster HDInsight :

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Remplacez `sshuser` par l’utilisateur SSH de votre cluster. Remplacez `mycluster` par le nom du cluster. Vous serez peut-être invité à entrer le mot de passe de l’utilisateur SSH.

    Pour plus d’informations sur SSH et SCP, consultez la page [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Une fois le fichier chargé, connectez-vous au cluster via SSH :

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Dans la session SSH, utilisez la commande suivante pour démarrer la topologie sur le cluster :

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Vous pouvez utiliser l’interface utilisateur Storm pour afficher la topologie sur le cluster. L’interface utilisateur Storm se trouve à l’adresse https://mycluster.azurehdinsight.net/stormui. Remplacez `mycluster` par le nom de votre cluster.

> [!NOTE]
> Une fois démarrée, la topologie Storm s’exécute jusqu’à ce qu’elle soit arrêtée. Pour arrêter la topologie, utilisez l’une des méthodes suivantes :
>
> * la commande `storm kill TOPOLOGYNAME` en ligne de commande ;
> * le bouton **Tuer** dans l’interface utilisateur Storm.


## <a name="next-steps"></a>Étapes suivantes

Consultez les documents suivants pour découvrir d’autres façons de travailler avec HDInsight :

* [Développement de programmes de diffusion en continu Python pour HDInsight](hdinsight-hadoop-streaming-python.md)
* [Utilisation de Python avec Hive et Pig dans HDInsight](hdinsight-python.md)

