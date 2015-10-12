<properties
	pageTitle="Nouveautés des versions de cluster Hadoop dans HDInsight | Microsoft Azure"
	description="HDInsight prend en charge plusieurs versions de cluster Hadoop pouvant être déployées. Découvrez les versions de distributions prises en charge Hadoop et HortonWorks Data Platform (HDP)."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>


#Nouveautés des versions de cluster Hadoop fournies par HDInsight

##Versions de HDInsight et composants de Hadoop
Azure HDInsight prend en charge plusieurs versions de cluster Hadoop qui peuvent être déployées à tout moment. Le choix d'une version approvisionne une version spécifique de la distribution de la plateforme de données Hortonworks et un ensemble de composants qui sont contenus dans cette distribution. Les versions des composants associées aux versions de cluster HDInsight sont répertoriées dans le tableau suivant. Notez que la version de cluster par défaut actuellement utilisée par Azure HDInsight est la version 3.1, et, à la date du 7/11/2014, elle est basée sur HDP 2.1.7.


Composant|HDInsight Version 3.2|HDInsight Version 3.1 (par défaut)|HDInsight Version 3,0|HDInsight Version 2.1
---|---|---|---|---
Hortonworks Data Platform|2\.2|2\.1.7|2\.0|1\.3
Apache Hadoop et YARN|2\.6.0|2\.4.0|2\.2.0|1\.2.0
Apache Tez|0\.5.2|0\.4.0||
Apache Pig|0\.14.0|0\.12.1|0\.12.0|0\.11.0
Apache Hive et HCatalog|0\.14.0|0\.13.1|0\.12.0|0\.11.0
Apache HBase |0\.98.4|0\.98.0||
Apache Sqoop|1\.4.5|1\.4.4|1\.4.4|1\.4.3
Apache Oozie|4\.1.0|4\.0.0|4\.0.0|3\.3.2
Apache Zookeeper|3\.4.6|3\.4.5|3\.4.5|
Apache Storm|0\.9.3|0\.9.1||
Apache Mahout|0\.9.0|0\.9.0||
Apache Phoenix|4\.2.0|4\.0.0.2.1.7.0-2162||
Apache Spark|1\.3.1|||


**Obtention d’informations sur les versions actuelles des composants**

Les versions de composant associées aux versions de cluster HDInsight sont susceptibles de changer dans les futures mises à jour de HDInsight. Pour déterminer quels sont les composants disponibles et vérifier quelles versions sont utilisées pour un cluster, vous pouvez utiliser l’API REST Ambari. La commande **GetComponentInformation** permet de récupérer des informations sur un composant du service. Pour des détails, consultez la [documentation Ambari][ambari-docs]. Vous pouvez également obtenir ces informations en vous connectant à un cluster à l’aide du Bureau à distance et en examinant directement le contenu du répertoire « C:\\apps\\dist ».


**Notes de publication**

Pour d’autres notes de publication sur les dernières versions de HDInsight, consultez la page [Notes de publication de HDInsight](hdinsight-release-notes.md).

### Sélectionnez une version lors de l'approvisionnement d'un cluster HDInsight

Lorsque vous créez un cluster par l'intermédiaire des applets de commande Windows PowerShell HDInsight ou du Kit de développement logiciel (SDK) .NET HDInsight, vous pouvez choisir la version du cluster Hadoop HDInsight avec le menu déroulant de la **version HDInsight** sur le panneau ** Configuration facultative** de la version préliminaire du portail Azure.

##Présentation des fonctionnalités
Les caractéristiques principales de la plateforme HDInsight sont notamment les suivantes :

- **Spark** : Apache Spark est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d'analyse de données volumineuses. De par ses capacités de calcul en mémoire, Spark constitue le choix idéal pour les algorithmes itératifs utilisés dans les calculs d'apprentissage machine et de graphiques.

	Spark permet également d'effectuer des opérations standard de traitement des données sur disque. En évitant les écritures sur disque lors des étapes intermédiaires, Spark améliore l’infrastructure MapReduce traditionnelle. En outre, Spark est compatible avec le système HDFS (Hadoop Distributed File System) et le stockage d’objets blob Azure ; les données existantes peuvent dont être traitées facilement via Spark.

	Spark peut également être ajouté à l’aide d’une action de script. Une action de script ajoute Spark 1.2.0 au cluster HDInsight 3.2 ou Spark 1.0.2 au cluster HDInsight 3.1. Pour plus d'informations, consultez [Installer et utiliser Spark sur des clusters HDInsight Hadoop](hdinsight-hadoop-spark-install.md).


