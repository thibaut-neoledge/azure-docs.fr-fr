<properties
	pageTitle="Modèle Resource Manager Cluster Redis"
	description="Apprenez à déployer facilement un nouveau cluster Redis sur des machines virtuelles Ubuntu en utilisant Azure PowerShell ou l'interface de ligne de commande Azure et un modèle Resource Manager"
	services="virtual-machines"
	documentationCenter=""
	authors="timwieman"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="twieman"/>

# Cluster Redis avec un modèle Resource Manager

Redis est une solution de cache et stockage clé-valeur open-source dont les clés peuvent contenir des structures de données, telles que des chaînes de caractères, des hachages, des listes, des groupes et des groupes triés. Redis prend en charge un ensemble d'opérations atomiques pour ces types de données. Avec la version de Redis 3.0, Redis Cluster est désormais disponible dans la version stable la plus récente de Redis. Redis Cluster est une implémentation distribuée de Redis dans laquelle les données sont automatiquement partitionnées sur plusieurs nœuds de Redis, avec la possibilité de continuer à fonctionner lorsqu’un sous-ensemble de nœuds rencontre des erreurs.

Le Cache Redis Microsoft Azure est un service de cache Redis dédié, géré par Microsoft. Cependant, tous les clients Microsoft Azure ne souhaitent pas utiliser le Cache Redis Azure. Certains souhaitent que leur Cache Redis se trouve derrière un sous-réseau dans leurs propres déploiements Azure, tandis que d’autres préfèrent héberger leurs propres serveurs Redis sur des machines virtuelles Linux afin de tirer pleinement parti de toutes les fonctionnalités de Redis.

Ce didacticiel présente un exemple de modèle Azure Resource Manager pour déployer un cluster Redis sur des machines virtuelles Ubuntu au sein d’un sous-réseau dans un groupe de ressources dans Microsoft Azure. En plus du Cluster Redis 3.0, ce modèle prend également en charge le déploiement de Redis 2.8 avec Redis Sentinel. Notez que ce didacticiel se concentre sur l’implémentation de Redis 3.0 Cluster.

Le cluster Redis est créé derrière un sous-réseau, il n’existe donc aucun accès IP public au cluster Redis. Une « jump box » peut être déployée dans le cadre du déploiement. Cette « jump box » est une machine virtuelle Ubuntu également déployée dans le sous-réseau, mais elle *expose* une adresse IP publique avec un port SSH sur lequel vous pouvez exécuter SSH. Vous pouvez ensuite exécuter SSH pour toutes les machines virtuelles Redis dans le sous-réseau depuis la « jump box ».

Ce modèle utilise un concept de « taille de t-shirt » afin de spécifier une configuration « Small », « Medium » ou « Large » de Redis Cluster. Lorsque la langue du modèle Azure Resource Manager prend en charge un dimensionnement de modèle plus dynamique, ce paramètre peut être modifié pour spécifier le nombre de nœuds de Redis Cluster principaux, les nœuds subordonnés, la taille des machines virtuelles, etc. Actuellement, vous pouvez voir la taille des machines virtuelles et le nombre de maîtres et de subordonnés définis dans le fichier azuredeploy.json dans les variables `tshirtSizeSmall`, `tshirtSizeMedium` et `tshirtSizeLarge`.

Le modèle de Cluster Redis de taille « Medium » crée la configuration suivante :

![cluster-architecture](media/virtual-machines-redis-template/cluster-architecture.png)

Avant de voir plus en détail Azure Resource Manager et le modèle utilisé pour ce déploiement, vérifiez que vous disposez d’une version configurée et opérationnelle d’Azure PowerShell et de l’interface de ligne de commande Azure.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Déployer un cluster Redis en utilisant un modèle Resource Manager

Procédez comme suit pour créer un cluster Redis en utilisant un modèle Resource Manager provenant du référentiel de modèles GitHub. Chaque étape inclut des instructions pour Azure PowerShell et l'interface de ligne de commande Azure.

### Étape 1-a : télécharger les fichiers de modèle en utilisant Azure PowerShell

