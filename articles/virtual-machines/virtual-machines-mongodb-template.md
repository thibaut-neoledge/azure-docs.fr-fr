<properties
  pageTitle="Créer un cluster de MongoDB sur Ubuntu | Microsoft Azure"
  description="Création d'un cluster MongoDB sous Ubuntu à l'aide d'un modèle Azure Resource Manager à l’aide de PowerShell ou de l'interface de ligne de commande Azure"
  services="virtual-machines"
  documentationCenter=""
  authors="scoriani"
  manager="timlt"
  editor="tysonn"
  tags="azure-resource-manager"/>

<tags
  ms.service="virtual-machines"
  ms.workload="multiple"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="04/29/2015"
  ms.author="scoriani"/>

# Création d'un cluster MongoDB sous Ubuntu à l'aide d'un modèle Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


MongoDB est une base de données de documents open source qui fournit de hautes performances, une disponibilité élevée et la mise à l'échelle automatique. MongoDB peut être installé en base de données autonome ou dans un cluster, tirant ainsi parti des fonctionnalités de réplication intégrées. Dans certains cas, vous pouvez utiliser la réplication pour augmenter la capacité de lecture. Les clients ont la possibilité d'envoyer des opérations de lecture et d'écriture sur différents serveurs. Vous pouvez également conserver des copies dans différents centres de données pour augmenter la localité et la disponibilité des données pour les applications distribuées. Avec MongoDB, la réplication assure également la redondance et optimise la disponibilité des données. Avec plusieurs copies des données sur des serveurs de bases de données différents, la réplication protège une base de données contre la perte d'un seul serveur. La réplication vous permet également de récupérer suite à une défaillance matérielle et des interruptions de service. Avec des copies supplémentaires des données, vous pouvez en dédier une à la récupération d'urgence, la création de rapports ou la sauvegarde.

En plus des différentes versions qui étaient déjà disponibles dans Azure Marketplace, vous pouvez maintenant déployer facilement un nouveau cluster MongoDB sur des machines virtuelles Ubuntu à l’aide d’un modèle Azure Resource Manager déployé avec [Azure PowerShell](../powershell-install-configure.md) ou [l’interface de ligne de commande Azure](../xplat-cli-install.md).

Les clusters récemment déployés basés sur ce modèle disposent de la topologie décrite dans le schéma suivant, bien que d’autres topologies puissent être obtenues facilement grâce à la personnalisation du modèle présenté dans cet article.

![cluster-architecture](media/virtual-machines-mongodb-template/cluster-architecture.png)

