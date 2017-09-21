---
title: "Didacticiel Azure Container Service - Déployer un cluster | Microsoft Docs"
description: "Didacticiel Azure Container Service - Déployer un cluster"
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
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 05476e35488c104a483f52f88540d33c55e5bc3c
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Déployer un cluster Kubernetes dans Azure Container Service

Kubernetes fournit une plateforme distribuée destinée aux applications en conteneur. Avec Azure Container Service, l’approvisionnement d’un cluster Kubernetes prêt pour la production est une opération simple et rapide. Dans ce didacticiel (le troisième d’une série de sept), un cluster Azure Container Service Kubernetes est déployé. Les étapes terminées sont les suivantes :

> [!div class="checklist"]
> * Déploiement d’un cluster ACS Kubernetes
> * Installation de l’interface de ligne de commande Kubernetes (kubectl)
> * Configuration de kubectl

Dans les didacticiels suivants, l’application Azure Vote est déployée sur le cluster, puis mise à l’échelle et mise à jour. En outre, Operations Management Suite est configuré pour la surveillance du cluster Kubernetes.

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une image conteneur a été créée et chargée dans une instance Azure Container Registry. Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Créer un cluster Kubernetes

Pour créer un cluster Kubernetes dans Azure Container Service, utilisez la commande [az acs create](/cli/azure/acs#create). 

L’exemple suivant crée un cluster nommé `myK8sCluster` dans le groupe de ressources `myResourceGroup`. Vous avez créé le groupe de ressources au [tutoriel précédent](./container-service-tutorial-kubernetes-prepare-acr.md).

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

Dans certains cas, par exemple avec une version d’évaluation limitée, un abonnement Azure dispose d’un accès limité aux ressources Azure. Si le déploiement échoue à cause d’une limitation du nombre de cœurs disponibles, réduisez le nombre d’agents par défaut en ajoutant `--agent-count 1` à la commande [az acs create](/cli/azure/acs#create). 

Au bout de quelques minutes, le déploiement se termine et retourne des informations au format JSON concernant le déploiement ACS.

## <a name="install-the-kubectl-cli"></a>Installer l’interface de ligne de commande kubectl

Pour vous connecter au cluster Kubernetes à partir de votre ordinateur client, utilisez [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), le client de ligne de commande Kubernetes. 

Si vous utilisez Azure CloudShell, l’outil kubectl est déjà installé. Pour l’installer en local, utilisez la commande [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

Avec Linux ou MacOS, vous devrez peut-être l’exécuter avec sudo. Dans Windows, vérifiez que votre interpréteur de commandes a été exécuté en tant qu’administrateur.

```azurecli-interactive 
az acs kubernetes install-cli 
```

Dans Windows, l’installation par défaut est *c:\program files (x86)\kubectl.exe*. Vous devrez peut-être ajouter ce fichier au chemin de Windows. 

## <a name="connect-with-kubectl"></a>Se connecter avec kubectl

Pour configurer kubectl afin qu’il se connecte à votre cluster Kubernetes, exécutez la commande [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Pour vérifier la connexion à votre cluster, exécutez la commande [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli-interactive
kubectl get nodes
```

Output:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

À l’issue du didacticiel, vous disposez d’un cluster ACS Kubernetes prêt pour les charges de travail. Dans les didacticiels suivants, une application à plusieurs conteneurs est déployée sur ce cluster, mise à l’échelle, mise à jour et surveillée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, un cluster Azure Container Service Kubernetes a été déployé. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement d’un cluster ACS Kubernetes
> * Installation de l’interface de ligne de commande Kubernetes (kubectl)
> * Configuration de kubectl

Passez au didacticiel suivant pour en savoir plus sur l’exécution de l’application sur le cluster.

> [!div class="nextstepaction"]
> [Déployer une application dans Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)

