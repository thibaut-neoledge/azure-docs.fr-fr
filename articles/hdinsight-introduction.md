<properties linkid="manage-services-hdinsight-introduction-hdinsight" urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Azure HDInsight | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Azure HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Présentation d'Azure HDInsight
==============================

Vue d'ensemble
--------------

Azure HDInsight est un service qui déploie et met en service les clusters Apache Hadoop® dans le cloud, en fournissant une infrastructure logicielle conçue pour gérer et analyser les données volumineuses, et générer des rapports à leur sujet.

### Données volumineuses (« Big Data »)

Les données sont décrites comme étant « volumineuses » pour indiquer qu'elles sont collectées dans des volumes toujours plus importants, à des vitesses de plus en plus élevées et pour une variété croissante de formats non structurés et de contextes sémantiques variables. La collecte de données volumineuses ne présente en soi aucun intérêt pour l'entreprise. Pour que ces données aient une valeur sous la forme de renseignements ou d'informations exploitables, non seulement il est indispensable de poser les bonnes questions et de collecter des données en rapport avec les problématiques rencontrées, mais les données doivent également être accessibles, nettoyées, analysées, puis présentées de manière utile, souvent en association avec des données issues de diverses autres sources qui offrent une perspective et un contexte dans ce que l'on appelle désormais une application Web hybride.

### Apache Hadoop

Apache Hadoop est une infrastructure logicielle qui facilite la gestion et l'analyse des données volumineuses. Apache Hadoop Core fournit un mode de stockage de données fiable grâce au système HDFS (Hadoop Distributed File System), ainsi qu'un modèle de programmation simple MapReduce destiné à traiter et à analyser, en parallèle, les données stockées dans ce système distribué. HDFS s'appuie sur la réplication de données pour pallier les problèmes de défaillances matérielles qui surviennent lors du déploiement de tels systèmes fortement distribués.

### MapReduce

Pour simplifier la complexité de l'analyse de données non structurées issues de diverses sources, le modèle de programmation MapReduce fournit une abstraction de base qui garantit l'aboutissement des opérations de mappage et de réduction. Il considère toutes ses tâches comme des calculs réalisés sur des jeux de données constitués de paires clé-valeur. Ainsi, les fichiers d'entrée et de sortie doivent contenir des jeux de données constitués uniquement de paires clé-valeur. De cette contrainte, il ressort essentiellement que les tâches MapReduce peuvent être composées.

Les autres projets liés à Hadoop, tels que Pig et Hive, s'appuient sur HDFS et l'infrastructure MapReduce. Les projets de ce type permettent de gérer un cluster de manière plus simple qu'en utilisant directement les programmes MapReduce. Par exemple, Pig permet d'écrire des programmes dans un langage procédural appelé Pig Latin avant de les compiler en programmes MapReduce sur le cluster. Il procure également des contrôles courants pour gérer le flux de données. Hive est une infrastructure d'entrepôt de données qui fournit une abstraction de table pour les données contenues dans les fichiers stockés dans un cluster qui peut alors être interrogé à l'aide d'instructions comparables à SQL dans un langage déclaratif appelé HiveQL.

### HDInsight

Azure HDInsight rend Apache Hadoop disponible en tant que service dans le cloud. L'infrastructure logicielle HDFS/MapReduce et les projets associés, tels que Pig et Hive, deviennent ainsi disponibles dans un environnement plus simple, plus évolutif et plus économique.

L'un des principaux avantages de HDInsight réside dans sa façon de gérer et de stocker les données. HDInsight utilise le stockage d'objets blob Azure comme système de fichiers par défaut. Le stockage d'objets blob et HDFS sont des systèmes de fichiers distincts qui sont optimisés, respectivement, pour le stockage de données et pour les calculs réalisés à partir de ces données.

-   Le stockage d'objets blob Azure constitue une option de stockage à long terme économique, partageable, hautement évolutive et disponible pour les données qui doivent être traitées à l'aide de HDInsight.
-   Les clusters Hadoop déployés par HDInsight sur HDFS sont optimisés pour l'exécution des tâches de calcul MapReduce sur les données.