Grâce à un paramètre, vous pouvez définir le nombre de nœuds qui seront déployés dans le nouveau cluster MongoDB et, en fonction d'un autre paramètre, une instance de machine virtuelle (Jumpbox) avec une adresse IP publique peut également être déployée dans le même réseau virtuel. Cela vous donne la possibilité de vous connecter au cluster à partir du réseau Internet public et d'effectuer toute tâche d'administration liée à ce cluster. Une autre option disponible en tant que paramètre est la possibilité d'ajouter un nœud Arbiter au jeu de réplicas, qui est généralement suggéré en cas de nombre pair de membres. Pour plus d’informations sur les topologies de réplication MongoDB et plus de détails, reportez-vous à la [documentation MongoDB](http://docs.mongodb.org/manual/core/replication-introduction/) officielle.

Une fois le déploiement terminé, vous pouvez accéder à la Jumpbox en utilisant l'adresse DNS configurée sur le port SSH 22.

Avant de voir plus en détail Azure Resource Manager et le modèle utilisé pour ce déploiement, veuillez vérifier que vous disposez d'une version configurée et opérationnelle d'Azure PowerShell et de l’interface de ligne de commande Azure.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/powershell-preview-inline-include.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Création d'un cluster MongoDB à l'aide d'un modèle Resource Manager

Procédez comme suit pour créer un cluster MongoDB en utilisant un modèle Resource Manager provenant du référentiel de modèles Github. Chaque étape inclut les instructions pour Azure PowerShell et l'interface de ligne de commande Azure.

### Étape 1-a : télécharger les fichiers de modèle à l'aide de PowerShell

Créez un dossier local pour le modèle JSON et autres fichiers associés (par exemple, C:\\Azure\\Templates\\MongoDB).

Dans l’exemple suivant, remplacez le nom de votre dossier local et exécutez les commandes.

    $folderName="C:\Azure\Templates\MongoDB"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy-parameters.json"
    $filePath = $folderName + "\azuredeploy-parameters.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/arbiter-resources.json"
    $filePath = $folderName + "\arbiter-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/empty-resources.json"
    $filePath = $folderName + "\empty-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/jumpbox-resources.json"
    $filePath = $folderName + "\jumpbox-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D1.json"
    $filePath = $folderName + "\member-resources-D1.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D2.json"
    $filePath = $folderName + "\member-resources-D2.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D3.json"
    $filePath = $folderName + "\member-resources-D3.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D4.json"
    $filePath = $folderName + "\member-resources-D4.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D11.json"
    $filePath = $folderName + "\member-resources-D11.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D12.json"
    $filePath = $folderName + "\member-resources-D12.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D13.json"
    $filePath = $folderName + "\member-resources-D13.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D14.json"
    $filePath = $folderName + "\member-resources-D14.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/mongodb-ubuntu-install.sh"
    $filePath = $folderName + "\mongodb-ubuntu-install.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/metadata.json"
    $filePath = $folderName + "\metadata.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/shared-resources.json"
    $filePath = $folderName + "\shared-resources.json"
    $webclient.DownloadFile($url,$filePath)  

### Étape 1-b : télécharger les fichiers de modèle à l’aide de l’interface de ligne de commande Azure

Les exemples suivants montrent comment cloner tout le référentiel de modèles à l'aide d'un client Git de votre choix.

    git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Lorsque vous avez terminé, recherchez le dossier mongodb-high-availability dans votre répertoire C:\\Azure\\Templates.

### Étape 2 : (facultatif) comprendre les paramètres du modèle

Lorsque vous déployez des solutions importantes comme un cluster MongoDB, vous devez spécifier un ensemble de paramètres de configuration pour gérer un certain nombre de paramètres requis. En déclarant ces paramètres dans la définition du modèle, vous pouvez spécifier des valeurs pendant l'exécution du déploiement au moyen d’un fichier externe ou dans la ligne de commande.

Dans la section des paramètres en haut du fichier azuredeploy.json, vous trouverez l'ensemble de paramètres requis par le modèle pour configurer un cluster MongoDB. L’exemple suivant montre la section des paramètres du fichier azuredeploy.json de ce modèle.

    "parameters": {
      "adminUsername": {
          "type": "string",
          "metadata": {
            "Description": "Administrator user name used when provisioning virtual machines (which also becomes a system user administrator in MongoDB)"
          }
        },
        "adminPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Administrator password used when provisioning virtual machines (which is also a password for the system administrator in MongoDB)"
          }
        },
        "storageAccountName": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
            "Description": "Unique namespace for the storage account where the Virtual Machine's disks will be placed (this name will be used as a prefix to create one or more storage accounts as per t-shirt size)"
          }
        },
        "region": {
          "type": "string",
          "metadata": {
            "Description": "Location where resources will be provisioned"
          }
        },
        "virtualNetworkName": {
          "type": "string",
          "defaultValue": "mongodbVnet",
          "metadata": {
            "Description": "The arbitrary name of the virtual network provisioned for the MongoDB deployment"
          }
        },
        "subnetName": {
          "type": "string",
          "defaultValue": "mongodbSubnet",
          "metadata": {
            "Description": "Subnet name for the virtual network that resources will be provisioned in to"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "Description": "The network address space for the virtual network"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/24",
          "metadata": {
            "Description": "The network address space for the virtual subnet"
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
            "Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the MongoDB environment"
          }
        },
        "tshirtSize": {
          "type": "string",
          "defaultValue": "XSmall",
          "allowedValues": [
          "XSmall",
          "Small",
          "Medium",
          "Large",
          "XLarge",
          "XXLarge"
          ],
          "metadata": {
            "Description": "T-shirt size of the MongoDB deployment"
          }
        },
        "osFamily": {
          "type": "string",
          "defaultValue": "Ubuntu",
          "allowedValues": [
          "Ubuntu"
          ],
          "metadata": {
            "Description": "The target OS for the virtual machines running MongoDB"
          }
        },
        "mongodbVersion": {
          "type": "string",
          "defaultValue": "3.0.2",
          "metadata": {
            "Description": "The version of the MongoDB packages to be deployed"
          }
        },
        "replicaSetName": {
          "type": "string",
          "defaultValue": "rs0",
          "metadata": {
            "Description": "The name of the MongoDB replica set"
          }
        },
        "replicaSetKey": {
          "type": "string",
          "metadata": {
            "Description": "The shared secret key for the MongoDB replica set"
          }
        }
      },

