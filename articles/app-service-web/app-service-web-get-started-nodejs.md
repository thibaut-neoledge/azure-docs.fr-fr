---
title: "Créer votre première application web Node.js dans Azure en&5; minutes | Microsoft Docs"
description: "Découvrez la facilité avec laquelle vous pouvez exécuter des applications web dans App Service en déployant un exemple d’application Node.js."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 412cc786-5bf3-4e1b-b696-6a08cf46501e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 746f697076566ce3edd970336b005e53dc4d2d39
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-nodejs-web-app-in-azure-in-five-minutes"></a>Créer votre première application web Node.js dans Azure en&5; minutes
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Ce démarrage rapide vous permet de déployer votre première application web Node.js dans [Azure App Service](../app-service/app-service-value-prop-what-is.md) en quelques minutes.

Avant de commencer, vérifiez que [l’interface de ligne de commande Azure est installée](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) sur votre ordinateur.

# <a name="create-a-nodejs-web-app"></a>Créer une application web Node.js
2. Connectez-vous à Azure en exécutant `az login` et en suivant les instructions à l’écran.
   
    ```azurecli
    az login
    ```
   
3. Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md). C’est ici que vous placez toutes les ressources Azure que vous souhaitez gérer ensemble, telles que l’application web et son serveur principal SQL Database.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    Pour connaître les valeurs que vous pouvez utiliser pour `---location`, utilisez la commande d’interface de ligne de commande Azure `az appservice list-locations`.

3. Créez un [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) appelé « Standard ». Le niveau Standard est requis pour exécuter des conteneurs Linux.

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku S1 --is-linux 
    ```

4. Créez une application web avec un nom unique dans `<app_name>`.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Configurez le conteneur Linux pour utiliser l’image de Node.js 6.9.3 par défaut.

    ```azurecli
    az appservice web config update --node-version 6.9.3 --name <app_name> --resource-group myResourceGroup
    ```

4. Déployez un exemple d’application Node.js à partir de GitHub.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git" --branch master --manual-integration 
    ```

5. Pour exécuter votre application en temps réel dans Azure, exécutez la commande qui suit.

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

Félicitations, votre première application web Node.js s’exécute en temps réel dans Azure App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les [scripts d’interface de ligne de commande des applications web](app-service-cli-samples.md) créés au préalable.

