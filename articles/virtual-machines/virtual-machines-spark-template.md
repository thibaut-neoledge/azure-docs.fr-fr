<properties
	pageTitle="Spark sur modèle du Gestionnaire des ressources Ubuntu | Microsoft Azure"
	description="Déploiement facilement d’un nouveau cluster Spark sur des machines virtuelles Ubuntu en utilisant Azure PowerShell ou l’interface de ligne de commande d’Azure et un modèle du Gestionnaire des ressources"
	services="virtual-machines"
	documentationCenter=""
	authors="paolosalvatori"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="multiple"
	ms.date="05/16/2015"
	ms.author="paolosalvatori"/>

# Spark sous Ubuntu avec un modèle Resource Manager

Apache Spark est un moteur rapide pour le traitement des données à grande échelle. Spark a un moteur d’exécution DAG avancé qui prend en charge les flux de données cycliques et le calcul en mémoire. Il peut accéder à différentes sources de données, notamment HDFS, Spark, HBase et S3.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique Vous ne pouvez pas déployer cette ressource avec le modèle de déploiement classique.


En plus de s’exécuter sur les gestionnaires de cluster Mesos ou YARN, Spark fournit un mode de déploiement autonome simple. Ce didacticiel vous guide dans l’utilisation d’un exemple de modèle Azure Resource Manager pour déployer un cluster Spark sur des machines virtuelles Ubuntu via [Azure PowerShell](../powershell-install-configure.md) ou l’[interface de ligne de commande Azure](../xplat-cli-install.md).

Ce modèle déploie un cluster Spark sur des machines virtuelles Ubuntu. Ce modèle configure également un compte de stockage, un réseau virtuel, des groupes à haute disponibilité, des adresses IP publiques et les interfaces réseau requis par l’installation. Le cluster Spark est créé derrière un sous-réseau : il n’existe donc pas d’accès IP public au cluster. Une « jump box » facultative peut être déployée dans le cadre du déploiement. Cette « jump box » est une machine virtuelle Ubuntu également déployée dans le sous-réseau, mais elle *expose* une adresse IP publique avec un port SSH ouvert auquel vous pouvez vous connecter. Vous pouvez ensuite exécuter SSH pour toutes les machines virtuelles Spark du sous-réseau depuis la « jump box ».

Ce modèle utilise un concept de « taille de t-shirt » pour spécifier une configuration « small », « medium » ou « large » du cluster Spark. Lorsque la langue du modèle prend en charge un dimensionnement de modèle plus dynamique, ce paramètre peut être modifié pour spécifier le nombre de nœuds principaux et de nœuds subordonnés, la taille des machines virtuelles, etc., du cluster Spark. Actuellement, vous pouvez voir la taille des machines virtuelles et le nombre de nœuds principaux et subordonnés définis dans le fichier azuredeploy.json, dans les variables **tshirtSizeS**, **tshirtSizeM** et **tshirtSizeL**.

- S : 1 principal, 1 subordonné
- M : 1 principal, 4 subordonnés
- L : 1 principal, 6 subordonnés

Les clusters récemment déployés basés sur ce modèle disposent de la topologie décrite dans le schéma suivant, bien que d’autres topologies puissent être obtenues facilement grâce à la personnalisation du modèle présenté dans cet article :

![cluster-architecture](media/virtual-machines-spark-template/cluster-architecture.png)

Comme le montre l’illustration ci-dessus, la topologie de déploiement comprend les éléments suivants :

-	Un nouveau compte de stockage qui héberge le disque du système d’exploitation des machines virtuelles nouvellement créées.
-	Un réseau virtuel avec un sous-réseau. Toutes les machines virtuelles créées par le modèle sont configurées dans ce réseau virtuel.
-	Un groupe à haute disponibilité pour le nœud principal et les nœuds subordonnés.
-	Un nœud principal dans le groupe à haute disponibilité nouvellement créé.
-	Quatre nœuds subordonnés s’exécutant dans le même sous-réseau virtuel et le même groupe à haute disponibilité que le nœud principal.
-	Une machine virtuelle « jump box » située dans le même réseau virtuel et le même sous-réseau qui peut être utilisée pour accéder au cluster.

