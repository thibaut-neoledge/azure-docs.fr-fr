<properties urlDisplayName="HDInsight Introduction" pageTitle="Pr&eacute;sentation de Hadoop dans HDInsight&nbsp;: analyse des donn&eacute;es volumineuses dans le cloud | Azure" metaKeywords="" description="D&eacute;couvrez comment Azure HDInsight utilise les clusters Apache Hadoop dans le cloud pour fournir une infrastructure logicielle permettant de g&eacute;rer et d'analyser les donn&eacute;es volumineuses, ainsi que de g&eacute;n&eacute;rer des rapports &agrave; leur sujet." metaCanonical="" services="hdinsight" documentationCenter="" title="Pr&eacute;sentation de Hadoop dans HDInsight&nbsp;: traitement et analyse des donn&eacute;es volumineuses dans le cloud" authors="cgronlun" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="cgronlun" />

# Présentation de Hadoop dans HDInsight : traitement et analyse des données volumineuses dans le cloud

Bénéficiez d'un aperçu des composants HDInsight, de la terminologie courante et des scénarios, et accédez aux ressources permettant d'utiliser Hadoop dans HDInsight.

Azure HDInsight déploie et approvisionne des clusters Apache Hadoop dans le cloud, en fournissant une infrastructure logicielle conçue pour gérer et analyser les données volumineuses, et générer des rapports à leur sujet. Hadoop Core fournit un mode de stockage de données fiable grâce au système HDFS (Hadoop Distributed File System), ainsi qu'un modèle de programmation simple MapReduce destiné à traiter et à analyser, en parallèle, les données stockées dans ce système distribué.

### Que sont les données volumineuses ?

Les données volumineuses font référence aux données qui sont collectées dans des volumes toujours plus importants, à des vitesses de plus en plus élevées et pour une variété croissante de formats non structurés et de contextes sémantiques variables.

Les données volumineuses correspondent à tout ensemble important d'informations numériques, depuis le texte d'un flux Twitter jusqu'aux informations de capteurs provenant d'équipements industriels, en passant par les informations relatives à la navigation et les achats d'un client sur un catalogue en ligne. Les données volumineuses peuvent être historiques (c'est-à-dire des données stockées) ou en temps réel (c'est-à-dire diffusées directement à partir de la source).

Pour que ces données aient une valeur sous la forme de renseignements ou d'informations exploitables, non seulement il est indispensable de poser les bonnes questions et de collecter des données en rapport avec les problématiques rencontrées, mais les données doivent également être accessibles, nettoyées, analysées, puis présentées de manière utile. C'est là que Hadoop dans HDInsight peut s'avérer utile.

## Dans cet article

Cet article offre une vue d'ensemble de Hadoop dans HDInsight, y compris :

-   **[Présentation de l'écosystème Hadoop dans HDInsight :][Présentation de l'écosystème Hadoop dans HDInsight :]** HDInsight est la solution Hadoop dans Azure, qui fournit des implémentations de Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari, etc. HDInsight peut également être intégré aux outils décisionnels tels qu'Excel, SQL Server Analysis Services et SQL Server Reporting Services.
-   **[Avantages de Hadoop dans le cloud :][Avantages de Hadoop dans le cloud :]** raisons pour lesquelles vous pouvez prendre en considération l'implémentation de Hadoop dans le cloud par HDInsight.
-   **[Solutions HDInsight pour l'analyse de données volumineuses :][Solutions HDInsight pour l'analyse de données volumineuses :]** quelques façons pratiques d'utiliser HDInsight pour répondre aux questions concernant votre organisation, depuis l'analyse des sentiments Twitter jusqu'à l'analyse de l'efficacité d'un système de chauffage, de ventilation et de climatisation.
-   **[Ressources supplémentaires sur l'analyse des données volumineuses, Hadoop et HDInsight :][Ressources supplémentaires sur l'analyse des données volumineuses, Hadoop et HDInsight :]** liens vers des informations supplémentaires.

## <a name="overview"></a>Présentation de l'écosystème Hadoop dans HDInsight

Hadoop est une pile de technologies se développant rapidement et constituant la solution pertinente pour l'analyse des données volumineuses. HDInsight est l'infrastructure pour l'implémentation de Hadoop dans le cloud Microsoft Azure.

Les clusters Hadoop dans HDInsight utilisent des versions de la distribution HDP (Hortonworks Data Platform) et de l'ensemble de composants Hadoop au sein de cette distribution. Pour plus d'informations sur les composants et leurs versions dans les clusters HDInsight, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][Nouveautés des versions de cluster Hadoop fournies par HDInsight]