Les clusters HDInsight sont déployés dans Azure sur des nœuds de calcul pour exécuter les tâches MapReduce et peuvent être supprimés par les utilisateurs une fois ces tâches terminées. Conserver les données dans les clusters HDFS une fois les calculs terminés serait une façon onéreuse de stocker ces données. Le stockage d'objets blob est une solution de stockage Azure fiable et généraliste. Ainsi, en stockant les données dans le stockage d'objets blob, les clusters ayant servi aux calculs peuvent être supprimés sans risque de perte de données utilisateur. Or, le stockage d'objets blob n'est pas qu'une solution économique : son interface complète de système de fichiers HDFS offre aux clients une expérience transparente en permettant à l'ensemble complet de composants de l'écosystème Hadoop de fonctionner (par défaut) directement sur les données qu'il gère.

HDInsight utilise Azure PowerShell pour assurer la configuration, l'exécution et le post-traitement des tâches Hadoop. HDInsight fournit également un connecteur Sqoop qui permet d'importer les données d'une base de données SQL Azure vers HDFS ou d'exporter des données vers une base de données SQL Azure à partir de HDFS.

Microsoft Power Query pour Excel est disponible pour l'importation de données dans Excel à partir d'Azure HDInsight ou d'un système HDFS. Ce complément améliore l'expérience du décisionnel en libre-service dans Excel en simplifiant la découverte de données et l'accès à une grande diversité de sources de données. Outre Power Query, le pilote ODBC Microsoft Hive permet d'intégrer des outils décisionnels (BI), tels que Excel, SQL Server Analysis Services et Reporting Services, ce qui facilite et simplifie l'analyse de données de bout en bout.

### Description

Cette rubrique décrit l'écosystème Hadoop pris en charge par HDInsight, les principaux scénarios d'utilisation de HDInsight et propose des liens vers d'autres ressources. Elle se compose des sections suivantes :