Spark version 3.0.0 est la version par défaut et peut être remplacé par n’importe quel exécutable binaire pré-généré disponible sur le référentiel Spark. Il existe également une configuration dans le script pour enlever les marques de commentaire de la build dans la source. Une adresse IP statique est affectée à chaque nœud maître Spark : 10.0.0.10. Une adresse IP statique est attribuée à chaque nœud esclave Spark afin de contourner la limitation actuelle qui empêche de pouvoir composer dynamiquement une liste d'adresses IP à partir du modèle. (Par défaut, le premier nœud se voit attribuer l'adresse IP privée 10.0.0.30, le deuxième nœud l’adresse 10.0.0.31 et ainsi de suite.) Pour consulter les erreurs de déploiement, accédez au nouveau portail Azure et regardez sous **Groupe de ressources** > **Dernier déploiement** > **Vérifier les détails de l’opération**.

Avant de voir plus en détail Azure Resource Manager et le modèle utilisé pour ce déploiement, vérifiez que vous disposez d’une version configurée et opérationnelle d’Azure PowerShell et de l’interface de ligne de commande Azure.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Création d’un cluster Spark à l’aide d’un modèle Resource Manager

Procédez comme suit pour créer un cluster Spark en utilisant un modèle Resource Manager provenant du référentiel de modèles GitHub, en utilisant Azure PowerShell ou la CLI Azure.

### Étape 1-a : télécharger les fichiers de modèle en utilisant Azure PowerShell

Créez un dossier local pour le modèle JSON et les autres fichiers associés (par exemple C:\\Azure\\Templates\\Spark).

Incluez le nom de votre dossier local et exécutez les commandes suivantes :

```powershell
# Define variables
$folderName="C:\Azure\Templates\Spark"
$baseAddress = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/spark-on-ubuntu/"
$webclient = New-Object System.Net.WebClient
$files = $("azuredeploy.json", `
         "azuredeploy-parameters.json", `
         "jumpbox-resources-disabled.json", `
         "jumpbox-resources-enabled.json",
         "metadata.json", `
         "shared-resources.json", `
         "spark-cluster-install.sh", `
         "jumpbox-resources-enabled.json")

# Download files
foreach ($file in $files)
{
    $url = $baseAddress + $file
    $filePath = $folderName + $file
    $webclient.DownloadFile($baseAddress + $file, $folderName + $file)
    Write-Host $url "downloaded to" $filePath
}
```

### Étape 1-b : télécharger les fichiers de modèle en utilisant l’interface de ligne de commande Azure

Clonez le référentiel de modèles entier en utilisant un client Git de votre choix, par exemple :

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Lorsque le clonage est terminé, recherchez le dossier **spark-on-ubuntu** dans le répertoire C:\\Azure\\Templates.

### Étape 2 : (facultatif) comprendre les paramètres du modèle

Lorsque vous créez un cluster Spark à l’aide d’un modèle, vous devez spécifier un jeu de paramètres de configuration pour gérer plusieurs des paramètres requis. En déclarant ces paramètres dans la définition du modèle, vous pouvez spécifier des valeurs lors de l’exécution du déploiement via un fichier externe ou sur une ligne de commande.

Dans la section « parameters » en haut du fichier azuredeploy.json, vous trouverez le jeu de paramètres requis par le modèle pour configurer un cluster Spark. Voici un exemple de la section « parameters » dans le fichier azuredeploy.json de ce modèle :

