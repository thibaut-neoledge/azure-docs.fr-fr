<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Notes de publication de Microsoft HDInsight

## Notes pour la version du 21/08/2014

-   Nous ajoutons la nouvelle configuration WebHCat suivante (HIVE-7155) qui fixe la limite de mémoire par défaut pour une tâche de contrôleur Templeton à 1 Go (l'ancienne valeur par défaut était de 512 Mo) :

    -   templeton.mapper.memory.mb (=1024)
    -   Cette modification résout l'erreur suivante que certaines requêtes Hive rencontraient en raison des limites de mémoire plus basses : « Container is running beyond physical memory limits » (Le conteneur a dépassé les limites de la mémoire physique).
    -   Pour revenir aux anciens paramètres par défaut, vous pouvez définir cette valeur de configuration sur 512 via le Kit de développement logiciel (SDK) PowerShell au moment de la création du cluster, avec la commande suivante :

        Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}

-   Le nom d'hôte du rôle zookeeper a été remplacé par zookeeper. Cela a une incidence sur la résolution de noms dans le cluster, mais pas sur les API REST externes. Si vous avez des composants qui utilisent le nom d'hôte du nœud zookeeper, vous devez les mettre à jour pour qu'ils utilisent le nouveau nom. Les nouveaux noms des trois nœuds zookeeper sont les suivants :

    -   zookeeper0
    -   zookeeper1
    -   zookeeper2
-   La matrice de support des versions de HBase est mise à jour. Seule la version HDInsight 3.1 (HBase version 0.98) est prise en charge pour les charges de travail HBase de production. La version 3.0 qui était disponible en tant que version préliminaire ne sera plus prise en charge à l'avenir. Pendant la période de transition, les clients pourront continuer à créer des clusters de la version 3.0.

## Notes pour la version du 28/07/14

-   **HDInsight disponible dans de nouvelles régions** : avec cette version, nous avons étendu la présence géographique de HDInsight à trois nouvelles régions. Les clients HDInsight peuvent désormais créer des clusters dans ces régions.
    -   Asie orientale
    -   Nord du centre des États-Unis et
    -   Sud du centre des États-Unis.
-   Avec cette version, HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) et HDInsight v2.1 (HDP1.3, Hadoop 1.2) sont retirés du portail de gestion Azure. Vous pouvez continuer à créer des clusters Hadoop pour ces versions avec les applets de commande PowerShell HDInsight ([New-AzureHDInsightCluster][New-AzureHDInsightCluster]) ou avec le [Kit de développement logiciel (SDK) HDInsight][Kit de développement logiciel (SDK) HDInsight]. Pour plus d'informations, consultez la page [Contrôle de version des composants HDInsight][Contrôle de version des composants HDInsight].
-   Changements concernant Hortonworks Data Platform (HDP) dans cette version :

| HDP               | Changements                                                   |
|-------------------|---------------------------------------------------------------|
| HDP 1.3 / HDI 2.1 | Pas de changements                                            |
| HDP 2.0 / HDI 3.0 | Pas de changements                                            |
| HDP 2.1 / HDI 3.1 | zookeeper : ['3.4.5.2.1.3.0-1948'] -\> ['3.4.5.2.1.3.2-0002'] |

## Notes pour la version du 24/06/14

Cette version inclut plusieurs nouvelles améliorations du service HDInsight :

-   **Disponibilité de HDP 2.1** : HDInsight 3.1, qui contient HDP 2.1, est désormais disponible pour le grand public et constitue la version par défaut pour les nouveaux clusters.
-   **HBase – Améliorations du portail de gestion Azure** : nous faisons en sorte que les clusters HBase soient disponibles dans la version préliminaire. Vous pouvez désormais créer des clusters HBase à partir du portail en 3 clics.

![][]

Avec HBase, vous pouvez créer différentes charges de travail en temps réel sur HDInsight, de sites web interactifs fonctionnant avec des jeux de données volumineux à des services stockant les données de capteur et de télémétrie provenant de millions de points de terminaison. L'étape suivante consisterait à analyser les données dans ces charges de travail avec des tâches Hadoop, ce qui est déjà possible dans HDInsight grâce, notamment, à PowerShell et au tableau de bord de cluster Hive.

### Apache Mahout désormais installé sur HDInsight 3.1

[Mahout][Mahout] est préinstallé sur les clusters Hadoop HDInsight 3.1. Vous pouvez donc exécuter des tâches Mahout sans avoir besoin d'une configuration de cluster supplémentaire. Par exemple, vous pouvez vous connecter à distance à un cluster Hadoop avec le protocole RDP (Remote Desktop Protocol) et exécuter la commande Hello world Mahout sans étapes supplémentaires :

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Pour une explication plus complète de cette procédure, consultez la documentation de l'[Exemple Breiman][Exemple Breiman] sur le site web Apache Mahout.

