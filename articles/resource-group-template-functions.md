<properties
   pageTitle="Fonctions des modèles Resource Manager | Microsoft Azure"
   description="Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour récupérer des valeurs, utiliser des chaînes et des valeurs numériques, et récupérer des informations sur le déploiement."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/11/2016"
   ms.author="tomfitz"/>

# Fonctions des modèles Azure Resource Manager

Cette rubrique décrit toutes les fonctions que vous pouvez utiliser dans un modèle Azure Resource Manager.

Les fonctions des modèles et leurs paramètres ne respectent pas la casse. Par exemple, le Gestionnaire de ressources résout **variables('var1')** et **VARIABLES('VAR1')** de la même façon. Lors de l’évaluation, la fonction préserve la casse sauf si elle la modifie expressément (toUpper ou toLower, par exemple). Certains types de ressources peuvent avoir des exigences de casse, quelle que soit la manière dont les fonctions sont évaluées.

## Fonctions numériques

Resource Manager fournit les expressions ci-après pour travailler avec des entiers :

- [ajouter](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### ajouter

**add(operand1, operand2)**

Retourne la somme des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Premier entier à ajouter.
| operand2 | Oui | Deuxième entier à ajouter.

L’exemple suivant ajoute deux paramètres.

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

<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

Retourne l'index actuel d'une boucle d'itération.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| Offset | Non | Montant à ajouter à la valeur de l’itération actuelle.

Cette fonction est toujours utilisée avec un objet **copy**. Pour obtenir une description complète d’exemples d’utilisation de l’expression **copyIndex**, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

L’exemple suivant montre une boucle de copie ainsi que la valeur d’index incluse dans le nom.

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


<a id="div" />
### div

**div(operand1, operand2)**

Retourne la division entière des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Entier à diviser.
| operand2 | Oui | Entier utilisé pour diviser. Ne peut pas être 0.

L’exemple suivant divise un paramètre par un autre paramètre.

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

<a id="int" />
### int

**int(valueToConvert)**

Convertit la valeur spécifiée en entier.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| valueToConvert | Oui | Valeur à convertir en entier. Le type de valeur peut uniquement être une chaîne ou un entier.

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en entier.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="mod" />
### mod

**mod(operand1, operand2)**

Retourne le reste de la division entière des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Entier à diviser.
| operand2 | Oui | Entier utilisé pour diviser. Doit être différent de 0.

L’exemple suivant renvoie le reste de la division d’un paramètre par un autre paramètre.

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

<a id="mul" />
### mul

**mul(operand1, operand2)**

Retourne la multiplication des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Premier entier à multiplier.
| operand2 | Oui | Deuxième entier à multiplier.

L’exemple suivant multiplie un paramètre par un autre paramètre.

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

<a id="sub" />
### sub

**sub(operand1, operand2)**

Retourne la soustraction des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Entier auquel est appliquée la soustraction.
| operand2 | Oui | Entier soustrait.

L’exemple suivant soustrait un paramètre à un autre paramètre.

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

## Fonctions de chaîne

Resource Manager fournit les fonctions ci-après pour travailler avec des chaînes de caractères :

- [base64](#base64)
- [concat](#concat)
- [length](#lengthstring)
- [padLeft](#padleft)
- [replace](#replace)
- [skip](#skipstring)
- [split](#split)
- [string](#string)
- [substring](#substring)
- [take](#takestring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [découper](#trim)
- [uniqueString](#uniquestring)
- [URI](#uri)


<a id="base64" />
### base64

**base64 (chaîne\_entrée)**

Retourne la représentation en base 64 de la chaîne d'entrée.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne\_entrée | Oui | Valeur de chaîne à retourner sous la forme d'une représentation en base 64.

L’exemple suivant montre comment utiliser la fonction base64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### concat - string

**concat (string1, string2, string3, ...)**

Combine plusieurs valeurs de chaîne et renvoie la chaîne concaténée.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| string1 | Oui | Valeur de chaîne à concaténer.
| chaînes supplémentaires | Non | Valeurs de chaîne à concaténer.

Cette fonction peut prendre n’importe quel nombre d’arguments et accepter à la fois des chaînes ou des tableaux pour les paramètres. Pour obtenir un exemple de concaténation des tableaux, consultez [concat - array](#concatarray).

L’exemple suivant montre comment combiner plusieurs valeurs pour retourner au final une chaîne concaténée.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }


<a id="lengthstring" />
### length - string

**length(string)**

Renvoie le nombre de caractères dans une chaîne.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| string | Oui | Valeur de chaîne à utiliser pour l’obtention du nombre de caractères.

Pour découvrir un exemple d’utilisation de longueur avec un tableau, consultez [length - array](#length).

L’exemple suivant renvoie le nombre de caractères dans une chaîne.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }
        

<a id="padleft" />
### padLeft

**padLeft(valeur\_à\_remplir, longueur\_totale, caractère\_de\_remplissage)**

Renvoie une chaîne alignée à droite en lui ajoutant des caractères sur la gauche jusqu’à ce que la longueur totale spécifiée ait été atteinte.
  
| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| valeur\_à\_remplir | Oui | Chaîne ou entier à aligner à droite.
| longueur\_totale | Oui | Nombre total de caractères de la chaîne renvoyée.
| caractère\_de\_remplissage | Non | Caractère de remplissage à insérer sur la gauche jusqu’à ce que la longueur totale soit atteinte. La valeur par défaut est un espace.

L’exemple ci-après indique comment remplir la valeur de paramètre fournie par l’utilisateur avec le caractère zéro jusqu’à ce que la chaîne atteigne 10 caractères. Si la valeur de paramètre d’origine comporte plus de 10 caractères, aucun caractère n’est ajouté.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### replace

**replace(chaîne\_initiale, ancien\_caractère, nouveau\_caractère)**

Renvoie une nouvelle chaîne dans laquelle toutes les instances d’un caractère de la chaîne initiale spécifiée ont été remplacées par un autre caractère.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne\_initiale | Oui | Chaîne pour laquelle toutes les instances d’un caractère sont remplacées par un autre caractère.
| ancien\_caractère | Oui | Caractère à supprimer de la chaîne initiale.
| nouveau\_caractère | Oui | Caractère à ajouter à la place du caractère supprimé.

L’exemple ci-après indique comment supprimer tous les tirets de la chaîne fournie par l’utilisateur.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="skipstring" />
### skip - string
**skip(originalValue, numberToSkip)**

Renvoie une chaîne avec tous les caractères après le nombre spécifié dans la chaîne.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| originalValue | Oui | Chaîne à utiliser pour ignorer les caractères.
| numberToSkip | Oui | Nombre de caractères à ignorer. Si cette valeur est inférieure ou égale à 0, tous les caractères de la chaîne sont renvoyés. Si elle est supérieure à la longueur de la chaîne, une chaîne vide est renvoyée. 

Pour découvrir un exemple de caractères ignorés avec un tableau, consultez [skip - array](#skip).

L’exemple suivant ignore le nombre spécifié de caractères de la chaîne.

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


<a id="split" />
### split

**split(inputString, delimiterString)**

**split(inputString, delimiterArray)**

Renvoie un tableau de chaînes qui contient les sous-chaînes de la chaîne d’entrée séparées par les délimiteurs spécifiés.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne\_entrée | Oui | Chaîne à fractionner.
| delimiter | Oui | Le séparateur à utiliser. Peut être une chaîne unique ou un tableau de chaînes.

L'exemple suivant fractionne la chaîne d'entrée en la séparant par une virgule.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

L’exemple suivant fractionne la chaîne d’entrée par une virgule ou un point-virgule.

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

<a id="string" />
### string

**string(valueToConvert)**

Convertit la valeur spécifiée en chaîne.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| valueToConvert | Oui | Valeur à convertir en chaîne. N’importe quel type de valeur peut être converti, y compris les objets et des tableaux.

L’exemple ci-après convertit les valeurs de paramètre fournies par l’utilisateur en chaîne.

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

<a id="substring" />
### substring

**substring(chaîne\_à\_analyser, index\_début, longueur)**

Retourne une sous-chaîne qui commence à la position de caractère spécifiée et qui contient le nombre de caractères spécifié.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne\_à\_analyser | Oui | La chaîne d’origine de laquelle la sous-chaîne est extraite.
| index\_début | Non | La position de caractère (commençant à zéro) de la sous-chaîne.
| longueur | Non | Le nombre de caractères de la sous-chaîne.

L’exemple suivant extrait les trois premiers caractères d’un paramètre.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="takestring" />
### take - string
**take(originalValue, numberToTake)**

Renvoie une chaîne avec le nombre de caractères spécifié à partir du début de la chaîne.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| originalValue | Oui | Chaîne à partir de laquelle il faut tirer les caractères.
| numberToTake | Oui | Nombre de caractères à tirer. Si cette valeur est inférieure ou égale à 0, une chaîne vide est renvoyée. Si elle est supérieure à la longueur de la chaîne donnée, tous les caractères de la chaîne sont renvoyés.

Pour découvrir un exemple de caractères tirés avec un tableau, consultez [take - array](#take).

L’exemple suivant tire le nombre spécifié de caractères de la chaîne.

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

<a id="tolower" />
### toLower

**toLower(chaîne\_à\_modifier)**

Convertit la chaîne spécifiée en minuscules.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne\_à\_modifier | Oui | Chaîne à convertir en minuscules.

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en minuscules.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### toUpper

**toUpper(chaîne\_à\_modifier)**

Convertit la chaîne spécifiée en majuscules.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne\_à\_modifier | Oui | Chaîne à convertir en majuscules.

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en majuscules.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### découper

**découper (stringToTrim)**

Supprime tous les espaces de début et de fin de la chaîne indiquée.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| stringToTrim | Oui | Chaîne à découper.

L’exemple suivant supprime les espaces à partir de la valeur de paramètre indiquée par l’utilisateur.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### uniqueString

**uniqueString (baseString, ...)**

Crée une chaîne unique basée sur les valeurs fournies en tant que paramètres.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| baseString | Oui | Chaîne utilisée dans la fonction de hachage pour créer une chaîne unique.
| paramètres supplémentaires le cas échéant | Non | Vous pouvez ajouter autant de chaînes que nécessaire pour créer la valeur qui spécifie le niveau d’unicité.

Cette fonction est utile lorsque vous avez besoin de créer un nom unique pour une ressource. Vous fournissez des valeurs de paramètre qui représentent le niveau d'unicité pour le résultat. Vous pouvez spécifier si le nom est unique pour votre abonnement, le groupe de ressources ou le déploiement.

La valeur renvoyée n’est pas une chaîne aléatoire, mais plutôt le résultat d’une fonction de hachage. La valeur renvoyée comprend 13 caractères. Son unicité globale n'est pas garantie Il se peut que vous souhaitiez associer un préfixe de votre convention d’affectation de noms à la valeur pour créer un nom plus facile à reconnaître. L’exemple suivant montre le format de la valeur renvoyée. Évidemment, la valeur réelle varie en fonction des paramètres fournis.

    tcvhiyu5h2o5o

Les exemples suivants montrent comment utiliser uniqueString afin de créer une valeur unique pour des niveaux couramment utilisés.

Unique au niveau de l’abonnement

    "[uniqueString(subscription().subscriptionId)]"

Unique au niveau du groupe de ressources

    "[uniqueString(resourceGroup().id)]"

Unique au niveau du déploiement pour un groupe de ressources

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
L'exemple suivant montre comment créer un nom unique pour un compte de stockage basé sur votre groupe de ressources.

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...



<a id="uri" />
### URI

**URI (baseUri, relativeUri)**

Crée un URI absolu en combinant le baseUri et la chaîne relativeUri.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| baseUri | Oui | La chaîne d’URI de base.
| relativeUri | Oui | La chaîne d’URI relatif à ajouter à la chaîne d’URI de base.

La valeur du paramètre **baseUri** peut inclure un fichier spécifique, mais seul le chemin de base est utilisé lors de la construction de l’URI. Par exemple, si vous passez **http://contoso.com/resources/azuredeploy.json** comme paramètre baseUri, l’URI de base résultant est **http://contoso.com/resources/**.

L’exemple suivant montre comment créer un lien vers un modèle imbriqué en fonction de la valeur du modèle parent.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## Fonctions de tableau

Resource Manager fournit les fonctions ci-après pour travailler avec des valeurs de tableau :

- [concat](#concatarray)
- [length](#length)
- [skip](#skip)
- [take](#take)

Pour obtenir un tableau de valeurs de chaîne délimitée par une valeur, consultez [split](#split).

<a id="concatarray" />
### concat - array

**concat (array1, array2, array3, ...)**

Combine plusieurs tableaux et renvoie le tableau concaténé.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| array1 | Oui | Tableau à concaténer.
| tableaux supplémentaires | Non | Tableaux à concaténer.

Cette fonction peut prendre n’importe quel nombre d’arguments et accepter à la fois des chaînes ou des tableaux pour les paramètres. Pour obtenir un exemple de concaténation des valeurs de chaîne, consultez [concat - string](#concat).

L’exemple suivant montre comment combiner deux tableaux.

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="length" />
### length - array

**length(array)**

Retourne le nombre d'éléments dans un tableau.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| array | Oui | Tableau à utiliser pour obtenir le nombre d’éléments.

Vous pouvez utiliser cette fonction avec un tableau pour spécifier le nombre d’itérations lors de la création de ressources. Dans l’exemple ci-après, le paramètre **siteNames** fait référence à un tableau de noms à utiliser lors de la création de sites web.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Pour plus d’informations sur l’utilisation de cette fonction avec un tableau, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

Pour découvrir un exemple d’utilisation de longueur avec une valeur de chaîne, consultez [length - string](#lengthstring).

<a id="skip" />
### skip - array
**skip(originalValue, numberToSkip)**

Renvoie un tableau avec tous les éléments après le nombre spécifié dans le tableau.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| originalValue | Oui | Tableau à utiliser pour ignorer les éléments.
| numberToSkip | Oui | Nombre d’éléments à ignorer. Si cette valeur est inférieure ou égale à 0, tous les éléments du tableau sont renvoyés. Si elle est supérieure à la longueur du tableau, un tableau vide est renvoyé. 

Pour découvrir un exemple d’éléments ignorés avec une chaîne, consultez [skip - string](#skipstring).

L’exemple suivant ignore le nombre spécifié d’éléments du tableau.

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

<a id="take" />
### take - array
**take(originalValue, numberToTake)**

Renvoie un tableau avec le nombre spécifié d’éléments depuis le début du tableau.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| originalValue | Oui | Tableau à partir duquel les éléments sont tirés.
| numberToTake | Oui | Nombre d’éléments à tirer. Si cette valeur est inférieure ou égale à 0, un tableau vide est renvoyé. Si elle est supérieure à la longueur du tableau donné, tous les éléments du tableau sont renvoyés.

Pour découvrir un exemple d’éléments de ce type avec une chaîne, consultez [take - string](#takestring).

L’exemple suivant prend le nombre spécifié d’éléments du tableau.

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

## Fonctions de valeur de déploiement

Resource Manager offre les fonctions ci-après pour l’obtention de valeurs à partir des sections du modèle et de valeurs associées au déploiement :

- [deployment](#deployment)
- [parameters](#parameters)
- [variables](#variables)

Pour obtenir des valeurs de ressources, de groupes de ressources ou d’abonnements, consultez [Fonctions de ressource](#resource-functions).

<a id="deployment" />
### deployment

**deployment()**

Renvoie des informations sur l’opération de déploiement actuelle.

Cette fonction retourne l’objet transmis au cours du déploiement. Les propriétés de l’objet renvoyé diffèrent selon que l’objet de déploiement est passé sous forme de lien ou d’objet inline.

Quand l’objet de déploiement est passé inline, comme lors de l’utilisation du paramètre **-TemplateFile** dans Azure PowerShell pour pointer vers un fichier local, l’objet renvoyé a le format suivant :

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

Quand l’objet est passé comme lien, par exemple lors de l’utilisation du paramètre **-TemplateUri** pour pointer vers un objet distant, l’objet est retourné dans le format suivant.

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

L’exemple suivant montre comment utiliser deployment() pour établir une liaison à un autre modèle en fonction de l’URI du modèle parent.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  

<a id="parameters" />
### parameters

**parameters (nom\_paramètre)**

Retourne une valeur de paramètre. Le nom de paramètre spécifié doit être défini dans la section parameters du modèle.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_paramètre | Oui | Nom du paramètre à retourner.

L'exemple suivant montre une utilisation simplifiée de la fonction parameters.

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

<a id="variables" />
### variables

**variables (nom\_variable)**

Retourne la valeur de la variable. Le nom de variable spécifié doit être défini dans la section variables du modèle.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_variable | Oui | Nom de la variable à retourner.

L’exemple suivant utilise une valeur de variable.

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

## Fonctions de ressource

Resource Manager offre les fonctions ci-après pour obtenir des valeurs de ressource :

- [listKeys and list{Value}](#listkeys)
- [fournisseurs](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [abonnement](#subscription)

Pour obtenir des valeurs de paramètres, de variables ou du déploiement actuel, consultez [Fonctions de valeur de déploiement](#deployment-value-functions).

<a id="listkeys" /> <a id="list" />
### listKeys and list{Value}

**listKeys (nom\_ressource ou identificateur\_ressource, version\_api)**

**list{Value} (resourceName or resourceIdentifier, apiVersion)**

Renvoie les valeurs pour n’importe quel type de ressource qui prend en charge l’opération list. L’utilisation la plus courante est **listKeys**.
  
| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_ressource ou identificateur\_ressource | Oui | Identificateur unique pour la ressource.
| apiVersion | Oui | Version d'API de l'état d'exécution des ressources.

Toute opération qui commence par **list** peut être utilisée en tant que fonction dans votre modèle. Les opérations disponibles incluent **listKeys**, mais également les opérations telles que **list**, **listAdminKeys** et **listStatus**. Pour déterminer les types de ressources qui ont une opération de liste, utilisez la commande PowerShell suivante.

    Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

Ou, récupérez la liste avec l’interface de ligne de commande Azure. L’exemple suivant récupère toutes les opérations pour **apiapps** et utilise l’utilitaire JSON [jq](http://stedolan.github.io/jq/download/) pour filtrer uniquement les opérations list.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains("list"))"

L’identificateur de ressource peut être spécifié à l’aide de la [fonction resourceId](./#resourceid) ou en utilisant le format **{providerNamespace}/{resourceType}/{resourceName}**.

L’exemple suivant montre comment renvoyer les clés primaires et secondaires à partir d’un compte de stockage dans la section outputs.

    "outputs": { 
      "listKeysOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
        "type" : "object" 
      } 
    } 

L’objet renvoyé par listKeys a le format suivant :

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

<a id="providers" />
### fournisseurs

**fournisseurs (espacedenoms\_fournisseur, [type\_ressource])**

Renvoie des informations sur un fournisseur de ressources et les types de ressources qu’il prend en charge. Si vous ne fournissez pas un type de ressource, la fonction renvoie tous les types pris en charge pour le fournisseur de ressources.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| espacedenoms\_fournisseur | Oui | Espace de noms du fournisseur.
| resourceType | Non | Type de ressource dans l'espace de noms spécifié.

Chaque type pris en charge est renvoyé au format suivant. Le classement du tableau n’est pas garanti.

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

L'exemple suivant montre comment utiliser la fonction provider :

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

<a id="reference" />
### reference

**reference (nom\_ressource ou identificateur\_ressource, [version\_api])**

Renvoie un objet représentant l’état d’exécution d’une autre ressource.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_ressource ou identificateur\_ressource | Oui | Nom ou identificateur unique d’une ressource.
| apiVersion | Non | Version d’API de la ressource spécifiée. Incluez ce paramètre lorsque la ressource n’est pas approvisionnée dans le même modèle.

La fonction **reference** dérive sa valeur d'un état d'exécution, et ne peut donc pas être utilisée dans la section variables. Elle peut être utilisée dans la section outputs d'un modèle.

En utilisant la fonction « reference », vous déclarez de manière implicite qu’une ressource dépend d’une autre ressource si la ressource référencée est configurée dans le même modèle. Vous n’avez pas besoin d’utiliser également la propriété **dependsOn**. La fonction n’est pas évaluée tant que le déploiement de la ressource référencée n’est pas terminé.

L’exemple ci-après référence un compte de stockage déployé dans le même modèle.

    "outputs": {
		"NewStorage": {
			"value": "[reference(parameters('storageAccountName'))]",
			"type" : "object"
		}
	}

L’exemple ci-après référence un compte de stockage qui n’est pas déployé dans ce modèle, mais qui existe dans le même groupe de ressources que les ressources en cours de déploiement.

    "outputs": {
		"ExistingStorage": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
			"type" : "object"
		}
	}

Vous pouvez récupérer une valeur spécifique à partir de l’objet renvoyé, comme l’URI du point de terminaison d’objet blob, tel qu’indiqué dans l’exemple suivant.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

L’exemple ci-après référence un compte de stockage figurant dans un autre groupe de ressources.

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

Les propriétés de l’objet renvoyé varient en fonction du type de ressource.

<a id="resourcegroup" />
### resourceGroup

**resourceGroup()**

Renvoie un objet qui représente le groupe de ressources actuel.

L’objet renvoyé présente le format suivant :

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

L'exemple suivant utilise l'emplacement du groupe de ressources pour affecter l'emplacement d'un site web.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### resourceId

**resourceId ([ID\_abonnement], [nom\_groupe\_ressource], type\_ressource, nom\_ressource1, [nom\_ressource2]...)**

Retourne l'identificateur unique d'une ressource.
      
| Paramètre | Requis | Description
| :---------------: | :------: | :----------
| subscriptionId | Non | La valeur par défaut est l’abonnement actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre abonnement.
| resourceGroupName | Non | La valeur par défaut est le groupe de ressources actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre groupe de ressources.
| resourceType | Oui | Type de ressource, y compris l'espace de noms du fournisseur de ressources.
| nom\_ressource1 | Oui | Nom de la ressource.
| nom\_ressource2 | Non | Segment de nom de ressource suivant si la ressource est imbriquée.

Vous utilisez cette fonction lorsque le nom de la ressource est ambigu ou non configuré dans le même modèle. L'identificateur est retourné au format suivant :

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

L'exemple suivant montre comment récupérer les ID de ressources pour un site web et une base de données. Le site web se trouve dans un groupe de ressources nommé **myWebsitesGroup** et la base de données se trouve dans le groupe de ressources actuel pour ce modèle.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
    
Souvent, vous devez utiliser cette fonction lorsque vous utilisez un compte de stockage ou un réseau virtuel se trouvant dans un autre groupe de ressources. Le compte de stockage ou le réseau virtuel peut être utilisé sur plusieurs groupes de ressources. Par conséquent, vous ne voulez pas les supprimer lors de la suppression d'un seul groupe de ressources. L'exemple suivant montre comment une ressource d'un groupe de ressources externe peut être facilement utilisée :

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

<a id="subscription" />
### abonnement

**subscription()**

Renvoie des détails sur l’abonnement au format suivant.

    {
        "id": "/subscriptions/#####",
        "subscriptionId": "#####",
        "tenantId": "#####"
    }

L’exemple suivant montre la fonction subscription appelée dans la section outputs.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## Étapes suivantes
- Pour obtenir une description des sections d’un modèle Azure Resource Manager, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
- Pour effectuer une itération un nombre de fois spécifié pendant la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
- Pour savoir comment déployer le modèle que vous avez créé, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0817_2016-->