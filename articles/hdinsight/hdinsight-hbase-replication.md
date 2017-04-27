---
title: "Configurer la réplication HBase | Microsoft Docs"
description: "Apprenez à configurer la réplication HBase pour l’équilibrage de charge, la haute disponibilité, la mise à jour/migration sans interruption de service d’une version HDInsight à une autre, ainsi que la récupération d’urgence."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a62cd616f0abd59c83c834bf13b4ba8549a9c73e
ms.openlocfilehash: 83aabe0e5161cd3f94caa996dec29bc020e5308b
ms.lasthandoff: 02/23/2017


---
# <a name="configure-hbase-replication"></a>Configurer la réplication HBase

Apprenez à configurer la réplication HBase dans un réseau virtuel ou entre deux réseaux virtuels.

La réplication en cluster utilise une méthodologie par émission de données source. Un cluster HBase peut être une source, une destination ou jouer les deux rôles à la fois. La réplication est asynchrone et l'objectif de la réplication est une cohérence éventuelle. Quand la source reçoit une modification apportée à une famille de colonnes dont la réplication est activée, cette modification est propagée à tous les clusters de destination. Quand les données sont répliquées d’un cluster à un autre, le cluster source et tous les clusters qui ont déjà utilisé les données font l’objet d’un suivi afin d’empêcher les boucles de réplication.

Dans ce didacticiel, vous allez configurer une réplication source-destination. Pour d'autres topologies de cluster, consultez le [Guide de référence d'Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Cas d’utilisation de la réplication HBase pour un seul réseau virtuel :

* Équilibrage de charge, par exemple, l’exécution d’analyses ou de travaux MapReduce sur le cluster de destination et l’ingestion de données sur le cluster source
* Haute disponibilité
* Migration des données à partir d’un cluster HBase vers un autre
* Mise à niveau d’un cluster Azure HDInsight à partir d’une version vers une autre

Cas d’utilisation de la réplication HBase pour deux réseaux virtuels :

* Récupération d'urgence
* Équilibrage de charge et partitionnement de l’application
* Haute disponibilité

