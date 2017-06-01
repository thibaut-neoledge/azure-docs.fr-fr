---
title: "Lier des modèles pour un déploiement Azure | Microsoft Docs"
description: "Décrit comment utiliser des modèles liés dans un modèle Azure Resource Manager afin de créer une solution de modèle modulaire. Indique comment transmettre des valeurs de paramètres, spécifier un fichier de paramètres et créer dynamiquement des URL."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 9ab6d3e5e41f155b1404cee8a555078409c09c60
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Utilisation de modèles liés lors du déploiement des ressources Azure
À partir d’un modèle Azure Resource Manager, vous pouvez établir un lien avec un autre modèle, ce qui vous permet le cas échéant de décomposer votre déploiement en un ensemble de modèles ciblés, dédiés. Tout comme la décomposition d’une application en plusieurs classes de codes, cette décomposition procure des avantages en matière de test, de réutilisation et de lisibilité.  

Vous pouvez déplacer des paramètres d’un modèle principal à un modèle lié. Ces paramètres peuvent être directement mappés sur des paramètres ou des variables exposés par le modèle d’appel. Le modèle lié peut également retransmettre une variable de sortie vers le modèle source, permettant ainsi un échange bidirectionnel de données entre les modèles.

## <a name="linking-to-a-template"></a>Liaison à un modèle
Pour créer un lien entre deux modèles, ajoutez une ressource de déploiement dans le modèle principal pointant vers le modèle lié. Vous définissez la propriété **templateLink** à l’URI du modèle lié. Vous pouvez fournir des valeurs de paramètres pour le modèle lié directement dans votre modèle ou dans un fichier de paramètres. L’exemple suivant utilise la propriété **parameters** afin de spécifier directement une valeur de paramètre.

```json
"resources": [ 
  { 
      "apiVersion": "2017-05-10", 
      "name": "linkedTemplate", 
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
```

Comme pour d’autres types de ressources, vous pouvez définir des dépendances entre le modèle lié et d’autres ressources. Par conséquent, lorsque d’autres ressources requièrent une valeur de sortie à partir du modèle lié, vous pouvez vous assurer que le modèle lié est déployé avant celles-ci. Sinon, lorsque le modèle lié s’appuie sur d’autres ressources, vous pouvez vous assurer que d’autres ressources sont déployées avant le modèle lié. Vous pouvez récupérer une valeur à partir d’un modèle lié avec la syntaxe suivante :

```json
"[reference('linkedTemplate').outputs.exampleProperty.value]"
```

Le service Resource Manager doit être en mesure d’accéder au modèle lié. Vous ne pouvez pas spécifier un fichier local ou un fichier uniquement disponible sur votre réseau local pour le modèle lié. Vous pouvez seulement fournir une valeur URI qui inclut soit **http** soit **https**. Une possibilité consiste à placer votre modèle lié dans un compte de stockage et à utiliser l’URI de cet élément, comme illustré ci-dessous :

```json
"templateLink": {
    "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
    "contentVersion": "1.0.0.0",
}
```

Bien que le modèle lié doive être disponible en externe, il n’a pas besoin d’être accessible au public. Vous pouvez ajouter votre modèle dans un compte de stockage privé, uniquement accessible au propriétaire du compte de stockage. Ensuite, vous créez un jeton de signature d’accès partagé (SAP) pour autoriser l’accès en cours de déploiement. Vous ajoutez ce jeton SAP à l’URI pour le modèle lié. Pour connaître les étapes de configuration d’un modèle dans un compte de stockage et de génération d’un jeton SAP, consultez [Déployer des ressources avec des modèles Resource Manager et Azure PowerShell](resource-group-template-deploy.md) ou [Déployer des ressources avec des modèles Resource Manager et l’interface de ligne de commande Azure](resource-group-template-deploy-cli.md). 

L’exemple suivant montre un modèle parent lié à un autre modèle. Le modèle lié est accessible avec un jeton SAP qui est transmis en tant paramètre.

