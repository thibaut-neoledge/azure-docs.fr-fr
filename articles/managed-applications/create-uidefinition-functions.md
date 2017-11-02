---
title: "Fonctions CreateUiDefinition des applications gérées Azure | Microsoft Docs"
description: "Décrit les fonctions à utiliser lors de l’élaboration de définitions d’interface utilisateur pour les applications gérées Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: dcf570ca4bdc8eacb7e4d7a8ff0011c8e07b7a40
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2017
---
# <a name="createuidefinition-functions"></a>Fonctions CreateUiDefinition
Cette section contient les signatures pour toutes les fonctions prises en charge de CreateUiDefinition.

Pour utiliser une fonction, encadrez la déclaration de crochets. Par exemple :

```json
"[function()]"
```

Les chaînes et autres fonctions peuvent être référencées en tant que paramètres pour une fonction, mais les chaînes doivent être encadrées de guillemets simples. Par exemple :

```json
"[fn1(fn2(), 'foobar')]"
```

Le cas échéant, vous pouvez référencer les propriétés de la sortie d’une fonction à l’aide de l’opérateur point. Par exemple :

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Fonctions de référencement
Ces fonctions peuvent être utilisées pour référencer les sorties à partir des propriétés ou d’un contexte de CreateUiDefinition.

### <a name="basics"></a>basics
Retourne les valeurs de sortie d’un élément qui est défini dans l’étape relative aux principes de base.

L’exemple suivant retourne la sortie de l’élément nommé `foo` dans l’étape relative aux principes de base :

```json
"[basics('foo')]"
```

### <a name="steps"></a>steps
Retourne les valeurs de sortie d’un élément qui est défini dans l’étape spécifiée. Pour obtenir les valeurs de sortie des éléments dans l’étape relative aux principes de base, utilisez plutôt `basics()`.

L’exemple suivant retourne la sortie de l’élément nommé `bar` dans l’étape nommée `foo` :

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Retourne l’emplacement sélectionné dans l’étape relative aux principes de base ou le contexte actuel.

L’exemple suivant peut retourner `"westus"` :

```json
"[location()]"
```

## <a name="string-functions"></a>Fonctions de chaîne
Ces fonctions peuvent uniquement être utilisées avec des chaînes JSON.

### <a name="concat"></a>concat
Concatène une ou plusieurs chaînes.

Par exemple, si la valeur de sortie `element1` est `"bar"`, cet exemple retourne la chaîne `"foobar!"` :

```json
"[concat('foo', steps('step1').element1), '!']"
```

### <a name="substring"></a>substring
Retourne la sous-chaîne de la chaîne spécifiée. La sous-chaîne commence à l’index spécifié et présente la longueur spécifiée.

L’exemple suivant retourne `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>replace
Retourne une chaîne dans laquelle toutes les occurrences de la chaîne spécifiée dans la chaîne actuelle sont remplacées par une autre chaîne.

L’exemple suivant retourne `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Génère une chaîne globale unique (GUID).

L’exemple suivant peut retourner `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` :

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Retourne une chaîne convertie en minuscules.

L’exemple suivant retourne `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Retourne une chaîne convertie en majuscules.

L’exemple suivant retourne `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Fonctions de collection
Ces fonctions peuvent être utilisées avec les collections, comme les chaînes JSON, les tableaux et les objets.

### <a name="contains"></a>contains
Retourne `true` si une chaîne contient la sous-chaîne spécifiée, un tableau contient la valeur spécifiée, ou un objet contient la clé spécifiée.

#### <a name="example-1-string"></a>Exemple 1 : chaîne
L’exemple suivant retourne `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Exemple 2 : tableau
Supposons que `element1` retourne `[1, 2, 3]`. L’exemple suivant retourne `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Example 3 : objet
Supposons que `element1` retourne :

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

L’exemple suivant retourne `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Retourne le nombre de caractères dans une chaîne, le nombre de valeurs dans un tableau, ou le nombre de clés dans un objet.

#### <a name="example-1-string"></a>Exemple 1 : chaîne
L’exemple suivant retourne `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Exemple 2 : tableau
Supposons que `element1` retourne `[1, 2, 3]`. L’exemple suivant retourne `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Example 3 : objet
Supposons que `element1` retourne :

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

L’exemple suivant retourne `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
Retourne `true` si la chaîne, le tableau ou l’objet est null ou vide.

#### <a name="example-1-string"></a>Exemple 1 : chaîne
L’exemple suivant retourne `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Exemple 2 : tableau
Supposons que `element1` retourne `[1, 2, 3]`. L’exemple suivant retourne `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Example 3 : objet
Supposons que `element1` retourne :

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

