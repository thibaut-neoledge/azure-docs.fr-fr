 <properties
	pageTitle="Qu’est-ce que Hadoop dans le cloud ? Introduction à HDInsight | Microsoft Azure"
	description="Qu’est-ce que Hadoop dans le cloud et comment est-il géré dans Microsoft Azure HDInsight ? Introduction aux composants Hadoop et analyse de données volumineuses."
	keywords="analyse de données volumineuses, présentation de hadoop, description de hadoop, hadoop dans le cloud, pile de technologies hadoop, écosystème hadoop"
	services="hdinsight"
	documentationCenter=""
	authors="cjgronlund"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/21/2016"
   ms.author="cgronlun"/>


# Qu’est-ce que Hadoop dans le cloud ? Présentation de l’écosystème Hadoop dans HDInsight

Obtenez une présentation de Hadoop, de son écosystème et des données volumineuses (Big Data) dans Azure HDInsight : description de Hadoop dans HDInsight, composants de Hadoop, terminologie commune et scénarios d’analyse de données volumineuses. En outre, apprenez-en plus sur la documentation, les didacticiels et les ressources pour l’utilisation de Hadoop dans le cloud dans HDInsight.

## Présentation de Hadoop dans HDInsight

Azure HDInsight utilise la distribution **HDP (Hortonworks Data Platform)** de Hadoop. HDInsight déploie et approvisionne des clusters Apache Hadoop dans le cloud gérés, en fournissant une infrastructure logicielle conçue pour traiter et analyser les données volumineuses (Big Data) avec une haute disponibilité et une haute fiabilité, et générer des rapports à leur sujet.

