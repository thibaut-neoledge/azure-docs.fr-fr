---
title: "Didacticiel Azure Container Instances - Préparer votre application | Azure Docs"
description: "Préparer une application pour le déploiement vers Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 07ad1a6edbcb4d6160b37b4923586e23058f3c04
ms.contentlocale: fr-fr
ms.lasthandoff: 08/02/2017

---

# <a name="create-container-for-deployment-to-azure-container-instances"></a>Créer un conteneur à déployer dans Azure Container Instances

Azure Container Instances permet de déployer des conteneurs Docker sur l’infrastructure Azure sans configurer de machines virtuelles ni adopter de service de niveau supérieur. Dans ce didacticiel, vous allez voir comment générer une application web basique dans Node.js, et comment la placer comme package dans un conteneur pouvant être exécuté avec Azure Container Instances. Nous aborderons :

> [!div class="checklist"]
> * Le clonage de la source de l’application à partir de GitHub  
> * la création des images de conteneur à partir de la source de l’application
> * Le test des images dans un environnement Docker local

Dans les didacticiels suivants, vous aller voir comment charger votre image dans un registre Azure Container Registry, et comment la déployer dans Azure Container Instances.

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel suppose une compréhension élémentaire des concepts Docker principaux tels que les conteneurs, les images de conteneur et les commandes Docker de base. Si besoin, consultez [Bien démarrer avec Docker]( https://docs.docker.com/get-started/) pour apprendre les principes de base des conteneurs. 

Pour terminer ce didacticiel, il vous faut un environnement de développement Docker. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="get-application-code"></a>Obtenir le code de l’application

L’exemple dans ce didacticiel inclut une application web basique générée dans [Node.js](http://nodejs.org). L’application se présente sous forme de page HTML statique et ressemble à ceci :

![Application du didacticiel affichée dans le navigateur][aci-tutorial-app]

Utilisez Git pour télécharger l’exemple :

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Générer l’image de conteneur

Le fichier Dockerfile fourni dans le référentiel de l’exemple montre comment le conteneur est généré. La génération du conteneur commence par une [image officielle Node.js][dockerhub-nodeimage] sur [Linux Alpine](https://alpinelinux.org/), une petite distribution parfaitement adaptée aux conteneurs. Les fichiers d’application sont ensuite copiés dans le conteneur, les dépendances sont installées à l’aide de Node Package Manager, et l’application démarre.

```
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Utilisez la commande `docker build` pour créer l’image de conteneur, et balisez-la *aci-tutorial-app* :

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Utilisez `docker images` pour voir l’image ainsi générée :

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>Exécutez localement le conteneur

Avant d’essayer de déployer le conteneur dans Azure Container Instances, exécutez-le localement pour vous assurer qu’il fonctionne. Le commutateur `-d` permet l’exécution du conteneur en arrière-plan, tandis que `-p` vous permet de mapper un port arbitraire sur votre ordinateur vers le port 80 du conteneur.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Allez à l’adresse http://localhost:8080 dans votre navigateur pour confirmer l’exécution du conteneur.

![Exécution locale de l’application dans le navigateur][aci-tutorial-app-local]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une image de conteneur qui peut être déployée dans Azure Container Instances. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * le clonage de la source de l’application à partir de GitHub  
> * La création des images de conteneur à partir de la source de l’application
> * Le test de l’exécution locale du conteneur

Passez au didacticiel suivant pour en savoir plus sur le stockage d’images de conteneur dans un registre Azure Container Registry.

> [!div class="nextstepaction"]
> [Envoyer des images à Azure Container Registry](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://hub.docker.com/r/library/node/tags/8.2.0-alpine/

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png
