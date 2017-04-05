---
title: "Démarrage rapide du cluster Kubernetes dans Azure | Microsoft Docs"
description: "Déploiement et prise en main d’un cluster Kubernetes dans Azure Container Service"
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
ms.date: 03/01/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e4f47341554e2de514c8be2f5c85983d09bbb760
ms.lasthandoff: 04/03/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Prise en main d’un cluster Kubernetes dans Container Service


Les instructions de cet article montrent comment utiliser les commandes Azure CLI 2.0 pour créer un cluster Kubernetes. Vous pouvez utiliser l’outil de ligne de commande `kubectl` pour commencer à travailler avec des conteneurs dans le cluster.

L’image suivante représente l’architecture d’un cluster de service de conteneur comportant un maître et deux agents. Le nœud maître sert l’API REST de Kubernetes. Les nœuds d’agent sont rassemblés dans un groupe à haute disponibilité Azure et exécutent vos conteneurs. Toutes les machines virtuelles figurent dans le même réseau privé virtuel et sont entièrement accessibles les unes par rapport aux autres.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>Composants requis
Cette procédure pas à pas suppose que vous avez installé et configuré [Azure CLI v. 2.0](/cli/azure/install-az-cli2). Vous devez également avoir une clé publique SSH RSA sur `~/.ssh/id_rsa.pub`. Si vous n’en avez pas, consultez les étapes pour [OS X et Linux](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../virtual-machines/linux/ssh-from-windows.md).






## <a name="create-your-kubernetes-cluster"></a>Créer votre cluster Kubernetes

Voici des brèves commandes shell utilisant Azure CLI 2.0 pour créer votre cluster. Pour en savoir plus, consultez la rubrique [Utiliser Azure CLI 2.0 pour créer un cluster Azure Container Service](container-service-create-acs-cluster-cli.md).

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Pour créer votre cluster, vous devez d’abord créer un groupe de ressources dans un emplacement spécifique. Exécutez des commandes similaires à la suivante :

```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Créer un cluster
Une fois le groupe de ressources créé, vous pouvez créer un cluster dans ce groupe :

```console
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> Pendant le déploiement, l’interface CLI télécharge `~/.ssh/id_rsa.pub` sur les machines virtuelles Linux.
>

Une fois cette commande terminée, votre cluster Kubernetes devrait être opérationnel.

### <a name="connect-to-the-cluster"></a>Connexion au cluster

Voici les commandes Azure CLI pour vous connecter au cluster Kubernetes à partir de votre ordinateur client à l’aide de `kubectl`, le client de ligne de commande Kubernetes. Pour plus d’informations, consultez [Connexion à un cluster Azure Container Service](container-service-connect.md).

Si vous n’avez pas encore installé `kubectl`, vous pouvez le faire à l’aide de :

```console
az acs kubernetes install-cli
```

Une fois `kubectl` installé, exécutez la commande suivante pour télécharger la configuration principale du cluster Kubernetes dans le fichier ~/.kube/config :

```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

À ce stade, vous devriez pouvoir accéder à votre cluster à partir de votre machine. Essayez d’exécuter :
```console
kubectl get nodes
```

Puis vérifiez que vous pouvez voir les machines dans votre cluster.

## <a name="create-your-first-kubernetes-service"></a>Création de votre premier service Kubernetes

Une fois cette procédure terminée, vous saurez comment :
 * déployer une application Docker et l’exposer au public ;
 * utiliser `kubectl exec` pour exécuter des commandes dans un conteneur ; 
 * accéder au tableau de bord Kubernetes.

### <a name="start-a-simple-container"></a>Démarrer un conteneur simple
Vous pouvez exécuter un conteneur simple (dans le cas présent le serveur web Nginx) en exécutant :

```console
kubectl run nginx --image nginx
```

Cette commande démarre le conteneur Docker Nginx dans un bloc sur l’un des nœuds.

Pour voir le conteneur en cours d’exécution, exécutez :

```console
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>Rendre le service accessible au public
Pour exposer le service au public, créez un Kubernetes `Service` de type `LoadBalancer` :

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Kubernetes crée alors une règle d’équilibreur de charge Azure avec une adresse IP publique. La propagation de cette modification de l’équilibreur de charge dure quelques minutes. Pour en savoir plus, consultez la rubrique [Équilibrer la charge des conteneurs dans un cluster Kubernetes dans Azure Container Service](container-service-kubernetes-load-balancing.md).

Exécutez la commande suivante et vous verrez le statut du service passer à `pending` pour afficher une adresse IP externe :

```console
watch 'kubectl get svc'
```

  ![Image de la transition du statut En attente vers Adresse IP externe](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Une fois que vous voyez l’adresse IP externe, vous pouvez y accéder dans votre navigateur :

  ![Image de la navigation vers Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Parcourir l’interface utilisateur Kubernetes
Pour afficher l’interface web de Kubernetes, vous pouvez utiliser :

```console
kubectl proxy
```
Cette commande exécute un proxy authentifié simple sur localhost, ce qui vous permet d’afficher l’interface utilisateur web Kubernetes s’exécutant sur [http://localhost:8001/ui](http://localhost:8001/ui). Pour en savoir plus, consultez la rubrique [Utilisation de l’interface utilisateur Web Kubernetes avec Azure Container Service](container-service-kubernetes-ui.md).

![Image du tableau de bord Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sessions à distance à l’intérieur de vos conteneurs
Kubernetes vous permet d’exécuter des commandes dans un conteneur Docker à distance qui est en cours d’exécution dans votre cluster.

```console
# Get the name of your nginx pods
kubectl get pods
```

À l’aide de votre nom de bloc, vous pouvez exécuter une commande à distance sur votre bloc.  Par exemple :

```console
kubectl exec <pod name> date
```

Vous pouvez également obtenir une session entièrement interactive à l’aide des indicateurs `-it` :

```console
kubectl exec <pod name> -it bash
```

![Session à distance à l’intérieur d’un conteneur](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Étapes suivantes

Pour en faire plus avec votre cluster Kubernetes, consultez les ressources suivantes :

* [Camp d’entraînement Kubernetes](https://katacoda.com/embed/kubernetes-bootcamp/1/) : montre comment déployer, mettre à l’échelle, mettre à jour et déboguer des applications.
* [Guide d’utilisateur Kubernetes](http://kubernetes.io/docs/user-guide/) : fournit des informations sur les programmes en cours d’exécution dans un cluster Kubernetes existant.
* [Exemples de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) : fournit des exemples d’exécution d’applications réelles avec Kubernetes.

