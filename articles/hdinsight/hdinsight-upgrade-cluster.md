---
title: Effectuer la migration de HDInsight Windows vers HDInsight Azure | Microsoft Docs
description: "Apprenez à effectuer la migration d’un cluster HDInsight Windows vers un cluster HDInsight Linux."
services: hdinsight
documentationcenter: 
author: bhanupr
editor: bhanupr
ms.assetid: 
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2017
ms.author: bhanupr
translationtype: Human Translation
ms.sourcegitcommit: 8c59375290e410c34ba25d4e5d8e8f9f8de0cafe
ms.openlocfilehash: ceb5f5c639633d7118a057927b236b51b54f8fa7


---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Mettre à niveau le cluster HDInsight
Pour tirer parti des dernières fonctionnalités proposées par HDInsight, nous vous recommandons de mettre à niveau les clusters HDInsight vers la version la plus récente. Suivez les instructions ci-dessous pour mettre à niveau vos clusters HDInsight.

> [!NOTE]
> Les clusters HDInsight version 3.2 et 3.3 seront bientôt obsolètes. Pour obtenir des informations sur les versions HDInsight prises en charge, consultez [Quels sont les différents composants Hadoop disponibles avec HDInsight ?](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

## <a name="upgrade-tasks"></a>Tâches de mise à niveau
Le workflow pour mettre à niveau un cluster HDInsight est le suivant :

![Schéma du workflow de mise à niveau](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Lisez chaque section de ce document pour comprendre les modifications qui peuvent être nécessaires lors de la mise à jour de votre cluster HDInsight.
2. Créez un cluster comme environnement de test ou d’assurance qualité. Pour plus d’informations sur la création d’un cluster, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
3. Copiez les travaux, sources de données et récepteurs existants dans le nouvel environnement. Pour plus d’informations, consultez la section [Copier des données dans l’environnement de test](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment).
4. Effectuez des tests de validation pour vérifier que vos tâches fonctionnent comme prévu sur le nouveau cluster.


Une fois que vous avez vérifié que tout fonctionne comme prévu, planifiez un temps d’arrêt pour la migration. Pendant ce temps d’arrêt, effectuez les actions suivantes :

1.  Sauvegardez toutes les données temporaires stockées localement sur les nœuds du cluster, par exemple si vous avez des données stockées directement sur un nœud principal.
2.  Supprimez le cluster existant.
3.  Créez un cluster dans le même sous-réseau de réseau virtuel avec la version HDI la plus récente (ou prise en charge) à l’aide du même magasin de données par défaut que le cluster précédent a utilisé. Cela permet au nouveau cluster de continuer à travailler sur vos données de production existantes.
4.  Importez toutes les données temporaires que vous avez sauvegardées.
5.  Démarrez des tâches ou poursuivez le traitement avec le nouveau cluster.

## <a name="next-steps"></a>Étapes suivantes
* [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
* [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md)




<!--HONumber=Feb17_HO1-->


