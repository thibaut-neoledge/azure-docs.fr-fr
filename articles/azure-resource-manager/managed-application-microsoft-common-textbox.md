---
title: "Élément d’interface utilisateur TextBox des applications gérées Azure | Microsoft Docs"
description: "Décrit l’élément d’interface utilisateur Microsoft.Common.TextBox pour les applications gérées Azure"
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
ms.openlocfilehash: aa161d2f6611cd7f1ef8fac8eebfde12eedd9fcf
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---

# <a name="microsoftcommontextbox-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.TextBox
Contrôle pouvant être utilisé pour modifier du texte non mis en forme.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Halp!",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- Si `constraints.required` est défini sur **true**, la zone de texte doit contenir une valeur permettant de réussir la validation. La valeur par défaut est **false**.
- `constraints.regex` est un modèle d’expression régulière JavaScript. S’il est spécifié, la valeur de la zone de texte doit correspondre au modèle pour permettre la réussite de la validation. La valeur par défaut est **null**.
- `constraints.validationMessage` est une chaîne à afficher en cas d’échec de la validation de la valeur de la zone de texte. Si elle n’est pas spécifiée, les messages de validation intégrés de la zone de texte sont utilisés. La valeur par défaut est **null**.
- Il est possible de spécifier une valeur pour `constraints.regex` lorsque `constraints.required` est défini sur **false**. Dans ce scénario, aucune valeur n’est requise pour valider la zone de texte. Si elle est spécifiée, celle-ci doit correspondre au modèle d’expression régulière.

## <a name="sample-output"></a>Exemple de sortie

```json
"foobar"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](managed-application-createuidefinition-elements.md).

