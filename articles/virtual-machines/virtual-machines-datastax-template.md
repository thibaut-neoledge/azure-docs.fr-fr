<properties
	pageTitle="Utilisation de DataStax avec un modèle du Gestionnaire de ressources Ubuntu"
	description="Apprenez à déployer facilement un nouveau cluster DataStax sur des machines virtuelles Ubuntu en utilisant Azure PowerShell ou l’interface de ligne de commande Azure et un modèle du Gestionnaire de ressources"
	services="virtual-machines"
	documentationCenter=""
	authors="karthmut"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="karthmut"/>

# DataStax sous Ubuntu avec un modèle Microsoft Azure Resource Manager

DataStax est l'un des principaux acteurs dans le secteur du développement et de la livraison de solutions basées sur Apache Cassandra™, une technologie de base de données distribuée NoSQL soutenue commercialement et intégrée au sein des entreprises, largement reconnue comme agile, permanente et évolutive de façon prévisible pour n'importe quelle taille. DataStax propose les versions Enterprise (DSE) et Community (DSC). Cette solution fournit également des fonctionnalités de calcul en mémoire, de sécurité au niveau de l’entreprise, d’analyses intégrées rapides et puissantes et de recherche d’entreprise.

En plus de ce qui est déjà disponible dans Microsoft Azure Marketplace, vous pouvez maintenant déployer facilement un nouveau cluster DataStax sur des machines virtuelles Ubuntu à l'aide d'un modèle Resource Manager déployé par le biais de [Microsoft Azure PowerShell](../powershell-install-configure.md) ou de l'[interface de ligne de commande Microsoft Azure](../xplat-cli.md).

Les clusters récemment déployés basés sur ce modèle disposent de la topologie décrite dans le schéma suivant, bien que d'autres topologies puissent être obtenues facilement grâce à la personnalisation du modèle présenté dans cet article :

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

À l'aide de paramètres, vous pouvez définir le nombre de nœuds qui seront déployés dans le nouveau cluster Apache Cassandra. Une instance du service de centre d'opérations DataStax sera également déployée dans une machine virtuelle autonome du même réseau virtuel, ce qui vous permet de surveiller l'état du cluster et de tous les nœuds individuels, d’ajouter/de supprimer des nœuds et d'effectuer toutes les tâches administratives associées à ce cluster.

Une fois le déploiement terminé, vous pouvez accéder à l'instance de la machine virtuelle du centre d'opérations Datastax en utilisant l'adresse DNS configurée. La machine virtuelle OpsCenter a un port SSH 22 activé, ainsi qu'un port 8443 pour HTTPS. L'adresse DNS du centre d'opérations inclut *dnsName* et *region* entrés comme paramètres, donnant le format `{dnsName}.{region}.cloudapp.azure.com`. Si vous avez créé un déploiement avec le paramètre *dnsName* défini sur « datastax » dans la région « Ouest des États-Unis », vous pouvez accéder à la machine virtuelle du centre d'opérations Datastax pour le déploiement à l'adresse `https://datastax.westus.cloudapp.azure.com:8443`.

