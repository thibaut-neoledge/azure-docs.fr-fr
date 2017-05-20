---
title: "Création d’une application Ruby dans l’application web Azure App Service sur Linux | Microsoft Docs"
description: "Apprenez à créer des applications Ruby avec l’application web Azure App Service sur Linux."
keywords: azure app service, linux, oss
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
ms.date: 05/05/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c63bf790a50b894ea1732ae3142e6c1ff9ccbd4c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-ruby-app-with-azure-web-app-on-linux---preview"></a>Créer une application Ruby avec Azure Web App sur Linux - Préversion

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Vue d'ensemble

Ce didacticiel vous montre comment créer une application Ruby on Rails de base en local et la déployer dans Azure Web App sur Linux.

## <a name="prerequisites"></a>Composants requis

* [Ruby 2.3.3 ou version ultérieure](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller) doit être installé sur votre ordinateur de développement.
* [Git](https://git-scm.com/downloads) doit être installé sur votre ordinateur de développement.
* Vous devez disposer d’un [abonnement Azure actif](https://azure.microsoft.com/pricing/free-trial/).
* Ce didacticiel est rédigé dans le contexte d’un environnement Ubuntu. Toutes les commandes système sont spécifiques à un interpréteur de commande (Bash).


## <a name="create-a-new-local-rails-application"></a>Créer une nouvelle application Rails locale

1. Créez un répertoire pour la nouvelle application et accédez à ce répertoire.

        mkdir ~/workspace
        cd ~/workspace

2. Initialisez Ruby et vérifiez la version à l’aide de la commande `ruby -v`.

    ![Initialiser Ruby](./media/app-service-linux-ruby-get-started/ruby-version.png)

3. Installez Rails à l’aide de la commande `gem install rails`.

    ![Installer Rails](./media/app-service-linux-ruby-get-started/install-rails.png)

4. Créez une application Rails nommée **hello-world** à l’aide de la commande suivante :

    Si vous utilisez Rails 5.1.0 ou une version ultérieure, incluez l’option `--skip-yarn` comme illustré dans la commande suivante :

        rails new hello-world --skip-yarn

    Pour les versions Rails antérieures à la version 5.1.0, utilisez la commande suivante :

        rails new hello-world 

    ![Nouveau Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world.png)

    ![Nouveau Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world-2.png)

    Si vous utilisez Rails 5.1+, un fichier package.json est créé si l’option `--skip-yarn` n’est pas utilisée. Nous ne voulons pas l’inclure dans notre déploiement. Vous pouvez également supprimer le fichier package.json ou l’ajouter au fichier *.git-ignore* dans le répertoire en procédant comme suit : 

        # Ignore package.json
        /package.json

5. Passez au répertoire *hello-world* et démarrez le serveur.

        cd hello-world
        rails server

    ![Démarrer Hello-world](./media/app-service-linux-ruby-get-started/start-hello-world-server.png)
    
6. À l’aide de votre navigateur web, accédez à `http://localhost:3000` pour tester l’application en local.    

    ![Hello-world](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="prepare-the-app-for-azure"></a>Préparation de l’application pour Azure

Par défaut, l’image Ruby exécute le serveur avec l’indicateur `-e production`. Cet environnement nécessite une configuration particulière pour Azure Web App sur Linux. Le conteneur prend en charge une partie de cette configuration (comme le paramétrage d’un élément `SECRET_KEY_BASE`). Un itinéraire par défaut doit être configuré. Dans le cas contraire, vous recevez une erreur 404 lorsque vous naviguez sur le site.

Pour configurer un itinéraire par défaut :

1. Ouvrez *~/workspace/hello-world/config/routes.rb* pour le modifier. Ajoutez la ligne suivante, comme indiqué dans la capture d’écran. 

        root 'application#hello'

    ![routes.rb](./media/app-service-linux-ruby-get-started/routes-rb.png)


2. Ouvrez *~/workspace/hello-world/app/controllers/application_controller.rb* pour le modifier. Ajoutez les lignes suivantes, comme indiqué dans la capture d’écran.

        def hello
            render html: "Hello, world from Azure Web App on Linux!"
        end

    ![routes.rb](./media/app-service-linux-ruby-get-started/application-controller-rb.png)


3. Votre application est désormais configurée. À l’aide de votre navigateur web, accédez à `http://localhost:3000` pour vérifier la page d’accueil racine.

    ![Hello World configurée](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

## <a name="create-a-ruby-website-on-azure"></a>Création d’un site web Ruby sur Azure

1. Accédez au [portail Azure](http://portal.azure.com) et connectez-vous avec votre abonnement. Ajoutez une **application web sur Linux**, comme illustré dans la capture d’écran suivante :

    ![Créer l’application web sur Linux](./media/app-service-linux-ruby-get-started/top-level-create.png)

2. Le **panneau Créer** s’ouvre comme indiqué dans la capture d’écran suivante :

    ![Panneau Créer](./media/app-service-linux-ruby-get-started/create-blade.png)


    1. Donnez un nom à votre application web.
    2. Sélectionnez un groupe de ressources existant ou créez-en un. (Consultez les régions disponibles dans la [section Limitations](app-service-linux-intro.md).)
    3. Sélectionnez un plan Azure App Service existant ou créez-en un. (Consultez les notes relatives au plan App Service dans la [section Limitations](app-service-linux-intro.md).)
    4. Choisissez la pile runtime **Ruby 2.3** intégrée pour la configuration de votre conteneur.
    5. Cliquez sur **Épingler au tableau de bord** pour l’application web.
    6. Cliquez sur **Create**.

3. Une fois l’application web créée, le panneau **Vue d’ensemble** s’affiche. Copiez l’**URL** correspondant à la nouvelle application web et ouvrez-la dans le navigateur. La page de démarrage suivante s’affiche.

    ![page de démarrage](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>Déployer votre application

Dans ce didacticiel, nous utilisons Git pour déployer l’application Ruby locale sur Azure.

1. Un déploiement Git est déjà configuré sur le nouveau site web Azure. Vous trouverez l’URL de déploiement Git en suivant l’URL suivante après avoir inséré le nom de votre application web :

        https://{your web app name}.scm.azurewebsites.net/api/scm/info

    L’URL Git a la forme suivante, selon le nom de votre application web :

        https://{your web app name}.scm.azurewebsites.net/{your web app name}.git

2. Exécutez les commandes suivantes pour déployer l’application locale sur votre site web Azure.

        cd ~/workspace/hello-world
        git init
        git remote add azure <Git deployment URL from above>
        git add -A
        git commit -m "Initial deployment commit"
        git push master

    Vérifiez que les opérations de déploiement à distance réussissent.

    ![Déploiement de l'application web](./media/app-service-linux-ruby-get-started/deployment-success.png)

    Si vous voyez une erreur indiquant que l’instance distante a raccroché, le déploiement est probablement encore en cours. Dans ce cas, accédez à l’URL suivante dans votre navigateur :

        https://{your web app name}.scm.azurewebsites.net/api/deployments

3. Une fois le déploiement terminé, redémarrez votre application web pour que le déploiement prenne effet. Dans le [portail Azure](http://portal.azure.com), accédez au panneau **Vue d’ensemble** de votre application web.

    Dans la barre d’outils, cliquez sur **Redémarrer**.

    ![Redémarrer l’application web](./media/app-service-linux-ruby-get-started/restart-web-app.png)

4. Accédez à votre site et vérifiez que vos mises à jour ont été publiées. 

    Si vous tentez de parcourir les résultats du site pendant que l’application redémarre, vous recevrez une erreur HTTP 503 (serveur indisponible). Le redémarrage complet peut prendre quelques minutes.

        http://{your web app name}.azurewebsites.net

    ![application web mise à jour](./media/app-service-linux-ruby-get-started/hello-world-updated.png)
    

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’application web Azure App Service sur Linux, consultez les liens suivants. Vous pouvez également poser des questions et signaler vos préoccupations sur [notre forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Création d’applications Web dans App Service sur Linux](app-service-linux-how-to-create-web-app.md)
* [Utilisation d’une image Docker personnalisée pour App Service sur Linux](app-service-linux-using-custom-docker-image.md)
* [Utilisation de la configuration PM2 pour Node.js dans les applications Web sur Linux](app-service-linux-using-nodejs-pm2.md)
* [Utilisation de .NET Core dans Azure App Service Web Apps sur Linux](app-service-linux-using-dotnetcore.md)
* [FAQ Azure App Service Web Apps sous Linux](app-service-linux-faq.md)


