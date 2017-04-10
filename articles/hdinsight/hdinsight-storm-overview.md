---
title: "Présentation d’Apache Storm sur HDInsight | Microsoft Docs"
description: "Présentation d’Apache Storm sur HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/31/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1155f534869f240c1567fa8b791dbcb7750c8a40
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Présentation d’Apache Storm sur HDInsight : analyse en temps réel pour Hadoop

Apache Storm sur HDInsight offre la possibilité de créer des solutions d’analyse distribuée et en temps réel sur Azure.

Apache Storm est un système de calcul distribué, open source et tolérant aux pannes qui permet de traiter les données en temps réel avec Hadoop. Les solutions Storm peuvent également permettre un traitement garanti des données, ainsi que la possibilité de relire les données dont le traitement a échoué une première fois.

## <a name="how-does-storm-work"></a>Fonctionnement de Storm

Apache Storm exécute des **topologies** au lieu de tâches MapReduce que vous connaissez peut-être si vous utilisez HDInsight ou Hadoop. Les topologies sont composées de plusieurs composants organisés dans un graphe orienté acyclique (DAG). Le diagramme suivant illustre les flux de données entre les composants dans une topologie de base des statistiques

![Exemple d’organisation des composants dans une topologie Storm](./media/hdinsight-storm-overview/wordcount-topology.png)

* Les composants __Spout__ importent les données dans une topologie. Ils émettent un ou plusieurs flux dans la topologie

* Les composants __Bolt__ utilisent les flux émis à partir des spouts ou des autres bolts. Les bolts peuvent éventuellement émettre de nouveaux flux vers la topologie. Les bolts sont également responsables de l’écriture de données sur le stockage permanent, comme HDFS ou HBase.

## <a name="why-use-storm-on-hdinsight"></a>Pourquoi utiliser Storm sur HDInsight ?

Le logiciel Apache Storm sur HDInsight offre les principaux avantages suivants :

* Il s’exécute en tant que service géré, avec un contrat de niveau de service garantissant un taux de disponibilité de 99,9 %.

* Il est facile à personnaliser via l’exécution de scripts sur le cluster, pendant la création ou une fois celle-ci effectuée. Pour en savoir plus, voir [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md).

* Utilisez le langage de votre choix : les composants Storm peuvent être écrits dans plusieurs langages, tels que **Java**, **C#**, et **Python**.

    * Intégration de Visual Studio avec HDInsight pour le développement, la gestion et la surveillance des topologies C#. Pour en savoir plus, voir [Développement de topologies C# pour Apache Storm sur HDInsight à l’aide des outils Hadoop pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Prise en charge de l’interface Java **Trident**. Cette interface vous permet de créer des topologies Storm qui prennent en charge « exactement une fois » le traitement des messages, la persistance de magasin de données « transactionnels » et un ensemble d’opérations d’analyses courantes de flux de données.

* Il permet une montée ou descente en puissance aisée du cluster. Ajoutez ou supprimez les nœuds Worker sans nuire à l’exécution des topologies Storm.

* Intégration aux Services Azure suivants :

    * Event Hubs

    * Réseau virtuel

    * Base de données SQL

    * Azure Storage

    * DocumentDB.

    * Association sécurisée des fonctionnalités de plusieurs clusters HDInsight à l’aide d’un réseau virtuel Azure ; créez des pipelines analytiques qui utilisent des clusters HDInsight, HBase ou Hadoop.

