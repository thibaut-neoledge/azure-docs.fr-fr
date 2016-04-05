<properties
   pageTitle="Guide de création d’un modèle Resource Manager | Microsoft Azure"
   description="Une procédure étape par étape de création d’un modèle de gestionnaire de ressources, approvisionnant une architecture Azure IaaS de base."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/29/2016"
   ms.author="navale;tomfitz"/>
   
# Guide de création d’un modèle Resource Manager

Cette rubrique vous guide tout au long des étapes de création d’un modèle Resource Manager. Vous allez créer un modèle basé sur le [modèle de 2 machines virtuelles avec équilibrage de charge et de configuration de règles d’équilibrage de charge](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules) dans la [galerie des modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates). Les techniques que vous apprenez peuvent être appliquées à n’importe quel modèle que vous devez créer.

Commençons par étudier une architecture commune :

* deux machines virtuelles qui utilisent le même compte de stockage et sont présentes au sein du même groupe à haute disponibilité et au sein du même sous-réseau d’un réseau virtuel ;
* une carte d’interface réseau et une adresse IP uniques pour chaque machine virtuelle ;
* Un équilibrage de charge avec une règle d’équilibrage de charge sur le port 80

![architecture](./media/resource-group-overview/arm_arch.png)

Vous avez décidé de déployer cette architecture sur Azure, et vous souhaitez utiliser des modèles Resource Manager afin de redéployer facilement l’architecture. Toutefois, vous ne savez pas comment créer ce modèle. Cette rubrique vous aidera à comprendre quels éléments inclure dans le modèle.

Vous pouvez utiliser n’importe quel type d’éditeur lors de la création du modèle. Visual Studio fournit des outils qui simplifient le développement de modèles, mais vous n’avez pas besoin de Visual Studio pour suivre ce didacticiel. Pour obtenir un didacticiel sur la création d’une application web et le déploiement d’une base de données SQL Database à l’aide de Visual Studio, voir [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Créer le modèle Resource Manager

Le modèle est un fichier JSON qui définit toutes les ressources que vous allez déployer. Il vous permet également de définir les paramètres spécifiés pendant le déploiement, les variables construites à partir d’autres valeurs et expressions ainsi que les sorties du déploiement.

Commençons par le modèle le plus simple :

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }

Enregistrez ce fichier sous **azuredeploy.json**.

Nous allons d’abord nous concentrer sur la section **resources**, puis passer aux sections **parameters** et **variables** plus loin dans cette rubrique.

## Compte de stockage
Vous allez définir un compte de stockage à utiliser par les machines virtuelles. Dans la section **resources**, ajoutez un objet qui définit le compte de stockage, comme indiqué ci-dessous.

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