-   [Écosystème Hadoop sous HDInsight](#Ecosystem) : HDInsight fournit des implémentations de Pig, Hive et Sqoop, et prend en charge d'autres outils décisionnels, tels que Microsoft Excel, SQL Server Analysis Services et Reporting Services, qui sont intégrés au stockage d'objets blob/à HDFS et à l'infrastructure MapReduce via Power Query ou le pilote ODBC Microsoft Hive. Cette section décrit les tâches qui sont traitées spécifiquement par ces programmes de l'écosystème Hadoop.

-   [Scénarios de données volumineuses pour HDInsight](#Scenarios) : cette section répond à la question suivante : pour quels types de tâches la technologie HDInsight est-elle appropriée ?

-   [Ressources relatives à HDInsight](#Resources) : cette section indique où trouver des ressources utiles pour obtenir des informations supplémentaires.

<h2 id="ecosystem"> <a name="Ecosystem">Écosystème Hadoop sur Azure </a></h2>

### Introduction

HDInsight offre une infrastructure qui implémente la solution cloud de Microsoft pour le traitement de données volumineuses. Cet écosystème fédéré gère et analyse les grandes quantités de données, exploitant les capacités de traitement parallèle du modèle de programmation MapReduce. Les technologies Hadoop compatibles Apache qui peuvent être utilisées avec HDInsight sont répertoriées et décrites brièvement dans cette section.

HDInsight fournit des implémentations de Hive et Pig pour intégrer des capacités de traitement et d'entreposage de données. La solution de données volumineuses de Microsoft s'intègre aux outils décisionnels de Microsoft, tels que SQL Server Analysis Services, Reporting Services, PowerPivot et Excel. Cela vous permet de pratiquer un décisionnel simple à partir des données stockées et gérées par HDInsight dans le stockage d'objets blob.

Les autres technologies compatibles Apache et les technologies sœurs qui font partie de l'écosystème Hadoop et qui ont été conçues pour s'exécuter sur des clusters Hadoop peuvent également être téléchargées et utilisées avec HDInsight. Il s'agit notamment des technologies open source comme Sqoop qui intègrent HDFS aux banques de données relationnelles.

### Pig

Pig est une plateforme généraliste conçue pour traiter les données volumineuses sur les clusters Hadoop. Pig est à la fois un langage de flux de données appelé Pig Latin qui prend en charge l'écriture de requêtes sur les jeux de données volumineux, et un environnement d'exécution exécutant des programmes à partir d'une console. Les programmes Pig Latin se composent d'une série de transformations de jeux de données convertie discrètement en une série de programmes MapReduce. Les abstractions Pig Latin offrent des structures de données plus riches que MapReduce et sont pour Hadoop ce qu'est SQL pour les systèmes de gestion de base de données relationnelle (SGBDR). Pig Latin est entièrement extensible. Des fonctions définies par l'utilisateur (UDF), écrites en Java, Python, Ruby, C\# ou JavaScript, peuvent être appelées pour personnaliser chaque étape du chemin de traitement lors de la composition de l'analyse. Pour plus d'informations, consultez la [page d'accueil d'Apache Pig](http://pig.apache.org/).

### Hive

Hive est un entrepôt de données distribuées qui permet de gérer les données stockées dans un système HDFS. Il s'agit du moteur d'interrogation Hadoop. Hive, dont l'interface et le modèle de données relationnelles s'apparentent à ceux de SQL, s'adresse aux analystes rompus à SQL. Hive utilise un langage appelé HiveQL, qui est une variante de SQL. À l'instar de Pig, Hive est une abstraction qui s'appuie sur MapReduce et qui, lorsqu'elle est exécutée, transforme les requêtes en une série de tâches MapReduce. Les scénarios pour Hive étant plus proches dans l'esprit des scénarios SGBDR, il est recommandé de les utiliser avec des données plus structurées. Pour les données non structurées, Pig est un choix plus judicieux. Pour plus d'informations, consultez la [page d'accueil d'Apache Hive](http://hive.apache.org/).

### Sqoop

Sqoop est un outil qui permet de transférer des données en bloc entre Hadoop et des bases de données relationnelles, telles que SQL ou d'autres banques de données structurées, avec une efficacité optimale. Utilisez Sqoop pour importer des données issues de banques de données structurées externes dans HDFS ou des systèmes analogues, comme Hive. Sqoop peut également extraire des données de Hadoop et les exporter vers des bases de données relationnelles externes, des entrepôts de données d'entreprise ou tout autre type de banque de données structurées. Pour plus d'informations, consultez le site Web [-Apache Sqoop](http://sqoop.apache.org/).

### Connecteurs et outils décisionnels

Les outils décisionnels courants que sont Excel, PowerPivot, SQL Server Analysis Services et Reporting Services permettent de récupérer des données intégrées à HDInsight, de les analyser et de générer des rapports à leur sujet via le complément Power Query ou le pilote ODBC Microsoft Hive.

-   Microsoft Power Query pour Excel peut être téléchargé depuis le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkID=286689).

-   Le pilote ODBC Microsoft Hive peut être téléchargé depuis ce [site de téléchargement](http://go.microsoft.com/fwlink/?LinkID=286698).

-   Pour plus d'informations sur Analysis Services, consultez la page [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx).

-   Pour plus d'informations sur Reporting Services, consultez la page [SQL Server 2012 Reporting](http://www.microsoft.com/en-us/sqlserver/solutions-technologies/business-intelligence/reporting.aspx).

Scénarios de données volumineuses pour HDInsight
------------------------------------------------

Le scénario-type d'utilisation de HDInsight est une analyse ad hoc par lots pratiquée sur un jeu de données non structurées complet stocké sur des nœuds Azure, qui ne nécessite pas de mises à jour fréquentes.

Ces conditions s'appliquent à une large gamme d'activités dans les domaines de l'entreprise, de la science et de l'administration publique. Par exemple, ces activités peuvent consister dans la surveillance des chaînes d'approvisionnement dans le commerce de détail, des opérations financières suspectes, des caractéristiques de la demande pour les services publics, de la qualité de l'air et de l'eau mesurée par une multitude de capteurs et des tendances de la criminalité dans les zones urbaines.

HDInsight (et les technologies Hadoop en général) est particulièrement indiqué pour le traitement de grandes quantités de données consignées ou archivées, qui n'ont pas besoin d'être fréquemment mises à jour après avoir été écrites et qui sont souvent lues, généralement pour mener à bien des analyses complètes. Ce scénario se prête bien aux données plus adaptées à un traitement SGBDR qui nécessite moins de données (volumes se comptant en gigaoctets et non en pétaoctets) et qui ne doivent pas être constamment mises à jour ou faire l'objet de requêtes visant à rechercher des points de données dans le jeu de données complet. Les systèmes RDBMS fonctionnent mieux avec des données structurées, organisées et stockées selon un schéma fixe. MapReduce fonctionne correctement avec les données non structurées sans aucun schéma prédéfini, car il est capable d'interpréter ces données pendant le traitement.

Étapes suivantes : ressources relatives à HDInsight
---------------------------------------------------

**Microsoft : HDInsight**

-   [Documentation HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) : page de documentation sur Azure HDInsight proposant des liens vers des articles, des vidéos et d'autres ressources.

-   [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) : didacticiel permettant de prendre rapidement en main HDInsight.

-   [Exécution des exemples HDInsight](/en-us/manage/services/hdinsight/howto-run-samples/) : didacticiel expliquant comment exécuter les exemples fournis avec HDInsight.

-   [Données volumineuses et Azure](http://www.windowsazure.com/en-us/home/scenarios/big-data/) : scénarios de données volumineuses explorant les diverses possibilités de création offertes par Azure.

-   [Kit de développement logiciel (SDK) Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx) : documentation de référence pour le Kit de développement logiciel (SDK) HDinsight.

**Microsoft : Windows et base de données SQL**

-   [Page d'accueil Azure](https://www.windowsazure.com/en-us/) : scénarios, inscription pour une évaluation gratuite, outils de développement et documentation nécessaires pour se lancer dans la création d'applications.

-   [Base de données SQL Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx) : documentation MSDN pour la base de données SQL.

-   [Portail de gestion pour la base de données SQL](http://msdn.microsoft.com/en-us/library/windowsazure/gg442309.aspx) : outil de gestion de base de données léger et simple d'utilisation pour gérer la base de données SQL dans le cloud.

-   [Adventure Works pour la base de données SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304) : page de téléchargement de l'exemple de base de données SQL.

**Microsoft : décisionnel**

-   [Connexion d'Excel à HDInsight à l'aide de Power Query](/en-us/manage/services/hdinsight/connect-excel-with-power-query/) : apprenez à connecter Excel au compte de stockage Azure dans lequel sont stockées les données associées à votre cluster HDInsight via Microsoft Power Query pour Excel.

-   [Connexion d'Excel à HDInsight à l'aide du pilote ODBC Microsoft Hive](/en-us/manage/services/hdinsight/connect-excel-with-hive-ODBC/) : apprenez à importer des données à partir d'Azure HDInsight avec le pilote ODBC Microsoft Hive.

-   [Microsoft BI PowerPivot](http://www.microsoft.com/en-us/bi/PowerPivot.aspx) : téléchargez et obtenez des informations sur une application Web hybride et un puissant outil d'exploration de données.

-   [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx) : téléchargez une copie d'évaluation de SQL Server 2012 et apprenez à créer des solutions analytiques complètes à l'échelle de l'entreprise qui fournissent des informations exploitables.

-   [SQL Server 2012 Reporting](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-reporting-services.aspx) : téléchargez une copie d'évaluation de SQL Server 2012 et apprenez à créer des solutions complètes hautement évolutives qui permettent de prendre des décisions en temps réel à l'échelle de l'entreprise.

**Apache Hadoop** :

-   [Apache Hadoop](http://hadoop.apache.org/) : découvrez plus en détail la bibliothèque de logiciels Apache Hadoop, infrastructure autorisant le traitement distribué de jeux de données volumineux sur des clusters d'ordinateurs.

-   [HDFS](http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html) : découvrez plus en détail l'architecture et la conception du système HDFS (Hadoop Distributed File System), principal système de stockage utilisé par les applications Hadoop.

-   [MapReduce](http://mapreduce.org/) : découvrez plus en détail l'infrastructure de programmation pour écrire des applications Hadoop qui autorisent un traitement rapide et en parallèle de vastes quantités de données sur des clusters de nœuds de calcul volumineux.


