<properties
   pageTitle="Création de clusters Hadoop basés sur Windows dans HDInsight à l’aide de modèles Azure Resource Manager | Microsoft Azure"
   	description="Apprenez à créer des clusters pour Azure HDInsight à l’aide de modèles Azure Resource Manager."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jgao"/>

# Création de clusters Hadoop basés sur Windows dans HDInsight à l’aide de modèles Azure Resource Manager

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-selector-create-clusters.md)]

Apprenez à créer des clusters HDInsight à l’aide des modèles Azure Resource Manager. Pour plus d’informations, consultez la page [Déploiement d’une application avec un modèle Azure Resource Manager](../resource-group-template-deploy.md). Pour accéder à d’autres outils et fonctions de création de clusters, cliquez sur l’onglet de sélection situé en haut de cette page, ou consultez la section relative aux [méthodes de création de clusters](hdinsight-provision-clusters.md#cluster-creation-methods).

##Configuration requise :

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

- [Abonnement Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Azure PowerShell ou Interface de ligne de commande Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## Modèles Microsoft Azure Resource Manager

Le modèle Resource Manager permet de créer facilement, au cours d’une opération unique et coordonnée, des clusters HDInsight, leurs ressources dépendantes (telles que le compte de stockage par défaut) ainsi que d’autres ressources (telles que la base de données Azure SQL pour utiliser Apache Sqoop) correspondant à votre application. Dans le modèle, vous définissez les ressources nécessaires à l'application et vous spécifiez les paramètres de déploiement pour entrer des valeurs pour différents environnements. Le modèle se compose de JSON et d'expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement.

[L’Annexe A](#appx-a-arm-template) propose un modèle Resource Manager permettant de créer un cluster HDInsight avec le compte de stockage Azure dépendant. Utilisez un éditeur de texte pour enregistrer le modèle dans un fichier sur votre station de travail. Vous allez apprendre à appeler le modèle à l’aide de différents outils.

Pour plus d’informations sur le modèle Resource Manager, consultez

- [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md)
- [Déploiement d’une application avec un modèle Azure Resource Manager](../resource-group-template-deploy.md)


## Déployer avec PowerShell

La procédure suivante crée un cluster HDInsight.

**Pour déployer un cluster à l’aide d’un modèle Resource Manager**

1. Enregistrez le fichier .json de l’[annexe A](#appx-a-arm-template) sur votre poste de travail.
2. Définissez les paramètres si nécessaire.
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
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

	Le script PowerShell configure uniquement le nom du cluster et le nom du compte de stockage. Vous pouvez définir d'autres valeurs dans le modèle ARM.
	
Pour plus d’informations, consultez la rubrique [Déploiement avec PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## Déploiement avec l’interface de ligne de commande Azure

L’exemple suivant appelle un modèle ARM pour créer un cluster, son compte de stockage dépendant et son conteneur :

	azure login
	azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{"clusterName":{"value":"hdi1229win"},"clusterStorageAccountName":{"value":"hdi1229store"},"location":{"value":"East US 2"},"clusterLoginPassword":{"value":"Pass@word1"}}"





## Déploiement avec l’API REST

Consultez [Déployer avec l’API REST](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## Déployer avec Visual Studio

Avec Visual Studio, vous pouvez créer un projet de groupe de ressources et le déployer vers Azure via l’interface utilisateur. Vous sélectionnez le type de ressources à inclure dans votre projet et ces ressources sont automatiquement ajoutées au modèle de gestionnaire de ressources. Le projet fournit également un script PowerShell pour déployer le modèle.

Pour une introduction à l’utilisation de Visual Studio avec les groupes de ressources, consultez [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

##Étapes suivantes
Cet article vous a présenté différentes méthodes pour créer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :


- Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](../virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour obtenir un exemple détaillé de déploiement d’une application, consultez [Approvisionner et déployer des microservices de manière prévisible dans Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](../solution-dev-test-environments.md).
- Pour en savoir plus sur les sections du modèle Azure Resource Manager, consultez [Création de modèles](../resource-group-authoring-templates.md).
- Pour obtenir la liste des fonctions que vous pouvez utiliser dans un modèle Azure Resource Manager, voir [Fonctions des modèles](../resource-group-template-functions.md).



##Annexe A : Modèle Resource Manager

Le modèle Azure Resource Manager suivant crée un cluster Hadoop basé sur Windows avec le compte de stockage Azure dépendant.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
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
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
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
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
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
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
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

<!---HONumber=AcomDC_0907_2016-->