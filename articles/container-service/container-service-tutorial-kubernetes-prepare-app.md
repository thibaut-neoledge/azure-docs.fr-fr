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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 49d727de69c700af0ae6402ad54b5880010ebb3b
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Créer des images de conteneur à utiliser avec Azure Container Service

Dans ce didacticiel, une application est préparée pour Kubernetes. Les étapes terminées sont les suivantes :  

> [!div class="checklist"]
> * le clonage de la source de l’application à partir de GitHub  
> * la création des images de conteneur à partir de la source de l’application
> * le test des images dans un environnement Docker local

Dans les didacticiels suivants, ces images de conteneur sont téléchargées dans Azure Container Registry, puis exécutées dans le cluster Kubernetes hébergés Azure.

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel suppose une compréhension élémentaire des concepts Docker principaux tels que les conteneurs, les images de conteneur et les commandes Docker de base. Si besoin, consultez [Bien démarrer avec Docker]( https://docs.docker.com/get-started/) pour apprendre les principes de base des conteneurs. 

Pour terminer ce didacticiel, il vous faut un environnement de développement Docker. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="get-application-code"></a>Obtenir le code d’application

L’exemple d’application utilisé dans ce didacticiel est une application de votes de base. L’application se compose d’un composant Web frontal et d’une base de données principale. 

Utilisez git pour télécharger une copie de l’application dans votre environnement de développement.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

Dans le répertoire de l’application, vous trouverez les fichiers manifestes Dockerfiles et Kubernetes créés au préalable. Ces fichiers sont utilisés pour créer des ressources tout au long de ce didacticiel. 

## <a name="create-container-images"></a>Créer des images de conteneur

Pour créer une image de conteneur pour l’application frontale, utilisez la commande [docker build](https://docs.docker.com/engine/reference/commandline/build/).

```bash
docker build ./azure-voting-app/azure-vote -t azure-vote-front
```

Répétez la commande, cette fois pour l’image de conteneur principale.

```bash
docker build ./azure-voting-app/azure-vote-mysql -t azure-vote-back
```

Une fois terminé, utilisez la commande `docker images` pour afficher les images créées. 

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

## <a name="test-application"></a>Tester l’application

Maintenant que les deux images de conteneur ont été créées, testez ces images dans votre environnement de développement local. 

Créez d’abord un réseau Docker. Ce réseau est utilisé pour la communication entre les conteneurs.  

```bash
docker network create azure-vote
```

Exécutez une instance de l’image de conteneur principale à l’aide de la commande `docker run`.

Dans cet exemple, le fichier de base de données mysql est stocké dans le conteneur. Une fois cette application déplacée vers les clusters Kubernete, un volume de données externe est utilisé pour stocker le fichier de base de données. En outre, les variables d’environnement sont utilisées pour définir les informations d’identification MySQL.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

Exécutez une instance de l’image de conteneur frontale.

Les variables d’environnement sont utilisées pour configurer les informations de connexion de la base de données.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front
```

Lorsque vous avez terminé, exécutez `docker ps` pour afficher les conteneurs en cours d’exécution.  

```bash
docker ps
```

Output:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
3aa02e8ae965        azure-vote-front     "/usr/bin/supervisord"   59 seconds ago      Up 57 seconds       443/tcp, 0.0.0.0:8080->80/tcp   flaskmysqlvote_azure-vote-front_1
5ae60b3ba181        azure-vote-backend   "docker-entrypoint..."   59 seconds ago      Up 58 seconds       0.0.0.0:3306->3306/tcp          azure-vote-back
```

Accédez à `http://localhost:8080` pour afficher l’application en cours d’exécution. L’initialisation de l’application prend quelques secondes. En cas d’erreur, réessayez.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/vote-app.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Maintenant que la fonctionnalité de l’application a été validée, les conteneurs en cours d’exécution peuvent être arrêtés et supprimés. Ne supprimez pas les images de conteneur. Ces images sont téléchargées vers une instance Azure Container Registry dans le didacticiel suivant.

Arrêtez et supprimez le conteneur frontal avec la commande [docker rm](https://docs.docker.com/engine/reference/commandline/rm/). 

```bash
docker rm -f azure-vote-front
```

Arrêtez et supprimez le conteneur principal avec la commande [docker rm](https://docs.docker.com/engine/reference/commandline/rm/). 

```bash
docker rm -f azure-vote-back
```

Supprimez le réseau avec la commande [docker network rm](https://docs.docker.com/engine/reference/commandline/network_rm/).

```bash
docker network rm azure-vote
```

Une fois terminé, deux images de conteneur composent votre application de votes Azure.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, une application a été testée et les images de conteneur créées pour l’application. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * le clonage de la source de l’application à partir de GitHub  
> * la création des images de conteneur à partir de la source de l’application
> * le test des images dans un environnement Docker local

Passez au didacticiel suivant pour en savoir plus sur le stockage d’images de conteneur dans Azure Container Registry.

> [!div class="nextstepaction"]
> [Envoyer des images à Azure Container Registry](./container-service-tutorial-kubernetes-prepare-acr.md)