```json
"parameters": {
	"adminUsername": {
		"type": "string",
		"metadata": {
			"Description": "Administrator user name used when provisioning virtual machines"
		}
	},
	"adminPassword": {
		"type": "securestring",
		"metadata": {
			"Description": "Administrator password used when provisioning virtual machines"
		}
	},
	"imagePublisher": {
		"type": "string",
		"defaultValue": "Canonical",
		"metadata": {
			"Description": "Image publisher"
		}
	},
	"imageOffer": {
		"type": "string",
		"defaultValue": "UbuntuServer",
		"metadata": {
			"Description": "Image offer"
		}
	},
	"imageSKU": {
		"type": "string",
		"defaultValue": "14.04.2-LTS",
		"metadata": {
			"Description": "Image SKU"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "uniquesparkstoragev12",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"region": {
		"type": "string",
		"defaultValue": "West US",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "myVNETspark",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the cluster"
		}
	},
	"dataDiskSize": {
		"type": "int",
		"defaultValue": 100,
		"metadata": {
			"Description": "Size of the data disk attached to Spark nodes (in GB)"
		}
	},
	"addressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/16",
		"metadata": {
			"Description": "The network address space for the virtual network"
		}
	},
	"subnetName": {
		"type": "string",
		"defaultValue": "Subnet-1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned into"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"sparkVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Spark package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"sparkClusterName": {
		"type": "string",
		"metadata": {
			"Description": "The arbitrary name of the Spark cluster (maps to cluster's configuration file name)"
		}
	},
	"sparkNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"sparkSlaveNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.3",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "enabled",
		"allowedValues": [
		"enabled",
		"disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Spark nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "S",
		"allowedValues": [
		"S",
		"M",
		"L"
		],
		"metadata": {
			"Description": "T-shirt size of the Spark cluster"
		}
	}
}
```

Chaque paramètre contient des détails, tels que le type de données et les valeurs autorisées. Ceci permet la validation des paramètres passés pendant l’exécution du modèle en mode interactif (par exemple, Azure PowerShell ou l’interface de ligne de commande Azure), ainsi qu’une interface utilisateur à détection automatique, qui peut être construite de manière dynamique par l’analyse de la liste des paramètres requis et leurs descriptions.

### Étape 3-a : déployer un cluster Spark en utilisant un modèle à l’aide d’Azure PowerShell

Préparez un fichier de paramètres pour votre déploiement en créant un fichier JSON contenant des valeurs d’exécution pour tous les paramètres. Ce fichier est ensuite transmis en tant qu’entité unique à la commande de déploiement. Si vous n’incluez pas de fichier de paramètres, Azure PowerShell utilisera les valeurs par défaut spécifiées dans le modèle et vous invitera à renseigner les valeurs restantes.

Voici un exemple de jeu de paramètres du fichier azuredeploy-parameters.json. Notez que vous devez fournir des valeurs valides pour les paramètres **storageAccountName**, **adminUsername** et **adminPassword**, ainsi que les personnalisations des autres paramètres :

```json
{
  "storageAccountName": {
    "value": "paolosspark"
  },
  "adminUsername": {
    "value": "paolos"
  },
  "adminPassword": {
    "value": "Passw0rd!"
  },
  "region": {
    "value": "West US"
  },
  "virtualNetworkName": {
    "value": "sparkClustVnet"
  },
  "subnetName": {
    "value": "Subnet1"
  },
  "addressPrefix": {
    "value": "10.0.0.0/16"
  },
  "subnetPrefix": {
    "value": "10.0.0.0/24"
  },
  "sparkVersion": {
    "value": "3.0.0"
  },
  "sparkClusterName": {
    "value": "spark-arm-cluster"
  },
  "sparkNodeIPAddressPrefix": {
		"value": "10.0.0.1"
  },
  "sparkSlaveNodeIPAddressPrefix": {
    "value": "10.0.0.3"
  },
  "jumpbox": {
    "value": "enabled"
  },
  "tshirtSize": {
    "value": "M"
  }
}
```
Entrez un nom de déploiement Azure, un nom de groupe de ressources, un emplacement Azure et le dossier du fichier de déploiement JSON enregistré. Exécutez ensuite ces commandes :

```powershell
$deployName="<deployment name>"
$RGName="<resource group name>"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"
New-AzureResourceGroup -Name $RGName -Location $locName
New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParamterFile $templateParameterFile -TemplateFile $templateFile
```
> [AZURE.NOTE]**$RGName** doit être unique dans votre abonnement.

Lorsque vous exécutez la commande **New-AzureResourceGroupDeployment**, elle extrait les valeurs de paramètre à partir du fichier de paramètres JSON et démarre l’exécution du modèle correspondant. Le fait de définir et d’utiliser plusieurs fichiers de paramètres sur vos différents environnements (par exemple, test, production, etc.) favorise la réutilisation du modèle et simplifie les solutions contenant plusieurs environnements complexes.

Lors du déploiement, n’oubliez pas qu’un compte Azure Storage doit être créé. Le nom fourni en tant que paramètre de compte de stockage doit donc être unique et respecter la configuration requise pour un compte Azure Storage (minuscules et chiffres uniquement).