Apache Hadoop est une infrastructure logicielle pour la gestion et l'analyse des données volumineuses. Apache Hadoop Core fournit un mode de stockage de données fiable grâce au système HDFS (Hadoop Distributed File System), ainsi qu'un modèle de programmation simple MapReduce destiné à traiter et à analyser, en parallèle, les données stockées dans ce système distribué. HDFS s'appuie sur la réplication de données pour pallier les problèmes de défaillances matérielles qui surviennent lors du déploiement de tels systèmes fortement distribués.

Voici les technologies Hadoop dans HDInsight :

-   **[Ambari][Ambari] :** approvisionnement, gestion et surveillance des clusters
-   **[Avro][Avro]** (bibliothèque Microsoft .NET pour Avro) : sérialisation des données pour l'environnement Microsoft .NET
-   **[HBase][HBase] :** base de données non relationnelle pour les très grandes tables
-   **[HDFS][HDFS] :** Hadoop Distributed File System
-   **[Hive][Hive] :** requêtes similaires à SQL
-   **[Mahout][Mahout] :** Machine Learning (apprentissage automatique)
-   **[MapReduce et YARN][MapReduce et YARN] :** traitement distribué et gestion des ressources
-   **[Oozie][Oozie] :** gestion de workflow
-   **[Pig][Pig] :** création de scripts simplifiée pour les transformations MapReduce
-   **[Sqoop][Sqoop] :** importation et exportation de données
-   **[Storm][Storm] :** traitement en temps réel de flux de données rapides et volumineux
-   **[Zookeeper][Zookeeper] :** coordination des processus dans les systèmes distribués

Découvrez également les **[outils décisionnels][outils décisionnels]** que vous pouvez utiliser avec HDInsight.

### <a name="ambari"></a>Ambari

Apache Ambari est destiné à l'approvisionnement, à la gestion et à la surveillance des clusters Apache Hadoop. Il comprend une collection intuitive d'outils d'opérateurs et un solide jeu d'API qui masque la complexité de Hadoop, en simplifiant le fonctionnement des clusters. Consultez les rubriques [Surveillance des clusters HDInsight à l'aide des API Ambari][Surveillance des clusters HDInsight à l'aide des API Ambari] et [Référence des API Ambari][Référence des API Ambari].

### <a name="avro"></a>Avro (bibliothèque Microsoft .NET pour Avro)

La bibliothèque Microsoft .NET pour Avro implémente le format compact d'échange des données binaires pour la sérialisation dans l'environnement Microsoft.NET. Elle utilise [JSON][JSON] pour définir un schéma sans langage spécifié qui assure l'interopérabilité des langages, c'est-à-dire que les données sérialisées dans un langage peuvent être lues dans un autre langage. Vous pouvez trouver des informations détaillées sur ce format dans la [Spécification Apache Avro][Spécification Apache Avro].
Le format des fichiers Avro prend en charge le modèle de programmation MapReduce distribué. Les fichiers sont « fractionnables », ce qui signifie que vous pouvez rechercher un point quelconque dans un fichier et commencer la lecture à partir d'un bloc particulier. Pour plus d'informations sur la procédure, consultez la rubrique [Sérialisation des données avec la bibliothèque Microsoft .NET pour Avro][Sérialisation des données avec la bibliothèque Microsoft .NET pour Avro].

### <a name="hbase"></a>HBase

[Apache HBase][Apache HBase] est une base de données non relationnelle basée sur Hadoop et conçue pour de grandes quantités de données non structurées et semi-structurées, potentiellement des milliards de lignes multipliées par des millions de colonnes. Les clusters HBase dans HDInsight sont configurés de façon à stocker les données directement dans le stockage d'objets blob Azure, avec une faible latence et une élasticité accrue. Consultez la rubrique [Présentation de HBase dans HDInsight][Présentation de HBase dans HDInsight].

### <a name="hdfs"></a>HDFS

