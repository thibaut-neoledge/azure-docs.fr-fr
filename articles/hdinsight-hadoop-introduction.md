<properties 
	pageTitle="Présentation de Hadoop dans HDInsight : analyse des données volumineuses dans le cloud | Azure" 
	description="Découvrez comment Azure HDInsight utilise les clusters Apache Hadoop dans le cloud pour fournir une infrastructure logicielle permettant de gérer et d'analyser les données volumineuses, ainsi que de générer des rapports à leur sujet." 
	services="hdinsight" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="cgronlun"/>



# Présentation de Hadoop dans HDInsight : traitement et analyse des données volumineuses dans le cloud

Bénéficiez d'un aperçu des composants HDInsight, de la terminologie courante et des scénarios, et accédez aux ressources permettant d'utiliser Hadoop dans HDInsight.

Azure HDInsight déploie et approvisionne des clusters Apache Hadoop dans le cloud, en fournissant une infrastructure logicielle conçue pour gérer et analyser les données volumineuses, et générer des rapports à leur sujet. Hadoop Core fournit un mode de stockage de données fiable grâce au système HDFS (Hadoop Distributed File System), ainsi qu'un modèle de programmation simple MapReduce destiné à traiter et à analyser, en parallèle, les données stockées dans ce système distribué. 

### Que sont les données volumineuses ?
Les données volumineuses font référence aux données qui sont collectées dans des volumes toujours plus importants, à des vitesses de plus en plus élevées et pour une variété croissante de formats non structurés et de contextes sémantiques variables. 

