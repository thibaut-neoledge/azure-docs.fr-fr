---
title: "Créer votre première application web PHP dans Azure en 5 minutes | Microsoft Docs"
description: "Découvrez la facilité avec laquelle vous pouvez exécuter des applications web dans App Service en déployant un exemple d’application PHP."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: afecc8997631bf507c797e56a9e3fc0d1df27614
ms.lasthandoff: 03/21/2017


---
# <a name="create-your-first-php-web-app-in-azure-in-five-minutes"></a>Créer votre première application web PHP dans Azure en 5 minutes
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

Ce démarrage rapide vous permet de déployer votre première application web PHP dans [Azure App Service](../app-service/app-service-value-prop-what-is.md) en quelques minutes.

Avant de commencer, assurez-vous que l’interface de ligne de commande Azure est installée. Pour plus d’informations, consultez le [Guide d’installation de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Connexion à Azure
Connectez-vous à Azure en exécutant `az login` et en suivant les instructions à l’écran.
   
```azurecli
az login
```
   
## <a name="create-a-resource-group"></a>Créer un groupe de ressources   
Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md). C’est ici que vous placez toutes les ressources Azure que vous souhaitez gérer ensemble, telles que l’application web et son serveur principal SQL Database.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Pour connaître les valeurs que vous pouvez utiliser pour `---location`, utilisez la commande d’interface de ligne de commande Azure `az appservice list-locations`.

## <a name="create-an-app-service-plan"></a>Créer un plan App Service
Créez un [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) « Standard » qui exécute un conteneur Linux. 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --is-linux --sku S1
```

## <a name="create-a-web-app"></a>Créer une application web
Créez une application web avec un nom unique dans `<app_name>`.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="configure-the-linux-container"></a>Configurer le conteneur Linux
Configurez le conteneur Linux pour utiliser l’image de PHP 7.0.6 par défaut.

```azurecli
az appservice web config update --php-version 7.0.6 --name <app_name> --resource-group myResourceGroup
```
## <a name="deploy-sample-application"></a>Déployer un exemple d’application
Déployez un exemple d’application PHP à partir de GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-php-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Accéder à l’application web
Pour exécuter votre application en temps réel dans Azure, exécutez la commande qui suit.

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Félicitations, votre première application web PHP s’exécute en temps réel dans Azure App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les [scripts d’interface de ligne de commande des applications web](app-service-cli-samples.md) créés au préalable.

