<properties
   pageTitle="Fonctions des modèles de gestionnaire des ressources Azure"
	description="Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour récupérer des valeurs, mettre en forme des chaînes et récupérer des informations sur le déploiement."
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
	ms.date="08/21/2015"
	ms.author="tomfitz"/>

# Fonctions des modèles de gestionnaire des ressources Azure

Cette rubrique décrit toutes les fonctions que vous pouvez utiliser dans un modèle de gestionnaire des ressources Azure.

## ajouter

**add(operand1, operand2)**

Retourne la somme des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Premier opérande à utiliser.
| operand2 | Oui | Deuxième opérande à utiliser.


## base64

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

## concat

**concat (arg1, arg2, arg3, ...)**

Combine plusieurs valeurs de chaîne et retourne la valeur de chaîne obtenue. Cette fonction peut prendre n'importe quel nombre d'arguments.

L'exemple suivant montre comment combiner plusieurs valeurs pour en retourner une seule.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## copyIndex

**copyIndex(offset)**

Retourne l'index actuel d'une boucle d'itération. Pour obtenir des exemples d'utilisation de cette fonction, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

## deployment

**deployment()**

Renvoie des informations sur l’opération de déploiement actuelle.

Les informations relatives au déploiement sont renvoyées en tant qu’objet avec les propriétés suivantes :

    {
      "name": "",
      "properties": {
        "template": {},
        "parameters": {},
        "mode": "",
        "provisioningState": ""
      }
    }

L’exemple ci-après indique comment renvoyer les informations de déploiement dans la section « outputs ».

    "outputs": {
      "exampleOutput": {
        "value": "[deployment()]",
        "type" : "object"
      }
    }

## div

**div(operand1, operand2)**

Retourne la division entière des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Nombre à diviser.
| operand2 | Oui | Nombre utilisé pour diviser. Doit être différent de 0.

## length

**length(array)**

Retourne le nombre d'éléments dans un tableau. En règle générale, utilisé pour spécifier le nombre d'itérations lors de la création de ressources. Pour obtenir un exemple d'utilisation de cette fonction, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

## listKeys

**listKeys (nom\_ressource ou identificateur\_ressource, [version\_api])**

Retourne les clés d'un compte de stockage. L'identificateur de ressource peut être spécifié à l'aide de la [fonction resourceId](./#resourceid) ou en utilisant le format **espacedenoms\_fournisseur/type\_ressource/nom\_ressource**. Vous pouvez utiliser cette fonction pour obtenir les valeurs primaryKey et secondaryKey.
  
| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_ressource ou identificateur\_ressource | Oui | Identifiant unique du compte de stockage.
| version\_api | Oui | Version d'API de l'état d'exécution des ressources.

L'exemple suivant montre comment retourner les clés à partir d'un compte de stockage dans la section outputs.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## mod

**mod(operand1, operand2)**

Retourne le reste de la division entière des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Nombre à diviser.
| operand2 | Oui | Nombre utilisé pour diviser. Doit être différent de 0.


## mul

**mul(operand1, operand2)**

Retourne la multiplication des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Premier opérande à utiliser.
| operand2 | Oui | Deuxième opérande à utiliser.


## padLeft

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


## parameters

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

## provider

**provider (espacedenoms\_fournisseur, [type\_ressource])**

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

## reference

**reference (nom\_ressource ou identificateur\_ressource, [version\_api])**

Permet à une expression de dériver sa valeur de l'état d'exécution d'une autre ressource.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_ressource ou identificateur\_ressource | Oui | Nom de l'identificateur unique d'une ressource.
| version\_api | Non | Version d'API de l'état d'exécution des ressources. Ce paramètre doit être utilisé si la ressource n'est pas configurée dans le même modèle.

La fonction **reference** dérive sa valeur d'un état d'exécution, et ne peut donc pas être utilisée dans la section variables. Elle peut être utilisée dans la section outputs d'un modèle.

En utilisant l'expression « reference », vous déclarez de manière implicite qu'une ressource dépend d'une autre ressource si la ressource référencée est configurée dans le même modèle. Vous n'avez pas besoin d'utiliser également la propriété **dependsOn**. L'expression n'est pas évaluée tant que le déploiement de la ressource référencée n'est pas terminé.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## replace

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

## resourceGroup

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

## resourceId

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

## split

**split(inputString, delimiter)** **split(inputString, [delimiters])**

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

## sub

**sub(operand1, operand2)**

Retourne la soustraction des deux entiers fournis.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| operand1 | Oui | Nombre auquel sera appliquée la soustraction.
| operand2 | Oui | Nombre à soustraire.


## subscription

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

## toLower

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

## toUpper

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


## variables

**variables (nom\_variable)**

Retourne la valeur de la variable. Le nom de variable spécifié doit être défini dans la section variables du modèle.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_variable | Oui | Nom de la variable à retourner.


## Étapes suivantes
- Pour obtenir une description des sections dans un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md)
- Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md)
- Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md)
- Pour savoir comment déployer le modèle que vous avez créé, consultez [Déploiement d'une application avec un modèle Azure Resource Manager](azure-portal/resource-group-template-deploy.md)

<!---HONumber=August15_HO9-->