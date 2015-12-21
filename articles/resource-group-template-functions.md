<properties
   pageTitle="Expressions de modèle Resource Manager | Microsoft Azure"
   description="Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour récupérer des valeurs, utiliser des chaînes et des valeurs numériques, et récupérer des informations sur le déploiement."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/07/2015"
   ms.author="tomfitz"/>

# Expressions de modèle Azure Resource Manager

Cette rubrique décrit toutes les expressions que vous pouvez utiliser dans un modèle Azure Resource Manager.

Les expressions de modèle et leurs paramètres ne respectent pas la casse. Par exemple, le Gestionnaire de ressources résout **variables('var1')** et **VARIABLES('VAR1')** de la même façon. Lors de l’évaluation, l’expression préservera la casse, sauf si elle la modifie expressément (toUpper ou toLower, par exemple). Certains types de ressources peuvent avoir des exigences de casse, quelle que soit la manière dont les expressions sont évaluées.

## Expressions numériques

Resource Manager vous offre les expressions ci-après pour travailler avec des entiers :

- [ajouter](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [length](#length)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### ajouter

**add(operand1, operand2)**

Retourne la somme des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Premier opérande à utiliser.
| operand2 | Oui | Deuxième opérande à utiliser.


<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

Retourne l'index actuel d'une boucle d'itération.

Cette expression est toujours utilisée avec un objet **copy**. Pour découvrir des exemples d’utilisation de l’expression **copyIndex**, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).


<a id="div" />
### div

**div(operand1, operand2)**

Retourne la division entière des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Nombre à diviser.
| operand2 | Oui | Nombre utilisé pour diviser. Doit être différent de 0.


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


<a id="length" />
### length

**length (tableau ou chaîne)**

Retourne le nombre d’éléments dans un tableau ou le nombre de caractères dans une chaîne. Vous pouvez utiliser cette fonction avec un tableau pour spécifier le nombre d’itérations lors de la création de ressources. Dans l’exemple ci-après, le paramètre **siteNames** fait référence à un tableau de noms à utiliser lors de la création de sites web.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Pour plus d’informations sur l’utilisation de cette fonction avec un tableau, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

Vous pouvez aussi l’utiliser avec une chaîne :

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }


<a id="mod" />
### mod

**mod(operand1, operand2)**

Retourne le reste de la division entière des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Nombre à diviser.
| operand2 | Oui | Nombre utilisé pour diviser. Doit être différent de 0.



<a id="mul" />
### mul

**mul(operand1, operand2)**

Retourne la multiplication des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Premier opérande à utiliser.
| operand2 | Oui | Deuxième opérande à utiliser.


<a id="sub" />
### sub

**sub(operand1, operand2)**

Retourne la soustraction des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Nombre auquel sera appliquée la soustraction.
| operand2 | Oui | Nombre à soustraire.


## Expressions de chaîne

Resource Manager vous offre les expressions ci-après pour travailler avec des chaînes :

