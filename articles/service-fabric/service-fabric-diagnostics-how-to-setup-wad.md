---
title: Collecte des journaux avec Azure Diagnostics | Documents Microsoft
description: "Cet article décrit la procédure de configuration d’Azure Diagnostics pour la collecte de journaux d’un cluster Service Fabric exécuté dans Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 9f7e1fa5-6543-4efd-b53f-39510f18df56
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: e8b6709d4b2cbb71fdf2d83c9a95998ab147b4ba
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Collecte des journaux avec Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Lorsque vous exécutez un cluster Service Fabric dans Azure, il peut être intéressant de collecter les journaux de tous les nœuds pour les regrouper dans un emplacement central. La centralisation des journaux vous permet d’analyser et résoudre les problèmes que vous pourriez rencontrer dans votre cluster ou dans les applications et services exécutés dans ce cluster.

L’une des façons de charger et collecter les journaux consiste à utiliser l’extension Azure Diagnostics, qui assure le chargement des journaux dans Stockage Azure, Azure Application Insights ou Azure Event Hubs. Les journaux ne sont pas directement exploitables dans le stockage ou dans Event Hubs. Cependant, vous pouvez utiliser un processus externe pour lire les événements à partir du stockage et les placer dans un produit tel que [Log Analytics](../log-analytics/log-analytics-service-fabric.md) ou une autre solution d’analyse de journaux. [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) est fourni avec un service complet de recherche dans les journaux et d’analyse des données intégré.

## <a name="prerequisites"></a>Composants requis
Les outils suivants sont utilisés pour exécuter certaines opérations décrites dans ce document :

* [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) (page en lien avec Azure Cloud Services, mais qui contient des informations et des exemples pertinents)
* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Applets de commande Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Modèle Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-sources-that-you-might-want-to-collect"></a>Sources de journaux que vous pourriez vouloir collecter
* **Journaux Service Fabric :** émis par la plateforme vers les canaux ETW (Event Tracing for Windows) et EventSource standard. Il existe plusieurs types de journaux :
  * Événements opérationnels : ces journaux concernent les opérations exécutées par la plateforme Service Fabric. Par exemple : la création d’applications et de services, les modifications d’état des nœuds et les informations de mise à niveau.
  * [Événements du modèle de programmation Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Événements du modèle de programmation Reliable Services](service-fabric-reliable-services-diagnostics.md)
