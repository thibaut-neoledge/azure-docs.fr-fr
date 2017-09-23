---
title: "Référencez une image personnalisée dans un modèle de groupe identique Azure | Microsoft Docs"
description: "Découvrez comment ajouter une image personnalisée à un modèle de groupe de machines virtuelles identiques Azure existant"
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
ms.date: 5/10/2017
ms.author: negat
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: cf52fc9e95267c4bc5c0106aadf626685ddd5c24
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017

---

# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Ajout d’une image personnalisée à un modèle de groupe identique Azure

Cet article montre comment modifier le [modèle de groupe identique viable minimal](./virtual-machine-scale-sets-mvss-start.md) afin de déployer avec une image personnalisée.

## <a name="change-the-template-definition"></a>Modifier la définition du modèle

Notre modèle de groupe identique viable minimal peut être consulté [ici](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json) et notre modèle de déploiement de groupe identique à partir d’une image personnalisée peut être consulté [ici](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Examinons le différentiel utilisé pour créer ce modèle (`git diff minimum-viable-scale-set custom-image`), élément par élément :

### <a name="creating-a-managed-disk-image"></a>Création d’une image de disque géré

Si vous disposez déjà d’une image disque gérée personnalisée (une ressource de type `Microsoft.Compute/images`), vous pouvez ignorer cette section.

Tout d’abord, nous ajoutons un paramètre `sourceImageVhdUri`, qui est l’URI vers l’objet blob généralisé dans le stockage Azure qui contient l’image personnalisée à partir de laquelle le déploiement est effectué.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Ensuite, nous ajoutons une ressource de type `Microsoft.Compute/images`, qui est l’image de disque géré basée sur le blob généralisé situé à l’URI `sourceImageVhdUri`. Cette image doit se trouver dans la même région que le groupe identique qui l’utilise. Dans les propriétés de l’image, nous spécifions le type de système d’exploitation, l’emplacement de l’objet blob (à partir du paramètre `sourceImageVhdUri`) et le type de compte de stockage :

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

Dans la ressource groupe identique, nous ajoutons une clause `dependsOn` faisant référence à l’image personnalisée pour nous assurer que l’image est créée avant que le groupe identique tente de se déployer à partir de cette image :

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Modification des propriétés du groupe identique pour utiliser l’image de disque géré

Dans la `imageReference` du groupe identique `storageProfile`, au lieu de spécifier l’éditeur, l’offre, la référence (SKU) et la version d’une image de plateforme, nous spécifions la valeur `id` de la ressource `Microsoft.Compute/images` :

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Dans cet exemple, nous utilisons la fonction `resourceId` pour obtenir l’ID de ressource de l’image créée dans le même modèle. Si vous avez créé l’image de disque géré au préalable, vous devez fournir l’ID de cette image à la place. Cet ID doit être au format : `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

