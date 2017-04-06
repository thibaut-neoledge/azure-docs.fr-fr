---
title: "Personnaliser des clusters HDInsight à l&quot;aide d’actions de script | Microsoft Docs"
description: "Découvrez comment ajouter des composants personnalisés à des clusters HDInsight basés sur Linux à l’aide des actions de script. Les actions de script sont des scripts Bash qui s’exécutent sur les nœuds de cluster et qui peuvent être utilisés pour personnaliser la configuration du cluster ou pour ajouter d’autres services et utilitaires comme Hue, Solr ou R."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1f64380b0bd271658c0f06e01a9b013ae4e25636
ms.lasthandoff: 03/25/2017


---
# <a name="customize-linux-based-hdinsight-clusters-using-script-action"></a>Personnalisation de clusters HDInsight basés sur Linux à l'aide d'une action de script

HDInsight fournit une option de configuration intitulée **Action de script** , qui appelle des scripts personnalisés pouvant personnaliser le cluster. Ces scripts peuvent être utilisés pendant la création du cluster ou sur un cluster déjà en cours d’exécution et servent à installer des composants supplémentaires ou à modifier les paramètres de configuration.

> [!IMPORTANT]
> La possibilité d’utiliser les actions de script sur un cluster déjà en cours d’exécution est uniquement disponible pour les clusters HDInsight sous Linux.
>
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).


