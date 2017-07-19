---
title: "Créez un registre de Docker privé dans le portail Azure | Microsoft Docs"
description: "Prise en main de la création et de la gestion de registres de conteneurs Docker privés avec le portail Azure"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: fr-fr
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>Créer un registre de conteneurs géré à l’aide du portail Azure

Azure Container Registry est un service de registre de conteneurs Docker géré utilisé pour stocker des images de conteneurs Docker privés. Ce guide décrit en détail la création d’une instance Azure Container Registry gérée à l’aide du portail Azure.

Les registres de conteneurs Azure gérés sont en préversion et non disponibles dans toutes les régions.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Recherchez **Azure Container Registry** dans la Place de marché et sélectionnez cette entrée.

3. Cliquez sur **Créer** afin d’ouvrir le panneau de création ACR.

    ![Paramètres de Container Registry](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. Dans le panneau **Azure Container Registry**, entrez les informations suivantes. Une fois ces opérations effectuées, cliquez sur **Créer**.

    a. **Nom du registre** : Un nom de domaine global unique et de niveau supérieur pour votre registre spécifique. Dans cet exemple, le nom de registre est *myAzureContainerRegistry1*, mais vous pouvez le remplacer par un nom unique de votre choix. Le nom ne peut contenir que des lettres et des chiffres.

    b. **Groupe de ressources** : sélectionnez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups) existant ou tapez le nom d’un nouveau groupe.

    c. **Emplacement** : sélectionnez un emplacement de centre de données Azure où le service est [disponible](https://azure.microsoft.com/regions/services/), tel que **Sud du centre des États-Unis**.

    d. **Utilisateur Administrateur** : si vous le souhaitez, vous pouvez autoriser l’accès au registre à un utilisateur Administrateur. Vous pouvez modifier ce paramètre une fois le registre créé.

    e. **Utiliser un registre géré** : sélectionnez Oui pour qu’ACR gère le stockage du registre, utilise des webhooks et l’authentification AAD automatiquement.

    f. **Niveau tarifaire** : sélectionnez un niveau tarifaire. Consultez ici les prix ACR pour plus d’informations.

## <a name="log-in-to-acr-instance"></a>Connexion à l’instance ACR

Avant d’extraire et de transmettre des images conteneur, vous devez vous connecter à l’instance ACR. 

Pour ce faire, utilisez Azure CLI 2.0. Tout d’abord, si nécessaire, connectez-vous à Azure à l’aide de la commande [az login](/cli/azure/#login). 

```azurecli
az login
```

Ensuite, utilisez la commande [az acr login](/cli/azure/acr#login) pour vous connecter à Azure Container Registry.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

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

Dans ce guide de démarrage rapide, vous avez créé une instance Azure Container Registry gérée à l’aide du portail Azure.

> [!div class="nextstepaction"]
> [Effectuer un push de votre première image à l’aide de l’interface CLI Docker](container-registry-get-started-docker-cli.md)