> [AZURE.NOTE]Le certificat utilisé dans le déploiement est un certificat auto-signé qui crée un avertissement de navigateur. Vous pouvez suivre le processus sur le site Web de [Datastax](http://www.datastax.com/) pour remplacer le certificat par votre propre certificat SSL.

Avant de voir plus en détail Azure Resource Manager et le modèle utilisé pour ce déploiement, veuillez vérifier que vous disposez d'une version configurée et opérationnelle d'Azure PowerShell et de l'interface de ligne de commande Azure.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Créer un cluster Cassandra basé sur DataStax avec un modèle Resource Manager

Procédez comme suit pour créer un cluster Apache Cassandra basé sur DataStax, en utilisant un modèle Resource Manager du référentiel de modèles Github. Chaque étape inclut des instructions pour Azure PowerShell et l'interface de ligne de commande Azure.

### Étape 1-a : télécharger les fichiers de modèle à l'aide de PowerShell

Créez un dossier local pour le modèle JSON et autres fichiers associés (par exemple, C:\\Azure\\Templates\\DataStax).

Incluez le nom de votre dossier local et exécutez les commandes suivantes :

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

### Étape 1-b : télécharger les fichiers de modèle à l’aide de l’interface de ligne de commande Azure

Clonez le référentiel de modèles entier à l'aide d'un client git de votre choix, par exemple :

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Lorsque vous avez terminé, recherchez le dossier **datastax-on-ubuntu** dans le répertoire C:\\Azure\\Templates.

### Étape 2 : (facultatif) comprendre les paramètres du modèle

Lorsque vous déployez des solutions importantes comme un cluster Apache Cassandra basé sur DataStax, vous devez spécifier un ensemble de paramètres de configuration pour gérer un nombre de paramètres requis. En déclarant ces paramètres dans la définition du modèle, vous pouvez spécifier des valeurs pendant l'exécution du déploiement au moyen d'un fichier externe ou dans la ligne de commande.

Recherchez la section "parameters" en haut du fichier **azuredeploy.json**. Vous y trouverez l'ensemble de paramètres requis par le modèle pour configurer un cluster DataStax. Voici un exemple de la section des paramètres dans le fichier azuredeploy.json de ce modèle :

	"parameters": {
		"region": {
			"type": "string",
			"defaultValue": "West US",
			"metadata": {
				"Description": "Location where resources will be provisioned"
			}
		},
		"storageAccountPrefix": {
			"type": "string",
			"defaultValue": "uniqueStorageAccountName",
			"metadata": {
				"Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the opserations center public IP"
			}
		},
		"virtualNetworkName": {
			"type": "string",
			"defaultValue": "myvnet",
			"metadata": {
				"Description": "Name of the virtual network provisioned for the cluster"
			}
		},
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
		"opsCenterAdminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Sets the operations center admin user password"
			}
		},
		"clusterVmSize": {
			"type": "string",
			"defaultValue": "Standard_D3",
			"allowedValues": [
				"Standard_D1",
				"Standard_D2",
				"Standard_D3",
				"Standard_D4",
				"Standard_D11",
				"Standard_D12",
				"Standard_D13",
				"Standard_D14"
			],
			"metadata": {
				"Description": "The size of the virtual machines used when provisioning cluster nodes"
			}
		},
		"clusterNodeCount": {
			"type": "int",
			"metadata": {
				"Description": "The number of nodes provisioned in the cluster"
			}
		},
		"clusterName": {
			"type": "string",
			"metadata": {
				"Description": "The name of the cluster provisioned"
			}
		}
	}

Chaque paramètre contient des détails, tels que le type de données et les valeurs autorisées. Ceci permet la validation des paramètres passés pendant l'exécution du modèle en mode interactif (par exemple, PowerShell ou interface de ligne de commande Azure), ainsi qu'une interface utilisateur à détection automatique, qui peut être construite de manière dynamique par l'analyse de la liste des paramètres requis et leurs descriptions.

### Étape 3-a : Déployer un cluster DataStax avec un modèle à l’aide de PowerShell

Préparez un fichier de paramètres pour votre déploiement en créant un fichier JSON contenant des valeurs d'exécution pour tous les paramètres. Ce fichier est ensuite transmis en tant qu’entité unique à la commande de déploiement. Si vous n'incluez pas de fichier de paramètres, PowerShell utilisera les valeurs par défaut spécifiées dans le modèle et vous invitera à renseigner les valeurs restantes.

