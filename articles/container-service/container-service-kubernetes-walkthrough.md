---
title: "Démarrage rapide : Cluster Azure Kubernetes pour Linux | Microsoft Docs"
description: "Découvrez rapidement comment créer un cluster Kubernetes pour des conteneurs Linux dans Azure Container Service, avec Azure CLI."
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 25043f6bf5e5ab3def8563bd2c096b79706bfec1
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Déployer un cluster Azure Kubernetes pour des conteneurs Linux

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide explique en détail comment utiliser Azure CLI pour déployer un cluster [Kubernetes](https://kubernetes.io/docs/home/) dans [Azure Container Service](container-service-intro.md). Une fois le cluster déployé, vous pouvez vous y connecter avec l’outil en ligne de commande Kubernetes `kubectl`, puis déployer votre premier conteneur Linux.

Ce didacticiel requiert Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Créer un cluster Kubernetes
Pour créer un cluster Kubernetes dans Azure Container Service, utilisez la commande [az acs create](/cli/azure/acs#create). 

L’exemple ci-après permet de créer un cluster nommé *myK8sCluster*, qui inclut un nœud maître Linux et deux nœuds agents Windows. Cet exemple permet de créer les clés SSH, si elles ne figurent pas déjà aux emplacements par défaut. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`. Mettez à jour le nom du cluster, en indiquant une valeur adaptée à votre environnement. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --generate-ssh-keys 
```

Au bout de quelques minutes, la commande s’achève et vous fournit des informations sur votre déploiement.

## <a name="install-kubectl"></a>Installer kubectl

Pour vous connecter au cluster Kubernetes depuis l’ordinateur client, utilisez l’outil [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), le client de ligne de commande Kubernetes. 

Si vous utilisez Azure CloudShell, l’outil `kubectl` est déjà installé. Si vous souhaitez l’installer en local, vous pouvez utiliser la commande [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

L’exemple Azure CLI ci-après installe `kubectl` sur votre système. Si vous exécutez Azure CLI sur macOS ou Linux, vous devrez peut-être exécuter la commande avec `sudo`.

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>Se connecter avec kubectl

Pour configurer le client `kubectl` afin qu’il se connecte à votre cluster Kubernetes, exécutez la commande [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). L’exemple ci-après télécharge la configuration de votre cluster Kubernetes.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Pour vérifier la connexion à votre cluster à partir de votre machine, essayez d’exécuter la commande suivante :

```azurecli-interactive
kubectl get nodes
```

Le client `kubectl` répertorie les nœuds maître et agents.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>Déployer un conteneur NGINX

Vous pouvez exécuter un conteneur Docker à l’intérieur d’un *pod* Kubernetes, qui contient un ou plusieurs conteneurs. 

La commande suivante démarre le conteneur Docker NGINX dans un pod Kubernetes, sur l’un des nœuds. Dans ce cas, le conteneur exécute le serveur web NGINX extrait d’une image dans le [Hub Docker](https://hub.docker.com/_/nginx/).

```azurecli-interactive
kubectl run nginx --image nginx
```
Pour voir si le conteneur est en cours d’exécution, exécutez la commande suivante :

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>Afficher la page d’accueil NGINX
Pour exposer le serveur NGINX avec une adresse IP publique, saisissez la commande suivante :

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Grâce à cette commande, Kubernetes crée un service et une [règle Azure Load Balancer](container-service-kubernetes-load-balancing.md) avec une adresse IP publique pour ce service. 

Exécutez la commande suivante pour afficher l’état du service.

```azurecli-interactive
kubectl get svc
```

L’adresse IP affichée initialement apparaît comme étant `pending`. Après quelques minutes, l’adresse IP externe du service est définie :
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

Vous pouvez utiliser un navigateur web de votre choix pour visualiser la page d’accueil NGINX par défaut à l’adresse IP externe :

![Image de la navigation vers Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>Supprimer un cluster
Lorsque vous n’avez plus besoin du cluster, vous pouvez utiliser la commande [az group delete](/cli/azure/group#delete) pour supprimer le groupe de ressources, le service de conteneur et toutes les ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Étapes suivantes

Par le biais de ce guide de démarrage rapide, vous avez déployé un cluster Kubernetes, vous vous êtes connecté avec l’outil `kubectl` et vous avez déployé un pod avec un conteneur NGINX. Pour en savoir plus sur Azure Container Service, passez au didacticiel relatif au cluster Kubernetes.

> [!div class="nextstepaction"]
> [Gérer un cluster Kubernetes ACS](./container-service-tutorial-kubernetes-prepare-app.md)

