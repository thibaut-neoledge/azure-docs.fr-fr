<properties
	pageTitle="Gestion de l’état dans les modèles Resource Manager | Microsoft Azure"
	description="Ce didacticiel présente des approches recommandées pour l’utilisation des objets complexes afin de partager des données d’état avec des modèles Azure Resource Manager et leurs modèles liés."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="tomfitz"/>

# Partage d’état dans les modèles Azure Resource Manager

Cette rubrique présente les bonnes pratiques pour gérer et partager l’état dans les modèles. Les paramètres et les variables présentés dans cette rubrique sont des exemples du type d'objets que vous pouvez définir pour organiser aisément votre déploiement. À partir de ces exemples, vous pouvez implémenter vos propres objets avec les valeurs de propriété pertinentes pour votre environnement.

Cette rubrique fait partie d’un livre blanc plus volumineux. Pour lire tout le document, téléchargez [World Class ARM Templates Considerations and Proven Practices](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).


## Fournir les paramètres de configuration standard

Au lieu de proposer un modèle qui fournit une flexibilité totale et des variations innombrables, un modèle courant consiste à permettre de sélectionner des configurations connues, c'est-à-dire des tailles standard (bac à sable (sandbox), petite, moyenne et grande). Les autres exemples de taille standard sont des offres de produits, telles que l’édition Community ou Enterprise. Dans d’autres cas, il peut s’agir de configurations d’une technologie propres à une charge de travail, par exemple MapReduce ou sans SQL.

Avec des objets complexes, vous pouvez créer des variables qui contiennent des collections de données, parfois appelées « conteneurs de propriétés » et utiliser ces données pour effectuer la déclaration de ressources dans votre modèle. Cette approche fournit des configurations correctes, connues de différentes tailles qui sont préconfigurées pour les clients. Sans configurations connues, les clients finaux doivent eux-mêmes déterminer la taille de cluster, tenir compte des contraintes des ressources de plateforme et effectuer des opérations mathématiques pour identifier le partitionnement résultant des comptes de stockage et des autres ressources (en raison des contraintes de taille de cluster et de ressource). Outre l’amélioration de l’expérience du client qu’il procure, un petit nombre de configurations connues est plus facile à prendre en charge et peut vous aider à fournir un haut niveau de densité.

L’exemple suivant indique comment définir des variables qui contiennent des objets complexes pour représenter des collections de données. Les collections définissent des valeurs utilisées pour la taille de machine virtuelle, des paramètres réseau, des paramètres de système d’exploitation et des paramètres de disponibilité.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

Notez que la variable **tshirtSize** concatène la taille de t-shirt que vous avez fournie via un paramètre (**Small**, **Medium**, **Large**) au texte **tshirtSize**. Cette variable permet de récupérer la variable objet complexe associée pour cette taille de t-shirt.

