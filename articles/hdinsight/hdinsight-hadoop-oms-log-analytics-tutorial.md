---
title: Utiliser Log Analytics pour surveiller les clusters Azure HDInsight | Microsoft Docs
description: "Découvrez comment utiliser Azure Log Analytics pour surveiller les travaux en cours d’exécution dans un cluster HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: dbd3d0ed4337d4fe86465c5c59bf20c0a50a87b4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Utiliser Azure Log Analytics pour surveiller les clusters HDInsight (préversion)

Cet article explique comment utiliser Azure Log Analytics pour surveiller les opérations de clusters Hadoop HDInsight.

Log Analytics est un service d’[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) qui surveille vos environnements cloud et locaux et assure leur disponibilité et leurs performances. Il collecte les données générées par les ressources de votre cloud et de vos environnements locaux et d’autres outils d’analyse pour fournir une analyse sur plusieurs sources. 

## <a name="prerequisites"></a>Composants requis

* **Un abonnement Azure**. Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Voir [Créez votre compte Azure gratuit](https://azure.microsoft.com/free).

* Un **cluster Azure HDInsight**. Vous pouvez actuellement utiliser Azure OMS avec les types de cluster HDInsight suivants :
    * Hadoop
    * Spark
    * HBase
    * Storm
    * Kafka
    * Hive interactif

    Pour savoir comment créer un cluster HDInsight, consultez [Prise en main d’Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).


* **Un espace de travail Log Analytics**. Considérez cet espace de travail comme un environnement Log Analytics unique avec son propre référentiel de données, et ses propres sources de données et solutions. Vous devez déjà avoir créé un espace de travail de ce type que vous pouvez associer aux clusters Azure HDInsight. Pour obtenir des instructions, consultez [Créer un espace de travail Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-azure-log-analytics"></a>Configuration d’un cluster HDInsight pour utiliser Azure Log Analytics

Cette section vous explique comment configurer un cluster Hadoop HDInsight existant pour utiliser un espace de travail Azure Log Analytics pour surveiller les travaux, les journaux de débogage, etc.

1. Dans le portail Azure, dans le volet gauche, cliquez sur **Clusters HDInsight**, puis sur le nom du cluster que vous souhaitez configurer avec Azure Log Analytics.

2. Dans le panneau de cluster, dans le volet gauche, cliquez sur **Surveillance**.

3. Dans le volet droit, cliquez sur **Activer**, puis sélectionnez un espace de travail Log Analytics. Cliquez sur **Enregistrer**.

    ![Activer la surveillance des clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Activer la surveillance des clusters HDInsight")

4. Une fois le cluster configuré pour utiliser Log Analytics pour la surveillance, l’option **Ouvrir le tableau de bord OMS** s’affiche en haut de l’onglet. Cliquez sur le bouton correspondant.

    ![Ouvrir le tableau de bord OMS](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Ouvrir le tableau de bord OMS")

5. Entrez vos informations d’identification Azure si vous êtes invité à le faire. Vous êtes dirigé vers le tableau de bord Microsoft OMS.

    ![Portail Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Portail Operations Management Suite")

## <a name="next-steps"></a>Étapes suivantes
* [Ajouter des solutions de gestion de clusters HDInsight à Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
