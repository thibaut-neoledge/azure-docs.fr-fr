---
title: "Création de clusters Hadoop basés sur Windows dans HDInsight à l&quot;aide de l’interface de ligne de commande Azure"
description: "Apprenez à créer des clusters Hadoop Windows pour Azure HDInsight à l’aide l’interface de ligne de commande Azure"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c766544c-c16f-4bfa-89d0-592325d24250
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a2b32f23381ed1f9912edf6432f029e51bdf1be4
ms.openlocfilehash: 393b7e44b21fe510e07b4048ddd3bdbcc31d90a9
ms.lasthandoff: 02/07/2017


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-cli"></a>Création de clusters Hadoop basés sur Windows dans HDInsight à l'aide de l’interface de ligne de commande Azure

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Apprenez à créer des clusters Hadoop Windows dans HDInsight à l’aide l’interface de ligne de commande Azure. 

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date). Les informations mentionnées dans cet article s’appliquent uniquement aux clusters HDInsight basés sur Windows. Pour plus d’informations sur la création de clusters Linux, consultez [Création de clusters Hadoop dans HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="prerequisites"></a>Configuration requise :
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

* **Abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Interface de ligne de commande Azure**.
  
[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="connect-to-azure"></a>Connexion à Azure
Utilisez la commande suivante pour vous connecter à Azure :

    azure login

Pour plus d’informations sur l’authentification à l’aide d’un compte professionnel ou scolaire, consultez la section [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure](../xplat-cli-connect.md).

Utilisez la commande suivante pour passer en mode Azure Resource Manager :

    azure config mode arm

Pour obtenir de l’aide, utilisez le commutateur **-h** .  Par exemple :

    azure hdinsight cluster create -h

## <a name="create-clusters"></a>Créer des clusters
Avant de pouvoir créer un cluster HDInsight, vous devez disposer d’un groupe Resource Management et d’un compte de stockage d’objets Blob Azure. Pour créer un cluster HDInsight, vous devez indiquer les éléments suivants :

* **Groupe de ressources Azure** : un compte Data Lake Analytics doit être créé au sein d’un groupe de ressources Azure. Azure Resource Manager vous permet de manipuler les ressources de votre application sous la forme d’un groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée.
  
    Pour répertorier les groupes de ressources dans votre abonnement :
  
        azure group list
  
    Pour créer un groupe de ressources :
  
        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Nom du cluster HDInsight**
* **Emplacement**: un des centres de données Azure prenant en charge des clusters HDInsight. Pour obtenir la liste des emplacements pris en charge, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
* **Compte de stockage par défaut**: HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage est nécessaire avant de pouvoir créer un cluster HDInsight.
  
    Pour créer un compte de stockage Azure :
  
        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS
  
  > [!NOTE]
  > Il doit se trouver dans le même centre de données que le cluster HDInsight.
  > Le type de compte de stockage ne peut pas être ZRS, car ZRS ne prend pas en charge de table.
  > 
  > 
  
    Pour plus d’informations sur la création d’un compte Azure Storage au moyen du portail Azure, consultez la rubrique [Création, gestion ou suppression d’un compte de stockage][azure-create-storageaccount].
  
    Si vous disposez déjà d’un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :
  
        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"
  
    Pour en savoir plus sur l’obtention d’informations au moyen du portail Azure, consultez la section « Gérer votre compte de stockage » de la rubrique [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md#manage-your-storage-account).
* **(facultatif) Conteneur d'objets blob par défaut** : la commande **azure hdinsight cluster create** crée le conteneur s'il n'existe pas. Si vous avez choisi de créer le conteneur au préalable, vous pouvez utiliser la commande suivante :
  
    azure storage container create --account-name « <Storage Account Name> » --account-key <Storage Account Key> [ContainerName]

Lorsque le compte de stockage est prêt, vous êtes prêt à créer un cluster :

    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


## <a name="create-clusters-using-configuration-files"></a>Créer des clusters à l'aide de fichiers de configuration
Généralement, vous créez un cluster HDInsight, y exécutez des tâches, puis le supprimez afin de réduire les coûts. L'interface de ligne de commande vous donne la possibilité d'enregistrer les configurations dans un fichier, de sorte que vous pouvez les réutiliser chaque fois que vous créez un cluster.  

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

Exemple : créer un fichier de configuration qui contient une action de script à exécuter lors de la création d’un cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

## <a name="create-clusters-with-script-action"></a>Créer des clusters à l’aide d’une action de script
Créez un fichier de configuration qui contient une action de script à exécuter lors de la création d’un cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Créer un cluster avec une action de script

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


Pour plus d’informations sur les actions de script, consultez [Personnaliser des clusters HDInsight à l’aide d’une action de script (Linux)](hdinsight-hadoop-customize-cluster.md).

## <a name="create-clusters-using-resource-manager-templates"></a>Créer des clusters à l’aide de modèles Resource Manager
Vous pouvez utiliser l’interface de ligne de commande pour créer des clusters en appelant des modèles Azure Resource Manager. Consultez [Déploiement avec l’interface de ligne de commande Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## <a name="see-also"></a>Voir aussi
* [Prise en main d’Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) : apprenez à utiliser votre cluster HDInsight
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md) : découvrez comment envoyer des tâches par programme à HDInsight
* [Gestion des clusters Hadoop dans HDInsight à l'aide du portail Azure](hdinsight-administer-use-command-line.md)
* [Utilisation de l’interface de ligne de commande Microsoft Azure pour Mac, Linux et Windows avec Microsoft Azure Service Management.](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)


