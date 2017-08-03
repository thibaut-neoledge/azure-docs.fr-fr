---
title: "Résolution de problèmes STORM - Azure HDInsight | Microsoft Docs"
description: "Utilisez le FAQ Storm pour obtenir des réponses aux questions courantes sur l’utilisation de Storm sur la plateforme Azure HDInsight."
keywords: "Azure HDInsight, Storm, FAQ, guide de dépannage, problèmes courants"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: b52462096ce977b94ff9514df650607b05e17030
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017

---

# <a name="storm-troubleshooting"></a>Résolution de problèmes STORM

Cet article décrit les principaux problèmes rencontrés lors de l’utilisation de charges utiles Storm dans Apache Ambari, ainsi que les étapes à suivre pour les résoudre.

## <a name="how-do-i-access-storm-ui-on-a-cluster"></a>Comment accéder à l’interface utilisateur Storm sur un cluster

### <a name="issue"></a>Problème :
Il existe deux façons d’accéder à l’interface utilisateur Storm à partir d’un navigateur :

#### <a name="ambari-ui"></a>Interface utilisateur Ambari
1. Accédez au tableau de bord Ambari.
1. Sélectionnez Storm dans la liste des services de gauche.
1. Sélectionnez l’option de l’interface utilisateur Storm dans le menu déroulant des liens rapides.

#### <a name="direct-link"></a>Lien direct
L’interface utilisateur Storm est accessible à partir de l’URL :

https://\<NomDnsDuCluster\>/stormui

Par exemple : https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another"></a>Comment transférer les informations de point de contrôle du spout Storm Eventhub d’une topologie à une autre

### <a name="issue"></a>Problème :
Lors du développement de topologies qui lisent à partir de concentrateurs d’événements utilisant le spout Storm Eventhub d’HDInsight, comment peut-on déployer une topologie portant le même nom sur un nouveau cluster tout en conservant dans l’ancien cluster les données de point de contrôle validées dans Apache ZooKeeper ?

#### <a name="where-is-checkpoint-data-stored"></a>Où sont stockées les données de point de contrôle ?
Les données de point de contrôle des décalages sont enregistrées par le spout EventHub dans Zookeeper sous deux chemins d’accès racine :
- Les points de contrôle de spout non transactionnels sont stockés sous /eventhubspout
- Les données de point de contrôle de spout de transaction sont stockées sous /transactional

#### <a name="how-to-restore"></a>Comment effectuer une restauration ?
Les scripts et les bibliothèques permettant d’exporter des données depuis Zookeeper et de les réimporter sous un nouveau nom sont disponibles dans https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0

Le dossier lib renferme des fichiers Jar contenant les instructions d’implémentation de l’opération d’importation/exportation.
Le dossier bash comporte un exemple de script servant à exporter des données depuis le serveur Zookeeper de l’ancien cluster et à les réimporter dans le serveur Zookeeper du nouveau cluster.

Le script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) doit être exécuté à partir des nœuds Zookeeper pour importer/exporter des données.
Le script doit être mis à jour pour corriger la chaîne de la version HDP.
(HDInsight travaille actuellement à la généralisation de ces scripts, afin qu’ils puissent s’exécuter à partir de n’importe quel nœud du cluster sans que l’utilisateur final n’ait à les modifier).

La commande d’exportation écrit les métadonnées vers un chemin d’accès au stockage HDFS (magasin d’objets BLOB ou ADLS) à l’emplacement spécifié.

### <a name="examples"></a>Exemples

##### <a name="export-offset-metadata"></a>Exporter les métadonnées de décalage :
1. À l’aide de SSH, accédez à l’ancien cluster Zookeeper à partir duquel les données de décalage de point de contrôle doivent être exportées.
1. Exécutez la commande ci-dessous (après la mise à jour de la chaîne de la version hdp) pour exporter les données de décalage Zookeeper vers le chemin de stockage HDFS /stormmetadta/zkdata.

```apache   
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
```

