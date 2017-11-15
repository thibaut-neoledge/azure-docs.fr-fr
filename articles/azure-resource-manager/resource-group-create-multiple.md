---
title: "Déploiement de plusieurs instances de ressources Azure | Microsoft Docs"
description: "Utilisez l’opération de copie et les tableaux dans un modèle Azure Resource Manager pour effectuer une itération à plusieurs reprises lors du déploiement de ressources."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 8e6d68612be4b7d4e1d6cea13e0f29636931abd8
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2017
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Déployer plusieurs instances d’une ressource ou d’une propriété dans des modèles Azure Resource Manager
Cette rubrique montre comment procéder à une itération dans votre modèle Azure Resource Manager pour créer plusieurs instances d’une ressource ou d’une propriété sur une ressource.

Si vous devez ajouter une logique à votre modèle, qui vous permette de spécifier si une ressource est déployée, voir [Déployer une ressource de manière conditionnelle](#conditionally-deploy-resource).

Pour obtenir un exemple de création de plusieurs éléments dans une variable de tableau, consultez [Variables](resource-group-authoring-templates.md#variables).

## <a name="resource-iteration"></a>Itération de ressource
Pour créer plusieurs instances d’un type de ressource, ajoutez un élément `copy` au type de ressource. Dans l’élément copy, vous indiquez le nombre d’itérations et un nom pour cette boucle. La valeur de décompte doit être un entier positif et ne pas dépasser 800. Resource Manager crée les ressources en parallèle. Par conséquent, l’ordre de création n’est pas garanti. Pour créer des ressources itérées en séquence, consultez [Copie en série](#serial-copy). 

La ressource à créer plusieurs fois prend le format suivant :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Notez que le nom de chaque ressource inclut la fonction `copyIndex()`, qui renvoie l’itération actuelle de la boucle. `copyIndex()` est basé sur zéro. Si bien que l’exemple suivant :

```json
"name": "[concat('storage', copyIndex())]",
```

Crée les noms suivants :

* storage0
* storage1
* storage2.

Pour décaler la valeur d’index, vous pouvez transmettre une valeur dans la fonction copyIndex(). Le nombre d’itérations à effectuer est toujours spécifié dans l’élément copy, mais la valeur de copyIndex est décalée en fonction de la valeur spécifiée. Si bien que l’exemple suivant :

```json
"name": "[concat('storage', copyIndex(1))]",
```

Crée les noms suivants :

* storage1
* storage2
* storage3

L’opération copy se révèle utile lorsque vous travaillez avec des tableaux, car vous pouvez itérer sur chaque élément du tableau. Utilisez la fonction `length` sur le tableau pour spécifier le nombre d’itérations, et `copyIndex` pour récupérer l’index actuel dans le tableau. Si bien que l’exemple suivant :

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Crée les noms suivants :

* storagecontoso
* storagefabrikam
* storagecoho

## <a name="serial-copy"></a>Copie en série

Lorsque vous utilisez l’élément de copie pour créer plusieurs instances d’un type de ressource, Resource Manager déploie par défaut ces instances en parallèle. Toutefois, vous souhaiterez peut-être spécifier que les ressources soient déployées en séquence. Par exemple, lors de la mise à jour d’un environnement de production, vous souhaiterez échelonner les mises à jour afin que seulement un certain nombre soient mises à jour à un moment donné.

Resource Manager fournit des propriétés sur l’élément de copie qui vous permettent de déployer en série plusieurs instances. Dans l’élément de copie, définissez `mode` sur **serial** et `batchSize` sur le nombre d’instances à déployer en même temps. Avec le mode série, Resource Manager crée une dépendance sur les instances précédentes de la boucle, afin de ne pas démarrer un lot tant que le précédent n’est pas terminé.

```json
"copy": {
    "name": "iterator",
    "count": "[parameters('numberToDeploy')]",
    "mode": "serial",
    "batchSize": 2
},
```

La propriété mode accepte également **parallel**, qui est la valeur par défaut.

Pour tester la copie en série sans créer de ressources réelles, utilisez le modèle suivant qui déploie des modèles imbriqués vides :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex())]",
      "copy": {
        "name": "iterator",
        "count": "[parameters('numberToDeploy')]",
        "mode": "serial",
        "batchSize": 1
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Dans l’historique de déploiement, vous remarquez que les déploiements imbriqués sont traités en séquence.

![déploiement en série](./media/resource-group-create-multiple/serial-copy.png)

Pour un scénario plus réaliste, l’exemple suivant déploie deux instances à la fois d’une machine virtuelle Linux à partir d’un modèle imbriqué :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "User name for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "dnsLabelPrefix": {
            "type": "string",
            "metadata": {
                "description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "ubuntuOSVersion": {
            "type": "string",
            "defaultValue": "16.04.0-LTS",
            "allowedValues": [
                "12.04.5-LTS",
                "14.04.5-LTS",
                "15.10",
                "16.04.0-LTS"
            ],
            "metadata": {
                "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version."
            }
        }
    },
    "variables": {
        "templatelink": "https://raw.githubusercontent.com/rjmax/Build2017/master/Act1.TemplateEnhancements/Chapter03.LinuxVM.json"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "[concat('nestedDeployment',copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "copy": {
                "name": "myCopySet",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            },
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "adminUsername": {
                        "value": "[parameters('adminUsername')]"
                    },
                    "adminPassword": {
                        "value": "[parameters('adminPassword')]"
                    },
                    "dnsLabelPrefix": {
                        "value": "[parameters('dnsLabelPrefix')]"
                    },
                    "ubuntuOSVersion": {
                        "value": "[parameters('ubuntuOSVersion')]"
                    },
                    "index":{
                        "value": "[copyIndex()]"
                    }
                }
            }
        }
    ]
}
```

## <a name="property-iteration"></a>Itération de propriété

Pour créer des valeurs multiples pour une propriété sur une ressource, ajoutez un tableau `copy` dans l’élément Propriétés. Ce tableau contient des objets possédant tous les propriétés suivantes :

* name : nom de la propriété pour laquelle créer plusieurs valeurs
* count : nombre de valeurs à créer
* input : objet contenant les valeurs à assigner à la propriété  

L’exemple suivant montre comment appliquer `copy` à la propriété dataDisks sur une machine virtuelle :

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
          "name": "dataDisks",
          "count": 3,
          "input": {
              "lun": "[copyIndex('dataDisks')]",
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Notez que, lorsque vous utilisez `copyIndex` à l’intérieur d’une itération de propriété, vous devez fournir le nom de l’itération. Il est inutile de fournir le nom quand l’itération de propriété est utilisé avec une itération de ressource.

Le Gestionnaire des ressources développe le tableau `copy` durant le déploiement. Le nom du tableau devient celui de la propriété. Les valeurs d’entrée deviennent les propriétés de l’objet. Le modèle déployé devient :

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
          {
              "lun": 0,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 1,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 2,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Vous pouvez utiliser des itérations de ressource et de propriété ensemble. Référencez l’itération de propriété par son nom.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2016-06-01",
    "copy":{
        "count": 2,
        "name": "vnetloop"
    },
    "location": "[resourceGroup().location]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "copy": [
            {
                "name": "subnets",
                "count": 2,
                "input": {
                    "name": "[concat('subnet-', copyIndex('subnets'))]",
                    "properties": {
                        "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
                    }
                }
            }
        ]
    }
}
```

Vous ne pouvez inclure qu’un seul élément de copie dans les propriétés de chaque ressource. Pour spécifier une boucle d’itération pour plusieurs propriétés, définissez plusieurs objets dans le tableau de copie. Chaque objet est itéré séparément. Par exemple, pour créer plusieurs instances des propriétés `frontendIPConfigurations` et `loadBalancingRules` sur un équilibreur de charge, définissez les deux objets dans un élément de copie unique : 

```json
{
    "name": "[variables('loadBalancerName')]",
    "type": "Microsoft.Network/loadBalancers",
    "properties": {
        "copy": [
          {
              "name": "frontendIPConfigurations",
              "count": 2,
              "input": {
                  "name": "[concat('loadBalancerFrontEnd', copyIndex('frontendIPConfigurations', 1))]",
                  "properties": {
                      "publicIPAddress": {
                          "id": "[variables(concat('publicIPAddressID', copyIndex('frontendIPConfigurations', 1)))]"
                      }
                  }
              }
          },
          {
              "name": "loadBalancingRules",
              "count": 2,
              "input": {
                  "name": "[concat('LBRuleForVIP', copyIndex('loadBalancingRules', 1))]",
                  "properties": {
                      "frontendIPConfiguration": {
                          "id": "[variables(concat('frontEndIPConfigID', copyIndex('loadBalancingRules', 1)))]"
                      },
                      "backendAddressPool": {
                          "id": "[variables('lbBackendPoolID')]"
                      },
                      "protocol": "tcp",
                      "frontendPort": "[variables(concat('frontEndPort' copyIndex('loadBalancingRules', 1))]",
                      "backendPort": "[variables(concat('backEndPort' copyIndex('loadBalancingRules', 1))]",
                      "probe": {
                          "id": "[variables('lbProbeID')]"
                      }
                  }
              }
          }
        ],
        ...
    }
}
```

