---
title: "Fonctions des modèles Resource Manager | Microsoft Docs"
description: "Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour récupérer des valeurs, utiliser des chaînes et des valeurs numériques, et récupérer des informations sur le déploiement."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 1ed23fc5a69cea70636de8b18911c1b11119d3a3
ms.lasthandoff: 03/22/2017


---
# <a name="azure-resource-manager-template-functions"></a>Fonctions des modèles Azure Resource Manager
Cette rubrique décrit toutes les fonctions que vous pouvez utiliser dans un modèle Azure Resource Manager.

Les fonctions des modèles et leurs paramètres ne respectent pas la casse. Par exemple, Resource Manager résout **variables('var1')** et **VARIABLES('VAR1')** de la même manière. Lors de l’évaluation, la fonction préserve la casse sauf si elle la modifie expressément (toUpper ou toLower, par exemple). Certains types de ressources peuvent avoir des exigences de casse, quelle que soit la manière dont les fonctions sont évaluées.

## <a name="numeric-functions"></a>Fonctions numériques
Resource Manager fournit les expressions ci-après pour travailler avec des entiers :

* [ajouter](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [int](#int)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

### <a name="add"></a>ajouter
`add(operand1, operand2)`

Retourne la somme des deux entiers fournis.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- | 
|operand1 |Oui |Entier  |Premier nombre à ajouter. |
|operand2 |Oui |Entier  |Deuxième nombre à ajouter. |

L’exemple suivant ajoute deux paramètres.

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "First integer to add"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Second integer to add"
    }
  }
},
...
"outputs": {
  "addResult": {
    "type": "int",
    "value": "[add(parameters('first'), parameters('second'))]"
  }
}
```

<a id="copyindex" />

### <a name="copyindex"></a>copyIndex
`copyIndex(offset)`

Retourne l’index d’une boucle d’itération. 

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| Offset |Non |Entier  |Le nombre à ajouter à la valeur d’itération de base zéro. |

Cette fonction est toujours utilisée avec un objet **copy** . Si aucune valeur n’est fournie pour **offset**, la valeur d’itération actuelle est retournée. La valeur d’itération commence à zéro. Pour obtenir une description complète d’exemples d’utilisation de l’expression **copyIndex**, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

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

<a id="div" />

### <a name="div"></a>div
`div(operand1, operand2)`

Retourne la division entière des deux entiers fournis.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |Entier  |Le nombre à diviser. |
| operand2 |Oui |Entier  |Le nombre utilisé pour diviser. Ne peut pas être 0. |

L’exemple suivant divise un paramètre par un autre paramètre.

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "Integer being divided"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Integer used to divide"
    }
  }
},
...
"outputs": {
  "divResult": {
    "type": "int",
    "value": "[div(parameters('first'), parameters('second'))]"
  }
}
```

<a id="int" />

### <a name="int"></a>int
`int(valueToConvert)`

Convertit la valeur spécifiée en entier.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Oui |String ou Integer |La valeur à convertir en entier. |

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en entier.

```json
"parameters": {
    "appId": { "type": "string" }
},
"variables": { 
    "intValue": "[int(parameters('appId'))]"
}
```

<a id="mod" />

### <a name="mod"></a>mod
`mod(operand1, operand2)`

Retourne le reste de la division entière des deux entiers fournis.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |Entier  |Le nombre à diviser. |
| operand2 |Oui |Entier  |Le nombre utilisé pour diviser, Ne peut pas être 0. |

L’exemple suivant renvoie le reste de la division d’un paramètre par un autre paramètre.

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "Integer being divided"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Integer used to divide"
    }
  }
},
...
"outputs": {
  "modResult": {
    "type": "int",
    "value": "[mod(parameters('first'), parameters('second'))]"
  }
}
```

<a id="mul" />

### <a name="mul"></a>mul
`mul(operand1, operand2)`

Retourne la multiplication des deux entiers fournis.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |Entier  |Premier nombre à multiplier. |
| operand2 |Oui |Entier  |Deuxième nombre à multiplier. |

L’exemple suivant multiplie un paramètre par un autre paramètre.

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "First integer to multiply"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Second integer to multiply"
    }
  }
},
...
"outputs": {
  "mulResult": {
    "type": "int",
    "value": "[mul(parameters('first'), parameters('second'))]"
  }
}
```

