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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: adc00cb52d5805e152ca742f4cd6b9fab3ed3788
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Déployer et utiliser Azure Container Registry

Azure Container Registry (ACR) est un registre privé Azure pour les images de conteneur Docker. Ce didacticiel vous guide à travers le déploiement d’une instance Azure Container Registry et l’envoi des images de conteneur à ce dernier. Les étapes terminées sont les suivantes :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry
> * Marquage des images de conteneur pour ACR
> * Téléchargement d’images dans l’ACR

Dans les didacticiels suivants, cette instance ACR est intégrée à un cluster Azure Container Service Kubernetes, pour l’exécution des images de conteneur en toute sécurité. 

## <a name="before-you-begin"></a>Avant de commencer

Dans le [didacticiel précédent](./container-service-tutorial-kubernetes-prepare-app.md), les images de conteneur ont été créées pour une application de votes Azure simple. Dans ce didacticiel, ces images sont envoyées à un registre de conteneurs Azure. Si vous n’avez pas créé les images de l’application de votes Azure, retournez au [Didacticiel 1 : créer des images de conteneur](./container-service-tutorial-kubernetes-prepare-app.md). Sinon, les étapes présentées en détail ici fonctionnent avec n’importe quelle image de conteneur.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel exige que vous exécutiez Azure CLI version 2.0.4 ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Déployer Azure Container Registry

Lorsque vous déployez un registre de conteneurs Azure, il vous faut tout d’abord un groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Dans cet exemple, un groupe de ressources nommé *myResourceGroupVM* est créé dans la région *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Créez un registre de conteneurs Azure à l’aide de la commande [az acr create](/cli/azure/acr#create). Le nom d’un registre de conteneurs **doit être unique**. À l’aide de l’exemple suivant, mettez à jour le nom avec des caractères aléatoires.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic --admin-enabled true
```

## <a name="get-acr-information"></a>Obtenir des informations ACR 

Une fois l’instance ACR créée, le nom, le nom du serveur de connexion et le mot de passe d’authentification sont nécessaires. Le code suivant retourne chacune de ces valeurs. Notez chaque valeur, elles sont référencées tout au long de ce didacticiel.  

Nom ACR et serveur de connexion :

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrName:name,acrLoginServer:loginServer}" --output table
```

Mot de passe ACR : mise à jour avec le nom d’ACR.

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value -o tsv
```

## <a name="container-registry-login"></a>Connexion au registre de conteneurs

Vous devez vous connecter à votre instance ACR avant de lui envoyer des images. Utilisez la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/) pour terminer l’opération. Lors de l’exécution de la connexion docker, vous devez fournir le nom du serveur de connexion ACR et les informations d’identification ACR.

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

Après son exécution, la commande retourne le message « Connexion réussie ».

## <a name="tag-container-images"></a>Marquer les images de conteneur

Chaque image de conteneur doit être marquée avec le `loginServer` nom du registre. Cette balise est utilisée pour l’acheminement lors de l’envoi des images de conteneur dans un registre d’images.

Pour afficher une liste des images en cours, utilisez la commande `docker images`.

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

Marquer l’image *azure-vote-front* à l’aide du loginServer du registre de conteneurs. En outre, ajoutez `:v1` à la fin du nom de l’image. Cette balise indique le numéro de version de l’image.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Répétez la commande avec l’image *azure-vote-back*.

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

Une fois marqué, exécutez `docker images` pour vérifier l’opération.

```bash
docker images
```

Output:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                                      latest              a9dace4e1a17        7 minutes ago       407 MB
mycontainerregistry082.azurecr.io/azure-vote-back    v1                  a9dace4e1a17        7 minutes ago       407 MB
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1                  eaf2b9c57e5e        8 minutes ago       716 MB
mysql                                                latest              e799c7f9ae9c        6 weeks ago         407 MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-acr"></a>Envoyer des images à l’ACR

Envoyez l’image *azure-vote-front* au registre. 

À l’aide de l’exemple suivant, remplacez le nom d’ACR loginServer par le loginServer à partir de votre environnement. Quelques minutes sont nécessaires pour achever l’opération.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Faites de même pour l’image *azure-vote-back*.

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

## <a name="list-images-in-acr"></a>Répertorier des images dans ACR 

Pour retourner une liste d’images qui ont été déplacées dans le registre de conteneurs Azure, utilisez la commande [az acr repository list](/cli/azure/acr/repository#list). Mettez à jour la commande avec le nom d’instance ACR.

```azurecli-interactive
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

Output:

```azurecli
Result
----------------
azure-vote-back
azure-vote-front
```

Puis, pour afficher les balises d’une image spécifique, utilisez la commande [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli-interactive
az acr repository show-tags --name <acrName> --username <acrName> --password <acrPassword> --repository azure-vote-front --output table
```

Output:

```azurecli
Result
--------
v1
```

Au terme de ce didacticiel, les deux images de conteneur ont été stockées dans une instance privée Azure Container Registry. Ces images sont déployées à partir de l’ACR vers un cluster Kubernetes dans les didacticiels suivants.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, un registre de conteneurs Azure a été préparé pour une utilisation dans un cluster ACS Kubernetes. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry
> * Marquage des images de conteneur pour ACR
> * Téléchargement d’images dans l’ACR

Passer à l’étape suivante du didacticiel pour en savoir plus sur le déploiement d’un cluster Kubernetes dans Azure.

> [!div class="nextstepaction"]
> [Déployer un cluster Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)
