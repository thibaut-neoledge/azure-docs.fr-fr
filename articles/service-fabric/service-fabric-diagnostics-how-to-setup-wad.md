<properties
   pageTitle="Collecte des journaux avec Azure Diagnostics | Microsoft Azure"
   description="Cet article décrit la procédure de configuration d’Azure Diagnostics pour la collecte de journaux d’un cluster Service Fabric exécuté dans Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/20/2016"
   ms.author="toddabel"/>


# Collecte des journaux avec Azure Diagnostics

Lorsque vous exécutez un cluster Service Fabric dans Azure, il peut être intéressant de collecter les journaux de tous les nœuds pour les regrouper dans un emplacement central. La centralisation des journaux facilite l’analyse et la résolution des problèmes que vous pourriez rencontrer dans votre cluster ou dans les applications et services exécutés dans ce cluster. L’une des façons de charger et collecter les journaux consiste à utiliser l’extension Azure Diagnostics, qui charge les journaux dans Azure Storage. Les journaux ne sont pas vraiment utiles directement dans le stockage, mais un processus externe peut être utilisé pour lire les événements du stockage et les copier dans un produit comme [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou une autre solution d’analyse des journaux.

## Composants requis
Les outils suivants sont utilisés pour exécuter certaines opérations décrites dans ce document :

* [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) (page en lien avec Azure Cloud Services, mais qui contient des informations et des exemples pertinents)
* [Azure Resource Manager](../resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Applets de commande Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Créer une machine virtuelle Windows avec des fonctionnalités de surveillance et de diagnostics à l’aide d’un modèle Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## Les différentes sources de journaux que vous pourriez souhaiter collecter
1. **Journaux Service Fabric :** émis par la plateforme vers les canaux ETW et EventSource standard. Il existe plusieurs types de journaux :
  - Événements opérationnels : ces journaux concernent les opérations exécutées par la plateforme Service Fabric. Par exemple : la création d’applications et de services, les modifications d’état des nœuds et les informations de mise à niveau.
  - [Événements du modèle de programmation Actor](service-fabric-reliable-actors-diagnostics.md)
  - [Événements du modèle de programmation Reliable Services](service-fabric-reliable-services-diagnostics.md)
2. **Événements d’application :** ces événements sont émis à partir de votre code de services et écrits à l’aide de la classe d’assistance EventSource fournie dans les modèles Visual Studio. Pour plus d’informations concernant l’écriture des journaux à partir de votre application, reportez-vous à [cet article sur la surveillance et diagnostic des services dans une configuration de machine locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## Déployer les extensions de diagnostic
La première étape de la collecte de journaux consiste à déployer l’extension Diagnostics sur chaque machine virtuelle du cluster Service Fabric. L’extension Diagnostics collecte les journaux sur chaque machine virtuelle et les charge sur le compte de stockage que vous spécifiez. La procédure varie légèrement selon que vous utilisiez le portail Azure ou Azure Resource Manager et que vous effectuiez le déploiement pour un nouveau cluster ou pour un cluster déjà existant. Examinons les différentes étapes pour chaque scénario.

### Déploiement de l’extension Diagnostics dans le cadre de la création d’un cluster via le portail
Pour déployer l'extension de diagnostic sur les machines virtuelles du cluster dans le cadre de la création d’un cluster, utilisez le volet Diagnostics illustré sur l’image ci-dessous. Pour activer la collecte d’événements Actor ou Reliable Service, vérifiez que l’option Diagnostics est définie sur **On** (paramètre par défaut). Une fois le cluster créé, ces paramètres ne sont pas modifiables à l’aide du portail.

![Paramètre Azure Diagnostics dans le portail pour la création d’un cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Les journaux de prise en charge sont **nécessaires** à l’équipe de support technique Azure pour traiter les demandes que vous créez. Ces journaux sont collectés en temps réel et sont stockés un des comptes de stockage créés dans le groupe de ressources. Le paramètre Diagnostics configure les événements de niveau application, notamment les événements [Actor](service-fabric-reliable-actors-diagnostics.md), les événements [Reliable Service](service-fabric-reliable-services-diagnostics.md) et certains événements de Service Fabric de niveau système à stocker dans Azure Storage. Les produits comme [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou votre propre processus peuvent récupérer les événements dans le compte de stockage. Il n’existe actuellement aucun moyen de filtrer ou de nettoyer les événements qui sont envoyés à la table. Si aucun processus de suppression des événements de la table n’est implémenté, la table continuera à croître. Lors de la création d’un cluster à l’aide du portail, il est conseillé d’exporter le modèle une fois le déploiement terminé. Les modèles peuvent être exportés du portail par

1. Ouvrez votre groupe de ressources.
2. Sélectionnez Paramètres pour afficher le volet Paramètres
3. Sélectionnez Déploiements pour afficher le volet de l’historique de déploiement
4. Sélectionnez un déploiement pour afficher ses détails
5. Sélectionnez Exporter le modèle pour afficher le volet Modèle
6. Sélectionnez Enregistrer dans un fichier pour exporter un fichier .zip contenant le modèle, les paramètres et les fichiers PowerShell.

Après avoir exporté les fichiers, une modification est nécessaire. Modifiez le fichier **parameters.json** et supprimez l’élément **adminPassword**. Cela génère une invite pour le mot de passe lors de l’exécution du script de déploiement. Pour utiliser le modèle téléchargé afin de mettre à jour une configuration

1. Extrayez le contenu vers un dossier sur votre ordinateur local
2. Modifiez le contenu afin qu’il reflète la nouvelle configuration
3. Lancez PowerShell et accédez au dossier où vous avez extrait le contenu
4. Exécutez **deploy.ps1** et entrez l’ID d’abonnement, le nom du groupe de ressources (utilisez le même nom pour mettre à jour la configuration) et un nom de déploiement unique


### Déploiement de l’extension Diagnostics dans le cadre de la création d’un cluster via Azure Resource Manager
Pour créer un cluster à l’aide de Resource Manager, vous devez ajouter le fichier de configuration Diagnostics JSON au modèle Resource Manager du cluster complet, avant de créer le cluster. Nous fournissons un exemple de modèle Resource Manager de cluster à cinq machines virtuelles avec la configuration Diagnostics ajoutée dans le cadre de nos exemples de modèle Resource Manager. Vous pouvez le voir à cet emplacement dans la galerie d’exemples d’Azure : [cluster à cinq nœuds avec exemple de modèle Diagnostics Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad). Pour voir les paramètres de diagnostic dans le modèle Resource Manager, ouvrez le fichier **azuredeploy.json** et recherchez **IaaSDiagnostics**. Pour créer un cluster à partir de ce modèle, cliquez simplement sur le bouton **Déployer sur Azure**, disponible via le lien ci-dessus.

Vous pouvez également télécharger l’exemple Resource Manager, y apporter des modifications et créer un cluster à partir du modèle modifié en utilisant la commande `New-AzureRmResourceGroupDeployment` dans une fenêtre Azure PowerShell. Consultez les informations ci-dessous pour connaître les paramètres nécessaires à la commande. Pour plus d’informations sur le déploiement d’un groupe de ressources à l’aide de PowerShell, consultez l’article [Déployer un groupe de ressources avec un modèle Azure Resource Manager](../resource-group-template-deploy.md)

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### Déployer l’extension Diagnostics sur un cluster existant
Si les diagnostics ne sont pas déployés sur l’un de vos clusters existants ou que vous souhaitez modifier une configuration existante, vous pouvez l’ajouter ou la mettre à jour en appliquant la procédure ci-après. Modifiez le modèle ARM utilisé pour créer le cluster existant ou téléchargez le modèle sur le portail, comme décrit ci-dessus. Modifiez le fichier **template.json** en effectuant les opérations suivantes :

Ajouter une ressource de stockage au modèle en l’ajoutant à la section de ressources.

##### Mettre à jour la section de ressources
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

 Ensuite, ajoutez la section parameters juste après les définitions de compte de stockage, entre « supportLogStorageAccountName » et « vmNodeType0Name ». Remplacez l’espace réservé *nom du compte de stockage* par le nom du compte de stockage souhaité.

##### Mettre à jour la section des paramètres
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
Mettez ensuite à jour la section *VirtualMachineProfile* de **template.json**, en ajoutant le code suivant dans le tableau « extensions ». N’oubliez pas d’ajouter une virgule au début ou à la fin, en fonction de l’emplacement.

##### Ajouter des éléments au tableau d’extensions de VirtualMachineProfile
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

Après avoir modifié le fichier **template.json** comme décrit, republiez le modèle ARM. Si le modèle a été exporté, exécutez le fichier **deploy.ps1** pour republier le modèle. Après le déploiement, assurez-vous que *ProvisioningState* est *Succeeded*.


## Mise à jour de Diagnostics pour collecter et charger des journaux depuis de nouveaux canaux EventSource
Pour mettre à jour les diagnostics de manière à collecter des journaux à partir de nouveaux canaux EventSource représentant une nouvelle application que vous allez déployer, vous devez simplement exécuter les mêmes étapes que celles décrites dans la [section ci-dessus](#deploywadarm) relative à la configuration des diagnostics pour un cluster existant. Vous devrez mettre à jour la section *EtwEventSourceProviderConfiguration* dans le fichier **template.json** pour ajouter des entrées pour les nouveaux EventSources avant d’appliquer la mise à jour de la configuration avec la commande PowerShell *New-AzureRmResourceGroupDeployment*.


## Étapes suivantes
Vérifiez les événements de diagnostic émis pour [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) et [Reliable Services](service-fabric-reliable-services-diagnostics.md) pour comprendre plus en détail les événements auxquels vous devriez être attentif lors de la résolution des problèmes.


## Articles connexes
* [Découvrez comment collecter des compteurs de performances ou des journaux à l’aide des extensions de diagnostic](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

<!---HONumber=AcomDC_0601_2016-->