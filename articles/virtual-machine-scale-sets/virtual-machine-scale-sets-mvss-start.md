---
title: "Jeux de mise à l’échelle de machine virtuelle Azure : taille minimale de jeu de mise à l’échelle viable | Microsoft Docs"
description: "Apprenez à créer un modèle de jeu de mise à l’échelle viable minimal"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/14/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: eee63d76941f327304348262f00afafe8413cb6b
ms.lasthandoff: 03/09/2017


---

# <a name="about-this-tutorial"></a>À propos de ce didacticiel

Les [modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) constituent un excellent moyen de déployer des groupes de ressources liées. Cette série de didacticiels montre comment créer un modèle de jeu de mise à l’échelle viable minimal et comment modifier ce modèle pour l’adapter à différents scénarios. Tous les exemples proviennent de ce [référentiel github](https://github.com/gatneil/mvss). Chaque différentiel présenté dans cette procédure pas à pas est le résultat de l’exécution d’une `git diff` entre les branches dans ce référentiel. Ces modèles et différentiels sont destinés à être des exemples simples, non exhaustifs. Pour des exemples plus complets de modèles de jeux de mise à l’échelle, consultez le [dépôt Github de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates) et recherchez les dossiers qui contiennent la chaîne `vmss`.

## <a name="a-minimum-viable-scale-set"></a>Un jeu de mise à l’échelle viable minimal

Notre modèle de jeu de mise à l’échelle viable minimal peut être consulté [ici](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json). Si vous êtes déjà familiarisé avec les modèles, vous pouvez ignorer la section « Étapes suivantes » pour savoir comment modifier ce modèle pour d’autres scénarios. Toutefois, si vous êtes moins familier avec les modèles, vous trouverez cette description pas à pas utile. Pour commencer, examinons le différentiel pour créer ce modèle (`git diff master minimum-viable-scale-set`), élément par élément :

Tout d’abord, nous définissons le `$schema` et la `contentVersion` du modèle. `$schema` définit la version de la langue du modèle et est utilisé pour le surlignage de syntaxe et d’autres fonctionnalités de validation similaires dans Visual Studio. `contentVersion` n’est en fait pas utilisé par Azure du tout. Cette valeur vous aide simplement à effectuer le suivi de la version du modèle en question.

```diff
+{
+  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
+  "contentVersion": "1.0.0.0",
```

Ensuite, nous définissons deux paramètres, `adminUsername` et `adminPassword`. Ces paramètres sont des valeurs spécifiées par l’utilisateur au moment du déploiement. `adminUsername` est simplement de type `string`, mais étant donné que `adminPassword` est un secret, nous lui donnons un type `securestring`. Nous verrons plus loin que ces paramètres sont passés dans la configuration du jeu de mise à l’échelle.

```diff
+  "parameters": {
+    "adminUsername": {
+      "type": "string"
+    },
+    "adminPassword": {
+      "type": "securestring"
+    }
+  },
```

Les modèles Resource Manager vous permettent également de définir les variables à utiliser par la suite dans le modèle. Dans cet exemple, nous n’utilisons aucune variable, nous avons donc laissé l’objet JSON vide.

```diff
+  "variables": {},
```

Nous avons ensuite les ressources du modèle, où nous définissons ce que nous voulons réellement déployer. Contrairement à `parameters` et `variables` (qui sont des objets JSON), `resources` est une liste JSON d’objets JSON.

```diff
+  "resources": [
```

Toutes les ressources nécessitent un `type`, un `name`, une `apiVersion` et un `location`. Notre première ressource est de type `Microsft.Network/virtualNetwork` avec le nom `myVnet` et la valeur d’apiVersion `2016-03-30`. Pour déterminer la dernière version de l’api pour un type de ressource, reportez-vous à la [Documentation de l’API REST Azure](https://docs.microsoft.com/rest/api/).

```diff
+    {
+      "type": "Microsoft.Network/virtualNetworks",
+      "name": "myVnet",
+      "apiVersion": "2016-12-01",
```

Pour spécifier l’emplacement du réseau virtuel, nous utilisons une [Fonction de modèle Resource Manager](../azure-resource-manager/resource-group-template-functions.md), qui doit être mis entre guillemets et les crochets comme suit : `"[<template-function>]"`. Dans ce cas, nous utilisons la fonction resourceGroup, qui ne prend aucun argument et renvoie un objet JSON avec des métadonnées concernant le groupe de ressources cible de ce déploiement. Le groupe de ressources est défini par l’utilisateur au moment du déploiement. Nous l’indexons ensuite dans cet objet JSON avec `.location` pour obtenir l’emplacement de l’objet JSON.

```diff
+      "location": "[resourceGroup().location]",
```


Chaque ressource de Resource Manager a sa propre section `properties` pour les configurations spécifiques à la ressource. Dans ce cas, nous spécifions que le réseau virtuel doit avoir un sous-réseau qui utilise la plage d’adresses IP privées `10.0.0.0/16`. Un jeu de mise à l’échelle est toujours contenu dans un sous-réseau. Il ne peut pas s’étendre sur plusieurs sous-réseaux.

```diff
+      "properties": {
+        "addressSpace": {
+          "addressPrefixes": [
+            "10.0.0.0/16"
+          ]
+        },
+        "subnets": [
+          {
+            "name": "mySubnet",
+            "properties": {
+              "addressPrefix": "10.0.0.0/16"
+            }
+          }
+        ]
+      }
+    },
```

Outre les propriétés `type`, `name`, `apiVersion` et `location`, chaque ressource peut éventuellement inclure une liste de chaînes `dependsOn` qui spécifie ce que les autres ressources de ce déploiement doivent terminer avant leur déploiement. Dans ce cas, il n’y a qu’un seul élément dans cette liste, le réseau virtuel vu plus haut. Nous spécifions cette dépendance, car le jeu de mise à l’échelle a besoin que le réseau existe avant la création de machines virtuelles. De cette façon, le jeu de mise à l’échelle peut donner des adresses IP privées à ces machines virtuelles à partir de la plage d’adresses IP spécifiée précédemment dans les propriétés du réseau. Le format de chaque chaîne dans la liste dependsOn est `<type>/<name>` (les mêmes valeurs de `type` et `name` que nous avons utilisées précédemment dans la définition de ressource de réseau virtuel).

```diff
+    {
+      "type": "Microsoft.Compute/virtualMachineScaleSets",
+      "name": "myScaleSet",
+      "apiVersion": "2016-04-30-preview",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Network/virtualNetworks/myVnet"
+      ],
```

Le jeu de mise à l’échelle doit connaître la taille de machine virtuelle à créer (le « nom de la référence (SKU) ») et le nombre de ces machines virtuelles à créer (la « capacité de la référence (SKU) »). Pour voir les tailles de machines virtuelles disponibles, reportez-vous à la [Documentation sur les tailles de machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```diff
+      "sku": {
+        "name": "Standard_A1",
+        "capacity": 2
+      },
```

Le jeu de mise à l’échelle doit également savoir comment gérer les mises à jour sur l’ensemble du jeu de mise à l’échelle. Il existe actuellement deux options, `Manual` et `Automatic`. Pour plus d’informations sur les différences entre les deux, consultez la documentation sur la [Mise à niveau d’un jeu de mise à l’échelle](./virtual-machine-scale-sets-upgrade-scale-set.md).

```diff
+      "properties": {
+        "upgradePolicy": {
+          "mode": "Manual"
+        },
```

Le jeu de mise à l’échelle doit également savoir quel système d’exploitation mettre sur les machines virtuelles. Ici, nous créons des machines virtuelles avec une image Ubuntu 16.04-LTS entièrement corrigée.

```diff
+        "virtualMachineProfile": {
+          "storageProfile": {
+            "imageReference": {
+              "publisher": "Canonical",
+              "offer": "UbuntuServer",
+              "sku": "16.04-LTS",
+              "version": "latest"
+            }
+          },
```

Étant donné que le jeu de mise à l’échelle déploie plusieurs machines virtuelles, nous spécifions un `computerNamePrefix` plutôt que de spécifier chaque nom de machine virtuelle. Le jeu de mise à l’échelle ajoute un index à préfixe pour chaque machine virtuelle, les noms d’ordinateurs virtuels se présentent donc sous la forme `<computerNamePrefix>_<auto-generated-index>`. Dans cet extrait de code, nous utilisons également les paramètres précédents pour définir le nom d’utilisateur de l’administrateur et son mot de passe pour toutes les machines virtuelles dans le jeu de mise à l’échelle. Nous faisons cela avec la fonction de modèle `parameters`, qui prend une chaîne spécifiant le paramètre auquel nous souhaitons nous référer et renvoie la valeur pour ce paramètre.

```diff
+          "osProfile": {
+            "computerNamePrefix": "vm",
+            "adminUsername": "[parameters('adminUsername')]",
+            "adminPassword": "[parameters('adminPassword')]"
+          },
```

Enfin, nous devons spécifier la configuration du réseau pour les machines virtuelles dans le jeu de mise à l’échelle. Dans ce cas, il nous suffit spécifier l’ID du sous-réseau créé précédemment pour que le jeu de mise à l’échelle sache qu’il doit placer les interfaces réseau dans ce sous-réseau. Nous pouvons obtenir l’ID du réseau virtuel qui contient le sous-réseau à l’aide de la fonction de modèle `resourceId`. Cette fonction prend le type et le nom d’une ressource et renvoie l’identificateur complet de la ressource (cet identificateur est au format : `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`). Toutefois, l’identificateur du réseau virtuel n’est pas suffisant. Nous devons spécifier le sous-réseau spécifique dans lequel les machines virtuelles du jeu de mise à l’échelle doivent se trouver, nous ajoutons donc `/subnets/mySubnet` à l’ID du réseau virtuel. Le résultat est l’ID complet du sous-réseau. Nous effectuons cette concaténation avec la fonction `concat`, qui prend une série de chaînes et renvoie leur concaténation.

```diff
+          "networkProfile": {
+            "networkInterfaceConfigurations": [
+              {
+                "name": "myNic",
+                "properties": {
+                  "primary": "true",
+                  "ipConfigurations": [
+                    {
+                      "name": "myIpConfig",
+                      "properties": {
+                        "subnet": {
+                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                        }
+                      }
+                    }
+                  ]
+                }
+              }
+            ]
+          }
+        }
+      }
+    }
+  ]
+}

```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
