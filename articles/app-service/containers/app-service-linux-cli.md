---
title: "Gérer les applications Web App for Containers à l’aide d’Azure CLI 2.0 | Microsoft Docs"
description: "Gérer les applications Web App for Containers à l’aide d’Azure CLI."
keywords: azure app service, application web, cli, linux, oss
services: app-service
documentationCenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.openlocfilehash: 5213bec66095d50ef29cc3bec0a7b6307614dc92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Gérer les applications Web App for Containers à l’aide d’Azure CLI

À l’aide des commandes présentées dans cet article, vous pouvez créer et gérer une application Web App for Containers à l’aide d’Azure CLI 2.0.
Vous pouvez commencer à utiliser la nouvelle version de la CLI de deux manières :

* [Installation d’Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) sur votre ordinateur.
* Utilisation d’[Azure Cloud Shell (version préliminaire)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Créer un plan App Service Linux

Pour créer un plan App Service Linux, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az appservice plan create -n appname -g rgname --islinux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Créer une application web de conteneur Docker personnalisé

Pour créer une application web et la configurer pour utiliser un conteneur Docker personnalisé, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Activer la journalisation du conteneur Docker

Pour activer la journalisation du conteneur Docker, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Changer le conteneur Docker personnalisé pour une application Web App for Containers existante

Pour modifier une application créée précédemment, de l’image Docker actuelle en une nouvelle image, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Utilisation d’images Docker d’un registre privé

Vous pouvez configurer votre application pour utiliser des images d’un registre privé. Vous devez fournir l’URL de votre registre, le nom d’utilisateur et le mot de passe. Ceci peut être effectué à l’aide de la commande suivante :

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Activer les déploiements continus d’images Docker personnalisées

Avec la commande suivante, vous pouvez activer la fonctionnalité CD et obtenir l’URL webhook. Cette URL peut être utilisée pour configurer votre référentiel DockerHub ou Azure Container Registry.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Créer une application Web App for Containers à l’aide de l’une de nos infrastructures runtime intégrées

Pour créer une application PHP 5.6 Web App for Containers, vous pouvez utiliser la commande suivante.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Changer la version de l’infrastructure pour une application Web App for Containers existante

Pour modifier une application créée précédemment, de la version d’infrastructure actuelle en Node.js 6.11, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Configurer des déploiements Git pour votre application web

Pour configurer des déploiements Git pour votre application, vous pouvez utiliser la commande suivante :

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Web Apps for Containers](app-service-linux-intro.md)
* [Installation d’Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
* [Azure Cloud Shell (version préliminaire)](../../cloud-shell/overview.md)
* [Configurer des environnements intermédiaires dans Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Déploiement continu avec Azure Web App for Containers](app-service-linux-ci-cd.md)
