---
title: "Créer une application web HTML statique dans Azure en&5; minutes | Microsoft Docs"
description: "Découvrez la facilité avec laquelle vous pouvez exécuter des applications web dans App Service en déployant un exemple d’application."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 3a618e4dfed5e941fb0200f4f21a6a45e33d948e
ms.lasthandoff: 03/15/2017


---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Créer une application web HTML statique dans Azure en&5; minutes
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Ce démarrage rapide vous permet de déployer un site HTML+CSS simple dans [Azure App Service](../app-service/app-service-value-prop-what-is.md) en quelques minutes.

Avant de commencer, vérifiez que [l’interface de ligne de commande Azure est installée](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) sur votre ordinateur.

## <a name="create-a-static-html-site-in-azure"></a>Créer un site HTML statique dans Azure
2. Connectez-vous à Azure en exécutant `az login` et en suivant les instructions à l’écran.
   
    ```azurecli
    az login
    ```

3. Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md). C’est ici que vous placez toutes les ressources Azure que vous souhaitez gérer ensemble, telles que l’application web et son serveur principal SQL Database.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    Pour connaître les valeurs que vous pouvez utiliser pour `---location`, utilisez la commande d’interface de ligne de commande Azure `az appservice list-locations`.

3. Créez un [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) appelé « FREE ». 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. Créez une application web avec un nom unique dans `<app_name>`.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Déployez un exemple de site HTML à partir de GitHub.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-html-get-started.git" --branch master --manual-integration 
    ```

5. Pour exécuter votre application en temps réel dans Azure, exécutez la commande qui suit.

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

Félicitations, votre premier site HTML statique s’exécute en temps réel dans Azure App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les [scripts d’interface de ligne de commande des applications web](app-service-cli-samples.md) créés au préalable.