L’exemple suivant retourne `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Exemple 4 : null et non défini
Supposons que `element1` est `null` ou n’est pas défini. L’exemple suivant retourne `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>first
Retourne le premier caractère de la chaîne spécifiée, la première valeur du tableau spécifié, ou la première clé et la valeur de l’objet spécifié.

#### <a name="example-1-string"></a>Exemple 1 : chaîne
L’exemple suivant retourne `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Exemple 2 : tableau
Supposons que `element1` retourne `[1, 2, 3]`. L’exemple suivant retourne `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Example 3 : objet
Supposons que `element1` retourne :

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
L’exemple suivant retourne `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>last
Retourne le dernier caractère de la chaîne spécifiée, la dernière valeur du tableau spécifié, ou la dernière clé et la valeur de l’objet spécifié.

#### <a name="example-1-string"></a>Exemple 1 : chaîne
L’exemple suivant retourne `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Exemple 2 : tableau
Supposons que `element1` retourne `[1, 2, 3]`. L’exemple suivant retourne `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Example 3 : objet
Supposons que `element1` retourne :

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

L’exemple suivant retourne `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>take
Retourne un nombre spécifié de caractères contigus à partir du début de la chaîne, un nombre spécifié de valeurs contiguës à partir du début du tableau, ou un nombre spécifié de valeurs et clés contiguës à partir du début de l’objet.

#### <a name="example-1-string"></a>Exemple 1 : chaîne
L’exemple suivant retourne `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemple 2 : tableau
Supposons que `element1` retourne `[1, 2, 3]`. L’exemple suivant retourne `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Example 3 : objet
Supposons que `element1` retourne :

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

L’exemple suivant retourne `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>skip
Ignore un nombre spécifié d’éléments dans une collection, puis retourne les éléments restants.

#### <a name="example-1-string"></a>Exemple 1 : chaîne
L’exemple suivant retourne `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemple 2 : tableau
Supposons que `element1` retourne `[1, 2, 3]`. L’exemple suivant retourne `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Example 3 : objet
Supposons que `element1` retourne :

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
L’exemple suivant retourne `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Fonctions logiques
Ces fonctions peuvent être utilisées dans des instructions conditionnelles. Certaines fonctions ne peuvent pas prendre en charge tous les types de données JSON.

### <a name="equals"></a>equals
Retourne `true` si les deux paramètres ont le même type et la même valeur. Cette fonction prend en charge tous les types de données JSON.

L’exemple suivant retourne `true`:

```json
"[equals(0, 0)]"
```

L’exemple suivant retourne `true`:

```json
"[equals('foo', 'foo')]"
```

L’exemple suivant retourne `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Retourne `true` si le premier paramètre est strictement inférieur au second paramètre. Cette fonction prend en charge les paramètres de type nombre et chaîne uniquement.

L’exemple suivant retourne `true`:

```json
"[less(1, 2)]"
```

L’exemple suivant retourne `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Retourne `true` si le premier paramètre est inférieur ou égal au second paramètre. Cette fonction prend en charge les paramètres de type nombre et chaîne uniquement.

L’exemple suivant retourne `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Retourne `true` si le premier paramètre est strictement supérieur au second paramètre. Cette fonction prend en charge les paramètres de type nombre et chaîne uniquement.

L’exemple suivant retourne `false`:

```json
"[greater(1, 2)]"
```

L’exemple suivant retourne `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Retourne `true` si le premier paramètre est supérieur ou égal au second paramètre. Cette fonction prend en charge les paramètres de type nombre et chaîne uniquement.

L’exemple suivant retourne `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>and
Retourne `true` si tous les paramètres correspondent à `true`. Cette fonction prend en charge plusieurs paramètres de type booléen uniquement.

L’exemple suivant retourne `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

L’exemple suivant retourne `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>ou
Retourne `true` si au moins l’un des paramètres correspond à `true`. Cette fonction prend en charge plusieurs paramètres de type booléen uniquement.

L’exemple suivant retourne `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

L’exemple suivant retourne `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Retourne `true` si le paramètre correspond à `false`. Cette fonction prend en charge les paramètres de type booléen uniquement.

L’exemple suivant retourne `true`:

```json
"[not(false)]"
```

L’exemple suivant retourne `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
Retourne la valeur du premier paramètre non null. Cette fonction prend en charge tous les types de données JSON.

