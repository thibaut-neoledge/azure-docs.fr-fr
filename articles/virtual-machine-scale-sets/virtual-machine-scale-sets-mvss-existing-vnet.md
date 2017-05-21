---
title: "Référencer un réseau virtuel dans un modèle de groupe identique Azure | Microsoft Docs"
description: "Découvrez comment ajouter un réseau virtuel à un modèle de groupe de machines virtuelles identiques Azure existant"
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
ms.date: 3/06/2017
ms.author: negat
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 8e9caf7eebc17682b3204004e3a74331efbd04fb
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017

---

# <a name="add-reference-to-a-virtual-network-to-an-azure-scale-set-template"></a>Ajouter une référence à un réseau virtuel dans un modèle de groupe identique Azure

Cet article explique comment modifier le [modèle de groupe identique viable minimal](./virtual-machine-scale-sets-mvss-start.md) pour un déploiement dans un réseau virtuel existant au lieu d’en créer un.

## <a name="change-the-template-definition"></a>Modifier la définition du modèle

Notre modèle de groupe identique viable minimal peut être consulté [ici](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json) et notre modèle de déploiement de groupe identique sur un réseau virtuel existant peut être consulté [ici](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Examinons le différentiel utilisé pour créer ce modèle (`git diff minimum-viable-scale-set existing-vnet`), élément par élément :

Tout d’abord, nous ajoutons un paramètre `subnetId`. Cette chaîne est transférée dans la configuration du groupe identique, ce qui permet au groupe identique d’identifier le sous-réseau précréé pour y déployer des machines virtuelles. Cette chaîne doit être au format : `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Par exemple, pour déployer le groupe identique sur un réseau virtuel existant avec le nom `myvnet`, le sous-réseau `mysubnet`, le groupe de ressources `myrg` et l’abonnement `00000000-0000-0000-0000-000000000000`, l’ID du sous-réseau serait : `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Ensuite, nous pouvons supprimer la ressource de réseau virtuel à partir du tableau `resources`, dans la mesure où nous utilisons un réseau virtuel existant et n’avons pas besoin d’en déployer un nouveau.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2016-12-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

Le réseau virtuel existe déjà avant que le modèle ne soit déployé, il est donc inutile de spécifier une clause dependsOn du groupe identique vers le réseau virtuel. Par conséquent, nous supprimons ces lignes :

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Enfin, nous transmettons le paramètre `subnetId` défini par l’utilisateur (au lieu d’utiliser `resourceId` pour obtenir l’ID d’un réseau virtuel dans le même déploiement, ce que fait le modèle de groupe identique viable minimal).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

