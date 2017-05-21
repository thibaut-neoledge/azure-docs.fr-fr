---
title: "Élément d’interface utilisateur StorageAccountSelector des applications gérées Azure | Microsoft Docs"
description: "Décrit l’élément d’interface utilisateur Microsoft.Storage.StorageAccountSelector pour les applications gérées Azure"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2d64cfcd958f17171e9ef1a38777f202bdfda774
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Élément d’interface utilisateur Microsoft.Storage.StorageAccountSelector
Contrôle permettant de sélectionner un compte de stockage nouveau ou existant.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- S’il est spécifié, le caractère unique de `defaultValue.name` est automatiquement validé. Si le nom de compte de stockage n’est pas unique, l’utilisateur doit indiquer un autre nom ou choisir un compte de stockage existant.
- La valeur par défaut pour `defaultValue.type` est **Premium_LRS**.
- Tout type non spécifié dans `constraints.allowedTypes` est masqué et tout type non spécifié dans `constraints.excludedTypes` s’affiche.
`constraints.allowedTypes`et `constraints.excludedTypes` sont tous deux facultatifs, mais ne peuvent pas être utilisés simultanément.
- Si `options.hideExisting` est défini sur **true**, l’utilisateur ne peut pas choisir de compte de stockage existant. La valeur par défaut est **false**.


## <a name="sample-output"></a>Exemple de sortie
```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](managed-application-createuidefinition-elements.md).