Chaque paramètre contient des détails, tels que le type de données et les valeurs autorisées. Cela permet la validation des paramètres passés pendant l’exécution du modèle en mode interactif (par exemple, PowerShell ou l’interface de ligne de commande Azure), ainsi qu’une interface utilisateur à détection automatique, qui peut être construite de manière dynamique par l’analyse de la liste des paramètres requis et leurs descriptions.

### Étape 3-a : déployer un cluster MongoDB avec un modèle à l'aide de PowerShell

Préparez un fichier de paramètres pour votre déploiement en créant un fichier JSON contenant des valeurs d'exécution pour tous les paramètres. Ce fichier est ensuite transmis en tant qu’entité unique à la commande de déploiement. Si vous n'incluez pas de fichier de paramètres, PowerShell utilisera les valeurs par défaut spécifiées dans le modèle et vous invitera à renseigner les valeurs restantes.

L’exemple suivant montre l’ensemble des paramètres du fichier azuredeploy-parameters.json.

    {
      "adminUsername": {
          "value": "MongoAdmin"
      },
      "adminPassword": {
          "value": ""
      },
      "storageAccountName": {
          "value": ""
      },
      "region": {
          "value": "West US"
      },
      "virtualNetworkName": {
          "value": "mongodbVnet"
      },
      "subnetName": {
          "value": "mongodbSubnet"
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
      "jumpbox": {
          "value": "Disabled"
      },
      "tshirtSize": {
          "value": "XSmall"
      },
      "osFamily": {
          "value": "Ubuntu"
      },
      "mongodbVersion": {
          "value": "3.0.2"
      },
      "replicaSetName": {
          "value": "rs0"
      },
      "replicaSetKey":  {
          "value": ""
      }
    }

Entrez un nom de déploiement Azure, un nom de groupe de ressources, un emplacement Azure et le dossier du fichier de déploiement JSON enregistré. Exécutez ensuite les commande suivantes.

    $deployName="<deployment name>"
    $RGName="<resource group name>"
    $locName="<Azure location, such as West US>"
    $folderName="<folder name, such as C:\Azure\Templates\MongoDB>"
    $templateFile= $folderName + "\azuredeploy.json"
    $templateParameterFile= $folderName + "\azuredeploy-parameters.json"

    New-AzureResourceGroup –Name $RGName –Location $locName

    New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Lorsque vous exécutez la commande **New-AzureResourceGroupDeployment**, elle extrait les valeurs de paramètre à partir du fichier de paramètres JSON et démarre l’exécution du modèle correspondant. La définition et l’utilisation de différents fichiers de paramètres sur vos différents environnements (par exemple, Test, Production, etc.) favorise la réutilisation du modèle et simplifie les solutions contenant plusieurs environnements complexes.

Lors du déploiement, n’oubliez pas qu’un nouveau compte de stockage Azure doit être créé. Le nom fourni en tant que paramètre du compte de stockage doit donc être unique et respecter la configuration requise pour un compte de stockage Azure (minuscules et chiffres uniquement).

Pendant et après le déploiement, vous pouvez vérifier toutes les requêtes effectuées durant la configuration et notamment toutes les erreurs survenues.

Pour ce faire, accédez au [Portail Azure](https://portal.azure.com), puis procédez comme suit :

- Dans la barre de navigation de gauche, cliquez sur **Parcourir**, faites défiler vers le bas, puis cliquez sur **Groupes de ressources**.
- Une fois que vous avez cliqué sur le groupe de ressources que vous venez de créer, le panneau du groupe de ressources s’affiche.
- En cliquant sur l’histogramme « Événements » dans la partie « Surveillance » du panneau du groupe de ressources, vous pouvez voir les événements de votre déploiement. En cliquant sur chaque événement, vous pouvez accéder à des informations plus détaillées sur chaque opération menée pour le compte du modèle.

Après vos tests, si vous devez supprimer ce groupe de ressources et toutes ses ressources (le compte de stockage, la machine virtuelle et le réseau virtuel), utilisez la commande suivante.

    Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Étape 3-b : déployer un cluster MongoDB avec un modèle à l'aide de l'interface de ligne de commande Azure

Pour déployer un cluster MongoDB via l'interface de ligne de commande Azure, commencez par créer un groupe de ressources en spécifiant un nom et un emplacement à l’aide de la commande suivante.

    azure group create mdbc "West US"

Transmettez ce nom de groupe de ressources, l'emplacement du fichier de modèle JSON et l'emplacement du fichier de paramètres (voir la section PowerShell ci-dessus pour plus de détails) dans la commande suivante.

    azure group deployment create mdbc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Vous pouvez vérifier l’état des différents déploiements de ressources à l’aide de la commande suivante.

    azure group deployment list mdbc

## Visite guidée de l'organisation des fichiers et la structure de modèle MongoDB

Pour créer un modèle Azure Resource Manager robuste et réutilisable, vous devez également prendre en compte l'organisation d'une série de tâches complexes et interdépendantes requises durant le déploiement d'une solution complexe telle que MongoDB. En tirant parti des capacités de *liaison des modèles* et de l’*itération des ressources* d’Azure Resource Manager en plus de l’exécution de scripts via les extensions associées, vous pouvez implémenter une approche modulaire qui peut être réutilisée lors de n’importe quel déploiement basé sur un modèle complexe.

Ce schéma décrit les relations entre tous les fichiers téléchargés à partir de GitHub pour ce déploiement.

![mongodb-fichiers](media/virtual-machines-mongodb-template/mongodb-files.png)

Cette section vous guide dans la structure du fichier azuredeploy.json pour le cluster MongoDB.

### Section Paramètres

La section Paramètres du fichier azuredeploy.json indique les paramètres modifiables utilisés dans ce modèle. Le fichier azuredeploy-parameters.json décrit précédemment dans cet article est utilisé pour transmettre des valeurs dans la section des paramètres du fichier azuredeploy.json pendant l'exécution du modèle.

### Section Variables

La section Variables indique les variables qui peuvent être utilisées dans ce modèle. Cette section contient un certain nombre de champs (types de données ou fragments JSON) définis sur des valeurs constantes ou calculées lors de l’exécution. L’exemple suivant montre la section des variables pour ce modèle MongoDB.

    "variables": {
          "_comment0": "/* T-shirt sizes may vary for different reasons, and some customers may want to modify these - so feel free to go ahead and define your favorite t-shirts */",
          "tshirtSizeXSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 1,
              "totalMemberCount": 2,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 2,
              "totalMemberCount": 3,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeMedium": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 3,
              "totalMemberCount": 4,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 2,
              "dataDiskSize": 250
          },
          "tshirtSizeLarge": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 250
          },
          "tshirtSizeXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 500
          },
          "tshirtSizeXXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 15,
              "totalMemberCount": 16,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 8,
              "dataDiskSize": 500
          },
          "osFamilyUbuntu": {
              "osName": "ubuntu",
              "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
              "installerPackages": "mongodb-org",
              "imagePublisher": "Canonical",
              "imageOffer": "UbuntuServer",
              "imageSKU": "14.04.2-LTS"
          },
          "vmStorageAccountContainerName": "vhd-mongodb",
          "vmStorageAccountDomain": ".blob.core.windows.net",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "sharedScriptUrl": "[concat('https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/', variables('osFamilySpec').osName, '/')]",
          "scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-high-availability/",
          "templateBaseUrl": "[variables('scriptUrl')]",
          "jumpboxTemplateEnabled": "jumpbox-resources.json",
          "jumpboxTemplateDisabled": "empty-resources.json",
          "arbiterTemplateEnabled": "arbiter-resources.json",
          "arbiterTemplateDisabled": "empty-resources.json",
          "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
          "jumpboxTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('jumpboxTemplate', parameters('jumpbox'))))]",
          "arbiterTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('arbiterTemplate', variables('clusterSpec').arbiter)))]",
          "commonSettings": {
              "availabilitySetName": "mongodbAvailSet",
              "region": "[parameters('region')]"
          },
          "storageSettings": {
              "vhdStorageAccountName": "[parameters('storageAccountName')]",
              "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
              "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]",
              "storageAccountCount": "[variables('clusterSpec').storageAccountCount]"
          },
          "networkSettings": {
              "virtualNetworkName": "[parameters('virtualNetworkName')]",
              "addressPrefix": "[parameters('addressPrefix')]",
              "subnetName": "[parameters('subnetName')]",
              "subnetPrefix": "[parameters('subnetPrefix')]",
              "subnetRef": "[concat(variables('vnetID'), '/subnets/', parameters('subnetName'))]",
              "machineIpPrefix": "[parameters('nodeAddressPrefix')]"
          },
          "machineSettings": {
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]",
              "machineNamePrefix": "mongodb-",
              "osImageReference": {
                  "publisher": "[variables('osFamilySpec').imagePublisher]",
                  "offer": "[variables('osFamilySpec').imageOffer]",
                  "sku": "[variables('osFamilySpec').imageSKU]",
                  "version": "latest"
              }
          },
          "clusterSpec": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
          "osFamilySpec": "[variables(concat('osFamily', parameters('osFamily')))]",
          "installCommand": "[concat('bash mongodb-', variables('osFamilySpec').osName, '-install.sh', ' -i ', variables('osFamilySpec').installerBaseUrl, ' -b ', variables('osFamilySpec').installerPackages, ' -r ', parameters('replicaSetName'), ' -k ', parameters('replicaSetKey'), ' -u ', parameters('adminUsername'), ' -p ', parameters('adminPassword'), ' -x ', variables('networkSettings').machineIpPrefix, ' -n ', variables('clusterSpec').totalMemberCount)]",
          "vmScripts": {
              "scriptsToDownload": [
                  "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
                  "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
              ],
              "regularNodeInstallCommand": "[variables('installCommand')]",
              "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
              "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
          },
          "_comment1": "/* The list of values below helps partition VM disks across multiple storage accounts to achieve a better throughput */",
          "_comment2": "/* Feel free to modify the default allocations if you are comfortable and understand what you are doing */",
          "storageAccountForXSmall_0": "0",
          "storageAccountForXSmall_1": "0",
          "storageAccountForSmall_0": "0",
          "storageAccountForSmall_1": "0",
          "storageAccountForSmall_2": "0",
          "storageAccountForMedium_0": "0",
          "storageAccountForMedium_1": "0",
          "storageAccountForMedium_2": "0",
          "storageAccountForMedium_3": "0",
          "storageAccountForLarge_0": "0",
          "storageAccountForLarge_1": "1",
          "storageAccountForLarge_2": "2",
          "storageAccountForLarge_3": "3",
          "storageAccountForLarge_4": "0",
          "storageAccountForLarge_5": "1",
          "storageAccountForLarge_6": "2",
          "storageAccountForLarge_7": "3",
          "storageAccountForXLarge_0": "0",
          "storageAccountForXLarge_1": "1",
          "storageAccountForXLarge_2": "2",
          "storageAccountForXLarge_3": "3",
          "storageAccountForXLarge_4": "0",
          "storageAccountForXLarge_5": "1",
          "storageAccountForXLarge_6": "2",
          "storageAccountForXLarge_7": "3",
          "storageAccountForXXLarge_0": "0",
          "storageAccountForXXLarge_1": "1",
          "storageAccountForXXLarge_2": "2",
          "storageAccountForXXLarge_3": "3",
          "storageAccountForXXLarge_4": "4",
          "storageAccountForXXLarge_5": "5",
          "storageAccountForXXLarge_6": "6",
          "storageAccountForXXLarge_7": "7",
          "storageAccountForXXLarge_8": "0",
          "storageAccountForXXLarge_9": "1",
          "storageAccountForXXLarge_10": "2",
          "storageAccountForXXLarge_11": "3",
          "storageAccountForXXLarge_12": "4",
          "storageAccountForXXLarge_13": "5",
          "storageAccountForXXLarge_14": "6",
          "storageAccountForXXLarge_15": "7"
      },