HDFS (Hadoop Distributed File System) est un système de fichiers distribués qui, avec MapReduce et YARN, constitue le cœur de l'écosystème Hadoop. HDFS est le système de fichiers standard pour les clusters Hadoop dans HDInsight.

### <a name="hive"></a>Hive

[Apache Hive][Apache Hive] est un logiciel d'entrepôt de données basé sur Hadoop qui vous permet d'interroger et de gérer des jeux de données volumineux dans un système de stockage distribué au moyen d'un langage similaire à SQL appelé HiveQL. À l'instar de Pig, Hive est une abstraction qui s'appuie sur MapReduce et qui, lorsqu'elle est exécutée, transforme les requêtes en une série de tâches MapReduce. D'un point de vue conceptuel, Hive est plus proche d'un système de gestion de base de données relationnelle que Pig et convient dès lors mieux à une utilisation avec des données plus structurées. Pour les données non structurées, Pig est un choix plus judicieux. Consultez la rubrique [Utilisation de Hive avec Hadoop dans HDInsight][Utilisation de Hive avec Hadoop dans HDInsight]

### <a name="mahout"></a>Mahout

[Apache Mahout][Apache Mahout] est une bibliothèque évolutive d'algorithmes d'apprentissage automatique s'exécutant sur Hadoop. Au moyen de principes statistiques, les applications d'apprentissage automatique apprennent aux systèmes à exploiter les données et à utiliser les résultats passés pour déterminer le comportement futur. Consultez la rubrique [Génération de recommandations de films en utilisant Mahout dans Hadoop][Génération de recommandations de films en utilisant Mahout dans Hadoop].

### <a name="mapreduce"></a>MapReduce et YARN

Hadoop MapReduce est une infrastructure logicielle permettant d'écrire des applications traitant les jeux de données volumineux en parallèle. Une tâche MapReduce fractionne les jeux de données volumineux et organise les données en paires clé-valeur en vue de leur traitement.

Apache YARN représente la nouvelle génération de MapReduce (MapReduce 2.0 ou MRv2), qui fractionne les deux principales tâches de JobTracker (la gestion des ressources et la planification/surveillance des tâches) en entités séparées.

Pour plus d'informations sur MapReduce, consultez la rubrique [MapReduce][MapReduce] sur le Wiki Hadoop. Pour plus d'informations sur YARN, consultez la rubrique [Apache Hadoop NextGen MapReduce (YARN)][Apache Hadoop NextGen MapReduce (YARN)].

### <a name="oozie"></a>Oozie

[Apache Oozie][Apache Oozie] est un système de coordination de workflow qui gère les tâches Hadoop. Il est intégré à la pile Hadoop et prend en charge les tâches Hadoop pour MapReduce, Pig, Hive et Sqoop. Il peut également être utilisé pour planifier des tâches propres à un système comme des programmes Java ou des scripts shell. Consultez la rubrique [Utilisation du coordinateur Oozie basé sur le temps avec HDInsight][Utilisation du coordinateur Oozie basé sur le temps avec HDInsight].

### <a name="pig"></a>Pig

[Apache Pig][Apache Pig] est une plateforme généraliste permettant d'effectuer des transformations MapReduce complexes dans des jeux de données très volumineux au moyen d'un langage simple de création de scripts appelé Pig Latin. Pig convertit les scripts Pig Latin pour qu'ils puissent s'exécuter dans Hadoop. Vous pouvez créer des fonctions définies par l'utilisateur pour étendre Pig Latin. Consultez la rubrique [Utilisation de Pig avec Hadoop pour analyser un fichier journal Apache][Utilisation de Pig avec Hadoop pour analyser un fichier journal Apache].

### <a name="sqoop"></a>Sqoop

[Apache Sqoop][Apache Sqoop] est un outil qui permet de transférer des données en bloc entre Hadoop et des bases de données relationnelles, telles que SQL ou d'autres banques de données structurées, avec une efficacité optimale. Consultez la rubrique [Utilisation de Sqoop avec Hadoop][Utilisation de Sqoop avec Hadoop].

### <a name="storm"></a>Storm

[Apache Storm][Apache Storm] est un système de calcul distribué et en temps réel permettant le traitement rapide de vastes flux de données. Storm est fourni en tant que cluster géré dans HDInsight. Consultez la rubrique [Analyse de données de capteur en temps réel au moyen de Storm et de Hadoop][Analyse de données de capteur en temps réel au moyen de Storm et de Hadoop].

