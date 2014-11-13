<properties urlDisplayName="HDInsight HBase overview" pageTitle="Vue d'ensemble de HBase dans HDInsight | Azure" metaKeywords="" description="Pr&eacute;sentation de HBase dans HDInsight, cas d'utilisation et comparaison avec d'autres solutions de base de donn&eacute;es." metaCanonical="" services="hdinsight" documentationCenter="" title="Vue d'ensemble de HDInsight HBase" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev" />

# Vue d'ensemble de HDInsight HBase

## Qu'est-ce que HBase ?

HBase est une base de données NoSQL open source Apache basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées. Elle est inspirée du système BigTable de Google et est une base de données orientée famille de colonnes. Les données sont stockées dans les lignes d'une table et les données au sein d'une ligne sont regroupées par familles de colonnes. HBase est une base de données sans schéma dans le sens où ni les colonnes ni le type de données qui y sont stockées ne doivent être définis avant de pouvoir les utiliser. Le code open source a été publié pour la première fois par Mike Cafarella en 2007. Il peut être mis à l'échelle de façon linéaire pour gérer des pétaoctets de données dans des milliers de nœuds Il peut reposer sur la redondance des données, le traitement par lots et d'autres fonctionnalités qui sont fournies par des applications distribuées dans l'écosystème Hadoop.

## Qu'est-ce qu'Azure HDInsight HBase ?

HDInsight HBase est proposé en tant que cluster géré intégré à l'environnement Azure. Les clusters sont configurés de façon à stocker les données directement dans le stockage d'objets blob Azure, ce qui fournit une faible latence et une élasticité accrue en matière de choix performances/coût. Cela permet aux clients de créer des sites web interactifs fonctionnant avec des jeux de données volumineux, de créer des services stockant les données de capteur et de télémétrie provenant de millions de points de terminaison, et d'analyser ces données avec des tâches Hadoop. HBase et Hadoop sont de bons points de départ pour les projets présentant des données volumineuses dans Azure, et, en particulier, ils permettent à des applications en temps réel d'opérer avec des jeux de données volumineux.

La mise en œuvre de HDInsight exploite l'architecture de montée en charge de HBase pour fournir un partitionnement automatique des tables, une cohérence forte pour les lectures et les écritures, et un basculement automatique. Les performances sont optimisées par la mise en cache en mémoire des lectures et par des écritures en diffusion à débit élevé. L'approvisionnement du réseau virtuel est également disponible pour HDInsight HBase. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight sur Azure Virtual Network][Approvisionnement de clusters HDInsight sur Azure Virtual Network].

## Mode de gestion des données HDInsight HBase

Les données peuvent être gérées dans HBase au moyen des commandes `create` `get`, `put` et `scan` provenant du shell HBase. Les données sont écrites dans la base de données au moyen de `put` et lues au moyen des commandes `get`. La commande `scan` permet d'obtenir des données à partir de plusieurs lignes dans une table. Les données peuvent également être gérées au moyen de l'API HBase C#, qui fournit une bibliothèque cliente par-dessus l'API REST HBase. Une base de données HBase peut également être interrogée au moyen de Hive. Pour une introduction à ces modèles de programmation, consultez la page [Prise en main de HBase avec Hadoop dans HDInsight][Prise en main de HBase avec Hadoop dans HDInsight]. Des coprocesseurs sont également disponibles pour autoriser le traitement de données dans les nœuds hébergeant la base de données.

## Scénarios : cas d'utilisation de HBase

Le cas d'utilisation canonique pour lequel BigTable, et par extension, HBase ont été créés est les recherches web. Les moteurs de recherche créent des index qui mappent les termes avec les pages web les contenant. Mais, il y a de nombreux autres cas d'utilisation pour lesquels HBase est adapté, dont plusieurs sont répertoriés dans cette section.

### Cas d'utilisation n°1 : stockage clé-valeur

HBase peut être utilisé comme stockage clé-valeur et convient pour la gestion des systèmes de messagerie. Facebook utilise HBase pour son système de messagerie et est idéal pour le stockage et la gestion des communications Internet. WebTable utilise HBase pour rechercher et gérer des tables extraites à partir de pages web.

### Cas d'utilisation n°2 : données de capteur

HBase est utile pour la capture de données qui sont collectées de façon incrémentielle à partir de diverses sources. Cela inclut l'analytique sociale, les séries chronologiques, la mise à jour des tableaux de bord interactifs avec les tendances et les compteurs, et la gestion de systèmes de journal d'audit. Par exemple, le Bloomberg Terminal
 et la base de données OpenTSDB (Open Time Series Database) qui stocke les métriques collectées sur l'intégrité des systèmes serveur et y donne accès.

### Cas d'utilisation n°3 : requête en temps réel

[Phoenix][Phoenix] est un moteur de requête SQL pour Apache HBase. Il est accessible en tant que pilote JDBC, et permet d'interroger et de gérer les tables HBase au moyen de SQL.

### Cas d'utilisation n°4 : HBase en tant que plateforme

Les applications peuvent fonctionner par-dessus HBase, en l'utilisant comme banque de données. Exemples : Phoenix, OpenTSDB, Kiji et Titan. Les applications peuvent également s'intégrer à HBase. Exemples : Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia et Drill.

## <a name="next-steps"></a>Étapes suivantes

[Prise en main de HBase avec Hadoop dans HDInsight][Prise en main de HBase avec Hadoop dans HDInsight]

[Approvisionnement de clusters HDInsight sur Azure Virtual Network][Approvisionnement de clusters HDInsight sur Azure Virtual Network]

[Analyse de sentiments Twitter avec HBase dans HDInsight][Analyse de sentiments Twitter avec HBase dans HDInsight]

[Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)][Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)]

[Kit de développement logiciel (SDK) C# HBase][Kit de développement logiciel (SDK) C# HBase]

## <a name="see-also"></a>Voir aussi

[Apache HBase][Apache HBase]

[Bigtable : un système de stockage distribué pour les données structurées][Bigtable : un système de stockage distribué pour les données structurées]

  [Approvisionnement de clusters HDInsight sur Azure Virtual Network]: ../hdinsight-hbase-provision-vnet/
  [Prise en main de HBase avec Hadoop dans HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-hbase-get-started/
  [Phoenix]: http://phoenix.apache.org/
  [Analyse de sentiments Twitter avec HBase dans HDInsight]: ../hdinsight-hbase-analyze-twitter-sentiment/
  [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)]: ../hdinsight-hbase-build-java-maven/
  [Kit de développement logiciel (SDK) C# HBase]: https://github.com/hdinsight/hbase-sdk-for-net
  [Apache HBase]: https://hbase.apache.org/
  [Bigtable : un système de stockage distribué pour les données structurées]: http://research.google.com/archive/bigtable.html
