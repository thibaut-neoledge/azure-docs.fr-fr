---
title: "Didacticiel Azure Container Service - Mettre à jour une application | Microsoft Docs"
description: "Didacticiel Azure Container Service - Mettre à jour une application"
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
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 72cdbfe2fe65e5152ca748cbb3989e3ef980ee50
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---

# <a name="update-an-application-in-kubernetes"></a>Mettre à jour une application dans Kubernetes

Après avoir déployé une application dans Kubernetes, vous pouvez la mettre à jour en spécifiant une nouvelle image conteneur ou une nouvelle version de l’image. Lorsque vous mettez à jour une application, le déploiement de cette mise à jour se fait par étapes, afin que seulement une partie du déploiement soit mis à jour simultanément. Cette mise à jour par étapes permet à l’application de poursuivre son exécution pendant la mise à jour, tout en offrant un dispositif de restauration en cas d’échec du déploiement. 

Dans ce didacticiel (le sixième d’une série de sept), l’exemple de l’application Azure Vote est mis à jour. Les tâches que vous effectuez sont les suivantes :

> [!div class="checklist"]
> * Mise à jour du code de l’application frontale
> * Création d’une image conteneur mise à jour
> * Envoi (push) de l’image conteneur à Azure Container Registry
> * Déploiement de l’image conteneur mise à jour

Dans les didacticiels suivants, Operations Management Suite est configuré pour la surveillance du cluster Kubernetes.

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. L’application a ensuite été exécutée sur le cluster Kubernetes. 

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Mettre à jour l’application

Pour suivre les étapes de ce didacticiel, vous devez avoir cloné une copie de l’application Azure Vote. Le cas échéant, créez cette copie clonée à l’aide de la commande suivante :

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Ouvrez le fichier `config_file.cfg` avec un éditeur de code ou de texte. Vous trouvez ce fichier sous le répertoire suivant du dépôt cloné.

```bash
 /azure-voting-app-redis/azure-vote/azure-vote/config_file.cfg
```

Modifiez les valeurs de `VOTE1VALUE` et `VOTE2VALUE`, puis enregistrez le fichier.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Utilisez [docker-compose](https://docs.docker.com/compose/) pour recréer l’image de l’application frontale et exécuter l’application mise à jour.

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yml up --build -d
```

## <a name="test-application-locally"></a>Tester l’application localement

Accédez à `http://localhost:8080` pour voir l’application mise à jour.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Marquer et envoyer des images

Marquer l’image *azure-vote-front* à l’aide du loginServer du registre de conteneurs.

Si vous utilisez Azure Container Registry, récupérez le nom du serveur de connexion au moyen de la commande [az acr list](/cli/azure/acr#list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilisez [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) pour ajouter une balise à l’image. Remplacez `<acrLoginServer>` par le nom de votre serveur de connexion Azure Container Registry ou par votre nom d’hôte de registre public.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Utilisez [docker push](https://docs.docker.com/engine/reference/commandline/push/) pour charger l’image dans le Registre. Remplacez `<acrLoginServer>` par le nom de votre serveur de connexion Azure Container Registry ou par votre nom d’hôte de registre public.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Déployer l’application mise à jour

Pour garantir une disponibilité maximale, vous devez exécuter plusieurs instances du pod d’application. Vérifiez cette configuration avec la commande [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get pod
```

Output:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Si vous n’avez qu’un POD exécutant l’image azure-vote-front, mettez à l’échelle le déploiement d’*azure-vote-front*.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

Pour mettre à jour l’application, utilisez la commande [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set). Mettez à jour `<acrLoginServer>` avec le nom du serveur de connexion ou le nom d’hôte de votre registre de conteneurs.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Pour surveiller le déploiement, utilisez la commande [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get). À mesure que l’application mise à jour est déployée, vos pods sont terminés et recréés avec la nouvelle image conteneur.

```azurecli-interactive
kubectl get pod
```

Output:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Tester l’application mise à jour

Obtenez l’adresse IP externe du service *azure-vote-front*.

```azurecli-interactive
kubectl get service azure-vote-front
```

Accédez à l’adresse IP pour voir l’application mise à jour.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons mis à jour une application et nous avons déployé cette mise à jour sur un cluster Kubernetes. Les tâches suivantes ont été accomplies :

> [!div class="checklist"]
> * Mise à jour du code de l’application frontale
> * Création d’une image conteneur mise à jour
> * Envoi (push) de l’image conteneur à Azure Container Registry
> * Déploiement de l’application mise à jour

Passez au didacticiel suivant pour en savoir plus sur la surveillance de Kubernetes avec Operations Management Suite.

> [!div class="nextstepaction"]
> [Surveiller Kubernetes avec OMS (Operations Management Suite)](./container-service-tutorial-kubernetes-monitor.md)