Voici un exemple de jeu de paramètres issu du fichier **azuredeploy-parameters.json** :

	{
		"storageAccountPrefix": {
			"value": "scorianisa"
		},
		"dnsName": {
			"value": "scorianids"
		},
		"virtualNetworkName": {
			"value": "datastax"
		},
		"adminUsername": {
			"value": "scoriani"
		},
		"adminPassword": {
			"value": ""
		},
		"region": {
			"value": "West US"
		},
		"opsCenterAdminPassword": {
			"value": ""
		},
		"clusterVmSize": {
			"value": "Standard_D3"
		},
		"clusterNodeCount": {
			"value": 3
		},
		"clusterName": {
			"value": "cl1"
		}
	}

Entrez un nom de déploiement Azure, un nom de groupe de ressources, un emplacement Azure et le dossier du fichier de déploiement JSON enregistré. Exécutez ensuite ces commandes :

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Lorsque vous exécutez la commande **New-AzureResourceGroupDeployment**, elle extrait les valeurs de paramètre à partir du fichier de paramètres JSON et démarre l'exécution du modèle correspondant. Le fait de définir et d’utiliser plusieurs fichiers de paramètres sur vos différents environnements (par exemple, Test, Production, etc.) favorise la réutilisation du modèle et simplifie les solutions contenant plusieurs environnements complexes.

Lors du déploiement, n'oubliez pas qu'un nouveau compte Azure Storage doit être créé. Le nom fourni en tant que paramètre de compte de stockage doit donc être unique et respecter la configuration requise pour un compte Azure Storage (lettres en minuscules et chiffres uniquement).

Pendant et après le déploiement, vous pouvez vérifier toutes les requêtes effectuées durant la configuration et notamment toutes les erreurs survenues.

Pour ce faire, accédez au [Portail Azure](https://portal.azure.com), puis procédez comme suit :

- Dans la barre de navigation située sur la gauche, cliquez sur « Parcourir », faites défiler les options vers le bas, puis cliquez sur « Groupes de ressources ».  
- Après avoir cliqué sur le groupe de ressources que vous venez de créer, le volet « Groupe de ressources » s'affiche.  
- En cliquant sur l'histogramme « Événements » dans la partie « Surveillance » du volet « Groupe de ressources », les événements de votre déploiement s'affichent :
- En cliquant sur les différents événements, vous pouvez accéder à des informations plus détaillées sur chaque opération menée pour le compte du modèle.

Après vos tests, si vous devez supprimer ce groupe de ressources et toutes ses ressources (le compte de stockage, la machine virtuelle et le réseau virtuel), utilisez cette commande unique :

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Étape 3-b : Déployer un cluster DataStax avec un modèle à l’aide de l’interface de ligne de commande de Microsoft Azure

Pour déployer un cluster DataStax par le biais de l'interface de ligne de commande Microsoft Azure, commencez par créer un groupe de ressources en spécifiant un nom et un emplacement :

	azure group create dsc "West US"

Intégrez ce nom de groupe de ressources, l'emplacement du fichier de modèle JSON et l'emplacement du fichier de paramètres (voir la section PowerShell ci-dessus pour plus de détails) dans la commande suivante :

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Vous pouvez vérifier l'état des différents déploiements de ressources à l'aide de la commande suivante :

	azure group deployment list dsc

## Visite guidée de l'organisation des fichiers et de la structure de modèle DataStax

Pour créer un modèle Resource Manager robuste et réutilisable, vous devez également prendre en compte l'organisation d'une série de tâches complexes et interdépendantes requises durant le déploiement d'une solution complexe telle que DataStax. En tirant parti des capacités de **liaison de modèles** et de **l'itération de ressource** ARM en plus de l'exécution de scripts via les extensions associées, vous pouvez implémenter une approche modulaire qui peut être réutilisée lors de n'importe quel déploiement basé sur un modèle complexe.

Ce schéma décrit les relations entre tous les fichiers téléchargés à partir de GitHub pour ce déploiement :

![datastax-files](media/virtual-machines-datastax-template/datastax-files.png)

Cette section vous guide dans la structure du fichier **azuredeploy.json** pour le cluster DataStax.

### Section "parameters"

La section « paramètres » du fichier **azuredeploy.json** indique les paramètres modifiables utilisés dans ce modèle. Le fichier **azuredeploy-parameters.json** décrit précédemment dans cet article est utilisé pour transmettre des valeurs dans la section « paramètres » du fichier azuredeploy.json pendant l'exécution du modèle.

### section « variables »

La section « variables » indique les variables qui peuvent être utilisées dans ce modèle. Cette section contient un certain nombre de champs (types de données ou fragments JSON) définis sur des valeurs constantes ou calculées lors de l'exécution. Voici la section « variables » pour ce modèle Datastax :

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-on-ubuntu/",
	"sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
	"clusterNodesTemplateUrl": "[concat(variables('templateBaseUrl'), 'ephemeral-nodes-resources.json')]",
	"opsCenterTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-resources.json')]",
	"opsCenterInstallTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-install-resources.json')]",
	"opsCenterVmSize": "Standard_A1",
	"networkSettings": {
		"virtualNetworkName": "[parameters('virtualNetworkName')]",
		"addressPrefix": "10.0.0.0/16",
		"subnet": {
			"dse": {
				"name": "dse",
				"prefix": "10.0.0.0/24",
				"vnet": "[parameters('virtualNetworkName')]"
			}
		},
		"statics": {
			"clusterRange": {
				"base": "10.0.0.",
				"start": 5
			},
			"opsCenter": "10.0.0.240"
		}
	},
	"osSettings": {
		"imageReference": {
			"publisher": "Canonical",
			"offer": "UbuntuServer",
			"sku": "14.04.2-LTS",
			"version": "latest"
		},
		"scripts": [
			"[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
			"[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
			"https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
		]
	},
	"sharedStorageAccountName": "[concat(parameters('storageAccountPrefix'),'cmn')]",
	"nodeList": "[concat(variables('networkSettings').statics.clusterRange.base, variables('networkSettings').statics.clusterRange.start, '-', parameters('clusterNodeCount'))]"
	},

