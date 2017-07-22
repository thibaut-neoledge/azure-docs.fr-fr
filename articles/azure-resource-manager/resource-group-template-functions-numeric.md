---
title: "Fonctions de modèle Azure Resource Manager - numérique| Microsoft Docs"
description: "Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour travailler avec des nombres."
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
ms.date: 06/13/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: ae0261134b8d4a934048f58d6c679a48a904950b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/21/2017

---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Fonctions numériques pour les modèles Azure Resource Manager

Resource Manager fournit les expressions ci-après pour travailler avec des entiers :

* [ajouter](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [min](#min)
* [max](#max)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>ajouter
`add(operand1, operand2)`

Retourne la somme des deux entiers fournis.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- | 
|operand1 |Oui |int |Premier nombre à ajouter. |
|operand2 |Oui |int |Deuxième nombre à ajouter. |

### <a name="return-value"></a>Valeur de retour

Entier qui contient la somme des paramètres.

### <a name="example"></a>Exemple

L’exemple suivant ajoute deux paramètres.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| addResult | int | 8 |

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Retourne l’index d’une boucle d’itération. 

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| loopName | Non | string | Nom de la boucle pour l’obtention de l’itération. |
| Offset |Non |int |Le nombre à ajouter à la valeur d’itération de base zéro. |

### <a name="remarks"></a>Remarques

Cette fonction est toujours utilisée avec un objet **copy** . Si aucune valeur n’est fournie pour **offset**, la valeur d’itération actuelle est retournée. La valeur d’itération commence à zéro.

La propriété **loopName** permet d’indiquer si copyIndex fait référence à une itération de ressource ou de propriété. Si aucune valeur n’est indiquée pour **loopName**, l’itération du type de ressource actuelle est utilisée. Indiquez une valeur pour **loopName** lors de l’itération sur une propriété. 
 
Pour obtenir une description complète d’exemples d’utilisation de l’expression **copyIndex**, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

### <a name="example"></a>Exemple

L’exemple suivant montre une boucle de copie ainsi que la valeur d’index incluse dans le nom. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Valeur de retour

Entier représentant l’index actuel de l’itération.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Retourne la division entière des deux entiers fournis.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |int |Le nombre à diviser. |
| operand2 |Oui |int |Le nombre utilisé pour diviser. Ne peut pas être 0. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la division.

### <a name="example"></a>Exemple

L’exemple suivant divise un paramètre par un autre paramètre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| divResult | int | 2 |

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Convertit la valeur en nombre à virgule flottante. Vous utilisez uniquement cette fonction lors de la transmission de paramètres personnalisés à une application, telle qu’une application logique.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |chaîne ou entier |Valeur à convertir en nombre à virgule flottante. |

### <a name="return-value"></a>Valeur de retour
Nombre à virgule flottante.

### <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser float pour passer des paramètres à une application logique :

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
        "custom1": {
            "value": "[float('3.0')]"
        },
        "custom2": {
            "value": "[float(3)]"
        },
```

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Convertit la valeur spécifiée en entier.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Oui |chaîne ou entier |La valeur à convertir en entier. |

### <a name="return-value"></a>Valeur de retour

Nombre entier de la valeur convertie.

### <a name="example"></a>Exemple

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en entier.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": { 
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| intResult | int | 4 |


<a id="min" />

## <a name="min"></a>min
`min (arg1)`

Retourne la valeur minimale à partir d’un tableau d’entiers ou une liste séparée par des virgules d’entiers.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau d’entiers ou liste séparée par des virgules d’entiers |Collection permettant d’obtenir la valeur minimale. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la valeur minimale de la collection.

### <a name="example"></a>Exemple

L’exemple suivant indique comment utiliser la fonction min avec un tableau et une liste d’entiers :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Retourne la valeur minimale à partir d’un tableau d’entiers ou une liste séparée par des virgules d’entiers.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau d’entiers ou liste séparée par des virgules d’entiers |Collection permettant d’obtenir la valeur maximale. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la valeur maximale de la collection.

### <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser max avec un tableau et une liste d’entiers :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

Retourne le reste de la division entière des deux entiers fournis.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |int |Le nombre à diviser. |
| operand2 |Oui |int |Le nombre utilisé pour diviser, Ne peut pas être 0. |

### <a name="return-value"></a>Valeur de retour
Entier représentant le reste.

### <a name="example"></a>Exemple

L’exemple suivant renvoie le reste de la division d’un paramètre par un autre paramètre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| modResult | int | 1 |

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Retourne la multiplication des deux entiers fournis.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |int |Premier nombre à multiplier. |
| operand2 |Oui |int |Deuxième nombre à multiplier. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la multiplication.

### <a name="example"></a>Exemple

L’exemple suivant multiplie un paramètre par un autre paramètre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| mulResult | int | 15 |

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Retourne la soustraction des deux entiers fournis.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |int |Le nombre auquel est appliquée la soustraction. |
| operand2 |Oui |int |Le nombre qui est soustrait. |

### <a name="return-value"></a>Valeur de retour
Entier représentant la multiplication.

### <a name="example"></a>Exemple

L’exemple suivant soustrait un paramètre à un autre paramètre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| subResult | int | 4 |

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).


