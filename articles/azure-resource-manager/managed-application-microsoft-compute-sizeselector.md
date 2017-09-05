---
title: "Élément d’interface utilisateur SizeSelector des applications gérées Azure | Microsoft Docs"
description: "Décrit l’élément d’interface utilisateur Microsoft.Compute.SizeSelector pour les applications gérées Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e54962f73028ada258a7faef271d66f0fbcef649
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017

---
# <a name="microsoftcomputesizeselector-ui-element"></a>Élément d’interface utilisateur Microsoft.Compute.SizeSelector
Contrôle permettant de sélectionner une taille pour une ou plusieurs instances de machine virtuelle. Vous utilisez cet élément lors de la [création d’une application gérée Azure](managed-application-publishing.md).

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": []
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- `recommendedSizes` doit contenir au moins une taille. La première taille recommandée est utilisée comme valeur par défaut.
- Si une taille recommandée n’est pas disponible à l’emplacement sélectionné, la taille est automatiquement ignorée. La taille recommandée suivante est alors utilisée.
- Toute taille non spécifiée dans `constraints.allowedSizes` est masquée et toute taille non spécifiée dans `constraints.excludedSizes` s’affiche.
`constraints.allowedSizes`et `constraints.excludedSizes` sont tous deux facultatifs, mais ne peuvent pas être utilisés simultanément. La liste des tailles disponibles peut être déterminée en appelant [Répertorier les tailles de machines virtuelles disponibles pour un abonnement](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region).
- `osPlatform` doit être spécifié. Il peut s’agir de **Windows** ou de **Linux**. Il permet de déterminer le coût du matériel des machines virtuelles.
- `imageReference` est omis pour les images internes, mais est indiqué pour les images issues de tiers. Il permet de déterminer le coût des logiciels des machines virtuelles.
- `count` permet de définir le multiplicateur approprié pour l’élément. Il prend en charge une valeur statique, telle que **2**, ou une valeur dynamique issue d’un autre élément, comme `[steps('step1').vmCount]`. La valeur par défaut est **1**.

## <a name="sample-output"></a>Exemple de sortie
```json
"Standard_D1"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](managed-application-overview.md).
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](managed-application-createuidefinition-elements.md).

