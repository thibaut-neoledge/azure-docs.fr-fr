<properties
   pageTitle="Utilisation de modèles liés à Azure Resource Manager"
   description="Décrit comment utiliser des modèles liés dans un modèle Azure Resource Manager afin de créer une solution de modèle modulaire. Indique comment transmettre des valeurs de paramètres, spécifier un fichier de paramètres et créer dynamiquement des URL."
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
   ms.date="09/04/2015"
   ms.author="tomfitz"/>

# Utilisation de modèles liés à Azure Resource Manager

À partir d’un modèle Azure Resource Manager, vous pouvez établir un lien avec un autre modèle, ce qui vous permet le cas échéant de décomposer votre déploiement en un ensemble de modèles ciblés, dédiés. Tout comme la décomposition d’une application en plusieurs classes de codes, cette décomposition procure des avantages en matière de test, de réutilisation et de lisibilité.

Vous pouvez déplacer des paramètres d’un modèle principal à un modèle lié. Ces paramètres peuvent être directement mappés sur des paramètres ou des variables exposés par le modèle d’appel. Le modèle lié peut également retransmettre une variable de sortie vers le modèle source, permettant ainsi un échange bidirectionnel de données entre les modèles.

## Liaison à un modèle

Pour créer un lien entre deux modèles, ajoutez une ressource de déploiement dans le modèle principal pointant vers le modèle lié. Vous définissez la propriété **templateLink** à l’URI du modèle lié. Vous pouvez fournir des valeurs de paramètre pour le modèle lié en spécifiant les valeurs directement dans votre modèle ou en créant un lien vers un fichier de paramètres. L’exemple suivant utilise la propriété **parameters** afin de spécifier directement une valeur de paramètre.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

Le service Resource Manager doit être en mesure d’accéder au modèle lié, ce qui signifie que vous ne pouvez pas spécifier un fichier local ou un fichier qui est uniquement disponible sur votre réseau local pour le modèle lié. Vous pouvez seulement fournir une valeur URI qui inclut soit **http** soit **https**. Une possibilité consiste à placer votre modèle lié dans un compte de stockage et à utiliser l'URI de cet élément, comme illustré ci-dessous.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }


## Liaison à un fichier de paramètres

L’exemple suivant utilise la propriété **parametersLink** pour créer un lien vers un fichier de paramètres.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

La valeur d'URI pour le fichier de paramètres liés ne peut pas être un fichier local et doit inclure **http** ou **https**.

## Utilisation de variables pour lier des modèles

Les exemples précédents représentaient des valeurs d’URL codées en dur pour les liens de modèle. Cette approche peut s’avérer efficace avec un modèle isolé, mais elle est incompatible avec le traitement d’un large ensemble des modèles modulaires. Au lieu de cela, vous pouvez créer une variable statique qui stocke une URL de base pour le modèle principal, avant de créer dynamiquement les URL pour les modèles liés à partir de cette URL de base. Cette approche permet notamment de déplacer ou de répliquer facilement le modèle. En effet, il vous suffit de modifier la variable statique dans le modèle principal. Le modèle principal transmet les URI appropriés au modèle décomposé.

L’exemple suivant indique comment utiliser une URL de base afin de créer deux URL pour des modèles liés (**sharedTemplateUrl** et **vmTemplate**).

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

## Transmettre des valeurs à partir d’un modèle lié

Si vous devez transmettre une valeur du modèle lié vers le modèle principal, vous pouvez créer une valeur dans la section **outputs** du modèle lié. Pour consulter un exemple, accédez à la page [Partage d’état dans les modèles Azure Resource Manager](best-practices-resource-manager-state.md).

## Étapes suivantes
- [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md)
- [Déploiement d’une application à l’aide d’un modèle Azure Resource Manager](azure-portal/resource-group-template-deploy.md)

<!---HONumber=Sept15_HO2-->