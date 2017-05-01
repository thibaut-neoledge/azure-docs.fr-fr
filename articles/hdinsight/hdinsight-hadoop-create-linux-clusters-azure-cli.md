---
title: "Créer Azure HDInsight (Hadoop) à l’aide de la ligne de commande | Microsoft Docs"
description: "Apprenez à créer des clusters HDInsight à l’aide de l’interface multiplateforme Azure CLI 1.0."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 995aacb5d243eb50df0cf35e68a1a931bd010c53
ms.lasthandoff: 04/11/2017


---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Créer des clusters HDInsight à l’aide de l’interface de ligne de commande Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Les étapes de cette procédure présentent la création d’un cluster HDInsight 3.5 à l’aide d’Azure CLI 1.0.

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence d’HDInsight 3.2 et 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).


## <a name="prerequisites"></a>Composants requis

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Interface de ligne de commande Azure**. Les étapes décrites dans ce document ont été testées pour la dernière fois avec la version 0.10.1 de l’interface de ligne de commande Azure.

    > [!IMPORTANT]
    > Les étapes décrites dans ce document ne fonctionnent pas avec Azure CLI 2.0. Azure CLI 2.0 ne prend pas en charge la création d’un cluster HDInsight.

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="log-in-to-your-azure-subscription"></a>Connexion à votre abonnement Azure

Suivez les étapes décrites dans [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)](../xplat-cli-connect.md) et connectez-vous à votre abonnement à l’aide de la méthode **login** .

## <a name="create-a-cluster"></a>Créer un cluster

La procédure suivante doit être effectuée à partir d’une session d’invite de commande, shell ou terminal après l’installation et la configuration de l’interface CLI Azure.

1. Utilisez la commande suivante pour vous identifier auprès de votre abonnement Azure :

        azure login

    Vous êtes invité à fournir votre nom et mot de passe. Si vous possédez plusieurs abonnements Azure, utilisez `azure account set <subscriptionname>` pour définir l’abonnement que les commandes CLI Azure doivent utiliser.

2. Passez en mode Gestionnaire de ressources Azure en exécutant la commande suivante :

        azure config mode arm

3. Créez un groupe de ressources. Ce groupe de ressources contient le cluster HDInsight et le compte de stockage associé.

        azure group create groupname location

    * Remplacez **groupname** par un nom unique pour le groupe.

    * Remplacez **location** par la région géographique dans laquelle vous souhaitez créer le groupe.

       Pour obtenir la liste des emplacements valides, utilisez la commande `azure location list` , puis un des emplacements de la colonne **Nom** .

4. Créez un compte de stockage. Ce compte de stockage est utilisé comme espace de stockage par défaut pour le cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Remplacez **groupname** par le nom du groupe créé à l’étape précédente.

    * Remplacez **location** par le même emplacement que celui utilisé à l’étape précédente.

    * Remplacez **storagename** par un nom de compte de stockage unique.

        > [!NOTE]
        > Pour plus d’informations sur les paramètres utilisés dans cette commande, utilisez `azure storage account create -h` pour afficher l’aide relative à cette commande.

5. Récupérez la clé utilisée pour accéder au compte de stockage.

        azure storage account keys list -g groupname storagename

    * Remplacez **groupname** par le nom du groupe de ressources.
    * Remplacez **storagename** par le nom du compte de stockage.

     Dans les données renvoyées, enregistrez la valeur **key** pour **key1**.

6. Créez un cluster HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Remplacez **groupname** par le nom du groupe de ressources.

    * Remplacez **Hadoop** avec le type de cluster que vous souhaitez créer. Par exemple, Hadoop, HBase, Storm ou Spark.

     > [!IMPORTANT]
     > Il existe différents types de clusters HDInsight correspondant à la charge de travail ou à la technologie pour laquelle ils sont utilisés. Il n’existe aucune méthode prise en charge pour créer un cluster combinant plusieurs types, tels que Storm et HBase sur un seul cluster.

    * Remplacez **location** par le même emplacement que celui utilisé lors des étapes précédentes.

    * Remplacez **storagename** par le nom du compte de stockage.

    * Remplacez **storagekey** par la clé obtenue à l’étape précédente.

    * Pour le paramètre `--defaultStorageContainer` , utilisez le même nom que celui utilisé pour le cluster.

    * Remplacez **admin** et **httppassword** par le nom et le mot de passe à utiliser lors de l’accès au cluster via HTTPS.

    * Remplacez **sshuser** ed **sshuserpassword** par le nom d’utilisateur et le mot de passe à utiliser lors de l’accès au cluster via SSH.

    > [!IMPORTANT]
    > Cet exemple crée un cluster avec deux nœuds de travail. Si vous envisagez d’utiliser plus de 32 nœuds de travail (lors de la création ou de la mise à l’échelle du cluster), vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM. Vous pouvez définir la taille du nœud principal à l’aide du paramètre `--headNodeSize`.
    >
    > Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Le processus de création de cluster peut prendre plusieurs minutes. En règle générale, il dure environ 15 minutes.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un cluster HDInsight à l’aide de l’interface de ligne de commande Azure. Pour apprendre à l’utiliser, consultez les rubriques ci-dessous :

### <a name="hadoop-clusters"></a>Clusters Hadoop

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters HBase

* [Prise en main de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java pour HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters Storm

* [Développement de topologies Java pour Storm sur HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilisation de composants Python dans Storm sur HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

