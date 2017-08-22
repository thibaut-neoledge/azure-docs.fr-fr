---
title: "Didacticiel Azure Container Service - Déployer une application | Microsoft Docs"
description: "Didacticiel Azure Container Service - Déployer une application"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 81a6a3d5364642b2da75faf875d64d2f4a1939d4
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---

# <a name="run-applications-in-kubernetes"></a>Exécuter des applications dans Kubernetes

Dans ce didacticiel (le quatrième d’une série de sept), un exemple d’application est déployé dans un cluster Kubernetes. Les étapes terminées sont les suivantes :

> [!div class="checklist"]
> * Télécharger les fichiers manifeste Kubernetes
> * Exécuter une application dans Kubernetes
> * Test de l'application

Dans les didacticiels suivants, cette application est mise à l’échelle et Operations Management Suite est configuré pour la surveillance du cluster Kubernetes.

Ce didacticiel suppose une compréhension élémentaire des concepts de Kubernetes. Pour en savoir plus, consultez la [documentation Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md). 

Au minimum, ce didacticiel nécessite un cluster Kubernetes.

## <a name="get-manifest-file"></a>Obtenir un fichier manifeste

Pour ce didacticiel, les [objets Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) sont déployés à l’aide d’un manifeste Kubernetes. Un manifeste Kubernetes est un fichier YAML ou JSON contenant des instructions de configuration et de déploiement pour l’objet Kubernetes.

Le fichier manifeste d’application de ce didacticiel est disponible dans le référentiel d’application Azure Vote, qui a été cloné dans un didacticiel précédent. Si vous ne l’avez pas déjà fait, clonez le référentiel à l’aide de la commande suivante : 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Le fichier manifeste se trouve dans le répertoire suivant du référentiel cloné.

```bash
/azure-voting-app-redis/kubernetes-manifests/
```

## <a name="update-manifest-file"></a>Mettre à jour le fichier manifeste

Si vous utilisez Azure Container Registry pour stocker les images conteneur, le fichier manifeste doit être mis à jour avec le nom du serveur de connexion ACR.

Obtenez le nom du serveur de connexion ACR à l’aide de la commande [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

L’exemple de fichier manifeste a été précréé avec le nom de référentiel *microsoft*. Ouvrez le fichier avec un éditeur de texte, puis remplacez la valeur *microsoft* par le nom du serveur de connexion de votre instance ACR.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

## <a name="deploy-application"></a>Déployer l’application

Utilisez la commande [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) pour exécuter l’application. Cette commande analyse le fichier manifeste et crée les objets Kubernetes définis.

```azurecli-interactive
kubectl create -f ./azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yaml
```

Output:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Tester l’application

Un [service Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) est créé et expose l’application à Internet. Ce processus peut prendre plusieurs minutes. 

Pour surveiller la progression, utilisez la commande [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) avec l’argument `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Au début, **EXTERNAL-IP** apparaît comme étant *en attente* pour le service *azure-vote-front*. Une fois que l’adresse IP externe est passée du statut *En attente* à *Adresse IP*, utilisez `CTRL-C` pour arrêter le processus de surveillance kubectl.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Pour afficher l’application, accédez à l’adresse IP externe.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, l’application Azure Vote a été déployée sur un cluster Kubernetes Azure Container Service. Les tâches accomplies sont les suivantes :  

> [!div class="checklist"]
> * Téléchargement des fichiers manifeste Kubernetes
> * Exécution de l’application dans Kubernetes
> * Test de l’application

Passez au didacticiel suivant pour en savoir plus sur la mise à l’échelle d’une application Kubernetes et de l’infrastructure Kubernetes sous-jacente. 

> [!div class="nextstepaction"]
> [Mettre à l’échelle l’application et l’infrastructure Kubernetes](./container-service-tutorial-kubernetes-scale.md)