Des actions de script peuvent également être publiées sur Azure Marketplace en tant qu’application HDInsight. Certains des exemples de ce document montrent comment vous pouvez installer une application HDInsight à l’aide de commandes d’action de script à partir de PowerShell et du Kit de développement logiciel (SDK) .NET. Pour plus d’informations sur les applications HDInsight, consultez [Publier des applications HDInsight dans Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Autorisations

Si vous utilisez un cluster HDInsight joint à un domaine, deux autorisations Ambari sont requises lorsque vous utilisez des actions de script avec le cluster :

* **AMBARI.RUN\_CUSTOM\_COMMAND** : le rôle Administrateur d’Ambari dispose de cette autorisation par défaut.
* **CLUSTER.RUN\_CUSTOM\_COMMAND** : l’Administrateur de cluster HDInsight et l’Administrateur Ambari disposent de cette autorisation par défaut.

Pour plus d’informations sur l’utilisation des autorisations avec un cluster HDInsight joint à un domaine, consultez [Gestion des clusters HDInsight joints à un domaine](hdinsight-domain-joined-manage.md).

## <a name="access-control"></a>Contrôle d’accès

Si vous utilisez un abonnement Azure dans lequel vous n’êtes pas administrateur/propriétaire, par exemple dans le cas d’un abonnement appartenant à une société, vous devez vérifier que votre compte Azure possède au moins l’accès **Collaborateur** pour le groupe de ressources Azure qui contient le cluster HDInsight.

De plus, si vous créez un cluster HDInsight, une personne disposant d’au moins un accès **Collaborateur** à l’abonnement Azure doit avoir auparavant enregistré le fournisseur pour HDInsight. L’enregistrement du fournisseur se produit lorsqu’un utilisateur disposant d’un accès Contributeur à l’abonnement crée une ressource pour la première fois sur l’abonnement. Vous pouvez également faire cela sans créer une ressource en [enregistrant un fournisseur à l’aide de REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Pour plus d’informations sur le fonctionnement de la gestion des accès, consultez les documents suivants :

* [Prise en main de la gestion des accès dans le portail Azure](../active-directory/role-based-access-control-what-is.md)
* [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../active-directory/role-based-access-control-configure.md)

## <a name="understanding-script-actions"></a>Comprendre les actions de script

Une action de script est tout simplement un script Bash auquel vous apportez une URI et des paramètres. Le script s’exécute sur les nœuds du cluster HDInsight. Les éléments suivants constituent les caractéristiques et les fonctionnalités des actions de script.

* Elles doivent être stockées sur un URI accessible à partir du cluster HDInsight. Voici les emplacements de stockage possibles :

    * Un compte **Azure Data Lake Store** accessible via le cluster HDInsight. Pour plus d’informations sur l’utilisation d'Azure Data Lake Store avec HDInsight, voir [Créer un cluster HDInsight avec Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

        Lorsque vous utilisez un script stocké dans Data Lake Store, le format d’URI est `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > Le principal de service que HDInsight utilise pour accéder au Data Lake Store doit avoir accès en lecture au script.

    * Un blob est un **compte de stockage Azure** considéré comme compte de stockage principal ou supplémentaire pour le cluster HDInsight. Dans la mesure où HDInsight peut accéder à ces deux types de comptes de stockage lors de la création du cluster, une action de script de non publique peut être utilisée.

    * Un service de partage de fichiers public, comme un Blob Azure, GitHub, OneDrive, Dropbox, etc.

        Pour obtenir des exemples d’URI pour les scripts stockés dans le conteneur d’objets blob (lisibles publiquement), consultez la section [Exemple de script d’action de script](#example-script-action-scripts) .

        > [!WARNING]
        > HDInsight prend uniquement en charge les comptes de stockage Azure à __usage général__. Il ne prend actuellement pas en charge le type de compte __stockage Blob__.

* Ils peuvent être limités de manière à **s’exécuter sur certains types de nœuds uniquement**, par exemple des nœuds principaux ou des nœuds de travail (worker).

  > [!NOTE]
  > Lorsqu’il est utilisé avec HDInsight Premium, vous pouvez spécifier que le script doit être utilisé sur le nœud de périphérie.

* Son état peut être **persistant** ou **ad hoc**.

    Les scripts **persistants** sont appliqués aux nœuds Worker et automatiquement exécutés sur les nœuds créés lors de la mise à l’échelle d’un cluster.

    Un script persistant peut également apporter des modifications à un autre type de nœud, tel que le nœud principal. Cependant, la seule raison d’utiliser un script persistant, d’un point de vue fonctionnel, est de l’appliquer aux nœuds de travail créés lorsqu’un cluster est monté en charge.

  > [!IMPORTANT]
  > Les actions de script persistantes doivent avoir un nom unique.

    Les scripts **ad hoc** ne sont pas persistants. Vous pouvez toutefois promouvoir un script ad hoc en script persistant ou abaisser un script persistant en script ad hoc.

  > [!IMPORTANT]
  > Les actions de script utilisées lors de la création du cluster sont automatiquement rendues persistantes.
  >
  > Les scripts qui échouent ne sont pas rendus persistants, même si vous précisez qu’ils doivent l’être.

* Ils peuvent accepter les **paramètres** utilisés par le script lors de l’exécution.
* Ils sont exécutés avec des **privilèges au niveau de la racine** sur les nœuds du cluster.
* Ils peuvent être utilisés par le biais du **portail Azure**, **d’Azure PowerShell**, de **l’interface de ligne de commande Azure (CLI)** ou du **Kit de développement logiciel (SDK) HDInsight .NET**

Le cluster conserve l’historique de tous les scripts ayant été exécutés ; cela vous aide à comprendre quels scripts ont été appliqués à un cluster et à déterminer les ID des scripts devant faire l’objet de promotion ou de rétrogradation.

> [!IMPORTANT]
> Il n’existe aucune méthode automatique pour annuler les modifications apportées par une action de script. Pour annuler les résultats d’un script, vous devez d’abord comprendre quelles modifications ont été apportées, puis les annuler manuellement (ou fournir une action de script qui les annule).


### <a name="script-action-in-the-cluster-creation-process"></a>Action de script dans le processus de création de cluster

Les actions de script utilisées lors de la création du cluster sont légèrement différentes des actions de script exécutées sur un cluster existant :

* Le script est **automatiquement rendu persistant**.
* Un **échec** dans le script peut provoquer l’échec du processus de création du cluster.

Le diagramme suivant illustre le moment de l’exécution de l’action de script pendant le processus de création :

![Personnalisation du cluster HDInsight et procédure de création d’un cluster][img-hdi-cluster-states]

Le script est exécuté pendant la configuration de HDInsight. À ce stade, le script s’exécute en parallèle sur tous les nœuds spécifiés dans le cluster, et s’exécute avec des privilèges root complets sur les nœuds.

> [!NOTE]
> Étant donné que le script est exécuté avec des privilèges root sur les nœuds du cluster, vous pouvez effectuer des opérations comme arrêter et démarrer des services, notamment des services liés à Hadoop. Si vous arrêtez les services, vous devez vous assurer que le service Ambari et d’autres services liés à Hadoop sont en cours d’exécution avant la fin de l’exécution du script. Ces services sont requis pour établir correctement l’intégrité et l’état du cluster pendant sa création.


Pendant la création du cluster, vous pouvez spécifier plusieurs actions de script qui sont appelées dans l’ordre spécifié.

> [!IMPORTANT]
> Les actions de script doivent se terminer dans les 60 minutes, faute de quoi elles expirent. Lors de l’approvisionnement du cluster, le script est exécuté en même temps que les autres processus d’installation et de configuration. En raison de cette concurrence pour les ressources, par exemple au niveau du temps processeur ou de la bande passante, l’exécution du script risque de prendre plus de temps que dans votre environnement de développement.
>
> Pour réduire le temps nécessaire à l’exécution du script, évitez les tâches comme le téléchargement et la compilation d’applications à partir de la source. Au lieu de cela, précompilez l’application et stockez le fichier binaire dans le stockage Azure afin de pouvoir le télécharger rapidement vers le cluster.


### <a name="script-action-on-a-running-cluster"></a>Action de script sur un cluster en cours d’exécution

Contrairement aux actions de script utilisées lors de la création d’un cluster, un échec dans un script exécuté sur un cluster déjà en cours d’exécution n’entraîne pas l’échec automatique du cluster. Une fois le script terminé, le cluster doit revenir à son état « en cours d’exécution ».

> [!IMPORTANT]
> Cela ne signifie pas que votre cluster en cours d’exécution est immunisé contre les scripts dangereux. Par exemple, un script peut supprimer des fichiers requis par le cluster, voire modifier la configuration, entraînant l’échec des services, etc.
>
> Les actions de scripts s’exécutent avec des privilèges root. Soyez donc sûr de comprendre ce que fait un script avant de l’appliquer à votre cluster.

Quand vous appliquez un script à un cluster, l’état du cluster pour un script réussi passe **d’En cours d’exécution** à **Accepté**, puis à **Configuration HDInsight**. Il finit ensuite par revenir à **En cours d’exécution**. Le statut du script est enregistré dans l’historique des actions de script ; vous pouvez l’utiliser pour déterminer si le script a réussi ou échoué. Par exemple, l’applet de commande PowerShell `Get-AzureRmHDInsightScriptActionHistory` peut être utilisée pour afficher l’état d’un script. Elle renvoie des informations semblables à ce qui suit :

    ScriptExecutionId : 635918532516474303
    StartTime         : 2/23/2016 7:40:55 PM
    EndTime           : 2/23/2016 7:41:05 PM
    Status            : Succeeded

> [!NOTE]
> Si vous avez modifié le mot de passe d’utilisateur (admin) du cluster après la création de ce dernier, les actions de script exécutées sur ce cluster risquent d’échouer. Si des actions de script persistantes ciblent des nœuds de travail, elles peuvent échouer quand vous ajoutez des nœuds au cluster par le biais d’opérations de redimensionnement.

## <a name="example-script-action-scripts"></a>Exemple de script d’action de script

Les scripts d’action de script peuvent être utilisés à partir du portail Azure, d’Azure PowerShell, de l’interface de ligne de commande Azure (CLI) ou du Kit de développement logiciel (SDK) .NET HDInsight. HDInsight propose des scripts pour installer les composants suivants sur des clusters HDInsight :

| Nom | Script |
| --- | --- |
| **Ajouter un compte Azure Storage** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Consultez la page [Ajouter un stockage supplémentaire à un cluster HDInsight](hdinsight-hadoop-add-storage.md). |
| **Installez Hue.** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Consultez [Installer et utiliser Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md). |
| **Installation de R** |https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Consultez [Installer et utiliser R sur les clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md). |
| **Installation de Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Consultez [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). |
| **Installation de Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Consultez [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). |
| **Précharger les bibliothèques Hive** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Consultez [Ajouter des bibliothèques Hive sur des clusters HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="use-a-script-action-during-cluster-creation"></a>Utiliser une action de script lors de la création du cluster

Cette section fournit des exemples des différentes façons d’utiliser les actions de script lorsque vous créez un cluster HDInsight à partir du portail Azure à l’aide d’un modèle Azure Resource Manager, des applets de commande PowerShell et du Kit de développement logiciel (SDK) .NET.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Utiliser une action de script lors de la création d’un cluster à partir du portail Azure

1. Démarrez la création d'un cluster comme décrit dans [Création de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md).
2. Dans **Configuration facultative**, sur le panneau **Actions de script**, cliquez sur **ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

    ![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

    | Propriété | Valeur |
    | --- | --- |
    | Nom |Indiquez un nom pour l'action de script. |
    | URI du script |Spécifiez l'URI du script appelé pour personnaliser le cluster. |
    | Head/Worker |Spécifiez les nœuds (**Head** ou **Worker** ou **ZooKeeper**) sur lesquels le script de personnalisation est exécuté. |
    | parameters |Spécifiez les paramètres, si le script le demande. |

    Appuyez sur ENTRÉE pour ajouter plusieurs actions de script et installer plusieurs composants sur le cluster.

3. Cliquez sur **Sélectionner** pour enregistrer la configuration et poursuivre la création du cluster.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Utilisez une action de Script à partir de modèles Azure Resource Manager
Dans cette section, nous utilisons des modèles Azure Resource Manager pour créer un cluster HDInsight et une action de script pour installer des composants personnalisés (R, dans cet exemple) sur le cluster. Cette section fournit un exemple de modèle pour créer un cluster utilisant l’action de script.

> [!NOTE]
> Les étapes décrites dans cette section illustrent la création d’un cluster à l’aide d’une action de script. Pour obtenir un exemple de création d’un cluster à partir d’un modèle à l’aide d’une application HDInsight, consultez [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).

#### <a name="before-you-begin"></a>Avant de commencer

* Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des applets de commande HDInsight Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Pour obtenir des instructions sur la création de modèles, consultez [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Si vous n’avez pas déjà utilisé Azure PowerShell avec Resource Manager, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md).

#### <a name="create-clusters-using-script-action"></a>Création de clusters à l’aide d’une action de script

1. Copiez le modèle suivant vers un emplacement sur votre ordinateur. Ce modèle installe Giraph sur les nœuds principaux, ainsi que des nœuds de travail dans le cluster. Vous pouvez également vérifier si le modèle JSON est valide. Collez le contenu de votre modèle dans [JSONLint](http://jsonlint.com/), un outil de validation JSON en ligne.

            {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "clusterLocation": {
                    "type": "string",
                    "defaultValue": "West US",
                    "allowedValues": [ "West US" ]
                },
                "clusterName": {
                    "type": "string"
                },
                "clusterUserName": {
                    "type": "string",
                    "defaultValue": "admin"
                },
                "clusterUserPassword": {
                    "type": "securestring"
                },
                "sshUserName": {
                    "type": "string",
                    "defaultValue": "username"
                },
                "sshPassword": {
                    "type": "securestring"
                },
                "clusterStorageAccountName": {
                    "type": "string"
                },
                "clusterStorageAccountResourceGroup": {
                    "type": "string"
                },
                "clusterStorageType": {
                    "type": "string",
                    "defaultValue": "Standard_LRS",
                    "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS"
                    ]
                },
                "clusterStorageAccountContainer": {
                    "type": "string"
                },
                "clusterHeadNodeCount": {
                    "type": "int",
                    "defaultValue": 1
                },
                "clusterWorkerNodeCount": {
                    "type": "int",
                    "defaultValue": 2
                }
            },
            "variables": {
            },
            "resources": [
                {
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-05-01-preview",
                    "dependsOn": [ ],
                    "tags": { },
                    "properties": {
                        "accountType": "[parameters('clusterStorageType')]"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-03-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
                    ],
                    "tags": { },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "hadoop",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterUserPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [
                                {
                                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                    "isDefault": true,
                                    "container": "[parameters('clusterStorageAccountContainer')]",
                                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                                }
                            ]
                        },
                        "computeProfile": {
                            "roles": [
                                {
                                    "name": "headnode",
                                    "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installGiraph",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                },
                                {
                                    "name": "workernode",
                                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                }
            ],
            "outputs": {
                "cluster":{
                    "type" : "object",
                    "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                }
            }
        }
2. Démarrer Azure PowerShell et se connecter à votre compte Azure. Une fois que vous avez entré vos informations d'identification, la commande retourne les informations relatives à votre compte.

        Add-AzureRmAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...
3. Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement.

        Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [!NOTE]
    > Vous pouvez utiliser `Get-AzureRmSubscription` pour obtenir la liste de tous les abonnements associés à votre compte, y compris l’ID d’abonnement de chacun d’eux.

4. Si vous n’avez pas de groupe de ressources, créez-en un. Indiquez le nom du groupe de ressources et l'emplacement dont vous avez besoin pour votre solution. Un résumé du nouveau groupe de ressources est retourné.

        New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

        ResourceGroupName : myresourcegroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Pour créer un déploiement pour votre groupe de ressources, exécutez la commande **New-AzureRmResourceGroupDeployment** et indiquez les paramètres nécessaires. Les paramètres incluent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL du modèle que vous avez créé. Si votre modèle requiert n’importe quel paramètre, vous devez également transférer les paramètres. Dans ce cas, l’action de script pour installer R sur le cluster ne nécessite pas de paramètres.

        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>

    Vous serez invité à fournir des valeurs pour les paramètres définis dans le modèle.

1. Une fois le groupe de ressources déployé, un résumé du déploiement apparaît.

          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/17/2015 7:00:27 PM
          Mode              : Incremental
          ...

2. Si votre déploiement échoue, vous pouvez utiliser les applets de commande suivants pour obtenir des informations sur les échecs.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Utiliser une action de script lors de la création du cluster à partir d’Azure PowerShell

Dans cette section, nous allons utiliser l'applet de commande [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) pour appeler des scripts avec une action de script pour personnaliser un cluster. Avant de poursuivre, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour plus d'informations sur la configuration d'un poste de travail pour exécuter des applets de commande HDInsight PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/powershell/azureps-cmdlets-docs).

Procédez comme suit :

1. Ouvrez la console Azure PowerShell et utilisez la commande suivante pour vous connecter à votre abonnement Azure et déclarer des variables PowerShell :

        # LOGIN TO ZURE
        Login-AzureRmAccount

        # PROVIDE VALUES FOR THESE VARIABLES
        $subscriptionId = "<SubscriptionId>"        # ID of the Azure subscription
        $clusterName = "<HDInsightClusterName>"            # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"                # Location of the HDInsight cluster. It must be in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNumbers>            # The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster is created in

2. Spécifiez les valeurs de configuration telles que les nœuds du cluster et le stockage par défaut à utiliser.

        # SPECIFY THE CONFIGURATION OPTIONS
        Select-AzureRmSubscription -SubscriptionId $subscriptionId
        $config = New-AzureRmHDInsightClusterConfig
        $config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccountKey=$storageAccountKey

3. Utilisez l'applet de commande **Add-AzureRmHDInsightScriptAction** pour appeler le script. L’exemple suivant utilise un script qui installe Giraph sur le cluster :

        # INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

    L'applet de commande **Add-AzureRmHDInsightScriptAction** accepte les paramètres suivants :

    | Paramètre | Définition |
    | --- | --- |
    | Configuration |Objet de configuration auquel des informations d'action de script sont ajoutées. |
    | Nom |Nom de l'action de script. |
    | NodeType |Spécifie le nœud sur lequel le script de personnalisation est exécuté. Les valeurs correctes sont **HeadNode** (pour une installation sur le nœud principal), **WorkerNode** (pour une installation sur l'ensemble des nœuds de données) ou **ZookeeperNode** (pour une installation sur le nœud zookeeper). |
    | parameters |Paramètres requis par le script. |
    | Uri |Spécifie l’URI du script qui est exécuté. |

4. Définir l'utilisateur admin/HTTPS pour le cluster :

        $httpCreds = get-credential

    Lorsque vous y êtes invité, entrez « admin » comme nom et saisissez un mot de passe.

5. Définir les informations d'identification SSH :

        $sshCreds = get-credential

    Lorsque vous y êtes invité, entrez le nom d'utilisateur et le mot de passe SSH. Si vous souhaitez sécuriser le compte SSH avec un certificat au lieu d'un mot de passe, utilisez un mot de passe vide et définissez `$sshPublicKey` sur le contenu de la clé publique du certificat à utiliser. Par exemple :

        $sshPublicKey = Get-Content .\path\to\public.key -Raw

6. Enfin, créez le cluster :

        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux

    Si vous utilisez une clé publique pour sécuriser votre compte SSH, vous devez également spécifier `-SshPublicKey $sshPublicKey` en tant que paramètre.

La création du cluster peut prendre plusieurs minutes.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Utiliser une action de script lors de la création du cluster à l’aide du Kit de développement logiciel (SDK) HDInsight .NET

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes qui facilitent l’utilisation d’HDInsight à partir d’une application .NET. Pour obtenir un exemple de code, consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Appliquer une action de script sur un cluster en cours d’exécution

Cette section fournit des exemples sur les différentes manières d’appliquer des actions de script à un cluster HDInsight en cours d’exécution à partir du portail Azure, à l’aide des applets de commande PowerShell, de l’interface de ligne de commande Azure (CLI) multiplateforme et du Kit de développement logiciel (SDK) .NET. L’action de script persistante utilisée dans cette section ajoute un compte de stockage Azure existant à un cluster en cours d’exécution. Vous pouvez également utiliser d’autres actions de script, consultez [Exemple de script d’action de script](#example-script-action-scripts).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Appliquer une action de script sur un cluster en cours d’exécution à partir du portail Azure

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre cluster HDInsight.

2. Dans le panneau de cluster HDInsight, sélectionnez la vignette **Actions de script** .

    ![Vignette Actions de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Vous pouvez également sélectionner **Tous les paramètres**, puis **Actions de script** dans le panneau Paramètres.

3. En haut du panneau Actions de script, sélectionnez **Envoyer**.

    ![Icône Envoyer](./media/hdinsight-hadoop-customize-cluster-linux/newscriptaction.png)

4. Dans le panneau Ajouter une action de script, entrez les informations suivantes.

   * **Nom** : le nom convivial à utiliser pour cette action de script. Dans cet exemple : `Add Storage account`.

   * **URI du script** : l’URI du script. Dans cet exemple : `https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`

   * **Head**, **Worker** et **Zookeeper** : cochez les nœuds auxquels ce script doit être appliqué. Dans cet exemple, Head, Worker et Zookeeper sont cochés.

   * **PARAMÈTRES** : si le script accepte des paramètres, entrez-les ici. Dans cet exemple, entrez le nom du compte de stockage et la clé du compte de stockage :

       ![hdinsight action script persistante cpte compte stockage sur clusters en cours d’éxécution](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-persisted-script-action-add-storage-account.png)

       Sur la capture d’écran, `contosodata` est un compte Azure Storage existant, la deuxième ligne est la clé du compte de stockage.

   * **PERSISTANT** : cochez cette entrée si vous souhaitez rendre le script persistant afin de l’appliquer aux nœuds Worker créés lors de la montée en puissance du cluster.

5. Pour finir, cliquez sur le bouton **Créer** afin d’appliquer le script au cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Appliquer une action de script sur un cluster en cours d’exécution à partir d’Azure PowerShell

Avant de poursuivre, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour plus d'informations sur la configuration d'un poste de travail pour exécuter des applets de commande HDInsight PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/powershell/azureps-cmdlets-docs).

1. Ouvrez la console Azure PowerShell et utilisez la commande suivante pour vous connecter à votre abonnement Azure et déclarer des variables PowerShell :

        # LOGIN TO ZURE
        Login-AzureRmAccount

        # PROVIDE VALUES FOR THESE VARIABLES
        $clusterName = "<HDInsightClusterName>"            # HDInsight cluster name
        $saName = "<ScriptActionName>"                  # Name of the script action
        $saURI = "<URI to the script>"                  # The URI where the script is located
        $nodeTypes = "headnode", "workernode"

   > [!NOTE]
   > Si vous utilisez un cluster HDInsight Premium, vous pouvez utiliser un type de nœud `"edgenode"` pour exécuter le script sur le nœud de périphérique.

2. Utilisez la commande suivante pour appliquer le script au cluster :

        Submit-AzureRmHDInsightScriptAction -ClusterName $clusterName -Name $saName -Uri $saURI -NodeTypes $nodeTypes -PersistOnSuccess

    Une fois la tâche terminée, des informations similaires à celles présentées ci-dessous doivent s’afficher :

        OperationState  : Succeeded
        ErrorMessage    :
        Name            : Giraph
        Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        Parameters      :
        NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Appliquer une action de script sur un cluster en cours d’exécution à partir de l'interface de ligne de commande Azure (CLI)

Avant de poursuivre, assurez-vous que vous avez installé et configuré l'interface de ligne de commande Azure (CLI). Pour plus d’informations, consultez la rubrique [Installation de l’interface de ligne de commande Azure (CLI)](../cli-install-nodejs.md).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. Ouvrez une session de l’interpréteur de commandes, une fenêtre de terminal, une invite de commandes ou toute autre ligne de commande de votre système et utilisez la commande suivante pour passer en mode Azure Resource Manager.

        azure config mode arm

2. Utilisez la commande suivante pour vous identifier auprès de votre abonnement Azure.

        azure login

3. Utilisez la commande suivante pour appliquer une action de script à un cluster en cours d'exécution

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    Si vous omettez les paramètres de cette commande, vous êtes invité à les saisir. Si le script que vous spécifiez avec `-u` accepte des paramètres, vous pouvez les spécifier à l’aide du paramètre `-p`.

    Les valeurs **nodetypes** acceptées sont **headnode**, **workernode** et **zookeeper**. Si le script doit être appliqué à plusieurs types de nœud, spécifiez ces types en les séparant par un « ; ». Par exemple : `-n headnode;workernode`.

    Pour conserver le script, ajoutez le paramètre `--persistOnSuccess`. Vous pouvez également conserver le script à une date ultérieure à l’aide du paramètre `azure hdinsight script-action persisted set`.

    Une fois la tâche terminée, vous obtenez une sortie similaire à celle présentée ci-dessous.

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Appliquer une action de script sur un cluster en cours d’exécution à l’aide de l’API REST

Consultez [Exécuter des actions de script sur un cluster en cours d’exécution](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Appliquer une action de script sur un cluster en cours d’exécution à partir du Kit de développement logiciel (SDK) HDInsight .NET

Pour obtenir un exemple d’utilisation du Kit de développement logiciel (SDK) .NET pour appliquer des scripts à un cluster, consultez [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Afficher l’historique, promouvoir et abaisser des actions de script

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre cluster HDInsight.

2. Dans le panneau de cluster HDInsight, sélectionnez **Paramètres**.

    ![Icône Paramètres](./media/hdinsight-hadoop-customize-cluster-linux/settingsicon.png)

3. Dans le panneau Paramètres, sélectionnez **Actions de script**.

    ![Lien Actions de script](./media/hdinsight-hadoop-customize-cluster-linux/settings.png)

4. Une liste de scripts persistants, ainsi que l’historique des scripts appliqués au cluster, s’affiche dans le volet Actions de script. Dans la capture d’écran ci-dessous, vous pouvez voir que le script Solr a été exécuté sur ce cluster, mais qu’aucune action de script n’a été rendue persistante.

    ![Panneau Actions de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionhistory.png)

5. Le fait de sélectionner un script dans l’historique affiche le panneau Propriétés pour le script en question. En haut du panneau, vous pouvez réexécuter le script ou le promouvoir.

    ![Panneau Propriétés des actions de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionproperties.png)

6. Vous pouvez également utiliser **…** à droite des entrées dans le panneau Actions de script pour effectuer des actions telles que réexécuter, rendre persistante ou, pour les actions persistantes, supprimer.

    ![Utilisation de … des actions de script](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell

| Utilisez... | Pour... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Récupérer des informations sur les actions de script persistantes |
| Get-AzureRmHDInsightScriptActionHistory |Récupérer l’historique des actions de script appliquées au cluster, ou les informations d’un script spécifique |
| Set-AzureRmHDInsightPersistedScriptAction |Promouvoir une action de script ad hoc en action de script persistante |
| Remove-AzureRmHDInsightPersistedScriptAction |Abaisser une action de script persistante en action ad hoc |

> [!IMPORTANT]
> La commande `Remove-AzureRmHDInsightPersistedScriptAction` n’annule pas les actions exécutées par un script. Elle supprime uniquement l’indicateur persistant pour que le script ne soit pas exécuté sur les nouveaux nœuds Worker ajoutés au cluster.

L’exemple de script suivant illustre l’utilisation des applets de commande pour promouvoir, puis pour abaisser un script.

    # Get a history of scripts
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster

    # From the list, we want to get information on a specific script
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Promote this to a persisted script
    # Note: the script must have a unique name to be promoted
    # if the name is not unique, you receive an error
    Set-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Demote the script back to ad hoc
    # Note that demotion uses the unique script name instead of
    # execution ID.
    Remove-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -Name "Install Giraph"

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

| Utilisez... | Pour... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Récupérer une liste des actions de script persistantes |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Récupérer des informations sur les actions de script persistantes |
| `azure hdinsight script-action history list <clustername>` |Récupérer l’historique des actions de script appliquées au cluster |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Récupérer des informations sur une action de script spécifique |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promouvoir une action de script ad hoc en action de script persistante |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Abaisser une action de script persistante en action ad hoc |

> [!IMPORTANT]
> La commande `azure hdinsight script-action persisted delete` n’annule pas les actions exécutées par un script. Elle supprime uniquement l’indicateur persistant pour que le script ne soit pas exécuté sur les nouveaux nœuds Worker ajoutés au cluster.

### <a name="using-the-hdinsight-net-sdk"></a>Utilisation du Kit de développement logiciel (SDK) HDInsight .NET

Pour obtenir un exemple d’utilisation du Kit de développement logiciel (SDK) .NET afin de récupérer l’historique des scripts d’un cluster, promouvoir ou abaisser des scripts, consultez [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> Cet exemple montre également comment installer une application de HDInsight à l’aide du Kit de développement logiciel (SDK) .NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Prise en charge des logiciels open source utilisés sur les clusters HDInsight

Le service Microsoft Azure HDInsight est une plateforme flexible qui vous permet de créer des applications de données volumineuses (Big Data) dans le cloud à l’aide d’un écosystème de technologies open source articulées autour de Hadoop. Microsoft Azure fournit un niveau de support général pour les technologies open source, comme indiqué dans la section **Portée du support** du site web [FAQ du support Azure](https://azure.microsoft.com/support/faq/). Le service HDInsight fournit un niveau de support supplémentaire pour certains composants, comme indiqué ci-dessous.

Deux types de composant open source sont disponibles dans le service HDInsight :

* **Composants intégrés** : ces composants sont préinstallés sur les clusters HDInsight et fournissent la fonctionnalité principale du cluster. Par exemple, YARN ResourceManager, le langage de requête Hive (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. Une liste complète des composants de cluster est disponible sur la page [Nouveautés des versions de cluster Hadoop fournies par HDInsight](hdinsight-component-versioning.md).
* **Composants personnalisés** : en tant qu’utilisateur du cluster, vous pouvez installer ou utiliser, dans votre charge de travail, tout composant qui est disponible dans la communauté ou que vous avez créé.

> [!WARNING]
> Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aide à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org). Par exemple : [Hadoop](http://hadoop.apache.org/).

Le service HDInsight fournit plusieurs méthodes d’utilisation de ces composants personnalisés. Quel que soit le mode d’utilisation ou d’installation du composant sur le cluster, le même niveau de support s’applique. Vous trouverez, ci-dessous, la liste des méthodes les plus courantes d’utilisation des composants personnalisés sur des clusters HDInsight.

1. Envoi de travaux : il est possible d’envoyer des travaux Hadoop ou d’autres types de travaux au cluster qui exécute ou utilise des composants personnalisés.

2. Personnalisation de cluster : lors de la création du cluster, vous pouvez spécifier des paramètres supplémentaires et des composants personnalisés qui sont installés sur les nœuds de cluster.

3. Exemples : pour des composants personnalisés fréquemment utilisés, il arrive que Microsoft et d’autres éditeurs proposent des exemples illustrant leur utilisation sur les clusters HDInsight. Ces exemples sont fournis sans support.

## <a name="troubleshooting"></a>Résolution des problèmes

Vous pouvez utiliser l’interface utilisateur web de Ambari pour afficher les informations consignées par des actions de script. Si le script est utilisé pendant la création du cluster et que la création du cluster a échoué en raison d’une erreur dans le script, les journaux sont également disponibles dans le compte de stockage par défaut associé au cluster. Cette section fournit des informations sur la façon de récupérer les journaux à l'aide de ces deux options.

### <a name="using-the-ambari-web-ui"></a>À l'aide de l'interface utilisateur Web d’Ambari

1. Dans votre navigateur, accédez à https://CLUSTERNAME.azurehdinsight.net. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight.

    Lorsque vous y êtes invité, saisissez le nom de compte (admin) et le mot de passe correspondant au cluster. Vous devrez peut-être saisir de nouveau les informations d’identification d’administrateur dans un formulaire web.

2. Dans la barre située en haut de la page, sélectionnez l’entrée **ops** . Cette opération permet d’afficher une liste des opérations en cours et précédentes effectuées sur le cluster via Ambari.

    ![Barre de l’interface utilisateur web Ambari avec ops sélectionné](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Recherchez les entrées comportant **run\_customscriptaction** dans la colonne **Operations**. Ceux-ci sont créés lors de l’exécution des actions de Script.

    ![Capture d’écran des opérations](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Sélectionnez cette entrée run\customscriptaction et explorez les liens pour afficher les sorties STDOUT et STDERR. Cette sortie est générée lorsque le script s’exécute et peut contenir des informations utiles.

### <a name="access-logs-from-the-default-storage-account"></a>Accès aux journaux à partir du compte de stockage par défaut

Si la création du cluster a échoué en raison d'une erreur dans l'action de script, les journaux des actions de script restent accessibles directement à partir du compte de stockage par défaut associé au cluster.

* Les journaux de stockage sont disponibles dans `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Capture d’écran des opérations](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Ici, les journaux sont classés séparément selon les nœuds headnode, workernode et zookeeper. Voici quelques exemples :

    * **Headnode** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Nœud Worker** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Nœud Zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Toutes les valeurs stdout et stderr de l'hôte correspondant sont téléchargées vers le compte de stockage. Il existe un fichier **output-\*.txt** et un fichier **errors-\*.txt** pour chaque action de script. Le fichier output-*.txt contient des informations sur l'URI du script que vous avez exécuté sur l'ordinateur hôte. Par exemple :

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Vous pouvez créer plusieurs fois un cluster d'action de script portant le même nom. Dans ce cas, vous pouvez différencier les journaux correspondants selon le nom de dossier DATE. Par exemple, la structure de dossiers d’un cluster (mycluster) créé à différentes dates ressemble à ce qui suit :

    * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`

    * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Si vous créez un cluster d'action de script avec le même nom le même jour, vous pouvez utiliser le préfixe unique pour identifier les fichiers journaux correspondants.

* Si vous créez un cluster à la fin de la journée, il est possible que les fichiers journaux s'étendent sur deux jours. Dans ce cas, vous voyez deux dossiers de dates différents pour le même cluster.

* Le téléchargement des fichiers journaux vers le conteneur par défaut peut prendre jusqu'à 5 minutes, en particulier pour les clusters de grande taille. Par conséquent, si vous souhaitez accéder aux journaux, vous ne devez pas immédiatement supprimer le cluster en cas d'échec d'une action de script.

### <a name="ambari-watchdog"></a>Agent de surveillance Ambari

> [!WARNING]
> Ne modifiez pas le mot de passe pour l’agent de surveillance Ambari (hdinsightwatchdog) sur votre cluster HDInsight basé sur Linux. La modification du mot de passe pour ce compte élimine la possibilité d’exécuter de nouvelles actions de script sur le cluster HDInsight.

### <a name="cannot-import-name-blobservice"></a>Impossible d’importer le nom BlobService

__Symptômes__ : échec de l’action de script, et une erreur semblable à ce qui suit s’affiche lorsque vous observez l’opération dans Ambari :

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Cause__ : cette erreur se produit si vous mettez à niveau le client Python de stockage Azure qui est inclus dans le cluster HDInsight. HDInsight attend le client de stockage Azure 0.20.0.

__Résolution__ : pour résoudre cette erreur, connectez-vous manuellement à chaque nœud de cluster à l’aide de `ssh` et utilisez la commande suivante pour réinstaller la version correcte du client de stockage :

```
sudo pip install azure-storage==0.20.0
```

Pour en savoir plus sur la connexion au cluster via SSH, voir [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>L’historique n’affiche pas les scripts utilisés lors de la création du cluster

Si votre cluster a été créé avant le 15 mars 2016, vous ne verrez peut-être pas d’entrée dans l’historique des actions de script pour les scripts utilisés lors de la création du cluster. Toutefois, si vous redimensionnez le cluster après le 15 mars 2016, les scripts utilisés lors de la création du cluster sont affichés dans l’historique, car ils sont appliqués aux nouveaux nœuds du cluster dans le cadre de l’opération de redimensionnement.

Deux exceptions :

* Si votre cluster a été créé avant le 1er septembre 2015. Il s’agit de la date à laquelle les actions de script ont été introduites ; tout cluster créé avant cette date n’aurait pas pu utiliser les actions de script pour la création de cluster.

* Si vous avez utilisé plusieurs actions de script lors de la création du cluster et donné le même nom à plusieurs scripts, ou le même nom, le même URI, mais des paramètres différents pour plusieurs scripts. Dans ce cas, vous recevez l’erreur suivante.

    Aucune nouvelle action de script ne peut être exécutée sur ce cluster en raison d’un conflit de noms de script dans les scripts existants. Les noms de script fournis au moment de la création du cluster doivent être uniques. Les scripts existants seront toujours exécutés, même après redimensionnement.

## <a name="next-steps"></a>Étapes suivantes

Consultez la rubrique suivante pour obtenir des informations et des exemples sur la création et l’utilisation de scripts afin de personnaliser un cluster :

* [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Ajouter un stockage supplémentaire à un cluster HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Procédure de création d’un cluster"

