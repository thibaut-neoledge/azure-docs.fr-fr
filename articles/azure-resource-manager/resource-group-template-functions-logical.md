---
title: "Fonctions de modèle Azure Resource Manager - logiques | Documents Microsoft"
description: "Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour déterminer les valeurs logiques."
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
ms.date: 08/01/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 313601ad99cdc12c4b50f5469959d37a9fa70d35
ms.contentlocale: fr-fr
ms.lasthandoff: 08/02/2017

---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Fonctions logiques pour les modèles Azure Resource Manager

Resource Manager fournit plusieurs fonctions pour effectuer des comparaisons dans vos modèles.

* [et](#and)
* [bool](#bool)
* [si](#if)
* [non](#not)
* [ou](#or)

## <a name="and"></a>and
`and(arg1, arg2)`

Vérifie si les deux valeurs de paramètres sont true.

### <a name="parameters"></a>parameters

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |booléenne |La première valeur pour vérifier si c’est true. |
| arg2 |Oui |booléenne |La deuxième valeur pour vérifier si c’est true. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si les valeurs sont true ; sinon, renvoie **False**.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser des fonctions logiques.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | true |
| notExampleOutput | Bool | False |


## <a name="bool"></a>bool
`bool(arg1)`

Convertit le paramètre en valeur booléenne.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |chaîne ou entier |La valeur à convertir en booléen. |

### <a name="return-value"></a>Valeur de retour
Valeur booléenne de la valeur convertie.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser le booléen avec une chaîne ou un entier.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| trueString | Bool | true |
| falseString | Bool | False |
| trueInt | Bool | true |
| falseInt | Bool | False |

## <a name="if"></a>if
`if(condition, trueValue, falseValue)`

Retourne une valeur indiquant si une condition est true ou false.

### <a name="parameters"></a>parameters

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| condition |Oui |booléenne |La valeur pour vérifier si c’est true. |
| trueValue |Oui | chaîne, int, objet ou tableau |La valeur à retourner lorsque la condition est true. |
| falseValue |Oui | chaîne, int, objet ou tableau |La valeur à retourner lorsque la condition est false. |

### <a name="return-value"></a>Valeur de retour

Retourne le deuxième paramètre lorsque le premier paramètre est **True** ; sinon, retourne le troisième paramètre.

### <a name="remarks"></a>Remarques

Vous pouvez utiliser cette fonction pour définir de manière conditionnelle une propriété de ressource. L’exemple suivant n’est pas un modèle complet, mais il affiche les parties pertinentes pour la définition de manière conditionnelle du groupe à haute disponibilité.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "availabilitySet": {
            "type": "string",
            "allowedValues": [
                "yes",
                "no"
            ]
        }
    },
    "variables": {
        ...
        "availabilitySetName": "availabilitySet1",
        "availabilitySet": {
            "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
        }
    },
    "resources": [
        {
            "condition": "[equals(parameters('availabilitySet'),'yes')]",
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            ...
        },
        {
            "apiVersion": "2016-03-30",
            "type": "Microsoft.Compute/virtualMachines",
            "properties": {
                "availabilitySet": "[if(equals(parameters('availabilitySet'),'yes'), variables('availabilitySet'), json('null'))]",
                ...
            }
        },
        ...
    ],
    ...
}
```

### <a name="examples"></a>Exemples

L’exemple suivant explique comment utiliser la fonction `if`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        }
    }
}
```

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| yesOutput | String | yes |
| noOutput | String | no |


## <a name="not"></a>not
`not(arg1)`

Convertit la valeur booléenne à sa valeur opposée.

### <a name="parameters"></a>parameters

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |booléenne |La valeur à convertir. |


### <a name="return-value"></a>Valeur de retour

Retourne **True** lorsque le paramètre est **False**. Retourne **False** lorsque le paramètre est **True**.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser des fonctions logiques.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | true |
| notExampleOutput | Bool | False |

L’exemple suivant utilise **non** avec[égal à](resource-group-template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| checkNotEquals | Bool | true |


## <a name="or"></a>ou
`or(arg1, arg2)`

Vérifie si l’une des valeurs du paramètre est true.

### <a name="parameters"></a>parameters

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |booléenne |La première valeur pour vérifier si c’est true. |
| arg2 |Oui |booléenne |La deuxième valeur pour vérifier si c’est true. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si la valeur est true ; sinon, **False**.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser des fonctions logiques.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | true |
| notExampleOutput | Bool | False |


## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).


