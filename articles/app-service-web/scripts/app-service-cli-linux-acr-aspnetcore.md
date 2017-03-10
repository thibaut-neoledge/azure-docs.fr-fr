---
title: "Exemple de script Azure CLI - Créer une application ASP.NET Core dans un conteneur Docker à partir d’Azure Container Registry | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une application ASP.NET Core dans un conteneur Docker à partir d’Azure Container Registry"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/23/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 862aad510a9d6e8ae15324457d8b15378b2b2776
ms.openlocfilehash: 61d0f71d13429cffa3b7e01cf2fb18eb358a1817
ms.lasthandoff: 02/27/2017

---

# <a name="create-an-aspnet-core-web-app-in-a-docker-container-from-azure-container-registry"></a>Créer une application ASP.NET Core dans un conteneur Docker à partir d’Azure Container Registry

Dans ce scénario, vous allez apprendre à créer un groupe de ressources, un plan App Service Linux et une application web, et à déployer une application ASP.NET Core à l’aide d’un conteneur Docker à partir d’Azure Container Registry.

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de la commande `az login`.

## <a name="create-app-sample"></a>Exemple de création d’application

[!code-azurecli[principal](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh?highlight=6-9 "Linux Azure Container Registry")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une application web et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. Cela équivaut à une batterie de serveurs pour votre application web Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Crée une application web Azure dans le plan App Service. |
| [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) | Définit le conteneur Docker pour l’application web Azure. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).