Créez un dossier local pour le modèle JSON et les fichiers associés (par exemple, C:\\Azure\\Templates\\RedisCluster).

Incluez le nom de votre dossier local et exécutez les commandes suivantes :

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy-parameters.json"
$filePath = $folderName + "\azuredeploy-parameters.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/empty-resources.json"
$filePath = $folderName + "\empty-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/jumpbox-resources.json"
$filePath = $folderName + "\jumpbox-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/node-resources.json"
$filePath = $folderName + "\node-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-install.sh"
$filePath = $folderName + "\redis-cluster-install.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-setup.sh"
$filePath = $folderName + "\redis-cluster-setup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-sentinel-startup.sh"
$filePath = $folderName + "\redis-sentinel-startup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/shared-resources.json"
$filePath = $folderName + "\shared-resources.json"
$webclient.DownloadFile($url,$filePath)
```

### Étape 1-b : télécharger les fichiers de modèle en utilisant l’interface de ligne de commande Azure

Clonez le référentiel de modèles entier en utilisant un client Git de votre choix, par exemple :

```
git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates
```

Lorsque vous avez terminé le clonage, recherchez le dossier **redis-high-availability** dans votre répertoire C:\\Azure\\Templates.

### Étape 2 : (facultatif) comprendre les paramètres du modèle

Lorsque vous créez un cluster Redis en utilisant un modèle, vous devez spécifier un ensemble de paramètres de configuration. Pour afficher les paramètres que vous devez spécifier pour le modèle dans un fichier JSON local avant d’exécuter la commande pour créer le cluster Redis, ouvrez le fichier JSON dans un outil ou un éditeur de texte de votre choix.

Recherchez la section « parameters » située en haut du fichier, qui répertorie l’ensemble des paramètres requis par le modèle pour configurer le cluster Redis. Voici la section « parameters » du modèle azuredeploy.json :

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
	"storageAccountName": {
		"type": "string",
		"defaultValue": "",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"location": {
		"type": "string",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "redisVirtNet",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the Redis cluster"
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
		"defaultValue": "redisSubnet1",
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
	"nodeAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "Disabled",
		"allowedValues": [
			"Enabled",
			"Disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Redis nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "Small",
		"allowedValues": [
			"Small",
			"Medium",
			"Large"
		],
		"metadata": {
			"Description": "T-shirt size of the Redis deployment"
		}
	},
	"redisVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Redis package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"redisClusterName": {
		"type": "string",
		"defaultValue": "redis-cluster",
		"metadata": {
			"Description": "The arbitrary name of the Redis cluster"
		}
	}
},
```

Chaque paramètre contient des détails, tels que le type de données et les valeurs autorisées. Ceci permet la validation des paramètres passés pendant l’exécution du modèle en mode interactif (par exemple, Azure PowerShell ou l’interface de ligne de commande Azure), ainsi qu’une interface utilisateur à détection automatique, qui peut être construite de manière dynamique par l’analyse de la liste des paramètres requis et leurs descriptions.

### Étape 3-a : déployer un cluster Redis en utilisant un modèle par le biais d’Azure PowerShell

Préparez un fichier de paramètres pour votre déploiement en créant un fichier JSON contenant des valeurs d’exécution pour tous les paramètres. Ce fichier est ensuite transmis en tant qu’entité unique à la commande de déploiement. Si vous n’incluez pas de fichier de paramètres, Azure PowerShell utilisera les valeurs par défaut spécifiées dans le modèle et vous invitera à renseigner les valeurs restantes.

Voici un exemple tiré du fichier azuredeploy-parameters.json : Notez que vous devez fournir des valeurs valides pour les paramètres `storageAccountName`, `adminUsername` et `adminPassword`, ainsi que les personnalisations apportées aux autres paramètres :