Hadoop fait souvent référence à l’écosystème Hadoop de composants, qui inclut Apache HBase, Apache Spark et Apache Storm, ainsi que d’autres technologies sous l’égide Hadoop. Pour plus d’informations, consultez la page [Présentation de l’écosystème Hadoop dans HDInsight](#overview).

## Que sont les données volumineuses ?

Les données volumineuses correspondent à des ensembles importants d’informations numériques, depuis le texte d’un flux Twitter jusqu’aux informations de capteurs provenant d’équipements industriels, en passant par les informations relatives à la navigation et aux achats d’un client sur un site web. Les données volumineuses peuvent être historiques (c'est-à-dire des données stockées) ou en temps réel (c'est-à-dire diffusées directement à partir de la source). Les données volumineuses sont collectées dans des volumes toujours plus importants, à des vitesses de plus en plus élevées et pour une variété croissante de formats.

Pour que ces données aient une valeur sous la forme de renseignements ou d'informations exploitables, non seulement il est indispensable de collecter les bonnes données et de poser les bonnes questions, mais les données doivent également être accessibles, nettoyées, analysées, puis présentées de manière utile. C'est dans ce contexte qu’une analyse de données volumineuses sur Hadoop dans HDInsight peut vous aider.

## <a name="overview"></a>Présentation de l’écosystème Hadoop dans HDInsight

HDInsight est une implémentation dans le cloud Microsoft Azure de la pile de technologies Apache Hadoop, qui connaît actuellement un développement rapide. Cette implémentation constitue la solution pertinente pour l'analyse des données volumineuses. Elle comprend, entre autres, des implémentations de Apache Spark, HBase, Storm, Pig, Hive, Sqoop, Oozie, Ambari. HDInsight peut également être intégré aux outils décisionnels tels que Power BI, Excel, SQL Server Analysis Services et SQL Server Reporting Services.

### Clusters Hadoop, HBase, Spark, Storm et personnalisés

HDInsight fournit des configurations de cluster pour Apache Hadoop, Spark, HBase ou Storm. Vous pouvez également [personnaliser les clusters avec des actions de script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop** (charge de travail de requête) : fournit un stockage fiable des données avec [HDFS](#hdfs) et un simple modèle de programmation [MapReduce](#mapreduce) pour traiter et analyser les données en parallèle.

* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>** : infrastructure de traitement parallèle prenant en charge le traitement en mémoire pour améliorer les performances des applications d'analyse de données volumineuses, Spark fonctionne avec SQL, les données de diffusion et l'apprentissage automatique. Voir [Vue d’ensemble : qu'est-ce qu'Apache Spark dans HDInsight ?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** (charge de travail NoSQL) : base de données NoSQL basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées (potentiellement, des milliards de lignes multipliées par des millions de colonnes). Consultez la rubrique [Présentation de HBase dans HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** (charge de travail de flux) : système de calcul distribué et en temps réel permettant le traitement rapide de vastes flux de données. Storm est fourni en tant que cluster géré dans HDInsight. Consultez la rubrique [Analyse de données de capteur en temps réel au moyen de Storm et de Hadoop](hdinsight-storm-sensor-data-analysis.md).

### Exemples de scripts de personnalisation

Les actions de script sont des scripts exécutés pendant l’approvisionnement du cluster et peuvent être utilisées pour installer des composants supplémentaires sur le cluster. Pour les clusters Linux, utilisez des scripts Bash.

Voici des exemples de scripts fournis par l’équipe HDInsight :

* [Hue](hdinsight-hadoop-hue-linux.md) : ensemble d’applications web permettant d’interagir avec un cluster. Clusters Linux uniquement.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md) : traitement de graphiques pour modéliser des relations entre des éléments ou des personnes.

* [R](hdinsight-hadoop-r-scripts-linux.md) : se compose d'un langage et d'un environnement open source destinés au calcul de statistiques lors de l’apprentissage machine.

* [Solr](hdinsight-hadoop-solr-install-linux.md) : plateforme de recherche d'entreprise qui permet d'effectuer des recherches en texte intégral sur des données.

Pour plus d’informations sur le développement de vos propres actions de script, consultez [Développer des actions de Script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).


## Quels sont les composants et utilitaires d'Hadoop ?

Les composants et les utilitaires suivants sont inclus dans les clusters HDInsight.

* **[Ambari](#ambari)** : approvisionnement, gestion, surveillance des clusters et utilitaires.

* **[Avro](#avro)** (bibliothèque Microsoft .NET pour Avro) : sérialisation de données pour l’environnement Microsoft .NET.

* **[Hive et HCatalog](#hive)** : interrogation SQL (Structured Query Language, langage de requête structuré) et couche de gestion du stockage et des tables.

* **[Mahout](#mahout)** : apprentissage automatique.

* **[MapReduce](#mapreduce)** : infrastructure héritée pour le traitement distribué et la gestion des ressources Hadoop. Voir [YARN](#yarn), la nouvelle génération d’infrastructure de ressources.

* **[Oozie](#oozie)** : gestion de flux de travail.

* **[Phoenix](#phoenix)** : couche de base de données relationnelle sur HBase.

* **[Pig](#pig)** : création de scripts simplifiée pour les transformations MapReduce.

* **[Sqoop](#sqoop)** : importation et exportation de données.

* **[Tez](#tez)** : permet aux processus gros consommateurs de données de s’exécuter efficacement à l’échelle.

* **[YARN](#yarn)** : partie intégrante de la principale bibliothèque Hadoop et de la nouvelle génération d’infrastructure logicielle MapReduce.

* **[ZooKeeper](#zookeeper)** : coordination des processus dans les systèmes distribués.

> [AZURE.NOTE] Pour plus d’informations sur les composants et leurs versions, consultez [Composants, contrôle de version et offres de services dans HDInsight][component-versioning]

### <a name="ambari"></a>Ambari

Apache Ambari est destiné à l'approvisionnement, à la gestion et à la surveillance des clusters Apache Hadoop. Il comprend une collection intuitive d'outils d'opérateurs et un solide jeu d'API qui masque la complexité de Hadoop, en simplifiant le fonctionnement des clusters. Les clusters HDInsight Linux fournissent tous l’interface utilisateur web Ambari et l’API REST Ambari, tandis que les clusters Windows fournissent un sous-ensemble de l’API REST. Les affichages Ambari sur les clusters HDInsight permettent d’incorporer des éléments d’interface utilisateur.

Consultez les pages [Gestion des clusters HDInsight avec Ambari](hdinsight-hadoop-manage-ambari.md) (Linux exclusivement), [Surveillance des clusters Hadoop dans HDInsight à l'aide des API Ambari](hdinsight-monitor-use-ambari-api.md) et <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Référence des API Apache Ambari</a>.

### <a name="avro"></a>Avro (bibliothèque Microsoft .NET pour Avro)

La bibliothèque Microsoft .NET pour Avro implémente le format compact d'échange des données binaires pour la sérialisation dans l'environnement Microsoft.NET. Il utilise <a target="_blank" href="http://www.json.org/">JavaScript Objet Notation (JSON)</a> pour définir un schéma sans langage spécifié qui assure l'interopérabilité des langages, c'est-à-dire que les données sérialisées dans un langage peuvent être lues dans un autre langage. Vous pouvez trouver des informations détaillées sur ce format dans la <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">spécification Apache Avro</a>. Le format des fichiers Avro prend en charge le modèle de programmation MapReduce distribué. Les fichiers sont « fractionnables », ce qui signifie que vous pouvez rechercher un point quelconque dans un fichier et commencer la lecture à partir d'un bloc particulier. Pour plus d'informations sur la procédure, consultez la rubrique [Sérialisation des données avec la bibliothèque Microsoft .NET pour Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

HDFS (Hadoop Distributed File System) est un système de fichiers distribués qui, avec MapReduce et YARN, constitue le cœur de l'écosystème Hadoop. HDFS est le système de fichiers standard pour les clusters Hadoop dans HDInsight.

### <a name="hive"></a>Hive et HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> est un logiciel d’entrepôt de données basé sur Hadoop qui vous permet d’interroger et de gérer des jeux de données volumineux dans un système de stockage distribué au moyen d’un langage similaire à SQL appelé HiveQL. À l'instar de Pig, Hive est une abstraction qui s'appuie sur MapReduce. Lorsque vous l’exécutez, Hive traduit des requêtes en une série de tâches MapReduce. D'un point de vue conceptuel, Hive est plus proche d'un système de gestion de base de données relationnelle que Pig et convient dès lors mieux à une utilisation avec des données plus structurées. Pour les données non structurées, Pig est le meilleur choix. Consultez la rubrique [Utilisation de Hive avec Hadoop dans HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> est une couche de gestion du stockage et des tables pour Hadoop qui offre aux utilisateurs une vue relationnelle des données. Dans HCatalog, vous pouvez lire et écrire des fichiers dans les formats pour lesquels un sérialiseur-désérialiseur Hive peut être écrit.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> est une bibliothèque évolutive d'algorithmes d'apprentissage automatique s'exécutant sur Hadoop. Au moyen de principes statistiques, les applications d'apprentissage automatique apprennent aux systèmes à exploiter les données et à utiliser les résultats passés pour déterminer le comportement futur. Consultez la rubrique [Génération de recommandations de films en utilisant Mahout dans Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce est une infrastructure logicielle héritée pour Hadoop, permettant d'écrire des applications qui traitent par lots des jeux de données volumineux en parallèle. Une tâche MapReduce fractionne les jeux de données volumineuses et organise les données en paires clé-valeur en vue de leur traitement.

[YARN](#yarn) représente la nouvelle génération de gestionnaire de ressources et d’application Hadoop, appelée MapReduce 2.0. Les travaux MapReduce seront exécutés sur YARN.

Pour plus d'informations sur MapReduce, consultez <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> dans le wiki Hadoop.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> est un système de coordination de flux de travail qui gère les tâches Hadoop. Il est intégré à la pile Hadoop et prend en charge les tâches Hadoop pour MapReduce, Pig, Hive et Sqoop. Il peut également être utilisé pour planifier des tâches propres à un système comme des programmes Java ou des scripts shell. Voir [Utilisation d’Oozie avec Hadoop](hdinsight-use-oozie.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> est une couche de base de données relationnelle sur HBase. Phoenix inclut un pilote JDBC qui permet aux utilisateurs d’interroger et de gérer directement les tables SQL. Phoenix convertit les requêtes et autres instructions en appels d’API NoSQL natifs (au lieu d’utiliser MapReduce), permettant ainsi des applications plus rapides sur les magasins NoSQL. Consultez la page [Utiliser Apache Phoenix et SQuirreL avec les clusters HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> est une plateforme généraliste permettant d'effectuer des transformations MapReduce complexes dans des jeux de données très volumineux au moyen d'un langage simple de création de scripts appelé Pig Latin. Pig convertit les scripts Pig Latin pour qu'ils puissent s'exécuter dans Hadoop. Vous pouvez créer des fonctions définies par l'utilisateur pour étendre Pig Latin. Voir [Utilisation de Pig avec Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> est un outil qui permet de transférer des données en bloc entre Hadoop et des bases de données relationnelles, telles que SQL ou d'autres banques de données structurées, avec une efficacité optimale. Consultez la rubrique [Utilisation de Sqoop avec Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> est une infrastructure d’application qui repose sur des fichiers YARN Hadoop. Elle exécute des graphiques complexes et acycliques de traitement de données générales. Elle est plus souple et puissante que l’infrastructure MapReduce qui permet aux processus gros consommateurs de données, tels que Hive, de s’exécuter plus efficacement à l’échelle. Consultez la section [Utilisation d’Apache Tez pour des performances améliorées de la rubrique Utilisation de Hive et HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
Apache YARN représente la nouvelle génération de MapReduce (MapReduce 2.0 ou MRv2) et prend en charge les scénarios de traitement des données qui vont au-delà du traitement par lots MapReduce grâce à une plus grande évolutivité et un traitement en temps réel. YARN fournit une gestion des ressources et une infrastructure d'application distribuée. Les travaux MapReduce seront exécutés sur YARN.

Pour en savoir plus sur YARN, consultez <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache Zookeeper</a> coordonne les processus dans les systèmes distribués de grande taille au moyen d'un espace de noms hiérarchique partagé de registres de données (znodes). les znodes contiennent de petites quantités de méta-informations permettant de coordonner les processus : état, emplacement, configuration, etc.

## Langages de programmation sur HDInsight

Les clusters HDInsight (clusters Hadoop, HBase, Storm et Spark) prennent en charge plusieurs langages de programmation, mais certains ne sont pas installés par défaut. Pour les bibliothèques, modules ou packages non installés par défaut, utilisez une action de script pour installer le composant. Voir [Développement d’actions de script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Prise en charge des langages de programmation par défaut

Par défaut, HDInsight prend en charge :

* Java

* Python

Des langages supplémentaires peuvent être installés à l’aide d’actions de script : [Développement d’actions de script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Langages de machines virtuelles Java (JVM)

De nombreux langages autre que Java peuvent être exécutés à l'aide d'une machine virtuelle Java (JVM). Cependant, certains de ces langages peuvent nécessiter des composants supplémentaires installés sur le cluster.

Les langages JVM suivants sont pris en charge sur les clusters HDInsight :

* Clojure

* Jython (Python pour Java)

* Scala

### Langages spécifiques à Hadoop

Les clusters HDInsight prennent en charge les langages suivants, spécifiques à l'écosystème Hadoop :

* Pig Latin pour les travaux Pig

* HiveQL pour les travaux Hive et SparkSQL


## <a name="advantage"></a>Avantages de Hadoop dans le cloud

En tant que partie intégrante de l'écosystème du cloud Azure, Hadoop dans HDInsight offre une série d'avantages :

* Approvisionnement automatique des clusters Hadoop. Il est beaucoup plus facile de créer des clusters HDInsight que de configurer manuellement des clusters Hadoop. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Composants Hadoop à la pointe de la technologie. Pour plus d’informations, consultez [Composants, contrôle de version et offres de services Hadoop dans HDInsight][component-versioning].

* Disponibilité et fiabilité élevées des clusters. Consultez la rubrique [Disponibilité et fiabilité des clusters Hadoop dans HDInsight](hdinsight-high-availability-linux.md) pour plus d'informations.

* Stockage efficace et économique des données avec le stockage d'objets blob Azure, une option compatible Hadoop. Consultez la rubrique [Utilisation du stockage d’objets blob Azure avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md) pour plus d’informations.

* Intégration aux autres services Azure, y compris [Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/) et [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/).

* Tailles et types de machines virtuelles supplémentaires pour l’exécution de clusters HDInsight. Voir [Composants, contrôle de version et offres de services Hadoop dans HDInsight][component-versioning] pour plus d’informations.

* Mise à l’échelle du cluster. La mise à l’échelle du cluster permet de modifier le nombre de nœuds d’un cluster HDInsight en cours d’exécution, sans avoir à le supprimer ou à le recréer.

* Prise en charge des réseaux virtuels. les clusters HDInsight peuvent être utilisés avec Azure Virtual Network pour prendre en charge l'isolement des ressources de cloud ou de scénarios hybrides liant les ressources cloud avec celles de votre centre de données.

* Faible coût d’entrée. Essayez une [version d'évaluation gratuite](https://azure.microsoft.com/free/) ou consultez la rubrique [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Pour plus d'informations sur les avantages de Hadoop dans HDInsight, consultez la rubrique [Page de fonctionnalités Azure pour HDInsight][marketing-page].

## HDInsight Standard et HDInsight Premium

HDInsight propose deux catégories d’offres de cloud Big Data : Standard et Premium. HDInsight Standard fournit un cluster de niveau entreprise que les organisations peuvent utiliser pour exécuter leurs charges de travail Big Data. HDInsight Premium est dérivé de cette offre et introduit des fonctionnalités d’analyse et de sécurité avancées dans un cluster HDInsight. Pour plus d’informations, consultez [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)


## <a id="resources"></a>Ressources supplémentaires sur l'analyse des données volumineuses, Hadoop et HDInsight

Créez sur cette présentation de Hadoop dans le cloud et l'analyse de données volumineuses avec les ressources ci-dessous.


### Documentation Hadoop pour HDInsight.

* [Documentation HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) : page de documentation pour Hadoop sur Azure HDInsight proposant des liens vers des articles, des vidéos et d’autres ressources.

* [Prise en main de Hadoop dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) : didacticiel de démarrage rapide pour l’approvisionnement de clusters HDInsight Hadoop et l’exécution d’exemples de requêtes Hive.

* [Prise en main de Spark dans HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) : didacticiel de démarrage rapide pour la création d’un cluster Spark et l’exécution de requêtes Spark SQL interactives.

* [Utilisation de R Server sur HDInsight](hdinsight-hadoop-r-server-get-started.md) : commencez à utiliser R Server dans HDInsight Premium.

* [Approvisionnement de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md) : apprenez à configurer un cluster Hadoop HDInsight par le biais du portail Azure, de l’interface de ligne de commande Azure ou d’Azure PowerShell.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a> : découvrez plus en détail la bibliothèque de logiciels Apache Hadoop, infrastructure autorisant le traitement distribué de jeux de données volumineux sur des clusters d’ordinateurs.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a> : découvrez plus en détail l’architecture et la conception du système HDFS (Hadoop Distributed File System), principal système de stockage utilisé par les applications Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce Tutorial</a> : découvrez plus en détail l’infrastructure de programmation pour écrire des applications Hadoop qui autorisent un traitement rapide et en parallèle de vastes quantités de données sur des clusters de nœuds de calcul volumineux.


### Décisionnel Microsoft

Les outils décisionnels courants que sont Excel, PowerPivot, SQL Server Analysis Services et Reporting Services permettent de récupérer des données intégrées à HDInsight, de les analyser et de générer des rapports à leur sujet via le complément Power Query ou le pilote ODBC Microsoft Hive.

Ces outils décisionnels peuvent s’avérer utiles lors de vos analyses de données volumineuses :

* [Connexion d’Excel à Hadoop à l’aide de Power Query](hdinsight-connect-excel-power-query.md) : apprenez à connecter Excel au compte de stockage Azure dans lequel sont stockées les données associées à votre cluster HDInsight via Microsoft Power Query pour Excel. Station de travail Windows requise. Compatible avec les clusters basés sur Windows ou Linux.

* [Connexion d’Excel à Hadoop à l’aide du pilote ODBC Microsoft Hive](hdinsight-connect-excel-hive-ODBC-driver.md) : apprenez à importer des données à partir de HDInsight avec le pilote ODBC Microsoft Hive. Station de travail Windows requise. Compatible avec les clusters basés sur Windows ou Linux.

* [Plateforme cloud de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx) : découvrez Power BI pour Office 365, téléchargez la version d’évaluation de SQL Server, et configurez SharePoint Server 2013 et SQL Server BI.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0914_2016-->