<properties
	pageTitle="Créer, configurer et déployer une application web PHP dans Azure"
	description="Ce didacticiel vous montre comment exécuter une application web PHP (Laravel) dans Azure App Service. Découvrez comment configurer Azure App Service pour répondre aux exigences de l’infrastructure PHP de votre choix."
	services="app-service\web"
	documentationCenter="php"
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="mysql"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="06/03/2016" 
	ms.author="cephalin"/>

# Créer, configurer et déployer une application web PHP dans Azure

[AZURE.INCLUDE [onglets](../../includes/app-service-web-get-started-nav-tabs.md)]

Ce didacticiel vous montre comment créer, configurer et déployer une application web PHP pour Azure. Il explique également comment configurer Azure App Service pour répondre aux exigences de votre application web PHP. À la fin du didacticiel, vous disposerez d’une application web [Laravel](https://www.laravel.com/) parfaitement opérationnelle sous [Azure App Service](../app-service/app-service-value-prop-what-is.md).

En tant que développeur PHP, vous pouvez transférer l’infrastructure PHP de votre choix dans Azure. Ce didacticiel utilise simplement Laravel comme exemple d’application concrète. Vous apprendrez à effectuer les opérations suivantes :

- Effectuer un déploiement à l’aide de Git
- Définir la version PHP
- Utiliser un fichier de démarrage qui ne se trouve pas dans le répertoire racine de l’application
- Accéder à des variables spécifiques à l’environnement
- Mettez à jour votre application dans Azure

Vous pourrez appliquer vos acquis à d’autres applications web PHP que vous déployez sur Azure.

## Composants requis

- Installer [PHP 5.6.x](http://php.net/downloads.php) (prise en charge de PHP 7 en version bêta)
- Installer [Composer](https://getcomposer.org/download/)
- Installer [l’interface de ligne de commande Azure](../xplat-cli-install.md)
- Installer [Git](http://www.git-scm.com/downloads)
- Obtenir un compte Microsoft Azure. Si vous ne possédez pas de compte, vous pouvez [vous inscrire à un essai gratuit](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Visualisez une application web en action. [Essayez App Service](http://go.microsoft.com/fwlink/?LinkId=523751) dès maintenant et créez une première application temporaire. Aucune carte de crédit ni aucun engagement ne sont nécessaires.

## Créer une application PHP (Laravel) sur votre ordinateur de développement

1. Ouvrez une nouvelle invite de commandes Windows, une fenêtre PowerShell, un interpréteur de commandes Linux ou un terminal OS X. Exécutez les commandes suivantes pour vérifier que les outils requis sont correctement installés sur votre ordinateur. 

        php --version
        composer --version
        azure --version
        git --version

    ![Tester l’installation des outils avant de créer votre application PHP (Laravel) pour Azure](./media/app-service-web-php-get-started/test-tools.png)

    Si les outils ne sont pas installés, consultez la [Configuration requise](#Prerequisites) pour accéder aux liens de téléchargement.
    
2. Installez Laravel comme suit :

        composer global require "laravel/installer

3. À l’aide de la commande `CD`, accédez à un répertoire de travail et créez une nouvelle application Laravel comme suit :

        cd <working_directory>
        laravel new <app_name>

4. À l’aide de l’a commande `CD`, accédez au répertoire `<app_name>` que vous venez de créer et testez l’application comme suit :

        cd <app_name>
        php artisan serve
        
    Vous devriez maintenant pouvoir accéder à http://localhost:8000 dans un navigateur et voir l’écran de démarrage Laravel.
    
    ![Tester votre application PHP (Laravel) en locale avant de la déployer vers Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)
    
À ce stade, nous sommes toujours dans le workflow Laravel habituel, et le but n’est pas ici <a href="https://laravel.com/docs/5.2" rel="nofollow">d’apprendre à utiliser Laravel</a>. Passons donc à l’étape suivante.

## Créer une application web Azure et configurer le déploiement Git

>[AZURE.NOTE] « Attendez ! Que se passe-t-il si je souhaite déployer avec le protocole FTP? » Voici un [didacticiel FTP](web-sites-php-mysql-deploy-use-ftp.md) qui vous sera utile.

Vous pouvez utiliser l’interface de ligne de commande Azure pour créer une application web dans Azure App Service et la configurer pour un déploiement Git avec une seule ligne de commande. Allons-y.

3. Connectez-vous au portail Azure :

        azure login
    
    Appuyez-vous sur le message d’aide pour poursuivre le processus de connexion.
    
    ![Se connecter à Azure pour déployer votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/log-in-to-azure-cli.png)

4. Exécutez la commande pour créer l’application web Azure avec un déploiement Git. Lorsque vous y êtes invité, spécifiez le numéro de la région souhaitée.

        azure site create --git <app_name>
    
    ![Créer la ressource Azure pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/create-site-cli.png)
    
    >[AZURE.NOTE] Si vous n’avez jamais configuré les informations d’identification de déploiement pour votre abonnement Azure, vous serez invité à les créer. Ces informations d’identification (et non pas les informations d’identification de votre compte Azure) sont utilisées par App Service uniquement dans le cadre des déploiements Git et des connexions FTP.
    
    Cette commande crée un nouveau référentiel Git sur le répertoire en cours (avec `git init`) et le connecte au référentiel dans Azure en tant que Git distant (avec `git remote add`).

<a name="configure"/>
## Configurer l’application web Azure

Pour que votre application Laravel puisse fonctionner dans Azure, vous devez tenir compte de plusieurs aspects. Vous pourrez répéter l’exercice suivant pour l’infrastructure PHP de votre choix.

- Configurez PHP 5.5.9 ou version ultérieure. Consultez la section relative à la [configuration serveur requise pour Laravel 5.2](https://laravel.com/docs/5.2#server-requirements) pour obtenir la liste complète de la configuration requise du serveur. Le reste de la liste indique des extensions qui sont déjà activées par les installations PHP d’Azure. 
- Définissez les variables d’environnement requises par votre application. Laravel utilise le fichier `.env` pour faciliter la configuration des variables d’environnement. Mais dans la mesure où il n’est pas censé être validé dans le contrôle de code source (voir la section relative à la [configuration de l’environnement Laravel](https://laravel.com/docs/5.2/configuration#environment-configuration)), vous allez devoir définir à la place les paramètres d’application de votre application web Azure.
- Veillez à charger en premier le point d’entrée de l’application Laravel, `public/index.php`. Consultez la [vue d’ensemble du cycle de vie de Laravel](https://laravel.com/docs/5.2/lifecycle#lifecycle-overview). En d’autres termes, vous devez définir l’URL racine de l’application web pour pointer vers le répertoire `public`.
- Activez l’extension du Compositeur dans Azure, puisque vous disposez d’un fichier composer.json. Vous pourrez ainsi laisser au Compositeur le soin d’obtenir les packages dont vous avez besoin lors d’un déploiement avec `git push`. Cette étape n’est à effectuer que pour des raisons pratiques. Si vous n’activez pas l’automatisation du Compositeur, il vous suffit de supprimer `/vendor` du fichier `.gitignore` pour que Git inclue (« n’ignore pas ») tous les éléments du répertoire `vendor` lors de la validation et du déploiement du code.

Nous allons maintenant configurer ces tâches de façon séquentielle.

4. Définissez la version PHP dont a besoin votre application Laravel.

        azure site set --php-version 5.6

    Vous avez terminé l’étape de définition de la version de PHP !
    
4. Générez une nouvelle `APP_KEY` pour votre application web Azure et définissez-la comme paramètre d’application pour votre application web Azure.

        php artisan key:generate --show
        azure site appsetting add APP_KEY="<output_of_php_artisan_key:generate_--show>"

4. Activez également le débogage de Laravel pour prévaloir sur toute page `Whoops, looks like something went wrong.`.

        azure site appsetting add APP_DEBUG=true

    Vous avez fini de définir les variables d’environnement !
    
    >[AZURE.NOTE] Attendez, ralentissons un peu le rythme et voyons ce que font respectivement Laravel et Azure ici. Laravel utilise le fichier `.env` dans le répertoire racine pour fournir des variables d’environnement à l’application, où vous trouverez la ligne `APP_DEBUG=true` (et également `APP_KEY=...`). Cette variable est accessible dans `config/app.php` par le code `'debug' => env('APP_DEBUG', false),`. [env()](https://laravel.com/docs/5.2/helpers#method-env) est une méthode d’assistance Laravel qui utilise le fichier PHP [getenv()](http://php.net/manual/en/function.getenv.php) en arrière-plan.
    >
    >Cependant, `.env` est ignoré par Git car il est appelé par le fichier `.gitignore` dans le répertoire racine. Pour simplifier, `.env` dans votre référentiel Git local n’est pas placé dans Azure avec le reste des fichiers. Bien sûr, vous pouvez simplement supprimer cette ligne de `.gitignore`, mais comme nous l’avons indiqué, il n’est pas recommandé de valider ce fichier dans le contrôle de code source. Pour autant, il vous faut trouver un moyen de spécifier ces variables d’environnement dans Azure.
    >
    >La bonne nouvelle est que les paramètres d’application dans Azure App Service prennent en charge [getenv()](http://php.net/manual/en/function.getenv.php) dans PHP. Bien que vous puissiez utiliser FTP ou d’autres moyens pour charger manuellement un fichier `.env` dans Azure, vous pouvez simplement spécifier les variables de votre choix en tant que paramètres de l’application Azure sans ajouter de `.env` dans Azure, comme vous venez de faire. En outre, si une variable se trouve à la fois dans un fichier `.env` et dans les paramètres d’application Azure, le paramètre d’application Azure l’emporte.

4. Les deux dernières tâches (configuration du répertoire virtuel et activation du Compositeur) supposent d’utiliser le [portail](https://portal.azure.com). Vous devez donc vous connecter au [portail Azure](https://portal.azure.com) avec votre compte Azure.

4. À partir du menu de gauche, cliquez sur **App Services** > **&lt;nom\_application>** > **Outils**.

    ![Activer le Compositeur pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
    
    >[AZURE.TIP] Si vous cliquez sur **Paramètres** au lieu de l’option **Outils**, vous accéderez au panneau **Paramètres de l’application**, qui vous permet de définir les versions PHP, les paramètres de l’application et les répertoires virtuels, comme vous venez de faire.
    
4. Cliquez sur **Extensions** > **Ajouter** pour ajouter une extension.

4. Sélectionnez **Compositeur** dans le [panneau](../azure-portal-overview.md) **Choisir une extension** (*panneau* : une page de portail s’ouvre à l’horizontale).

4. Cliquez sur **OK** dans le panneau **Accepter les conditions juridiques**.

5. Cliquez sur **OK** dans le panneau **Ajouter une extension**.

    Lorsque Azure a terminé d’ajouter l’extension, un message contextuel doit s’afficher dans l’angle et le **Compositeur** doit normalement être répertorié dans le panneau **Extensions**.

    ![Panneau Extensions après activation du Compositeur pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-composer-end.png)

    Vous avez terminé l’activation de l’éditeur !
    
4. Dans le panneau de votre application web, cliquez sur **Paramètres** > **Paramètres de l’application**.

    ![Accéder au panneau Paramètres pour définir le répertoire virtuel de votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)

    Dans le panneau **Paramètres de l’application**, notez la version de PHP que vous avez définie précédemment :

    ![Version de PHP dans le panneau Paramètres pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a.png)

    et les paramètres d’application que vous avez ajoutés :
    
    ![Paramètres de l’application dans le panneau Paramètres pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)

4. Faites défiler vers le bas du panneau et accédez au répertoire virtuel racine pour pointer vers **site\\wwwroot\\public** au lieu de **site\\wwwroot**.

    ![Définir un répertoire virtuel pour votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)

4. Cliquez sur **Enregistrer** dans la partie supérieure du panneau.

    Vous avez fini de définir le répertoire virtuel.

## Déployer votre application web avec Git (et définir les variables d’environnement)

Vous êtes maintenant prêt à déployer votre code. Pour cela, vous devez revenir à votre invite ou à votre terminal de commandes.

4. Validez toutes vos modifications et déployez votre code dans l’application web Azure, comme vous le feriez dans n’importe quel référentiel Git :

        git add .
        git commit -m "Hurray! My first commit for my Azure app!"
        git push azure master 

    Lors de l’exécution de `git push`, vous devrez fournir le mot de passe de votre déploiement Git. Si vous êtes invité à créer des informations d’identification pour le déploiement à l’étape `azure site create` précédente, entrez le mot de passe que vous avez utilisé.
    
5. Voyons maintenant comment cela se passe au niveau du navigateur en exécutant la commande suivante :

        azure site browse

    Votre navigateur doit afficher l’écran de démarrage Laravel.
    
    ![Écran de démarrage Laravel après le déploiement de l’application web vers Azure](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
    
    Félicitations, votre application web Laravel peut maintenant s’exécuter dans Azure.
             
## Résolution des erreurs courantes

Voici les erreurs que vous pouvez rencontrer dans ce didacticiel :

- [La ligne de commande Azure affiche « 'site' is not an azure command »](#clierror)
- [L’application web affiche l’erreur HTTP 403](#http403)
- [L’application web affiche le message « Whoops, looks like something went wrong. »](#whoops)
- [L’application web affiche le message « No supported encryptor found. »](#encryptor)

<a name="clierror"></a>
### La ligne de commande Azure affiche « 'site' is not an azure command »

Lors de l’exécution de `azure site *` dans le terminal de ligne de commande, l’erreur `error:   'site' is not an azure command. See 'azure help'.` s’affiche.

Cette erreur est généralement liée au passage en mode « ARM » (Azure Resource Manager). Pour résoudre ce problème, revenez en mode « ASM » (Azure Service Management) en exécutant `azure config mode asm`.

<a name="http403"></a>
### L’application web affiche l’erreur HTTP 403

Vous avez réussi à déployer votre application web sur Azure, mais lorsque vous accédez à votre application web Azure, vous obtenez une erreur `HTTP 403` ou `You do not have permission to view this directory or page.`

Cela est probablement dû au fait que l’application web ne peut pas trouver le point d’entrée à l’application Laravel. Assurez-vous que vous avez correctement modifié le répertoire virtuel racine pour pointer vers `site\wwwroot\public`, où se trouve l’élément `index.php` de Laravel (voir la section [Configurer l’application web Azure](#configure)).

<a name="whoops"></a>
### L’application web affiche le message « Whoops, looks like something went wrong. »

Vous avez réussi à déployer votre application web sur Azure, mais lorsque vous accédez à votre application web Azure, vous obtenez le message `Whoops, looks like something went wrong.`.

Pour obtenir une erreur plus explicite, activez le débogage de Laravel en définissant la variable d’environnement `APP_DEBUG` sur `true` (voir [Configurer l’application web Azure](#configure)).

<a name="encryptor"></a>
### L’application web affiche le message « No supported encryptor found. »

Vous avez réussi à déployer votre application web sur Azure, mais lorsque vous accédez à votre application web Azure, vous obtenez le message d’erreur suivant :

![APP\_KEY manquant dans votre application PHP (Laravel) dans Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)
    
Cette erreur est regrettable, mais elle est explicite puisque vous avez activé le débogage Laravel. Après une recherche rapide de la chaîne d’erreur sur les forums Laravel, vous remarquerez qu’elle n’est pas liée à l’absence du paramètre APP\_KEY dans `.env`, ou dans votre cas, à l’absence pure et simple de `.env` dans Azure. Pour résoudre ce problème, ajoutez le paramètre `APP_KEY` en tant que paramètre d’application Azure (voir [Configurer l’application web Azure](#configure)).
    
## Étapes suivantes

Découvrez comment ajouter des données à votre application en [créant une base de données MySQL dans Azure](../store-php-create-mysql-database.md). Vous trouverez également ci-dessous d’autres liens utiles pour le déploiement de PHP dans Azure :

- [Centre de développement PHP](/develop/php/).
- [Créer une application web à partir d’Azure Marketplace](app-service-web-create-web-app-from-marketplace.md)
- [Configurer PHP dans Azure App Service Web Apps](web-sites-php-configure.md)
- [Conversion de WordPress en WordPress multisite dans Azure App Service](web-sites-php-convert-wordpress-multisite.md)
- [WordPress d’entreprise sur Azure App Service](web-sites-php-enterprise-wordpress.md)

<!---HONumber=AcomDC_0608_2016-->