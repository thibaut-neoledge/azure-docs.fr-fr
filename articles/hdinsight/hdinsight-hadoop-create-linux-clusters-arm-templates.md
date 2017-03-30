---
title: "Créer Azure HDInsight (Hadoop) à l’aide de modèles | Microsoft Docs"
description: "Apprenez à créer des clusters pour Azure HDInsight à l’aide de modèles Azure Resource Manager."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/14/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 37567bf014d1deb5bcd36af94924948550d55f8e
ms.lasthandoff: 03/21/2017


---
# <a name="create-hadoop-clusters-in-hdinsight-using-azure-resource-management-templates"></a>Création de clusters Hadoop dans HDInsight à l’aide de modèles Azure Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Apprenez à créer des clusters HDInsight à l’aide des modèles Azure Resource Manager. Pour plus d’informations, consultez la page [Déploiement d’une application avec un modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Pour découvrir d’autres outils et fonctions de création de clusters, cliquez sur l’onglet de sélection situé en haut de cette page, ou consultez la section relative aux [méthodes de création de clusters](hdinsight-hadoop-provision-linux-clusters.md#cluster-creation-methods).

## <a name="prerequisites"></a>Configuration requise :
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

* [Abonnement Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell et/ou interface de ligne de commande Azure

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-management-templates"></a>Modèles Resource Manager
Le modèle Resource Manager permet de créer facilement, au cours d’une opération unique et coordonnée, des clusters HDInsight, leurs ressources dépendantes (telles que le compte de stockage par défaut) ainsi que d’autres ressources (telles que la base de données Azure SQL pour utiliser Apache Sqoop) correspondant à votre application. Dans le modèle, vous définissez les ressources nécessaires à l'application et vous spécifiez les paramètres de déploiement pour entrer des valeurs pour différents environnements. Le modèle se compose de JSON et d'expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement.

Pour accéder à des exemples de modèles HDInsight, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Utilisez [VSCode](https://code.visualstudio.com/#alt-downloads) interplateforme avec [l’extension Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) ou un éditeur de texte pour enregistrer le modèle dans un fichier sur votre station de travail. Vous pourrez apprendre à appeler le modèle à l’aide de différentes méthodes.

Pour plus d’informations sur le modèle Resource Manager, consultez les articles suivants :

* [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Déploiement d’une application avec un modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Génération de modèles

À l’aide du Portail Azure, vous pouvez configurer toutes les propriétés d’un cluster, puis enregistrer le modèle avant de le déployer.  Vous pouvez ensuite réutiliser le modèle.

**Pour générer un modèle à l’aide du Portail Azure**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Intelligence + analyse** et sur **HDInsight**.
3. Suivez les instructions pour entrer les propriétés. Vous pouvez utiliser l’option **Création rapide** ou **Personnalisé**.
4. Dans l’onglet Résumé du cluster, cliquez sur **Télécharger le modèle et les paramètres**.

    ![Téléchargement de modèle Resource Manager pour la création de clusters Hadoop dans HDInsight](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-download.png)

    Sont répertoriés le fichier de modèle, le fichier de paramètres et les exemples de code pour le déploiement du modèle :

    ![Options de téléchargement de modèle Resource Manager pour la création de clusters Hadoop dans HDInsight](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-download-options.png)

    À ce stade, vous pouvez télécharger le modèle, l’enregistrer dans votre bibliothèque de modèles ou le déployer.

    Pour accéder à un modèle de votre bibliothèque, cliquez sur **Plus de services** dans le menu de gauche, puis cliquez sur **Modèles** (sous la catégorie **Autres**).

> [!Note]
> Les modèles doivent être utilisés conjointement avec les fichiers de paramètres.  Sinon, vous obtiendrez peut-être des résultats inattendus.  Par exemple, la valeur par défaut de la propriété clusterKind est toujours hadoop, quels que soient les éléments que vous avez spécifiés avant de télécharger le modèle.



## <a name="deploy-with-powershell"></a>Déployer avec PowerShell

La procédure suivante crée un cluster Hadoop dans HDInsight :

**Pour déployer un cluster à l’aide d’un modèle Resource Manager**

1. Enregistrez le fichier .json de l’ [annexe A](#appx-a-arm-template) sur votre poste de travail. Dans le script PowerShell, le nom de fichier est *C:\HDITutorials-ARM\hdinsight-arm-template.json*.
2. Définissez les paramètres et les variables, si nécessaire.
3. Exécutez le modèle à l’aide du script PowerShell suivant.

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>"
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

    Le script PowerShell configure uniquement le nom du cluster. Le nom du compte de stockage est codé en dur dans le modèle. Vous êtes invité à entrer le mot de passe utilisateur du cluster (le nom d’utilisateur par défaut est *admin*) et le mot de passe utilisateur SSH (le nom d’utilisateur SSH par défaut est *sshuser*).  

Pour plus d’informations, consultez la rubrique [Déploiement avec PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy).

## <a name="deploy-with-cli"></a>Déploiement avec la CLI
L’exemple suivant appelle un modèle Resource Manager pour créer un cluster, son compte de stockage dépendant et son conteneur :

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"

Vous êtes invité à entrer le nom du cluster, le mot de passe utilisateur du cluster (le nom d’utilisateur par défaut est *admin*) et le mot de passe utilisateur SSH (le nom d’utilisateur SSH par défaut est *sshuser*). Pour fournir les paramètres en ligne :

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{\"clusterName\":{\"value\":\"hdi1229\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"},\"sshPassword\":{\"value\":\"Pass@word1\"}}'

## <a name="deploy-with-rest-api"></a>Déploiement avec l’API REST
Consultez [Déployer avec l’API REST](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Déployer avec Visual Studio
Avec Visual Studio, vous pouvez créer un projet de groupe de ressources et le déployer vers Azure via l’interface utilisateur. Vous sélectionnez le type de ressources à inclure dans votre projet et ces ressources sont automatiquement ajoutées au modèle de gestionnaire de ressources. Le projet fournit également un script PowerShell pour déployer le modèle.

Pour une introduction à l’utilisation de Visual Studio avec les groupes de ressources, consultez [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté différentes méthodes pour créer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour obtenir un exemple détaillé de déploiement d’une application, consultez [Approvisionner et déployer des microservices de manière prévisible dans Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
* Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](../solution-dev-test-environments.md).
* Pour en savoir plus sur les sections du modèle Azure Resource Manager, consultez [Création de modèles](../azure-resource-manager/resource-group-authoring-templates.md).
* Pour obtenir la liste des fonctions que vous pouvez utiliser dans un modèle Azure Resource Manager, voir [Fonctions des modèles](../azure-resource-manager/resource-group-template-functions.md).

## <a name="appx-a-resource-manager-template"></a>Annexe A : Modèle Resource Manager
Le modèle Azure Resource Manager suivant crée un cluster Hadoop basé sur Linux avec le compte de stockage Azure dépendant.

> [!NOTE]
> L’exemple inclut des informations de configuration pour le metastore Hive et le metastore Oozie.  Supprimez la section ou configurez la section avant d’utiliser le modèle.
>
>

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.4",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "2",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                }
            ]
            }
        }
        }
    ],
    "outputs": {
        "cluster": {
        "type": "object",
        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
        }
    }
    }

