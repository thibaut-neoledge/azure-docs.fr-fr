---
title: "Création d’une application Ruby dans Web Apps sur Linux | Microsoft Docs"
description: "Apprenez à créer des applications Ruby avec Web Apps sur Linux."
keywords: azure app service, linux, oss, ruby
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: wesmc;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 17f3f1a2122c508501134a0c43ab6abce412fb44
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---
# <a name="create-a-ruby-app-with-web-apps-on-linux"></a>Création d’une application Ruby dans Web Apps sur Linux 

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

[Azure Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce guide de démarrage rapide vous montre comment créer une application Ruby on Rails de base puis la déployer dans Azure Web App sur Linux.

![Hello-world](./media/app-service-linux-ruby-get-started/hello-world-updated.png)

## <a name="prerequisites"></a>Composants requis

* [Ruby 2.4.1 ou une version ultérieure](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller).
* [Git](https://git-scm.com/downloads).
* Un [abonnement Azure actif](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Téléchargez l’exemple

Dans une fenêtre de terminal, exécutez la commande ci-après pour cloner le référentiel de l’exemple d’application sur votre ordinateur local :

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="run-the-application-locally"></a>Exécution locale de l'application

Exécutez le serveur Rails afin que l’application fonctionne. Passez au répertoire *hello-world* et démarrez le serveur avec la commande `rails server`.

```bash
cd hello-world\bin
rails server
```
    
À l’aide de votre navigateur web, accédez à `http://localhost:3000` pour tester l’application en local.    

![Hello-world](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="modify-app-to-display-welcome-message"></a>Modification de l’application pour afficher un message d’accueil

Modifiez l’application afin qu’elle affiche un message d’accueil. Modifiez le contrôleur de l’application de sorte pour qu’il renvoie le message au format HTML au navigateur. 

Ouvrez *~/workspace/hello-world/app/controllers/application_controller.rb* pour le modifier. Modifiez la classe `ApplicationController` pour qu’elle ressemble à l’exemple de code suivant :

  ```ruby
  class ApplicationController > ActionController :: base
    protect_from_forgery with: :exception 
    def hello
      render html: "Hello, world from Azure Web App on Linux!"
    end
  end
  ```

Votre application est désormais configurée. À l’aide de votre navigateur web, accédez à `http://localhost:3000` pour vérifier la page d’accueil racine.

![Hello World configurée](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ruby-web-app-on-azure"></a>Créer une application web Ruby sur Azure

Utilisez la commande [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) pour créer un plan de service d’application pour votre application web. 
 
```azurecli-interactive
  az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --is-linux
```

Ensuite, utilisez la commande [az webapp créer](https://docs.microsoft.com/cli/azure/webapp) pour créer l’application web qui utilise le plan de service nouvellement créé. Notez que le runtime est défini sur `ruby|2.3`. N’oubliez pas de remplacer `<app name>` par un nom d’application unique.

```azurecli-interactive
  az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "ruby|2.3" --deployment-local-git
```

Une fois l’application web créée, une page **Vue d’ensemble** est disponible à l’affichage. Naviguez vers cet emplacement. La page de démarrage suivante s’affiche :

![Page de démarrage](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>Déployer votre application

Utilisez Git pour déployer l’application Ruby sur Azure. Un déploiement Git est déjà configuré sur l’application web. Vous pouvez récupérer l’URL de déploiement en utilisant une commande [az webapp deployment](https://docs.microsoft.com/cli/azure/webapp/deployment).  

```bash
az webapp deployment source show --name <app name> --resource-group myResourceGroup
```

Notez que l’URL Git a la forme suivante, selon le nom de votre application web :

```bash
https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
```

[!INCLUDE [Clean-up section](../../includes/configure-deployment-user-no-h.md)]

Exécutez les commandes suivantes pour déployer l’application locale sur votre site web Azure :

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Vérifiez que les opérations de déploiement à distance réussissent. Les commandes produisent une sortie semblable au texte suivant :

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

Une fois le déploiement terminé, redémarrez votre application web pour que le déploiement prenne effet en utilisant la commande [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#restart), comme présenté ici :

```azurecli-interactive 
az webapp restart --name <app name> --resource-group myResourceGroup
```

Accédez à votre site et vérifiez les résultats.

```bash
http://<your web app name>.azurewebsites.net
```
![application web mise à jour](./media/app-service-linux-ruby-get-started/hello-world-updated.png)

> [!NOTE]
> Si vous tentez de parcourir les résultats du site pendant que l’application redémarre, vous recevrez une erreur HTTP `Error 503 Server unavailable`. Le redémarrage complet peut prendre quelques minutes.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Étapes suivantes

[FAQ de l’application web Azure App Service sur Linux](https://docs.microsoft.com/azure/app-service-web/app-service-linux-faq.md)