```json
{
	"storageAccountName": {
		"value": "redisdeploy1"
	},
	"adminUsername": {
		"value": ""
	},
	"adminPassword": {
		"value": ""
	},
	"location": {
		"value": "West US"
	},
	"virtualNetworkName": {
		"value": "redisClustVnet"
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
	"nodeAddressPrefix": {
		"value": "10.0.0.1"
	},
	"redisVersion": {
		"value": "3.0.0"
	},
	"redisClusterName": {
		"value": "redis-arm-cluster"
	},
	"jumpbox": {
		"value": "Enabled"
	},
	"tshirtSize":  {
		"value": "Small"
	}
}
```

>[AZURE.NOTE]Le paramètre `storageAccountName` doit être un nom de compte de stockage unique inexistant qui satisfait aux exigences d’affectation de noms pour un compte Microsoft Azure Storage (minuscules et chiffres uniquement). Ce compte de stockage sera créé dans le cadre du processus de déploiement.

Entrez un nom de déploiement Azure, un nom de groupe de ressources, un emplacement Azure et le dossier de vos fichiers JSON enregistrés. Exécutez ensuite les commandes suivantes :

```powershell
$deployName="<deployment name>, such as TestDeployment"
$RGName="<resource group name>, such as TestRG"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

New-AzureResourceGroup –Name $RGName –Location $locName

New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
```

>[AZURE.NOTE]`$RGName` doit être unique au sein de votre abonnement.

Lorsque vous exécutez la commande **New-AzureResourceGroupDeployment**, elle extrait des valeurs de paramètre à partir du fichier de paramètres JSON (azuredeploy-parameters.json) et démarre l'exécution du modèle en conséquence. Le fait de définir et d’utiliser plusieurs fichiers de paramètres sur vos différents environnements (par exemple, test, production, etc.) favorise la réutilisation du modèle et simplifie les solutions contenant plusieurs environnements complexes.

Lors du déploiement, n’oubliez pas qu’un compte Azure Storage doit être créé. Le nom fourni en tant que paramètre de compte de stockage doit donc être unique et respecter la configuration requise pour un compte Azure Storage (minuscules et chiffres uniquement).

Lors du déploiement, le résultat suivant doit s’afficher :

	PS C:> New-AzureResourceGroup –Name $RGName –Location $locName

	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

	ResourceId        : /subscriptions/1234abc1-abc1-1234-12a1-ab1ab12345ab/resourceGroups/TestRG

	PS C:> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
	VERBOSE: 2:39:10 PM - Template is valid.
	VERBOSE: 2:39:14 PM - Create template deployment 'TestDeployment'.
	VERBOSE: 2:39:25 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
	VERBOSE: 2:40:04 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
	VERBOSE: 2:42:23 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
	VERBOSE: 2:47:44 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is succeeded
	VERBOSE: 2:47:57 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is succeeded
	VERBOSE: 2:48:01 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is running
	VERBOSE: 2:58:24 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is succeeded

	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 7:58:23 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myadmin
                    adminPassword    SecureString
                    storageAccountName  String                     myuniqstgacct87
                    location         String                     West US
                    virtualNetworkName  String                     redisClustVnet
                    addressPrefix    String                     10.0.0.0/16
                    subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    nodeAddressPrefix  String                     10.0.0.1
                    jumpbox          String                     Enabled
                    tshirtSize       String                     Small
                    redisVersion     String                     3.0.0
                    redisClusterName  String                     redis-arm-cluster

	Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    installCommand   String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1
                    setupCommand     String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1 -l
	...

Pendant et après le déploiement, vous pouvez vérifier toutes les requêtes effectuées durant la configuration et notamment toutes les erreurs survenues.