<a id="sub" />

### <a name="sub"></a>sub
`sub(operand1, operand2)`

Retourne la soustraction des deux entiers fournis.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |Entier  |Le nombre auquel est appliquée la soustraction. |
| operand2 |Oui |Entier  |Le nombre qui est soustrait. |

L’exemple suivant soustrait un paramètre à un autre paramètre.

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "Integer subtracted from"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Integer to subtract"
    }
  }
},
...
"outputs": {
  "subResult": {
    "type": "int",
    "value": "[sub(parameters('first'), parameters('second'))]"
  }
}
```

## <a name="string-functions"></a>Fonctions de chaîne
Resource Manager fournit les fonctions ci-après pour travailler avec des chaînes de caractères :

* [base64](#base64)
* [concat](#concat)
* [length](#lengthstring)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skipstring)
* [split](#split)
* [string](#string)
* [substring](#substring)
* [take](#takestring)
* [toLower](#tolower)
* [toUpper](#toupper)
* [découper](#trim)
* [uniqueString](#uniquestring)
* [URI](#uri)

<a id="base64" />

### <a name="base64"></a>base64
`base64 (inputString)`

Retourne la représentation en base 64 de la chaîne d'entrée.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_entrée |Oui |String |La valeur à retourner sous la forme d’une représentation en base64. |

L’exemple suivant montre comment utiliser la fonction base64.

```json
"variables": {
  "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
  "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

<a id="concat" />

### <a name="concat---string"></a>concat - string
`concat (string1, string2, string3, ...)`

Combine plusieurs valeurs de chaîne et renvoie la chaîne concaténée. 

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| string1 |Oui |String |La première valeur pour la concaténation. |
| chaînes supplémentaires |Non |String |Valeurs supplémentaires en ordre séquentiel pour la concaténation. |

Cette fonction peut prendre n’importe quel nombre d’arguments et accepter à la fois des chaînes ou des tableaux pour les paramètres. Pour obtenir un exemple de concaténation des tableaux, consultez [concat - array](#concatarray).

L’exemple suivant montre comment combiner plusieurs valeurs pour retourner au final une chaîne concaténée.

```json
"outputs": {
    "siteUri": {
      "type": "string",
      "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

<a id="lengthstring" />

### <a name="length---string"></a>length - string
`length(string)`

Renvoie le nombre de caractères dans une chaîne.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| string |Oui |String |La valeur à utiliser pour l’obtention du nombre de caractères. |

Pour découvrir un exemple d’utilisation de longueur avec un tableau, consultez [length - array](#length).

L’exemple suivant renvoie le nombre de caractères dans une chaîne. 

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "nameLength": "[length(parameters('appName'))]"
}
```

<a id="padleft" />

### <a name="padleft"></a>padLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

Renvoie une chaîne alignée à droite en lui ajoutant des caractères sur la gauche jusqu’à ce que la longueur totale spécifiée ait été atteinte.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| valeur_à_remplir |Oui |String ou Integer |Valeur à aligner à droite. |
| longueur_totale |Oui |Entier  |Nombre total de caractères de la chaîne renvoyée. |
| caractère_de_remplissage |Non |Caractère unique |Caractère de remplissage à insérer sur la gauche jusqu’à ce que la longueur totale soit atteinte. La valeur par défaut est un espace. |

L’exemple ci-après indique comment remplir la valeur de paramètre fournie par l’utilisateur avec le caractère zéro jusqu’à ce que la chaîne atteigne 10 caractères. Si la valeur de paramètre d’origine comporte plus de 10 caractères, aucun caractère n’est ajouté.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
}
```

<a id="replace" />

### <a name="replace"></a>replace
`replace(originalString, oldCharacter, newCharacter)`

Renvoie une nouvelle chaîne dans laquelle toutes les instances d’un caractère de la chaîne initiale spécifiée ont été remplacées par un autre caractère.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_initiale |Oui |String |La valeur pour laquelle toutes les instances d’un caractère sont remplacées par un autre caractère. |
| ancien_caractère |Oui |String |Caractère à supprimer de la chaîne initiale. |
| nouveau_caractère |Oui |String |Caractère à ajouter à la place du caractère supprimé. |

L’exemple ci-après indique comment supprimer tous les tirets de la chaîne fournie par l’utilisateur.

```json
"parameters": {
    "identifier": { "type": "string" }
},
"variables": { 
    "newidentifier": "[replace(parameters('identifier'),'-','')]"
}
```

<a id="skipstring" />

### <a name="skip---string"></a>skip - string
`skip(originalValue, numberToSkip)`

Renvoie une chaîne avec tous les caractères après le nombre spécifié dans la chaîne.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |String |Chaîne à utiliser pour ignorer les caractères. |
| numberToSkip |Oui |Entier  |Nombre de caractères à ignorer. Si cette valeur est inférieure ou égale à 0, tous les caractères de la chaîne sont renvoyés. Si elle est supérieure à la longueur de la chaîne, une chaîne vide est renvoyée. |

Pour découvrir un exemple de caractères ignorés avec un tableau, consultez [skip - array](#skip).

L’exemple suivant ignore le nombre spécifié de caractères de la chaîne.

```json
"parameters": {
  "first": {
    "type": "string",
    "metadata": {
      "description": "Value to use for skipping"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of characters to skip"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "string",
    "value": "[skip(parameters('first'),parameters('second'))]"
  }
}
```

<a id="split" />

### <a name="split"></a>split
`split(inputString, delimiterString)`

`split(inputString, delimiterArray)`

Renvoie un tableau de chaînes qui contient les sous-chaînes de la chaîne d’entrée séparées par les délimiteurs spécifiés.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_entrée |Oui |String |Chaîne à fractionner. |
| delimiter |Oui |String ou Array of strings |Le séparateur à utiliser pour fractionner la chaîne. |

L'exemple suivant fractionne la chaîne d'entrée en la séparant par une virgule.

```json
"parameters": {
    "inputString": { "type": "string" }
},
"variables": { 
    "stringPieces": "[split(parameters('inputString'), ',')]"
}
```

L’exemple suivant fractionne la chaîne d’entrée par une virgule ou un point-virgule.

```json
"variables": {
  "stringToSplit": "test1,test2;test3",
  "delimiters": [ ",", ";" ]
},
"resources": [ ],
"outputs": {
  "exampleOutput": {
    "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
    "type": "array"
  }
}
```

<a id="string" />

### <a name="string"></a>string
`string(valueToConvert)`

Convertit la valeur spécifiée en chaîne.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Oui | Quelconque |Valeur à convertir en chaîne. N’importe quel type de valeur peut être converti, y compris les objets et des tableaux. |

L’exemple ci-après convertit les valeurs de paramètre fournies par l’utilisateur en chaîne.

```json
"parameters": {
  "jsonObject": {
    "type": "object",
    "defaultValue": {
      "valueA": 10,
      "valueB": "Example Text"
    }
  },
  "jsonArray": {
    "type": "array",
    "defaultValue": [ "a", "b", "c" ]
  },
  "jsonInt": {
    "type": "int",
    "defaultValue": 5
  }
},
"variables": { 
  "objectString": "[string(parameters('jsonObject'))]",
  "arrayString": "[string(parameters('jsonArray'))]",
  "intString": "[string(parameters('jsonInt'))]"
}
```

<a id="substring" />

### <a name="substring"></a>substring
`substring(stringToParse, startIndex, length)`

Retourne une sous-chaîne qui commence à la position de caractère spécifiée et qui contient le nombre de caractères spécifié.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_à_analyser |Oui |String |La chaîne d’origine de laquelle la sous-chaîne est extraite. |
| index_début |Non |Entier  |La position de caractère (commençant à zéro) de la sous-chaîne. |
| length |Non |Entier  |Le nombre de caractères de la sous-chaîne. Doit faire référence à un emplacement au sein de la chaîne. |

L’exemple suivant extrait les trois premiers caractères d’un paramètre.

```json
"parameters": {
    "inputString": { "type": "string" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 3)]"
}
```

L’exemple suivant échoue avec l’erreur « Les paramètres d'index et de longueur doivent correspondre à un emplacement au sein de la chaîne. Paramètre d’index : « 0 », paramètre de longueur : « 11 », paramètre de longueur de la chaîne : « 10 ».

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

<a id="takestring" />

### <a name="take---string"></a>take - string
`take(originalValue, numberToTake)`

Renvoie une chaîne avec le nombre de caractères spécifié à partir du début de la chaîne.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |String |La valeur à partir de laquelle il faut tirer les caractères. |
| numberToTake |Oui |Entier  |Nombre de caractères à tirer. Si cette valeur est inférieure ou égale à 0, une chaîne vide est renvoyée. Si elle est supérieure à la longueur de la chaîne donnée, tous les caractères de la chaîne sont renvoyés. |

Pour découvrir un exemple de caractères tirés avec un tableau, consultez [take - array](#take).

L’exemple suivant tire le nombre spécifié de caractères de la chaîne.

```json
"parameters": {
  "first": {
    "type": "string",
    "metadata": {
      "description": "Value to use for taking"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of characters to take"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "string",
    "value": "[take(parameters('first'), parameters('second'))]"
  }
}
```

<a id="tolower" />

### <a name="tolower"></a>toLower
`toLower(stringToChange)`

Convertit la chaîne spécifiée en minuscules.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_à_modifier |Oui |String |La valeur à convertir en minuscules. |

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en minuscules.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "lowerCaseAppName": "[toLower(parameters('appName'))]"
}
```

<a id="toupper" />

### <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

Convertit la chaîne spécifiée en majuscules.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_à_modifier |Oui |String |La valeur à convertir en majuscules. |

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en majuscules.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "upperCaseAppName": "[toUpper(parameters('appName'))]"
}
```

<a id="trim" />

### <a name="trim"></a>découper
`trim (stringToTrim)`

Supprime tous les espaces de début et de fin de la chaîne indiquée.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToTrim |Oui |String |La valeur à supprimer. |

L’exemple suivant supprime les espaces à partir de la valeur de paramètre indiquée par l’utilisateur.

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "trimAppName": "[trim(parameters('appName'))]"
}
```

<a id="uniquestring" />

### <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

Crée une chaîne de hachage déterministe basée sur les valeurs fournies en tant que paramètres. 

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| baseString |Oui |String |La valeur utilisée dans la fonction de hachage pour créer une chaîne unique. |
| paramètres supplémentaires le cas échéant |Non |String |Vous pouvez ajouter autant de chaînes que nécessaire pour créer la valeur qui spécifie le niveau d’unicité. |

Cette fonction est utile lorsque vous avez besoin de créer un nom unique pour une ressource. Vous fournissez des valeurs de paramètre qui limitent l’étendue d’unicité pour le résultat. Vous pouvez spécifier si le nom est unique pour l’abonnement, le groupe de ressources ou le déploiement. 

La valeur renvoyée n’est pas une chaîne aléatoire, mais plutôt le résultat d’une fonction de hachage. La valeur renvoyée comprend 13 caractères. Elle n’est pas globalement unique. Il se peut que vous souhaitiez associer un préfixe de votre convention d’affectation de noms à la valeur pour créer un nom explicite. L’exemple suivant montre le format de la valeur renvoyée. La valeur réelle varie en fonction des paramètres fournis.

    tcvhiyu5h2o5o

Les exemples suivants montrent comment utiliser uniqueString afin de créer une valeur unique pour des niveaux couramment utilisés.

Unique limité à l’abonnement

```json
"[uniqueString(subscription().subscriptionId)]"
```

Unique limité au groupe de ressources

```json
"[uniqueString(resourceGroup().id)]"
```

Unique limité au déploiement pour un groupe de ressources

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

L'exemple suivant montre comment créer un nom unique pour un compte de stockage basé sur votre groupe de ressources. Dans le groupe de ressources, le nom n’est pas unique s’il est construit de la même façon.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```


<a id="uri" />

### <a name="uri"></a>URI
`uri (baseUri, relativeUri)`

Crée un URI absolu en combinant le baseUri et la chaîne relativeUri.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| baseUri |Oui |String |La chaîne d’URI de base. |
| relativeUri |Oui |String |La chaîne d’URI relatif à ajouter à la chaîne d’URI de base. |

La valeur du paramètre **baseUri** peut inclure un fichier spécifique, mais seul le chemin de base est utilisé lors de la construction de l’URI. Par exemple, si vous passez `http://contoso.com/resources/azuredeploy.json` comme paramètre baseUri, l’URI de base résultant est `http://contoso.com/resources/`.

L’exemple suivant montre comment créer un lien vers un modèle imbriqué en fonction de la valeur du modèle parent.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

## <a name="array-functions"></a>Fonctions de tableau
Resource Manager fournit les fonctions ci-après pour travailler avec des valeurs de tableau :

* [concat](#concatarray)
* [length](#length)
* [skip](#skip)
* [take](#take)

Pour obtenir un tableau de valeurs de chaîne délimitée par une valeur, consultez [split](#split).

<a id="concatarray" />

### <a name="concat---array"></a>concat - array
`concat (array1, array2, array3, ...)`

Combine plusieurs tableaux et renvoie le tableau concaténé. 

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| array1 |Oui |Tableau |Le premier tableau pour la concaténation. |
| tableaux supplémentaires |Non |Tableau |Tableaux supplémentaires en ordre séquentiel pour la concaténation. |

Cette fonction peut prendre n’importe quel nombre d’arguments et accepter à la fois des chaînes ou des tableaux pour les paramètres. Pour obtenir un exemple de concaténation des valeurs de chaîne, consultez [concat - string](#concat).

L’exemple suivant montre comment combiner deux tableaux.

```json
"parameters": {
    "firstarray": {
      "type": "array"
    }
    "secondarray": {
      "type": "array"
    }
},
"variables": {
    "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]"
}
```

<a id="length" />

### <a name="length---array"></a>length - array
`length(array)`

Retourne le nombre d'éléments dans un tableau.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| array |Oui |Tableau |Tableau à utiliser pour obtenir le nombre d’éléments. |

Vous pouvez utiliser cette fonction avec un tableau pour spécifier le nombre d’itérations lors de la création de ressources. Dans l’exemple ci-après, le paramètre **siteNames** fait référence à un tableau de noms à utiliser lors de la création de sites web.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Pour plus d’informations sur l’utilisation de cette fonction avec un tableau, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md). 

Pour découvrir un exemple d’utilisation de longueur avec une valeur de chaîne, consultez [length - string](#lengthstring).

<a id="skip" />

### <a name="skip---array"></a>skip - array
`skip(originalValue, numberToSkip)`

Renvoie un tableau avec tous les éléments après le nombre spécifié dans le tableau.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |Tableau |Tableau à utiliser pour ignorer les éléments. |
| numberToSkip |Oui |Entier  |Nombre d’éléments à ignorer. Si cette valeur est inférieure ou égale à 0, tous les éléments du tableau sont renvoyés. Si elle est supérieure à la longueur du tableau, un tableau vide est renvoyé. |

Pour découvrir un exemple d’éléments ignorés avec une chaîne, consultez [skip - string](#skipstring).

L’exemple suivant ignore le nombre spécifié d’éléments du tableau.

```json
"parameters": {
  "first": {
    "type": "array",
    "metadata": {
      "description": "Values to use for skipping"
    },
    "defaultValue": [ "one", "two", "three" ]
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of elements to skip"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "array",
    "value": "[skip(parameters('first'), parameters('second'))]"
  }
}
```

<a id="take" />

### <a name="take---array"></a>take - array
`take(originalValue, numberToTake)`

Renvoie un tableau avec le nombre spécifié d’éléments depuis le début du tableau.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |Tableau |Tableau à partir duquel les éléments sont tirés. |
| numberToTake |Oui |Entier  |Nombre d’éléments à tirer. Si cette valeur est inférieure ou égale à 0, un tableau vide est renvoyé. Si elle est supérieure à la longueur du tableau donné, tous les éléments du tableau sont renvoyés. |

Pour découvrir un exemple d’éléments de ce type avec une chaîne, consultez [take - string](#takestring).

L’exemple suivant prend le nombre spécifié d’éléments du tableau.

```json
"parameters": {
  "first": {
    "type": "array",
    "metadata": {
      "description": "Values to use for taking"
    },
    "defaultValue": [ "one", "two", "three" ]
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of elements to take"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "array",
    "value": "[take(parameters('first'),parameters('second'))]"
  }
}
```

## <a name="deployment-value-functions"></a>Fonctions de valeur de déploiement
Resource Manager offre les fonctions ci-après pour l’obtention de valeurs à partir des sections du modèle et de valeurs associées au déploiement :

* [deployment](#deployment)
* [parameters](#parameters)
* [variables](#variables)

Pour obtenir des valeurs de ressources, de groupes de ressources ou d’abonnements, consultez [Fonctions de ressource](#resource-functions).

<a id="deployment" />

### <a name="deployment"></a>déploiement
`deployment()`

Renvoie des informations sur l’opération de déploiement actuelle.

Cette fonction retourne l’objet transmis au cours du déploiement. Les propriétés de l’objet renvoyé diffèrent selon que l’objet de déploiement est passé sous forme de lien ou d’objet inline. 

Quand l’objet de déploiement est passé inline, comme lors de l’utilisation du paramètre **-TemplateFile** dans Azure PowerShell pour pointer vers un fichier local, l’objet renvoyé a le format suivant :

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quand l’objet est passé comme lien, par exemple lors de l’utilisation du paramètre **-TemplateUri** pour pointer vers un objet distant, l’objet est retourné dans le format suivant : 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

L’exemple suivant montre comment utiliser deployment() pour établir une liaison à un autre modèle en fonction de l’URI du modèle parent.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

<a id="parameters" />

### <a name="parameters"></a>parameters
`parameters (parameterName)`

Retourne une valeur de paramètre. Le nom de paramètre spécifié doit être défini dans la section parameters du modèle.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| nom_paramètre |Oui |String |Nom du paramètre à retourner. |

L'exemple suivant montre une utilisation simplifiée de la fonction parameters.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2014-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

<a id="variables" />

### <a name="variables"></a>variables
`variables (variableName)`

Retourne la valeur de la variable. Le nom de variable spécifié doit être défini dans la section variables du modèle.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| variableName |Oui |String |Nom de la variable à retourner. |

L’exemple suivant utilise une valeur de variable.

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
],
```

## <a name="resource-functions"></a>Fonctions de ressource
Resource Manager offre les fonctions ci-après pour obtenir des valeurs de ressource :

* [listKeys and list{Value}](#listkeys)
* [fournisseurs](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [abonnement](#subscription)

Pour obtenir des valeurs de paramètres, de variables ou du déploiement actuel, consultez [Fonctions de valeur de déploiement](#deployment-value-functions).

<a id="listkeys" />
<a id="list" />

### <a name="listkeys-and-listvalue"></a>listKeys and list{Value}
`listKeys (resourceName or resourceIdentifier, apiVersion)`

`list{Value} (resourceName or resourceIdentifier, apiVersion)`

Renvoie les valeurs pour n’importe quel type de ressource qui prend en charge l’opération list. L’utilisation la plus courante est **listKeys**. 

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| nom_ressource ou identificateur_ressource |Oui |String |Identificateur unique pour la ressource. |
| apiVersion |Oui |String |Version d'API de l'état d'exécution des ressources. En règle générale, au format, **aaaa-mm-jj**. |

Toute opération qui commence par **list** peut être utilisée en tant que fonction dans votre modèle. Les opérations disponibles incluent **listKeys**, mais également des opérations telles que **list**, **listAdminKeys** et **listStatus**. Pour déterminer les types de ressources qui possèdent une opération de liste, consultez les [opérations API REST](/rest/api/) pour le fournisseur de ressources.

Pour trouver les opérations de liste pour un fournisseur de ressources, utilisez l’applet de commande PowerShell suivante :

```powershell
Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
```

Pour rechercher les opérations de liste pour un fournisseur de ressources, utilisez la commande Azure CLI et l’utilitaire JSON [jq](http://stedolan.github.io/jq/download/) pour filtrer uniquement les opérations de liste :

```azurecli
azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"
```

L’identificateur de ressource (resourceId) peut être spécifié à l’aide de la [fonction resourceId](#resourceid) ou en utilisant le format **{providerNamespace}/{resourceType}/{resourceName}**.

L’exemple suivant montre comment renvoyer les clés primaires et secondaires à partir d’un compte de stockage dans la section outputs.

```json
"outputs": { 
  "listKeysOutput": { 
    "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
    "type" : "object" 
  } 
}
``` 

L’objet renvoyé par listKeys a le format suivant :

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

<a id="providers" />

### <a name="providers"></a>fournisseurs
`providers (providerNamespace, [resourceType])`

Renvoie des informations sur un fournisseur de ressources et les types de ressources qu’il prend en charge. Si vous ne fournissez pas un type de ressource, la fonction renvoie tous les types pris en charge pour le fournisseur de ressources.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| espacedenoms_fournisseur |Oui |String |Espace de noms du fournisseur. |
| resourceType |Non |String |Type de ressource dans l'espace de noms spécifié. |

Chaque type pris en charge est renvoyé au format suivant. Le classement du tableau n’est pas garanti.

```json
{
    "resourceType": "",
    "locations": [ ],
    "apiVersions": [ ]
}
```

L'exemple suivant montre comment utiliser la fonction provider :

```json
"outputs": {
    "exampleOutput": {
        "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
        "type" : "object"
    }
}
```

<a id="reference" />

### <a name="reference"></a>reference
`reference (resourceName or resourceIdentifier, [apiVersion])`

Renvoie un objet représentant l’état d’exécution d’une autre ressource.

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| nom_ressource ou identificateur_ressource |Oui |String |Nom ou identificateur unique d’une ressource. |
| apiVersion |Non |String |Version d’API de la ressource spécifiée. Incluez ce paramètre lorsque la ressource n’est pas approvisionnée dans le même modèle. En règle générale, au format, **aaaa-mm-jj**. |

La fonction **reference** dérive sa valeur d'un état d'exécution, et ne peut donc pas être utilisée dans la section variables. Elle peut être utilisée dans la section outputs d'un modèle.

En utilisant la fonction « reference », vous déclarez de manière implicite qu’une ressource dépend d’une autre ressource si la ressource référencée est configurée dans le même modèle. Vous n’avez pas besoin d’utiliser également la propriété **dependsOn** . La fonction n’est pas évaluée tant que le déploiement de la ressource référencée n’est pas terminé.

L’exemple ci-après référence un compte de stockage déployé dans le même modèle.

```json
"outputs": {
    "NewStorage": {
        "value": "[reference(parameters('storageAccountName'))]",
        "type" : "object"
    }
}
```

L’exemple ci-après référence un compte de stockage qui n’est pas déployé dans ce modèle, mais qui existe dans le même groupe de ressources que les ressources en cours de déploiement.

```json
"outputs": {
    "ExistingStorage": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
        "type" : "object"
    }
}
```

Vous pouvez récupérer une valeur spécifique à partir de l’objet renvoyé, comme l’URI du point de terminaison d’objet blob, tel qu’indiqué dans l’exemple suivant :

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    }
}
```

L’exemple ci-après référence un compte de stockage figurant dans un autre groupe de ressources.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    }
}
```

Les propriétés de l’objet renvoyé par la fonction **reference** varient selon le type de ressource. Pour afficher les noms et les valeurs des propriétés pour un type de ressource donné, créez un modèle simple qui retourne l’objet dans la section **outputs** . Si vous disposez déjà d’une ressource de ce type, votre modèle retourne simplement l’objet sans déployer de nouvelles ressources. Si vous ne disposez pas de ressources de ce type, votre modèle déploie seulement ce type et retourne l’objet. Ensuite, ajoutez ces propriétés à d’autres modèles qui doivent récupérer les valeurs de manière dynamique au cours du déploiement. 

<a id="resourcegroup" />

### <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Renvoie un objet qui représente le groupe de ressources actuel. 

L’objet renvoyé présente le format suivant :

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

L'exemple suivant utilise l'emplacement du groupe de ressources pour affecter l'emplacement d'un site web.

```json
"resources": [
   {
      "apiVersion": "2014-06-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

<a id="resourceid" />

### <a name="resourceid"></a>resourceId
`resourceId ([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Retourne l'identificateur unique d'une ressource. 

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |Non |Chaîne (au format GUID) |La valeur par défaut est l’abonnement actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre abonnement. |
| resourceGroupName |Non |String |La valeur par défaut est le groupe de ressources actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre groupe de ressources. |
| resourceType |Oui |String |Type de ressource, y compris l'espace de noms du fournisseur de ressources. |
| nom_ressource1 |Oui |String |Nom de la ressource. |
| nom_ressource2 |Non |String |Segment de nom de ressource suivant si la ressource est imbriquée. |

Vous utilisez cette fonction lorsque le nom de la ressource est ambigu ou non configuré dans le même modèle. L'identificateur est retourné au format suivant :

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

L'exemple suivant montre comment récupérer les ID de ressources pour un site web et une base de données. Le site web se trouve dans un groupe de ressources nommé **myWebsitesGroup** et la base de données se trouve dans le groupe de ressources actuel pour ce modèle.

```json
[resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
[resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
```

Souvent, vous devez utiliser cette fonction lorsque vous utilisez un compte de stockage ou un réseau virtuel se trouvant dans un autre groupe de ressources. Le compte de stockage ou le réseau virtuel peut être utilisé sur plusieurs groupes de ressources. Par conséquent, vous ne voulez pas les supprimer lors de la suppression d'un seul groupe de ressources. L'exemple suivant montre comment une ressource d'un groupe de ressources externe peut être facilement utilisée :

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

<a id="subscription" />

### <a name="subscription"></a>abonnement
`subscription()`

Renvoie des détails sur l’abonnement au format suivant :

```json
{
    "id": "/subscriptions/#####",
    "subscriptionId": "#####",
    "tenantId": "#####"
}
```

L’exemple suivant montre la fonction subscription appelée dans la section outputs. 

```json
"outputs": { 
  "exampleOutput": { 
      "value": "[subscription()]", 
      "type" : "object" 
  } 
} 
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir une description des sections d’un modèle Azure Resource Manager, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md)
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md)
* Pour effectuer une itération un nombre de fois spécifié pendant la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md)
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md)


