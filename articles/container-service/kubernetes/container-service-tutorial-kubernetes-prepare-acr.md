---
title: "Didacticiel Azure Container Service - Préparer l’ACR | Microsoft Docs"
description: "Didacticiel Azure Container Service - Préparer l’ACR"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 3e1f7617bf2fc52ee4c15598f51a46276f4dc57d
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Déployer et utiliser Azure Container Registry

Azure Container Registry (ACR) est un registre privé Azure pour les images de conteneur Docker. Ce didacticiel (le deuxième d’une série de sept) vous aide à déployer une instance Azure Container Registry et à envoyer une image conteneur à ce dernier. Les étapes terminées sont les suivantes :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry (ACR)
> * Marquage d’une image conteneur pour ACR
> * Chargement de l’image dans ACR

Dans les didacticiels suivants, cette instance ACR est intégrée à un cluster Azure Container Service Kubernetes, pour l’exécution des images de conteneur en toute sécurité. 

## <a name="before-you-begin"></a>Avant de commencer

Dans le [didacticiel précédent](./container-service-tutorial-kubernetes-prepare-app.md), une image conteneur a été créée pour une application Azure Vote. Dans ce didacticiel, cette image est envoyée à Azure Container Registry. Si vous n’avez pas créé l’image de l’application Azure Vote, retournez au [Didacticiel 1 : Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md). Sinon, les étapes présentées en détail ici fonctionnent avec n’importe quelle image de conteneur.

Ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Déployer Azure Container Registry

Lorsque vous déployez un registre de conteneurs Azure, il vous faut tout d’abord un groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Dans cet exemple, un groupe de ressources nommé *myResourceGroupVM* est créé dans la région *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Créez un registre de conteneurs Azure à l’aide de la commande [az acr create](/cli/azure/acr#create). Le nom d’un registre de conteneurs **doit être unique**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

Dans le reste de ce didacticiel, nous utilisons « acrname ». Ce nom correspond au registre de conteneurs que vous avez choisi.

## <a name="container-registry-login"></a>Connexion au registre de conteneurs

Vous devez vous connecter à votre instance ACR avant de lui envoyer des images. Utilisez la commande [az acr login](https://docs.microsoft.com/en-us/cli/azure/acr#login) pour terminer l’opération. Vous devez fournir le nom unique qui a été donné au Registre de conteneurs au moment de sa création.

```azurecli
az acr login --name <acrName>
```

Après son exécution, la commande retourne le message « Connexion réussie ».

## <a name="tag-container-images"></a>Marquer les images de conteneur

Chaque image conteneur doit être marquée avec le nom de serveur de connexion du registre. Cette balise est utilisée pour l’acheminement lors de l’envoi des images de conteneur dans un registre d’images.

Pour afficher la liste des images actuelles, utilisez la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Pour obtenir le nom de loginServer, exécutez la commande suivante.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Maintenant, marquez l’image *azure-vote-front* avec le loginServer du Registre de conteneurs. En outre, ajoutez `:redis-v1` à la fin du nom de l’image. Cette balise indique la version de l’image.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

Une fois l’image marquée, exécutez [docker images] (https://docs.docker.com/engine/reference/commandline/images/) pour vérifier l’opération.

```bash
docker images
```

Output:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Envoyer des images au registre

Envoyez l’image *azure-vote-front* au registre. 

À l’aide de l’exemple suivant, remplacez le nom d’ACR loginServer par le loginServer à partir de votre environnement.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

Quelques minutes sont nécessaires pour achever l’opération.

## <a name="list-images-in-registry"></a>Créer la liste des images du registre

Pour retourner une liste d’images qui ont été déplacées dans le registre de conteneurs Azure, utilisez la commande [az acr repository list](/cli/azure/acr/repository#list). Mettez à jour la commande avec le nom d’instance ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```azurecli
Result
----------------
azure-vote-front
```

Puis, pour afficher les balises d’une image spécifique, utilisez la commande [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Output:

```azurecli
Result
--------
redis-v1
```

Au terme de ce didacticiel, l’image conteneur est stockée dans une instance privée Azure Container Registry. Dans les didacticiels suivants, cette image est déployée à partir d’ACR vers un cluster Kubernetes.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, un registre de conteneurs Azure a été préparé pour une utilisation dans un cluster ACS Kubernetes. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry
> * Marquage d’une image conteneur pour ACR
> * Chargement de l’image dans ACR

Passer à l’étape suivante du didacticiel pour en savoir plus sur le déploiement d’un cluster Kubernetes dans Azure.

> [!div class="nextstepaction"]
> [Déployer un cluster Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)
