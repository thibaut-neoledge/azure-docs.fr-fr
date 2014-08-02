<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in the cluster versions provided by HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

Nouveautés des versions de cluster fournies par HDInsight
=========================================================

Azure HDInsight prend désormais en charge Hadoop 2.2 avec les clusters HDinsight version 3.0 et tire pleinement profit de cette plateforme pour offrir aux clients un ensemble d'avantages considérables. Ces derniers incluent notamment :

-   Hive : amélioration considérable des temps de réponse des requêtes Hive (jusqu'à 40 fois plus rapides) et de la compression de données (jusqu'à 80 %) avec le format ORC (Optimized Row Columnar).

-   YARN : une nouvelle infrastructure de gestion d'application généraliste et distribuée qui a remplacé la classique infrastructure Apache Hadoop MapReduce pour le traitement des données dans les clusters Hadoop. Elle est efficace en tant que système d'exploitation Hadoop. Hadoop est ainsi passé d'une plateforme de données à usage unique pour le traitement par lots à une plateforme à usage multiple qui permet le traitement par lots, interactif, en ligne et par flux. Cette nouvelle infrastructure de gestion améliore l'extensibilité et l'utilisation des clusters en fonction de critères tels que les garanties de capacité, l'équité et les contrats de niveau de service.

-   Pig, Sqoop, Qozie, Ambari : mises à niveau des versions des composants pour le cluster HDinsight version 3.0 (HDP 2.0) qui fournissent la parité avec le cluster HDinsight version 2.1 (HDP 1.3). Pour des précisions, consultez les tableaux de versions ci-dessous. Notez que Hbase, Mahout et Flume ne sont pas inclus.

**Déploiement** 
La création de clusters HDInsight 3.0 sur Hadoop 2.2 est prise en charge par le portail Azure, le Kit de développement logiciel (SDK) HDinsight et Azure PowerShell.

**Disponibilité globale** 
Avec le lancement d'Azure HDInsight sur Hadoop 2.2, Microsoft a fait en sorte que HDInsight soit disponible dans les principales régions Azure, à l'exception de la Chine élargie. Plus précisément, l'Europe de l'Ouest et le Sud-Est asiatique ont été mis en ligne. Cela permet aux clients de rechercher des clusters dans un centre de données proche et, potentiellement, dans une zone avec des exigences de conformité similaires.

**Dernières modifications** 
Seule la syntaxe « wasb:// » est prise en charge dans les clusters HDInsight 3.0. L'ancienne syntaxe « asv:// » est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais pas dans les clusters HDInsight 3.0, et elle ne sera pas prise en charge dans les versions ultérieures. Cela signifie que toutes les tâches envoyées vers un cluster HDInsight 3.0 utilisant explicitement la syntaxe « asv:// » échoueront. Vous devez plutôt utiliser la syntaxe wasb://. De même, les tâches créées avec un metastore existant contenant des références explicites aux ressources utilisant la syntaxe asv:// et envoyées vers un cluster HDInsight 3.0 échoueront également. Vous devrez recréer ces metastores en utilisant la syntaxe wasb:// pour adresser les ressources.

Versions de HDInsight
---------------------

HDInsight prend en charge plusieurs versions de cluster Hadoop qui peuvent être déployées à tout moment. Le choix d'une version approvisionne une version spécifique de la distribution de la plateforme de données HortonWorks et un ensemble de composants qui sont contenus dans cette distribution.

### Cluster version 3.0

Azure HDInsight prend désormais en charge Hadoop 2.2. Le cluster version 3.0 est basé sur la plateforme de données Hortonworks version 2.0 et fournit des services Hadoop avec les versions de composant répertoriées dans le tableau suivant :

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Composant</th><th data-morhtml="true">Version</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">2.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.12.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.12</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.4</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">4.0.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">Fusionn&eacute; avec Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">Fusionn&eacute; avec Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Ambari</td><td data-morhtml="true">API v1.0</td></tr>
</table>

### Cluster version 2.1

La version de cluster par défaut utilisée par [Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) est la version 2.1. Elle est basée sur la plateforme de données Hortonworks version 1.3.0 et fournit des services Hadoop avec les versions de composant répertoriées dans le tableau suivant :

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Composant</th><th data-morhtml="true">Version</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">1.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.11.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.11</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">3.2.2</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">Fusionn&eacute; avec Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">Fusionn&eacute; avec Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Ambari</td><td data-morhtml="true">API v1.0</td></tr>
</table>

### Cluster version 1.6

Le cluster [Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) version 1.6 est également disponible. Il est basé sur la plateforme de données Hortonworks version 1.1.0 et fournit des services Hadoop avec les versions des composants répertoriées dans le tableau suivant :

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Composant</th><th data-morhtml="true">Version</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">1.0.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.9.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.9.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.2</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">3.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">0.4.1</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">0.1.4</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Pilote JDBC pour SQL Server</td><td data-morhtml="true">3.0</td></tr>
</table>