Dans l’exemple précédent, vous pouvez voir deux approches différentes. Dans ce premier fragment, la variable « osFamilyUbuntu » est définie sur un élément JSON contenant 6 paires clé-valeur.

    "osFamilyUbuntu": {
      "osName": "ubuntu",
      "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
      "installerPackages": "mongodb-org",
      "imagePublisher": "Canonical",
      "imageOffer": "UbuntuServer",
      "imageSKU": "14.04.2-LTS"
    },

Dans ce deuxième fragment, la variable « vmScripts » est attribuée à un tableau JSON où les éléments uniques sont calculés lors de l'exécution en utilisant une fonction de langage du modèle (concat) et la valeur d'une autre variable ainsi que des constantes de chaîne.

    "vmScripts": {
      "scriptsToDownload": [
      "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
      "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],

Un concept important dans ce modèle est la façon dont les différentes « tailles de t-shirt » pour les clusters MongoDB sont définies. En examinant une de ces variables « tshirtSizeXXXX », vous pouvez remarquer qu'elle décrit des caractéristiques importantes sur la manière dont un cluster est déployé. Examinons la taille moyenne (« Medium ») à titre d'exemple.

    "tshirtSizeMedium": {
      "vmSizeMember": "Standard_D2",
      "vmSizeArbiter": "Standard_A1",
      "numberOfMembers": 3,
      "totalMemberCount": 4,
      "arbiter": "Enabled",
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
      "storageAccountCount": 2,
      "dataDiskSize": 250
    },

Un cluster MongoDB « Medium » utilisera D2 comme taille de machine virtuelle pour les trois nœuds MongoDB hébergeant des données, ainsi qu'une quatrième machine virtuelle A1 qui sera utilisée comme arbitre pour la réplication. Le sous-modèle correspondant appelé pour déployer des nœuds de données sera `member-resources-D2.json` et les fichiers de données (250 Go chacun) seront stockés dans deux comptes de stockage. Ces variables seront utilisées dans la section des ressources pour organiser les déploiements de nœuds et d'autres tâches.

### Section Ressources

La plupart des actions se déroulent dans la section des ressources. En effectuant une recherche minutieuse dans cette section, vous pouvez immédiatement identifier deux cas distincts : le premier est un élément défini de type `Microsoft.Resources/deployments` qui correspond principalement à l’appel d’un déploiement imbriqué au déploiement principal. L’élément « templateLink » (et la propriété de version associée) permettent de spécifier un fichier de modèle lié qui sera appelé en transmettant un ensemble de paramètres en entrée, comme vous pouvez le voir dans l’exemple suivant.

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

L’exemple précédent présente clairement la façon dont azuredeploy.json est organisé en mécanisme d'orchestration, pour appeler un certain nombre d'autres fichiers de modèle, chacun d'eux étant responsable d'une partie des activités de déploiement requises.

Vous pouvez notamment remarquer les modèles liés suivants qui sont utilisés pour ce déploiement :

-	**shared-resource.json** : contient la définition de toutes les ressources partagées lors du déploiement. Par exemple, c'est le cas des comptes de stockage utilisés pour stocker des disques de système d'exploitation et des réseaux virtuels de machines virtuelles.
-	**Jumpbox-resources.json** : lorsque ce modèle est activé, il est responsable du déploiement de toutes les ressources liées à la machine virtuelle Jumpbox, celle disposant d’une adresse IP publique qui peut être utilisée pour accéder au cluster MongoDB à partir d’un réseau public.
-	**Arbiter-resources.json** : lorsque ce modèle est activé, il déploie un membre arbitre dans le cluster MongoDB. Un arbitre ne contient aucune donnée, mais est utilisé lorsqu'un jeu de réplicas contient un nombre pair de nœuds pour gérer les élections principales.
-	**Membre-ressources-Dx.json** : indique des modèles de ressources qui déploient les nœuds MongoDB. Un fichier spécifique sera utilisé en fonction de la définition de taille de t-shirt sélectionnée. La seule différence entre les fichiers étant le nombre de disques liés pour chaque nœud.
-	**Mongodb-ubuntu-install.sh** : un fichier de script d’interpréteur de commandes appelé par l’extension CustomScriptForLinux sur chaque nœud du cluster. Responsable du montage et de la mise en forme des disques de données, ainsi que l'installation de bits MongoDB sur le nœud.

Pour déployer un cluster MongoDB, une logique spécifique est requise pour pouvoir configurer correctement un jeu de réplicas. L’exemple suivant montre la séquence spécifique que vous devez utiliser au cours du déploiement.

DÉPLOYER MEMBRES DE DONNÉES (en parallèle) => DÉPLOYER DERNIER MEMBRE DE DONNÉES => (facultatif) DÉPLOYER ARBITRE

Dans cette séquence, le déploiement de plusieurs nœuds de données aura lieu en parallèle, à l'exception du dernier nœud. C'est là que le cluster sera formé et qu'un nouveau jeu de réplicas sera déployé. Donc, tous les nœuds précédents devront être opérationnels avant cela. La dernière étape consiste à déployer le nœud arbitre facultatif (uniquement pour les tailles de t-shirt pour lesquelles cela est nécessaire).

En consultant à nouveau notre modèle principal (azuredeploy.json), observons dans l’exemple suivant comment cette logique est implémentée, à partir de tous les membres de données.

    {
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('member-resources', copyindex())]",
      "apiVersion": "2015-01-01",
      "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
      ],
      "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').numberOfMembers]"
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
            "value": {
              "vhdStorageAccountName": "[concat(variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())))]",
                              "vhdContainerName": "[variables('storageSettings').vhdContainerName]",
                              "destinationVhdsContainer": "[concat('https://', variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())), variables('vmStorageAccountDomain'), '/', variables('storageSettings').vhdContainerName, '/')]"
            }
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          },
          "machineSettings": {
            "value": {
              "adminUsername": "[variables('machineSettings').adminUsername]",
                              "adminPassword": "[variables('machineSettings').adminPassword]",
                              "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                              "osImageReference": "[variables('machineSettings').osImageReference]",
                              "vmSize": "[variables('clusterSpec').vmSizeMember]",
                              "dataDiskSize": "[variables('clusterSpec').dataDiskSize]",
                              "machineIndex": "[copyindex()]",
                              "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                              "commandToExecute": "[variables('vmScripts').regularNodeInstallCommand]"
            }
          }
        }
      }
    },

