---
title: "Gestion des ressources avec l’interface CLI Azure | Microsoft Docs"
description: "Utiliser l’interface de ligne de commande Azure (CLI) pour gérer les groupes et les ressources Azure"
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: c68f2a8b6e18dc2d51d8bbb5cd05bc037dc2fadb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Utiliser l’interface de ligne de commande Azure pour gérer les ressources et les groupes de ressources Azure

Dans cet article, vous allez apprendre à gérer vos solutions avec Azure CLI et Azure Resource Manager. Si vous n’êtes pas familiarisé avec Resource Manager, consultez la page [Vue d’ensemble de Resource Manager](resource-group-overview.md). Cet article se concentre sur les tâches de gestion. Vous allez :

1. Créer un groupe de ressources
2. Ajouter une ressource au groupe de ressources
3. Ajouter une balise à la ressource
4. Interroger les ressources selon des noms ou des valeurs de balise
5. Appliquer et supprimer un verrou sur la ressource
6. Supprimer un groupe de ressources

Cet article n’indique pas comment déployer un modèle Resource Manager sur votre abonnement. Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pour installer et utiliser l’interface CLI localement, consultez [Install Azure CLI 2.0](/cli/azure/install-azure-cli) (Installer Azure CLI 2.0).

## <a name="set-subscription"></a>Définir l’abonnement

Si vous avez plusieurs abonnements, vous pouvez en choisir un autre. Tout d’abord, nous allons voir tous les abonnements de votre compte.

```azurecli-interactive
az account list
```

Une liste des abonnements activés et désactivés est retournée.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

Notez qu’un abonnement est marqué comme abonnement par défaut. Cet abonnement correspond au contexte actuel des opérations. Pour choisir un autre abonnement, indiquez son nom avec la commande **az account set**.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Pour afficher le contexte de l’abonnement actuel, utilisez **az account show** sans paramètre :

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de déployer des ressources dans votre abonnement, vous devez créer un groupe de ressources qui contiendra ces ressources.

Pour créer un groupe de ressources, utilisez la commande **az group create**. La commande utilise le paramètre **name** pour attribuer un nom au groupe de ressources et le paramètre **location** pour indiquer son emplacement.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

La sortie est au format suivant :

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Si vous avez besoin de récupérer le groupe de ressources ultérieurement, utilisez la commande suivante :

```azurecli-interactive
az group show --name TestRG1
```

Pour récupérer les groupes de ressources de votre abonnement, utilisez ce qui suit :

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Ajouter des ressources à un groupe de ressources

Pour ajouter une ressource au groupe de ressources, vous pouvez utiliser la commande **az resource create** ou une commande spécifique au type de ressource que vous créez (comme **az storage account create**). Il est peut-être plus facile d’utiliser une commande spécifique à un type de ressource, car elle inclut les paramètres relatifs aux propriétés requises pour la nouvelle ressource. Pour utiliser **az resource create**, vous devez connaître toutes les propriétés à définir, même si vous n’êtes pas invité à les entrer.

Cependant, l’ajout d’une ressource à l’aide de scripts risque de créer une confusion par la suite, car la nouvelle ressource n’existe pas dans un modèle Resource Manager. Les modèles vous permettent de déployer votre solution plusieurs fois de manière fiable.

La commande suivante génère un compte de stockage. Au lieu d’utiliser le nom indiqué dans l’exemple, entrez un nom unique pour le compte de stockage. Le nom doit comprendre entre 3 et 24 caractères et comporter uniquement des lettres en minuscules et des nombres. Si vous utilisez le nom indiqué dans l’exemple, vous recevez une erreur, car ce nom est déjà en cours d’utilisation.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Si vous avez besoin de récupérer cette ressource ultérieurement, utilisez la commande suivante :

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Ajouter une balise

Les balises vous permettent d’organiser vos ressources en fonction de différentes propriétés. Par exemple, vous pouvez disposer de plusieurs ressources incluses dans différents groupes de ressources appartenant au même service. Vous pouvez appliquer une valeur et une balise de service à ces ressources pour les marquer comme appartenant à la même catégorie. Vous pouvez également indiquer si une ressource est utilisée dans un environnement de production ou de test. Dans le cadre de cet article, vous appliquez des balises à une seule ressource, mais il conviendra probablement d’appliquer des balises à toutes vos ressources dans votre environnement.

La commande suivante applique deux balises à votre compte de stockage :

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Les balises sont mises à jour en tant qu’objet unique. Pour ajouter une balise à une ressource qui inclut déjà des balises, commencez par récupérer les balises existantes. Ajoutez la nouvelle balise à l’objet qui contient les balises existantes et appliquez de nouveau toutes les balises à la ressource.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Rechercher des ressources

Utilisez la commande **az resource list** pour récupérer les ressources selon différentes conditions de recherche.

* Pour obtenir une ressource par son nom, indiquez le paramètre **name** :

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Pour obtenir toutes les ressources d’un groupe de ressources, indiquez le paramètre **resource-group** :

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Pour obtenir toutes les ressources ayant une valeur et un nom de balise, indiquez le paramètre **tag** :

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Pour obtenir toutes les ressources d’un type particulier, indiquez le paramètre **resource-type** :

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Obtenir l’ID de ressource

De nombreuses commandes utilisent un ID de ressource comme paramètre. Pour obtenir l’ID d’une ressource et le stocker dans une variable, utilisez :

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Verrouiller une ressource

Pour vous assurer qu’une ressource critique ne sera pas accidentellement supprimée ou modifiée, appliquez un verrou à la ressource. Vous pouvez spécifier le niveau **CanNotDelete** ou **ReadOnly**.

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès à des actions `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`. Parmi les rôles prédéfinis, seuls les rôles Propriétaire et Administrateur de l'accès utilisateur peuvent effectuer ces actions.

Pour appliquer un verrou utilisez la commande suivante :

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

La ressource verrouillée de l’exemple précédent ne peut pas être effacée tant que le verrou n’est pas supprimé. Pour supprimer un verrou, utilisez :

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Pour plus d’informations sur la définition des verrous, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Supprimer des ressources ou un groupe de ressources
Vous pouvez supprimer une ressource ou un groupe de ressources. Lorsque vous supprimez un groupe de ressources, vous supprimez également toutes les ressources qu’il contient.

* Pour supprimer une ressource du groupe de ressources, utilisez la commande de suppression pour le type de ressource que vous supprimez. La commande supprime la ressource, mais pas le groupe de ressources.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Pour supprimer un groupe de ressources et toutes ses ressources, utilisez la commande **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

Pour les deux commandes, vous êtes invité à confirmer que vous souhaitez supprimer la ressource ou le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour savoir comment déployer des modèles, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy-cli.md).
* Vous pouvez déplacer des ressources existantes vers un nouveau groupe de ressources. Pour obtenir des exemples, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).