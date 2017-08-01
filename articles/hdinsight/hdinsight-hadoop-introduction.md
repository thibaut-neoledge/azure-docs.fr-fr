---
title: "Qu’est-ce que HDInsight, la pile de technologies Hadoop, et les clusters ? - Azure | Documents Microsoft"
description: "Présentation de HDInsight et de la pile de technologies et des composants Hadoop, notamment Spark, Kafka, Hive, HBase pour l’analyse de données volumineuses (Big Data)."
keywords: "azure hadoop, hadoop azure, intro hadoop, introduction hadoop, pile de technologies hadoop, intro à hadoop, introduction à hadoop, qu’est-ce qu’un cluster hadoop, qu’est-ce un cluster hadoop, pourquoi hadoop est utilisé"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: cgronlun
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: b413b6f1a6c73251dfdbe6bf9d23cdfa6510839a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/22/2017

---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Présentation d’Azure HDInsight, la pile de technologies Hadoop, et des clusters Hadoop
 Cet article fournit une présentation d’Azure HDInsight, une distribution par cloud de la pile de technologies Hadoop. Il décrit également ce qu’est un cluster Hadoop et quand l’utiliser. 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Qu’est-ce que HDInsight et la pile de technologies Hadoop ? 
Azure HDInsight est une distribution par cloud des composants Hadoop à partir de [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/). [Apache Hadoop](http://hadoop.apache.org/) était l’infrastructure open source d’origine de traitement et d’analyse distribués des jeux de données volumineuses sur des clusters d’ordinateurs. 


La pile de technologies Hadoop inclut des logiciels et utilitaires liés, notamment Apache Hive, HBase, Spark, Kafka et bien d’autres encore. Pour voir les piles de composants de technologie Hadoop disponibles sur HDInsight, consultez [Composants et versions disponibles avec HDInsight][component-versioning]. Pour plus d’informations sur Hadoop dans HDInsight, consultez la rubrique [Page de fonctionnalités Azure pour HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>Qu’est-ce qu’un cluster Hadoop et quand l’utiliser ?
*Hadoop* est également un type de cluster disposant de :

* YARN pour la planification de travaux et la gestion de ressources
* MapReduce pour le traitement en parallèle
* Le système de fichiers Hadoop Distributed File System (HDFS)
  
Les clusters Hadoop sont souvent utilisés pour le traitement par lots de données stockées. D’autres types de clusters HDInsight incluent des fonctionnalités supplémentaires : Spark est désormais devenu populaire grâce à sa vitesse de traitement en mémoire supérieure. Consultez [Types de cluster sur HDInsight](#overview) pour plus d’informations.

## <a name="what-is-big-data"></a>Que sont les données volumineuses ?
Les données volumineuses correspondent à tout ensemble important d’informations numériques tel que :

* Des données de capteur d’équipement industriel
* Une activité client collectée à partir d’un site web
* Un flux d’actualités Twitter

Les données volumineuses sont collectées dans des volumes toujours plus importants, à des vitesses élevées et pour une incroyable variété de formats. Elles peuvent être historiques (c'est-à-dire stockées) ou en temps réel (c'est-à-dire diffusées à partir de la source). 

## <a name="overview"></a>Types de cluster dans HDInsight
HDInsight comprend des types de cluster spécifiques et des fonctionnalités de personnalisation de cluster, comme l’ajout de composants, d’utilitaires et de langages.

### <a name="spark-kafka-interactive-hive-hbase-customized-and-other-cluster-types"></a>Spark, Kafka, Interactive Hive, HBase, personnalisés et autres types de cluster
HDInsight offre les types de clusters suivants :

* **[Apache Hadoop](https://wiki.apache.org/hadoop)** : utilise [HDFS](#hdfs), la gestion de ressources [YARN](#yarn) et un modèle de programmation [MapReduce](#mapreduce) simple pour traiter et analyser les lots de données en parallèle.
* **[Apache Spark](http://spark.apache.org/)** : infrastructure de traitement parallèle prenant en charge le traitement en mémoire pour améliorer les performances des applications d’analyse de données volumineuses, Spark fonctionne avec SQL, les données de diffusion et l’apprentissage automatique. Consultez [Qu’est-ce qu’Apache Spark dans HDInsight ?](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)** : base de données NoSQL basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées (potentiellement, des milliards de lignes multipliées par des millions de colonnes). Consultez [Qu’est-ce que HBase sur HDInsight ?](hdinsight-hbase-overview.md)
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)** : serveur pour l’hébergement et la gestion de processus R distribués en parallèle. Ce serveur offre aux experts en science des données, aux statisticiens et aux programmeurs R un accès à la demande à des méthodes d’analyse évolutives et distribuées sur HDInsight. Consultez l’article [Vue d’ensemble : R Server sur HDInsight](hdinsight-hadoop-r-server-overview.md).
* **[Apache Storm](https://storm.incubator.apache.org/)** : système de calcul distribué et en temps réel permettant le traitement rapide de vastes flux de données. Storm est fourni en tant que cluster géré dans HDInsight. Consultez la rubrique [Analyse de données de capteur en temps réel au moyen de Storm et de Hadoop](hdinsight-storm-sensor-data-analysis.md).
* **[Version préliminaire d’Apache Interactive Hive (également appelé Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)** : mise en cache en mémoire autorisant des requêtes Hive interactives et plus rapides. Consultez l’article [Utilisation de Hive interactif dans HDInsight](hdinsight-hadoop-use-interactive-hive.md).
* **[Apache Kafka](https://kafka.apache.org/)** : plateforme open source utilisée pour créer des applications et des pipelines de données de diffusion en continu. Kafka fournit également une fonctionnalité de file d’attente de messages qui vous permet de publier des flux de données et de vous abonner à ces derniers. Consultez l’article [Présentation d’Apache Kafka sur HDInsight](hdinsight-apache-kafka-introduction.md).

Vous pouvez également configurer des clusters à l’aide des méthodes suivantes :
* **[Version préliminaire des clusters joints à un domaine](hdinsight-domain-joined-introduction.md)** : cluster joint à un domaine Active Directory pour que vous puissiez contrôler l’accès et assurer la gouvernance des données.
* **[Clusters personnalisés avec des actions de script](hdinsight-hadoop-customize-cluster-linux.md)** : clusters avec des scripts qui s’exécutent pendant l’approvisionnement et qui installent des composants supplémentaires.

### <a name="example-cluster-customization-scripts"></a>Exemples de scripts de personnalisation de cluster
Les actions de script sont des scripts Bash sur Linux qui sont exécutés pendant l’approvisionnement des clusters et qui permettent d’installer des composants supplémentaires sur le cluster. 

Voici des exemples de scripts fournis par l’équipe HDInsight :

* **[Hue](hdinsight-hadoop-hue-linux.md)** : ensemble d’applications web permettant d’interagir avec un cluster. Clusters Linux uniquement.
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)** : traitement de graphiques pour modéliser des relations entre des éléments ou des personnes.
* **[Solr](hdinsight-hadoop-solr-install-linux.md)** : plateforme de recherche d’entreprise qui permet d’effectuer des recherches en texte intégral sur des données.

Pour plus d’informations sur le développement de vos propres actions de script, consultez [Développement d’actions de Script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="components-and-utilities-on-hdinsight-clusters"></a>Composants et utilitaires sur des clusters HDInsight
Les composants et utilitaires suivants sont inclus sur les clusters HDInsight :

* **[Ambari](#ambari)**: approvisionnement, gestion, surveillance des clusters et utilitaires.
* **[Avro](#avro)** (bibliothèque Microsoft .NET pour Avro) : sérialisation de données pour l’environnement Microsoft .NET. 
* **[Hive et HCatalog](#hive)** : interrogation SQL et couche de gestion du stockage et des tables.
* **[Mahout](#mahout)** : pour les applications d’apprentissage automatique évolutives.
* **[MapReduce](#mapreduce)**: infrastructure héritée pour le traitement distribué et la gestion des ressources Hadoop. Consultez [YARN](#yarn).
* **[Oozie](#oozie)**: gestion de flux de travail.
* **[Phoenix](#phoenix)**: couche de base de données relationnelle sur HBase.
* **[Pig](#pig)**: création de scripts simplifiée pour les transformations MapReduce.
* **[Sqoop](#sqoop)**: importation et exportation de données.
* **[Tez](#tez)**: permet aux processus gros consommateurs de données de s’exécuter efficacement à l’échelle.
* **[YARN](#yarn)** : gestion de ressources faisant partie de la bibliothèque principale Hadoop.
* **[ZooKeeper](#zookeeper)**: coordination des processus dans les systèmes distribués.

> [!NOTE]
> Pour plus d’informations sur les composants et leurs versions, consultez [Composants et versions Hadoop dans HDInsight][component-versioning]
>
>

### <a name="ambari"></a>Ambari
Apache Ambari est destiné à l'approvisionnement, à la gestion et à la surveillance des clusters Apache Hadoop. Il comprend une collection intuitive d'outils d'opérateurs et un solide jeu d'API qui masque la complexité de Hadoop, en simplifiant le fonctionnement des clusters. Les clusters HDInsight sur Linux fournissent à la fois l’interface utilisateur web d’Ambari et l’API REST d’Ambari. Les affichages Ambari sur les clusters HDInsight permettent d’incorporer des éléments d’interface utilisateur.
Consultez [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md) et <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Référence sur l’API Apache Ambari</a>.

### <a name="avro"></a>Avro (bibliothèque Microsoft .NET pour Avro)
La bibliothèque Microsoft .NET pour Avro implémente le format compact d'échange des données binaires pour la sérialisation dans l'environnement Microsoft.NET. Elle définit un schéma sans langage spécifié afin que les données sérialisées dans un langage puissent être lues dans un autre. Vous trouverez des informations détaillées sur le format dans la <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">spécification Apache Avro</a>. Le format des fichiers Avro prend en charge le modèle de programmation MapReduce distribué : les fichiers sont « fractionnables », ce qui signifie que vous pouvez rechercher un point quelconque dans un fichier et commencer la lecture à partir d’un bloc particulier. Pour plus d'informations sur la procédure, consultez la rubrique [Sérialisation des données avec la bibliothèque Microsoft .NET pour Avro](hdinsight-dotnet-avro-serialization.md). Prise en charge de cluster basé sur Linux à venir.

### <a name="hdfs"></a>HDFS
HDFS (Hadoop Distributed File System) est un système de fichiers qui, avec YARN et MapReduce, constitue le cœur de la technologie Hadoop. Il s’agit du système de fichiers standard pour les clusters Hadoop sur HDInsight. Consultez [Interrogation des données depuis un stockage compatible avec HDFS](hdinsight-hadoop-use-blob-storage.md).

### <a name="hive"></a>Hive et HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> est un logiciel d’entrepôt de données basé sur Hadoop qui vous permet d’interroger et de gérer des jeux de données volumineux dans un système de stockage distribué au moyen d’un langage similaire à SQL appelé HiveQL. Hive, Pig par exemple, est une abstraction qui s’appuie sur MapReduce et qui traduit des requêtes en une série de travaux MapReduce. Hive est plus proche d’un système de gestion de base de données relationnelle que Pig et est utilisé avec des données plus structurées. Pour les données non structurées, Pig est le meilleur choix. Consultez la rubrique [Utilisation de Hive avec Hadoop dans HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> est une couche de gestion du stockage et des tables pour Hadoop qui vous présente une vue relationnelle des données. Dans HCatalog, vous pouvez lire et écrire des fichiers dans les formats fonctionnant avec le sérialiseur-désérialiseur Hive.

### <a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> est une bibliothèque d’algorithmes d’apprentissage automatique s’exécutant sur Hadoop. Au moyen de principes statistiques, les applications d'apprentissage automatique apprennent aux systèmes à exploiter les données et à utiliser les résultats passés pour déterminer le comportement futur. Consultez la rubrique [Génération de recommandations de films en utilisant Mahout dans Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce est une infrastructure logicielle héritée pour Hadoop, permettant d'écrire des applications qui traitent par lots des jeux de données volumineux en parallèle. Une tâche MapReduce fractionne les jeux de données volumineuses et organise les données en paires clé-valeur en vue de leur traitement. Les travaux MapReduce s’exécutent sur [YARN](#yarn). Consultez <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> dans le Wiki Hadoop.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> est un système de coordination de flux de travail qui gère les tâches Hadoop. Il est intégré à la pile Hadoop et prend en charge les tâches Hadoop pour MapReduce, Pig, Hive et Sqoop. Il peut également être utilisé pour planifier des tâches propres à un système comme des programmes Java ou des scripts shell. Voir [Utilisation d’Oozie avec Hadoop](hdinsight-use-oozie-linux-mac.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> est une couche de base de données relationnelle sur HBase. Phoenix inclut un pilote JDBC qui vous permet d’interroger et de gérer les tables SQL directement. Phoenix convertit les requêtes et autres instructions en appels d’API NoSQL natifs (au lieu d’utiliser MapReduce), permettant ainsi des applications plus rapides sur les magasins NoSQL. Consultez la page [Utiliser Apache Phoenix et SQuirreL avec les clusters HBase](hdinsight-hbase-phoenix-squirrel.md).

### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> est une plateforme généraliste permettant d’effectuer des transformations MapReduce complexes dans des jeux de données volumineux au moyen d’un langage simple de création de scripts appelé Pig Latin. Pig convertit les scripts Pig Latin pour qu'ils puissent s'exécuter dans Hadoop. Vous pouvez créer des fonctions définies par l’utilisateur pour étendre Pig Latin. Voir [Utilisation de Pig avec Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> est un outil qui permet de transférer des données en bloc entre Hadoop et des bases de données relationnelles, telles que SQL ou d’autres banques de données structurées, avec une efficacité optimale. Consultez la rubrique [Utilisation de Sqoop avec Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> est une infrastructure d’application qui repose sur des fichiers YARN Hadoop. Elle exécute des graphiques complexes et acycliques de traitement de données générales. Elle est plus souple et puissante que l’infrastructure MapReduce qui permet aux processus gros consommateurs de données, tels que Hive, de s’exécuter plus efficacement à l’échelle. Consultez la section [Utilisation d’Apache Tez pour des performances améliorées de la rubrique Utilisation de Hive et HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
Apache YARN représente la nouvelle génération de MapReduce (MapReduce 2.0 ou MRv2) et prend en charge les scénarios de traitement des données qui vont au-delà du traitement par lots MapReduce grâce à une plus grande évolutivité et un traitement en temps réel. YARN fournit une gestion des ressources et une infrastructure d'application distribuée. Les travaux MapReduce s’exécutent sur YARN. Consultez <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordonne les processus dans les systèmes distribués de grande taille à l’aide d’un espace de noms hiérarchique partagé de registres de données (znodes). les znodes contiennent de petites quantités de méta-informations permettant de coordonner les processus : état, emplacement, configuration, etc. Voir un exemple de [ZooKeeper avec un cluster HBase et Apache Phoenix](hdinsight-hbase-phoenix-squirrel-linux.md). 

## <a name="programming-languages-on-hdinsight"></a>Langages de programmation sur HDInsight
Les clusters HDInsight (Spark, HBase, Kafka, Hadoop et autres clusters) prennent en charge de nombreux langages de programmation, mais certains ne sont pas installés par défaut. Pour les bibliothèques, modules ou packages non installés par défaut, [utilisez une action de script pour installer le composant](hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Prise en charge des langages de programmation par défaut
Par défaut, HDInsight prend en charge :

* Java
* Python

Des langages supplémentaires peuvent être installés à l’aide d’[actions de script](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Langages de machines virtuelles Java (JVM)
De nombreux langages autre que Java peuvent être exécutés sur une machine virtuelle Java (JVM). Cependant, certains de ces langages peuvent nécessiter des composants supplémentaires installés sur le cluster.

Les langages JVM suivants sont pris en charge sur les clusters HDInsight :

* Clojure
* Jython (Python pour Java)
* Scala

### <a name="hadoop-specific-languages"></a>Langages spécifiques à Hadoop
Les clusters HDInsight prennent en charge les langages suivants, spécifiques à la pile de technologies Hadoop :

* Pig Latin pour les travaux Pig
* HiveQL pour les travaux Hive et SparkSQL

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard et HDInsight Premium
HDInsight propose deux catégories d’offres de cloud Big Data : Standard et Premium. HDInsight Standard fournit un cluster de niveau entreprise que les organisations peuvent utiliser pour exécuter leurs charges de travail Big Data. HDInsight Premium est dérivé des fonctionnalités Standard et introduit des fonctionnalités d’analyse et de sécurité avancées dans un cluster HDInsight. Pour plus d’informations, consultez [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="microsoft-business-intelligence-and-hdinsight"></a>Décisionnel Microsoft et HDInsight
Les outils décisionnels courants permettent de récupérer des données intégrées à HDInsight, de les analyser et de générer des rapports à leur sujet via le complément Power Query ou le pilote ODBC Microsoft Hive :

* [Connecter Eccel à Hadoop avec Power Query](hdinsight-connect-excel-power-query.md) : découvrez comment connecter Excel au compte de stockage Azure stockant les données à partir de votre cluster HDInsight à l’aide de Microsoft Power Query pour Excel. Station de travail Windows requise. 
* [Connexion d’Excel à Hadoop à l’aide du pilote ODBC Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md) : apprenez à importer des données à partir de HDInsight avec le pilote ODBC Microsoft Hive. Station de travail Windows requise. 
* [Plateforme cloud de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): découvrez Power BI pour Office 365, téléchargez la version d’évaluation de SQL Server, et configurez SharePoint Server 2013 et SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Étapes suivantes

* [Prise en main de Hadoop dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): didacticiel de démarrage rapide pour l’approvisionnement de clusters HDInsight Hadoop et l’exécution d’exemples de requêtes Hive.
* [Prise en main de Spark dans HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): didacticiel de démarrage rapide pour la création d’un cluster Spark et l’exécution de requêtes Spark SQL interactives.
* [Utilisation de R Server sur HDInsight](hdinsight-hadoop-r-server-get-started.md): commencez à utiliser R Server dans HDInsight Premium.
* [Approvisionnement de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md) : apprenez à configurer un cluster Hadoop HDInsight par le biais du portail Azure, de l’interface de ligne de commande Azure ou d’Azure PowerShell.


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
