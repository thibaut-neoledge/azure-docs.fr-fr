---
title: Surveiller les performances du cluster - Azure HDInsight | Microsoft Docs
description: "Découvrez comment surveiller la capacité et les performances d’un cluster HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: f2333202cdccc82edea649ff1c295752a414c8b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-cluster-performance"></a>Surveiller les performances du cluster

La surveillance de l’intégrité et des performances d’un cluster HDInsight est essentielle à l’optimisation continue des performances et de l’utilisation des ressources. Cette surveillance peut également vous aider à corriger les éventuelles erreurs de codage ou de configuration du cluster.

Les sections ci-après vous indiquent comment optimiser la charge du cluster, l’efficacité des files d’attente YARN et l’accessibilité du stockage.

## <a name="cluster-loading"></a>Charge du cluster

Les clusters Hadoop doivent équilibrer la charge entre les différents nœuds du cluster. Cet équilibrage permet d’éviter que les tâches de traitement soient limitées par les ressources de RAM, de processeur ou de disque.

Pour obtenir une vue d’ensemble des nœuds de votre cluster et de leur charge, connectez-vous à [l’interface utilisateur web Ambari](hdinsight-hadoop-manage-ambari.md), puis sélectionnez l’onglet **Hosts** (Hôtes). Vos hôtes sont répertoriés par leur nom de domaine complet. L’état de fonctionnement de chaque hôte est spécifié par un indicateur d’intégrité en couleur :

| Couleur | Description |
| --- | --- |
| Rouge | Au moins un composant maître de l’hôte est défaillant. Pointez sur l’indicateur pour visualiser une info-bulle répertoriant les composants concernés. |
| Orange | Au moins un composant subordonné de l’hôte est défaillant. Pointez sur l’indicateur pour visualiser une info-bulle répertoriant les composants concernés. |
| Jaune | Le serveur Ambari n’a reçu aucune pulsation de l’hôte depuis plus de 3 minutes. |
| Vert | L’état de fonctionnement est normal. |

Le tableau de bord comporte également des colonnes indiquant le nombre de cœurs et la quantité de RAM de chaque hôte, ainsi que l’utilisation du disque et la charge moyenne.

