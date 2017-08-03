---
title: "Didacticiel Azure Container Service - Déployer un cluster | Microsoft Docs"
description: "Didacticiel Azure Container Service - Déployer un cluster"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, microservices, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 1d3006b2100e84920cdfeb7a0762213a93f9a8a5
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017

---

# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Déployer un cluster Kubernetes dans Azure Container Service

Kubernetes propose une plateforme distribuée destinée à exécuter des applications en conteneur modernes. Avec Azure Container Service, l’approvisionnement d’un cluster Kubernetes prêt pour la production est une opération simple et rapide. Ce démarrage rapide décrit les étapes de base nécessaires pour déployer un cluster Kubernetes. Les étapes accomplies sont les suivantes :

> [!div class="checklist"]
> * Déploiement d’un cluster ACS Kubernetes
> * Installation de l’interface de ligne de commande Kubernetes (kubectl)
> * Configuration de kubectl

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, des images conteneur ont été créées et chargées sur une instance Azure Container Registry. Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-kubernetes-cluster"></a>Créer un cluster Kubernetes

Dans le [didacticiel précédent](./container-service-tutorial-kubernetes-prepare-acr.md), un groupe de ressources nommé *myResourceGroup* a été créé. Si vous ne l’avez pas déjà fait, créez ce groupe de ressources maintenant.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pour créer un cluster Kubernetes dans Azure Container Service, utilisez la commande [az acs create](/cli/azure/acs#create). 

L’exemple ci-après permet de créer un cluster nommé *myK8sCluster*, qui inclut un nœud maître Linux et trois nœuds agents Linux. Si elles n’existent pas déjà, des clés SSH sont créées. Pour utiliser un ensemble spécifique de clés dans un emplacement non défini par défaut, utilisez l’option `--ssh-key-value`.

```azurecli-interactive 
az acs create --orchestrator-type=kubernetes --resource-group myResourceGroup --name=myK8SCluster --generate-ssh-keys 
```

Au bout de quelques minutes, la commande se termine et retourne des informations sur le déploiement ACS.

## <a name="install-the-kubectl-cli"></a>Installer l’interface de ligne de commande kubectl

Pour vous connecter au cluster Kubernetes à partir de votre ordinateur client, utilisez [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), le client de ligne de commande Kubernetes. 

Si vous utilisez Azure CloudShell, l’outil `kubectl` est déjà installé. Pour l’installer en local, utilisez la commande [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

Avec Linux ou MacOS, vous devrez peut-être l’exécuter avec sudo. Dans Windows, vérifiez que votre interpréteur de commandes a été exécuté en tant qu’administrateur.

```azurecli-interactive 
az acs kubernetes install-cli 
```

Dans Windows, l’installation par défaut est *c:\program files (x86)\kubectl.exe*. Vous devrez peut-être ajouter ce fichier au chemin de Windows. 

## <a name="connect-with-kubectl"></a>Se connecter avec kubectl

Pour configurer le client `kubectl` afin qu’il se connecte à votre cluster Kubernetes, exécutez la commande [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8SCluster
```

Pour vérifier la connexion à votre cluster, exécutez la commande [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get nodes
```

Sortie :

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
