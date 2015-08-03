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
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Fonctions des modèles de gestionnaire des ressources Azure

Cette rubrique décrit toutes les fonctions que vous pouvez utiliser dans un modèle de gestionnaire des ressources Azure.

## base64

**base64 (chaîne_entrée)**

Retourne la représentation en base 64 de la chaîne d'entrée.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne_entrée | Oui | Valeur de chaîne à retourner sous la forme d'une représentation en base 64.

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

## listKeys

**listKeys (nom_ressource ou identificateur_ressource, [version_api])**

Retourne les clés d'un compte de stockage. L'identificateur de ressource peut être spécifié à l'aide de la [fonction resourceId](./#resourceid) ou en utilisant le format **espacedenoms_fournisseur/type_ressource/nom_ressource**. Vous pouvez utiliser cette fonction pour obtenir les valeurs primaryKey et secondaryKey.
  
| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom_ressource ou identificateur_ressource | Oui | Identifiant unique du compte de stockage.
| version_api | Oui | Version d'API de l'état d'exécution des ressources.

L'exemple suivant montre comment retourner les clés à partir d'un compte de stockage dans la section outputs.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## padLeft

**padLeft(chaîne_à_remplir, longueur_totale, caractère_de_remplissage)**

Renvoie une chaîne alignée à droite en lui ajoutant des caractères sur la gauche jusqu’à ce que la longueur totale spécifiée ait été atteinte.
  
| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne_à_remplir | Oui | Chaîne à aligner à droite.
| longueur_totale | Oui | Nombre total de caractères de la chaîne renvoyée.
| caractère_de_remplissage | Oui | Caractère de remplissage à insérer sur la gauche jusqu’à ce que la longueur totale soit atteinte.

L’exemple ci-après indique comment remplir la valeur de paramètre fournie par l’utilisateur avec le caractère zéro jusqu’à ce que la chaîne atteigne 10 caractères. Si la valeur de paramètre d’origine comporte plus de 10 caractères, aucun caractère n’est ajouté.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }


## parameters

**parameters (nom_paramètre)**

Retourne une valeur de paramètre. Le nom de paramètre spécifié doit être défini dans la section parameters du modèle.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom_paramètre | Oui | Nom du paramètre à retourner.

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

**provider (espacedenoms_fournisseur, [type_ressource])**

Retourne des informations sur un fournisseur de ressources et les types de ressources qu'il prend en charge. Si aucun type n'est spécifié, tous les types pris en charge sont retournés.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| espacedenoms_fournisseur | Oui | Espace de noms du fournisseur.
| type_ressource | Non | Type de ressource dans l'espace de noms spécifié.

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

**reference (nom_ressource ou identificateur_ressource, [version_api])**

Permet à une expression de dériver sa valeur de l'état d'exécution d'une autre ressource.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom_ressource ou identificateur_ressource | Oui | Nom de l'identificateur unique d'une ressource.
| version_api | Non | Version d'API de l'état d'exécution des ressources. Ce paramètre doit être utilisé si la ressource n'est pas configurée dans le même modèle.

La fonction **reference** dérive sa valeur d'un état d'exécution, et ne peut donc pas être utilisée dans la section variables. Elle peut être utilisée dans la section outputs d'un modèle.

En utilisant l'expression reference, vous déclarez qu'une ressource dépend d'une autre ressource si la ressource référencée est configurée dans le même modèle.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## replace

**replace(chaîne_initiale, ancien_caractère, nouveau_caractère)**

Renvoie une nouvelle chaîne dans laquelle toutes les instances d’un caractère de la chaîne initiale spécifiée ont été remplacées par un autre caractère.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne_initiale | Oui | Chaîne pour laquelle toutes les instances d’un caractère seront remplacées par un autre caractère.
| ancien_caractère | Oui | Caractère à supprimer de la chaîne initiale.
| nouveau_caractère | Oui | Caractère à ajouter à la place du caractère supprimé.

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

**resourceId ([nom_groupe_ressources], type_ressource, nom_ressource1, [nom_ressource2]...)**

Retourne l'identificateur unique d'une ressource. Vous utilisez cette fonction lorsque le nom de la ressource est ambigu ou non configuré dans le même modèle. L'identificateur est retourné au format suivant :

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Paramètre | Requis | Description
| :---------------: | :------: | :----------
| nom_groupe_ressources | Non | Nom de groupe de ressources facultatif. La valeur par défaut est le groupe de ressources actuel. Spécifiez cette valeur lorsque vous récupérez une ressource se trouvant dans un autre groupe de ressources.
| type_ressource | Oui | Type de ressource, y compris l'espace de noms du fournisseur de ressources.
| nom_ressource1 | Oui | Nom de la ressource.
| nom_ressource2 | Non | Segment de nom de ressource suivant si la ressource est imbriquée.

L'exemple suivant montre comment récupérer les ID de ressources pour un site web et une base de données. Le site web se trouve dans un groupe de ressources nommé **myWebsitesGroup** et la base de données se trouve dans le groupe de ressources actuel pour ce modèle.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
Souvent, vous devez utiliser cette fonction lorsque vous utilisez un compte de stockage ou un réseau virtuel se trouvant dans un autre groupe de ressources. Le compte de stockage ou le réseau virtuel peut être utilisé sur plusieurs groupes de ressources. Par conséquent, vous ne voulez pas les supprimer lors de la suppression d'un seul groupe de ressources. L'exemple suivant montre comment une ressource d'un groupe de ressources externe peut être facilement utilisée :

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
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

**toLower(chaîne_à_modifier)**

Convertit la chaîne spécifiée en minuscules.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne_à_modifier | Oui | Chaîne à convertir en minuscules.

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en minuscules.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

## toUpper

**toUpper(chaîne_à_modifier)**

Convertit la chaîne spécifiée en majuscules.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| chaîne_à_modifier | Oui | Chaîne à convertir en majuscules.

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en majuscules.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }


## variables

**variables (nom_variable)**

Retourne la valeur de la variable. Le nom de variable spécifié doit être défini dans la section variables du modèle.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom_variable | Oui | Nom de la variable à retourner.


## Étapes suivantes
- [Création de modèles de gestionnaire des ressources Azure](./resource-group-authoring-templates.md)
- [Opérations de modèle avancées](./resource-group-advanced-template.md)
- [Déploiement d'une application avec un modèle de gestionnaire des ressources Azure](azure-portal/resource-group-template-deploy.md)
- [Présentation du gestionnaire des ressources Azure](./resource-group-overview.md)

<!---HONumber=July15_HO4-->