### Sélectionnez une version lors de l'approvisionnement d'un cluster HDInsight

Lorsque vous créez un cluster par l'intermédiaire des cmdlets PowerShell HDInsight ou du Kit de développement logiciel (SDK) .NET HDInsight, vous pouvez choisir la version du cluster Hadoop HDInsight avec le paramètre « Version ».

Si vous utilisez l'option **Création rapide**, vous obtenez la version 2.1 du cluster Hadoop HDInsight par défaut. Si vous utilisez l'option **Création personnalisée** à partir du portail Azure, vous pouvez choisir la version du cluster que vous allez déployer à partir du menu déroulant **Version HDInsight** sur la page **Détails du cluster**. La version 3.0 du cluster Hadoop HDInsight est disponible seulement en tant qu'option dans l'Assistant **Création personnalisée**.

![HDI.Versioning.VersionScreen](./media/hdinsight-component-versioning/hdi-versioning-version-screen.png)

Versions prises en charge
-------------------------

Le tableau suivant répertorie les versions de HDInsight actuellement disponibles, les versions correspondantes de la plateforme de données Hortonworks (HDP) utilisées par ces dernières, ainsi que leurs dates de lancement. Leurs dates de dépréciation, si elles sont connues, sont également indiquées.

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Version de HDInsight</th><th data-morhtml="true">Version de HDP</th><th data-morhtml="true">Date de lancement</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 3.0</td><td data-morhtml="true">HDP 2.0</td><td data-morhtml="true">02/11/2014</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 2.1</td><td data-morhtml="true">HDP 1.3</td><td data-morhtml="true">10/28/2013</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 1.6</td><td data-morhtml="true">HDP 1.1</td><td data-morhtml="true">10/28/2013</td></tr>
</table>

### Le contrat de niveau de service pour les versions de cluster HDInsight

Le contrat de niveau de service est défini en termes de « fenêtre de support ». Une fenêtre de support désigne la période pendant laquelle une version de cluster HDInsight est prise en charge par le support technique Microsoft. Un cluster HDInsight est en dehors de la fenêtre de support si la date d'expiration du support correspondant à la version de ce cluster est postérieure à la date courante. Le tableau ci-dessus contient une liste des versions de cluster HDInsight prises en charge. La date d'expiration du support pour une version donnée de HDInsight (désignée comme la version X) est calculée comme suit, la date la plus tardive prévalant :

-   Formule 1 : ajoutez 180 jours à la date de lancement du cluster HDInsight version X.
-   Formule 2 : ajoutez 90 jours à la date à laquelle le cluster HDInsight version X+1 (la version qui suit la version X) est mis à disposition dans le portail de gestion Azure.

> [WACOM.NOTE] HDInsight 2.1 et 3.0 utilisent tous deux la version 64 bits de Windows 2008 R2 SP1 avec .NET Framework 4.0.

**Remarques et informations supplémentaires sur le contrôle de version**

-   Le pilote JDBC pour SQL Server est utilisé en interne par HDInsight et n'est pas employé pour les opérations externes. Si vous voulez vous connecter à HDInsight avec ODBC, utilisez le pilote ODBC Hive de Microsoft. Pour plus d'informations sur l'utilisation d'ODBC Hive, consultez la page [Connexion d'Excel à HDInsight avec le pilote ODBC Hive de Microsoft](/en-us/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver).

-   Le cluster HDInsight version 3.0 utilise une distribution Hadoop basée sur la [plateforme de données Hortonworks 2.0](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html).

-   Le cluster HDInsight version 2.1 utilise une distribution Hadoop basée sur la [plateforme de données Hortonworks 1.3](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html). Il s'agit du cluster Hadoop par défaut créé lors de l'utilisation du portail Azure HDInsight.

-   Le cluster HDInsight version 1.6 utilise une distribution Hadoop basée sur la [plateforme de données Hortonworks 1.1](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html).

-   Les versions de composant associées aux versions de cluster HDInsight sont susceptibles de changer dans les futures mises à jour de HDInsight. Pour déterminer quels sont les composants disponibles et vérifier quelles versions sont utilisées pour un cluster, vous pouvez utiliser l'API REST Ambari. La commande GetComponentInformation permet de récupérer des informations sur un composant du service. Pour des détails, consultez la [documentation Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md). Vous pouvez également obtenir ces informations en vous connectant à un cluster à l'aide du Bureau à distance et en examinant directement le contenu du répertoire « C:\\apps\\dist ».


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://www.windowsazure.com/en-us/support/forums/

[connect-excel-with-hive-ODBC]: /en-us/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