### <a name="zookeeper"></a>Zookeeper

[Apache Zookeeper][Apache Zookeeper] coordonne les processus dans les systèmes distribués de grande taille au moyen d'un espace de noms hiérarchique partagé de registres de données (znodes). Les znodes contiennent de petites quantités de méta-informations permettant de coordonner les processus : statut, emplacement, configuration, etc.

### <a name="bi"></a>Outils décisionnels

Les outils décisionnels courants que sont Excel, PowerPivot, SQL Server Analysis Services et Reporting Services permettent de récupérer des données intégrées à HDInsight, de les analyser et de générer des rapports à leur sujet via le complément Power Query ou le pilote ODBC Microsoft Hive.

Ces outils décisionnels peuvent s'avérer utiles lors de vos analyses de données volumineuses :

-   [Téléchargement de Microsoft Power Query pour Excel][Téléchargement de Microsoft Power Query pour Excel]
-   [Téléchargement du pilote ODBC Microsoft Hive][Téléchargement du pilote ODBC Microsoft Hive]
-   [En savoir plus sur SQL Server Analysis Services][En savoir plus sur SQL Server Analysis Services]
-   [En savoir plus sur SQL Server Reporting Services][En savoir plus sur SQL Server Reporting Services]

## <a name="advantage"></a>Avantages de Hadoop dans le cloud

En tant que partie intégrante de l'écosystème du cloud Azure, Hadoop dans HDInsight offre une série d'avantages :