* **Événements d’application** : ces événements sont émis à partir de votre code de services et écrits à l’aide de la classe d’assistance EventSource fournie dans les modèles Visual Studio. Pour plus d’informations concernant l’écriture des journaux à partir de votre application, consultez la page [Surveillance et diagnostic des services dans une configuration de développement d’ordinateur local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Déployer l’extension Diagnostics
La première étape de la collecte de journaux consiste à déployer l’extension Diagnostics sur chaque machine virtuelle du cluster Service Fabric. Cette extension collecte les journaux sur chaque machine virtuelle et les charge dans le compte de stockage que vous spécifiez. Les étapes varient légèrement selon que vous utilisez le portail Azure ou Azure Resource Manager. Les étapes varient également selon que le déploiement fait partie de la création du cluster ou est effectué pour un cluster qui existe déjà. Examinons les différentes étapes pour chaque scénario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Déploiement de l’extension Diagnostics dans le cadre de la création d’un cluster via le portail
Pour déployer l’extension Diagnostics sur les machines virtuelles du cluster dans le cadre de la création d’un cluster, utilisez le volet de paramètre Diagnostics illustré sur l’image ci-dessous. Pour activer la collecte d’événements Reliable Actors ou Reliable Services, vérifiez que l’option Diagnostics est définie sur **On** (paramètre par défaut). Une fois le cluster créé, vous ne pouvez plus modifier ces paramètres à l’aide du portail.

![Paramètres Azure Diagnostics dans le portail pour la création d’un cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

L’équipe de support technique Azure *a besoin* des journaux de prise en charge pour traiter les demandes de support que vous créez. Ces journaux sont collectés en temps réel et sont stockés dans un des comptes de stockage créés dans le groupe de ressources. Les paramètres Diagnostics configurent les événements au niveau de l’application. Il s’agit notamment des événements [Reliable Actors](service-fabric-reliable-actors-diagnostics.md), [Reliable Services](service-fabric-reliable-services-diagnostics.md) et de certains événements Service Fabric de niveau système devant être stockés dans le stockage Azure.

Les produits comme [Elastic Search](https://www.elastic.co/guide/index.html) ou votre propre processus peuvent récupérer les événements à partir du compte de stockage. Il n’existe actuellement aucun moyen de filtrer ou de nettoyer les événements qui sont envoyés à la table. Si vous n’implémentez aucun processus de suppression des événements de la table, la table continuera à croître.

Lorsque vous créez un cluster à l’aide du portail, nous vous recommandons vivement de télécharger le modèle **avant de cliquer sur OK** pour créer le cluster. Pour plus de détails, voir [Configurer un cluster Service Fabric à l’aide d’un modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Vous aurez besoin du modèle pour pouvoir apporter des modifications ultérieurement, car vous ne pouvez effectuer aucune modification à l’aide du portail.

Vous pouvez exporter des modèles à partir du portail à l’aide de la procédure suivante. Ces modèles peuvent toutefois être plus difficiles à utiliser car ils peuvent comporter des valeurs null qui ne contiennent pas les informations requises.

1. Ouvrez votre groupe de ressources.
2. Sélectionnez **Paramètres** pour afficher le volet Paramètres.
3. Sélectionnez **Déploiements** pour afficher le volet de l’historique de déploiement.
4. Sélectionnez un déploiement pour afficher ses détails.
5. Sélectionnez **Exporter le modèle** pour afficher le volet Modèle.
6. Sélectionnez **Enregistrer dans un fichier** pour exporter un fichier .zip contenant le modèle, les paramètres et les fichiers PowerShell.

Une fois les fichiers exportés, vous devez effectuer une modification. Modifiez le fichier parameters.json et supprimez l’élément **adminPassword**. Cela génère une invite pour le mot de passe lors de l’exécution du script de déploiement. Lorsque vous exécutez le script de déploiement, vous devrez peut-être corriger les valeurs de paramètre null.

Pour utiliser le modèle téléchargé afin de mettre à jour une configuration :

1. Extrayez le contenu vers un dossier sur votre ordinateur local.
2. Modifiez le contenu afin qu’il reflète la nouvelle configuration.
3. Lancez PowerShell et accédez au dossier où vous avez extrait le contenu.
4. Exécutez **deploy.ps1** et entrez l’ID d’abonnement, le nom du groupe de ressources (utilisez le même nom pour mettre à jour la configuration) et un nom de déploiement unique.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Déploiement de l’extension Diagnostics dans le cadre de la création d’un cluster via Azure Resource Manager
Pour créer un cluster à l’aide de Resource Manager, vous devez ajouter le fichier de configuration Diagnostics JSON au modèle Resource Manager du cluster complet avant de créer le cluster. Nous fournissons un exemple de modèle Resource Manager de cluster à cinq machines virtuelles avec la configuration Diagnostics ajoutée dans le cadre de nos exemples de modèle Resource Manager. Vous pouvez le voir à cet emplacement dans la galerie d’exemples d’Azure : [cluster à cinq nœuds avec exemple de modèle Diagnostics Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype).

Pour voir les paramètres de diagnostic dans le modèle Resource Manager, ouvrez le fichier azuredeploy.json et recherchez **IaaSDiagnostics**. Pour créer un cluster à l’aide de ce modèle, cliquez sur le bouton **Déployer sur Azure**, disponible via le lien précédent.

Vous pouvez également télécharger l’exemple Resource Manager, y apporter des modifications et créer un cluster à partir du modèle modifié en utilisant la commande `New-AzureRmResourceGroupDeployment` dans une fenêtre Azure PowerShell. Consultez le code suivant pour les paramètres que vous passez à la commande. Pour plus d’informations sur le déploiement d’un groupe de ressources à l’aide de PowerShell, consultez l’article [Déployer un groupe de ressources avec un modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Déployer l’extension Diagnostics sur un cluster existant
Si Diagnostics n’est pas déployé sur l’un de vos clusters existants ou que vous souhaitez modifier une configuration existante, vous pouvez l’ajouter ou la mettre à jour. Modifiez le modèle Resource Manager utilisé pour créer le cluster existant ou téléchargez le modèle sur le portail, comme décrit ci-dessus. Modifiez le fichier template.json en effectuant les opérations suivantes.

Ajouter une ressource de stockage au modèle en l’ajoutant à la section de ressources.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Ensuite, ajoutez la section parameters juste après les définitions de compte de stockage, entre `supportLogStorageAccountName` et `vmNodeType0Name`. Remplacez l’espace réservé *nom du compte de stockage* par le nom du compte de stockage.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Mettez ensuite à jour la section `VirtualMachineProfile` du fichier template.json en ajoutant le code suivant dans le tableau extensions. N’oubliez pas d’ajouter une virgule au début ou à la fin, en fonction de l’emplacement d’insertion.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Après avoir modifié le fichier template.json comme décrit, republiez le modèle Resource Manager. Si le modèle a été exporté, exécutez le fichier deploy.ps1 pour republier le modèle. Après le déploiement, assurez-vous que **ProvisioningState** présente la valeur **Succeeded**.

## <a name="update-diagnostics-to-collect-health-and-load-events"></a>Mettre à jour les diagnostics pour collecter les événements d’intégrité et de charge

Depuis la version 5.4 de Service Fabric, il est possible de collecter les événements liés aux mesures d’intégrité et de charge. Ces événements reflètent les événements générés par le système ou votre code à l’aide d’API de création de rapports d’intégrité ou de charge comme [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) ou [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Cela permet non seulement de consolider et de visualiser l’intégrité du système dans le temps, mais aussi de générer des alertes en fonction de certains événements d’intégrité et de charge. Pour afficher ces événements dans la visionneuse d’événements de diagnostic de Visual Studio, ajoutez « Microsoft-ServiceFabric:4:0x4000000000000008 » à la liste des fournisseurs ETW.

Pour collecter les événements, modifiez le modèle Resource Manager de manière à inclure

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Mise à jour de Diagnostics pour collecter et charger des journaux depuis de nouveaux canaux EventSource
Pour mettre à jour Diagnostics de manière à collecter des journaux à partir de nouveaux canaux EventSource représentant une nouvelle application que vous allez déployer, exécutez les mêmes étapes que celles décrites dans la [section ci-dessus](#deploywadarm) relative à la configuration de Diagnostics pour un cluster existant.

Mettez à jour la section `EtwEventSourceProviderConfiguration` dans le fichier template.json pour ajouter des entrées pour les nouveaux canaux EventSource avant d’appliquer la mise à jour de la configuration à l’aide de la commande PowerShell `New-AzureRmResourceGroupDeployment`. Le nom de la source de l’événement est défini dans le cadre de votre code dans le fichier ServiceEventSource.cs généré par Visual Studio.

Par exemple, si votre source d’événements est nommée My-Eventsource, ajoutez le code suivant pour placer les événements de My-Eventsource dans une table nommée MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Pour collecter des compteurs de performances ou des journaux d’événements, modifiez le modèle Resource Manager en utilisant les exemples fournis dans [Créer une machine virtuelle Windows avec des fonctionnalités de surveillance et de diagnostics à l’aide d’un modèle Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Republiez ensuite le modèle Resource Manager.

## <a name="next-steps"></a>Étapes suivantes
Pour comprendre plus en détail les événements auxquels vous devriez être attentif lors de la résolution des problèmes, vérifiez les événements de diagnostic émis pour [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) et [Reliable Services](service-fabric-reliable-services-diagnostics.md).

## <a name="related-articles"></a>Articles connexes
* [Découvrez comment collecter des compteurs de performances ou des journaux à l’aide de l’extension Diagnostics](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Solution Service Fabric dans Log Analytics](../log-analytics/log-analytics-service-fabric.md)