En étudiant cet exemple plus en détail, vous pouvez voir deux approches différentes. Dans ce premier fragment, la variable « osSettings » est définie sur un élément JSON imbriqué contenant 4 paires clé-valeur :

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
Dans ce deuxième fragment, la variable « scripts » est un tableau JSON où chaque élément est calculé lors de l'exécution en utilisant une fonction de langage du modèle (concat) et la valeur d'une autre variable, ainsi que des constantes de chaîne :

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

### section « ressources »

La plupart des actions se déroulent dans la section **« ressources »**. En effectuant une recherche minutieuse dans cette section, vous pouvez immédiatement identifier deux cas distincts : le premier est un élément défini de type `Microsoft.Resources/deployments` qui correspond principalement à l'appel d'un déploiement imbriqué au déploiement principal. L'élément « templateLink » et la propriété de version associée permettent de spécifier un fichier de modèle lié qui sera appelé en transmettant un ensemble de paramètres en tant que résultat, comme vous pouvez le voir dans ce fragment :

	{
	      "name": "shared",
	      "type": "Microsoft.Resources/deployments",
	      "apiVersion": "2015-01-01",
	      "properties": {
	        "mode": "Incremental",
	        "templateLink": {
	          "uri": "[variables('sharedTemplateUrl')]",
	          "contentVersion": "1.0.0.0"
	        },
	        "parameters": {
	          "region": {
	            "value": "[parameters('region')]"
	          },
	          "networkSettings": {
	            "value": "[variables('networkSettings')]"
	          },
	          "storageAccountName": {
	            "value": "[variables('sharedStorageAccountName')]"
	          }
	        }
	      }
	    },

Ce premier exemple présente clairement la façon dont **azuredeploy.json** est organisé en mécanisme d'orchestration, pour appeler un certain nombre d'autres fichiers de modèle, chacun d'eux étant responsable d'une partie des activités de déploiement requises.