Vous pouvez ensuite référencer ces variables plus loin dans le modèle. La possibilité de référencer des variables nommées et leurs propriétés simplifie la syntaxe du modèle et la compréhension du contexte. L’exemple suivant définit une ressource à déployer à l’aide d’objets indiqués ci-dessus pour définir des valeurs. Par exemple, notez que la taille de la machine virtuelle est définie en récupérant la valeur de `variables('tshirtSize').vmSize` tandis que la valeur de la taille du disque est extraite de `variables('tshirtSize').diskSize`. En outre, l'URI d'un modèle lié est défini avec la valeur de `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## Passer l’état à un modèle

Vous pouvez partager l’état dans un modèle via les paramètres que vous fournissez directement pendant le déploiement.

Le tableau suivant répertorie les paramètres couramment utilisés dans les modèles.

Nom | Valeur | Description
---- | ----- | -----------
location | Chaîne obtenue à partir d’une liste contrainte des régions Azure | Il s’agit de l’emplacement de déploiement des ressources.
storageAccountNamePrefix | Chaîne | Il s’agit du nom DNS unique du compte de stockage où sont placés les disques de la machine virtuelle.
domainName | Chaîne | Il s’agit du nom de domaine de la machine virtuelle jumpbox publique, dont le format est : **{Nom\_de\_domaine}.{emplacement}.cloudapp.com**. Par exemple : **monnomdedomaine.westus.cloudapp.azure.com**
adminUsername | Chaîne | Il s’agit du nom d’utilisateur des machines virtuelles
adminPassword | Chaîne | Il s’agit du mot de passe des machines virtuelles
tshirtSize | Chaîne obtenue à partir d’une liste contrainte des propositions de tailles de t-shirt | Il s’agit de la taille d’unité d’échelle nommée à approvisionner. Par exemple, « Petit », « Moyen », « Grand »
virtualNetworkName | Chaîne | Il s’agit du nom du réseau virtuel que le consommateur souhaite utiliser.
enableJumpbox | Chaîne obtenue à partir d’une liste contrainte (activée/désactivée) | Paramètre indiquant s’il faut activer une jumpbox pour l’environnement. Valeurs : « activée », « désactivée »

Le paramètre **tshirtSize** utilisé dans la section précédente est défini comme suit :

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## Passer l’état à des modèles liés

Lors de la connexion aux modèles liés, vous utilisez généralement une combinaison de variables statiques et générées.

### Variables statiques

Des variables statiques sont souvent utilisées pour fournir les valeurs de base, telles que des URL, qui sont utilisées au sein d’un modèle.

Dans l’extrait de modèle ci-dessous, `templateBaseUrl` indique l’emplacement racine du modèle dans GitHub. La ligne suivante génère une nouvelle variable `sharedTemplateUrl` qui concatène l’URL de base avec le nom connu du modèle de ressources partagées. En dessous, une variable objet complexe est utilisée pour stocker une taille de t-shirt, dans laquelle l’URL de base est concaténée à l’emplacement du modèle de configuration connu et stockée dans la propriété `vmTemplate`.

L’avantage de cette approche est que si l’emplacement du modèle change, il vous suffit de modifier la variable statique à un endroit, d’où la modification est transmise à tous les modèles liés.

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### Variables générées

En plus des variables statiques, plusieurs variables sont générées dynamiquement. Cette section répertorie les types courants de variables générées.

#### tshirtSize

Vous connaissez déjà cette variable générée de l’exemple précédent.

#### networkSettings

Dans une capacité, une fonctionnalité ou un modèle de solution étendue de bout en bout, les modèles liés créent généralement des ressources qui existent sur un réseau. Une approche simple consiste à utiliser un objet complexe pour stocker les paramètres de réseau et les transmettre aux modèles liés.

Un exemple de paramètres de réseau de communication est affiché ci-dessous.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### availabilitySettings

Les ressources créées dans des modèles liés sont souvent placées dans un groupe à haute disponibilité. Dans l’exemple suivant, le nom du groupe à haute disponibilité est spécifié, ainsi que le nombre de domaines d’erreur et de domaines de mise à jour à utiliser.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Si vous avez besoin de plusieurs groupes à haute disponibilité (par exemple, un pour les nœuds principaux et un autre pour les nœuds de données), vous pouvez utiliser un nom en tant que préfixe, indiquer plusieurs groupes à haute disponibilité, ou suivre le modèle indiqué précédemment pour créer une variable pour une taille de t-shirt spécifique.

#### storageSettings

Les détails du stockage sont souvent partagés avec les modèles liés. Dans l'exemple ci-dessous, un objet *storageSettings* fournit des détails sur les noms du compte de stockage et du conteneur de stockage.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### osSettings

Si vous utilisez des modèles liés, vous devrez peut-être transmettre des paramètres de système d’exploitation à différents types de nœuds entre les différents types de configurations connus. Un objet complexe est pratique pour stocker et partager facilement des informations de système d’exploitation et facilite également la prise en charge de plusieurs options de système d’exploitation pour le déploiement.

L’exemple suivant montre un objet pour *osSettings* :

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### machineSettings

Une variable générée, *machineSettings* correspond à un objet complexe contenant des variables de base pour la création d'une nouvelle machine virtuelle : le nom et le mot de passe d'utilisateur de l'administrateur, un préfixe pour les noms d'ordinateur virtuel et une référence de l'image du système d'exploitation, comme indiqué ci-dessous :

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

Notez que *osImageReference* récupère les valeurs à partir de la variable *osSettings* définie dans le modèle principal. Cela signifie que vous pouvez facilement modifier le système d’exploitation d’une machine virtuelle, soit entièrement, soit en fonction de la préférence d’un consommateur du modèle.

#### vmScripts

L'objet *vmScripts* contient des détails sur les scripts à télécharger et exécuter sur une instance de machine virtuelle, notamment des références externes et internes. Les références extérieures incluent l’infrastructure. Les références intérieures incluent le logiciel installé et la configuration.

La propriété *scriptsToDownload* permet de répertorier les scripts à télécharger vers la machine virtuelle.

Comme l’indique l’exemple ci-dessous, cet objet contient également des références pointant vers des arguments de ligne de commande pour différents types d’actions. Ces actions incluent l’exécution de l’installation par défaut pour chaque nœud, une installation qui s’exécute après le déploiement de tous les nœuds et l’exécution de tous les scripts pouvant être spécifiques à un modèle donné.

Cet exemple est tiré d’un modèle utilisé pour déployer MongoDB, qui nécessite un arbitre pour garantir une haute disponibilité. La chaîne *arbiterNodeInstallCommand* a été ajoutée à *vmScripts* pour installer l'arbitre.

Dans la section des variables, vous trouverez les variables qui définissent le texte spécifique pour exécuter le script avec les valeurs appropriées.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## Retourner l’état à partir d’un modèle

Vous pouvez non seulement transmettre des données vers un modèle, mais également renvoyer des données partagées vers le modèle d’appel. Dans la section **sortie** d'un modèle lié, vous pouvez fournir des paires clé/valeur qui peuvent être utilisées par le modèle source.

L’exemple suivant montre comment transmettre l’adresse IP privée générée dans un modèle lié.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

Dans le modèle principal, vous pouvez utiliser ces données avec la syntaxe suivante :

    "[reference('master-node').outputs.masterip.value]"

Vous pouvez utiliser cette expression dans la section outputs ou la section resources du modèle principal. En revanche, vous ne pouvez pas l’utiliser dans la section variables, car elle dépend de l’état d’exécution. Pour retourner cette valeur à partir du modèle principal, utilisez :

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
Pour obtenir un exemple dans lequel la section outputs d’un modèle lié est utilisée pour retourner des disques de données pour une machine virtuelle, consultez [Création de plusieurs disques de données pour une machine virtuelle](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine).

## Définir les paramètres d’authentification d’une machine virtuelle

Vous pouvez spécifier les paramètres d’authentification d’une machine virtuelle selon le modèle présenté ci-dessus pour les paramètres de configuration. Vous créez un paramètre pour passer le type d’authentification.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

Vous ajoutez des variables pour les différents types d’authentification et une variable pour stocker le type utilisé pour ce déploiement en fonction de la valeur du paramètre.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

Au moment de définir la machine virtuelle, vous affectez à **osProfile** la variable que vous avez créée.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## Étapes suivantes
- Pour en savoir plus sur les sections du modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour consulter les fonctions disponibles dans un modèle, voir [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).

<!---HONumber=AcomDC_0713_2016-->