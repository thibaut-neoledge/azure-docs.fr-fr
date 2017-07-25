---
title: "Créez un registre de conteneur Docker privé dans Azure CLI | Microsoft Docs"
description: "Prise en main de la création et de la gestion de registres de conteneurs Docker privés avec Azure CLI 2.0"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: fr-fr
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>Créer un registre de conteneurs géré à l’aide de l’interface CLI Azure

Azure Container Registry est un service de registre de conteneurs Docker géré utilisé pour stocker des images de conteneurs Docker privés. Ce guide décrit en détail la création d’une instance Azure Container Registry gérée à l’aide de l’interface CLI Azure.

Les registres de conteneurs Azure gérés sont en préversion et non disponibles dans toutes les régions.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *westcentralus*.

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Créez une instance ACR à l’aide de la commande [az acr create](/cli/azure/acr#create).

> [!NOTE]
> Lorsque vous créez un registre, spécifiez un nom de domaine global unique de niveau supérieur, contenant uniquement des chiffres et des lettres.

 Dans cet exemple, le nom de registre est *myContainerRegistry1*, mais vous pouvez le remplacer par un nom unique de votre choix.

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

Une fois le registre créé, la sortie ressemble à ce qui suit :

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>Connexion à l’instance ACR

Avant d’extraire et de transmettre des images conteneur, vous devez vous connecter à l’instance ACR. Pour ce faire, utilisez la commande [az acr login](/cli/azure/acr#login).

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

Après son exécution, la commande retourne le message « Connexion réussie ».

## <a name="use-azure-container-registry"></a>Utilisation d’Azure Container Registry

### <a name="list-container-images"></a>Répertorier les images conteneur

Utilisez les commandes CLI `az acr` pour interroger les images et les balises dans un référentiel.

> [!NOTE]
> Actuellement, Container Registry ne prend pas en charge la commande `docker search` permettant d’effectuer une requête sur les images et les balises.

### <a name="list-repositories"></a>Répertorier les référentiels

L’exemple suivant répertorie les référentiels dans un registre, au format JSON (JavaScript Object Notation) :

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Répertorier les balises

L’exemple suivant répertorie les balises sur le référentiel **exemples/nginx**, au format JSON :

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une instance Azure Container Registry gérée à l’aide de l’interface CLI Azure.

> [!div class="nextstepaction"]
> [Effectuer un push de votre première image à l’aide de l’interface CLI Docker](container-registry-get-started-docker-cli.md)

