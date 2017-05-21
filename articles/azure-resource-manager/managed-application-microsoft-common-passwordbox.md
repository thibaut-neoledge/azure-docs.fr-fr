---
title: "Élément d’interface utilisateur PasswordBox des applications gérées Azure | Microsoft Docs"
description: "Décrit l’élément d’interface utilisateur Microsoft.Common.PasswordBox pour les applications gérées Azure"
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
ms.openlocfilehash: 4646521531e839e4751b4f01f7a66876dac96b57
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.PasswordBox
Contrôle qui peut être utilisé pour indiquer et confirmer un mot de passe.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- Cet élément ne prend pas en charge la propriété `defaultValue`.
- Pour obtenir des détails relatifs à l’implémentation de `constraints`, consultez [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md).
- Si `options.hideConfirmation` est défini sur **true**, la deuxième zone de texte de confirmation du mot de passe utilisateur est masquée. La valeur par défaut est **false**.

## <a name="sample-output"></a>Exemple de sortie
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](managed-application-createuidefinition-elements.md).

