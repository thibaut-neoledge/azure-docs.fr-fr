---
title: "Convertir un modèle de jeu de mise à l’échelle Azure Resource Manager pour utiliser un disque géré | Microsoft Docs"
description: "Convertissez un modèle de groupe identique pour utiliser modèle de jeu de mise à l’échelle de disque géré."
keywords: "Jeux de mise à l’échelle de machine virtuelle"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: negat
ms.openlocfilehash: 2f5cb85703888c5056611d466f508547ee72e44b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Convertir un modèle de groupe identique pour utiliser modèle de jeu de mise à l’échelle de disque géré

Les clients utilisant un modèle Resource Manager pour créer un jeu de mise à l’échelle sans utiliser de disque géré devront le modifier pour utiliser un disque géré. Cet article explique comment procéder, avec l’exemple d’une requête d’extraction à partir des [Modèles de démarrage rapide Microsoft Azure](https://github.com/Azure/azure-quickstart-templates), un référentiel communautaire contenant des exemples de modèles Resource Manager. Voici la requête d’extraction complète : [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), et les parties pertinentes de la comparaison, ainsi que des explications :

## <a name="making-the-os-disks-managed"></a>Passer les disques de système d’exploitation en mode géré

Dans la comparaison ci-dessous, nous pouvons voir que nous avons supprimé plusieurs variables liées aux propriétés de disque et au compte de stockage. Le type de compte de stockage n’est plus nécessaire (Standard_LRS est la valeur par défaut), mais nous pouvons toujours le spécifier si nous le souhaitons. Seuls Standard_LRS et Premium_LRS sont pris en charge pour les disques gérés. Un nouveau suffixe de compte de stockage, un tableau de chaînes uniques et le nombre d’associations de sécurité étaient utilisés dans l’ancien modèle pour générer des noms de compte de stockage. Ces variables ne sont plus nécessaires dans le nouveau modèle, car le disque géré crée automatiquement des comptes de stockage au nom du client. De même, le nom de conteneur de disque dur virtuel et le nom du disque du système d’exploitation ne sont plus nécessaires, car le disque géré nomme automatiquement les conteneurs de stockage d’objets blob et les disques sous-jacents.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


Dans la comparaison ci-dessous, nous pouvons voir que nous avons mis à jour le calcul version de l’API vers 2016-04-30-preview, qui est la version minimale requise pour la prise en charge des disques gérés avec des jeux de mise à l’échelle. Notez que nous pourrions toujours utiliser des disques non gérés dans la nouvelle version de l’API avec l’ancienne syntaxe si nous le souhaitions. En d’autres termes, si nous mettons seulement à jour la version d’API de calcul et ne modifions rien d’autre, le modèle devrait continuer à fonctionner comme avant.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

Dans la comparaison ci-dessous, nous pouvons voir que nous supprimons complètement la ressource de compte de stockage à partir du tableau ressources. Nous n’en avons plus besoin, car le disque géré les crée automatiquement en notre nom.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

Dans la comparaison ci-dessous, nous pouvons voir que ce que nous supprimons dépend de la clause faisant référence, depuis le jeu de mise à l’échelle, à la boucle qui créait des comptes de stockage. Dans l’ancien modèle, cela garantissait que les comptes de stockage étaient créés avant le début de la création du jeu de mise à l’échelle, mais cette clause n’est plus nécessaire avec un disque géré. Nous avons également supprimé la propriété de conteneurs de disque dur virtuel, et la propriété de nom du disque du système d’exploitation, car celles-ci sont gérées automatiquement par le disque géré en arrière-plan. Si nous le souhaitions, nous pourrions ajouter `"managedDisk": { "storageAccountType": "Premium_LRS" }` dans la configuration « osDisk » si nous voulions des disques de système d’exploitation premium. Seules les machines virtuelles avec une un « s » minuscule ou majuscule dans la référence de machine virtuelle (SKU) peuvent utiliser des disques premium.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Il n’existe aucune propriété explicite dans la configuration du jeu de mise à l’échelle pour indiquer s’il faut utiliser un disque géré ou non géré. Le jeu de mise à l’échelle sait lequel utiliser sur la base des propriétés sont présentes dans le profil de stockage. Par conséquent, il est important, lorsque vous modifiez le modèle, de garantir que les bonnes propriétés sont dans le profil de stockage du jeu de mise à l’échelle.


## <a name="data-disks"></a>Disques de données

Avec les modifications ci-dessus, le jeu de mise à l’échelle utilise des disques gérés pour le disque de système d’exploitation, mais qu’en est-il des disques de données ? Pour ajouter des disques de données, ajoutez la propriété « dataDisks » sous « storageProfile » au même niveau que « osDisk ». La valeur de la propriété est une liste JSON d’objets, chacun d'entre eux possédant les propriétés « lun » (qui doit être unique pour chaque disque de données sur une machine virtuelle), « createOption » (« empty » est actuellement la seule option prise en charge) et « diskSizeGB » (la taille du disque en gigaoctets ; doit être supérieure à 0 et inférieure à 1024) comme dans l’exemple suivant : 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Si vous spécifiez `n` disques dans ce tableau, chaque machine virtuelle dans le jeu de mise à l’échelle obtient `n` disques de données. Notez, cependant, que ces disques de données sont des appareils bruts. Ils ne sont pas formatés. Il incombe au client de joindre, partitioner et formater les disques avant de les utiliser. Nous pouvons également spécifier `"managedDisk": { "storageAccountType": "Premium_LRS" }` dans chaque objet de disque de données pour indiquer qu’il doit être un disque de données premium. Seules les machines virtuelles avec une un « s » minuscule ou majuscule dans la référence de machine virtuelle (SKU) peuvent utiliser des disques premium.

Pour en savoir plus sur l’utilisation de disques de données avec des jeux de mise à l’échelle, consultez [cet article](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Étapes suivantes
Pour les modèles Resource Manager utilisant des jeux de mise à l’échelle, recherchez « vmss » dans le [dépôt Github de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).

Pour plus d’informations, consultez la [page d’accueil principale pour les jeux de mise à l’échelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