![Onglet Hosts (Hôtes)](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Sélectionnez l’un des noms d’hôte pour obtenir une vue d’ensemble détaillée des composants en cours d’exécution sur cet hôte et de leurs métriques. Les métriques sont présentés en fonction d’une chronologie configurable et concernent l’utilisation du processeur, la charge, l’utilisation du disque, l’utilisation de la mémoire, l’utilisation du réseau et les nombres de processus.

![Détails d’un hôte](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Pour plus d’informations sur la configuration d’alertes et sur la visualisation des métriques, consultez l’article [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="yarn-queue-configuration"></a>Configuration des files d’attente YARN

Hadoop exécute différents services sur sa plateforme distribuée. La plateforme YARN (Yet Another Resource Negotiator) coordonne ces services, alloue les ressources du cluster et gère l’accès à un jeu de données commun.

YARN répartit les deux responsabilités du JobTracker, à savoir la gestion des ressources et la planification/surveillance des travaux, entre deux démons : un démon ResourceManager global, et un démon ApplicationMaster (AM) par application.

Le démon ResourceManager est un *planificateur pur*, et arbitre uniquement la répartition des ressources disponibles entre toutes les applications en concurrence. Le ResourceManager garantit l’utilisation systématique de la totalité des ressources, optimisant ainsi différentes constantes telles que les Contrats de niveau de service (SLA), les garanties de capacité, et ainsi de suite. Le démon ApplicationMaster négocie les ressources auprès de ResourceManager et fonctionne avec les NodeManagers pour exécuter et surveiller les conteneurs et leur consommation des ressources.

Lorsque plusieurs locataires partagent un cluster volumineux, ils entrent en concurrence pour les ressources du cluster. Le CapacityScheduler est un planificateur enfichable qui facilite le partage des ressources en plaçant les requêtes en file d’attente. Le CapacityScheduler prend également en charge les *files d’attente hiérarchiques* pour garantir le partage des ressources entre les files d’attente secondaires d’une organisation, avant que les files d’attente des autres applications soient autorisées à utiliser les ressources disponibles.

YARN nous permet également d’allouer des ressources à ces files d’attente et vous indique si toutes vos ressources disponibles sont ou non attribuées. Pour visualiser les informations concernant vos files d’attente, connectez-vous à l’interface utilisateur web Ambari, puis sélectionnez **YARN Queue Manager** (Gestionnaire de files d’attente YARN) dans le menu supérieur.

![Gestionnaire de files d’attente YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

La page YARN Queue Manager (Gestionnaire de files d’attente YARN) répertorie vos files d’attente sur la gauche et indique le pourcentage de capacité attribué à chacune.

![Page de détails du Gestionnaire de files d’attente](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Pour obtenir une vue d’ensemble plus détaillée de vos files d’attente, dans le tableau de bord Ambari, sélectionnez le service **YARN** (YARN) dans la liste de gauche. Ensuite, dans le menu déroulant **Quick Links** (Liens rapides), sélectionnez **ResourceManager UI** (Interface utilisateur ResourceManager) sous votre nœud actif.

![Lien du menu de l’interface utilisateur ResourceManager](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Dans l’interface utilisateur ResourceManager, sélectionnez **Scheduler** (Planificateur) dans le menu de gauche. La liste de vos files d’attente s’affiche sous *Application Queues* (Files d’attente d’application). Cette zone présente la capacité utilisée pour chacune de vos files d’attente, ainsi que l’efficacité de la répartition des travaux entre ces files d’attente, et indique si des travaux sont limités en ressources.

![Lien du menu de l’interface utilisateur ResourceManager](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Limitation du stockage

Un goulot d’étranglement des performances d’un cluster peut survenir au niveau du stockage. Ce type de goulot d’étranglement est généralement dû au *blocage* des opérations d’entrée/sortie (E/S), qui se produit lorsque vos tâches en cours d’exécution envoient plus d’E/S que le service de stockage ne peut en traiter. Ce blocage crée une file d’attente des requêtes d’E/S en attente de traitement, le temps que les E/S actuelles soient traitées. Les blocages découlent d’une *limitation du stockage*, qui ne constitue pas une limite physique, mais plutôt une limite imposée par le service de stockage en vertu d’un contrat de niveau de service (SLA). Cette limite évite tout risque qu’un client ou locataire monopolise le service. Le SLA limite le nombre d’E/S par seconde (IOPS) pour le stockage Azure. Pour plus d’informations, consultez l’article [Objectifs de performance et d’extensibilité du Stockage Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Si vous utilisez le service Stockage Azure et que vous souhaitez en savoir plus sur la surveillance des problèmes liés au stockage, notamment la limitation, consultez l’article [Surveiller, diagnostiquer et résoudre les problèmes liés à Stockage Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Si le magasin de stockage de votre cluster est Azure Data Lake Store (ADLS), votre problème de limitation découle probablement des limites de bande passante. Dans ce cas, vous pouvez identifier la limitation en consultant les erreurs de limitation consignées dans les journaux des tâches. Pour ADLS, consultez la section sur la limitation relative au service approprié dans les articles suivants :

* [Recommandations en matière d’optimisation des performances pour Hive sur HDInsight et Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Recommandations en matière d’optimisation des performances pour MapReduce sur HDInsight et Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Recommandations en matière d’optimisation des performances pour Storm sur HDInsight et Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes et la surveillance de vos clusters, cliquez sur les liens suivants :

* [Analyse des journaux de HDInsight](hdinsight-debug-jobs.md)
* [Déboguer des applications avec des journaux YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Activer les dumps de tas pour les services Hadoop sur HDInsight sur Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
