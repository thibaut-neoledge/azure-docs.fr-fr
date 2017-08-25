---
title: "Augmenter la mise à l’échelle pour Apache Kafka - Azure HDInsight | Microsoft Docs"
description: "Découvrez comment configurer des disques gérés pour un cluster Apache Kafka sur Azure HDInsight afin d’accroître l’extensibilité."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/14/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 880a186a3d9a23b013294b0121e8265270d160cc
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Configurer le stockage et l’extensibilité pour Apache Kafka sur HDInsight

Découvrez comment configurer le nombre de disques gérés utilisés par Apache Kafka sur HDInsight.

Kafka sur HDInsight utilise le disque local des machines virtuelles dans le cluster HDInsight. Étant donné que Kafka fait une utilisation intensive des E/S, le service [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) est utilisé pour fournir un haut débit et un espace de stockage plus important pour chaque nœud. Si des disques durs virtuels (VHD) traditionnels ont été utilisés pour Kafka, chaque nœud est limité à 1 To. Grâce aux disques gérés, vous pouvez utiliser plusieurs disques afin d’atteindre une capacité de 16 To pour chaque nœud du cluster.

Le diagramme ci-après compare l’utilisation de Kafka sur HDInsight avant les disques gérés et celle de Kafka sur HDInsight avec les disques gérés :

![Diagramme illustrant l’utilisation de Kafka sur HDInsight avec un seul VHD par machine virtuelle et avec plusieurs disques gérés par machine virtuelle](./media/hdinsight-apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Configurer les disques gérés : Portail Azure

1. Pour comprendre la procédure courante de création d’un cluster à l’aide du portail, suivez les instructions de l’article [Création d’un cluster HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md). N’exécutez pas la procédure de création du portail.

2. Dans le panneau __Taille du cluster__, utilisez le champ __Disks per worker node (Disques par nœud Worker)__ pour configurer le nombre de disques.

    > [!NOTE]
    > Le type de disque géré peut être soit __Standard__ (HDD), soit __Premium__ (SSD). Les disques Premium sont utilisés avec les machines virtuelles séries DS et GS. Tous les autres types de machines virtuelles utilisent des disques Standard.

    ![Image du panneau Taille du cluster avec mise en surbrillance des disques par nœud Worker](./media/hdinsight-apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Configurer les disques gérés : modèle Resource Manager

Pour contrôler le nombre de disques utilisés par les nœuds Worker dans un cluster Kafka, utilisez la section ci-après du modèle :

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Un modèle complet expliquant comment configurer les disques gérés est disponible à l’adresse [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Kafka sur HDInsight, consultez les documents suivants :

* [Utilisation de MirrorMaker pour créer un réplica de Kafka sur HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Use Apache Storm with Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)
* [Use Apache Spark with Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md) (Utilisation d’Apache Spark avec Kafka sur HDInsight)
* [Connect to Kafka through an Azure Virtual Network](hdinsight-apache-kafka-connect-vpn-gateway.md) (Se connecter à Kafka via un réseau virtuel Azure)

* [Blog HDInsight sur les disques gérés avec Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
