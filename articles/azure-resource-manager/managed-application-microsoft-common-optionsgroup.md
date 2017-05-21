---
title: "Élément d’interface utilisateur OptionsGroup des applications gérées Azure | Microsoft Docs"
description: "Décrit l’élément d’interface utilisateur Microsoft.Common.OptionsGroup pour les applications gérées Azure"
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
ms.openlocfilehash: 0d1784b7e8d4fed13bbcfd828924ed464e8f4119
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.OptionsGroup
Contrôle de sélection avec une ligne d’options disponibles.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
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
- Si elle est spécifiée, la valeur par défaut doit être une étiquette présente dans `constraints.allowedValues`. Dans le cas contraire, le premier élément de `constraints.allowedValues` est sélectionné par défaut. La valeur par défaut est **null**.
- `constraints.allowedValues` doit contenir au moins un élément.
- Cet élément ne prend pas en charge la propriété `constraints.required` ; un élément doit être sélectionné pour réussir la validation.

## <a name="sample-output"></a>Exemple de sortie
```json
"Bar"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](managed-application-createuidefinition-elements.md).