- **Storm** - Storm sur Azure HDInsight est désormais disponible. Cette fonction permet de déployer rapidement et simplement des analyses en temps réel en quelques clics et en quelques minutes. Apache Storm sur Azure HDInsight est un projet open source dans l’écosystème Apache Hadoop qui permet d’accéder à une plateforme d’analyse capable de traiter de manière fiable des millions d’événements. Dorénavant, les utilisateurs de Hadoop peuvent interpréter les données en temps réel, tout en disposant des analyses d’événements passés. Microsoft a également ajouté une intégration avec Visual Studio, facilitant ainsi l’interaction des développeurs avec Storm. Vous pouvez maintenant développer, déployer et déboguer les topologies Storm à partir de Visual Studio.

- **HDInsight sur Linux** : Azure HDInsight fournit l'option d'approvisionnement des clusters Hadoop qui s'exécutent sur les machines virtuelles Linux (Ubuntu). Vous pouvez utiliser cette option si vous maîtrisez Linux ou Unix, si vous effectuez une migration à partir d’une solution Hadoop Linux existante, ou si vous souhaitez intégrer facilement des composants de l’écosystème Hadoop conçus pour Linux. Vous pouvez approvisionner un cluster HDInsight sur Linux à partir d'un ordinateur client fonctionnant sous Windows ou Linux à l'aide de la version préliminaire du portail Azure, de l'interface de ligne de commande Azure (CLI) ou du Kit de développement logiciel (SDK) .NET HDInsight (Windows uniquement).

- **Tailles des machines virtuelles supplémentaires** - Les clusters HDInsight sont désormais disponibles sur plusieurs tailles et types de machines virtuelles. Les clusters HDInsight peuvent à présent utiliser des tailles A2 à A7 conçues pour un usage général ; des nœuds de série D possédant des disques SSD et des processeurs 60 % plus rapides ; et des tailles A8 et A9 prenant en charge InfiniBand pour une mise en réseau rapide. Les clients Apache HBase sur Azure HDInsight peuvent tirer parti de configurations supérieures au niveau de la mémoire de série D pour augmenter les performances. Les clients Apache Storm sur Azure HDInsight peuvent également bénéficier d’une mémoire supplémentaire pour le chargement des jeux de données de référence plus volumineux, ainsi que de processeurs plus rapides pour augmenter le débit.

- **Mise à l’échelle du cluster** - La mise à l’échelle du cluster permet de modifier le nombre de nœuds d’un cluster HDInsight en cours d’exécution, sans avoir à le supprimer ou à le recréer. Actuellement, seuls Hadoop Query et Apache Storm disposent de cette fonction, mais Apache HBase en sera bientôt équipé.

- **Action de script** - Cette fonctionnalité de personnalisation des clusters permet de modifier les clusters Hadoop de façon arbitraire au moyen de scripts personnalisés. Cette nouvelle fonctionnalité offre aux utilisateurs la possibilité de tester et de déployer des projets disponibles dans l'écosystème Apache Hadoop vers les clusters Azure HDInsight. Elle est disponible sur tous les types de clusters HDInsight, dont Hadoop, HBase et Storm.

- **HBase** - HBase est une base de données NoSQL à faible latence qui permet le traitement transactionnel en ligne des données volumineuses (« Big Data »). HBase est proposé en tant que cluster géré intégré à l'environnement Azure. Les clusters sont configurés de façon à stocker les données directement dans le stockage d'objets blob Azure, ce qui fournit une faible latence et une élasticité accrue en matière de choix performances/coût. Cela permet aux clients de créer des sites web interactifs fonctionnant avec des jeux de données volumineux, de créer des services stockant les données de capteur et de télémétrie provenant de millions de points de terminaison, et d'analyser ces données avec des tâches Hadoop.

