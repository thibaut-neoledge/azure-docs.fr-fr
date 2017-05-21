---
title: "Élément d’interface utilisateur DropDown des applications gérées Azure | Microsoft Docs"
description: "Décrit l’élément d’interface utilisateur Microsoft.Common.DropDown pour les applications gérées Azure"
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
ms.openlocfilehash: 14809bd7ce5eec140929de88e525df0ca7442869
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---

# <a name="microsoftcommondropdown-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.DropDown
Contrôle de sélection avec liste déroulante.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "Foo",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Foo",
        "value": "Bar"
      },
      {
        "label": "Baz",
        "value": "Qux"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- L’étiquette de `constraints.allowedValues` est le texte qui s’affiche pour un élément, et sa valeur est la valeur de sortie de l’élément sélectionné lors de la sélection.
- Si elle est spécifiée, la valeur par défaut doit être une étiquette présente dans `constraints.allowedValues`. Dans le cas contraire, le premier élément de `constraints.allowedValues` est sélectionné. La valeur par défaut est **null**.
- `constraints.allowedValues` doit contenir au moins un élément.
- Cet élément ne prend pas en charge la propriété `constraints.required`. Pour émuler ce comportement, ajoutez un élément avec une étiquette et la valeur de `""` (chaîne vide) à `constraints.allowedValues`.

## <a name="sample-output"></a>Exemple de sortie
```json
"Bar"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](managed-application-createuidefinition-elements.md).