Vous pouvez notamment remarquer les modèles liés suivants qui sont utilisés pour ce déploiement :

-	**shared-resource.json** : contient la définition de toutes les ressources partagées lors du déploiement. Par exemple, c'est le cas des comptes de stockage utilisés pour stocker des disques de système d'exploitation et des réseaux virtuels de machines virtuelles.
-	**opscenter-resources.json** : déploie une machine virtuelle OpsCenter et toutes les ressources qui lui sont associées, y compris une interface réseau et une adresse IP publique.
-	**opscenter-install-resources.json** : déploie l'extension de machine virtuelle OpsCenter (script personnalisé pour Linux) qui appelle le fichier de script d'interpréteur de commandes spécifique (**opscenter.sh**) requis pour configurer le service OpsCenter au sein de cette machine virtuelle.
-	**ephemeral-nodes-resources.json** : déploie tous les nœuds de cluster de machines virtuelles et les ressources connectées (par exemple, des cartes réseau, des adresses IP privées, etc.). Ce modèle déploie également des extensions de machine virtuelle (scripts personnalisés pour Linux) et appelle un script d'interpréteur de commandes (**dsenode.sh**) pour installer physiquement les bits Apache Cassandra sur chaque nœud.

Penchons-nous sur l'utilisation de ce dernier modèle, car il s'agit d'un des plus intéressants, du point de vue du développement d'un modèle. Un concept important à retenir est la façon dont un fichier de modèle unique peut déployer plusieurs copies d'un type de ressource unique et, pour chaque instance, définir des valeurs uniques pour les paramètres requis. Ce concept est appelé **itération de ressource**.

Lorsque le fichier **ephemeral-nodes-resources.json** est appelé à partir du fichier **azuredeploy.json** principal, un paramètre appelé **nodeCount** est fourni parmi la liste de paramètres. Dans le modèle enfant, ce paramètre nodeCount (qui détermine le nombre de nœuds à déployer dans le cluster) est utilisé dans l'élément **« copie »** de chaque ressource à déployer en plusieurs copies, comme l'indique le fragment ci-dessous. Pour tous les paramètres où il est nécessaire de spécifier des valeurs uniques entre différentes instances de la ressource déployée, vous pouvez utiliser la fonction **copyindex()** pour obtenir une valeur numérique indiquant l'index actuel dans cette création d'itération de ressource particulière. Dans le fragment suivant, ce concept est appliqué à plusieurs créations de machines virtuelles pour les nœuds du cluster DataStax :

			   {
			      "apiVersion": "2015-05-01-preview",
			      "type": "Microsoft.Compute/virtualMachines",
			      "name": "[concat(parameters('namespace'), 'vm', copyindex())]",
			      "location": "[parameters('region')]",
			      "copy": {
			        "name": "[concat(parameters('namespace'), 'vmLoop')]",
			        "count": "[parameters('nodeCount')]"
			      },
			      "dependsOn": [
			        "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]",
			        "[concat('Microsoft.Compute/availabilitySets/', parameters('namespace'), 'set')]",
			        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]"
			      ],
			      "properties": {
			        "availabilitySet": {
			          "id": "[resourceId('Microsoft.Compute/availabilitySets', concat(parameters('namespace'), 'set'))]"
			        },
			        "hardwareProfile": {
			          "vmSize": "[parameters('vmSize')]"
			        },
			        "osProfile": {
			          "computername": "[concat(parameters('namespace'), 'vm', copyIndex())]",
			          "adminUsername": "[parameters('adminUsername')]",
			          "adminPassword": "[parameters('adminPassword')]"
			        },
			        "storageProfile": {
			          "imageReference": "[parameters('osSettings').imageReference]",
			          "osDisk": {
			            "name": "osdisk",
			            "vhd": {
			              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/', variables('vmName'), copyindex(), '-osdisk.vhd')]"
			            },
			            "caching": "ReadWrite",
			            "createOption": "FromImage"
			          },
			          "dataDisks": [
			            {
			              "name": "datadisk1",
			              "diskSizeGB": 1023,
			              "lun": 0,
			              "vhd": {
			                "Uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/','vhds/', variables('vmName'), copyindex(), 'DataDisk1.vhd')]"
			              },
			              "caching": "None",
			              "createOption": "Empty"
			            }
			          ]
			        },
			        "networkProfile": {
			          "networkInterfaces": [
			            {
			              "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('namespace'), 'nic', copyindex()))]"
			            }
			          ]
			        }
			      }
			    },