Un concept important à retenir est la façon dont il est possible de déployer plusieurs copies d'un type de ressource unique et, pour chaque instance, de définir des valeurs uniques pour les paramètres requis. Ce concept est appelé *itération de ressource*.

Dans l’exemple précédent, un paramètre (nombre de nœuds à déployer dans le cluster) est utilisé pour définir une variable (« numberOfMembers ») qui est ensuite transmise à l’élément **« copie »** pour déclencher un nombre (boucle) de déploiements enfants, chacun d’eux entraînant l’instanciation du modèle pour chaque membre du cluster. Pour pouvoir définir tous les paramètres où des valeurs uniques entre différentes instances sont requises, vous pouvez utiliser la fonction **copyindex()** pour obtenir une valeur numérique indiquant l'index actuel dans cette création d'itération de ressource particulière.

Un autre concept important pour la création de ressources est la possibilité de spécifier les dépendances et priorités entre les ressources, comme vous pouvez le constater dans le tableau JSON **dependsOn**. Dans ce modèle particulier, le déploiement de chaque nœud dépend de la réussite du déploiement précédent des **ressources partagées**.

Les disques liés sont formatés durant les activités de préparation du nœud déclenchées par l'exécution du fichier de script mongodb-ubuntu-install.sh. En fait, dans ce fichier, vous trouverez une instance de cet appel.

    bash ./vm-disk-utils-0.1.sh -b $DATA_DISKS -s