Lors du déploiement, le résultat suivant doit s’afficher :

```powershell
PS C:\> New-AzureResourceGroup -Name $RGName -Location $locName

ResourceGroupName : SparkResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

ResourceId        : /subscriptions/2018abc3-dbd9-4437-81a8-bb3cf56138ed/resourceGroups/sparkresourcegroup

PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
VERBOSE: 10:08:28 AM - Template is valid.
VERBOSE: 10:08:28 AM - Create template deployment 'SparkTestDeployment'.
VERBOSE: 10:08:37 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
VERBOSE: 10:09:11 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
VERBOSE: 10:09:13 AM - Resource Microsoft.Network/networkInterfaces 'nicsl0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Network/networkInterfaces 'nic0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
VERBOSE: 10:09:24 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is succeeded
VERBOSE: 10:11:11 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is running
VERBOSE: 10:11:42 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
VERBOSE: 10:13:10 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is succeeded
VERBOSE: 10:13:15 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is running
VERBOSE: 10:16:58 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is succeeded
VERBOSE: 10:19:05 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is succeeded


DeploymentName    : SparkTestDeployment
ResourceGroupName : SparkResourceGroup
ProvisioningState : Succeeded
Timestamp         : 5/5/2015 5:19:05 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     Paolos
                    adminPassword    SecureString
                    imagePublisher   String                     Canonical
                    imageOffer       String                     UbuntuServer
                    imageSKU         String                     14.04.2-LTS
                    storageAccountName  String                  paolosspark
                    region           String                     West US
                    virtualNetworkName  String                  sparkClustVnet
                    addressPrefix    String                     10.0.0.0/16
										subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    sparkVersion     String                     3.0.0
                    sparkClusterName  String                    spark-arm-cluster
                    sparkNodeIPAddressPrefix  String            10.0.0.1
                    sparkSlaveNodeIPAddressPrefix  String       10.0.0.3
                    jumpbox          String                     enabled
                    tshirtSize       String                     M
```

Pendant et après le déploiement, vous pouvez vérifier toutes les requêtes effectuées durant la configuration et notamment toutes les erreurs survenues.