Pour ce faire, accédez au [Portail Azure](https://portal.azure.com), puis procédez comme suit :

- Dans la barre de navigation située sur la gauche, cliquez sur **Parcourir**, puis faites défiler les options vers le bas et cliquez sur **Groupes de ressources**.
- Sélectionnez le groupe de ressources que vous venez de créer pour afficher le panneau « Groupe de ressources ».
- Dans la section **Analyse**, sélectionnez l’histogramme « Événements ». Ceci affichera les événements pour votre déploiement.
- En cliquant sur les différents événements, vous pouvez accéder à des informations plus détaillées sur chaque opération menée pour le compte du modèle.

Si vous devez supprimer ce groupe de ressources et toutes ses ressources (le compte de stockage, la machine virtuelle et le réseau virtuel) après le test, utilisez la commande suivante :

```powershell
Remove-AzureResourceGroup –Name "<resource group name>"
```

### Étape 3-b : déployer un cluster Redis en utilisant un modèle par le biais de l’interface de ligne de commande Azure

Pour déployer un cluster Redis par le biais de l’interface de ligne de commande Azure, commencez par créer un groupe de ressources en spécifiant un nom et un emplacement :

```powershell
azure group create TestRG "West US"
```

Intégrez ce nom de groupe de ressources, l’emplacement du fichier de modèle JSON et l’emplacement du fichier de paramètres (consultez la section Azure PowerShell ci-dessus pour plus de détails) dans la commande suivante :

```powershell
azure group deployment create TestRG -f .\azuredeploy.json -e .\azuredeploy-parameters.json
```

Vous pouvez vérifier l’état des différents déploiements de ressources en utilisant la commande suivante :

```powershell
azure group deployment list TestRG
```

## Présentation de la structure de modèle de cluster Redis et de l'organisation des fichiers

Pour créer une approche robuste et réutilisable de la conception des modèles du Resource Manager, vous devez également organiser une série de tâches complexes et interdépendantes durant le déploiement d'une solution complexe telle que le cluster Redis. En exploitant les capacités de liaison de modèles, de l’itération de ressources Resource Manager et de l’exécution de scripts par l’intermédiaire d’extensions associées, vous pouvez implémenter une approche modulaire qui peut être réutilisée lors de n’importe quel déploiement complexe basé sur un modèle.

Ce schéma décrit les relations entre tous les fichiers téléchargés à partir de GitHub pour le déploiement suivant :

![redis-files](media/virtual-machines-redis-template/redis-files.png)

Cette section vous guide dans la structure du modèle azuredeploy.json pour le cluster Redis.

Si vous n'avez pas téléchargé une copie du fichier de modèle, spécifiez un dossier local comme emplacement de fichier et créez-le (par exemple, C:\\Azure\\Templates\\RedisCluster). Indiquez le nom du dossier, puis exécutez ces commandes :

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Ouvrez le modèle azuredeploy.json dans un éditeur de texte ou dans un outil de votre choix. Les informations suivantes décrivent la structure du fichier de modèle et l’objectif de chaque section. Vous pouvez également voir le contenu de ce modèle dans votre navigateur [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json).

### Section "parameters"

Nous avons déjà mentionné le rôle de la commande azuredeploy-parameters.json, qui permet de transmettre un ensemble donné de valeurs de paramètre lors de l’exécution du modèle. La section "parameters" du fichier azuredeploy.json indique les paramètres utilisés pour saisir des données dans ce modèle.

Voici un exemple de paramètre pour le concept de « Taille de t-shirt » :

```json
"tshirtSize": {
	"type": "string",
	"defaultValue": "Small",
	"allowedValues": [
		"Small",
		"Medium",
		"Large"
	],
	"metadata": {
		"Description": "T-shirt size of the Redis deployment"
	}
},
```

>[AZURE.NOTE]Notez qu’une `defaultValue` peut être spécifiée, ainsi que des `allowedValues`.

### section "variables"

La section "variables" spécifie les variables qui peuvent être utilisées dans ce modèle. Cette section contient essentiellement un certain nombre de champs (types de données ou fragments JSON) définis sur des valeurs constantes ou calculées lors de l'exécution. Voici quelques exemples, du plus simple au plus complexe :

```json
"vmStorageAccountContainerName": "vhd-redis",
"vmStorageAccountDomain": ".blob.core.windows.net",
"vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
...
"machineSettings": {
	"adminUsername": "[parameters('adminUsername')]",
	"machineNamePrefix": "redisnode-",
	"osImageReference": {
		"publisher": "[variables('osFamilyUbuntu').imagePublisher]",
		"offer": "[variables('osFamilyUbuntu').imageOffer]",
		"sku": "[variables('osFamilyUbuntu').imageSKU]",
		"version": "latest"
	}
},
...
"vmScripts": {
	"scriptsToDownload": [
		"[concat(variables('scriptUrl'), 'redis-cluster-install.sh')]",
		"[concat(variables('scriptUrl'), 'redis-cluster-setup.sh')]",
		"[concat(variables('scriptUrl'), 'redis-sentinel-startup.sh')]"
	],
	"installCommand": "[concat('bash ', variables('installCommand'))]",
	"setupCommand": "[concat('bash ', variables('installCommand'), ' -l')]"
}
```

Les variables `vmStorageAccountContainerName` et `vmStorageAccountDomain` sont des exemples de variables de nom/valeur simples. `vnetID` est un exemple de variable calculée lors de l’exécution à l’aide des fonctions `resourceId` et `parameters`. `machineSettings` s’appuie même encore plus sur ces concepts en imbriquant l’objet JSON `osImageReference` dans la variable `machineSettings`. `vmScripts` contient un tableau JSON, `scriptsToDownload`, qui est calculé au moment de l’exécution à l’aide des fonctions `concat` et `variables`.

Si vous souhaitez personnaliser la taille du déploiement de Redis Cluster, vous pouvez modifier les propriétés des variables `tshirtSizeSmall`, `tshirtSizeMedium` et `tshirtSizeLarge` dans le modèle azuredeploy.json.

```json
"tshirtSizeSmall": {
	"vmSizeMember": "Standard_A1",
	"numberOfMasters": 3,
	"numberOfSlaves": 0,
	"totalMemberCount": 3,
	"totalMemberCountExcludingLast": 2,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeMedium": {
	"vmSizeMember": "Standard_A2",
	"numberOfMasters": 3,
	"numberOfSlaves": 3,
	"totalMemberCount": 6,
	"totalMemberCountExcludingLast": 5,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeLarge": {
	"vmSizeMember": "Standard_A5",
	"numberOfMasters": 3,
	"numberOfSlaves": 6,
	"totalMemberCount": 9,
	"totalMemberCountExcludingLast": 8,
	"arbiter": "Enabled",
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
```

Remarque : les propriétés `totalMemberCountExcludingLast` et `totalMemberCount` sont nécessaires car la langue du modèle ne dispose pas actuellement d’opérations « math ».

Vous trouverez plus d'informations sur la langue du modèle dans MSDN sous [Langue du modèle Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn835138.aspx).

### section "resources"

La plupart des actions se déroulent dans la section "resources". En effectuant une recherche minutieuse dans cette section, vous pouvez immédiatement identifier deux cas distincts : le premier est un élément défini de type `Microsoft.Resources/deployments` qui appelle principalement un déploiement imbriqué au sein du déploiement principal. Le second est la propriété `templateLink` (et la propriété `contentVersion` associée) qui permet de spécifier un fichier de modèle lié qui sera appelé en transmettant un ensemble de paramètres comme entrée. Ils sont visibles dans le fragment de modèle suivant :

```json
{
    "name": "shared-resources",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('sharedTemplateUrl')]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            }
        }
    }
},
```

Ce premier exemple présente clairement la façon dont azuredeploy.json est organisé en mécanisme d’orchestration dans ce scénario, pour appeler un certain nombre d’autres fichiers de modèle, chacun d’eux étant responsable d’une partie des activités de déploiement requises.

Vous pouvez notamment remarquer les modèles liés suivants qui sont utilisés pour ce déploiement :

- **shared-resource.json** : contient la définition de toutes les ressources partagées lors du déploiement. Par exemple, c’est le cas des comptes de stockage utilisés pour stocker des disques de système d’exploitation de machines virtuelles, des réseaux virtuels et des groupes à haute disponibilité.
- **jumpbox-resources.json** : déploie la machine virtuelle « jump box » et toutes les ressources liées, telles que l'interface réseau, l'adresse IP publique et le point de terminaison d'entrée utilisé pour exécuter SSH dans l'environnement.
- **node-resources.json** : déploie toutes les machines virtuelles de nœuds de Redis Cluster et les ressources connectées (cartes réseau, adresses IP privées, etc.). Ce modèle déploie également des extensions de machine virtuelle (scripts personnalisés pour Linux) et appelle un script d’interpréteur de commandes pour installer et configurer physiquement Redis sur chaque nœud. Le script à appeler est transmis à ce modèle dans le paramètre `machineSettings` de la propriété `commandToExecute`. Tous les nœuds du cluster Redis sauf un peuvent être déployés et inclus dans un script en parallèle. Un nœud doit être enregistré jusqu’à la fin, car la configuration de Redis Cluster ne peut être exécutée que sur un nœud, et elle ne doit être effectuée qu’une fois que tous les nœuds exécutent le serveur Redis. C’est pourquoi le script à exécuter est transmis à ce modèle. Le dernier nœud doit exécuter un script légèrement différent qui installera le serveur Redis, mais configurera également le cluster Redis.

Penchons-nous sur *l’utilisation* du dernier modèle, node-resources.json, car il s’agit de l’un des plus intéressants du point de vue du développement d’un modèle. Un concept important à retenir est la façon dont un fichier de modèle unique peut déployer plusieurs copies d’un type de ressource unique et, pour chaque instance, définir des valeurs uniques pour les paramètres requis. Ce concept est appelé **itération de ressources**.

Lorsque node-resources.json est appelé depuis le fichier azuredeploy.json principal, il est appelé depuis une ressource qui utilise l’élément `copy` pour créer une boucle de tri. Une ressource qui utilise l'élément `copy` sera copiée le nombre de fois spécifié dans le paramètres `count` de l'élément `copy`. Pour tous les paramètres où il est nécessaire de spécifier des valeurs uniques pour différentes instances de la ressource déployée, vous pouvez utiliser la fonction **copyindex()** pour obtenir une valeur numérique indiquant l’index actuel dans cette création d’itérations de ressource spécifique. Dans le fragment suivant d’azuredeploy.json, ce concept est appliqué à plusieurs machines virtuelles créées pour les nœuds de Redis Cluster :

```json
{
    "type": "Microsoft.Resources/deployments",
    "name": "[concat('node-resources', copyindex())]",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
    ],
    "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').totalMemberCountExcludingLast]"
    },
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('clusterSpec').vmTemplate]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            },
            "machineSettings": {
                "value": {
                    "adminUsername": "[variables('machineSettings').adminUsername]",
                    "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                    "osImageReference": "[variables('machineSettings').osImageReference]",
                    "vmSize": "[variables('clusterSpec').vmSizeMember]",
                    "machineIndex": "[copyindex()]",
                    "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                    "commandToExecute": "[concat(variables('vmScripts').installCommand, ' -i ', copyindex())]"
                }
            },
            "adminPassword": {
                "value": "[parameters('adminPassword')]"
            }
        }
    }
},
```

Un autre concept important pour la création de ressources est la possibilité de spécifier des dépendances et des priorités entre les ressources, comme vous pouvez le constater dans le tableau JSON `dependsOn`. Dans ce modèle spécifique, vous pouvez voir que les nœuds du cluster Redis dépendent des ressources partagées créées en premier.

Comme mentionné précédemment, le dernier nœud doit attendre son approvisionnement jusqu’à ce que tous les autres nœuds du cluster Redis aient été configurés avec le serveur Redis s’exécutant sur ceux-ci. Pour cela, il suffit d’avoir dans azuredeploy.json une ressource nommée `lastnode-resources` qui dépend de la boucle `copy` nommée `memberNodesLoop` de l’extrait de code de modèle ci-dessus. Une fois l’approvisionnement de `memberNodesLoop` terminé, il est possible de configurer `lastnode-resources` :

```json
{
	"name": "lastnode-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"dependsOn": [
		"memberNodesLoop"
	],
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[variables('clusterSpec').vmTemplate]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"commonSettings": {
				"value": "[variables('commonSettings')]"
			},
			"storageSettings": {
				"value": "[variables('storageSettings')]"
			},
			"networkSettings": {
				"value": "[variables('networkSettings')]"
			},
			"machineSettings": {
				"value": {
					"adminUsername": "[variables('machineSettings').adminUsername]",
					"machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
					"osImageReference": "[variables('machineSettings').osImageReference]",
					"vmSize": "[variables('clusterSpec').vmSizeMember]",
					"machineIndex": "[variables('clusterSpec').totalMemberCountExcludingLast]",
					"vmScripts": "[variables('vmScripts').scriptsToDownload]",
					"commandToExecute": "[concat(variables('vmScripts').setupCommand, ' -i ', variables('clusterSpec').totalMemberCountExcludingLast)]"
				}
			},
			"adminPassword": {
				"value": "[parameters('adminPassword')]"
			}
		}
	}
}
```

Notez que la ressource `lastnode-resources` transmet un paramètre `machineSettings.commandToExecute` légèrement différent au modèle lié. C’est la raison pour laquelle il est nécessaire d’appeler un script pour le dernier nœud en plus du serveur Redis installé afin de configurer le cluster Redis. Cet appel ne doit être effectué qu’une seule fois lorsque tous les serveurs Redis sont opérationnels.

Le fragment associé aux extensions de machines virtuelles `CustomScriptForLinux` est également intéressant à étudier. Celles-ci sont installées en tant que type de ressources distinct, avec une dépendance sur chaque nœud du cluster. Dans ce cas, elles sont utilisées pour installer et configurer Redis sur chaque nœud de machine virtuelle. Examinons un extrait de code du modèle node-resources.json qui les utilise :

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installscript')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('commonSettings').location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
    ],
    "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
            "fileUris": "[parameters('machineSettings').vmScripts]",
            "commandToExecute": "[parameters('machineSettings').commandToExecute]"
        }
    }
}
```

Vous pouvez voir que cette ressource dépend de la ressource de machine virtuelle déjà déployée (`Microsoft.Compute/virtualMachines/vmMember<X>` où `<X>` est le paramètre `machineSettings.machineIndex`, qui est l’index de la machine virtuelle qui a été passé à ce script à l’aide de la fonction **copyindex()**).

En vous familiarisant avec les autres fichiers inclus dans ce déploiement, vous serez en mesure de comprendre tous les détails et les meilleures pratiques requises pour organiser et orchestrer des stratégies de déploiement complexes pour les solutions à nœuds multiples, basées sur n’importe quelle technologie, en exploitant les modèles Azure Resource Manager. Il existe une approche recommandée (mais nullement obligatoire) consistant à structurer vos fichiers de modèle comme l'indique le schéma suivant :

![redis-template-structure](media/virtual-machines-redis-template/redis-template-structure.png)

Pour résumer, cette approche suggère de :

- définir votre fichier de modèle principal comme le point d’orchestration central pour toutes les activités de déploiement spécifiques, en exploitant les liaisons de modèles pour appeler les exécutions de modèles secondaires ;
- créer un fichier de modèle spécifique déployant toutes les ressources partagées entre toutes les autres tâches de déploiement (comptes de stockage, configuration du réseau virtuel, etc.). Cette méthode peut être régulièrement réutilisée entre les déploiements ayant des exigences similaires en termes d'infrastructure commune ;
- inclure des modèles de ressource facultatifs pour répondre aux besoins spécifiques d’une ressource donnée ;
- créer des modèles spécifiques pour les mêmes membres d’un groupe de ressources (nœuds dans un cluster, etc.) afin d’exploiter l’itération de ressources pour déployer plusieurs instances ayant des propriétés uniques ;
- exploiter les extensions de déploiement de script et créer des scripts propres à chaque technologie pour toutes les tâches de post-déploiement (installation de produits, configurations, etc.).

Pour plus d’informations, consultez [Langage du modèle Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn835138.aspx).

<!---HONumber=July15_HO2-->