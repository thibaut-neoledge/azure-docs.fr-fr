---
title: "Didacticiel Azure Container Service - Déployer une application | Microsoft Docs"
description: "Didacticiel Azure Container Service - Déployer une application"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, microservices, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 94e1eacd6864a7fcaf753a11e855f6e69aee0f98
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017

---

# <a name="run-applications-in-kubernetes"></a>Exécuter des applications dans Kubernetes

Dans ce didacticiel, un exemple d’application est déployé dans un cluster Kubernetes. Les étapes accomplies sont les suivantes :

> [!div class="checklist"]
> * Présentation des objets Kubernetes
> * Télécharger les fichiers manifeste Kubernetes
> * Exécuter une application dans Kubernetes
> * Tester l’application

Dans les didacticiels suivants, cette application est mise à l’échelle, mise à jour et le cluster Kubernetes est surveillé.

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans des images conteneur, ces images ont été chargées sur Azure Container Registry et un cluster Kubernetes a été créé. Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md). 

Au minimum, ce didacticiel nécessite un cluster Kubernetes.

## <a name="kubernetes-objects"></a>Objets Kubernetes

Quand vous déployez une application en conteneur dans Kubernetes, de nombreux objets Kubernetes différents sont créés. Chaque objet représente l’état souhaité pour le cluster. Par exemple, une application simple peut se composer d’un pod qui correspond à un regroupement de conteneurs étroitement liées, d’un volume persistant qui correspond à un élément de stockage en réseau et d’un déploiement qui gère l’état de l’application. 

Pour plus d’informations sur tous les objets Kubernetes, consultez [Concepts Kubernetes](https://kubernetes.io/docs/concepts/) sur kubernetes.io.

## <a name="get-manifest-files"></a>Obtenir des fichiers manifeste

Pour ce didacticiel, les objets Kubernetes sont déployés à l’aide de manifestes Kubernetes. Un manifeste Kubernetes est un fichier YAML contenant des instructions de configuration d’objet.

Les fichiers manifeste de chaque objet mentionné dans ce didacticiel sont disponibles dans le référentiel d’application Azure Vote, qui a été cloné dans un didacticiel précédent. Si vous ne l'avez pas déjà fait, clonez le référentiel à l’aide de la commande suivante : 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

Les fichiers manifeste se trouvent dans le répertoire suivant du référentiel cloné.

```bash
/azure-voting-app/kubernetes-manifests/
```

## <a name="run-application"></a>Exécuter l’application

### <a name="storage-objects"></a>Objets de stockage

Étant donné que l’application Azure Vote inclut une base de données MySQL, vous allez stocker le fichier de base de données sur un volume qui peut être partagé entre les pods. Dans cette configuration, si le pod MySQL est recréé, le fichier de base de données reste intact.

Le fichier manifeste `storage-resources.yaml` crée un [objet de classe de stockage](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#storageclasses), ce qui détermine comment et où est créé un volume persistant. Plusieurs plug-ins de volume sont disponibles pour Kubernetes. Dans cet exemple, le plug-in du [disque Azure](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#azure-disk) est utilisé. 

Une [revendication de volume persistant](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) est également créée, laquelle configure un élément de stockage (à l’aide d’une classe de stockage) et l’assigne à un pod.

Exécutez la commande suivante pour créer les objets de stockage.

```bash
kubectl create -f storage-resources.yaml
```

Une fois que vous avez terminé, un disque virtuel est créé et attaché au pod Kubernetes obtenu. Le disque virtuel est automatiquement créé dans un compte de stockage résidant dans le même groupe de ressources que le cluster Kubernetes et de la même configuration que l’objet de classe de stockage (Standard_LRS).

### <a name="secure-sensitive-values"></a>Valeurs sensibles sécurisées

Les [secrets Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/) assurent le stockage sécurisé des informations sensibles. À l’aide du fichier `pod-secrets.yaml`, les informations d’identification de la base de données Azure Vote sont stockées dans un secret. 

Exécutez la commande suivante pour créer les objets secrets.

```bash
kubectl create -f pod-secrets.yaml
```

### <a name="create-deployments"></a>Créer des déploiements

Un [déploiement Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) gère l’état des pods Kubernetes. Cette gestion inclut des opérations comme la vérification que le nombre souhaité de réplicas est en cours d’exécution, que les volumes sont montés et que les images conteneur appropriées sont utilisées.

Le fichier manifeste `azure-vote-deployment.yaml` crée un déploiement pour les parties frontend et backend de l’application Azure Vote.

#### <a name="update-image-names"></a>Mettre à jour les noms d’image

Si vous utilisez Azure Container Registry pour stocker les images, leurs noms doivent être ajoutés en préfixe au nom du serveur de connexion ACR.

Obtenez le nom du serveur de connexion ACR à l’aide de la commande [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Mettez à jour les noms d’image conteneur *azure-vote-front* et *azure-vote-back* dans le fichier `azure-vote-deployment.yaml`.

Exemple de nom d’image frontend :

```yaml
containers:
      - name: azure-vote-front
        image: <acrLoginServer>/azure-vote-front:v1
```

Exemple de nom d’image backend :

```yaml
containers:
      - name: azure-vote-back
        image: <acrLoginServer>/azure-vote-front:v1
```

#### <a name="create-deployment-objects"></a>Créer des objets de déploiement

Exécutez [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) pour démarrer l’application Azure Vote.

```bash
kubectl create -f azure-vote-deployment.yaml
```

### <a name="expose-application"></a>Exposer l’application

Un [service Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) définit la manière d’accéder à un pod. Avec l’application Azure Vote, le déploiement backend doit être accessible en interne par son nom. Le déploiement frontend doit être accessible par Internet. Les configurations des services de l’application Azure Vote sont définies dans le fichier manifeste `services.yaml`.

Exécutez la commande suivante pour créer les services.

```bash
kubectl create -f services.yaml
```

## <a name="test-application"></a>Tester l’application

Une fois que tous les objets ont été créés, l’application est accessible via l’adresse IP externe du service azure-vote-front. La création de ce service peut prendre quelques minutes. Pour surveiller le processus de création du service, exécutez la commande suivante. Quand la valeur *EXTERNAL-IP* du service *azure-vote-front* bascule d’un état *en attente* vers une adresse IP, l’application est prête et devient accessible sur l’adresse IP externe.

```bash
kubectl get service -w
```

Sortie :

```bash
NAME               CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
azure-vote-back    10.0.77.30    <none>          3306/TCP       4m
azure-vote-front   10.0.120.96   40.71.227.124   80:31482/TCP   4m
kubernetes         10.0.0.1      <none>          443/TCP        7m
```

Une fois que le service est prêt, exécutez `CTRL-C` pour mettre fin au suivi kubectl.

Accédez à l’adresse IP externe retournée pour voir l’application.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/vote-app-external.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, l’application Azure Vote a été déployée sur un cluster Azure Container Service Kubernetes. Les tâches accomplies sont les suivantes :  

> [!div class="checklist"]
> * Présenter les objets Kubernetes
> * Télécharger les fichiers manifeste Kubernetes
> * Exécuter une application dans Kubernetes
> * Tester l’application

Passez au didacticiel suivant pour en savoir plus sur la mise à l’échelle d’une application Kubernetes et de l’infrastructure Kubernetes sous-jacente. 

> [!div class="nextstepaction"]
> [Mettre à l’échelle l’application et l’infrastructure Kubernetes](./container-service-tutorial-kubernetes-scale.md)
