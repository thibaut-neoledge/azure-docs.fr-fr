---
title: "Résolution de problèmes YARN - Azure HDInsight | Microsoft Docs"
description: "Utilisez le FAQ Yarn pour obtenir des réponses aux questions courantes sur l’utilisation de Yarn sur la plateforme Azure HDInsight."
keywords: "Azure HDInsight, Yarn, FAQ, guide de dépannage, questions courantes"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 576d6bfe9697d2abd6f84824960dad62e9ed3c11
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017


---

# <a name="yarn-troubleshooting"></a>Résolution de problèmes YARN

Cet article décrit les principaux problèmes rencontrés lors de l’utilisation de charges utiles YARN dans Apache Ambari, et leur résolution.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Comment créer une file d’attente Yarn sur un cluster

### <a name="issue"></a>Problème :

Créer une file d’attente Yarn avec allocation de capacité sur le cluster HDInsight.  

### <a name="resolution-steps"></a>Étapes de résolution : 

Effectuez les étapes suivantes via Ambari pour créer une file d’attente Yarn et équilibrer l’allocation de capacité entre toutes les files d’attente. 

Dans cet exemple, la capacité de deux files d’attente existantes (file par défaut et thriftsvr) est modifiée de 50 % à 25 %, ce qui permet à la nouvelle file d’attente (Spark) de bénéficier d’une capacité de 50 %.
| File d'attente | Capacité | Bande passante Capacité |
| --- | --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |
1. Cliquez sur l’icône Ambari Views (Vues Ambari), en forme de grille, puis choisissez **Yarn Queue Manager** (Gestionnaire de files d’attente Yarn).

    ![Cliquer sur l’icône Ambari Views](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
1. Sélectionnez la file d’attente **default** (par défaut).

    ![Sélectionner la file d’attente par défaut](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
1. Modifiez la valeur **Capacity** (Capacité) de 50 % à 25 % pour la file d’attente **default** (par défaut) et faites de même pour la file d’attente **thriftsvr**.

    ![Définir la capacité sur 25 % pour les files d’attente par défaut et thriftsvr](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
1. Cliquez sur **Add Queue** (Ajouter une file d’attente) pour créer une file d’attente.

    ![Cliquer sur Add Queue (Ajouter une file d’attente)](media/hdinsight-troubleshoot-yarn/create-queue-4.png)
1. Nommez la nouvelle file d’attente.

    ![Indiquer Spark comme nom de file d’attente](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  
1. Conservez la valeur de **capacité** de 50 % et cliquez sur le bouton **Actions**.

    ![Cliquer sur le bouton Actions](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
1. Choisissez **Save and Refresh Queues** (Enregistrer et actualiser les files d’attente).

    ![Choisir Save and Refresh Queues (Enregistrer et actualiser les files d’attente)](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Ces modifications se répercutent immédiatement sur l’interface utilisateur du planificateur Yarn.

### <a name="further-reading"></a>Pour aller plus loin :

- [Yarn Capacity Scheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) (Planificateur de capacité Yarn, en anglais)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Comment télécharger les journaux Yarn à partir d’un cluster

#### <a name="issue"></a>Problème :

Vous avez besoin de télécharger le journal maître et d’autres journaux de conteneurs Yarn à partir du cluster HDInsight.  

#### <a name="resolution-steps"></a>Étapes de résolution : 

1. Connectez-vous au cluster HDInsight à l’aide d’un client Secure Shell (SSH) (consultez la section Pour aller plus loin ci-dessous).
1. Dressez la liste des ID de toutes les applications Yarn en cours d’exécution à l’aide de la commande suivante :

```apache
yarn top
```
Les ID sont répertoriés dans la colonne `APPLICATIONID` dont les journaux doivent être téléchargés.

```apache
YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                  APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
 application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
 application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
```

Télécharger les journaux de conteneurs Yarn pour tous les processus maîtres d’application à l’aide de la commande suivante :
   
```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
```

Le fichier journal nommé `amlogs.txt` est créé au format texte. 

Téléchargez les journaux de conteneurs Yarn uniquement pour les processus maîtres d’application les plus récents à l’aide de la commande suivante :

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
```

Le fichier journal nommé `latestamlogs.txt` est créé au format texte. 

Télécharger les journaux de conteneurs YARN pour les deux premiers processus maîtres d’application à l’aide de la commande suivante :

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
```

Le fichier journal nommé `first2amlogs.txt` est créé au format texte. 

Téléchargez tous les journaux de conteneurs YARN à l’aide de la commande suivante :

```apache
yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
```

Le fichier journal nommé `logs.txt` est créé au format texte. 

Téléchargez le journal de conteneur Yarn pour un conteneur particulier à l’aide de la commande suivante :

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
```

Le fichier journal nommé `containerlogs.txt` est créé au format texte.

#### <a name="further-readings"></a>Pour aller plus loin :

- [Se connecter à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

- [Apache Hadoop Yarn concepts and applications](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/) (Concepts et applications Apache Hadoop Yarn, en anglais)









