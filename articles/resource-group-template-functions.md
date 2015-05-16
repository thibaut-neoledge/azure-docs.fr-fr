<properties
   pageTitle="Fonctions des modèles de gestionnaire des ressources Azure"
   description="Décrit les fonctions à utiliser dans un modèle de gestionnaire des ressources Azure pour déployer des applications dans Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Fonctions des modèles de gestionnaire des ressources Azure

Cette rubrique décrit toutes les fonctions que vous pouvez utiliser dans un modèle de gestionnaire des ressources Azure.

## base64

**base64 \(chaîne\_entrée\)**

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

**concat \(arg1, arg2, arg3, ...\)**

Combine plusieurs valeurs de chaîne et retourne la valeur de chaîne obtenue. Cette fonction peut prendre n'importe quel nombre d'arguments.

L'exemple suivant montre comment combiner plusieurs valeurs pour en retourner une seule.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## listKeys

**listKeys \(nom\_ressource ou identificateur\_ressource, [version\_api]\)**

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

## parameters

**parameters \(nom\_paramètre\)**

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

**provider \(espacedenoms\_fournisseur, [type\_ressource]\)**

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

**reference \(nom\_ressource ou identificateur\_ressource, [version\_api]\)**

Permet à une expression de dériver sa valeur de l'état d'exécution d'une autre ressource.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_ressource ou identificateur\_ressource | Oui | Nom de l'identificateur unique d'une ressource.
| version\_api | Non | Version d'API de l'état d'exécution des ressources. Ce paramètre doit être utilisé si la ressource n'est pas configurée dans le même modèle.

La fonction **reference** dérive sa valeur d'un état d'exécution, et ne peut donc pas être utilisée dans la section variables. Elle peut être utilisée dans la section outputs d'un modèle.

En utilisant l'expression reference, vous déclarez qu'une ressource dépend d'une autre ressource si la ressource référencée est configurée dans le même modèle.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## resourceGroup

**resourceGroup\(\)**

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

**resourceId \([nom\_groupe\_ressources], type\_ressource, nom\_ressource1, [nom\_ressource2]...\)**

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

**subscription\(\)**

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

## variables

**variables \(nom\_variable\)**

Retourne la valeur de la variable. Le nom de variable spécifié doit être défini dans la section variables du modèle.

| Paramètre | Requis | Description
| :--------------------------------: | :------: | :----------
| nom\_variable | Oui | Nom de la variable à retourner.


## Étapes suivantes
- [Création de modèles de gestionnaire des ressources Azure](./resource-group-authoring-templates.md)
- [Opérations de modèle avancées](./resource-group-advanced-template.md)
- [Déploiement d'une application avec un modèle de gestionnaire des ressources Azure](./resouce-group-template-deploy.md)
- [Présentation du gestionnaire des ressources Azure](./resource-group-overview.md)

<!--HONumber=52-->