##### <a name="import-offset-metadata"></a>Importer les métadonnées de décalage
1. À l’aide de SSH, accédez à l’ancien cluster Zookeeper à partir duquel les données de décalage de point de contrôle doivent être exportées.
1. Exécutez la commande ci-dessous (après la mise à jour de la chaîne de la version hdp) pour importer les données de décalage Zookeeper à partir du chemin de stockage HDFS /stormmetadata/zkdata vers le serveur Zookeeper dans le cluster cible).

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
```
   
##### <a name="delete-offset-metadata-so-topologies-can-start-processing-data-from-either-beginning-or-timestamp-of-user-choice"></a>Supprimer les métadonnées de décalage afin que les topologies puissent commencer à traiter les données (à partir du début ou d’un horodatage défini par l’utilisateur)
1. À l’aide de SSH, accédez à l’ancien cluster Zookeeper à partir duquel les données de décalage de point de contrôle doivent être exportées.
1. Exécutez la commande ci-dessous (après la mise à jour de la chaîne de la version hdp) pour supprimer les données de décalage Zookeeper du cluster actuel.

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Comment localiser les fichiers binaires de Storm sur un cluster ?

### <a name="issue"></a>Problème :
 Connaître l’emplacement des fichiers binaires des services Storm sur un cluster HDInsight

### <a name="resolution-steps"></a>Étapes de résolution :

Les fichiers binaires Storm de la pile HDP actuelle se trouvent à l’emplacement /usr/hdp/current/storm-client

Cet emplacement est le même pour les nœuds principaux et les nœuds de travail.
 
Il peut y avoir plusieurs fichiers binaires propres aux différentes versions HDP sous /usr/hdp (par exemple : /usr/hdp/2.5.0.1233/storm)

En revanche, un lien symbolique avec /usr/hdp/current/storm-client est créé vers la version la plus récente exécutée sur le cluster.

### <a name="further-reading"></a>Pour aller plus loin :
 [Se connecter à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) [Storm](http://storm.apache.org/)
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Comment déterminer la topologie de déploiement d’un cluster Storm ?
 
### <a name="issue"></a>Problème :
 
Identifier tous les composants installés avec HDInsight Storm.
 
Un cluster Storm comprend 4 catégories de nœuds :
1. Passerelle
1. Nœuds principaux
1. Nœuds Zookeeper
1. Nœuds de travail
 
#### <a name="gateway-nodes"></a>Nœuds de passerelle
Service de passerelle et de proxy inverse qui permet un accès public au service de gestion actif Ambari et gère le choix du leader de cluster Ambari.
 
#### <a name="zookeeper-nodes"></a>Nœuds Zookeeper
HDInsight est fourni avec un quorum de 3 nœuds Zookeeper.
Sa taille est fixe et n’est pas configurable.
 
Les services Storm du cluster sont configurés pour utiliser le quorum Zookeeper automatiquement.
 
#### <a name="head-nodes"></a>Nœuds principaux
Les nœuds principaux Storm exécutent les services suivants :
1. Nimbus
1. Ambari Server
1. Ambari Metrics Server
1. Ambari Metrics Collector
 
#### <a name="worker-nodes"></a>Nœuds de travail
 Les nœuds de travail Storm exécutent les services suivants :
1. Supervisor
1. JVM de travail pour l’exécution de topologies
1. Ambari Agent
 
## <a name="how-do-i-locate-storm-eventhub-spout-binaries-for-development"></a>Comment localiser les fichiers binaires de spout EventHub Storm pour le développement ?
 
### <a name="issue"></a>Problème :
Comment obtenir plus d’informations sur l’utilisation des fichiers jar de spout EventHub Storm avec ma topologie ?
 
#### <a name="msdn-articles-on-how-to"></a>Articles MSDN sur différentes procédures
 
##### <a name="java-based-topology"></a>Topologie basée sur Java
https://docs.microsoft.com/fr-fr/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology
 
##### <a name="c-based-topology-using-mono-on-hdi-34-linux-storm-clusters"></a>Topologie basée sur C# (utilisation de Mono sur les clusters Storm HDI 3.4+ Linux)
https://docs.microsoft.com/fr-fr/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology
 
##### <a name="latest-storm-eventhub-spout-binaries-for-hdi35-linux-storm-clusters"></a>Fichiers binaires de spout EventHub Storm les plus récents pour les clusters Storm HDI 3.5+ Linux
Consultez le fichier https://github.com/hdinsight/mvn-repo/blob/master/README.md pour savoir comment utiliser le spout EventHub Storm le plus récent fonctionnant avec les clusters Storm HDI3.5+ Linux.
 
##### <a name="source-code-examples"></a>Exemples de code source :
https://github.com/Azure-Samples/hdinsight-java-storm-eventhub
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Comment localiser les fichiers de configuration Storm Log4J sur les clusters ?
 
### <a name="issue"></a>Problème :
 
Identifier les fichiers de configuration Log4J des services Storm.
 
#### <a name="on-headnodes"></a>Sur les nœuds principaux :
Le fichier de configuration Log4J du service Nimbus est lu à partir du chemin /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml
 
#### <a name="worker-nodes"></a>Nœuds de travail
Le fichier de configuration Log4J du service Supervisor est lu à partir du chemin /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml
 
Le fichier de configuration Log4J du worker est lu à partir du chemin /usr/hdp/<HDPVersion>/storm/log4j2/worker.xml
 
Par exemple : /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml







