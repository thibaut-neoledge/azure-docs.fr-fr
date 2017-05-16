---
title: "Modèles Azure Resource Manager - Objets en tant que paramètres | Microsoft Docs"
description: "Explique comment étendre les fonctionnalités des modèles Azure Resource Manager afin d’utiliser des objets en tant que paramètres"
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/01/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0ab00cc3455d4bff7bfe1dfb62bafa550d65dea8
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---objects-as-parameters"></a>Modèles pour étendre les fonctionnalités des modèles Azure Resource Manager - objets en tant que paramètres

Les modèles Azure Resource Manager prennent en charge les paramètres pour spécifier des valeurs afin de personnaliser un déploiement de ressources. Bien que cette fonctionnalité soit utile et vous permette de créer des déploiements complexes, un modèle unique est limité à 255 paramètres. Si vous utilisez un paramètre pour chaque propriété d’une ressource et si vous avez un déploiement à grande échelle, le nombre de paramètres pourrait ne pas être suffisant.

## <a name="create-object-as-parameter"></a>Créer un objet en tant que paramètre

Pour résoudre ce problème, vous pouvez utiliser un objet en tant que paramètre. Le modèle consiste à spécifier un paramètre en tant qu’objet dans votre modèle, puis de fournir l’objet en tant que valeur ou tableau de valeurs. Vous faites référence à ses sous-propriétés à l’aide de la fonction `parameter()` et de l’opérateur point. Par exemple :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "VNetSettings":{"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[parameters('VNetSettings').name]",
      "location":"[resourceGroup().location]",
      "properties": {
          "addressSpace":{
              "addressPrefixes": [
                  "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
              ]
          },
          "subnets":[
              {
                  "name":"[parameters('VNetSettings').subnets[0].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
                  }
              },
              {
                  "name":"[parameters('VNetSettings').subnets[1].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
                  }
              }
          ]
        }
    }
  ],          
  "outputs": {}
}

```

Le fichier de paramètres correspondant ressemble à ceci :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "VNetSettings":{
          "value":{
              "name":"VNet1",
              "addressPrefixes": [
                  { 
                      "name": "firstPrefix",
                      "addressPrefix": "10.0.0.0/22"
                  }
              ],
              "subnets":[
                  {
                      "name": "firstSubnet",
                      "addressPrefix": "10.0.0.0/24"
                  },
                  {
                      "name":"secondSubnet",
                      "addressPrefix":"10.0.1.0/24"
                  }
              ]
          }
      }
  }
}
```

Dans cet exemple, toutes les valeurs spécifiées pour le réseau virtuel proviennent d’un seul paramètre, `VNetSettings`. Ce modèle est utile pour la gestion des valeurs de propriété, car il vous permet de conserver toutes les valeurs pour une ressource particulière dans un seul objet. Et bien que cet exemple utilise un objet en tant que paramètre, vous pouvez également utiliser un tableau d’objets en tant que paramètre. Vous faites référence aux objets à l’aide d’un index dans le tableau.

## <a name="use-object-instead-of-multiple-arrays"></a>Utiliser un objet au lieu de plusieurs tableaux

Vous avez peut-être utilisé un modèle similaire pour créer plusieurs instances d’une ressource en créant plusieurs tableaux de valeurs de propriété et en effectuant une itération dans chaque tableau pour sélectionner la valeur. Ce modèle fonctionne bien lorsque vous créez plusieurs ressources du même type, mais il peut poser problème lorsqu’il est utilisé pour créer des ressources enfants. 

Il existe deux raisons à ce problème. Premièrement, Resource Manager essaie de déployer les ressources enfants en parallèle, mais votre déploiement échoue lorsque deux ressources enfants mettent à jour le parent simultanément. 

Deuxièmement, chaque tableau de valeurs de propriété est parcouru en parallèle et si la forme de chaque tableau n’est pas la même, une erreur se produit. Considérez l’exemple de tableaux de propriétés suivant :

```json
"variables": {
    "firstProperty": [
        {
            "name": "A",
            "type": "typeA"
        },
        {
            "name": "B",
            "type": "typeB"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ],
    "secondProperty": [
        "one","two"
    ]
}
```

Le modèle par défaut pour affecter ces valeurs aux propriétés dans une boucle de copie consiste à accéder à la propriété à l’aide de la fonction `variables()` et d’utiliser `copyIndex()` en tant qu’index dans le tableau. Par exemple :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    ...
    "copy": {
        "name": "copyLoop1",
        "count": "[length(variables('firstProperty'))]"
    },
    ...
    "properties": {
        "name": { "value": "[variables('firstProperty')[copyIndex()].name]" },
        "type": { "value": "[variables('firstProperty')[copyIndex()].type]" },
        "number": { "value": "[variables('secondProperty')[copyIndex()]]" }
    }
}
```
Notez que le paramètre `count` de la boucle de copie est basé sur le nombre de propriétés dans le tableau `firstProperty`. Toutefois, il n’existe pas le même nombre de propriétés dans le tableau `secondProperty`. La validation de ce modèle échoue car la longueur du tableau `secondProperty` n’est pas la même.

Toutefois, si vous incluez toutes les propriétés dans un objet unique, il est beaucoup plus facile de voir quand une valeur est manquante. Par exemple :

```json
"variables": {
    "propertyObject": [
        {
            "name": "A",
            "type": "typeA",
            "number": "one"
        },
        {
            "name": "B",
            "type": "typeB",
            "number": "two"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ]
}
```

## <a name="use-with-sequential-copy"></a>Utiliser avec la copie séquentielle

Ce modèle devient encore plus utile lorsqu’il est associé au [modèle de copie séquentielle](resource-manager-sequential-loop.md), en particulier pour le déploiement de ressources enfants. L’exemple de modèle suivant déploie un groupe de sécurité réseau (NSG) avec deux règles de sécurité. La première ressource nommée `NSG1` déploie le groupe de sécurité réseau. Le deuxième groupe de ressources nommé `loop-0` effectue deux fonctions : tout d’abord, il `dependsOn` le groupe de sécurité réseau afin que son déploiement ne commence pas avant que `NSG1` ne soit terminé, et il s’agit de la première itération de la boucle séquentielle. La troisième ressource est un modèle imbriqué qui déploie les règles de sécurité à l’aide d’un objet pour ses valeurs de paramètre comme dans l’exemple précédent.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "networkSecurityGroupsSettings": {"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location":"[resourceGroup().location]",
      "properties": {
          "securityRules":[]
      }
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "loop-0",
        "dependsOn": [
            "NSG1"
        ],
        "properties": {
            "mode":"Incremental",
            "parameters":{},
            "template": {
                "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            }
        }       
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "[concat('loop-', copyIndex(1))]",
        "dependsOn": [
          "[concat('loop-', copyIndex())]"
        ],
        "copy": {
          "name": "iterator",
          "count": "[length(parameters('networkSecurityGroupsSettings').securityRules)]"
        },
        "properties": {
          "mode": "Incremental",
          "template": {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
           "parameters": {},
            "variables": {},
            "resources": [
                {
                    "name": "[concat('NSG1/' , parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].name)]",
                    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
                    "apiVersion": "2016-09-01",
                    "location":"[resourceGroup().location]",
                    "properties":{
                        "description": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].description]",
                        "priority":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].priority]",
                        "protocol":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].protocol]",
                        "sourcePortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourcePortRange]",
                        "destinationPortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationPortRange]",
                        "sourceAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourceAddressPrefix]",
                        "destinationAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationAddressPrefix]",
                        "access":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].access]",
                        "direction":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].direction]"
                        }
                  }
            ],
            "outputs": {}
          }
        }
    }
  ],          
  "outputs": {}
}

```

Le fichier de paramètres correspondant ressemble à ceci :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "networkSecurityGroupsSettings": {
      "value": {
          "securityRules": [
            {
              "name": "RDPAllow",
              "description": "allow RDP connections",
              "direction": "Inbound",
              "priority": 100,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.0.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "access": "Allow",
              "protocol": "Tcp"
            },
            {
              "name": "HTTPAllow",
              "description": "allow HTTP connections",
              "direction": "Inbound",
              "priority": 200,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.1.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      }
    }
  }
```

## <a name="try-the-template"></a>Essayer le modèle

Si vous souhaitez effectuer des tests avec ces modèles, procédez comme suit :

1.    Accédez au portail Azure, sélectionnez l’icône « + » puis recherchez le type de ressource « déploiement de modèle ». Lorsque cette ressource apparaît dans les résultats de la recherche, sélectionnez-la.
2.    Lorsque vous accédez à la page « déploiement de modèle », sélectionnez le bouton **créer**. Ce bouton ouvre le panneau « déploiement personnalisé ».
3.    Sélectionnez le bouton **modifier un modèle**.
4.    Supprimez le modèle vide dans le volet de droite.
5.    Copiez et collez l’exemple de modèle dans le volet de droite.
6.    Sélectionnez le bouton **enregistrer**.
7.    Lorsque vous revenez au panneau « déploiement personnalisé », sélectionnez le bouton **modifier les paramètres**.
8.  Dans le panneau « modifier les paramètres », supprimez le modèle existant.
9.  Copiez et collez l’exemple de modèle de paramètres ci-dessus.
10. Sélectionnez le bouton **enregistrer** qui vous renvoie au panneau « déploiement personnalisé ».
11. Dans le panneau « déploiement personnalisé », sélectionnez votre abonnement, créez un groupe de ressources ou utilisez un groupe de ressources existant, puis sélectionnez un emplacement. Passez en revue les Conditions générales, puis cochez la case « J’accepte ».
12.    Sélectionnez le bouton **acheter**.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin de plus de 255 paramètres, qui est le maximum autorisé par déploiement, utilisez ce modèle pour spécifier moins de paramètres dans votre modèle. Vous pouvez également utiliser ce modèle pour gérer les propriétés de vos ressources plus facilement, puis les déployer sans conflit à l’aide du modèle de boucle séquentielle.

* Pour une introduction à la fonction `parameter()` et à l’utilisation des tableaux, consultez [Fonctions du modèle Azure Resource Manager](resource-group-template-functions.md).
* Ce modèle est également implémenté dans le [modèle de projet de blocs de construction](https://github.com/mspnp/template-building-blocks) et les [architectures de référence Azure](/azure/architecture/reference-architectures/).