Supposons que `element1` et `element2` ne soient pas définis. L’exemple suivant retourne `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Fonctions de conversion
Ces fonctions peuvent être utilisées pour convertir des valeurs entre des types de données JSON et des encodages.

### <a name="int"></a>int
Convertit le paramètre en entier. Cette fonction prend en charge les paramètres de type nombre et chaîne.

L’exemple suivant retourne `1`:

```json
"[int('1')]"
```

L’exemple suivant retourne `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Convertit le paramètre en nombre à virgule flottante. Cette fonction prend en charge les paramètres de type nombre et chaîne.

L’exemple suivant retourne `1.0`:

```json
"[float('1.0')]"
```

L’exemple suivant retourne `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>string
Convertit le paramètre en chaîne. Cette fonction prend en charge les paramètres de tous les types de données JSON.

L’exemple suivant retourne `"1"`:

```json
"[string(1)]"
```

L’exemple suivant retourne `"2.9"`:

```json
"[string(2.9)]"
```

L’exemple suivant retourne `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

L’exemple suivant retourne `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>valeur booléenne
Convertit le paramètre en booléen. Cette fonction prend en charge les paramètres de type nombre, chaîne et booléen. Tout comme les booléens en JavaScript, toutes les valeurs, à l’exception de `0` ou `'false'`, retournent `true`.

L’exemple suivant retourne `true`:

```json
"[bool(1)]"
```

L’exemple suivant retourne `false`:

```json
"[bool(0)]"
```

L’exemple suivant retourne `true`:

```json
"[bool(true)]"
```

L’exemple suivant retourne `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Convertit le paramètre en type natif. En d’autres termes, cette fonction est l’inverse de `string()`. Cette fonction prend en charge les paramètres de type chaîne uniquement.

L’exemple suivant retourne `1`:

```json
"[parse('1')]"
```

L’exemple suivant retourne `true`:

```json
"[parse('true')]"
```

L’exemple suivant retourne `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

L’exemple suivant retourne `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Encode le paramètre vers une chaîne codée en base 64. Cette fonction prend en charge les paramètres de type chaîne uniquement.

L’exemple suivant retourne `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Encode le paramètre à partir d’une chaîne codée en base 64. Cette fonction prend en charge les paramètres de type chaîne uniquement.

L’exemple suivant retourne `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Encode le paramètre vers une chaîne d’URL encodée. Cette fonction prend en charge les paramètres de type chaîne uniquement.

L’exemple suivant retourne `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Encode le paramètre à partir d’une chaîne d’URL encodée. Cette fonction prend en charge les paramètres de type chaîne uniquement.

L’exemple suivant retourne `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Fonctions mathématiques
### <a name="add"></a>ajouter
Ajoute deux nombres et retourne le résultat.

L’exemple suivant retourne `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Soustrait le second nombre du premier nombre et retourne le résultat.

L’exemple suivant retourne `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Multiplie deux nombres et retourne le résultat.

L’exemple suivant retourne `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Divise le premier nombre par le second nombre et retourne le résultat. Le résultat est toujours un entier.

L’exemple suivant retourne `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Divise le premier nombre par le second nombre et retourne le reste.

L’exemple suivant retourne `0`:

```json
"[mod(6, 3)]"
```

L’exemple suivant retourne `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>Min
Retourne le plus petit des deux nombres.

L’exemple suivant retourne `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Retourne le plus grand des deux nombres.

L’exemple suivant retourne `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
Génère une séquence de nombres entiers dans la plage spécifiée.

L’exemple suivant retourne `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Retourne un nombre entier aléatoire dans la plage spécifiée. Cette fonction ne génère pas de nombres aléatoires sécurisés par chiffrement.

L’exemple suivant peut retourner `42` :

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>floor
Retourne le plus grand nombre entier qui est inférieur ou égal au nombre spécifié.

L’exemple suivant retourne `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Retourne le plus grand nombre entier qui est supérieur ou égal au nombre spécifié.

L’exemple suivant retourne `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Fonctions de date
### <a name="utcnow"></a>utcNow
Retourne une chaîne au format ISO 8601 indiquant la date et l’heure de l’ordinateur local.

L’exemple suivant peut retourner `"1990-12-31T23:59:59.000Z"` :

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Ajoute un nombre entier de secondes à l’horodatage spécifié.

L’exemple suivant retourne `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Ajoute un nombre entier de minutes à l’horodatage spécifié.

L’exemple suivant retourne `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Ajoute un nombre entier d’heures à l’horodatage spécifié.

L’exemple suivant retourne `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour découvrir Azure Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

