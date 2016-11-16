---
title: "Guide de création d’un modèle Resource Manager | Microsoft Docs"
description: "Une procédure étape par étape de création d’un modèle de gestionnaire de ressources, approvisionnant une architecture Azure IaaS de base."
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: f1cfd704-f6e1-47d5-8094-b439c279c13f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2016
ms.author: navale;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8dcfe27b87cd76ea7b8f75c3c36f0115131eb6ae


---
# <a name="resource-manager-template-walkthrough"></a>Guide de création d’un modèle Resource Manager
L’une des premières questions lorsque l’on crée un modèle est : « Par où commencer ? ». Vous pouvez commencer par un modèle vierge, en suivant la structure de base décrite dans [l’article sur la création de modèles](resource-group-authoring-templates.md#template-format), et ajouter les ressources ainsi que les paramètres et variables appropriés. Vous pouvez aussi commencer par parcourir la [galerie des modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates) et rechercher des scénarios similaires à celui que vous essayez de créer. Vous pouvez fusionner plusieurs modèles ou en modifier un existant pour l’adapter à votre scénario spécifique. 

Commençons par étudier une infrastructure commune :

* deux machines virtuelles qui utilisent le même compte de stockage et sont présentes au sein du même groupe à haute disponibilité et au sein du même sous-réseau d’un réseau virtuel ;
* une carte d’interface réseau et une adresse IP uniques pour chaque machine virtuelle ;
* Un équilibrage de charge avec une règle d’équilibrage de charge sur le port 80

![architecture](./media/resource-group-overview/arm_arch.png)

Cette rubrique vous guide tout au long des étapes de création d’un modèle Resource Manager pour cette infrastructure. Le modèle final que vous créez est basé sur un modèle de démarrage rapide appelé [2 machines virtuelles avec équilibrage de charge et de configuration de règles d’équilibrage de charge](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

Mais, cela fait beaucoup de choses à créer en une fois. Nous allons donc d’abord créer un compte de stockage et le déployer. Une fois que vous aurez maîtrisé la création du compte de stockage, vous ajouterez les autres ressources et redéploierez le modèle pour terminer l’infrastructure.

> [!NOTE]
> Vous pouvez utiliser n’importe quel type d’éditeur lors de la création du modèle. Visual Studio fournit des outils qui simplifient le développement de modèles, mais vous n’avez pas besoin de Visual Studio pour suivre ce didacticiel. Pour obtenir un didacticiel sur la création d’une application web et le déploiement d’une base de données SQL Database à l’aide de Visual Studio, voir [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 
> 
> 

## <a name="create-the-resource-manager-template"></a>Créer le modèle Resource Manager
Le modèle est un fichier JSON qui définit toutes les ressources que vous allez déployer. Il vous permet également de définir les paramètres spécifiés pendant le déploiement, les variables construites à partir d’autres valeurs et expressions ainsi que les sorties du déploiement. 

Commençons par le modèle le plus simple :

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Enregistrez ce fichier en tant que **azuredeploy.json** (notez que le modèle peut avoir le nom de votre choix, et qu’il doit simplement s’agir d’un fichier json).

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
Dans la section **resources** , ajoutez un objet qui définit le compte de stockage, comme indiqué ci-dessous. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Vous vous demandez peut-être d’où ces propriétés et ces valeurs proviennent. Les propriétés **type**, **name**, **apiVersion**, et **location** sont des éléments standards qui sont disponibles pour tous types de ressource. Vous pouvez en savoir plus sur les éléments communs dans [Resources](resource-group-authoring-templates.md#resources). **name** est défini sur une valeur de paramètre que vous transmettez pendant le déploiement et **location** comme emplacement utilisé par le groupe de ressources. Vous découvrirez comment définir **type** et **apiVersion** dans les sections ci-dessous.

La section **properties** contient toutes les propriétés qui sont uniques à un type de ressource particulier. Les valeurs que vous spécifiez dans cette section correspondent à l’opération PUT dans l’API REST pour la création de ce type de ressource. Lorsque vous créez un compte de stockage, vous devez fournir une propriété **accountType**. Notez que, dans l’ [API REST pour la création d’un compte de stockage](https://msdn.microsoft.com/library/azure/mt163564.aspx) , la section properties de l’opération REST contient également une propriété **accountType** ; les valeurs autorisées sont documentées. Dans cet exemple, le type de compte est défini sur **Standard_LRS**, mais vous pouvez spécifier une autre valeur ou permettre aux utilisateurs de transmettre le type de compte en tant que paramètre.

Revenons maintenant à la section **parameters** et voyons comment définir le nom du compte de stockage. Pour plus d’informations sur l’utilisation de paramètres, consultez [Parameters](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Ici, vous avez défini un paramètre de type chaîne qui contiendra le nom du compte de stockage. La valeur de ce paramètre sera fournie pendant le déploiement du modèle.

## <a name="deploying-the-template"></a>Déploiement du modèle
Nous avons un modèle complet pour la création d’un nouveau compte de stockage. Comme vous le savez, le modèle a été enregistré dans le fichier **azuredeploy.json** :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Il existe plusieurs façons de déployer un modèle, comme vous pouvez le voir dans [l’article sur le déploiement des ressources](resource-group-template-deploy.md). Pour déployer le modèle à l’aide d’Azure PowerShell, utilisez :

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile azuredeploy.json
```

Ou, pour déployer le modèle à l’aide de l’interface de ligne de commande Azure, utilisez :

```azurecli
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

Vous êtes maintenant l’heureux propriétaire d’un compte de stockage.

Dans les étapes suivantes, nous allons ajouter toutes les ressources requises pour déployer l’architecture décrite au début de ce tutoriel. Vous allez ajouter ces ressources dans le même modèle que celui sur lequel vous avez déjà travaillé.

## <a name="availability-set"></a>Groupe à haute disponibilité
Après la définition du compte de stockage, ajoutez un groupe à haute disponibilité pour les machines virtuelles. Dans ce cas, aucune propriété supplémentaire n’est nécessaire ; sa définition est relativement simple. Consultez l’ [API REST pour la création d’un groupe à haute disponibilité](https://msdn.microsoft.com/library/azure/mt163607.aspx) pour voir la totalité de la section properties, au cas où vous souhaiteriez définir le nombre de domaines d’erreurs et de domaines de mise à jour.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Notez que **name** est défini sur la valeur d’une variable. Dans ce modèle, le nom du groupe à haute disponibilité est nécessaire à plusieurs endroits différents. Vous pouvez gérer votre modèle plus facilement en définissant cette valeur une fois et en l’utilisant à plusieurs emplacements.

La valeur que vous spécifiez pour **type** contient le fournisseur de ressources et le type de ressource. Pour les groupes à haute disponibilité, le fournisseur de ressources est **Microsoft.Compute** et le type de ressource est **availabilitySets**. Vous pouvez obtenir la liste des fournisseurs de ressources disponibles en exécutant la commande PowerShell suivante :

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

Ou, si vous utilisez l’interface de ligne de commande Azure, vous pouvez exécuter la commande suivante :

```azurecli
    azure provider list
```
Cette rubrique vous enseignant la création de comptes de stockage, de machines virtuelles et de réseaux virtuels, vous allez travailler avec :

* Microsoft.Storage
* Microsoft.Compute
* Microsoft.Network

Pour visualiser les types de ressources pour un fournisseur particulier, exécutez la commande PowerShell suivante :

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

Ou, pour l’interface de ligne de commande Azure, la commande suivante renvoie les types disponibles au format JSON, puis les enregistre dans un fichier.

```azurecli
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Le type **availabilitySets** devrait faire partie des types présents sous **Microsoft.Compute**. Le nom complet du type est **Microsoft.Compute/availabilitySets**. Vous pouvez déterminer le nom du type de ressource pour toutes les ressources de votre modèle.

## <a name="public-ip"></a>Adresse IP publique
Définir une adresse IP publique. Là encore, consultez l’ [API REST pour les adresses IP publiques](https://msdn.microsoft.com/library/azure/mt163590.aspx) afin de découvrir quelles propriétés définir.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

La méthode d’allocation est définie sur **Dynamic** , mais vous pouvez la définir sur la valeur dont vous avez besoin ou la configurer pour accepter une valeur de paramètre. Vous avez autorisé les utilisateurs de votre modèle à transmettre une valeur pour l’étiquette de nom de domaine.

À présent, examinons comment déterminer **apiVersion**. La valeur que vous spécifiez correspond simplement à la version de l’API REST que vous souhaitez utiliser lors de la création de la ressource. Par conséquent, vous pouvez consulter la documentation de l’API REST pour ce type de ressource. Vous pouvez également exécuter la commande PowerShell suivante pour un type particulier :

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Cette dernière renvoie les valeurs suivantes :

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Pour afficher les versions d’API avec l’interface de ligne de commande Azure, exécutez la commande **azure provider show** illustrée précédemment.

Lorsque vous créez un nouveau modèle, utilisez la version la plus récente de l’API.

## <a name="virtual-network-and-subnet"></a>Réseau virtuel et sous-réseau
Créez un réseau virtuel avec un sous-réseau Consultez l’[API REST pour les réseaux virtuels](https://msdn.microsoft.com/library/azure/mt163661.aspx) afin de découvrir les propriétés à définir.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>Équilibrage de charge
À présent, vous allez créer un équilibrage de charge externe. Étant donné que cet équilibrage de charge utilise l’adresse IP publique, vous devez déclarer une dépendance de l’adresse IP publique dans la section **dependsOn** . Cela signifie que l’équilibrage de charge ne sera pas déployé tant que l’adresse IP publique n’a pas terminé le déploiement. Vous recevrez un message d’erreur si vous ne définissez pas cette dépendance, car le Resource Manager tente de déployer les ressources en parallèle et de définir l’équilibrage de charge à l’adresse IP publique qui n’existe pas encore. 

Vous créerez également, dans cette définition de ressource, un pool d’adresses principales, quelques règles NAT entrantes pour la connexion RDP dans les machines virtuelles et une règle d’équilibrage de charge avec une sonde TCP sur le port 80. Consultez l’ [API REST pour l’équilibrage de charge](https://msdn.microsoft.com/library/azure/mt163574.aspx) afin de découvrir les propriétés à définir.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>Interface réseau
Vous allez créer deux interfaces réseau, une pour chaque machine virtuelle. Plutôt que d’inclure les entrées en double pour les interfaces réseau, vous pouvez utiliser la [fonction copyIndex()](resource-group-create-multiple.md) pour itérer la boucle de copie (également appelée nicLoop) et créer le nombre d’interfaces réseau tel qu’il est défini dans les variables `numberOfInstances`. L’interface réseau dépend de la création du réseau virtuel et de l’équilibrage de charge. Elle utilise le sous-réseau défini lors de la création d’un réseau virtuel ainsi que l’id d’équilibrage de charge pour configurer le pool d’adresses de l’équilibrage de charge et les règles NAT entrantes.
Consultez l’ [API REST pour les interfaces réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx) afin de découvrir les propriétés à définir.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>Machine virtuelle
Vous allez créer deux machines virtuelles à l’aide de la fonction copyIndex(), comme vous l’avez fait lors de la création des [interfaces réseau](#network-interface).
La création de machines virtuelles dépend du compte de stockage, de l’interface réseau et du groupe à haute disponibilité. Cette machine virtuelle sera créée à partir d’une image du marché, comme indiqué dans la propriété `storageProfile` ; la propriété `imageReference` est utilisée pour définir l’éditeur d’image, l’offre, la référence et la version. Enfin, un profil de diagnostic est configuré pour activer les diagnostics de la machine virtuelle. 

Pour rechercher les propriétés pertinentes pour une image Marketplace, consultez les articles [Sélectionner des images de machines virtuelles Linux](virtual-machines/virtual-machines-linux-cli-ps-findimage.md) ou [Sélectionner des images de machines virtuelles Windows](virtual-machines/virtual-machines-windows-cli-ps-findimage.md).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

> [!NOTE]
> Pour les images publiées par des **éditeurs tiers**, vous devez spécifier une autre propriété nommée `plan`. Vous trouverez un exemple dans [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) à partir de la galerie des modèles de démarrage rapide. 
> 
> 

Vous avez terminé la définition des ressources pour votre modèle.

## <a name="parameters"></a>parameters
Dans la section parameters, définissez les valeurs qui peuvent être spécifiées lors du déploiement du modèle. Définissez uniquement des paramètres pour les valeurs qui, selon vous, doivent varier au cours du déploiement. Vous pouvez définir une valeur de paramètre par défaut qui est utilisée si aucune autre valeur n’est fournie au cours du déploiement. Vous pouvez également définir les valeurs autorisées comme indiqué pour le paramètre **imageSKU** .

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>Variables
Dans la section variables, vous pouvez définir des valeurs qui sont utilisées à plusieurs endroits de votre modèle, ou qui sont construites à partir d’autres expressions ou variables. Les variables sont fréquemment utilisées pour simplifier la syntaxe de votre modèle.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Vous avez terminé le modèle. Vous pouvez comparer votre modèle au modèle complet dans la [galerie des modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates) sous le [modèle de 2 machines virtuelles avec équilibrage de charge et de configuration de règles d’équilibrage de charge](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules). Votre modèle peut être légèrement différent si vous utilisez des numéros de version différents. 

Vous pouvez redéployer le modèle en utilisant les mêmes commandes que celles utilisées lors du déploiement du compte de stockage. Il est inutile de supprimer le compte de stockage avant le redéploiement car Resource Manager ne recrée pas des ressources qui existent déjà et n’ont pas été modifiées.

## <a name="next-steps"></a>Étapes suivantes
* [Azure Resource Manager Template Visualizer](http://armviz.io/#/) est un excellent outil pour visualiser les modèles Resource Manager, qui sont difficiles à comprendre en lisant simplement le fichier json lorsqu’ils sont trop volumineux.
* Pour plus d’informations sur la structure du modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour apprendre à déployer des modèles, consultez [Déployer un groupe de ressources avec un modèle Azure Resource Manager](resource-group-template-deploy.md)
* Pour consulter une série en quatre parties sur l’automatisation du déploiement, consultez [Automatisation des déploiements d’applications sur des machines virtuelles Azure](virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md). Cette série couvre l’architecture, l’accès, la sécurité, la disponibilité, la mise à l’échelle et le déploiement des applications.




<!--HONumber=Nov16_HO2-->