Vous répliquez des clusters à l’aide de scripts [d’action de script](hdinsight-hadoop-customize-cluster-linux.md) disponibles dans [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="configure-the-environments"></a>Configurer les environnements

Il existe trois options de configuration :

- Deux clusters HBase dans un réseau virtuel Azure
- Deux clusters HBase dans deux réseaux virtuels différents dans la même région
- Deux clusters HBase dans deux réseaux virtuels différents dans deux régions distinctes (géoréplication)

Pour simplifier la configuration des environnements, nous avons créé quelques [modèles Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Si vous préférez configurer les environnements en utilisant d’autres méthodes, consultez :

- [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Création de clusters HBase dans un réseau virtuel Azure](hdinsight-hbase-provision-vnet.md)

### <a name="configure-one-virtual-network"></a>Configurer un réseau virtuel

Cliquez sur l’image suivante pour créer deux clusters HBase dans le même réseau virtuel. Ce modèle est stocké dans les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="configure-two-virtual-networks-in-the-same-region"></a>Configurer deux réseaux virtuels dans la même région

Cliquez sur l’image suivante pour créer deux réseaux virtuels avec homologation de réseaux virtuels et deux clusters HBase dans la même région. Ce modèle est stocké dans les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Ce scénario nécessite [l’homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md). Le modèle permet l’homologation de réseaux virtuels.   

La réplication HBase utilise des adresses IP des machines virtuelles ZooKeeper. Vous devez configurer des adresses IP statiques pour les nœuds ZooKeeper HBase de destination.

**Pour configurer des adresses IP statiques**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Groupes de ressources**.
3. Cliquez sur votre groupe de ressources qui contient le cluster HBase de destination. Il s’agit du groupe de ressources que vous avez spécifié lorsque vous avez utilisé le modèle Resource Manager pour créer l’environnement. Vous pouvez utiliser le filtre pour restreindre la liste. Vous pouvez voir une liste de ressources qui contient les deux réseaux virtuels.
4. Cliquez sur le réseau virtuel qui contient le cluster HBase de destination. Par exemple, cliquez sur **xxxx-vnet2**. Vous pouvez voir trois appareils avec des noms qui commencent par **nic-zookeepermode-**. Ces appareils sont les trois machines virtuelles ZooKeeper.
5. Cliquez sur l’une des machines virtuelles ZooKeeper.
6. Cliquez sur **Configurations IP**.
7. Dans la liste, cliquez sur **ipConfig1**.
8. Cliquez sur **Statique**et notez l’adresse IP réelle. Lorsque vous exécutez l’action de script pour activer la réplication, vous avez besoin de l’adresse IP.

  ![Adresse IP statique ZooKeeper de réplication HDInsight HBase](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Répétez l’étape 6 pour définir l’adresse IP statique des deux autres nœuds ZooKeeper.

Pour le scénario de réseaux virtuels croisés (cross-VNet), vous devez utiliser le commutateur **-ip** lors de l’appel de l’action de script **hdi_enable_replication.sh**.

### <a name="configure-two-virtual-networks-in-two-different-regions"></a>Configurer deux réseaux virtuels dans deux régions différentes

Cliquez sur l’image suivante pour créer deux réseaux virtuels dans deux régions différentes. Le modèle est stocké dans un conteneur de blobs Azure public.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Créez une passerelle VPN entre les deux réseaux virtuels. Pour savoir comment faire, consultez [Création d’un réseau virtuel avec une connexion de site à site à l’aide du portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

La réplication HBase utilise des adresses IP des machines virtuelles ZooKeeper. Vous devez configurer des adresses IP statiques pour les nœuds ZooKeeper HBase de destination. Pour configurer une adresse IP statique, consultez la section « Configurer deux réseaux virtuels dans la même région » de cet article.

Pour le scénario de réseaux virtuels croisés (cross-VNet), vous devez utiliser le commutateur **-ip** lors de l’appel de l’action de script **hdi_enable_replication.sh**.

## <a name="load-test-data"></a>Charger les données de test

Lorsque vous répliquez un cluster, vous devez spécifier les tables à répliquer. Dans cette section, vous allez charger des données dans le cluster source. Dans la section suivante, vous allez activer la réplication entre les deux clusters.

Suivez les instructions de [Didacticiel HBase : prise en main de HBase avec Hadoop dans HDInsight Linux](hdinsight-hbase-tutorial-get-started-linux.md) pour créer une table **Contacts** et insérer des données dans la table.

## <a name="enable-replication"></a>Activer la réplication

Les étapes suivantes montrent comment appeler le script d’action de script à partir du portail Azure. Pour exécuter une action de script à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure, consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

**Pour activer la réplication HBase à partir du portail Azure**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Ouvrez le cluster HBase source.
3. Dans le menu de cluster, cliquez sur **Actions de script**.
4. En haut du panneau, cliquez sur **Envoyer**.
5. Sélectionnez ou saisissez les informations suivantes :

  - **Nom** : saisissez **Activer la réplication**.
  - **Bash Script URL (URL de script bash)** : saisissez **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  - **Principal** : Sélectionné. Supprimez les autres types de nœuds.
  - **Paramètres** : les paramètres d’exemple suivants activent la réplication pour toutes les tables existantes et copient toutes les données du cluster source vers le cluster de destination :

            -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata

6. Cliquez sur **Create**. L’exécution du script peut prendre un certain temps, en particulier lorsque l’argument -copydata est utilisé.

Arguments requis :

|Nom|Description|
|----|-----------|
|-s, --src-cluster | Spécifiez le nom DNS du cluster HBase source. Par exemple : -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Spécifiez le nom DNS du cluster HBase de destination (réplica). Par exemple : -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Spécifiez le mot de passe d’administrateur pour Ambari sur le cluster HBase source. |
|-dp, --dst-ambari-password | Spécifiez le mot de passe d’administrateur pour Ambari sur le cluster HBase de destination.|

Arguments facultatifs :

|Nom|Description|
|----|-----------|
|-su, --src-ambari-user | Spécifiez le nom d’utilisateur administrateur pour Ambari sur le cluster HBase source. La valeur par défaut est **admin**. |
|-du, --dst-ambari-user | Spécifiez le nom d’utilisateur administrateur pour Ambari sur le cluster HBase de destination. La valeur par défaut est **admin**. |
|-t, --table-list | Spécifiez les tables à répliquer. Par exemple : --table-list="table1;table2;table3". Si vous ne spécifiez pas de tables, toutes les tables HBase existantes sont répliquées.|
|-m, --machine | Spécifiez le nœud principal sur lequel l’action de script s’exécute. La valeur peut être hn1 ou hn0. La valeur hn0 étant généralement plus utilisée, nous vous recommandons de choisir hn1. Vous utilisez cette option lorsque vous exécutez le script $0 comme une action de script à partir du portail HDInsight ou d’Azure PowerShell.|
|-ip | Cet argument est obligatoire lorsque vous activez la réplication entre deux réseaux virtuels. Cet argument agit comme un commutateur pour utiliser les adresses IP statiques des nœuds ZooKeeper à partir de clusters de réplica au lieu des noms de domaine complets. Les adresses IP statiques doivent être préconfigurées avant d’activer la réplication. |
|-cp, -copydata | Activez la migration des données existantes sur les tables pour lesquelles la réplication est activée. |
|-rpm, -replicate-phoenix-meta | Activez la réplication sur les tables système Phoenix. <br><br>*Utilisez cette option avec précaution.* Nous vous recommandons de recréer des tables Phoenix sur les clusters de réplica avant d’utiliser ce script. |
|-h, --help | Affichez des informations sur l’utilisation. |

La section print_usage() du [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) fournit une explication détaillée des paramètres.

Une fois le déploiement de l’action de script terminé, vous pouvez utiliser SSH pour vous connecter au cluster HBase de destination et vérifier que les données ont été répliquées.

### <a name="replication-scenarios"></a>Scénarios de réplication

La liste suivante présente certains cas d’utilisation générale et la définition de leurs paramètres :

- **Activer la réplication sur toutes les tables entre les deux clusters**. Ce scénario ne nécessite pas la copie/migration des données existantes sur les tables, et n’utilise pas de tables Phoenix. Utilisez les paramètres suivants :

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Activer la réplication sur des tables spécifiques**. Pour activer la réplication sur table1, table2 et table3, utilisez les paramètres suivants :

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Activer la réplication sur des tables spécifiques et copier les données existantes**. Pour activer la réplication sur table1, table2 et table3, utilisez les paramètres suivants :

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Activer la réplication sur toutes les tables avec la réplication des métadonnées Phoenix à partir de la source vers la destination**. La réplication des métadonnées Phoenix n’est pas parfaite et doit être utilisée avec précaution.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copier et migrer des données

Il existe deux scripts d’action de script distincts pour copier/migrer des données une fois la réplication activée :

- [Script pour les petites tables](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (taille de quelques gigaoctest et copie globale censée se terminer en moins d’une heure)

- [Script pour les tables volumineuses](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (copie censée prendre plus d’une heure)

Vous pouvez suivre la même procédure que celle utilisée dans la section [Activer la réplication](#enable-replication) pour appeler l’action de script avec les paramètres suivants :

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

La section print_usage() du [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) fournit une description détaillée des paramètres.

### <a name="scenarios"></a>Scénarios

- **Copier des tables spécifiques (test1, test2 et test3) pour toutes les lignes modifiées jusqu’à présent (horodatage actuel)** :

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  ou

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copier des tables spécifiques avec une plage horaire spécifiée** :

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Désactiver la réplication

Pour désactiver la réplication, vous utilisez un autre script d’action de script disponible dans [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Vous pouvez suivre la même procédure que celle utilisée dans la section [Activer la réplication](#enable-replication) pour appeler l’action de script avec les paramètres suivants :

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari Password> <-all|-t "table1;table2;...">  

La section print_usage() du [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) fournit une explication détaillée des paramètres.

### <a name="scenarios"></a>Scénarios

- **Désactiver la réplication sur toutes les tables** :

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  ou

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari username> --src-ambari-password=<source cluster Ambari password>

- **Désactiver la réplication sur les tables spécifiées (table1, table2 et table3)** :

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à configurer la réplication de HBase entre deux centres de données. Pour en savoir plus sur HDInsight et HBase, voir :

* [Didacticiel HBase : prise en main de HBase avec Hadoop dans HDInsight Linux][hdinsight-hbase-get-started]
* [Présentation de HBase dans HDInsight : une base de données NoSQL fournissant des fonctionnalités similaires à BigTable pour Hadoop][hdinsight-hbase-overview]
* [Création de clusters HBase dans un réseau virtuel Azure][hdinsight-hbase-provision-vnet]
* [Analyse de sentiments Twitter en temps réel avec HBase dans HDInsight][hdinsight-hbase-twitter-sentiment]
* [Analyse des données de capteur avec Storm et HBase dans HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

