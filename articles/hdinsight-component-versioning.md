<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in Hadoop cluster versions of HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Nouveautés des versions de cluster Hadoop fournies par HDInsight

## Versions de HDInsight

HDInsight prend en charge plusieurs versions de cluster Hadoop qui peuvent être déployées à tout moment. Le choix d'une version approvisionne une version spécifique de la distribution de la plateforme de données Hortonworks et un ensemble de composants qui sont contenus dans cette distribution. Les versions de composants associées à chaque version de cluster HDInsight sont répertoriées dans le tableau suivant. Notez que la version de cluster par défaut actuellement utilisée par [Azure HDInsight][Azure HDInsight] est la version 3.1 basée sur HDP 2.1.

<table border="1">
<tr>
<th>Composant</th>
<th>HDInsight Version 3.1 (par défaut)</th>
<th>HDInsight Version 3,0</th>
<th>HDInsight Version 2.1</th>
<th>HDInsight Version 1.6</th>
</tr>
<tr>
<td>Hortonworks Data Platform (HDP)</td>
<td>2.1</td>
<td>2.0</td>
<td>1.3</td>
<td>1.1</td>
</tr>
<tr>
<td>Apache Hadoop et YARN</td>
<td>2.4.0</td>
<td>2.2.0</td>
<td>1.2.0</td>
<td>1.0.3</td>
</tr>
<tr>
<td>Tez</td>
<td>0.4.0</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>Apache Pig</td>
<td>0.12.1</td>
<td>0.12.0</td>
<td>0.11.0</td>
<td>0.9.3</td>
</tr>
<tr>
<td>Apache Hive et HCatalog</td>
<td>0.13.0</td>
<td>0.12.0</td>
<td>0.11.0</td>
<td>0.9.0</td>
</tr>
<tr>
<td>HBase</td>
<td>0.98.0</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>Apache Sqoop</td>
<td>1.4.4</td>
<td>1.4.4</td>
<td>1.4.3</td>
<td>1.4.2</td>
</tr>
<tr>
<td>Apache Oozie</td>
<td>4.0.0</td>
<td>4.0.0</td>
<td>3.3.2</td>
<td>3.2.0</td>
</tr>
<tr>
<td>Apache HCatalog</td>
<td>Fusionné avec Hive</td>
<td>Fusionné avec Hive</td>
<td>Fusionné avec Hive</td>
<td>0.4.1</td>
</tr>
<tr>
<td>Apache Templeton</td>
<td>Fusionné avec Hive</td>
<td>Fusionné avec Hive</td>
<td>Fusionné avec Hive</td>
<td>0.1.4</td>
</tr>
<tr>
<td>Ambari</td>
<td>1.5.1</td>
<td>1.4.1</td>
<td>API v1.0</td>
<td></td>
</tr>
<tr>
<td>Zookeeper</td>
<td>3.4.5</td>
<td>3.4.5</td>
<td></td>
<td></td>
</tr>
<tr>
<td>Mahout</td>
<td>0.9.0</td>
<td></td>
<td></td>
<td></td>
</tr>
</table>

**Obtention d'informations sur les versions actuelles des composants**

Les versions de composant associées aux versions de cluster HDInsight sont susceptibles de changer dans les futures mises à jour de HDInsight. Pour déterminer quels sont les composants disponibles et vérifier quelles versions sont utilisées pour un cluster, vous pouvez utiliser l'API REST Ambari. La commande **GetComponentInformation** permet de récupérer des informations sur un composant du service. Pour des détails, consultez la [documentation Ambari][documentation Ambari]. Vous pouvez également obtenir ces informations en vous connectant à un cluster à l'aide du Bureau à distance et en examinant directement le contenu du répertoire « C:\\apps\\dist ».

**Notes de publication**

Pour d'autres notes de publication sur les dernières versions de HDInsight, consultez la page [Notes de publication de HDInsight][Notes de publication de HDInsight].

### Sélectionnez une version lors de l'approvisionnement d'un cluster HDInsight

Lorsque vous créez un cluster par l'intermédiaire des cmdlets PowerShell HDInsight ou du Kit de développement logiciel (SDK) .NET HDInsight, vous pouvez choisir la version du cluster Hadoop HDInsight avec le paramètre « Version ».

Si vous utilisez l'option **Création rapide**, vous obtenez la version 3.1 de HDInsight qui crée un cluster Hadoop par défaut. Si vous utilisez l'option **Création personnalisée** à partir du portail Azure, vous pouvez choisir la version du cluster que vous allez déployer à partir du menu déroulant **Version HDInsight** sur la page **Détails du cluster**.

## Présentation des fonctionnalités

Azure HDInsight prend désormais en charge Hadoop 2.4 avec la version 3.1 du cluster HDInsight (avec Hortonworks Data Platform 2.1) par défaut. Il tire pleinement profit de cette plateforme pour offrir aux clients un ensemble d'avantages considérables. Ces derniers incluent notamment :

-   **HBase** : HBase est une base de données NoSQL à faible latence qui permet un traitement transactionnel en ligne des données volumineuses. HBase est proposé en tant que cluster géré intégré à l'environnement Azure. Les clusters sont configurés de façon à stocker les données directement dans le stockage d'objets blob Azure, ce qui fournit une faible latence et une élasticité accrue en matière de choix performances/coût. Cela permet aux clients de créer des sites web interactifs fonctionnant avec des jeux de données volumineux, de créer des services stockant les données de capteur et de télémétrie provenant de millions de points de terminaison, et d'analyser ces données avec des tâches Hadoop.

-   **Tableau de bord** : nouvelle application web déployée vers votre cluster HDInsight. Utilisez-la pour exécuter des requêtes Hive, vérifier les journaux des tâches et parcourir le stockage d'objets blob Azure. L'URL utilisée pour accéder à l'application web est \<*Nom\_cluster*\>.azurehdinsight.net.

-   **Microsoft Avro Library** : cette bibliothèque met en œuvre le système de sérialisation des données Apache Avro pour l'environnement Microsoft.NET. Apache Avro fournit un format compact d'échange des données binaires pour la sérialisation. Il utilise JSON pour définir un schéma sans langage spécifié qui assure l'interopérabilité des langages. Les données sérialisées dans un langage peuvent être lues dans un autre langage. Les langages C, C++, C#, Java, PHP, Python et Ruby sont actuellement pris en charge. Le format de sérialisation Apache Avro est largement utilisé dans Azure HDInsight pour représenter des structures de données complexes dans une tâche Hadoop MapReduce.

-   **YARN** : une nouvelle infrastructure de gestion d'application généraliste et distribuée qui a remplacé la classique infrastructure Apache Hadoop MapReduce pour le traitement des données dans les clusters Hadoop. Elle est efficace en tant que système d'exploitation Hadoop. Hadoop est ainsi passé d'une plateforme de données à usage unique pour le traitement par lots à une plateforme à usage multiple qui permet le traitement par lots, interactif, en ligne et par flux. Cette nouvelle infrastructure de gestion améliore l'extensibilité et l'utilisation des clusters en fonction de critères tels que les garanties de capacité, l'équité et les contrats de niveau de service.

-   **Tez (HDInsight 3.1 uniquement)** : infrastructure généraliste et personnalisable qui crée des tâches de traitement de données simplifiées avec des charges de travail à petite et à grande échelle dans Hadoop. Elle offre la possibilité d'exécuter un graphique non cyclique dirigé (DAG) complexe de tâches pour un seul travail, ce qui permet aux projets de l'écosystème Apache Hadoop, tels que Apache Hive et Apache Pig, de satisfaire aux exigences en termes de temps de réponse humains interactifs et de débit extrême à l'échelle du péta-octet. Notez que Hive 0.13 permet aux requêtes Hive de s'exécuter par-dessus Tez, plutôt que sur MapReduce.

-   **Haute disponibilité** : Un second nœud principal a été ajouté aux clusters Hadoop déployés par HDInsight pour améliorer la disponibilité du service. Les implémentations standard des clusters Hadoop ont normalement un seul nœud principal. HDInsight élimine ce point de défaillance unique avec l'ajout d'un second nœud principal. Le basculement vers une nouvelle configuration de cluster Haute disponibilité n'a aucune incidence sur le prix du cluster, sauf si le client approvisionne des clusters avec un nœud principal de très grande taille.

-   **Performances de Hive** : amélioration considérable des temps de réponse des requêtes Hive (jusqu'à 40 fois plus rapides) et de la compression de données (jusqu'à 80 %) avec le format **ORC (Optimized Row Columnar)**.

-   **Pig, Sqoop, Qozie, Ambari** : mises à niveau des versions des composants pour le cluster HDinsight version 3.0 (HDP 2.0/Hadoop 2.2) qui fournissent la parité avec le cluster HDinsight version 2.1 (HDP 1.3/Hadoop 1.2). Pour des précisions, consultez les tableaux de versions ci-dessous.

-   **Mahout** : cette bibliothèque d'algorithmes à apprentissage automatique évolutifs est préinstallée sur les clusters Hadoop HDInsight 3.1. Vous pouvez donc exécuter des tâches Mahout sans avoir besoin d'une configuration de cluster supplémentaire.

## Versions prises en charge

Le tableau suivant répertorie les versions de HDInsight actuellement disponibles, les versions correspondantes de la plateforme de données Hortonworks (HDP) utilisées par ces dernières, ainsi que leurs dates de lancement. La date d'expiration du support et la date d'obsolescence sont également indiquées. Notez les points suivants :

