---
title: "Didacticiel Azure Container Instances - Déployer des applications | Microsoft Docs"
description: "Didacticiel Azure Container Instances - Déployer des applications"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: d8c2056734bc1fdea71543157debd089a9ca743d
ms.contentlocale: fr-fr
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-a-container-to-azure-container-instances"></a>Déployer un conteneur sur Azure Container Instances

Il s’agit de la dernière partie d’un didacticiel en trois parties. Dans les sections précédentes, [nous avons créé une image conteneur](container-instances-tutorial-prepare-app.md) et [nous l’avons transmise vers un Azure Container Registry](container-instances-tutorial-prepare-acr.md). Cette section termine le didacticiel en déployant le conteneur sur Azure Container Instances. Les étapes terminées sont les suivantes :

> [!div class="checklist"]
> * Définition d’un groupe de conteneurs à l’aide d’un modèle Azure Resource Manager
> * Déploiement du groupe de conteneurs à l’aide de l’interface CLI Azure
> * Affichage des journaux de conteneurs

## <a name="deploy-the-container-using-the-azure-cli"></a>Déployer le conteneur à l’aide de l’interface CLI Azure

L’interface CLI Azure permet de déployer un conteneur sur Azure Container Instances en une seule commande. L’image conteneur étant hébergée dans l’Azure Container Registry privé, vous devez inclure les informations d’identification requises pour y accéder. Si nécessaire, vous pouvez les interroger comme indiqué ci-dessous.

Serveur de connexion au Registre de conteneurs (mettez-le à jour avec le nom de votre Registre) :

```azurecli-interactive
az acr show --name <acrName> --query loginServer
```

Mot de passe du Registre de conteneurs :

```azurecli-interactive
az acr credential show --name <acrName> --query "passwords[0].value"
```

Pour déployer votre image conteneur à partir du Registre de conteneurs avec une demande de ressource de 1 noyau de processeur et 1 Go de mémoire, exécutez la commande suivante :

```azurecli-interactive
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

Après quelques secondes, vous recevrez une réponse initiale de la part d’Azure Resource Manager. Pour afficher l’état du déploiement, exécutez :

```azurecli-interactive
az container show --name aci-tutorial-app --resource-group myResourceGroup --query state
```

Nous pouvons continuer à exécuter cette commande jusqu'à ce que l’état passe de *en attente* à *en cours d’exécution*. Nous pouvons ensuite continuer.

## <a name="view-the-application-and-container-logs"></a>Afficher les journaux d’applications et de conteneurs

Une fois le déploiement réussi, ouvrez votre navigateur à l’adresse IP affichée dans la sortie de la commande suivante :

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

```json
"13.88.176.27"
```

![Application Hello world dans le navigateur][aci-app-browser]

Vous pouvez également afficher la sortie du journal du conteneur :

```azurecli-interactive
az container logs --name aci-tutorial-app -g myResourceGroup
```

Sortie :

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez terminé le processus de déploiement de vos conteneurs sur Azure Container Instances. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement du conteneur à partir d’Azure Container Registry à l’aide de l’interface CLI Azure
> * Affichage de l’application dans le navigateur
> * Affichage des journaux de conteneurs

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

