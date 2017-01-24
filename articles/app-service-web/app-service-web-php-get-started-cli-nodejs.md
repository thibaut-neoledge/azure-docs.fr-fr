---
title: "Créer, configurer et déployer une application web PHP dans Azure | Microsoft Docs"
description: "Ce didacticiel vous montre comment exécuter une application web PHP (Laravel) dans Azure App Service. Découvrez comment configurer Azure App Service pour répondre aux exigences de l’infrastructure PHP de votre choix."
services: app-service\web
documentationcenter: php
author: cephalin
manager: wpickett
editor: 
tags: mysql
ms.assetid: cb73859d-48aa-470a-b486-d984746d6d26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: f595be46983bf07783b529de885d889c18fdb61a
ms.openlocfilehash: 8cdbc6e5fccfce56b6c33720e3d442821d26fbd4


---
# <a name="create-configure-and-deploy-a-php-web-app-to-azure"></a>Créer, configurer et déployer une application web PHP dans Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Ce didacticiel vous montre comment créer, configurer et déployer une application web PHP pour Azure. Il explique également comment configurer Azure App Service pour répondre aux exigences de votre application web PHP. À la fin du didacticiel, vous disposerez d’une application web [Laravel](https://www.laravel.com/) parfaitement opérationnelle sous [Azure App Service](../app-service/app-service-value-prop-what-is.md).

En tant que développeur PHP, vous pouvez transférer l’infrastructure PHP de votre choix dans Azure. Ce didacticiel utilise simplement Laravel comme exemple d’application concrète. Vous apprendrez à effectuer les opérations suivantes : 

* Effectuer un déploiement à l’aide de Git
* Définir la version PHP
* Utiliser un fichier de démarrage qui ne se trouve pas dans le répertoire racine de l’application
* Accéder à des variables spécifiques à l’environnement
* Mettez à jour votre application dans Azure

