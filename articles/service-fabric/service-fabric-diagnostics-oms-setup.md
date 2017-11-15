---
title: Azure Service Fabric - Configuration de la surveillance avec OMS Log Analytics | Microsoft Docs
description: "Découvrez comment configurer OMS pour visualiser et analyser les événements dans le cadre de la surveillance de vos clusters Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: 32c09f06cea97024437e7e339407d344194a14ae
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>Configurer OMS Log Analytics pour un cluster

Vous pouvez configurer un espace de travail OMS à partir d’Azure Resource Manager ou de la Place de marché Microsoft Azure. Utilisez Azure Resource Manager si vous souhaitez mettre à jour un modèle de votre déploiement pour plus tard. Le déploiement via la Place de marché est plus facile si vous avez déjà déployé un cluster et activé les diagnostics.

> [!NOTE]
> Vous devez activer les diagnostics dans votre cluster pour voir les événements au niveau du cluster ou de la plateforme et pour configurer OMS de manière à surveiller correctement votre cluster.

## <a name="deploying-oms-using-a-resource-manager-template"></a>Déploiement d’OMS à l’aide du modèle Resource Manager

Lors du déploiement d’un cluster à l’aide d’un modèle Resource Manager, le modèle doit également créer un espace de travail OMS, lui ajouter la solution Service Fabric et le configurer pour lire des données provenant des tables de stockage appropriées.

Vous trouverez [ici](https://azure.microsoft.com/resources/templates/service-fabric-oms/) un exemple de modèle que vous pouvez utiliser et modifier selon vos besoins. Pour obtenir des modèles proposant plusieurs options de configuration pour les espaces de travail OMS, consultez [Modèles Service Fabric et OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

Les principales modifications à apporter sont les suivantes :

1. Ajoutez `omsWorkspaceName` et `omsRegion` à vos paramètres. Pour cela, vous devez ajouter l’extrait de code suivant aux paramètres définis dans le fichier *template.json*. N’hésitez pas à modifier les valeurs par défaut selon vos besoins. Vous devez également ajouter les deux nouveaux paramètres du fichier *parameters.json* pour définir leurs valeurs lors du déploiement de ressources :
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    Les valeurs `omsRegion` doivent se conformer à un ensemble spécifique de valeurs. Vous devez choisir celle qui est la plus proche du déploiement de votre cluster.

2. Si vous prévoyez d’envoyer les journaux des applications à OMS, vérifiez que `applicationDiagnosticsStorageAccountType` et `applicationDiagnosticsStorageAccountName` sont inclus en tant que paramètres dans votre modèle. Si ce n’est pas le cas, ajoutez-les à la section des variables de la façon suivante, puis modifiez leurs valeurs selon vos besoins. Vous pouvez également les inclure en tant que paramètres, en respectant le format ci-dessus.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]",
    ```

3. Ajoutez la solution Service Fabric OMS aux variables de votre modèle :

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric",
    ```

4. Ajoutez le code suivant à la fin de la section des ressources, après la déclaration de la ressource de cluster Service Fabric.

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```

5. Déployez le modèle comme une mise à niveau Resource Manager sur votre cluster. Pour cela, utilisez l’API `New-AzureRmResourceGroupDeployment` dans le module AzureRM PowerShell. Voici un exemple de commande :

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager va détecter qu’il s’agit d’une mise à jour d’une ressource existante. Il va uniquement traiter les modifications qui existent entre le modèle du déploiement existant et le nouveau modèle fourni.

## <a name="deploying-oms-using-azure-marketplace"></a>Déploiement d’OMS à l’aide de la Place de marché Azure

Si vous préférez ajouter un espace de travail OMS après avoir déployé un cluster, accédez à la Place de marché Azure (dans le portail), puis recherchez *« Service Fabric Analytics »*.

1. Cliquez sur **Nouveau** dans le menu de navigation gauche. 

2. Recherchez *Service Fabric Analytics*. Cliquez sur la ressource qui s’affiche.

3. Cliquez sur **Créer**

    ![SF Analytics OMS dans la Place de marché](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Dans la fenêtre de création Service Fabric Analytics, cliquez sur **Sélectionner un espace de travail** pour le champ *Espace de travail OMS*, puis sur **Créer un espace de travail**. Renseignez les entrées obligatoires. Ici, la seule exigence est que l’abonnement pour le cluster Service Fabric et celui pour l’espace de travail OMS soient identiques. Une fois vos entrées validées, le déploiement de votre espace de travail OMS commence. Ce processus doit prendre quelques minutes seulement.

5. Une fois le déploiement terminé, cliquez une nouvelle fois sur **Créer** au bas de la fenêtre de création Service Fabric Analytics. Vérifiez que le nouvel espace de travail s’affiche sous *Espace de travail OMS*. Cette opération ajoute la solution à l’espace de travail que vous venez de créer.

6. L’espace de travail doit néanmoins être connecté aux données de diagnostic provenant de votre cluster. Accédez au groupe de ressources dans lequel vous avez créé la solution Service Fabric Analytics. Vous devez voir s’afficher *ServiceFabric(\<nomEspacedetravailOMS\>)*. Cliquez sur la solution pour accéder à sa page de présentation d’où vous pouvez modifier les paramètres de la solution et les paramètres de l’espace de travail ainsi qu’accéder au portail OMS.

7. Dans le menu de navigation gauche, cliquez sur **Journaux de comptes de stockage**, sous *Sources de données de l’espace de travail*.

8. Dans la page *Journaux de compte de stockage*, cliquez sur **Ajouter** en haut pour ajouter les journaux de votre cluster à l’espace de travail.

9. Cliquez dans **Compte de stockage** pour ajouter le compte approprié créé dans votre cluster. Si vous avez utilisé le nom par défaut, le compte de stockage est nommé *sfdg\<nomGroupeRessources\>*. Vous pouvez également le confirmer en examinant le modèle Azure Resource Manager utilisé pour déployer votre cluster, en vérifiant la valeur utilisée pour `applicationDiagnosticsStorageAccountName`. Vous devrez peut-être également faire défiler vers le bas et cliquer sur **Charger plus** si le nom n’apparaît pas. Cliquez sur le nom de compte de stockage approprié pour le sélectionner.

10. Ensuite, vous devrez spécifier le *Type de données*, qui doit être défini sur **Événements de Service Fabric**.

11. La *Source* doit avoir automatiquement pour valeur *WADServiceFabric\*EventTable*.

12. Cliquez sur **OK** pour connecter votre espace de travail aux journaux de votre cluster.

    ![Ajouter des journaux de compte de stockage à OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Le compte doit maintenant apparaître dans le cadre des *journaux de compte de stockage* dans les sources de données de votre espace de travail.

Vous avez ainsi maintenant ajouté la solution Service Fabric Analytics dans un espace de travail OMS Log Analytics qui est à présent correctement connecté à la plateforme de votre cluster et la table du journal des applications. Vous pouvez ajouter des sources supplémentaires à l’espace de travail de la même façon.

## <a name="next-steps"></a>Étapes suivantes
* [Déployez l’agent OMS](service-fabric-diagnostics-oms-agent.md) sur vos nœuds pour collecter les compteurs de performances, ainsi que les statistiques et les journaux Docker de vos conteneurs.
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) offertes dans le cadre de Log Analytics
* [Utiliser le Concepteur de vues pour créer des vues personnalisées dans Log Analytics](../log-analytics/log-analytics-view-designer.md)