- **Apache Phoenix** - Apache Phoenix est une couche de requête SQL sur HBase. Il prend en charge un sous-ensemble limité de spécifications de langage de requête SQL, y compris les index secondaires. Il est fourni en tant que pilote JDBC (Java Database Connectivity) intégré au client ciblant les requêtes à faible latence sur les données HBase. Apache Phoenix prend votre requête SQL, la compile en une série d’analyses HBase et d’appels de coprocesseurs, et produit des jeux de résultats JDBC standard. Apache Phoenix est une couche de base de données relationnelle sur HBase. Il est fourni en tant que pilote JDBC intégré au client ciblant les requêtes à faible latence sur les données HBase. Apache Phoenix prend votre requête SQL, la compile en une série d'analyses HBase, et orchestre l'exécution de ces analyses pour produire des jeux de résultats JDBC standard.

- **Tableau de bord de cluster** - Cette nouvelle application web est déployée vers votre cluster HDInsight. Utilisez-la pour exécuter des requêtes Hive, vérifier les journaux des tâches et parcourir le stockage d’objets blob Azure. L'URL utilisée pour accéder à l'application web est <*Nom\_cluster*>.azurehdinsight.net.

- **Microsoft Avro Library** - Cette bibliothèque met en œuvre le système de sérialisation des données Apache Avro pour l’environnement Microsoft.NET. Apache Avro fournit un format compact d'échange des données binaires pour la sérialisation. Elle utilise le format JSON (JavaScript Object Notation) pour définir un schéma sans langage spécifié qui assure l’interopérabilité des langages. Les données sérialisées dans un langage peuvent être lues dans un autre langage. Les langages C, C++, C#, Java, PHP, Python et Ruby sont actuellement pris en charge. Le format de sérialisation Apache Avro est largement utilisé dans Azure HDInsight pour représenter des structures de données complexes dans une tâche Hadoop MapReduce.

- **YARN** - Cette nouvelle infrastructure de gestion d’application généraliste et distribuée a remplacé la classique infrastructure Apache Hadoop MapReduce pour le traitement des données dans les clusters Hadoop. Elle est efficace en tant que système d'exploitation Hadoop. Hadoop est ainsi passé d'une plateforme de données à usage unique pour le traitement par lots à une plateforme à usage multiple qui permet le traitement par lots, interactif, en ligne et par flux. Cette nouvelle infrastructure de gestion améliore l’extensibilité et l’utilisation des clusters en fonction de critères tels que les garanties de capacité, l’équité et les contrats de niveau de service (SLA).

- **Tez (HDInsight version 3.1 et ultérieures uniquement)** - Cette infrastructure généraliste et personnalisable crée des tâches de traitement de données simplifiées avec des charges de travail à petite et à grande échelle dans Hadoop. Elle offre la possibilité d’exécuter un graphique non cyclique dirigé (DAG) complexe de tâches pour un seul travail, ce qui permet aux projets de l’écosystème Apache Hadoop, tels que Apache Hive et Apache Pig, de satisfaire aux exigences en termes de temps de réponse homme-interactivité et de débit extrême à l’échelle du péta-octet. Notez que Hive 0.13 permet aux requêtes Hive de s'exécuter par-dessus Tez, plutôt que sur MapReduce.

- **Haute disponibilité** - Un second nœud principal a été ajouté aux clusters Hadoop déployés par HDInsight pour améliorer la disponibilité du service. Les implémentations standard des clusters Hadoop ont normalement un seul nœud principal. HDInsight élimine ce point de défaillance unique avec l’ajout d’un second nœud principal. Le basculement vers une nouvelle configuration de cluster Haute disponibilité n’a aucune incidence sur le prix du cluster, sauf si le client approvisionne des clusters avec un nœud principal de très grande taille.

- **Performances de Hive** - Amélioration considérable des temps de réponse des requêtes Hive (jusqu’à 40 fois plus rapides) et de la compression de données (jusqu’à 80 %) avec le format **ORC (Optimized Row Columnar)**.

- **Pig, Sqoop, Oozie, Ambari** - Mises à niveau des versions des composants pour le cluster HDInsight version 3.0 (HDP 2.0/Hadoop 2.2) qui fournissent la parité avec le cluster HDInsight version 2.1 (HDP 1.3/Hadoop 1.2). Pour des précisions, consultez le tableau des versions ci-dessous.

- **Mahout** - Cette bibliothèque d’algorithmes à apprentissage automatique évolutif est pré-installée sur les clusters Hadoop HDInsight 3.1 (et versions ultérieures). Vous pouvez donc exécuter des tâches Mahout sans avoir besoin d'une configuration de cluster supplémentaire.