Vous vous demandez peut-être d’où ces propriétés et ces valeurs proviennent. Les propriétés **type**, **name**, **apiVersion**, et **location** sont des éléments standards qui sont disponibles pour tous types de ressource. Vous pouvez en savoir plus sur les éléments communs dans la rubrique [Ressources](../resource-group-authoring-templates/#resources). Vous définissez **name** sur une valeur de paramètre que vous transmettez au cours du déploiement. Vous définissez **location** en tant que l’emplacement utilisé par le groupe de ressources. Vous découvrirez comment définir **type** et **apiVersion** dans les sections ci-dessous.

La section **properties** contient toutes les propriétés qui sont uniques à un type de ressource particulier. Les valeurs que vous spécifiez dans cette section correspondent à l’opération PUT dans l’API REST pour la création de ce type de ressource. Lorsque vous créez un compte de stockage, vous devez fournir une propriété **accountType**. Notez que, dans l’[API REST pour la création d’un compte de stockage](https://msdn.microsoft.com/library/azure/mt163564.aspx), la section properties de l’opération REST contient également une propriété **accountType** ; les valeurs autorisées sont documentées. Dans cet exemple, le type de compte est défini sur **Standard\_LRS**, mais vous pouvez spécifier une autre valeur ou permettre aux utilisateurs de transmettre le type de compte en tant que paramètre.

## Groupe à haute disponibilité
Après la définition du compte de stockage, ajoutez un groupe à haute disponibilité pour les machines virtuelles. Dans ce cas, aucune propriété supplémentaire n’est nécessaire ; sa définition est relativement simple. Consultez l’[API REST pour la création d’un groupe à haute disponibilité](https://msdn.microsoft.com/library/azure/mt163607.aspx) pour voir la totalité de la section properties, au cas où vous souhaitez définir le nombre de domaines d’erreurs et de domaines de mise à jour.

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

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

Ou, si vous utilisez l’interface de ligne de commande Azure, vous pouvez exécuter la commande suivante :

    azure provider list

Cette rubrique vous enseignant la création de comptes de stockage, de machines virtuelles et de réseaux virtuels, vous allez travailler avec :

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Pour visualiser les types de ressources pour un fournisseur particulier, exécutez la commande PowerShell suivante :

    PS C:\> (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes

Ou, pour l’interface de ligne de commande Azure, la commande suivante renvoie les types disponibles au format JSON, puis les enregistre dans un fichier.

    azure provider show Microsoft.Compute --json > c:\temp.json

Le type **availabilitySets** devrait faire partie des types présents sous **Microsoft.Compute**. Le nom complet du type est **Microsoft.Compute/availabilitySets**. Vous pouvez déterminer le nom du type de ressource pour toutes les ressources de votre modèle.

## Adresse IP publique
Définir une adresse IP publique. Là encore, consultez l’[API REST pour les adresses IP publiques](https://msdn.microsoft.com/library/azure/mt163590.aspx) afin de découvrir quelles propriétés définir.

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

La méthode d’allocation est définie sur **Dynamic**, mais vous pouvez la définir sur la valeur dont vous avez besoin ou la configurer pour accepter une valeur de paramètre. Vous avez autorisé les utilisateurs de votre modèle à transmettre une valeur pour l’étiquette de nom de domaine.

À présent, examinons comment déterminer **apiVersion**. La valeur que vous spécifiez correspond simplement à la version de l’API REST que vous souhaitez utiliser lors de la création de la ressource. Par conséquent, vous pouvez consulter la documentation de l’API REST pour ce type de ressource. Vous pouvez également exécuter la commande PowerShell suivante pour un type particulier :

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions

Cette dernière renvoie les valeurs suivantes :

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Pour afficher les versions d’API avec l’interface de ligne de commande Azure, exécutez la commande **azure provider show** illustrée précédemment.

Lorsque vous créez un nouveau modèle, utilisez la version la plus récente de l’API.

## Réseau virtuel et sous-réseau
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

## Load Balancer
À présent, vous allez créer un équilibrage de charge externe. Étant donné que cet équilibrage de charge utilise l’adresse IP publique, vous devez déclarer une dépendance de l’adresse IP publique dans la section **dependsOn**. Cela signifie que l’équilibrage de charge ne sera pas déployé tant que l’adresse IP publique n’a pas terminé le déploiement. Vous recevrez un message d’erreur si vous ne définissez pas cette dépendance, car le Resource Manager tente de déployer les ressources en parallèle et de définir l’équilibrage de charge à l’adresse IP publique qui n’existe pas encore.

Vous créerez également, dans cette définition de ressource, un pool d’adresses principales, quelques règles NAT entrantes pour la connexion RDP dans les machines virtuelles et une règle d’équilibrage de charge avec une sonde TCP sur le port 80. Consultez l’[API REST pour l’équilibrage de charge](https://msdn.microsoft.com/library/azure/mt163574.aspx) afin de découvrir les propriétés à définir.

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

## Interface réseau
Vous allez créer deux interfaces réseau, une pour chaque machine virtuelle. Plutôt que d’inclure les entrées en double pour les interfaces réseau, vous pouvez utiliser la [fonction copyIndex()](resource-group-create-multiple.md) pour itérer la boucle de copie (également appelée nicLoop) et créer le nombre d’interfaces réseau tel qu’il est défini dans les variables `numberOfInstances`. L’interface réseau dépend de la création du réseau virtuel et de l’équilibrage de charge. Elle utilise le sous-réseau défini lors de la création d’un réseau virtuel ainsi que l’id d’équilibrage de charge pour configurer le pool d’adresses de l’équilibrage de charge et les règles NAT entrantes. Consultez l’[API REST pour les interfaces réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx) afin de découvrir les propriétés à définir.

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

## Machine virtuelle
Vous allez créer deux machines virtuelles à l’aide de la fonction copyIndex(), comme vous l’avez fait lors de la création des [interfaces réseau](#network-interface). La création de machines virtuelles dépend du compte de stockage, de l’interface réseau et du groupe à haute disponibilité. Cette machine virtuelle sera créée à partir d’une image du marché, comme indiqué dans la propriété `storageProfile` ; la propriété `imageReference` est utilisée pour définir l’éditeur d’image, l’offre, la référence et la version. Enfin, un profil de diagnostic est configuré pour activer les diagnostics de la machine virtuelle.

Pour rechercher les propriétés pertinentes pour une image Marketplace, consultez les articles [Sélectionner des images de machines virtuelles Linux](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) ou [Sélectionner des images de machines virtuelles Windows](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md). Pour les images publiées par des éditeurs tiers, vous devez spécifier une autre propriété nommée `plan`. Vous trouverez un exemple dans [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) à partir de la galerie des modèles de démarrage rapide.


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

Vous avez terminé la définition des ressources pour votre modèle.

## Paramètres

Dans la section parameters, définissez les valeurs qui peuvent être spécifiées lors du déploiement du modèle. Définissez uniquement des paramètres pour les valeurs qui, selon vous, doivent varier au cours du déploiement. Vous pouvez définir une valeur de paramètre par défaut qui est utilisée si aucune autre valeur n’est fournie au cours du déploiement. Vous pouvez également définir les valeurs autorisées comme indiqué pour le paramètre **imageSKU**.

```
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

## Variables

Dans la section variables, vous pouvez définir des valeurs qui sont utilisées à plusieurs endroits de votre modèle, ou qui sont construites à partir d’autres expressions ou variables. Les variables sont fréquemment utilisées pour simplifier la syntaxe de votre modèle.

```
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



## Étapes suivantes

Vous avez créé votre modèle ; celui-ci est prêt pour le déploiement.

- Pour plus d’informations sur la structure du modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour apprendre à déployer des modèles, consultez [Déployer un groupe de ressources avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0330_2016-->