vm-disk-utils-0.1.sh appartient au dossier shared\_scripts\\ubuntu, dans le référentiel Github azure-quickstart-tempates et contient des fonctions très utiles pour le montage, la mise en forme et la troncation de disque qui peuvent être réutilisées lorsque que vous devez exécuter des tâches similaires lors de la création de votre modèle.

Le fragment associé aux extensions de machines virtuelles CustomScriptForLinux est également intéressant à étudier. Celles-ci sont installées en tant que type de ressources distinct, avec une dépendance sur chaque modèle de déploiement de nœud du cluster. Pour obtenir un exemple, consultez le fragment suivant à la fin de chaque fichier member-resources-Dx.json.

    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installmongodb')]",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('commonSettings').region]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
      ],
      "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
          "fileUris": "[parameters('machineSettings').vmScripts]", "commandToExecute":"[parameters('machineSettings').commandToExecute]"
        }
      }
    }

En vous familiarisant avec les autres fichiers inclus dans ce déploiement, vous serez en mesure de comprendre tous les détails et les meilleures pratiques requises pour organiser et orchestrer des stratégies de déploiement complexes pour les solutions à nœuds multiples, basées sur n’importe quelle technologie, en exploitant les modèles Azure Resource Manager. Il existe une approche recommandée (mais nullement obligatoire) consistant à structurer vos fichiers de modèle comme l'indique le schéma suivant.

