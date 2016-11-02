<properties
   pageTitle="Modèles liés avec Resource Manager | Microsoft Azure"
   description="Décrit comment utiliser des modèles liés dans un modèle Azure Resource Manager afin de créer une solution de modèle modulaire. Indique comment transmettre des valeurs de paramètres, spécifier un fichier de paramètres et créer dynamiquement des URL."
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
   ms.date="09/02/2016"
   ms.author="tomfitz"/>

# Utilisation de modèles liés à Azure Resource Manager

À partir d’un modèle Azure Resource Manager, vous pouvez établir un lien avec un autre modèle, ce qui vous permet le cas échéant de décomposer votre déploiement en un ensemble de modèles ciblés, dédiés. Tout comme la décomposition d’une application en plusieurs classes de codes, cette décomposition procure des avantages en matière de test, de réutilisation et de lisibilité.

Vous pouvez déplacer des paramètres d’un modèle principal à un modèle lié. Ces paramètres peuvent être directement mappés sur des paramètres ou des variables exposés par le modèle d’appel. Le modèle lié peut également retransmettre une variable de sortie vers le modèle source, permettant ainsi un échange bidirectionnel de données entre les modèles.

## Liaison à un modèle

Pour créer un lien entre deux modèles, ajoutez une ressource de déploiement dans le modèle principal pointant vers le modèle lié. Vous définissez la propriété **templateLink** à l’URI du modèle lié. Vous pouvez fournir des valeurs de paramètre pour le modèle lié en spécifiant les valeurs directement dans votre modèle ou en créant un lien vers un fichier de paramètres. L’exemple suivant utilise la propriété **parameters** afin de spécifier directement une valeur de paramètre.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
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

Le service Resource Manager doit être en mesure d’accéder au modèle lié. Vous ne pouvez pas spécifier un fichier local ou un fichier uniquement disponible sur votre réseau local pour le modèle lié. Vous pouvez seulement fournir une valeur URI qui inclut soit **http** soit **https**. Une possibilité consiste à placer votre modèle lié dans un compte de stockage et à utiliser l’URI de cet élément, comme illustré ci-dessous.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Bien que le modèle lié doive être disponible en externe, il n’a pas besoin d’être accessible au public. Vous pouvez ajouter votre modèle dans un compte de stockage privé, uniquement accessible au propriétaire du compte de stockage. Ensuite, vous créez un jeton de signature d’accès partagé (SAP) pour autoriser l’accès en cours de déploiement. Vous ajoutez ce jeton SAP à l’URI pour le modèle lié. Pour connaître les étapes de configuration d’un modèle dans un compte de stockage et de génération d’un jeton SAP, consultez [Déployer des ressources avec des modèles Resource Manager et Azure PowerShell](resource-group-template-deploy.md) ou [Déployer des ressources avec des modèles Resource Manager et l’interface de ligne de commande Azure](resource-group-template-deploy-cli.md).

L’exemple suivant montre un modèle parent lié à un autre modèle. Le modèle lié est accessible avec un jeton SAP qui est transmis en tant paramètre.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
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

Même si le jeton est transmis sous forme de chaîne sécurisée, l’URI du modèle lié, y compris le jeton SAP, est enregistré dans les opérations de déploiement de ce groupe de ressources. Pour limiter l’exposition, définissez un délai d’expiration pour le jeton.

## Liaison à un fichier de paramètres

L’exemple suivant utilise la propriété **parametersLink** pour créer un lien vers un fichier de paramètres.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
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

La valeur d’URI pour le fichier de paramètres liés ne peut pas être un fichier local et doit inclure **http** ou **https**. Le fichier de paramètres peut également être limité à l’accès avec un jeton SAP.

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

Vous pouvez également utiliser [deployment()](resource-group-template-functions.md#deployment) pour obtenir l’URL de base pour le modèle actuel, qui permet d’obtenir l’URL d’autres modèles dans le même emplacement. Cette approche est utile si l’emplacement des modèles change (à cause des versions notamment) ou si vous voulez éviter de coder en dur les URL dans le fichier de modèle.

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## Liaison conditionnelle vers les modèles

Vous pouvez établir une liaison vers différents modèles en passant une valeur de paramètre utilisée pour construire l’URI du modèle lié. Cette approche fonctionne parfaitement lorsque vous devez spécifier le modèle lié à utiliser en cours de déploiement. Par exemple, vous pouvez spécifier un modèle à utiliser pour un compte de stockage existant, puis un autre modèle pour un nouveau compte de stockage.

L’exemple suivant illustre un paramètre associé à un nom de compte de stockage, ainsi qu’un paramètre permettant de spécifier si le compte de stockage est nouveau ou existe déjà.

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

Vous créez une variable pour l’URI de modèle, qui contient la valeur du paramètre nouveau ou existant.

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

Vous fournissez cette valeur de variable pour la ressource de déploiement.

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

L’URI correspond à un modèle nommé **existingStorageAccount.json** ou **newStorageAccount.json**. Créez des modèles pour ces URI.

L’exemple suivant illustre le modèle **existingStorageAccount.json**.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

L’exemple suivant illustre le modèle **newStorageAccount.json**. Tout comme le modèle de compte de stockage existant, l’objet de compte de stockage est renvoyé dans la section outputs. Le modèle principal fonctionne avec l’un ou l’autre de ces modèles liés.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## Exemple complet

Les exemples de modèles suivants montrent une disposition simplifiée des modèles liés pour illustrer certains des concepts décrits dans cet article. Ils partent du principe que les modèles ont été ajoutés au même conteneur dans un compte de stockage dont l’accès public est désactivé. Le modèle lié retransmet une valeur au modèle principal dans la section **outputs**.

Le fichier **parent.json** est composé de :

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
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

Le fichier **helloworld.json** est composé de :

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
    
Dans PowerShell, vous obtenez un jeton pour le conteneur et déployez les modèles avec :

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

Dans l’interface de ligne de commande Azure, vous obtenez un jeton pour le conteneur et déployez les modèles avec le code suivant. Actuellement, vous devez fournir un nom pour le déploiement lorsque vous utilisez un modèle d’URI qui inclut un jeton SAP.

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

Vous devez fournir le jeton SAP en tant que paramètre. Vous devez faire précéder le jeton de **?**.

## Étapes suivantes
- Pour obtenir des informations sur la définition de l’ordre de déploiement de vos ressources, consultez [Définition de dépendances dans les modèles Azure Resource Manager](resource-group-define-dependencies.md)
- Pour savoir comment définir une seule ressource mais également créer de nombreuses instances de cette dernière, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md)

<!---HONumber=AcomDC_0907_2016-->