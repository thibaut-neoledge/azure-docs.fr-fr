---
title: "Présentation d’Apache Kafka sur HDInsight - Azure | Microsoft Docs"
description: "Découvrez Apache Kafka sur HDInsight : Présentation, fonctionnalités et exemples et informations de prise en main."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f6a49be148ded1a90a642b83d3d70a83e572f4e4
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017

---
# <a name="introducing-apache-kafka-on-hdinsight-preview"></a>Présentation d’Apache Kafka sur HDInsight (version préliminaire)

[Apache Kafka](https://kafka.apache.org) est une plateforme de diffusion en continu distribuée open source qui permet de générer des pipelines de données et des applications de diffusion en continu en temps réel. Kafka fournit également des fonctionnalités de courtier de messages semblables à une file d’attente, où vous pouvez publier et vous abonner aux flux de données nommés. Kafka sur HDInsight vous offre un service géré, hautement évolutif et hautement disponible dans le cloud Microsoft Azure.

## <a name="why-use-kafka-on-hdinsight"></a>Pourquoi utiliser Kafka sur HDInsight ?

Kafka fournit les fonctionnalités suivantes :

* Modèle de messagerie de publication/abonnement : Kafka fournit une API de producteur pour la publication des enregistrements dans un sujet Kafka. L’API de consommateur est utilisée lors de l’abonnement à un sujet.

* Traitement des flux : Kafka est souvent utilisé avec Apache Storm ou Spark pour le traitement des flux en temps réel. Kafka 0.10.0.0 (HDInsight version 3.5) a introduit une API de diffusion en continu qui vous permet de créer des solutions de diffusion en continu sans Storm ni Spark.

* Évolution horizontale : Kafka partitionne les flux de données entre les nœuds du cluster HDInsight. Les processus consommateur peuvent être associés à des partitions individuelles pour fournir un équilibrage de charge lors de l’utilisation des enregistrements.

* Livraison chronologique : dans chaque partition, les enregistrements sont stockés dans le flux de données dans l’ordre de réception. En associant un processus consommateur par partition, vous pouvez garantir que les enregistrements sont traités dans l’ordre.

* Tolérance de pannes : les partitions peuvent être répliquées entre les nœuds pour fournir une tolérance de pannes.

* Intégration à Azure Managed Disks : Managed Disks disques offre une mise à l’échelle et un débit supérieurs pour les disques utilisés par les machines virtuelles du cluster HDInsight.

    Les disques gérés sont activés par défaut pour Kafka sur HDInsight, et le nombre de disques utilisés par le nœud peut être configuré lors de la création de HDInsight. Pour plus d’informations sur les disques gérés, consultez [Azure Managed Disks](../storage/storage-managed-disks-overview.md).

    Pour plus d’informations sur la configuration des disques gérés avec Kafka sur HDInsight, consultez [Increase scalability of Kafka on HDInsight](hdinsight-apache-kafka-scalability.md) (Augmenter l’évolutivité de Kafka sur HDInsight).

## <a name="use-cases"></a>Cas d'utilisation

* **Messagerie** : dans la mesure où Kafka prend en charge le modèle de messagerie publication/abonnement, il est souvent utilisé comme courtier de messages.

* **Suivi des activités** : étant donné que Kafka fournit la journalisation dans l’ordre des enregistrements, il peut être utilisé pour effectuer le suivi et recréer des activités. Par exemple, les actions de l’utilisateur sur un site web ou dans une application.

* **Agrégation** : avec le traitement de flux de données, vous pouvez agréger des informations à partir de différents flux afin de combiner et de centraliser les informations dans des données opérationnelles.

* **Transformation** : avec le traitement de flux de données, vous pouvez combiner et enrichir les données à partir de plusieurs rubriques d’entrée dans une ou plusieurs rubriques de sortie.

## <a name="next-steps"></a>Étapes suivantes

Cliquez sur les liens suivants pour apprendre à utiliser Apache Kafka sur HDInsight :

* [Prise en main de Kafka sur HDInsight](hdinsight-apache-kafka-get-started.md)

* [Utilisation de MirrorMaker pour créer un réplica de Kafka sur HDInsight](hdinsight-apache-kafka-mirroring.md)

* [Use Apache Storm with Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)

* [Use Apache Spark with Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md) (Utilisation d’Apache Spark avec Kafka sur HDInsight)

* [Connect to Kafka through an Azure Virtual Network](hdinsight-apache-kafka-connect-vpn-gateway.md) (Se connecter à Kafka via un réseau virtuel Azure)
