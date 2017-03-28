---
title: "Créer le premier modèle Azure Resource Manager | Microsoft Docs"
description: "Guide pas à pas pour la création de votre premier modèle Azure Resource Manager. Ce guide montre comment utiliser la référence de modèle pour un compte de stockage afin de créer un modèle."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/21/2017
ms.topic: get-started-article
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f861f2ba0376f00ff21e03cbb81a04a266f38630
ms.lasthandoff: 03/22/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>Créer votre premier modèle Azure Resource Manager
Cette rubrique vous guide tout au long des étapes de création de votre premier modèle Azure Resource Manager. Les modèles Azure Resource Manager sont des fichiers JSON qui définissent les ressources nécessaires au déploiement de votre solution. Pour comprendre les concepts associés au déploiement et à la gestion de vos solutions Azure, voir [Présentation d’Azure Resource Manager](resource-group-overview.md). Si vous disposez de ressources existantes et que vous souhaitez créer un modèle pour ces ressources, voir [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md).

Pour créer et réviser des modèles, vous avez besoin d’un éditeur JSON. [Visual Studio Code](https://code.visualstudio.com/) est un éditeur de code multiplateforme, open source et léger. Il prend en charge la création et la modification des modèles Resource Manager via une extension. Cette rubrique suppose que vous utilisez VS Code ; toutefois, si vous disposez d’un autre éditeur JSON (tel que Visual Studio), vous pouvez utiliser cet éditeur.

## <a name="get-vs-code-and-extension"></a>Obtenir VS Code et l’extension
1. Si nécessaire, installez VS Code [https://code.visualstudio.com/](https://code.visualstudio.com/).

2. Installez l’extension [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) en accédant à Quick Open (Ctrl + P) et en exécutant : 

  ```
  ext install msazurermtools.azurerm-vscode-tools
  ```

3. Redémarrez VS Code lorsque vous êtes invité à activer l’extension.

## <a name="create-blank-template"></a>Créer un modèle vide

Commençons par un modèle vide qui inclut uniquement les sections de base d’un modèle.

1. Créez un fichier. 

2. Copiez et collez la syntaxe JSON suivante dans votre fichier :

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
  }
   ```

3. Enregistrez ce fichier sous **azuredeploy.json**. 

## <a name="add-storage-account"></a>Ajout d’un compte de stockage
1. Pour définir un compte de stockage pour le déploiement, ajoutez ce compte de stockage à la section **ressources** de votre modèle. Pour rechercher les valeurs qui sont disponibles pour le compte de stockage, consultez la [référence de modèle de comptes de stockage](/azure/templates/microsoft.storage/storageaccounts). Copiez le code JSON qui est affiché pour le compte de stockage. 

3. Collez ce code JSON dans la section **ressources** de votre modèle, comme illustré dans l’exemple suivant : 

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  },
    "variables": {  },
    "resources": [
      {
        "name": "string",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2016-05-01",
        "sku": {
          "name": "string"
        },
        "kind": "string",
        "location": "string",
        "tags": {},
        "properties": {
          "customDomain": {
            "name": "string",
            "useSubDomain": boolean
          },
          "encryption": {
            "services": {
              "blob": {
                "enabled": boolean
              }
            },
            "keySource": "Microsoft.Storage"
          },
          "accessTier": "string"
        }
      }
    ],
    "outputs": {  }
  }
  ```

  L’exemple précédent inclut de nombreuses valeurs d’espace réservé et des propriétés dont vous n’aurez peut-être pas besoin dans votre compte de stockage.

## <a name="set-values-for-storage-account"></a>Définir des valeurs pour le compte de stockage

À présent, vous êtes prêt à définir des valeurs pour votre compte de stockage. 

1. Examinez à nouveau la [référence de modèle de comptes de stockage](/azure/templates/microsoft.storage/storageaccounts) dans laquelle vous avez copié le code JSON. Plusieurs tables décrivent les propriétés et fournissent les valeurs disponibles. 

2. Notez que dans l’élément **propriétés**, **customDomain**, **encryption** et **accessTier** sont tous répertoriés comme non requis. Ces valeurs peuvent être importantes pour vos scénarios, mais pour simplifier cet exemple, nous allons les supprimer.

  ```json
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "string",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  ```

3. Actuellement, l’élément **type** est défini sur une valeur d’espace réservé (« chaîne »). VS Code inclut de nombreuses fonctionnalités qui vous aident à comprendre les valeurs à utiliser dans votre modèle. VS Code indique que cette valeur n’est pas valide. Si vous placez le pointeur sur « chaîne », VS Code suggère que les valeurs valides pour **type** sont `Storage` ou `BlobStorage`. 

  ![afficher les valeurs suggérées par VS Code](./media/resource-manager-create-first-template/vs-code-show-values.png)

  Pour afficher les valeurs disponibles, supprimez les caractères entre guillemets et sélectionnez **Ctrl + espace**. Sélectionnez **Stockage** parmi les options disponibles.
  
  ![afficher intellisense](./media/resource-manager-create-first-template/intellisense.png)

  Si vous n’utilisez pas VS Code, examinez la page de référence de modèle de comptes de stockage. La description répertorie les mêmes valeurs valides. Définissez l’élément sur **Stockage**.

  ```json
  "kind": "Storage",
  ```

Votre modèle doit maintenant ressembler à ceci :

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>Ajouter la fonction de modèle

Les fonctions de votre modèle permettent de simplifier la syntaxe du modèle et de récupérer les valeurs qui sont disponibles uniquement lorsque le modèle est déployé. Pour obtenir la liste complète des fonctions de modèle, voir [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).

Pour spécifier que le compte de stockage doit être déployé sur le même emplacement que le groupe de ressources, définissez la propriété **emplacement** sur :

```json
"location": "[resourceGroup().location]",
```

Là encore, VS Code vous aide en suggérant les fonctions disponibles. 

![afficher les fonctions](./media/resource-manager-create-first-template/show-functions.png)

La fonction est placée entre crochets. La fonction [resourceGroup](resource-group-template-functions.md#resourcegroup) renvoie un objet avec une propriété appelée `location`. Le groupe de ressources comprend toutes les ressources pour votre solution. Vous pouvez coder en dur la propriété emplacement avec une valeur telle que « États-Unis du Centre », mais vous devrez modifier manuellement le modèle pour redéployer vers un autre emplacement. La fonction `resourceGroup` vous permet de redéployer ce modèle vers un autre groupe de ressources dans un emplacement différent.



Votre modèle doit maintenant ressembler à ceci :

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-parameters-and-variables"></a>Ajouter des paramètres et des variables
Il existe seulement deux valeurs à définir dans votre modèle : **nom** et **sku.name**. Pour ces propriétés, vous ajoutez des paramètres qui vous permettent de personnaliser ces valeurs au cours du déploiement. 

Les noms de compte de stockage ont de nombreuses restrictions qui rendent leur définition plus difficile. Le nom doit comprendre entre 3 et 24 caractères, comporter uniquement des lettres en minuscules et des nombres, et être unique. Au lieu d’essayer de deviner une valeur unique correspondant aux restrictions, utilisez la fonction [uniqueString](resource-group-template-functions.md#uniquestring) pour générer une valeur de hachage. Pour donner à cette valeur de hachage plus de sens, ajoutez un préfixe qui vous aide à l’identifier comme un compte de stockage après le déploiement. 

1. Pour transmettre un préfixe pour le nom qui correspond à vos conventions d’affectation de noms, accédez à la section **paramètres** de votre modèle. Ajoutez un paramètre au modèle qui accepte un préfixe pour le nom de compte de stockage :

  ```json
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
  },
  ```

  Le préfixe est limité à un maximum de 11 caractères, car `uniqueString` retourne 13 caractères et le nom ne peut pas dépasser 24 caractères. Si vous ne transmettez pas de valeur pour le paramètre pendant le déploiement, la valeur par défaut est utilisée.

2. Accédez à la section **variables** du modèle. Pour construire le nom à partir du préfixe et de la chaîne unique, ajoutez la variable suivante :

  ```json
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  ```

3. Dans la section **ressources**, définissez le nom de compte de stockage sur cette variable.

  ```json
  "name": "[variables('storageName')]",
  ```

3. Pour activer le passage dans différentes références SKU pour le compte de stockage, accédez à la section **paramètres**. Après le paramètre pour le préfixe du nom de stockage, ajoutez un paramètre qui spécifie les valeurs de référence SKU autorisées et une valeur par défaut. Vous trouverez les valeurs autorisées à partir de la page de référence de modèle ou de VS Code. Dans l’exemple suivant, vous incluez toutes les valeurs valides pour la référence SKU. Toutefois, vous pouvez limiter les valeurs autorisées uniquement aux types de références SKU que vous souhaitez déployer via ce modèle.

  ```json
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  ```

3. Modifiez la propriété de référence SKU pour utiliser la valeur du paramètre :

  ```json
  "sku": {
    "name": "[parameters('storageSKU')]"
  },
  ```    

4. Enregistrez votre fichier.

Votre modèle doit maintenant ressembler à ceci :

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Votre modèle est terminé, et vous êtes prêt à le déployer sur votre abonnement. Pour déployer, voir [Déployer des ressources sur Azure](resource-manager-quickstart-deploy.md).
* Pour plus d’informations sur la structure du modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).

