<properties
	pageTitle="Description de Hadoop dans HDInsight : analyse des données volumineuses dans le cloud | Microsoft Azure"
	description="Introduction aux composants Hadoop dans le cloud dans HDInsight. Découvrez comment HDInsight utilise des clusters Hadoop pour gérer les données volumineuses, les analyser et créer des rapports sur ces données."
	keywords="big data,big data analysis,hadoop,introduction to hadoop,what is hadoop"
	services="hdinsight"
	documentationCenter=""
	authors="cjgronlund"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/03/2015"
   ms.author="cgronlun"/>


# Présentation de Hadoop dans HDInsight : traitement et analyse des données volumineuses dans le cloud

Obtenez une présentation de Hadoop, de son écosystème et des données volumineuses (Big Data) dans Azure HDInsight : description de Hadoop dans HDInsight, composants de Hadoop, terminologie commune et scénarios d’analyse de données volumineuses. En outre, apprenez-en plus sur la documentation, les didacticiels et les ressources pour l’utilisation de Hadoop dans HDInsight.

## Présentation de Hadoop dans HDInsight

Azure HDInsight déploie et approvisionne des clusters Apache Hadoop dans le cloud, en fournissant une infrastructure logicielle conçue pour gérer et analyser les données volumineuses (Big Data) avec une haute disponibilité et une haute fiabilité, et générer des rapports à leur sujet. HDInsight utilise la distribution **HDP (Hortonworks Data Platform)** de Hadoop. Hadoop fait souvent référence à l’écosystème Hadoop de composants, qui inclut des clusters Storm et HBase, ainsi que d’autres technologies sous l’égide Hadoop. Pour plus d’informations, consultez la page [Présentation de l’écosystème Hadoop dans HDInsight](#overview).


## Que sont les données volumineuses ?
Les données volumineuses font référence aux données qui sont collectées dans des volumes toujours plus importants, à des vitesses de plus en plus élevées et pour une variété croissante de formats non structurés et de contextes sémantiques variables.

Les données volumineuses correspondent à des ensembles importants d'informations numériques, depuis le texte d'un flux Twitter jusqu'aux informations de capteurs provenant d'équipements industriels, en passant par les informations relatives à la navigation et aux achats d'un client sur un catalogue en ligne. Les données volumineuses peuvent être historiques (c'est-à-dire des données stockées) ou en temps réel (c'est-à-dire diffusées directement à partir de la source).

Pour que ces données aient une valeur sous la forme de renseignements ou d'informations exploitables, non seulement il est indispensable de poser les bonnes questions et de collecter des données en rapport avec les problématiques rencontrées, mais les données doivent également être accessibles, nettoyées, analysées, puis présentées de manière utile. C'est dans ce contexte qu’une analyse de données volumineuses sur Hadoop dans HDInsight peut vous aider.


## <a name="overview"></a>Présentation de l'écosystème Hadoop dans HDInsight

HDInsight est une implémentation dans le cloud Microsoft Azure de la pile de technologies Apache Hadoop, qui connaît actuellement un développement rapide. Cette implémentation constitue la solution pertinente pour l'analyse des données volumineuses. Elle comprend, entre autres, des implémentations de Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari. HDInsight peut également être intégré aux outils décisionnels tels qu’Excel, SQL Server Analysis Services et SQL Server Reporting Services.

### Clusters Linux et Windows

Azure HDInsight déploie et approvisionne les clusters Hadoop dans le cloud, en utilisant **Linux** ou **Windows** en tant que système d’exploitation sous-jacent.

* **HDInsight sur Linux (version préliminaire)** - Cluster Hadoop sur Ubuntu. Vous pouvez utiliser cette option si vous maîtrisez Linux ou Unix, effectuez une migration à partir d'une solution Hadoop Linux existante ou souhaitez intégrer facilement des composants de l'écosystème Hadoop conçus pour Linux.

* **HDInsight sur Windows** - Cluster Hadoop sur Windows Server. Utilisez cette option si vous maîtrisez Windows, migrez à partir d’une solution Hadoop Windows ou souhaitez intégrer des fonctionnalités .NET ou d’autres fonctionnalités de Windows.

Le tableau suivant compare les deux systèmes d’exploitation :

Catégorie | Hadoop sur Linux | Hadoop sur Windows
---------| -------------------| --------------------
**Système d’exploitation du cluster** | Prise en charge à long terme (LTS) de Ubuntu 12.04 | Windows Server 2012 R2
**Type de cluster** | Hadoop | Hadoop, Hbase, Storm
**Déploiement** | Portail Azure, interface de ligne de commande Azure, Azure PowerShell | Portail Azure, interface de ligne de commande Azure, Azure PowerShell, Kit de développement logiciel (SDK) HDInsight .NET
**IU du cluster** | Ambari | Tableau de bord du cluster
**Accès à distance** | Secure Shell (SSH) | Remote Desktop Protocol (RDP)



### Clusters Hadoop, HBase, Storm, Spark et personnalisés

HDInsight fournit des configurations de cluster pour Hadoop, HBase, Storm et Spark. Vous pouvez également <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">personnaliser les clusters avec des actions de script</a>.

* **Hadoop** (charge de travail de requête) : fournit un stockage fiable des données avec [HDFS](#HDFS) et un simple modèle de programmation [MapReduce](#mapreduce) pour traiter et analyser les données en parallèle.

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** (charge de travail NoSQL) : base de données NoSQL basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées (potentiellement, des milliards de lignes multipliées par des millions de colonnes). Consultez la rubrique [Présentation de HBase dans HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** (charge de travail de flux) : système de calcul distribué et en temps réel permettant le traitement rapide de vastes flux de données. Storm est fourni en tant que cluster géré dans HDInsight. Consultez la rubrique [Analyse de données de capteur en temps réel au moyen de Storm et de Hadoop](hdinsight-storm-sensor-data-analysis.md).

* **<a  target="_blank" href="http://spark.apache.org/">Apache Spark</a>** : infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d’analyse de données volumineuses. Consultez [Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md).

## Quels sont les composants d'Hadoop ?

Outre les configurations globales précédentes, les composants individuels suivants sont également inclus dans les clusters HDInsight.

* **[Ambari](#ambari)** : approvisionnement, gestion et surveillance des clusters.

* **[Avro](#avro)** (bibliothèque Microsoft .NET pour Avro) : sérialisation de données pour l’environnement Microsoft .NET.

* **[Hive et HCatalog](#hive)** : interrogation SQL (Structured Query Language, langage de requête structuré) et couche de gestion du stockage et des tables.

* **[Mahout](#mahout)** : apprentissage automatique.

* **[MapReduce et YARN](#mapreduce)** : traitement distribué et gestion des ressources.

* **[Oozie](#oozie)** : gestion de flux de travail.

* **[Phoenix](#phoenix)** : couche de base de données relationnelle sur HBase.

* **[Pig](#pig)** : création de scripts simplifiée pour les transformations MapReduce.

* **[Sqoop](#sqoop)** : importation et exportation de données.

* **[Tez](#tez)** : permet aux processus gros consommateurs de données de s’exécuter efficacement à l’échelle.

* **[ZooKeeper](#zookeeper)** : coordination des processus dans les systèmes distribués.

> [AZURE.NOTE]Pour plus d’informations sur les composants et leurs versions, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][component-versioning].

###<a name="ambari"></a>Ambari

Apache Ambari est destiné à l'approvisionnement, à la gestion et à la surveillance des clusters Apache Hadoop. Il comprend une collection intuitive d'outils d'opérateurs et un solide jeu d'API qui masque la complexité de Hadoop, en simplifiant le fonctionnement des clusters. Consultez les pages [Gestion des clusters HDInsight avec Ambari](hdinsight-hadoop-manage-ambari.md) (Linux exclusivement), [Surveillance des clusters Hadoop dans HDInsight à l'aide des API Ambari](hdinsight-monitor-use-ambari-api.md) et <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Référence des API Apache Ambari</a>.

### <a name="avro"></a>Avro (bibliothèque Microsoft .NET pour Avro)

La bibliothèque Microsoft .NET pour Avro implémente le format compact d'échange des données binaires pour la sérialisation dans l'environnement Microsoft.NET. Il utilise <a target="_blank" href="http://www.json.org/">JavaScript Objet Notation (JSON)</a> pour définir un schéma sans langage spécifié qui assure l'interopérabilité des langages, c'est-à-dire que les données sérialisées dans un langage peuvent être lues dans un autre langage. Vous pouvez trouver des informations détaillées sur ce format dans la <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">spécification Apache Avro</a>. Le format des fichiers Avro prend en charge le modèle de programmation MapReduce distribué. Les fichiers sont « fractionnables », ce qui signifie que vous pouvez rechercher un point quelconque dans un fichier et commencer la lecture à partir d'un bloc particulier. Pour plus d'informations sur la procédure, consultez la rubrique [Sérialisation des données avec la bibliothèque Microsoft .NET pour Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

HDFS (Hadoop Distributed File System) est un système de fichiers distribués qui, avec MapReduce et YARN, constitue le cœur de l'écosystème Hadoop. HDFS est le système de fichiers standard pour les clusters Hadoop dans HDInsight.

### <a name="hive"></a>Hive et HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> est un logiciel d’entrepôt de données basé sur Hadoop qui vous permet d’interroger et de gérer des jeux de données volumineux dans un système de stockage distribué au moyen d’un langage similaire à SQL appelé HiveQL. À l'instar de Pig, Hive est une abstraction qui s'appuie sur MapReduce. Lorsque vous l’exécutez, Hive traduit des requêtes en une série de tâches MapReduce. D'un point de vue conceptuel, Hive est plus proche d'un système de gestion de base de données relationnelle que Pig et convient dès lors mieux à une utilisation avec des données plus structurées. Pour les données non structurées, Pig est le meilleur choix. Consultez la rubrique [Utilisation de Hive avec Hadoop dans HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> est une couche de gestion du stockage et des tables pour Hadoop qui offre aux utilisateurs une vue relationnelle des données. Dans HCatalog, vous pouvez lire et écrire des fichiers dans les formats pour lesquels un sérialiseur-désérialiseur Hive peut être écrit.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> est une bibliothèque évolutive d'algorithmes d'apprentissage automatique s'exécutant sur Hadoop. Au moyen de principes statistiques, les applications d'apprentissage automatique apprennent aux systèmes à exploiter les données et à utiliser les résultats passés pour déterminer le comportement futur. Consultez la rubrique [Génération de recommandations de films en utilisant Mahout dans Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce et YARN
Hadoop MapReduce est une infrastructure logicielle permettant d'écrire des applications traitant les jeux de données volumineuses en parallèle. Une tâche MapReduce fractionne les jeux de données volumineuses et organise les données en paires clé-valeur en vue de leur traitement.

Apache YARN représente la nouvelle génération de MapReduce (MapReduce 2.0 ou MRv2), qui fractionne les deux principales tâches de JobTracker (la gestion des ressources et la planification/surveillance des tâches) en entités séparées.

Pour plus d'informations sur MapReduce, consultez <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> dans le wiki Hadoop. Pour en savoir plus sur YARN, consultez <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> est un système de coordination de flux de travail qui gère les tâches Hadoop. Il est intégré à la pile Hadoop et prend en charge les tâches Hadoop pour MapReduce, Pig, Hive et Sqoop. Il peut également être utilisé pour planifier des tâches propres à un système comme des programmes Java ou des scripts shell. Consultez la rubrique [Utilisation d’un coordinateur Oozie basé sur le temps avec Hadoop](hdinsight-use-oozie-coordinator-time.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> est une couche de base de données relationnelle sur HBase. Phoenix inclut un pilote JDBC qui permet aux utilisateurs d’interroger et de gérer directement les tables SQL. Phoenix convertit les requêtes et autres instructions en appels d’API NoSQL natifs (au lieu d’utiliser MapReduce), permettant ainsi des applications plus rapides sur les magasins NoSQL. Consultez la page [Utiliser Apache Phoenix et SQuirreL avec les clusters HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> est une plateforme généraliste permettant d'effectuer des transformations MapReduce complexes dans des jeux de données très volumineux au moyen d'un langage simple de création de scripts appelé Pig Latin. Pig convertit les scripts Pig Latin pour qu'ils puissent s'exécuter dans Hadoop. Vous pouvez créer des fonctions définies par l'utilisateur pour étendre Pig Latin. Consultez la rubrique [Utilisation de Pig avec Hadoop pour analyser un fichier journal Apache](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> est un outil qui permet de transférer des données en bloc entre Hadoop et des bases de données relationnelles, telles que SQL ou d'autres banques de données structurées, avec une efficacité optimale. Consultez la rubrique [Utilisation de Sqoop avec Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> est une infrastructure d’application qui repose sur des fichiers YARN Hadoop. Elle exécute des graphiques complexes et acycliques de traitement de données générales. Elle est plus souple et puissante que l’infrastructure MapReduce qui permet aux processus gros consommateurs de données, tels que Hive, de s’exécuter plus efficacement à l’échelle. Consultez la section [Utilisation d’Apache Tez pour des performances améliorées de la rubrique Utilisation de Hive et HiveQL](hdinsight-use-hive.md#usetez).


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache Zookeeper</a> coordonne les processus dans les systèmes distribués de grande taille au moyen d'un espace de noms hiérarchique partagé de registres de données (znodes). les znodes contiennent de petites quantités de méta-informations permettant de coordonner les processus : état, emplacement, configuration, etc.

## <a name="advantage"></a>Avantages de Hadoop dans le cloud

En tant que partie intégrante de l'écosystème du cloud Azure, Hadoop dans HDInsight offre une série d'avantages :

* Approvisionnement automatique des clusters Hadoop. Il est beaucoup plus facile de créer des clusters HDInsight que de configurer manuellement des clusters Hadoop. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md).

* Composants Hadoop à la pointe de la technologie. Pour plus d’informations, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][component-versioning].

* Disponibilité et fiabilité élevées des clusters. Consultez la rubrique [Disponibilité et fiabilité des clusters Hadoop dans HDInsight](hdinsight-high-availability.md) pour plus d'informations.

* Stockage efficace et économique des données avec le stockage d'objets blob Azure, une option compatible Hadoop. Consultez la rubrique [Utilisation du stockage d’objets blob Azure avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md) pour plus d’informations.

* Intégration aux autres services Azure, y compris [Web Apps](../documentation/services/app-service/web/) et [Base de données SQL](../documentation/services/sql-database/).

* Faible coût d’entrée. Essayez une [version d'évaluation gratuite](/pricing/free-trial/) ou consultez la rubrique [Tarification HDInsight](/pricing/details/hdinsight/).


Pour plus d'informations sur les avantages de Hadoop dans HDInsight, consultez la rubrique [Page de fonctionnalités Azure pour HDInsight][marketing-page].



## <a id="resources"></a>Ressources supplémentaires sur l'analyse des données volumineuses, Hadoop et HDInsight

Créez sur cette présentation de Hadoop sur HDInsight et l'analyse de données volumineuses avec les ressources ci-dessous.


### HDInsight sur Linux (version préliminaire)

* [Prise en main de HDInsight sur Linux](hdinsight-hadoop-linux-tutorial-get-started.md) : didacticiel de démarrage rapide pour l’approvisionnement de clusters HDInsight Hadoop sous Linux et l’exécution d’exemples de requêtes Hive.

* [Approvisionnement de HDInsight sur Linux à l’aide d’options personnalisées](hdinsight-hadoop-provision-linux-clusters.md) : apprenez à configurer un cluster Hadoop HDInsight sous Linux à l’aide d’options personnalisées via le portail Azure, l’interface de ligne de commande Azure ou Azure PowerShell.

* [Utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md) : obtenez quelques conseils rapides sur l’utilisation de clusters Linux Hadoop approvisionnés sur Azure.

* [Gestion des clusters HDInsight avec Ambari](hdinsight-hadoop-manage-ambari.md) : découvrez comment gérer et contrôler votre cluster Hadoop Linux sur HDInsight à l’aide d’Ambari Web ou de l’API REST d’Ambari.


### HDInsight sur Windows

* [Documentation HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/) : page de documentation sur Azure HDInsight proposant des liens vers des articles, des vidéos et d’autres ressources.

* [Carte d’apprentissage de HDInsight](hdinsight-learn-map.md) : visite guidée de la documentation Hadoop pour HDInsight.

* [Prise en main d’Azure HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md) : didacticiel de prise en main rapide pour l’utilisation de Hadoop dans HDInsight.

* [Exécution des exemples HDInsight](hdinsight-run-samples.md) : didacticiel expliquant comment exécuter les exemples fournis avec HDInsight.

* [Kit de développement logiciel (SDK) Azure HDinsight](http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx) : documentation de référence pour le Kit de développement logiciel (SDK) HDinsight.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a> : découvrez plus en détail la bibliothèque de logiciels Apache Hadoop, infrastructure autorisant le traitement distribué de jeux de données volumineux sur des clusters d’ordinateurs.

* <a target="_blank" href="http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html">HDFS</a> : découvrez plus en détail l’architecture et la conception du système HDFS (Hadoop Distributed File System), principal système de stockage utilisé par les applications Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce Tutorial</a> : découvrez plus en détail l’infrastructure de programmation pour écrire des applications Hadoop qui autorisent un traitement rapide et en parallèle de vastes quantités de données sur des clusters de nœuds de calcul volumineux.

### Base de données SQL sur Azure

* [Base de données SQL Azure](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx) : documentation MSDN pour Base de données SQL.

* [Portail de gestion de Base de données SQL](https://msdn.microsoft.com/library/azure/dn771027.aspx) : outil de gestion de base de données léger et simple d’utilisation pour gérer Base de données SQL dans le cloud.

* [Adventure Works pour Base de données SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304) : page de téléchargement de l’exemple Base de données SQL.

### Microsoft Business Intelligence (pour HDInsight sur Windows)

Les outils décisionnels courants que sont Excel, PowerPivot, SQL Server Analysis Services et Reporting Services permettent de récupérer des données intégrées à HDInsight, de les analyser et de générer des rapports à leur sujet via le complément Power Query ou le pilote ODBC Microsoft Hive.

Ces outils décisionnels peuvent s’avérer utiles lors de vos analyses de données volumineuses :

* [Connexion d’Excel à Hadoop à l’aide de Power Query](hdinsight-connect-excel-power-query.md) : apprenez à connecter Excel au compte de stockage Azure dans lequel sont stockées les données associées à votre cluster HDInsight via Microsoft Power Query pour Excel.

* [Connexion d’Excel à Hadoop à l’aide du pilote ODBC Microsoft Hive](hdinsight-connect-excel-hive-ODBC-driver.md) : apprenez à importer des données à partir de HDInsight avec le pilote ODBC Microsoft Hive.

* [Plateforme cloud de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx) : découvrez Power BI pour Office 365, téléchargez la version d’évaluation de SQL Server, et configurez SharePoint Server 2013 et SQL Server BI.

* <a target="_blank" href="http://msdn.microsoft.com/library/hh231701.aspx">En savoir plus sur SQL Server Analysis Services</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">En savoir plus sur SQL Server Reporting Services</a>.


### Essayez les solutions Hadoop pour l'analyse de données volumineuses (pour HDInsight sur Windows)

Utilisez l’analyse de données volumineuses sur les données de votre organisation pour vous familiariser avec vos activités. Voici quelques exemples :

* [Analyse des données de capteur du système de chauffage, de ventilation et de climatisation](hdinsight-hive-analyze-sensor-data.md) : découvrez comment analyser les données de capteur au moyen de Hive avec HDInsight (Hadoop), puis visualisez les données dans Microsoft Excel. Dans cet exemple, vous utiliserez Hive pour traiter les données d’historique produites par les systèmes de chauffage, de ventilation et de climatisation, afin identifier les systèmes qui ne sont pas en mesure de maintenir de façon fiable une température donnée.

* [Utilisation de Hive avec HDInsight pour analyser les journaux de site web](hdinsight-hive-analyze-website-log.md) : découvrez comment utiliser HiveQL dans HDInsight pour analyser des fichiers journaux de site web afin d’obtenir des informations sur la fréquence des accès en une journée provenant de sites web externes, ainsi qu’un résumé des erreurs de site web rencontrées par les utilisateurs.

* [Analyse des données de capteur en temps réel avec Storm et HBase dans HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md) : découvrez comment créer une solution qui utilise un cluster Storm dans HDInsight afin de traiter des données de capteur provenant d’Azure Event Hubs, puis qui affiche les données de capteur traitées en tant qu’informations en temps quasi-réel sur un tableau de bord basé sur le web.


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
 

<!---HONumber=Sept15_HO2-->