```json
"parameters": {
    "sasToken": { "type": "securestring" }
},
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "linkedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
          "mode": "incremental",
          "templateLink": {
            "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
            "contentVersion": "1.0.0.0"
          }
        }
    }
],
```

Même si le jeton est transmis sous forme de chaîne sécurisée, l’URI du modèle lié, y compris le jeton SAP, est enregistré dans les opérations de déploiement. Pour limiter l’exposition, définissez un délai d’expiration pour le jeton.

Resource Manager gère chaque modèle lié comme un déploiement séparé. Dans l’historique de déploiement du groupe de ressources, vous voyez des déploiements distincts pour les modèles parents et imbriqués.

![historique des déploiements](./media/resource-group-linked-templates/linked-deployment-history.png)

## <a name="linking-to-a-parameter-file"></a>Liaison à un fichier de paramètres
L’exemple suivant utilise la propriété **parametersLink** pour créer un lien vers un fichier de paramètres.

```json
"resources": [ 
  { 
     "apiVersion": "2017-05-10", 
     "name": "linkedTemplate", 
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
```

La valeur d’URI pour le fichier de paramètres liés ne peut pas être un fichier local et doit inclure **http** ou **https**. Le fichier de paramètres peut également être limité à l’accès avec un jeton SAP.

## <a name="using-variables-to-link-templates"></a>Utilisation de variables pour lier des modèles
Les exemples précédents représentaient des valeurs d’URL codées en dur pour les liens de modèle. Cette approche peut s’avérer efficace avec un modèle isolé, mais elle est incompatible avec le traitement d’un large ensemble des modèles modulaires. Au lieu de cela, vous pouvez créer une variable statique qui stocke une URL de base pour le modèle principal, avant de créer dynamiquement les URL pour les modèles liés à partir de cette URL de base. Cette approche permet notamment de déplacer ou de répliquer facilement le modèle. En effet, il vous suffit de modifier la variable statique dans le modèle principal. Le modèle principal transmet les URI appropriés au modèle décomposé.

L’exemple suivant indique comment utiliser une URL de base afin de créer deux URL pour des modèles liés (**sharedTemplateUrl** et **vmTemplate**). 

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Vous pouvez également utiliser [deployment()](resource-group-template-functions-deployment.md#deployment) pour obtenir l’URL de base pour le modèle actuel, qui permet d’obtenir l’URL d’autres modèles dans le même emplacement. Cette approche est utile si l’emplacement des modèles change (à cause des versions notamment) ou si vous voulez éviter de coder en dur les URL dans le fichier de modèle. 

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="complete-example"></a>Exemple complet
Les exemples de modèles suivants montrent une disposition simplifiée des modèles liés pour illustrer certains des concepts décrits dans cet article. Ils partent du principe que les modèles ont été ajoutés au même conteneur dans un compte de stockage dont l’accès public est désactivé. Le modèle lié retransmet une valeur au modèle principal dans la section **outputs** .

Le fichier **parent.json** est composé de :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "object",
      "value": "[reference('linkedTemplate').outputs.result]"
    }
  }
}
```

Le fichier **helloworld.json** est composé de :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "result": {
        "value": "Hello World",
        "type" : "string"
    }
  }
}
```

Dans PowerShell, vous obtenez un jeton pour le conteneur et déployez les modèles avec :

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Dans Azure CLI 2.0, vous obtenez un jeton pour le conteneur et déployez les modèles avec le code suivant :

```azurecli
seconds='@'$(( $(date +%s) + 1800 ))
expiretime=$(date +%Y-%m-%dT%H:%MZ --date=$seconds)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des informations sur la définition de l’ordre de déploiement de vos ressources, consultez [Définition de dépendances dans les modèles Azure Resource Manager](resource-group-define-dependencies.md)
* Pour savoir comment définir une seule ressource mais également créer de nombreuses instances de cette dernière, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md)


