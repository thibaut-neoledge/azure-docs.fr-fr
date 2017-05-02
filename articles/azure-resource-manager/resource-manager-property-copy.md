---
title: "Plusieurs copies d’une propriété de ressource Azure | Microsoft Docs"
description: "Dans le modèle Azure Resource Manager, créez plusieurs instances d’une propriété pour un même type de ressource. Vous devez utiliser la fonction take au lieu de la boucle de copie pour ce scénario."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: da79ff58e892bde5a35456992105de9bdbe36364
ms.lasthandoff: 04/18/2017


---
# <a name="create-multiple-instances-of-property-on-resource-type"></a>Créer plusieurs instances d’une propriété pour un même type de ressource
Vous pouvez uniquement utiliser l’opération [copy](resource-group-create-multiple.md) sur les types de ressources, et non sur les propriétés d’un type de ressource. Cette exigence peut poser des problèmes lorsque vous souhaitez créer plusieurs instances d’un élément faisant partie d’une ressource. Il arrive souvent qu’un utilisateur souhaite créer plusieurs disques de données pour une machine virtuelle. Vous ne pouvez pas utiliser copy avec les disques de données, car dataDisks est une propriété sur la machine virtuelle et ne constitue pas son propre type de ressource. Au lieu de cela, vous créez un tableau avec autant de disques de données que nécessaire et vous transmettez le nombre réel de disques de données à créer. Dans la définition de la machine virtuelle, vous utilisez la fonction `take` pour obtenir uniquement le nombre d’éléments que vous souhaitez réellement obtenir à partir du tableau.

Un exemple complet de ce modèle est illustré dans le modèle de [création d’une machine virtuelle avec une sélection de disques de données dynamique](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

Les sections pertinentes du modèle de déploiement sont présentées dans cet article. Certaines parties des modèles ont été supprimées pour mettre l’accent sur les sections impliquées dans la création dynamique de plusieurs disques de données. 

## <a name="define-template-with-variable-for-the-property"></a>Définir un modèle avec une variable en guise de propriété

Pour créer une machine virtuelle avec plusieurs disques de données, ajoutez une variable de tableau qui définit plus de disques de données que ce dont vous avez besoin. Dans le paramètre `numDataDisks`, saisissez le nombre réel de disques de données à créer. Utilisez `take` pour spécifier le nombre exact d’éléments dans le tableau à attribuer à la machine virtuelle.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ... /* not all elements shown */
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        "storageProfile": {
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

## <a name="create-multiple-virtual-machines-with-multiple-data-disks"></a>Créer plusieurs machines virtuelles avec plusieurs disques de données

Vous pouvez utiliser la fonction **take** et l’élément **copy** ensemble lorsque vous avez besoin de créer plusieurs instances d’une ressource avec un nombre variable d’éléments pour une propriété. Par exemple, supposons que vous devez créer plusieurs machines virtuelles, mais que chaque machine virtuelle possède un nombre différent de disques de données. 

Pour donner à chaque disque de données un nom qui identifie la machine virtuelle associée, placez votre baie de disques de données dans un modèle séparé. Incluez les paramètres pour le nom de la machine virtuelle et le nombre de disques de données à retourner. Dans la section des sorties, retournez le nombre d’éléments spécifiés.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

Dans le modèle parent, vous incluez des paramètres pour le nombre de machines virtuelles et un tableau pour le nombre de disques de données pour chaque machine virtuelle.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

Dans la section de ressources, déployez plusieurs instances du modèle qui définit les disques de données. 

```json
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

Dans la section de ressources, déployez plusieurs instances de la machine virtuelle. Pour les disques de données, référencez le déploiement imbriqué qui contient le nombre correct de disques de données et les noms corrects des disques de données.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour une introduction à la création de plusieurs instances d’une ressource, consultez [Déployer plusieurs instances de ressources dans des modèles Azure Resource Manager](resource-group-create-multiple.md).
* Pour créer des ressources itérées dans une séquence, consultez [Boucle séquentielle pour modèles Azure Resource Manager](resource-manager-sequential-loop.md).
* Pour savoir comment déployer votre modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).