Pour obtenir une liste des entreprises qui utilisent Apache Storm pour leurs solutions d‘analyse en temps réel, consultez la rubrique [Entreprises utilisant Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Pour découvrir Storm, consultez la rubrique [Prise en main de Storm sur HDInsight][gettingstarted].

## <a name="ease-of-creation"></a>Facilité de création

Vous pouvez approvisionner un nouveau cluster Storm sur HDInsight en quelques minutes. Pour plus d’informations sur la création d’un cluster Storm, consultez [Prise en main de Storm sur un cluster HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Simplicité d'utilisation

* __Connectivité Secure Shell__ : vous pouvez accéder aux nœuds principaux de votre cluster HDInsight via internet, à l’aide de SSH. SSH vous permet d’exécuter des commandes directement sur le cluster.

  Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Connectivité web__ : les clusters HDInsight fournissent l’interface utilisateur web d’Ambari. L’interface utilisateur web d’Ambari vous permet de surveiller, configurer et gérer facilement des services sur votre cluster. Storm sur HDInsight fournit également l’interface utilisateur Storm, qui permet de surveiller et gérer l’exécution de topologies Storm à partir de votre navigateur.

  Pour en savoir plus, voir [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md) et [Déploiement et gestion des topologies Apache Storm sur HDInsight Linux](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell et CLI__ : Azure PowerShell et la CLI Azure fournissent des utilitaires de ligne de commande que vous pouvez utiliser avec HDInsight et d’autres services Azure, à partir de votre système client.

* __Intégration de Visual Studio__ : le logiciel Data Lake Tools pour Visual Studio inclut des modèles de projet pour la création de topologies Storm en C# à l’aide de SCP.Net framework. Data Lake Tools fournit des outils permettant de déployer, de surveiller et de gérer des solutions avec Storm sur HDInsight.

  Pour en savoir plus, voir [Développement de topologies C# pour Apache Storm sur HDInsight à l’aide des outils Hadoop pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Intégration avec d’autres services Azure

* __Azure Data Lake Store__ : pour obtenir un exemple d’utilisation de Data Lake Store avec Storm, voir [Utilisation d’Azure Data Lake Store avec Apache Storm sur HDInsight](hdinsight-storm-write-data-lake-store.md).

* __Azure Event Hubs__ : pour obtenir un exemple d’utilisation de Azure Event Hubs avec Storm, consultez les documents suivants :

    * [Développer une topologie basée sur Java pour Storm sur HDInsight](hdinsight-storm-develop-java-topology.md)

    * [Traiter des événements issus d’Azure Event Hubs avec Storm sur HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* Des exemples d’utilisation de la __base de données SQL__, __DocumentDB__, __EventHub__ et __HBase__ sont inclus en tant que modèles dans Azure Data Lake Tools pour Visual Studio. Pour plus d’informations, voir [Développement de topologies C# pour Apache Storm sur HDInsight à l’aide des outils Hadoop pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Fiabilité

Apache Storm garantit toujours que chaque message entrant est entièrement traité, même lorsque l‘analyse de données est répartie sur des centaines de nœuds.

Le **nœud Nimbus** offre une fonctionnalité similaire à Hadoop JobTracker et affecte des tâches à d‘autres nœuds du cluster via **Zookeeper**. Les nœuds Zookeeper permettent de coordonner le cluster et facilitent la communication entre Nimbus et le processus **Superviseur** sur les nœuds de travail. Si un nœud de traitement tombe en panne, le nœud Nimbus est informé et affecte la tâche et les données associées à un autre nœud.

Par défaut, Apache Storm est configuré pour n’avoir un seul nœud Nimbus. Storm sur HDInsight exécute deux nœuds Nimbus. Si le nœud principal tombe en panne, le cluster HDInsight bascule vers le nœud secondaire tandis que le nœud principal est récupéré.

![Schéma de Nimbus, de Zookeeper et de Superviseur](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Scale

Bien que vous puissiez spécifier le nombre de nœuds dans votre cluster lors de sa création, vous pourriez souhaiter augmenter ou réduire la taille du cluster pour l’adapter à votre charge de travail. Tous les clusters HDInsight permettent de modifier le nombre de nœuds du cluster, même lors du traitement des données.

> [!NOTE]
> Pour tirer parti des nouveaux nœuds ajoutés via la mise à l’échelle, vous devez rééquilibrer les topologies démarrées avant l’augmentation de la taille du cluster.

## <a name="support"></a>Support

Storm sur HDInsight bénéficie d’une assistance professionnelle complète 24h/24 7j/7. Storm sur HDInsight possède également un contrat SLA garantissant un taux de disponibilité de 99,9 %. Cela signifie que nous garantissons une disponibilité de la connectivité externe du cluster au moins 99,9 % du temps.

Pour plus d’informations, consultez le [support Azure](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Cas d’utilisation courants

Voici quelques scénarios courants dans lesquels vous pouvez utiliser Apache Storm sur HDInsight. 

* Internet des objets (IoT)
* Détection des fraudes
* Analyse des réseaux sociaux
* Extraire, transformer, charger (ETL)
* Analyse du réseau
* Search
* Engagement mobile

Pour plus d‘informations sur les scénarios réels, consultez le document [Comment les entreprises utilisent-elles Storm ?](https://storm.apache.org/documentation/Powered-By.html).

## <a name="development"></a>Développement

__Data Lake Tools pour Visual Studio__ permet aux développeurs .NET de concevoir et d‘implémenter une topologie en __C#__. Vous pouvez également créer des topologies hybrides qui utilisent des composants Java et C#.

Pour plus d‘informations, consultez la rubrique [Développer des topologies C# pour Storm sur HDInsight en utilisant Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Vous pouvez également développer des solutions __Java__ à l’aide de l’IDE de votre choix. Pour plus d’informations, voir [Développer des topologies Java pour Storm sur HDInsight](hdinsight-storm-develop-java-topology.md).

Python peut également être utilisé pour développer des composants Storm. Pour plus d’informations, voir [Développer des topologies Storm à l’aide de Python sur HDInsight](hdinsight-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Modèles de développement courants

### <a name="guaranteed-message-processing"></a>Traitement de message garanti

Storm peut fournir différents niveaux de traitement de message garanti. Par exemple, une application Storm de base peut garantir un traitement « Une fois au minimum », tandis que Trident peut garantir un traitement « Exactement une fois ».

Pour plus d‘informations, consultez la rubrique [Garanties sur le traitement des données](https://storm.apache.org/about/guarantees-data-processing.html) sur le site apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Le modèle de lecture d’une entrée tuple, qui émet entre zéro et plusieurs tuples, puis le mécanisme d’accusé de réception de l’entrée tuple appliqué dès la fin de la méthode d’exécution sont courants. Storm fournit l’interface [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html)pour automatiser ce modèle.

### <a name="joins"></a>Jointures

La façon dont les flux de données sont joints varie entre les applications. Vous pouvez, par exemple, joindre chaque tuple provenant de plusieurs flux dans un nouveau flux, ou joindre uniquement des lots de tuples pour une fenêtre spécifique. Dans tous les cas, la jointure s'effectue en utilisant [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), une méthode permettant de définir la façon dont les tuples sont acheminés vers les bolts.

Dans l‘exemple Java suivant, fieldsGrouping est utilisé pour acheminer les tuples qui proviennent des composants « 1 », « 2 » et « 3 » vers le bolt **MyJoiner** .

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>Traitement par lot

Storm fournit un mécanisme de synchronisation interne appelé « tick tuple », qui peut être utilisé pour émettre un lot toutes les X secondes.

Pour obtenir un exemple d’utilisation d’un tick tuple à partir d’un composant C#, consultez le fichier [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

Si vous utilisez Trident, ce dernier est basé sur le traitement par lot des tuples.

### <a name="caching"></a>Mise en cache

La mise en cache en mémoire est souvent utilisée pour accélérer le traitement, car elle garde en mémoire les éléments les plus utilisés. Dans la mesure où une topologie est distribuée entre plusieurs nœuds et plusieurs processus au sein de chaque nœud, vous devez envisager d’utiliser [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29). `fieldsGrouping` garantit que les tuples qui contiennent les champs utilisés pour la recherche dans le cache sont toujours acheminés vers le même processus. Cette fonctionnalité de groupage permet d’éviter la duplication des entrées de cache à travers les processus.

### <a name="streaming-top-n"></a>Diffusion des N premiers

Lorsque votre topologie dépend du calcul d’une valeur « N premiers », vous devez calculer cette valeur en parallèle. Puis, fusionnez le résultat de ces calculs dans une valeur globale. Pour ce faire, utilisez [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) pour procéder à l‘acheminement par champ pour le traitement en parallèle avant de les acheminer vers un bolt qui détermine la valeur N premiers de façon globale.

Pour obtenir un exemple de calcul pour une valeur « top N », voir l’exemple [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Journalisation

Storm utilise Apache Log4j pour journaliser les informations. Par défaut, une grande quantité de données est journalisée, et il peut donc être difficile d’effectuer un tri parmi toutes ces informations. Vous pouvez inclure un fichier de configuration de journalisation dans votre topologie Storm pour contrôler le comportement de journalisation.

Pour découvrir un exemple de topologie indiquant comment configurer la journalisation, consultez l’exemple de [comptage du nombre de mots basé sur Java](hdinsight-storm-develop-java-topology.md) pour Storm sur HDInsight.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les solutions d‘analyse en temps réel avec Apache Storm dans HDInsight :

* [Prise en main de Storm sur HDInsight][gettingstarted]
* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

