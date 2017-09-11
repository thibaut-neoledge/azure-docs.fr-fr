---
title: "Didacticiel Azure Container Service - Préparer l’application | Microsoft Docs"
description: "Didacticiel Azure Container Service - Préparer l’application"
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
ms.date: 07/25/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: f02ee61ef1cd3b3dfaa051cfabe52866e3e7e838
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Créer des images de conteneur à utiliser avec Azure Container Service

Dans ce didacticiel (le premier d’une série de sept), vous allez préparer une application à plusieurs conteneurs à son utilisation dans Kubernetes. Les étapes effectuées sont les suivantes :  

> [!div class="checklist"]
> * Clonage de la source de l’application à partir de GitHub  
> * Création d’une image conteneur à partir de la source de l’application
> * Test de l’application dans un environnement Docker local

Une fois ces étapes effectuées, l’application suivante est accessible dans votre environnement de développement local.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Dans les didacticiels suivants, l’image conteneur est chargée dans Azure Container Registry, puis exécutée dans le cluster Kubernetes hébergé dans Azure.

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel suppose une compréhension élémentaire des concepts Docker principaux tels que les conteneurs, les images de conteneur et les commandes Docker de base. Si besoin, consultez [Bien démarrer avec Docker]( https://docs.docker.com/get-started/) pour apprendre les principes de base des conteneurs. 

Pour terminer ce didacticiel, il vous faut un environnement de développement Docker. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="get-application-code"></a>Obtenir le code d’application

L’exemple d’application utilisé dans ce didacticiel est une application de votes de base. L’application est constituée d’un composant web frontal et d’une instance Redis principale. Le composant web est empaqueté dans une image conteneur personnalisée. L’instance Redis utilise une image non modifiée de Docker Hub.  

Utilisez git pour télécharger une copie de l’application dans votre environnement de développement.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Dans le répertoire cloné se trouvent le code source de l’application, un fichier Docker Compose précréé et un fichier manifeste Kubernetes. Ces fichiers sont utilisés pour créer des ressources tout au long de ce didacticiel. 

## <a name="create-container-images"></a>Créer des images de conteneur

[Docker Compose](https://docs.docker.com/compose/) peut être utilisé pour automatiser la génération à partir des images conteneur, ainsi que le déploiement des applications à plusieurs conteneurs.

Exécutez le fichier docker-compose.yml pour créer l’image conteneur, télécharger l’image Redis, puis démarrez l’application.

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yml up -d
```

Une fois terminé, utilisez la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/) pour afficher les images créées.

```bash
docker images
```

Notez que les trois images ont été téléchargées ou créées. L’image *azure-vote-front* contient l’application. Cette image est dérivée de l’image *nginx-flask*. L’image Redis a été téléchargée à partir de Docker Hub.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Exécutez la commande [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) pour voir les conteneurs en cours d’exécution.

```bash
docker ps
```

Output:

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Tester l’application localement

Accédez à http://localhost:8080 pour afficher le conteneur en cours d’exécution.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Maintenant que la fonctionnalité de l’application a été validée, les conteneurs en cours d’exécution peuvent être arrêtés et supprimés. Ne supprimez pas les images de conteneur. Dans le didacticiel suivant, l’image *azure-vote-front* est chargée dans une instance Azure Container Registry.

Exécutez le code suivant pour arrêter les conteneurs en cours d’exécution.

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yml stop
```

Supprimez les conteneurs arrêtés avec la commande suivante.

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yml rm
```

Une fois terminé, vous disposez de deux images conteneur contenant l’application Azure Vote.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, une application a été testée et les images de conteneur créées pour l’application. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Le clonage de la source de l’application à partir de GitHub  
> * La création d’une image conteneur à partir de la source de l’application
> * Le test de l’application dans un environnement Docker local

Passez au didacticiel suivant pour en savoir plus sur le stockage d’images de conteneur dans Azure Container Registry.

> [!div class="nextstepaction"]
> [Envoyer des images à Azure Container Registry](./container-service-tutorial-kubernetes-prepare-acr.md)
