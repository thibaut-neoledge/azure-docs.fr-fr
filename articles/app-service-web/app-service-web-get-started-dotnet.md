---
title: "Déployer votre première application web .NET dans Azure en 5 minutes | Microsoft Docs"
description: "Découvrez la facilité avec laquelle vous pouvez exécuter des applications web dans App Service en déployant un exemple d’application. Commencez le développement rapidement et visualisez les résultats immédiatement."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/13/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 331ada90e979cd0032907d80e8535d1872e90797


---
# <a name="deploy-your-first-net-web-app-to-azure-in-five-minutes"></a>Déployer votre première application web .NET dans Azure en 5 minutes
Dans ce didacticiel, vous découvrirez comment déployer une application web .NET simple dans [Azure App Service](../app-service/app-service-value-prop-what-is.md).
App Service permet de créer des applications web, des[back-ends d’applications mobiles](/documentation/learning-paths/appservice-mobileapps/) et des [applications API](../app-service-api/app-service-api-apps-why-best-platform.md).

Vous allez : 

* Créer une application web dans Azure App Service
* Déployer un exemple de code ASP.NET
* Voir votre code s’exécuter dans un environnement de production
* Mettre à jour votre application web de la même façon que vous [transmettez des validations Git](https://git-scm.com/docs/git-push)

## <a name="prerequisites"></a>Configuration requise
* [Git](http://www.git-scm.com/downloads).
* [Interface de ligne de commande Azure](../xplat-cli-install.md).
* Un compte Microsoft Azure Si vous n’avez pas de compte, vous pouvez [vous inscrire pour un essai gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Vous pouvez [essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751) sans compte Azure. Créez une application de base et expérimentez-la pendant une heure, sans carte de paiement et sans engagement.
> 
> 

## <a name="deploy-an-net-web-app"></a>Déployer une application web .NET
1. Ouvrez une nouvelle invite de commandes Windows, une fenêtre PowerShell, un interpréteur de commandes Linux ou un terminal OS X. Exécutez `git --version` et `azure --version` pour vérifier que Git et l’interface de ligne de commande Azure sont installés sur votre ordinateur.
   
    ![Tester l’installation des outils de l’interface de ligne de commande pour votre première application web dans Azure](./media/app-service-web-get-started/1-test-tools.png)
   
    Si les outils ne sont pas installés, consultez la [Configuration requise](#Prerequisites) pour accéder aux liens de téléchargement.
2. Connectez-vous au portail Azure :
   
        azure login
   
    Appuyez-vous sur le message d’aide pour poursuivre le processus de connexion.
   
    ![Se connecter à Azure pour créer votre première application web](./media/app-service-web-get-started/3-azure-login.png)
3. Passez l’interface de ligne de commande Azure en mode ASM, puis définissez l’utilisateur de déploiement pour le service App Service. Vous déployez le code à l’aide des informations d’identification ultérieurement.
   
        azure config mode asm
        azure site deployment user set --username <username> --pass <password>
4. Passez à un répertoire de travail (`CD`) et clonez l’exemple d’application.
   
        git clone https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git
5. Passez au référentiel de votre exemple d’application. 
   
        cd app-service-web-dotnet-get-started
6. Créez la ressource d’application App Service dans Azure avec un nom d’application unique et l’utilisateur de déploiement que vous avez configuré précédemment. Lorsque vous y êtes invité, spécifiez le numéro de la région souhaitée.
   
        azure site create <app_name> --git --gitusername <username>
   
    ![Créer la ressource Azure pour votre première application web dans Azure](./media/app-service-web-get-started-languages/dotnet-site-create.png)
   
    Votre application est maintenant créée dans Azure. Votre répertoire actuel a été initialisé avec Git et est connecté au nouvel App Service en tant que Git distant.
    Vous pouvez accéder à l’URL de l’application (http://&lt;app_name>.azurewebsites.net) pour visualiser la belle page HTML par défaut, mais essayons d’abord d’y amener votre code.
7. Déployez votre exemple de code dans votre application Azure de la même façon que vous transmettez du code avec Git. Lorsque vous y êtes invité, utilisez le mot de passe que vous avez configuré précédemment.
   
        git push azure master
   
    ![Transmettez du code pour votre première application web dans Azure](./media/app-service-web-get-started-languages/dotnet-git-push.png)
   
    `git push` restaure les packages requis et génère les fichiers binaires ASP.NET. 

Félicitations, vous avez déployé votre application vers Azure App Service.

## <a name="see-your-app-running-live"></a>Exécuter votre application en temps réel
Pour voir votre application en cours d’exécution dans Azure, exécutez cette commande à partir de n’importe quel répertoire de votre référentiel :

    azure site browse

## <a name="make-updates-to-your-app"></a>Mettre à jour votre application
Vous pouvez désormais utiliser Git pour transmettre votre code depuis la racine (référentiel) de votre projet dès que vous avez besoin d’apporter une mise à jour au site en ligne. Pour ce faire, il vous suffit de suivre la même procédure que pour le déploiement initial de votre code. Par exemple, chaque fois que vous voulez transmettre une nouvelle modification que vous avez testée localement, exécutez simplement les commandes suivantes depuis la racine (référentiel) de votre projet :

    git add .
    git commit -m "<your_message>"
    git push azure master


## <a name="next-steps"></a>Étapes suivantes
Découvrez comment créer, développer et déployer des applications web .NET dans Azure directement depuis Visual Studio en consultant l’article [Déployer une application web ASP.NET dans Azure App Service à l’aide de Visual Studio](web-sites-dotnet-get-started.md).

Ou faites-en plus avec votre première application web. Par exemple :

* Essayez [d’autres méthodes de déploiement de votre code dans Azure](web-sites-deploy.md). Par exemple, pour effectuer un déploiement à partir de l’un de vos référentiels GitHub, sélectionnez simplement **GitHub** au lieu de **Dépôt Git local** dans **Options de déploiement**.
* Donnez une nouvelle dimension à votre application Azure. Authentifiez vos utilisateurs. Faites évoluer sa capacité en fonction de la demande. Configurez des alertes de performance. Tout cela en seulement quelques clics. Consultez l’article [Ajouter des fonctionnalités à votre première application web](app-service-web-get-started-2.md).




<!--HONumber=Dec16_HO1-->