## <a name="depend-on-resources-in-a-loop"></a>En fonction des ressources dans une boucle
Vous spécifiez qu’une ressource est déployée après une autre ressource à l’aide de l’élément `dependsOn`. Pour déployer une ressource qui dépend de la collection de ressources dans une boucle, vous pouvez utiliser le nom de la boucle de copie dans l’élément dependsOn. L’exemple suivant montre comment déployer trois comptes de stockage avant de déployer la machine virtuelle. La définition complète de la machine virtuelle n’est pas affichée. Notez que le nom de l’élément de copie a la valeur `storagecopy` et que l’élément dependsOn pour la machine virtuelle est également défini sur `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>Création de plusieurs instances d’une ressource enfant
Vous ne pouvez pas utiliser une boucle de copie pour une ressource enfant. Pour créer plusieurs instances d’une ressource que vous définissez généralement comme imbriquée dans une autre ressource, vous devez plutôt créer cette ressource comme une ressource de niveau supérieur. Vous définissez la relation avec la ressource parente par le biais des propriétés type et name.

Par exemple, supposons que vous définissez généralement un jeu de données comme une ressource enfant dans une fabrique de données.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Pour créer plusieurs instances de jeux de données, vous devez le déplacer en dehors de la fabrique de données. Le jeu de données doit être au même niveau que la fabrique de données, mais il est toujours une ressource enfant de la fabrique de données. Vous conservez la relation entre le jeu de données et la fabrique de données par le biais des propriétés type et name. Étant donné que le type ne peut plus peut être déduit à partir de sa position dans le modèle, vous devez fournir le type qualifié complet au format : `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Pour établir une relation parent/enfant avec une instance de la fabrique de données, fournissez un nom pour le jeu de données incluant le nom de la ressource parente. Utilisez le format : `{parent-resource-name}/{child-resource-name}`.  

L’exemple ci-après illustre l’implémentation :

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="conditionally-deploy-resource"></a>Déployer une ressource de manière conditionnelle

Pour spécifier si une ressource est déployée, utilisez l’élément `condition`. La valeur de cet élément est résolue en true ou false. Lorsque la valeur est true, la ressource est déployée. Lorsque la valeur est false, la ressource n’est pas déployée. Par exemple, pour spécifier si un nouveau compte de stockage est déployé ou si un compte de stockage existant est utilisé, utilisez :

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Pour obtenir un exemple d’utilisation d’une ressource nouvelle ou existante, voir [Modèle de condition New ou Existing](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResources.NewOrExisting.json).

Pour obtenir un exemple d’utilisation d’un mot de passe ou d’une clé SSH pour déployer une machine virtuelle, voir [Modèle de condition Username ou SSH](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour savoir comment déployer votre modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

