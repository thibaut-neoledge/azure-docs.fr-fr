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
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Moteur de Microsoft Azure Container Service - Procédure pas à pas Kubernetes

## <a name="prerequisites"></a>Composants requis
Cette procédure pas à pas suppose que [l’outil de ligne de commande azure-cli](https://github.com/azure/azure-cli#installation) est installé et que vous avez créé la [clé publique SSH](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) à l’emplacement `~/.ssh/id_rsa.pub`.

## <a name="overview"></a>Vue d’ensemble

Les instructions ci-dessous permettent de créer un cluster Kubernetes avec un nœud maître et deux nœuds Worker.
Le nœud maître sert l’API REST de Kubernetes.  Les nœuds Worker sont rassemblés dans un groupe à haute disponibilité Azure et exécutent vos conteneurs. Toutes les machines virtuelles figurent dans le même réseau privé virtuel et sont entièrement accessibles les unes par rapport aux autres.

> [!NOTE]
> La prise en charge de Kubernetes dans l’Azure Container Service est actuellement en version préliminaire.
>

L’image suivante représente l’architecture d’un cluster de service de conteneur comportant un maître et deux agents :

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>Création de votre cluster Kubernetes

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Pour créer votre cluster, vous devez d’abord créer un groupe de ressources dans un emplacement spécifique. Pour cela :
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Créer un cluster
Une fois le groupe de ressources créé, vous pouvez créer un cluster dans ce groupe à l’aide de la commande suivante :
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> L’outil azure-cli charge `~/.ssh/id_rsa.pub` sur les machines virtuelles Linux.
>

Une fois cette commande terminée, votre cluster Kubernetes devrait être opérationnel.

### <a name="configure-kubectl"></a>Configurer kubectl
`kubectl` est le client de ligne de commande Kubernetes.  Si vous ne l’avez pas encore fait, vous pouvez l’installer avec :

```console
az acs kubernetes install-cli
```

Une fois le client `kubectl` installé, exécutez la commande ci-dessous pour télécharger la configuration principale du cluster kubernetes dans le fichier ~/.kube/config.
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

À ce stade, vous devriez pouvoir accéder à votre cluster à partir de votre machine. Essayez d’exécuter :
```console
kubectl get nodes
```

Puis vérifiez que vous pouvez voir les machines de votre cluster.

## <a name="create-your-first-kubernetes-service"></a>Créer votre premier service Kubernetes

Une fois cette procédure terminée, vous saurez comment :
 * déployer une application Docker et l’exposer au public ;
 * utiliser `kubectl exec` pour exécuter des commandes dans un conteneur ; 
 * accéder au tableau de bord Kubernetes.

### <a name="start-a-simple-container"></a>Démarrer un conteneur simple
Vous pouvez exécuter un conteneur simple (le serveur web `nginx` dans le cas présent) en exécutant :

```console
kubectl run nginx --image nginx
```

Cette commande démarre le conteneur Docker nginx dans un bloc sur l’un des nœuds.

Vous pouvez exécuter
```console
kubectl get pods
```

pour voir le conteneur en cours d’exécution.

### <a name="expose-the-service-to-the-world"></a>Rendre le service accessible au public
Pour rendre le service accessible au public.  Vous créez un Kubernetes `Service` de type `LoadBalancer` :

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Kubernetes créera ainsi un équilibrage de charge Azure avec une adresse IP publique. La propagation de cette modification à l’équilibrage de charge dure 2 à 3 minutes.

Pour voir le service passer du statut « en attente » à « adresse IP externe » :
```console
watch 'kubectl get svc'
```

  ![Image de la transition du statut « en attente » vers « adresse IP externe »](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Une fois que vous voyez l’adresse IP externe, vous pouvez y accéder dans votre navigateur :

  ![Image de la navigation vers nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Parcourir l’interface utilisateur Kubernetes
Pour afficher l’interface web de Kubernetes, vous pouvez utiliser :

```console
kubectl proxy
```
Cette commande exécute un proxy authentifié simple sur localhost, ce qui vous permet d’afficher [l’interface utilisateur kubernetes](http://localhost:8001/ui)

![Image du tableau de bord Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sessions à distance à l’intérieur de vos conteneurs
Kubernetes vous permet d’exécuter des commandes dans un conteneur Docker à distance qui est en cours d’exécution dans votre cluster.

```console
# Get the name of your nginx pod
kubectl get pods
```

À l’aide de votre nom de bloc, vous pouvez exécuter une commande à distance sur votre bloc.  Par exemple :
```console
kubectl exec nginx-701339712-retbj date
```

Vous pouvez également obtenir une session entièrement interactive à l’aide des indicateurs `-it` :

```console
kubectl exec nginx-701339712-retbj -it bash
```

![Image de curl sur l’adresse IP du bloc](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>Détails
### <a name="installing-the-kubectl-configuration-file"></a>Installation du fichier de configuration kubectl
Lorsque vous avez exécuté `az acs kubernetes get-credentials`, le fichier de configuration kube pour l’accès à distance a été stocké sous le répertoire de base ~/.kube/config.

Si vous devez le télécharger directement, vous pouvez utiliser `ssh` sur Linux ou OS X, ou `Putty` sur Windows :

#### <a name="windows"></a>Windows
Pour utiliser la commande pscp de [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Assurez-vous que votre certificat est exposé via [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant) :
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X ou Linux :
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>En savoir plus

### <a name="azure-container-service"></a>Azure Container Service

1. [Documentation Azure Container Service](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Moteur open source d’Azure Container Service](https://github.com/azure/acs-engine)

### <a name="kubernetes-community-documentation"></a>Documentation de la communauté Kubernetes

1. [Camp d’entraînement Kubernetes](https://katacoda.com/embed/kubernetes-bootcamp/1/) : montre comment déployer, mettre à l’échelle, mettre à jour et déboguer des applications.
2. [Guide d’utilisateur Kubernetes](http://kubernetes.io/docs/user-guide/) : fournit des informations sur les programmes en cours d’exécution dans un cluster Kubernetes existant.
3. [Exemples de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) : fournit des exemples d’exécution d’applications réelles avec Kubernetes.



<!--HONumber=Jan17_HO4-->