- **Prise en charge de Virtual Network** - Les clusters HDInsight peuvent être utilisés avec Azure Virtual Network pour prendre en charge l’isolement des ressources cloud ou de scénarios hybrides liant les ressources cloud avec celles de votre centre de données.


## Versions prises en charge
Le tableau suivant répertorie les versions de HDInsight actuellement disponibles, les versions correspondantes de la plateforme de données Hortonworks utilisées par ces dernières, ainsi que leurs dates de lancement. La date d’expiration de la prise en charge et la date d’obsolescence sont également indiquées. Notez les points suivants :

* Seuls les clusters à haute disponibilité avec deux nœuds principaux sont déployés par défaut pour les clusters HDInsight 2.1 et versions ultérieures. Ils ne sont pas disponibles pour les clusters HDInsight 1.6.
* Une fois que la prise en charge a expiré pour une version particulière, elle n'est plus disponible par l'intermédiaire de la version préliminaire du portail Azure. Le tableau suivant indique quelles versions sont disponibles sur le portail Azure. Les versions des clusters resteront disponibles avec le paramètre `Version` de la commande [New-AzureHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) Windows PowerShell et le Kit de développement logiciel (SDK) .NET jusqu’à leur date d’obsolescence.

Version de HDInsight|Version de HDP|Haute disponibilité|Date de lancement|Disponible sur le portail Azure|Date d'expiration du support|Date d'obsolescence
---|---|---|---|---|---|---
HDI 3.2|HDP 2.2|Oui|2/18/2015|Oui||
HDI 3,1|HDP 2,1|Oui|6/24/2014|Oui||
HDI 3,0|HDP 2,0|Oui|02/11/2014|Oui|09/17/2014|06/30/2015
HDI 2,1|HDP 1,3|Oui|10/28/2013|Non|05/12/2014|05/31/2015
HDI 1.6|HDP 1.1|Non|10/28/2013|Non|04/26/2014|05/31/2015

**Déploiement des clusters autres que les clusters par défaut**

### Contrat de niveau de service pour les versions de cluster HDInsight

Le contrat de niveau de service est défini en termes de « fenêtre de support ». Une fenêtre de support désigne la durée pendant laquelle une version de cluster HDInsight est prise en charge par le support technique et le service clientèle Microsoft. Un cluster HDInsight est en dehors de la fenêtre de support si la **Date d'expiration du support** correspondant à la version de ce cluster est postérieure à la date actuelle. Le tableau ci-dessus contient une liste des versions de cluster HDInsight prises en charge. La date d'expiration du support pour une version donnée de HDInsight (lorsqu'une version X+1 plus récente est disponible) est calculée comme suit, la date la plus tardive prévalant :

- Formule 1 : ajoutez 180 jours à la date de lancement du cluster HDInsight version X.
- Formule 2 : ajoutez 90 jours à la date à laquelle le cluster HDInsight version X+1 (la version qui suit la version X) est mis à disposition dans la version préliminaire du portail.

La **Date d’obsolescence** est la date après laquelle la version du cluster ne peut plus être créée sur HDInsight.

> [AZURE.NOTE]Les clusters HDInsight 2.1 et 3.0 fonctionnent tous deux sur le système d’exploitation invité d’Azure [Famille 4](../cloud-services-guestos-update-matrix.md), qui utilise la version 64 bits de Windows Server 2012 R2 et prend en charge .NET Framework 4.0, 4.5 et 4.5.1.

## Notes de publication de Hortonworks associées aux versions de HDInsight##

* Le cluster HDInsight version 3.2 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.2][hdp-2-2].

	* Notes de publication pour les composants spécifiques d’Apache - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).


* Le cluster HDInsight version 3.1 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Il s'agit du cluster Hadoop **par défaut** créé lors de l'utilisation du portail après le 1172014. Les clusters HDInsight 3.1 créés avant le 7/11/2014 étaient basés sur [Hortonworks Data Platform 2.1.1][hdp-2-1-1].

* Le cluster HDInsight version 3.0 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 2.0][hdp-2-0-8].

* Le cluster HDInsight version 2.1 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 1.3][hdp-1-3-0].

* Le cluster HDInsight version 1.6 utilise une distribution Hadoop basée sur [Hortonworks Data Platform 1.1][hdp-1-1-0].


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=Oct15_HO1-->