Pour ce faire, accédez au [portail Azure](https://portal.azure.com), puis procédez comme suit :

- Dans la barre de navigation située sur la gauche, cliquez sur **Parcourir**, puis faites défiler les options vers le bas et cliquez sur **Groupes de ressources**.
- Cliquez sur le groupe de ressources que vous venez de créer pour afficher le panneau « Groupe de ressources ».
- En cliquant sur l’histogramme **Événements** dans la partie **Analyse** du panneau « Groupe de ressources », vous pouvez voir les événements de votre déploiement.
- En cliquant sur les différents événements, vous pouvez accéder à des informations plus détaillées sur chaque opération menée pour le compte du modèle.

![événements-portail](media/virtual-machines-spark-template/portal-events.png)

Après vos tests, si vous devez supprimer ce groupe de ressources et toutes ses ressources (le compte de stockage, la machine virtuelle et le réseau virtuel), utilisez cette commande unique :

```powershell
Remove-AzureResourceGroup -Name "<resource group name>" -Force
```

### Étape 3-b : déployer un cluster Spark en utilisant un modèle à l’aide de l’interface de ligne de commande Azure

Pour déployer un cluster Spark par le biais de l’interface de ligne de commande Azure, commencez par créer un groupe de ressources en spécifiant un nom et un emplacement :

	azure group create SparkResourceGroup "West US"

Intégrez ce nom de groupe de ressources, l’emplacement du fichier de modèle JSON et l’emplacement du fichier de paramètres (consultez la section Azure PowerShell ci-dessus pour plus de détails) dans la commande suivante :

	azure group deployment create SparkResourceGroup -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Vous pouvez vérifier l’état des différents déploiements de ressources en utilisant la commande suivante :

	azure group deployment list SparkResourceGroup

## Visite guidée de l’organisation des fichiers et de la structure du modèle Spark

Pour créer un modèle Resource Manager robuste et réutilisable, vous devez également prendre en compte l’organisation d’une série de tâches complexes et interdépendantes, qui sont requises lors du déploiement d’une solution complexe comme Spark. En tirant parti des capacités de liaison des modèles et de l’itération des ressources de Resource Manager en plus de l’exécution de scripts via les extensions associées, vous pouvez implémenter une approche modulaire qui peut être réutilisée lors de n’importe quel déploiement basé sur un modèle complexe.

Ce schéma décrit les relations entre tous les fichiers téléchargés à partir de GitHub pour ce déploiement :

![fichiers-Spark](media/virtual-machines-spark-template/spark-files.png)

Cette section vous guide dans la structure du fichier azuredeploy.json pour le cluster Spark.

Si vous n’avez pas déjà téléchargé une copie du fichier de modèle, spécifiez un dossier local comme emplacement pour le fichier et créez-le (par exemple C:\\Azure\\Templates\\Spark). Indiquez le nom du dossier, puis exécutez ces commandes :

```powershell
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Ouvrez le modèle azuredeploy.json dans un éditeur de texte ou dans un outil de votre choix. Les informations suivantes décrivent la structure du fichier de modèle et l’objectif de chaque section. Vous pouvez également voir le contenu de ce modèle dans votre navigateur [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json).

### Section "parameters"

La section « parameters » du fichier azuredeploy.json indique les paramètres modifiables utilisés dans ce modèle. Le fichier azuredeploy-parameters.json décrit précédemment dans cet article est utilisé pour transmettre des valeurs dans la section « parameters » du fichier azuredeploy.json pendant l’exécution du modèle.

Voici un exemple de paramètre pour le concept de « taille de t-shirt » :

```json
"tshirtSize": {
    "type": "string",
    "defaultValue": "S",
    "allowedValues": [
        "S",
        "M",
        "L"
    ],
    "metadata": {
        "Description": "T-shirt size of the Spark deployment"
    }
},
```

> [AZURE.NOTE]Notez que **defaultValue** peut être spécifié, ainsi que **allowedValues**.

### section "variables"

La section « variables » indique les variables qui peuvent être utilisées dans ce modèle. Cette section contient un certain nombre de champs (types de données ou fragments JSON) définis sur des valeurs constantes ou calculées lors de l’exécution. Voici quelques exemples, du plus simple au plus complexe :

```json
"variables": {
	"vmStorageAccountContainerName": "vhd",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
	"subnetRef": "[concat(variables('vnetID'),'/subnets/',parameters('subnetName'))]",
	"computerNamePrefix": "sparknode",
	"scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/",
	"templateUrl": "[variables('scriptUrl')]",
	"sharedTemplateName": "shared-resources",
	"jumpboxTemplateName": "jumpbox-resources-",
	"tshirtSizeS": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 1,
		"vmSize": "Standard_A2"
	},
	"tshirtSizeM": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 4,
		"vmSize": "Standard_A4"
	},
	"tshirtSizeL": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 6,
		"vmSize": "Standard_A7"
	},
	"numberOfMasterInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfMasterInstances]",
	"numberOfSlavesInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfSlavesInstances]",
	"vmSize": "[variables(concat('tshirtSize', parameters('tshirtSize'))).vmSize]"
},
```

**vmStorageAccountContainerName** est un exemple de variable nom/valeur simple. **vnetID** est un exemple de variable calculée lors de l’exécution à l’aide des fonctions **resourceId** et **parameters**. Les valeurs des variables **numberOfMasterInstances** et **vmSize** sont calculées au moment de l’exécution à l’aide des fonctions **concat**, **variables** et **parameters**.

Si vous souhaitez personnaliser la taille du déploiement du cluster Spark, vous pouvez modifier les propriétés des variables **tshirtSizeS**, **tshirtSizeM** et **tshirtSizeL** dans le modèle azuredeploy.json.

Vous trouverez plus d’informations sur le langage des modèles sur MSDN, dans [Langage des modèles Azure Resource Manager](../resource-group-authoring-templates.md).


### section "resources"

La plupart des actions se déroulent dans la section "resources". En effectuant une recherche minutieuse dans cette section, vous pouvez immédiatement identifier deux cas distincts : le premier est un élément défini de type `Microsoft.Resources/deployments` qui appelle principalement un déploiement imbriqué au sein du déploiement principal. Le second est la propriété **templateLink** (et la propriété **contentVersion** associée) qui permet de spécifier un fichier de modèle lié qui sera appelé en transmettant un jeu de paramètres comme entrée. Ils sont visibles dans le fragment de modèle suivant :

```json
"resources": [
{
	"name": "shared-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[concat(variables('templateUrl'), variables('sharedTemplateName'), '.json')]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"region": {
				"value": "[parameters('region')]"
			},
			"storageAccountName": {
				"value": "[parameters('storageAccountName')]"
			},
			"virtualNetworkName": {
				"value": "[parameters('virtualNetworkName')]"
			},
			"addressPrefix": {
				"value": "[parameters('addressPrefix')]"
			},
			"subnetName": {
				"value": "[parameters('subnetName')]"
			},
			"subnetPrefix": {
				"value": "[parameters('subnetPrefix')]"
			}
		}
	}
},
```

Ce premier exemple présente clairement la façon dont azuredeploy.json est organisé en mécanisme d’orchestration dans ce scénario, pour appeler un certain nombre d’autres fichiers de modèle, chacun d’eux étant responsable d’une partie des activités de déploiement requises.

Vous pouvez notamment remarquer les modèles liés suivants qui sont utilisés pour ce déploiement :

-	**shared-resource.json** : contient la définition de toutes les ressources partagées lors du déploiement. Par exemple, c’est le cas des comptes de stockage utilisés pour stocker des disques de système d’exploitation de machines virtuelles et des réseaux virtuels.
-	**jumpbox-resources-enabled.json** : déploie la machine virtuelle « jump box » et toutes les ressources liées, comme l’interface réseau, l’adresse IP publique et le point de terminaison d’entrée utilisé pour exécuter SSH dans l’environnement.

Après avoir appelé ces deux modèles, le fichier azuredeploy.json configure toutes les ressources connectées et les machines virtuelles du nœud du cluster Spark (par exemple les cartes réseau, les adresses IP privées, etc.). Ce modèle déploie également les extensions de machine virtuelle (scripts personnalisés pour Linux) et appelle un script Bash (spark-cluster-install.sh) pour installer et configurer physiquement Spark sur chaque nœud.

Penchons-nous sur l’*utilisation* de ce dernier modèle, azuredeploy.json, car il s’agit de l’un des plus intéressants du point de vue du développement des modèles. Un concept important à retenir est la façon dont un fichier de modèle unique peut déployer plusieurs copies d’un type de ressource unique et, pour chaque instance, définir des valeurs uniques pour les paramètres requis. Ce concept est appelé **itération de ressource**.

Une ressource qui utilise l’élément **copy** se copie elle-même un nombre de fois spécifié dans le paramètre **count** de l’élément **copy**. Pour tous les paramètres où il est nécessaire de spécifier des valeurs uniques pour différentes instances de la ressource déployée, vous pouvez utiliser la fonction **copyindex()** pour obtenir une valeur numérique indiquant l’index actuel dans cette création d’itérations de ressource spécifique. Dans le fragment suivant d’azuredeploy.json, ce concept est appliqué à plusieurs cartes réseau, machines virtuelles et extensions de machine virtuelle créées pour le cluster Spark :

```json
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nic', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicLoop",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nicsl', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicslLoop",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkSlaveNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Compute/virtualMachines",
	"name": "[concat('mastervm', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
	"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"availabilitySet": {
			"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
		},
		"hardwareProfile": {
			"vmSize": "[variables('vmSize')]"
		},
		"osProfile": {
			"computername": "[concat(variables('computerNamePrefix'), copyIndex())]",
			"adminUsername": "[parameters('adminUsername')]",
			"adminPassword": "[parameters('adminPassword')]",
			"linuxConfiguration": {
				"disablePasswordAuthentication": "false"
			}
		},
		"storageProfile": {
			"imageReference": {
				"publisher": "[parameters('imagePublisher')]",
				"offer": "[parameters('imageOffer')]",
				"sku" : "[parameters('imageSKU')]",
				"version":"latest"
			},
			"osDisk" : {
				"name": "osdisk",
				"vhd": {
					"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisk', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nic', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[concat('slavevm', copyindex())]",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineLoop",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]",
		"virtualMachineLoopMaster"
		],
		"properties": {
			"availabilitySet": {
				"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
			},
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[concat(variables('computerNamePrefix'),'sl', copyIndex())]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]",
				"linuxConfiguration": {
					"disablePasswordAuthentication": "false"
				}
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[parameters('imagePublisher')]",
					"offer": "[parameters('imageOffer')]",
					"sku" : "[parameters('imageSKU')]",
					"version":"latest"
				},
				"osDisk" : {
					"name": "osdisksl",
					"vhd": {
						"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisksl', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nicsl', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopMaster",
			"count": "[variables('numberOfMasterInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopSlave",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
			}
		}
	}