![mongodb-modèle-structure](media/virtual-machines-mongodb-template/mongodb-template-structure.png)

En résumé, cette approche suggère de :

-	définir votre fichier de modèle principal comme le point d'orchestration central pour toutes les activités de déploiement spécifiques, en exploitant les liaisons de modèles pour appeler les exécutions de modèles secondaires ;
-	créer un fichier de modèle spécifique déployant toutes les ressources partagées entre toutes les autres tâches du déploiement spécifiques (par exemple, les comptes de stockage et la configuration du réseau virtuel, etc.). Cette méthode peut être régulièrement réutilisée entre les déploiements ayant des exigences similaires en termes d'infrastructure commune ;
-	inclure des modèles de ressource facultatifs pour répondre aux besoins directs d'une ressource donnée ;
-	créer des modèles spécifiques pour les mêmes membres d’un groupe de ressources (nœuds dans un cluster, etc.) afin d’exploiter l’itération de ressources pour déployer plusieurs instances ayant des propriétés uniques ;
-	exploiter les extensions de déploiement de script et créer des scripts propres à chaque technologie pour toutes les tâches de post-déploiement (par exemple, l'installation du produit, les configurations, etc.).

Pour plus d’informations, consultez [Langage du modèle Azure Resource Manager](../resource-group-authoring-templates.md).

<!---HONumber=AcomDC_1203_2015-->