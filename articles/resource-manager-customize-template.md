---
title: Personnaliser le modèle Resource Manager exporté | Microsoft Docs
description: Ajoutez des paramètres à un modèle Azure Resource Manager exporté et redéployez-le via Azure PowerShell ou l’interface de ligne de commande Azure.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/01/2016
ms.author: tomfitz

---
# Personnaliser un modèle Azure Resource Manager exporté
Cet article vous explique comment modifier un modèle exporté pour autoriser la transmission de valeurs supplémentaires en tant que paramètres. Elle fait suite aux étapes de l’article [Exporter un modèle Resource Manager](resource-manager-export-template.md), mais vous n’êtes pas obligé d’avoir suivi ces étapes pour appliquer ce qui suit. Vous trouverez les scripts et le modèle requis dans cet article.

## Afficher un modèle exporté
Si vous avez suivi les étapes de la rubrique [Exporter un modèle Resource Manager](resource-manager-export-template.md), ouvrez le modèle que vous avez téléchargé. Le modèle est nommé **template.json**. Si vous disposez de Visual Studio ou Visual Code, vous pouvez utiliser l’un d’eux pour modifier le modèle. Sinon, vous pouvez utiliser un éditeur JSON ou un éditeur de texte.

Si vous n’avez pas terminé la procédure précédente, créez un fichier nommé **template.json**, et ajoutez le contenu suivant au fichier à partir du modèle exporté.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
        },
        "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

Le modèle template.json fonctionne si vous souhaitez créer le même type de compte de stockage dans la même région avec un réseau virtuel qui utilise le même préfixe d’adresse et le même préfixe de sous-réseau pour chaque déploiement. Toutefois, Resource Manager propose des options permettant de déployer des modèles avec une plus grande flexibilité. Par exemple, pendant le déploiement, vous pouvez si vous le souhaitez spécifier le type de compte de stockage à créer ou les valeurs à utiliser pour le préfixe d’adresse de réseau virtuel et le préfixe de sous-réseau.

## Personnaliser le modèle
Dans cette section, vous allez ajouter des paramètres au modèle exporté pour pouvoir réutiliser le modèle lors du déploiement de ces ressources vers d’autres environnements. Vous ajouterez également des fonctionnalités à votre modèle qui diminueront la probabilité de rencontrer une erreur lors du déploiement de votre modèle. Vous n’aurez plus à deviner un nom unique pour votre compte de stockage. Au lieu de cela, le modèle créera un nom unique. Vous allez restreindre les valeurs pouvant être spécifiées pour le type de compte de stockage aux options valides seulement.

1. Pour permettre la transmission des valeurs à spécifier pendant le déploiement, remplacez la section **parameters** par les définitions de paramètres suivantes. Notez les valeurs autorisées de **allowedValues** pour **storageAccount\_accountType**. Si vous fournissez accidentellement une valeur non valide, cette erreur est reconnue avant le début du déploiement. Notez que vous devez uniquement fournir un préfixe comme nom du compte de stockage, et que ce préfixe est limité à 11 caractères. En limitant le préfixe à 11 caractères, vous êtes certain que le nom complet ne dépassera pas le nombre maximal de caractères pour un compte de stockage. Le préfixe vous permet d’appliquer une convention d’affectation de noms pour vos comptes de stockage. Vous allez apprendre à créer un nom unique à l’étape suivante.
   
        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },
2. La section **variables** de votre modèle est actuellement vide. Remplacez cette section par le code suivant : La section **variables** vous permet, en tant que créateur du modèle, de créer des valeurs qui simplifient la syntaxe pour le reste de votre modèle. La variable **storageAccount\_name** concatène le préfixe du paramètre en une chaîne unique générée en se basant sur l’identificateur du groupe de ressources.
   
        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },
3. Pour utiliser les paramètres et la variable dans les définitions de ressource, remplacez la section **resources** par les définitions suivantes. Notez que très peu de chose a changé dans les définitions de ressources, en dehors de la valeur assignée à la propriété de ressource. Les propriétés sont exactement les mêmes que celles du modèle exporté. Vous affectez simplement des propriétés aux valeurs de paramètres, au lieu de valeurs codées en dur. L’emplacement des ressources est défini pour utiliser le même emplacement que le groupe de ressources via l’expression **resourceGroup().location**. La variable que vous avez créée pour le nom de compte de stockage est référencée via l’expression **variables**.
   
        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

## Corriger le fichier de paramètres
Le fichier de paramètres téléchargé ne correspond plus aux paramètres dans votre modèle. Vous n’avez pas à utiliser un fichier de paramètres, mais cela peut simplifier le processus de redéploiement d’un environnement. Vous allez utiliser les valeurs par défaut définies dans le modèle pour la plupart des paramètres, de manière à ce que votre fichier de paramètres ait uniquement besoin de deux valeurs.

Remplacez le contenu du fichier parameters.json par :

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

Le fichier de paramètres mis à jour fournit seulement des valeurs pour les paramètres qui n’ont pas de valeur par défaut. Vous pouvez fournir des valeurs pour les autres paramètres si vous voulez une autre valeur que la valeur par défaut.

## Déployer votre modèle
Vous pouvez utiliser Azure PowerShell ou l’interface de ligne de commande Azure pour déployer le modèle personnalisé et les fichiers de paramètres. Si nécessaire, installez [Azure PowerShell](powershell-install-configure.md) ou l’[interface de ligne de commande Azure](xplat-cli-install.md). Vous pouvez utiliser les scripts téléchargés avec votre modèle lorsque vous avez exporté le modèle d’origine, ou écrire votre propre script pour déployer le modèle. Les deux options sont présentées dans cet article.

1. Pour déployer à l’aide de votre propre script, utilisez l’une des procédures suivantes.
   
     Pour PowerShell, exécutez :
   
        # login
        Add-AzureRmAccount
   
        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"
   
        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json
   
     Pour l’interface de ligne de commande Azure, exécutez :
   
        azure login
   
        azure group create -n ExportGroup -l "West Europe"
   
        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment
2. Si vous avez téléchargé le modèle exporté et les scripts, trouvez le fichier **deploy.ps1** (pour PowerShell) ou **deploy.sh** (pour l’interface de ligne de commande Azure).
   
     Pour PowerShell, exécutez :
   
        Get-Item deploy.ps1 | Unblock-File
   
        .\deploy.ps1
   
     Pour l’interface de ligne de commande Azure, exécutez :
   
        .\deploy.sh

## Étapes suivantes
* Le [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md) s’appuie sur ce que vous avez appris dans cet article en créant un modèle pour une solution plus complexe. Cela vous aide à mieux comprendre les ressources disponibles et à déterminer les valeurs à fournir.
* Pour savoir comment exporter un modèle via PowerShell, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](powershell-azure-resource-manager.md).
* Pour savoir comment exporter un modèle via l’interface de ligne de commande Azure, consultez [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* Pour en savoir plus sur la structuration des modèles, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0803_2016-->