-   Seuls les clusters à haute disponibilité avec deux nœuds principaux sont déployés par défaut pour les clusters HDInsight 2.1, 3.0 et 3.1. Ils ne sont pas disponibles pour les clusters HDInsight 1.6.
-   Une fois que le support a expiré pour une version particulière, il se peut qu'il ne soit plus disponible par l'intermédiaire du portail de gestion Azure. Le tableau suivant indique quelles versions sont disponibles sur le portail de gestion Azure. Les versions des clusters resteront disponibles avec le paramètre `Version` de la commande [New-AzureHDInsightCluster][New-AzureHDInsightCluster] PowerShell et le Kit de développement logiciel (SDK) .NET jusqu'à leur date d'obsolescence.

<table border="1">
<tr>
<th>
Version de HDInsight

</th>
<th>
Version de HDP</a>

<th>
Haute disponibilité

</th>
</th>
<th>
Date de lancement

</th>
<th>
Disponible sur le portail de gestion Azure

</th>
<th>
Date d'expiration du support

</th>
<th>
Date d'obsolescence

</th>
</tr>
<tr>
<td>
HDI 3,1

</td>
<td>
HDP 2,1

</td>
<td>
Oui

</td>
<td>
</td>
<td>
Oui

</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td>
HDI 3,0

</td>
<td>
HDP 2,0

</td>
<td>
Oui

</td>
<td>
02/11/2014

</td>
<td>
Oui

</td>
<td>
09/17/2014

</td>
<td>
06/30/2015

</td>
</tr>
<tr>
<td>
HDI 2,1

</td>
<td>
HDP 1,3

</td>
<td>
Oui

</td>
<td>
10/28/2013

</td>
<td>
Non

</td>
<td>
05/12/2014

</td>
<td>
05/31/2015

</td>
</tr>
<tr>
<td>
HDI 1.6

</td>
<td>
HDP 1.1

</td>
<td>
Non

</td>
<td>
10/28/2013

</td>
<td>
Non

</td>
<td>
04/26/2014

</td>
<td>
05/31/2015

</td>
</tr>
</table>
**Déploiement des clusters autres que les clusters par défaut**

La création de clusters HDInsight 3.1 sur Hadoop 2.4 est prise en charge par le portail Azure, le Kit de développement logiciel (SDK) HDinsight et Azure PowerShell. Notez que les clusters HDInsight 3.1 sont créés par défaut sur Hadoop 2.4. Les utilisateurs doivent donc utiliser l'option **Création personnalisée** du portail pour spécifier d'autres versions du cluster HDInsight si elles doivent être créées.

### Le contrat de niveau de service pour les versions de cluster HDInsight

Le contrat de niveau de service est défini en termes de « fenêtre de support ». Une fenêtre de support désigne la période pendant laquelle une version de cluster HDInsight est prise en charge par le support technique Microsoft. Un cluster HDInsight est en dehors de la fenêtre de support si la **Date d'expiration du support** correspondant à la version de ce cluster est postérieure à la date actuelle. Le tableau ci-dessus contient une liste des versions de cluster HDInsight prises en charge. La date d'expiration du support pour une version donnée de HDInsight (lorsqu'une version X+1 plus récente est disponible) est calculée comme suit, la date la plus tardive prévalant :

-   Formule 1 : ajoutez 180 jours à la date de lancement du cluster HDInsight version X.
-   Formule 2 : ajoutez 90 jours à la date à laquelle le cluster HDInsight version X+1 (la version qui suit la version X) est mis à disposition dans le portail de gestion Azure.

La **Date d'obsolescence** est la date après laquelle la version du cluster ne peut plus être créée sur HDInsight.

> [WACOM.NOTE] Les clusters HDInsight 2.1 et 3.0 fonctionnent tous deux sur le système d'exploitation invité d'Azure [Famille 4][Famille 4], qui utilise la version 64 bits de Windows Server 2012 R2 et prend en charge .NET Framework 4.0, 4.5. et 4.5.1.

## Notes de publication de Hortonworks associées aux versions de HDInsight

-   Le cluster HDInsight version 3.1 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1]. Il s'agit du cluster Hadoop par défaut créé lors de l'utilisation du portail Azure HDInsight.

-   Le cluster HDInsight version 3.0 utilise une distribution Hadoop basée sur la [plateforme de données Hortonworks 2.0][plateforme de données Hortonworks 2.0].

-   Le cluster HDInsight version 2.1 utilise une distribution Hadoop basée sur la [plateforme de données Hortonworks 1.3][plateforme de données Hortonworks 1.3].

-   Le cluster HDInsight version 1.6 utilise une distribution Hadoop basée sur la [plateforme de données Hortonworks 1.1][plateforme de données Hortonworks 1.1].

  [Azure HDInsight]: http://go.microsoft.com/fwlink/?LinkID=285601
  [documentation Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Notes de publication de HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-release-notes/
  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/fr-fr/library/dn593744.aspx
  [Famille 4]: http://msdn.microsoft.com/fr-fr/library/azure/ee924680.aspx#explanation
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [plateforme de données Hortonworks 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [plateforme de données Hortonworks 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [plateforme de données Hortonworks 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
