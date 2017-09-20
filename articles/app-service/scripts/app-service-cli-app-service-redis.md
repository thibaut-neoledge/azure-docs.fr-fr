---
title: "Exemple de script Azure CLI - Connecter une application web à un cache redis | Microsoft Docs"
description: "Exemple de script Azure CLI - Connecter une application web à un cache redis"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: c1277a04e3a63c09b2ed7f9304bfc482ab9189a8
ms.contentlocale: fr-fr
ms.lasthandoff: 09/09/2017

---

# <a name="connect-a-web-app-to-a-redis-cache"></a>Connecter une application web à un cache redis

Dans ce scénario, vous allez apprendre à créer un cache redis et une application web Azure. Ensuite, vous allez lier le cache redis à l’application web par le biais des paramètres de l’application.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Cache Redis Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une application web, un cache redis et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Crée un plan App Service. Cela équivaut à une batterie de serveurs pour votre application web Azure. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Crée une application web Azure. |
| [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_create) | Crée une nouvelle instance de Cache Redis. Il s’agit de l’emplacement où les données seront stockées. |
| [az redis list-keys](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_list_keys) | Répertorie les touches d’accès rapide pour l’instance de cache redis. |
| [az webapp config appsettings set](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Crée ou met à jour un paramètre d’application pour une application web Azure. Les paramètres d’application sont exposés en tant que variables d’environnement pour votre application. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).