-   Composants Hadoop à la pointe de la technologie. Pour plus d'informations, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][Nouveautés des versions de cluster Hadoop fournies par HDInsight]
-   Disponibilité et fiabilité élevées des clusters. Consultez la rubrique [Disponibilité et fiabilité des clusters Hadoop dans HDInsight][Disponibilité et fiabilité des clusters Hadoop dans HDInsight] pour plus d'informations.
-   Stockage efficace et économique des données avec le stockage d'objets blob Azure, une option compatible Hadoop. Consultez la rubrique [Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight][Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight] pour plus d'informations.
-   Intégration aux autres services Azure, y compris [Sites Web][Sites Web] et [Base de données SQL][Base de données SQL].
-   Faible coût d'entrée. Essayez une [version d'évaluation gratuite][version d'évaluation gratuite] ou consultez la rubrique [Tarification HDInsight][Tarification HDInsight].

Pour plus d'informations sur les avantages de Hadoop dans HDInsight, consultez la rubrique [Page de fonctionnalités Azure pour HDInsight][Page de fonctionnalités Azure pour HDInsight].

## <a name="solutions"></a>Essai de solutions HDInsight pour l'analyse de données volumineuses

Analysez les données de votre organisation pour vous familiariser avec vos activités. Voici quelques exemples :

-   [Analyse des données de capteur du système de chauffage, de ventilation et de climatisation][Analyse des données de capteur du système de chauffage, de ventilation et de climatisation] : découvrez comment analyser les données de capteur au moyen de Hive avec HDInsight (Hadoop), puis visualiser les données dans Microsoft Excel. Dans cet exemple, vous utiliserez Hive pour traiter les données d'historique produites par les systèmes de chauffage, de ventilation et de climatisation, afin identifier les systèmes qui ne sont pas en mesure de maintenir de façon fiable une température donnée.
-   [Utilisation de Hive avec HDInsight pour analyser les journaux de site web][Utilisation de Hive avec HDInsight pour analyser les journaux de site web]: découvrez comment utiliser HiveQL dans HDInsight pour analyser des fichiers journaux de site web afin d'obtenir des informations sur la fréquence des accès en une journée provenant de sites web externes, ainsi qu'un résumé des erreurs de site web rencontrées par les utilisateurs.
-   [Analyse des données de capteur en temps réel avec Storm et HBase dans HDInsight (Hadoop)][Analyse de données de capteur en temps réel au moyen de Storm et de Hadoop] : découvrez comment créer une solution qui utilise un cluster Storm dans HDInsight afin de traiter des données de capteur provenant de concentrateurs d'événements Azure, puis qui affiche les données de capteur traitées en tant qu'informations en temps quasi-réel sur un tableau de bord basé sur le web.

Pour essayer Hadoop dans HDInsight, consultez les articles « Prise en main » dans la section Explorer de la [page de documentation HDInsight][page de documentation HDInsight]. Pour essayer des exemples plus sophistiqués, accédez à la section Analyser plus bas.

## <a name="resources"></a>Ressources supplémentaires sur l'analyse des données volumineuses, Hadoop et HDInsight

### Hadoop dans HDInsight

-   [Documentation HDInsight][page de documentation HDInsight] : page de documentation sur Azure HDInsight proposant des liens vers des articles, des vidéos et d'autres ressources.

-   [Carte d'apprentissage pour Hadoop dans HDInsight][Carte d'apprentissage pour Hadoop dans HDInsight] : visite guidée de la documentation Hadoop pour HDInsight.

-   [Prise en main d'Azure HDInsight][Prise en main d'Azure HDInsight] : didacticiel de prise en main rapide pour l'utilisation de Hadoop dans HDInsight.

-   [Exécution des exemples HDInsight][Exécution des exemples HDInsight] : didacticiel expliquant comment exécuter les exemples fournis avec HDInsight.

-   [Kit de développement logiciel (SDK) Azure HDInsight][Kit de développement logiciel (SDK) Azure HDInsight] : documentation de référence pour le Kit de développement logiciel (SDK) HDinsight.

### Base de données SQL sur Azure

-   [Base de données SQL Azure][Base de données SQL Azure] : documentation MSDN pour la base de données SQL.

-   [Portail de gestion pour la base de données SQL][Portail de gestion pour la base de données SQL] : outil de gestion de base de données léger et simple d'utilisation pour gérer la base de données SQL dans le cloud.

-   [Adventure Works pour la base de données SQL][Adventure Works pour la base de données SQL] : page de téléchargement de l'exemple de base de données SQL.

### Décisionnel Microsoft

-   [Connexion d'Excel à Hadoop à l'aide de Power Query][Connexion d'Excel à Hadoop à l'aide de Power Query] : apprenez à connecter Excel au compte de stockage Azure dans lequel sont stockées les données associées à votre cluster HDInsight via Microsoft Power Query pour Excel.

-   [Connexion d'Excel à Hadoop à l'aide du pilote ODBC Microsoft Hive][Connexion d'Excel à Hadoop à l'aide du pilote ODBC Microsoft Hive] : apprenez à importer des données à partir de HDInsight avec le pilote ODBC Microsoft Hive.

-   [Décisionnel Microsoft][Décisionnel Microsoft] : découvrez Power BI pour Office 365, téléchargez la version d'évaluation de SQL Server, et configurez SharePoint Server 2013 et SQL Server BI.

### Apache Hadoop

-   [Apache Hadoop][Apache Hadoop] : découvrez plus en détail la bibliothèque de logiciels Apache Hadoop, infrastructure autorisant le traitement distribué de jeux de données volumineux sur des clusters d'ordinateurs.

-   [HDFS][1] : découvrez plus en détail l'architecture et la conception du système HDFS (Hadoop Distributed File System), principal système de stockage utilisé par les applications Hadoop.

-   [MapReduce][2] : découvrez plus en détail l'infrastructure de programmation pour écrire des applications Hadoop qui autorisent un traitement rapide et en parallèle de vastes quantités de données sur des clusters de nœuds de calcul volumineux.

  [Présentation de l'écosystème Hadoop dans HDInsight :]: #overview
  [Avantages de Hadoop dans le cloud :]: #advantage
  [Solutions HDInsight pour l'analyse de données volumineuses :]: #solutions
  [Ressources supplémentaires sur l'analyse des données volumineuses, Hadoop et HDInsight :]: #resources
  [Nouveautés des versions de cluster Hadoop fournies par HDInsight]: ../hdinsight-component-versioning/
  [Ambari]: #ambari
  [Avro]: #avro
  [HBase]: #hbase
  [HDFS]: #hdfs
  [Hive]: #hive
  [Mahout]: #mahout
  [MapReduce et YARN]: #mapreduce
  [Oozie]: #oozie
  [Pig]: #pig
  [Sqoop]: #sqoop
  [Storm]: #storm
  [Zookeeper]: #zookeeper
  [outils décisionnels]: #bi
  [Surveillance des clusters HDInsight à l'aide des API Ambari]: ../hdinsight-monitor-use-ambari-api/
  [Référence des API Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [JSON]: http://www.json.org/
  [Spécification Apache Avro]: http://avro.apache.org/docs/current/spec.html
  [Sérialisation des données avec la bibliothèque Microsoft .NET pour Avro]: ../hdinsight-dotnet-avro-serialization/
  [Apache HBase]: http://hbase.apache.org/
  [Présentation de HBase dans HDInsight]: ../hdinsight-hbase-overview/
  [Apache Hive]: http://hive.apache.org/
  [Utilisation de Hive avec Hadoop dans HDInsight]: ../hdinsight-use-hive/
  [Apache Mahout]: https://mahout.apache.org/
  [Génération de recommandations de films en utilisant Mahout dans Hadoop]: ../hdinsight-mahout/
  [MapReduce]: http://wiki.apache.org/hadoop/MapReduce
  [Apache Hadoop NextGen MapReduce (YARN)]: http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html
  [Apache Oozie]: http://oozie.apache.org/
  [Utilisation du coordinateur Oozie basé sur le temps avec HDInsight]: ../hdinsight-use-oozie-coordinator-time/
  [Apache Pig]: http://pig.apache.org/
  [Utilisation de Pig avec Hadoop pour analyser un fichier journal Apache]: ../hdinsight-use-pig/
  [Apache Sqoop]: http://sqoop.apache.org/
  [Utilisation de Sqoop avec Hadoop]: ../hdinsight-use-sqoop/
  [Apache Storm]: https://storm.incubator.apache.org/
  [Analyse de données de capteur en temps réel au moyen de Storm et de Hadoop]: ../hdinsight-storm-sensor-data-analysis/
  [Apache Zookeeper]: http://zookeeper.apache.org/
  [Téléchargement de Microsoft Power Query pour Excel]: http://www.microsoft.com/fr-fr/download/details.aspx?id=39379
  [Téléchargement du pilote ODBC Microsoft Hive]: http://www.microsoft.com/fr-fr/download/details.aspx?id=40886
  [En savoir plus sur SQL Server Analysis Services]: http://www.microsoft.com/fr-fr/server-cloud/solutions/business-intelligence/analysis.aspx
  [En savoir plus sur SQL Server Reporting Services]: http://msdn.microsoft.com/fr-fr/library/ms159106.aspx
  [Disponibilité et fiabilité des clusters Hadoop dans HDInsight]: ../hdinsight-high-availability/
  [Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight]: ../hdinsight-use-blob-storage/
  [Sites Web]: ../documentation/services/websites/
  [Base de données SQL]: ../documentation/services/sql-database/
  [version d'évaluation gratuite]: /pricing/free-trial/
  [Tarification HDInsight]: ../pricing/details/hdinsight/
  [Page de fonctionnalités Azure pour HDInsight]: ../services/hdinsight/
  [Analyse des données de capteur du système de chauffage, de ventilation et de climatisation]: ../hdinsight-hive-analyze-sensor-data/
  [Utilisation de Hive avec HDInsight pour analyser les journaux de site web]: ../hdinsight-hive-analyze-website-log/
  [page de documentation HDInsight]: ../documentation/services/hdinsight/
  [Carte d'apprentissage pour Hadoop dans HDInsight]: ../hdinsight-learn-map
  [Prise en main d'Azure HDInsight]: ../hdinsight-get-started/
  [Exécution des exemples HDInsight]: ../hdinsight-run-samples/
  [Kit de développement logiciel (SDK) Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/fr-fr/library/dn479185.aspx
  [Base de données SQL Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336279.aspx
  [Portail de gestion pour la base de données SQL]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg442309.aspx
  [Adventure Works pour la base de données SQL]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [Connexion d'Excel à Hadoop à l'aide de Power Query]: ../hdinsight-connect-excel-power-query/
  [Connexion d'Excel à Hadoop à l'aide du pilote ODBC Microsoft Hive]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Décisionnel Microsoft]: http://www.microsoft.com/fr-fr/server-cloud/solutions/business-intelligence/default.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
  [1]: http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html
  [2]: http://mapreduce.org/