```

Un autre concept important pour la création de ressources est la possibilité de spécifier les dépendances et les priorités entre les ressources, comme vous pouvez le constater dans le tableau JSON **dependsOn**. Dans ce modèle spécifique, vous pouvez voir que les nœuds du cluster Spark dépendent des ressources partagées et des ressources **networkInterfaces** créées en premier.

Le fragment associé aux extensions de machine virtuelle **CustomScriptForLinux** est également intéressant à explorer. Celles-ci sont installées en tant que type de ressources distinct, avec une dépendance sur chaque nœud du cluster. Dans ce cas, elles sont utilisées pour installer et configurer Spark sur chaque nœud de machine virtuelle. Examinons un extrait de code du modèle azuredeploy.json qui les utilise :

```json
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
		}
	}
},
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopSlave",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
		}
	}
}
```

Notez que l’extension pour les ressources du nœud principal et des nœuds subordonnés exécute différentes commandes définies dans la propriété **commandToExecute**, dans le cadre du processus de configuration.

Si vous examinez l'extrait JSON de l'extension de machine virtuelle la plus récente, vous pouvez voir que cette ressource dépend de la ressource et de l’interface réseau de la machine virtuelle. Cela indique que ces deux ressources doivent être déjà déployées avant l’approvisionnement et l'exécution de cette extension de machine virtuelle. Notez également l’utilisation de la fonction **copyindex()** pour répéter cette étape pour chaque machine virtuelle esclave.

En vous familiarisant avec les autres fichiers inclus dans ce déploiement, vous serez en mesure de comprendre tous les détails et les meilleures pratiques requises pour organiser et orchestrer des stratégies de déploiement complexes pour les solutions à nœuds multiples, basées sur n’importe quelle technologie, en exploitant les modèles Azure Resource Manager. Il existe une approche recommandée (mais nullement obligatoire) consistant à structurer vos fichiers de modèle comme l'indique le schéma suivant :

![structure-modèle-Spark](media/virtual-machines-spark-template/spark-template-structure.png)

Pour résumer, cette approche suggère de :

-	définir votre fichier de modèle principal comme le point d’orchestration central pour toutes les activités de déploiement spécifiques, en exploitant les liaisons de modèles pour appeler les exécutions de modèles secondaires ;
-	créer un fichier de modèle spécifique déployant toutes les ressources partagées entre toutes les autres tâches de déploiement (comptes de stockage, configuration du réseau virtuel, etc.). Cette méthode peut être régulièrement réutilisée entre les déploiements ayant des exigences similaires en termes d'infrastructure commune ;
-	inclure des modèles de ressource facultatifs pour répondre aux besoins spécifiques d’une ressource donnée ;
-	créer des modèles spécifiques pour les mêmes membres d’un groupe de ressources (nœuds dans un cluster, etc.) afin d’exploiter l’itération de ressources pour déployer plusieurs instances ayant des propriétés uniques ;
-	exploiter les extensions de déploiement de script et créer des scripts propres à chaque technologie pour toutes les tâches de post-déploiement (installation de produits, configurations, etc.).

Pour plus d’informations, consultez [Langage du modèle Azure Resource Manager](../resource-group-authoring-templates.md).

## Étapes suivantes

Découvrez plus d’informations sur le [déploiement d’un modèle](../resource-group-template-deploy.md).

Découvrez plus d’informations sur les [infrastructures d’application](virtual-machines-app-frameworks.md)

[Résolution des problèmes des déploiements de modèle](resource-group-deploy-debug.md).

<!---HONumber=Oct15_HO3-->