---
title: "Stratégies de ressources Azure pour les conventions d’affectation de noms | Microsoft Docs"
description: "Décrit les stratégies Azure Resource Manager pour les conventions d’affectation de noms des ressources."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 51b3519bbba8cb4c768bfdd7dadf92fced434f22
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017

---
# <a name="apply-resource-policies-for-names-and-text"></a>Appliquer des stratégies de ressources pour les noms et le texte
Cette rubrique montre plusieurs [stratégies de ressources](resource-manager-policy.md) que vous pouvez appliquer pour établir des conventions d’affectation de noms et de texte. Ces stratégies garantissent la cohérence des noms de ressources et des valeurs de balise. 

## <a name="set-naming-convention-with-wildcard"></a>Définir une convention d’affectation de noms avec un caractère générique
L’exemple suivant illustre l’utilisation de caractères génériques, grâce à la condition **like**. La condition stipule que la demande est refusée si le nom ne correspond pas au modèle indiqué (namePrefix\*nameSuffix) :

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>Définir une convention d’affectation de noms avec un modèle

Utilisez la condition de correspondance pour indiquer que des noms de ressources correspondent à un modèle. L’exemple suivant requiert que les noms commencent par `contoso` et contiennent six lettres supplémentaires :

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>Définir le modèle de date pour la valeur de balise

Pour exiger l’utilisation d’un modèle de date à deux chiffres, tiret, trois lettres, tiret et quatre chiffres, utilisez ce qui suit :

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Après avoir défini une règle de stratégie (comme le montrent les exemples précédents), vous devez créer la définition de stratégie et l’attribuer à une étendue. L’étendue peut être un abonnement, un groupe de ressources ou une ressource. Pour affecter des stratégies via le portail, consultez [Utiliser le portail Azure pour affecter et gérer les stratégies de ressources](resource-manager-policy-portal.md). Pour affecter des stratégies via l’API REST, PowerShell ou Azure CLI, consultez [Affecter et gérer des stratégies via un script](resource-manager-policy-create-assign.md). 
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).