- [base64](#base64)
- [concat](#concat)
- [padLeft](#padleft)
- [replace](#replace)
- [split](#split)
- [string](#string)
- [toLower](#tolower)
- [toUpper](#toupper)
- [découper](#trim)
- [uniqueString](#uniquestring)
- [URI](#uri)

Pour obtenir le nombre de caractères d’une chaîne ou d’un tableau, voir l’expression [length](#length).

<a id="base64" />
### base64

**base64 (chaîne\_entrée)**

Retourne la représentation en base 64 de la chaîne d'entrée.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne\_entrée | Oui | Valeur de chaîne à retourner sous la forme d'une représentation en base 64.

L'exemple suivant montre comment utiliser la fonction base64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### concat

**concat (arg1, arg2, arg3, ...)**

Combine plusieurs valeurs de chaîne et retourne la valeur de chaîne obtenue. Cette fonction peut prendre n'importe quel nombre d'arguments.

L'exemple suivant montre comment combiner plusieurs valeurs pour en retourner une seule.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

<a id="padleft" />
### padLeft

**padLeft(chaîne\_à\_remplir, longueur\_totale, caractère\_de\_remplissage)**

Renvoie une chaîne alignée à droite en lui ajoutant des caractères sur la gauche jusqu’à ce que la longueur totale spécifiée ait été atteinte.
  
| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne\_à\_remplir | Oui | Chaîne à aligner à droite.
| longueur\_totale | Oui | Nombre total de caractères de la chaîne renvoyée.
| caractère\_de\_remplissage | Oui | Caractère de remplissage à insérer sur la gauche jusqu’à ce que la longueur totale soit atteinte.

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
| chaîne\_initiale | Oui | Chaîne pour laquelle toutes les instances d’un caractère seront remplacées par un autre caractère.
| ancien\_caractère | Oui | Caractère à supprimer de la chaîne initiale.
| nouveau\_caractère | Oui | Caractère à ajouter à la place du caractère supprimé.

L’exemple ci-après indique comment supprimer tous les tirets de la chaîne fournie par l’utilisateur.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="split" />
### split

**split(chaîne\_entrée, délimiteur)** **split(chaîne\_entrée, [délimiteurs])**

Retourne un tableau de chaînes qui contient les sous-chaînes de la chaîne d'entrée séparées par les délimiteurs envoyés.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne\_entrée | Oui | La chaîne à fractionner.
| delimiter | Oui | Le séparateur à utiliser. Peut être une chaîne unique ou un tableau de chaînes.

L'exemple suivant fractionne la chaîne d'entrée en la séparant par une virgule.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

<a id="string" />
### string

**string(valueToConvert)**

Convertit la valeur spécifiée en chaîne.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| valueToConvert | Oui | Valeur à convertir en chaîne. Le type de valeur peut uniquement être une valeur booléenne, une chaîne ou un entier.

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en chaîne.

    "parameters": {
        "appId": { "type": "int" }
    },
    "variables": { 
        "stringValue": "[string(parameters('appId'))]"
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

**uniqueString (stringForCreatingUniqueString, ...)**

Effectue un hachage 64 bits des chaînes de caractères fournies pour créer une chaîne unique. Cette fonction est utile lorsque vous avez besoin créer un nom unique pour une ressource. Vous fournissez des valeurs de paramètre qui représentent le niveau d'unicité pour le résultat. Vous pouvez spécifier si le nom est unique pour votre abonnement, le groupe de ressources ou le déploiement.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| stringForCreatingUniqueString | Oui | La chaîne de base utilisée dans la fonction de hachage pour créer une chaîne unique.
| paramètres supplémentaires le cas échéant | Non | Vous pouvez ajouter autant de chaînes que nécessaire pour créer la valeur qui spécifie le niveau d'unicité.

La valeur renvoyée n'est pas une chaîne entièrement aléatoire, mais plutôt le résultat d'une fonction de hachage. La valeur renvoyée comprend 13 caractères. Son unicité globale n'est pas garantie Il se peut que vous souhaitiez associer un préfixe de votre convention d'affectation de noms à la valeur pour créer un nom plus convivial.

Les exemples suivants montrent comment utiliser uniqueString pour créer une valeur unique pour différents niveaux couramment utilisés.

Unique au niveau de l'abonnement

    "[uniqueString(subscription().subscriptionId)]"

Unique au niveau du groupe de ressources

    "[uniqueString(resourceGroup().id)]"

Unique au niveau du déploiement pour un groupe de ressources

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
L'exemple suivant montre comment créer un nom unique pour un compte de stockage basé sur votre groupe de ressources.

    "resources": [{ 
        "name": "[concat('ContosoStorage', uniqueString(resourceGroup().id))]", 
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

La valeur du paramètre **baseUri** peut inclure un fichier spécifique, mais seul le chemin d’accès de base est utilisé lors de la construction de l’URI. Par exemple, si vous transmettez ****http://contoso.com/resources/azuredeploy.json** en tant que paramètre baseUri, l’URI de base résultante sera ****http://contoso.com/resources/**.

L’exemple suivant montre comment créer un lien vers un modèle imbriqué en fonction de la valeur du modèle parent.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"



## Expressions de valeur de déploiement

Resource Manager offre les expressions ci-après pour l’obtention de valeurs à partir des sections du modèle et de valeurs associées au déploiement :

- [deployment](#deployment)
- [parameters](#parameters)
- [variables](#variables)

Pour obtenir les valeurs de ressources, de groupes de ressources ou d’abonnements, voir la section [Expressions de ressource](#resource-expressions).

<a id="deployment" />
### deployment

**deployment()**

Renvoie des informations sur l’opération de déploiement actuelle.

Cette expression retourne l’objet passé au cours du déploiement. Les propriétés de l’objet retourné diffèrent selon que l’objet de déploiement est passé sous forme de lien ou d’objet inline. Quand l’objet de déploiement est transmis en ligne, par exemple en cas d’utilisation du paramètre **-TemplateFile** dans Azure PowerShell pour pointer vers un fichier local, l’objet renvoyé présente le format suivant :

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Lorsque l’objet est transmis en tant que lien, par exemple en cas d’utilisation du paramètre **-TemplateUri** pour pointer vers un objet distant, l’objet est renvoyé au format ci-après.

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": "",
                "contentVersion": ""
            },
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



## Expressions de ressource

Resource Manager offre les expressions ci-après pour l’obtention des valeurs de ressource :

- [listkeys](#listkeys)
- [fournisseurs](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [subscription](#subscription)

Pour obtenir les valeurs de paramètres, de variables ou du déploiement actuel, voir la section [Expressions de valeur de déploiement](#deployment-value-expressions).

<a id="listkeys" />
### listKeys

**listKeys (nom\_ressource ou identificateur\_ressource, version\_api)**

Retourne les clés d'un compte de stockage. L'identificateur de ressource peut être spécifié à l'aide de la [fonction resourceId](./#resourceid) ou en utilisant le format **espacedenoms\_fournisseur/type\_ressource/nom\_ressource**. Vous pouvez utiliser cette fonction pour obtenir les valeurs primaryKey et secondaryKey.
  
| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_ressource ou identificateur\_ressource | Oui | Identifiant unique du compte de stockage.
| version\_api | Oui | Version d'API de l'état d'exécution des ressources.

L'exemple suivant montre comment retourner les clés à partir d'un compte de stockage dans la section outputs.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

<a id="providers" />
### fournisseurs

**fournisseurs (espacedenoms\_fournisseur, [type\_ressource])**

Retourne des informations sur un fournisseur de ressources et les types de ressources qu'il prend en charge. Si aucun type n'est spécifié, tous les types pris en charge sont retournés.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| espacedenoms\_fournisseur | Oui | Espace de noms du fournisseur.
| type\_ressource | Non | Type de ressource dans l'espace de noms spécifié.

Chaque type pris en charge est retourné au format suivant :

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

Permet à une expression de dériver sa valeur de l'état d'exécution d'une autre ressource.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_ressource ou identificateur\_ressource | Oui | Nom ou identificateur unique d’une ressource.
| version\_api | Non | Version d’API de la ressource spécifiée. Vous devez inclure ce paramètre lorsque la ressource n’est pas approvisionnée dans le même modèle.

La fonction **reference** dérive sa valeur d'un état d'exécution, et ne peut donc pas être utilisée dans la section variables. Elle peut être utilisée dans la section outputs d'un modèle.

En utilisant l'expression « reference », vous déclarez de manière implicite qu'une ressource dépend d'une autre ressource si la ressource référencée est configurée dans le même modèle. Vous n’avez pas besoin d’utiliser également la propriété **dependsOn**. L'expression n'est pas évaluée tant que le déploiement de la ressource référencée n'est pas terminé.

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
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15')]",
			"type" : "object"
		}
	}

Vous pouvez récupérer une valeur spécifique à partir de l’objet renvoyé, comme l’URI du point de terminaison d’objet blob, comme indiqué ci-dessous.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

Si vous souhaitez à présent spécifier directement la version d’API dans votre modèle, vous pouvez utiliser l’expression **providers** et récupérer l’une des valeurs, telles que la version la plus récente comme indiqué ci-dessous.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).primaryEndpoints.blob]",
			"type" : "string"
		}
	}

L’exemple ci-après référence un compte de stockage figurant dans un autre groupe de ressources.

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

<a id="resourcegroup" />
### resourceGroup

**resourceGroup()**

Retourne un objet structuré qui représente le groupe de ressources actuel. L'objet se présente au format suivant :

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
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

**resourceId ([nom\_groupe\_ressources], type\_ressource, nom\_ressource1, [nom\_ressource2]...)**

Retourne l'identificateur unique d'une ressource. Vous utilisez cette fonction lorsque le nom de la ressource est ambigu ou non configuré dans le même modèle. L'identificateur est retourné au format suivant :

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Paramètre | Requis | Description
| :---------------: | :------: | :----------
| nom\_groupe\_ressources | Non | Nom de groupe de ressources facultatif. La valeur par défaut est le groupe de ressources actuel. Spécifiez cette valeur lorsque vous récupérez une ressource se trouvant dans un autre groupe de ressources.
| type\_ressource | Oui | Type de ressource, y compris l'espace de noms du fournisseur de ressources.
| nom\_ressource1 | Oui | Nom de la ressource.
| nom\_ressource2 | Non | Segment de nom de ressource suivant si la ressource est imbriquée.

L'exemple suivant montre comment récupérer les ID de ressources pour un site web et une base de données. Le site web se trouve dans un groupe de ressources nommé **myWebsitesGroup** et la base de données se trouve dans le groupe de ressources actuel pour ce modèle.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
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
### subscription

**subscription()**

Retourne des détails sur l'abonnement au format suivant.

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

L'exemple suivant montre la fonction subscription appelée dans la section outputs.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## Étapes suivantes
- Pour obtenir une description des sections d’un modèle Azure Resource Manager, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour fusionner plusieurs modèles, voir [Utilisation de modèles liés à Azure Resource Manager](resource-group-linked-templates.md).
- Pour itérer un nombre de fois spécifié pendant la création d’un type de ressource, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
- Pour découvrir comment déployer le modèle que vous avez créé, voir [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_1210_2015-->