Vous pourrez appliquer vos acquis à d’autres applications web PHP que vous déployez sur Azure.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](app-service-web-php-get-started-cli-nodejs.md) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager
- [Azure CLI 2.0 (version préliminaire)](app-service-web-php-get-started.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="prerequisites"></a>Composants requis
* [PHP 5.6.29](http://php.net/downloads.php)
* [Composer](https://getcomposer.org/download/)
* [Interface de ligne de commande Azure](../xplat-cli-install.md)
* [Git](http://www.git-scm.com/downloads)
* Un compte Microsoft Azure Si vous n’avez pas de compte, vous pouvez [vous inscrire pour un essai gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Visualisez une application web en action. [Essayez App Service](http://go.microsoft.com/fwlink/?LinkId=523751) dès maintenant et créez une première application temporaire. Aucune carte de crédit ni aucun engagement ne sont nécessaires.
> 
> 

## <a name="create-a-php-laravel-app-on-your-dev-machine"></a>Créer une application PHP (Laravel) sur votre ordinateur de développement
1. Ouvrez une nouvelle invite de commandes Windows, une fenêtre PowerShell, un interpréteur de commandes Linux ou un terminal OS X. Exécutez les commandes suivantes pour vérifier que les outils requis sont correctement installés sur votre ordinateur. 
   
        php --version
        composer --version
        azure --version
        git --version
   
    Si les outils ne sont pas installés, consultez la [Configuration requise](#Prerequisites) pour accéder aux liens de téléchargement.

2. Installez Laravel comme suit :
   
        composer global require "laravel/installer"
3. À l’aide de la commande `CD`, accédez à un répertoire de travail et créez une nouvelle application Laravel comme suit :
   
        cd <working_directory>
        laravel new <app_name>
4. À l’aide de l’a commande `CD`, accédez au répertoire `<app_name>` que vous venez de créer et testez l’application comme suit :
   
        cd <app_name>
        php artisan serve
   
    Vous devriez maintenant pouvoir accéder à l’adresse http://localhost:8000 dans un navigateur et voir l’écran de démarrage Laravel.
   
    ![Tester votre application PHP (Laravel) en locale avant de la déployer vers Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)

À ce stade, nous sommes toujours dans le workflow Laravel habituel, et le but n’est pas ici <a href="https://laravel.com/docs/5.3" rel="nofollow">d’apprendre à utiliser Laravel</a>. Passons donc à l’étape suivante.

## <a name="create-an-azure-web-app-and-set-up-git-deployment"></a>Créer une application web Azure et configurer le déploiement Git
> [!NOTE]
> « Attendez ! Que se passe-t-il si je souhaite déployer avec le protocole FTP? » Voici un [didacticiel FTP](web-sites-php-mysql-deploy-use-ftp.md) qui vous sera utile. 
> 
> 

Vous pouvez utiliser l’interface de ligne de commande Azure pour créer une application web dans Azure App Service et la configurer pour un déploiement Git avec une seule ligne de commande. Allons-y.

1. Passez en mode ASM et connectez-vous à Azure :
   
        azure config mode asm
        azure login
   
    Appuyez-vous sur le message d’aide pour poursuivre le processus de connexion.
   
    ![Se connecter à Azure pour déployer votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/log-in-to-azure-cli.png)

3. Définissez l’utilisateur de déploiement pour App Service. Vous déployez le code à l’aide des informations d’identification ultérieurement.
   
        azure site deployment user set --username <username> --pass <password>

2. Exécutez la commande pour créer l’application web Azure avec un déploiement Git. Lorsque vous y êtes invité, spécifiez le numéro de la région souhaitée.
   
        azure site create --git <app_name>
   
    ![Créer la ressource Azure pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/create-site-cli.png)
   
    Cette commande crée un nouveau référentiel Git sur le répertoire en cours (avec `git init`) et le connecte au référentiel dans Azure en tant que Git distant (avec `git remote add`).

<a name="configure"/>

## <a name="configure-the-azure-web-app"></a>Configurer l’application web Azure
Pour que votre application Laravel puisse fonctionner dans Azure, vous devez tenir compte de plusieurs aspects. Vous pourrez répéter l’exercice suivant pour l’infrastructure PHP de votre choix.

* Configurez PHP 5.6.4 ou version ultérieure. Consultez la section relative à la [configuration serveur requise pour Laravel 5.3](https://laravel.com/docs/5.3#server-requirements) pour obtenir la liste complète de la configuration requise du serveur. Le reste de la liste indique des extensions qui sont déjà activées par les installations PHP d’Azure. 
* Définissez les variables d’environnement requises par votre application. Laravel utilise le fichier `.env` pour faciliter la configuration des variables d’environnement. Mais dans la mesure où il n’est pas censé être validé dans le contrôle de code source (voir la section relative à la [configuration de l’environnement Laravel](https://laravel.com/docs/5.3/configuration#environment-configuration)), vous allez devoir définir à la place les paramètres d’application de votre application web Azure.
* Veillez à charger en premier le point d’entrée de l’application Laravel, `public/index.php`. Consultez la [vue d’ensemble du cycle de vie de Laravel](https://laravel.com/docs/5.3/lifecycle#lifecycle-overview). En d’autres termes, vous devez définir l’URL racine de l’application web pour pointer vers le répertoire `public` .
* Activez l’extension du Compositeur dans Azure, puisque vous disposez d’un fichier composer.json. Vous pourrez ainsi laisser au Compositeur le soin d’obtenir les packages dont vous avez besoin lors d’un déploiement avec `git push`. Cette étape n’est à effectuer que pour des raisons pratiques. 
  Si vous n’activez pas l’automatisation du Compositeur, il vous suffit de supprimer `/vendor` du fichier `.gitignore` pour que Git inclue (« n’ignore pas ») tous les éléments du répertoire `vendor` lors de la validation et du déploiement du code.

Nous allons maintenant configurer ces tâches de façon séquentielle.

1. Définissez la version PHP dont a besoin votre application Laravel.
   
        azure site set --php-version 5.6
   
    Vous avez terminé l’étape de définition de la version de PHP ! 
2. Générez une nouvelle `APP_KEY` pour votre application web Azure et définissez-la comme paramètre d’application pour votre application web Azure.
   
        php artisan key:generate --show
        azure site appsetting add APP_KEY="<output_of_php_artisan_key:generate_--show>"
3. Activez également le débogage de Laravel pour prévaloir sur toute page `Whoops, looks like something went wrong.` .
   
        azure site appsetting add APP_DEBUG=true
   
    Vous avez fini de définir les variables d’environnement !
   
   > [!NOTE]
   > Attendez, ralentissons un peu le rythme et voyons ce que font respectivement Laravel et Azure ici. Laravel utilise le fichier `.env` dans le répertoire racine pour fournir des variables d’environnement à l’application, où vous trouverez la ligne `APP_DEBUG=true` (et également `APP_KEY=...`). Cette variable est accessible dans `config/app.php` par le biais du code `'debug' => env('APP_DEBUG', false),`. [env()](https://laravel.com/docs/5.3/helpers#method-env) est une méthode d’assistance Laravel qui utilise le fichier PHP [getenv()](http://php.net/manual/en/function.getenv.php) en arrière-plan.
   > 
   > Cependant, `.env` est ignoré par Git car il est appelé par le fichier `.gitignore` dans le répertoire racine. Pour simplifier, `.env` dans votre référentiel Git local n’est pas placé dans Azure avec le reste des fichiers. Bien sûr, vous pouvez simplement supprimer cette ligne de `.gitignore`, mais comme nous l’avons indiqué, il n’est pas recommandé de valider ce fichier dans le contrôle de code source. Pour autant, il vous faut trouver un moyen de spécifier ces variables d’environnement dans Azure. 
   > 
   > La bonne nouvelle est que les paramètres d’application dans Azure App Service prennent en charge [getenv()](http://php.net/manual/en/function.getenv.php) dans PHP. Bien que vous puissiez utiliser FTP ou d’autres moyens pour charger manuellement un fichier `.env` dans Azure, vous pouvez simplement spécifier les variables de votre choix en tant que paramètres de l’application Azure sans ajouter de `.env` dans Azure, comme vous venez de faire. En outre, si une variable se trouve à la fois dans un fichier `.env` et dans les paramètres d’application Azure, le paramètre d’application Azure l’emporte.     
   > 
   > 
4. Les deux dernières tâches (la configuration du répertoire virtuel et l’activation du Compositeur) impliquent l’utilisation du [portail Azure](https://portal.azure.com). Vous devez donc vous connecter au [portail](https://portal.azure.com) avec votre compte Azure.
5. Dans le menu de gauche, cliquez sur **App Services** > **&lt;nom_application** > **Extensions**.
   
    ![Activer le Compositeur pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
   
6. Cliquez sur **Ajouter** pour ajouter une extension.
7. Sélectionnez **Compositeur** dans le [panneau](../azure-resource-manager/resource-group-portal.md#manage-resources) **Choisir une extension** (*panneau* : une page de portail s’ouvre à l’horizontale).
8. Cliquez sur **OK** dans le [panneau](../azure-resource-manager/resource-group-portal.md#manage-resources) **Accepter les conditions juridiques**. 
9. Cliquez sur **OK** dans le [panneau](../azure-resource-manager/resource-group-portal.md#manage-resources) **Ajouter une extension**.
   
    Lorsqu’Azure a terminé d’ajouter l’extension, un message contextuel doit s’afficher dans l’angle et le  **Compositeur** doit normalement être répertorié dans le [panneau](../azure-resource-manager/resource-group-portal.md#manage-resources) **Extensions**.
   
    ![Panneau Extensions après activation du Compositeur pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-composer-end.png)
   
    Vous avez terminé l’activation de l’éditeur !
10. Dans le [panneau de ressources](../azure-resource-manager/resource-group-portal.md#manage-resources) de votre application web, cliquez sur **Paramètres de l’application**.
    
     ![Accéder au panneau Paramètres pour définir le répertoire virtuel de votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)
    
     Dans le panneau **Paramètres de l’application** , notez la version de PHP que vous avez définie précédemment :
    
     ![Version de PHP dans le panneau Paramètres pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a.png)
    
     et les paramètres d’application que vous avez ajoutés :
    
     ![Paramètres de l’application dans le panneau Paramètres pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)
11. Faites défiler le [panneau](../azure-resource-manager/resource-group-portal.md#manage-resources) jusqu’en bas et accédez au répertoire virtuel racine pour pointer vers **site\wwwroot\public** au lieu de **site\wwwroot**.
    
     ![Définir un répertoire virtuel pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)
12. Cliquez sur **Enregistrer** dans la partie supérieure du [panneau](../azure-resource-manager/resource-group-portal.md#manage-resources).
    
     Vous avez fini de définir le répertoire virtuel. 

## <a name="deploy-your-web-app-with-git-and-setting-environment-variables"></a>Déployer votre application web avec Git (et définir les variables d’environnement)
Vous êtes maintenant prêt à déployer votre code. Pour cela, vous devez revenir à votre invite ou à votre terminal de commandes.

1. Validez toutes vos modifications et déployez votre code dans l’application web Azure, comme vous le feriez dans n’importe quel référentiel Git :
   
        git add .
        git commit -m "Hurray! My first commit for my Azure app!"
        git push azure master 
   
    Lorsque vous y êtes invité, utilisez les informations d’identification utilisateur créées précédemment. 

2. Voyons maintenant comment cela se passe au niveau du navigateur en exécutant la commande suivante :
   
        azure site browse
   
    Votre navigateur doit afficher l’écran de démarrage Laravel.
   
    ![Écran de démarrage Laravel après le déploiement de l’application web vers Azure](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
   
    Félicitations, votre application web Laravel peut maintenant s’exécuter dans Azure.

## <a name="troubleshoot-common-errors"></a>Résolution des erreurs courantes
Voici les erreurs que vous pouvez rencontrer dans ce didacticiel :

* [La ligne de commande Azure affiche « 'site' is not an azure command »](#clierror)
* [L’application web affiche l’erreur HTTP 403](#http403)
* [L’application web affiche le message « Whoops, looks like something went wrong. »](#whoops)
* [L’application web affiche le message « No supported encryptor found. »](#encryptor)

<a name="clierror"></a>

### <a name="azure-cli-shows-site-is-not-an-azure-command"></a>La ligne de commande Azure affiche « 'site' is not an azure command »
Lors de l’exécution de `azure site *` dans le terminal de ligne de commande, l’erreur `error:   'site' is not an azure command. See 'azure help'.` s’affiche. 

Cette erreur est généralement liée au passage en mode « ARM » (Azure Resource Manager). Pour résoudre ce problème, revenez en mode « ASM » (Azure Service Management) en exécutant `azure config mode asm`.

<a name="http403"></a>

### <a name="web-app-shows-http-403-error"></a>L’application web affiche l’erreur HTTP 403
Vous avez réussi à déployer votre application web sur Azure, mais lorsque vous accédez à votre application web Azure, vous obtenez une erreur `HTTP 403` ou `You do not have permission to view this directory or page.`

Cela est probablement dû au fait que l’application web ne peut pas trouver le point d’entrée à l’application Laravel. Assurez-vous que vous avez correctement modifié le répertoire virtuel racine de manière à ce qu’il pointe vers `site\wwwroot\public`, où se trouve l’élément `index.php` de Laravel (voir la section [Configurer l’application web Azure](#configure)).

<a name="whoops"></a>

### <a name="web-app-shows-whoops-looks-like-something-went-wrong"></a>L’application web affiche le message « Whoops, looks like something went wrong. »
Vous avez réussi à déployer votre application web sur Azure, mais lorsque vous accédez à votre application web Azure, vous obtenez le message `Whoops, looks like something went wrong.`

Pour obtenir une erreur plus explicite, activez le débogage de Laravel en définissant la variable d’environnement `APP_DEBUG` sur `true` (voir [Configurer l’application web Azure](#configure)).

<a name="encryptor"></a>

### <a name="web-app-shows-no-supported-encryptor-found"></a>L’application web affiche le message « No supported encryptor found. »
Vous avez réussi à déployer votre application web sur Azure, mais lorsque vous accédez à votre application web Azure, vous obtenez le message d’erreur suivant :

![APP_KEY manquant dans votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)

Cette erreur est regrettable, mais elle est explicite puisque vous avez activé le débogage Laravel. Après une recherche rapide de la chaîne d’erreur sur les forums Laravel, vous remarquerez qu’elle n’est pas liée à l’absence du paramètre APP_KEY dans `.env`, ou dans votre cas, à l’absence pure et simple de `.env` dans Azure. Pour résoudre ce problème, ajoutez le paramètre `APP_KEY` en tant que paramètre d’application Azure (voir [Configurer l’application web Azure](#configure)).

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment ajouter des données à votre application en [créant une base de données MySQL dans Azure](../store-php-create-mysql-database.md). Vous trouverez également ci-dessous d’autres liens utiles pour le déploiement de PHP dans Azure :

* [Centre de développement PHP](/develop/php/)
* [Créer une application web à partir d’Azure Marketplace](app-service-web-create-web-app-from-marketplace.md)
* [Configurer PHP dans Azure App Service Web Apps](web-sites-php-configure.md)
* [Conversion de WordPress en WordPress multisite dans Azure App Service](web-sites-php-convert-wordpress-multisite.md)
* [WordPress d’entreprise sur Azure App Service](web-sites-php-enterprise-wordpress.md)




<!--HONumber=Dec16_HO3-->