Un autre concept important pour la création de ressources est la possibilité de spécifier les dépendances et les priorités entre les ressources, comme vous pouvez le constater dans le tableau JSON **dependsOn**. Dans ce modèle, chaque nœud dispose également d'un disque de 1 To (voir « dataDisks ») qui peut être utilisé pour héberger des sauvegardes et des instantanés de l'instance Apache Cassandra.

Les disques liés sont formatés durant les activités de préparation du nœud déclenchées par l'exécution du fichier de script **dsenode.sh**. La première ligne de ce script appelle un autre script :

	bash vm-disk-utils-0.1.sh

vm-disk-utils-0.1.sh appartient au dossier **shared_scripts\\ubuntu**, dans le référentiel github azure-quickstart-tempates et contient des fonctions très utiles pour le montage, la mise en forme et la troncation de disque. Ces fonctions peuvent être utilisées dans tous les modèles du référentiel.

Le fragment associé aux extensions de machines virtuelles CustomScriptForLinux est également intéressant à étudier. Celles-ci sont installées en tant que type distinct de ressources, avec une dépendance sur chaque nœud du cluster (et l'instance OpsCenter). Elles tirent parti du même mécanisme de bouclage de ressource que celui décrit pour les machines virtuelles :

	{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat(parameters('namespace'), 'vm', copyindex(), '/installdsenode')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "[concat(parameters('namespace'), 'vmLoop')]",
		"count": "[parameters('nodeCount')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', parameters('namespace'), 'vm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": "[parameters('osSettings').scripts]",
			"commandToExecute": "bash dsenode.sh"
		}
	}
	}

En vous familiarisant avec les autres fichiers inclus dans ce déploiement, vous serez en mesure de comprendre tous les détails et les meilleures pratiques requises pour organiser et orchestrer des stratégies de déploiement complexes pour les solutions à nœuds multiples, basées sur n'importe quelle technologie, en exploitant les modèles Azure Resource Manager. Il existe une approche recommandée (mais nullement obligatoire) consistant à structurer vos fichiers de modèle comme l'indique le schéma suivant :

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

Pour résumer, cette approche suggère de :

-	définir votre fichier de modèle principal comme le point d'orchestration central pour toutes les activités de déploiement spécifiques, en exploitant les liaisons de modèles pour appeler les exécutions de modèles secondaires ;
-	créer un fichier de modèle spécifique déployant toutes les ressources partagées entre toutes les autres tâches du déploiement (par exemple, les comptes de stockage, la configuration de réseau virtuel, etc.). Cette méthode peut être régulièrement réutilisée entre les déploiements ayant des exigences similaires en termes d'infrastructure commune ;
-	inclure des modèles de ressource facultatifs pour répondre aux besoins directs d'une ressource donnée ;
-	créer des modèles spécifiques pour les mêmes membres d'un groupe de ressources (nœuds dans un cluster, etc.) afin d'exploiter l'itération de ressource pour déployer plusieurs instances ayant des propriétés uniques ;
-	exploiter les extensions de déploiement de script et créer des scripts propres à chaque technologie pour toutes les tâches de post-déploiement (par exemple, l'installation du produit, les configurations, etc.).

Pour plus d'informations, consultez [Langage des modèles du gestionnaire de ressources Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).
 

<!---HONumber=July15_HO2-->