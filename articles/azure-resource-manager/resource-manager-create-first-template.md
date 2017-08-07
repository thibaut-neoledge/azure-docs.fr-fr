---
title: "Créer le premier modèle Azure Resource Manager | Microsoft Docs"
description: "Guide pas à pas pour la création de votre premier modèle Azure Resource Manager. Ce guide montre comment utiliser la référence de modèle pour un compte de stockage afin de créer un modèle."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/27/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 49086b51e2db1aebed45746306ae14b6f1feb631
ms.contentlocale: fr-fr
ms.lasthandoff: 07/31/2017

---

# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Créer et déployer votre premier modèle Azure Resource Manager
Cette rubrique vous guide tout au long des étapes de création de votre premier modèle Azure Resource Manager. Les modèles Azure Resource Manager sont des fichiers JSON qui définissent les ressources nécessaires au déploiement de votre solution. Pour comprendre les concepts associés au déploiement et à la gestion de vos solutions Azure, voir [Présentation d’Azure Resource Manager](resource-group-overview.md). Si vous disposez de ressources existantes et que vous souhaitez créer un modèle pour ces ressources, voir [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md).

Pour créer et réviser des modèles, vous avez besoin d’un éditeur JSON. [Visual Studio Code](https://code.visualstudio.com/) est un éditeur de code multiplateforme, open source et léger. Nous vous recommandons vivement d’utiliser Visual Studio Code pour créer des modèles Resource Manager. Cette rubrique suppose que vous utilisez VS Code ; toutefois, si vous disposez d’un autre éditeur JSON (tel que Visual Studio), vous pouvez utiliser cet éditeur.

## <a name="prerequisites"></a>Composants requis

* Visual Studio Code. Si nécessaire, installez-le à partir de [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-template"></a>Créer un modèle

Commençons avec un modèle simple qui déploie un compte de stockage sur votre abonnement.

1. Sélectionnez **Fichier** > **Nouveau fichier**. 

   ![Nouveau fichier](./media/resource-manager-create-first-template/new-file.png)

2. Copiez et collez la syntaxe JSON suivante dans votre fichier :

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   Les noms de compte de stockage ont de nombreuses restrictions qui rendent leur définition plus difficile. Le nom doit comprendre entre 3 et 24 caractères, comporter uniquement des lettres en minuscules et des nombres, et être unique. Le modèle précédent utilise la fonction [uniqueString](resource-group-template-functions-string.md#uniquestring) pour générer une valeur de hachage. Pour donner plus de sens à cette valeur de hachage, il ajoute le préfixe *stockage*. 

3. Enregistrez ce fichier sous **azuredeploy.json** dans un dossier local.

   ![Enregistrer un modèle](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>Déployer un modèle

Vous êtes prêt à déployer ce modèle. Vous utilisez soit PowerShell, soit Azure CLI pour créer un groupe de ressources. Vous déployez ensuite un compte de stockage dans ce groupe de ressources.

* Pour PowerShell, utilisez les commandes suivantes à partir du dossier qui contient le modèle :

   ```powershell
   Login-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Pour une installation locale d’Azure CLI, utilisez les commandes suivantes à partir du dossier qui contient le modèle :

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

Une fois le déploiement terminé, votre compte de stockage existe dans le groupe de ressources.

## <a name="deploy-template-from-cloud-shell"></a>Déployer le modèle à partir de Cloud Shell

Vous pouvez utiliser [Cloud Shell](../cloud-shell/overview.md) pour exécuter les commandes Azure CLI afin de déployer votre modèle. Toutefois, vous devez d’abord charger votre modèle dans le partage de fichiers de votre Cloud Shell. Si vous n’avez pas utilisé Cloud Shell, consultez [Vue d’ensemble d’Azure Cloud Shell (version préliminaire)](../cloud-shell/overview.md) pour plus d’informations sur sa configuration.

1. Connectez-vous au [portail Azure](https://portal.azure.com).   

2. Sélectionnez votre groupe de ressources Cloud Shell. Le modèle de nom est `cloud-shell-storage-<region>`.

   ![Sélection du groupe de ressources](./media/resource-manager-create-first-template/select-cs-resource-group.png)

3. Sélectionnez le compte de stockage de votre Cloud Shell.

   ![Sélectionner le compte de stockage](./media/resource-manager-create-first-template/select-storage.png)

4. Sélectionnez **Fichiers**.

   ![Sélectionner des fichiers](./media/resource-manager-create-first-template/select-files.png)

5. Sélectionnez le partage de fichiers pour Cloud Shell. Le modèle de nom est `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Sélectionner le partage de fichiers](./media/resource-manager-create-first-template/select-file-share.png)

6. Sélectionnez **Ajouter un répertoire**.

   ![Ajouter un répertoire](./media/resource-manager-create-first-template/select-add-directory.png)

7. Nommez-le **modèles**, puis sélectionnez **OK**.

   ![Nommer le répertoire](./media/resource-manager-create-first-template/name-templates.png)

8. Sélectionnez votre nouveau répertoire.

   ![Sélectionner le répertoire](./media/resource-manager-create-first-template/select-templates.png)

9. Sélectionnez **Télécharger**.

   ![Sélectionner Télécharger](./media/resource-manager-create-first-template/select-upload.png)

10. Recherchez et chargez votre modèle.

   ![Charger le fichier](./media/resource-manager-create-first-template/upload-files.png)

11. Ouvrez l’invite de commandes.

   ![Ouvrir Cloud Shell](./media/resource-manager-create-first-template/start-cloud-shell.png)

12. Entrez les commandes suivantes dans Cloud Shell :

   ```azurecli
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
   ```

Une fois le déploiement terminé, votre compte de stockage existe dans le groupe de ressources.

## <a name="customize-the-template"></a>Personnaliser le modèle

Le modèle fonctionne correctement, mais il n’est pas flexible. Il déploie toujours un stockage localement redondant pour la région Sud-Centre des États-Unis. Le nom est toujours *stockage* suivi d’une valeur de hachage. Pour permettre l’utilisation du modèle dans différents scénarios, ajoutez des paramètres au modèle.

L’exemple suivant montre la section des paramètres avec deux paramètres. Le premier paramètre `storageSKU` vous permet de spécifier le type de redondance. Il limite les valeurs que vous pouvez transmettre aux valeurs valides pour un compte de stockage. Il spécifie également une valeur par défaut. Le deuxième paramètre `storageNamePrefix` est défini de façon à autoriser un maximum de 11 caractères. Il spécifie une valeur par défaut.

```json
"parameters": {
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
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

Dans la section des variables, ajoutez une variable nommée `storageName`. Elle associe la valeur de préfixe des paramètres à une valeur de hachage de la fonction [uniqueString](resource-group-template-functions-string.md#uniquestring). Elle utilise la fonction [toLower](resource-group-template-functions-string.md#tolower) pour convertir tous les caractères en minuscules.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Pour utiliser ces nouvelles valeurs pour votre compte de stockage, modifiez la définition de ressource :

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

Notez que le nom du compte de stockage est maintenant défini sur la variable que vous avez ajoutée. Le nom de référence SKU est défini sur la valeur du paramètre. L’emplacement est défini sur celui du groupe de ressources.

Enregistrez votre fichier. 

Après avoir effectué les étapes de cet article, votre modèle doit maintenant ressembler à ceci :

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>Redéployer le modèle

Redéployez le modèle avec des valeurs différentes.

Pour PowerShell, utilisez :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Pour l’interface de ligne de commande Azure, consultez :

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

Pour Cloud Shell, chargez votre modèle modifié vers le partage de fichiers. Remplacez le fichier existant. Utilisez ensuite la commande suivante :

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources que vous avez déployées en supprimant le groupe de ressources.

Pour PowerShell, utilisez :

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Pour l’interface de ligne de commande Azure, consultez :

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la structure du modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour en savoir plus sur les propriétés d’un compte de stockage, consultez la [référence sur le modèle des comptes de stockage](/azure/templates/microsoft.storage/storageaccounts).
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).