### Les requêtes Hive peuvent utiliser Tez dans HDinsight 3.1

Hive 0.13 est désormais disponible dans HDInsight 3.1 et capable d'exécuter des requêtes avec Tez. Des gains de performances considérables peuvent ainsi être obtenus.
Tez n'est pas activé par défaut pour les requêtes Hive. Vous devez choisir de l'utiliser. Vous pouvez activer Tez en exécutant l'extrait de code suivant :

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks a publié une ventilation détaillée des améliorations apportées aux requêtes Hive en termes de performances avec Tez, tel qu'il est fourni dans les benchmarks standard. Pour des détails, consultez la page [Tests de performances d'Apache Hive 13 for Enterprise Hadoop][Tests de performances d'Apache Hive 13 for Enterprise Hadoop].

Pour plus d'informations sur l'utilisation de Hive avec Tez, consultez la [page wiki Hive on Tez][page wiki Hive on Tez].

### Disponibilité globale

Avec le lancement d'Azure HDInsight sur Hadoop 2.2, Microsoft a fait en sorte que HDInsight soit disponible dans les principales régions Azure. Plus précisément, l'Europe de l'Ouest et le Sud-Est asiatique ont été mis en ligne. Cela permet aux clients de rechercher des clusters dans un centre de données proche et, potentiellement, dans une zone avec des exigences de conformité similaires.

### Dernières modifications

**Syntaxe du préfixe** :
seule la syntaxe « wasb:// » est prise en charge dans les clusters HDInsight 3.0 et 3.1. L'ancienne syntaxe « asv:// » est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais pas dans les clusters HDInsight 3.0 ou les versions ultérieures. Cela signifie que toutes les tâches envoyées vers un cluster HDInsight 3.0 ou 3.1 utilisant explicitement la syntaxe « asv:// »? échoueront. Vous devez plutôt utiliser la syntaxe wasb://. De même, les tâches créées avec un metastore existant contenant des références explicites aux ressources utilisant la syntaxe asv:// et envoyées vers un cluster HDInsight 3.0 ou 3.1 échoueront également. Vous devrez recréer ces metastores en utilisant la syntaxe wasb:// pour adresser les ressources.

**Ports** : les ports utilisés par le service HDInsight ont changé. Les numéros de ports utilisés étaient inclus dans la plage de ports éphémères du système d'exploitation Windows. Les ports sont alloués automatiquement à partir d'une plage éphémère prédéfinie pour des communications à durée de vie limitée basées sur un protocole Internet. Le nouvel ensemble de numéros de ports du service HDP (Hortonworks Data Platform) autorisés est désormais à l'extérieur de cette plage pour éviter tout conflit avec les ports utilisés par les services exécutés sur le nœud principal. Les nouveaux numéros de ports ne devraient pas entraîner des modifications radicales. Les numéros utilisés sont les suivants :

**HDInsight 1.6 (HDP 1.1)**

<table border="1">

<tr>
<th>
Nom

</th>
<th>
Valeur

</th>
</tr>

<tr>
<td>
dfs.http.address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.secondary.http.address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
mapred.job.tracker.http.address

</td>
<td>
jobtrackerhost:30030

</td>
</tr>

<tr>
<td>
mapred.task.tracker.http.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
mapreduce.history.server.http.address

</td>
<td>
0.0.0.0:31111

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
**HDInsight 3.0 et 3.1 (HDP 2.0 et 2.1)**

<table border="1">

<tr>
<th>
Nom

</th>
<th>
Valeur

</th>
</tr>

<tr>
<td>
dfs.namenode.http-address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.namenode.https-address

</td>
<td>
headnodehost:30470

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.namenode.secondary.http-address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
yarn.nodemanager.webapp.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
### Dépendances

Les dépendances suivantes ont été ajoutées à HDInsight 3.x (HDP2.x) :

-   guice-servlet
-   optiq-core
-   javax.inject
-   activation
-   jsr305
-   geronimo-jaspic\_1.0\_spec
-   jul-to-slf4j
-   java-xmlbuilder
-   ant
-   commons-compiler
-   jdo-api
-   commons-math3
-   paranamer
-   jaxb-impl
-   stringtemplate
-   eigenbase-xom
-   jersey-servlet
-   commons-exec
-   jaxb-api
-   jetty-all-server
-   janino
-   xercesImpl
-   optiq-avatica
-   jta
-   eigenbase-properties
-   groovy-all
-   hamcrest-core
-   mail
-   linq4j
-   jpam
-   jersey-client
-   aopalliance
-   geronimo-annotation\_1.0\_spec
-   ant-launcher
-   jersey-guice
-   xml-apis
-   stax-api
-   asm-commons
-   asm-tree
-   wadl
-   geronimo-jta\_1.1\_spec
-   guice
-   leveldbjni-all
-   velocity
-   jettison
-   snappy-java
-   jetty-all
-   commons-dbcp

Les dépendances suivantes n'existent plus dans HDInsight 3.x (HDP2.x) :

-   jdeb
-   kfs
-   sqlline
-   ivy
-   aspectjrt
-   json
-   core
-   jdo2-api
-   avro-mapred
-   datanucleus-enhancer
-   jsp
-   commons-logging-api
-   commons-math
-   JavaEWAH
-   aspectjtools
-   javolution
-   hdfsproxy
-   hbase
-   snappy

### Changements de version

Les changements de version suivants ont eu lieu entre HDInsight 2.x (HDP1.x) et HDInsight 3.x (HDP2.x) :

-   metrics-core : ['2.1.2'] -\> ['3.0.0']
-   derbynet : ['10.4.2.0'] -\> ['10.10.1.1']
-   datanucleus : ['rdbms-3.0.8'] -\> ['rdbms-3.2.9']
-   jasper-compiler : ['5.5.12'] -\> ['5.5.23']
-   log4j : ['1.2.15', '1.2.16'] -\> ['1.2.16', '1.2.17']
-   derbyclient : ['10.4.2.0'] -\> ['10.10.1.1']
-   httpcore : ['4.2.4'] -\> ['4.2.5']
-   hsqldb : ['1.8.0.10'] -\> ['2.0.0']
-   jets3t : ['0.6.1'] -\> ['0.9.0']
-   protobuf-java : ['2.4.1'] -\> ['2.5.0']
-   derby : ['10.4.2.0'] -\> ['10.10.1.1']
-   jasper : ['runtime-5.5.12'] -\> ['runtime-5.5.23']
-   commons-daemon : ['1.0.1'] -\> ['1.0.13']
-   datanucleus-core : ['3.0.9'] -\> ['3.2.10']
-   datanucleus-api-jdo : ['3.0.7'] -\> ['3.2.6']
-   zookeeper : ['3.4.5.1.3.9.0-01320'] -\> ['3.4.5.2.1.3.0-1948']
-   bonecp : ['0.7.1.RELEASE'] -\> ['0.8.0.RELEASE']

### Pilotes

Le pilote JDBC pour SQL Server est utilisé en interne par HDInsight et n'est pas employé pour les opérations externes. Si vous voulez vous connecter à HDInsight avec ODBC, utilisez le pilote ODBC Hive de Microsoft. Pour plus d'informations sur l'utilisation d'ODBC Hive, consultez la page [Connexion d'Excel à HDInsight avec le pilote ODBC Hive de Microsoft][connexion-excel-avec-ODBC-hive].

### Résolution des bogues

Dans cette version, nous avons actualisé les versions suivantes de HDInsight (Hortonworks Data Platform - HDP) avec la résolution de plusieurs bogues :

-   HDInsight 2.1 (HDP 1.3)
-   HDInsight 3.0 (HDP 2.0)
-   HDInsight 3.1 (HDP 2.1)

## Notes de publication de Hortonworks

Les notes de publication des HDP utilisés par les versions de cluster HDInsight sont disponibles aux emplacements suivants.

-   Le cluster HDInsight version 3.1 utilise une distribution de Hadoop basée sur [Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1]. (Il s'agit du cluster Hadoop par défaut créé lors de l'utilisation du portail Azure HDInsight.)

-   Le cluster HDInsight version 3,0 utilise une distribution Hadoop basée sur la [plateforme de données Hortonworks 2.0][plateforme de données Hortonworks 2.0].

-   Le cluster HDInsight version 2.1 utilise une distribution Hadoop basée sur la [plateforme de données Hortonworks 1.3][plateforme de données Hortonworks 1.3].

-   Le cluster HDInsight version 1.6 utilise une distribution Hadoop basée sur la [plateforme de données Hortonworks 1.1][plateforme de données Hortonworks 1.1].

  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/fr-fr/library/dn593744.aspx
  [Kit de développement logiciel (SDK) HDInsight]: http://msdn.microsoft.com/fr-fr/library/azure/dn469975.aspx
  [Contrôle de version des composants HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-component-versioning/
  []: http://i.imgur.com/cmOl5fM.png
  [Mahout]: http://hortonworks.com/hadoop/mahout/
  [Exemple Breiman]: https://mahout.apache.org/users/classification/breiman-example.html
  [Tests de performances d'Apache Hive 13 for Enterprise Hadoop]: http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/
  [page wiki Hive on Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [plateforme de données Hortonworks 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [plateforme de données Hortonworks 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [plateforme de données Hortonworks 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