Les données volumineuses correspondent à tout ensemble important d'informations numériques, depuis le texte d'un flux Twitter jusqu'aux informations de capteurs provenant d'équipements industriels, en passant par les informations relatives à la navigation et les achats d'un client sur un catalogue en ligne. Les données volumineuses peuvent être historiques (c'est-à-dire des données stockées) ou en temps réel (c'est-à-dire diffusées directement à partir de la source). 

Pour que ces données aient une valeur sous la forme de renseignements ou d'informations exploitables, non seulement il est indispensable de poser les bonnes questions et de collecter des données en rapport avec les problématiques rencontrées, mais les données doivent également être accessibles, nettoyées, analysées, puis présentées de manière utile. C'est là que Hadoop dans HDInsight peut s'avérer utile.


## Dans cet article

Cet article offre une vue d'ensemble de Hadoop dans HDInsight, y compris :

* **[Présentation de l'écosystème Hadoop dans HDInsight :](#overview)**  HDInsight est la solution Hadoop dans Azure, qui fournit des implémentations de Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari, etc. HDInsight peut également être intégré aux outils décisionnels tels qu'Excel, SQL Server Analysis Services et SQL Server Reporting Services.
* **[Avantages de Hadoop dans le cloud :](#advantage)** raisons pour lesquelles vous pouvez prendre en considération l'implémentation de Hadoop dans le cloud par HDInsight.
* **[Solutions HDInsight pour l'analyse de données volumineuses :](#solutions)** quelques façons pratiques d'utiliser HDInsight pour répondre aux questions concernant votre organisation, depuis l'analyse des sentiments Twitter jusqu'à l'analyse de l'efficacité d'un système de chauffage, de ventilation et de climatisation.
* **[Ressources supplémentaires sur l'analyse des données volumineuses, Hadoop et HDInsight :](#resources)** liens vers des informations supplémentaires.


## <a name="overview"></a>Présentation de l'écosystème Hadoop dans HDInsight

Hadoop est une pile de technologies se développant rapidement et constituant la solution pertinente pour l'analyse des données volumineuses. HDInsight est l'infrastructure pour l'implémentation de Hadoop dans le cloud Microsoft Azure.
 
Les clusters Hadoop dans HDInsight utilisent des versions de la distribution HDP (Hortonworks Data Platform) et de l'ensemble de composants Hadoop au sein de cette distribution. Pour plus d'informations sur les composants et leurs versions dans les clusters HDInsight, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][component-versioning].

Apache Hadoop est une infrastructure logicielle pour la gestion et l'analyse des données volumineuses. Apache Hadoop Core fournit un mode de stockage de données fiable grâce au système HDFS (Hadoop Distributed File System), ainsi qu'un modèle de programmation simple MapReduce destiné à traiter et à analyser, en parallèle, les données stockées dans ce système distribué. HDFS s'appuie sur la réplication de données pour pallier les problèmes de défaillances matérielles qui surviennent lors du déploiement de tels systèmes fortement distribués.

Voici les technologies Hadoop dans HDInsight :

* **[Ambari](#ambari):** approvisionnement, gestion et surveillance des clusters 
* **[Avro](#avro)** (Microsoft .NET Library pour Avro) : sérialisation des données pour l'environnement Microsoft .NET 
* **[HBase](#hbase):** base de données non relationnelle pour les très grandes tables
* **[HDFS](#hdfs):** Hadoop Distributed File System
* **[Hive](#hive):** requêtes similaires à SQL
* **[Mahout](#mahout):** Machine Learning (apprentissage automatique)
* **[MapReduce et YARN](#mapreduce):** traitement distribué et gestion des ressources
* **[Oozie](#oozie):** gestion de workflow
* **[Pig](#pig):** création de scripts simplifiée pour les transformations MapReduce
* **[Sqoop](#sqoop):** importation et exportation de données 
* **[Storm](#storm):** traitement en temps réel de flux de données rapides et volumineux
* **[Zookeeper](#zookeeper):** coordination des processus dans les systèmes distribués
 
Découvrez également les **[outils Business Intelligence (BI)](#bi)** que vous pouvez utiliser avec HDInsight.

###<a name="ambari"></a>Ambari

Apache Ambari est destiné à l'approvisionnement, à la gestion et à la surveillance des clusters Apache Hadoop. Il comprend une collection intuitive d'outils d'opérateurs et un solide jeu d'API qui masque la complexité de Hadoop, en simplifiant le fonctionnement des clusters. Consultez les rubriques  [Surveillance des clusters Hadoop dans HDInsight à l'aide des API Ambari](../hdinsight-monitor-use-ambari-api/) et <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Référence des API Ambari</a>.

### <a name="avro"></a>Avro (bibliothèque Microsoft .NET pour Avro)

La bibliothèque Microsoft .NET pour Avro implémente le format compact d'échange des données binaires pour la sérialisation dans l'environnement Microsoft.NET. Il utilise <a target="_blank" href="http://www.json.org/">JSON</a> pour définir un schéma sans langage spécifié qui assure l'interopérabilité des langages, c'est-à-dire que les données sérialisées dans un langage peuvent être lues dans un autre langage. Vous pouvez trouver des informations détaillées sur ce format dans la <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">spécification Apache Avro</a>. 
Le format des fichiers Avro prend en charge le modèle de programmation MapReduce distribué. Les fichiers sont " fractionnables ", ce qui signifie que vous pouvez rechercher un point quelconque dans un fichier et commencer la lecture à partir d'un bloc particulier. Pour plus d'informations sur la procédure, consultez la rubrique [Sérialisation des données avec la bibliothèque Microsoft .NET pour Avro](../hdinsight-dotnet-avro-serialization/).

### <a name="hbase"></a>HBase

<a target="_blank" href="http://hbase.apache.org/">Apache HBase</a> est une base de données non relationnelle basée sur Hadoop et conçue pour de grandes quantités de données non structurées et semi-structurées, potentiellement des milliards de lignes multipliées par des millions de colonnes. Les clusters HBase dans HDInsight sont configurés de façon à stocker les données directement dans le stockage d'objets blob Azure, avec une faible latence et une élasticité accrue. Consultez la rubrique [Présentation de HBase dans HDInsight](../hdinsight-hbase-overview/).

### <a name="hdfs"></a>HDFS

HDFS (Hadoop Distributed File System) est un système de fichiers distribués qui, avec MapReduce et YARN, constitue le cœur de l'écosystème Hadoop. HDFS est le système de fichiers standard pour les clusters Hadoop dans HDInsight.

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> est un logiciel d'entrepôt de données basé sur Hadoop qui vous permet d'interroger et de gérer des jeux de données volumineux dans un système de stockage distribué au moyen d'un langage similaire à SQL appelé HiveQL. À l'instar de Pig, Hive est une abstraction qui s'appuie sur MapReduce et qui, lorsqu'elle est exécutée, transforme les requêtes en une série de tâches MapReduce. D'un point de vue conceptuel, Hive est plus proche d'un système de gestion de base de données relationnelle que Pig et convient dès lors mieux à une utilisation avec des données plus structurées. Pour les données non structurées, Pig est un choix plus judicieux. Consultez la rubrique [Utilisation de Hive avec Hadoop dans HDInsight](../hdinsight-use-hive/).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> est une bibliothèque évolutive d'algorithmes d'apprentissage automatique s'exécutant sur Hadoop. Au moyen de principes statistiques, les applications d'apprentissage automatique apprennent aux systèmes à exploiter les données et à utiliser les résultats passés pour déterminer le comportement futur. Consultez la rubrique [Génération de recommandations de films en utilisant Mahout dans Hadoop](../hdinsight-mahout/).

### <a name="mapreduce"></a>MapReduce et YARN
Hadoop MapReduce est une infrastructure logicielle permettant d'écrire des applications traitant les jeux de données volumineux en parallèle. Une tâche MapReduce fractionne les jeux de données volumineux et organise les données en paires clé-valeur en vue de leur traitement. 

Apache YARN représente la nouvelle génération de MapReduce (MapReduce 2.0 ou MRv2), qui fractionne les deux principales tâches de JobTracker (la gestion des ressources et la planification/surveillance des tâches) en entités séparées.

Pour plus d'informations sur MapReduce, consultez <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> dans le wiki Hadoop. Pour en savoir plus sur YARN, consultez <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> est un système de coordination de workflow qui gère les tâches Hadoop. Il est intégré à la pile Hadoop et prend en charge les tâches Hadoop pour MapReduce, Pig, Hive et Sqoop. Il peut également être utilisé pour planifier des tâches propres à un système comme des programmes Java ou des scripts shell. Consultez la rubrique [Utilisation d'un coordinateur Oozie basé sur le temps avec Hadoop dans HDInsight](../hdinsight-use-oozie-coordinator-time/).

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> est une plateforme de haut niveau permettant d'effectuer des transformations MapReduce complexes dans des jeux de données très volumineux au moyen d'un langage simple de création de scripts appelé Pig Latin. Pig convertit les scripts Pig Latin pour qu'ils puissent s'exécuter dans Hadoop. Vous pouvez créer des fonctions définies par l'utilisateur pour étendre Pig Latin. Consultez la rubrique [Utilisation de Pig avec Hadoop pour analyser un fichier journal Apache](../hdinsight-use-pig/).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> est un outil qui permet de transférer des données en bloc entre Hadoop et des bases de données relationnelles, telles que SQL ou d'autres banques de données structurées, avec une efficacité optimale. Consultez la rubrique [Utilisation de Sqoop avec Hadoop](../hdinsight-use-sqoop/).

### <a name="storm"></a>Storm
<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a> est un système de calcul distribué et en temps réel permettant le traitement rapide de vastes flux de données. Storm est fourni en tant que cluster géré dans HDInsight. Consultez la rubrique [Analyse de données de capteur en temps réel au moyen de Storm et de Hadoop](../hdinsight-storm-sensor-data-analysis/).

### <a name="zookeeper"></a>Zookeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache Zookeeper</a> coordonne les processus dans les systèmes distribués de grande taille au moyen d'un espace de noms hiérarchique partagé de registres de données (znodes). les znodes contiennent de petites quantités de méta-informations permettant de coordonner les processus : état, emplacement, configuration, etc. 

### <a name="bi"></a>Outils décisionnels 
Les outils décisionnels courants que sont Excel, PowerPivot, SQL Server Analysis Services et Reporting Services permettent de récupérer des données intégrées à HDInsight, de les analyser et de générer des rapports à leur sujet via le complément Power Query ou le pilote ODBC Microsoft Hive. 

Ces outils décisionnels peuvent s'avérer utiles lors de vos analyses de données volumineuses :

* <a target="_blank" href="http://www.microsoft.com/fr-fr/download/details.aspx?id=39379">Téléchargement de Microsoft Power Query pour Excel</a> 
* <a target="_blank" href="http://www.microsoft.com/fr-fr/download/details.aspx?id=40886">Téléchargement du pilote ODBC Microsoft Hive</a>
* <a target="_blank" href="http://www.microsoft.com/fr-fr/server-cloud/solutions/business-intelligence/analysis.aspx">En savoir plus sur SQL Server Analysis Services</a>
* <a target="_blank" href="http://msdn.microsoft.com/fr-fr/library/ms159106.aspx">En savoir plus sur SQL Server Reporting Services</a>

## <a name="advantage"></a>Avantages de Hadoop dans le cloud

En tant que partie intégrante de l'écosystème du cloud Azure, Hadoop dans HDInsight offre une série d'avantages :

* Composants Hadoop à la pointe de la technologie. Pour plus d'informations, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][component-versioning].
* Disponibilité et fiabilité élevées des clusters. Pour plus d'informations, consultez la rubrique [Disponibilité et fiabilité des clusters Hadoop dans HDInsight](../hdinsight-high-availability/).
* Stockage efficace et économique des données avec le stockage d'objets blob Azure, une option compatible Hadoop. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec Hadoop dans HDInsight](../hdinsight-use-blob-storage/).
* Intégration aux autres services Azure, y compris [Sites Web](../documentation/services/websites/) et [Base de données SQL](../documentation/services/sql-database/).
* Faible coût d'entrée. Essayez une [version d'évaluation gratuite](/pricing/free-trial/) ou consultez la rubrique [Tarification HDInsight](../pricing/details/hdinsight/).

Pour plus d'informations sur les avantages de Hadoop dans HDInsight, consultez la rubrique [Page de fonctionnalités Azure pour HDInsight][marketing-page].

## <a name="solutions"></a>Essai de solutions HDInsight pour l'analyse de données volumineuses

Analysez les données de votre organisation pour vous familiariser avec vos activités. Voici quelques exemples : 

* [Analyse des données de capteur du système de chauffage, de ventilation et de climatisation](../hdinsight-hive-analyze-sensor-data/) : découvrez comment analyser les données de capteur au moyen de Hive avec HDInsight (Hadoop), puis visualiser les données dans Microsoft Excel. Dans cet exemple, vous utiliserez Hive pour traiter les données d'historique produites par les systèmes de chauffage, de ventilation et de climatisation, afin identifier les systèmes qui ne sont pas en mesure de maintenir de façon fiable une température donnée.
* [Utilisation de Hive avec HDInsight pour analyser les journaux de site web](../hdinsight-hive-analyze-website-log/) : découvrez comment utiliser HiveQL dans HDInsight pour analyser des fichiers journaux de site web afin d'obtenir des informations sur la fréquence des accès en une journée provenant de sites web externes, ainsi qu'un résumé des erreurs de site web rencontrées par les utilisateurs.
* [Analyse des données de capteur en temps réel avec Storm et HBase dans HDInsight (Hadoop)](../hdinsight-storm-sensor-data-analysis/) : découvrez comment créer une solution qui utilise un cluster Storm dans HDInsight afin de traiter des données de capteur provenant de concentrateurs d'événements Azure, puis qui affiche les données de capteur traitées en tant qu'informations en temps quasi-réel sur un tableau de bord basé sur le web.

Pour essayer Hadoop dans HDInsight, consultez les articles " Prise en main " dans la section Explorer de la [page de documentation HDInsight](../documentation/services/hdinsight/). Pour essayer des exemples plus sophistiqués, accédez à la section Analyser plus bas.



## <a name="resources"></a>Ressources supplémentaires sur l'analyse des données volumineuses, Hadoop et HDInsight 

### Hadoop dans HDInsight	

* [Documentation HDInsight](../documentation/services/hdinsight/) : page de documentation sur Azure HDInsight proposant des liens vers des articles, des vidéos et d'autres ressources.

* [Carte d'apprentissage pour Hadoop dans HDInsight](../hdinsight-learn-map) : visite guidée de la documentation Hadoop pour HDInsight.

* [Prise en main d'Azure HDInsight](../hdinsight-get-started/): didacticiel de prise en main rapide pour l'utilisation de Hadoop dans HDInsight.

* [Exécution des exemples HDInsight](../hdinsight-run-samples/): didacticiel expliquant comment exécuter les exemples fournis avec HDInsight.
	
* [Kit de développement logiciel (SDK) Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/fr-fr/library/dn479185.aspx) : documentation de référence pour le Kit de développement logiciel (SDK) HDinsight.


### Base de données SQL sur Azure	
		
* [Base de données SQL Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336279.aspx) : documentation MSDN pour la base de données SQL.
	
* [Portail de gestion de la base de données SQL](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg442309.aspx) : outil de gestion de base de données léger et simple d'utilisation pour gérer la base de données SQL dans le cloud.

* [Adventure Works pour la base de données SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304) : page de téléchargement de l'exemple de base de données SQL.	

### Décisionnel Microsoft		

* [Connexion d'Excel à Hadoop à l'aide de Power Query](../hdinsight-connect-excel-power-query/) : apprenez à connecter Excel au compte de stockage Azure dans lequel sont stockées les données associées à votre cluster HDInsight via Microsoft Power Query pour Excel. 

* [Connexion d'Excel à Hadoop à l'aide du pilote ODBC Microsoft Hive](../hdinsight-connect-excel-hive-ODBC-driver/): apprenez à importer des données à partir de HDInsight avec le pilote ODBC Microsoft Hive.

* [Microsoft Business Intelligence (BI)](http://www.microsoft.com/fr-fr/server-cloud/solutions/business-intelligence/default.aspx) : découvrez Power BI pour Office 365, téléchargez la version d'évaluation de SQL Server, et configurez SharePoint Server 2013 et SQL Server BI.
			

### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: découvrez plus en détail la bibliothèque de logiciels Apache Hadoop, infrastructure autorisant le traitement distribué de jeux de données volumineux sur des clusters d'ordinateurs.	

*  <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html">HDFS</a>: découvrez plus en détail l'architecture et la conception du système HDFS (Hadoop Distributed File System), principal système de stockage utilisé par les applications Hadoop.		

* <a target="_blank" href="http://mapreduce.org/">MapReduce</a>: découvrez plus en détail l'infrastructure de programmation pour écrire des applications Hadoop qui autorisent un traitement rapide et en parallèle de vastes quantités de données sur des clusters de nœuds de calcul volumineux.	


[marketing-page]: ../services/hdinsight/
[component-versioning]: ../hdinsight-component-versioning/

[zookeeper]: http://zookeeper.apache.org/ 
<!--HONumber=42-->
