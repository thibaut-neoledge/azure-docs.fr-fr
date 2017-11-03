---
title: "Exécuter une image Docker Hub personnalisée dans Web App pour conteneurs | Microsoft Docs"
description: "Comment utiliser une image Docker personnalisée pour Web App pour conteneurs."
keywords: azure app service, application web, linux, docker, conteneur
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: eadc0f7eb20b9e8d1cacc79b2907559e2b2535a2
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="run-a-custom-docker-hub-image-in-web-app-for-containers"></a>Exécuter une image Docker Hub personnalisée dans Web App pour conteneurs

App Service fournit des piles d’applications prédéfinies sur Linux avec la prise en charge de versions spécifiques, comme PHP 7.0 et Node.js 4.5. Vous pouvez également utiliser une image Docker personnalisée pour déployer votre application web sur une pile d’applications qui n’est pas encore définie dans Azure. Ce guide de démarrage rapide vous montre comment créer une application web et y déployer une image Docker basée sur Python. Vous allez créer l’application web à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Créer une application Web App pour conteneurs

Créez une [application web](../app-service-web-overview.md) dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp#create). N’oubliez pas de remplacer `<app name>` par un nom d’application unique.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name elnably/dockerimagetest
```

Dans la commande précédente, `--deployment-container-image-name` pointe vers l’image publique Docker Hub [https://hub.docker.com/r/elnably/dockerimagetest/](https://hub.docker.com/r/elnably/dockerimagetest/). Vous pouvez examiner son contenu à l’adresse [https://github.com/rachelappel/docker-image](https://github.com/rachelappel/docker-image).

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’URL suivante à l’aide de votre navigateur web.

```bash
http://<app_name>.azurewebsites.net
```

![Exemple d’application s’exécutant dans Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**Félicitations !** Vous avez déployé une image Docker personnalisée dans Web App for Containers.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web Docker Python et PostgreSQL dans Azure](tutorial-docker-python-postgresql-app.md)
