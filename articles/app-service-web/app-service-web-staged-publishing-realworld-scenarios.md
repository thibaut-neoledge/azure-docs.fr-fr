---
title: Utiliser efficacement les environnements DevOps pour votre application web | Microsoft Docs
description: "Apprenez à utiliser les emplacements de déploiement pour configurer et gérer plusieurs environnements de développement pour votre application"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 16a594dc-61f5-4984-b5ca-9d5abc39fb1e
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 385eb87ec32f5f605b28cc8c76b1c89c7e90bfec
ms.openlocfilehash: 5284022ea473db893800b0f64b5bf4f811d994aa


---
# <a name="use-devops-environments-effectively-for-your-web-apps"></a>Utiliser efficacement les environnements DevOps pour vos applications web
Cet article vous montre comment configurer et gérer les déploiements d’application web lorsque plusieurs versions de votre application cohabitent dans différents environnements (conception, intermédiaire, assurance qualité et production). Chaque version de votre application peut être considérée comme un environnement de développement pour les besoins spécifiques de votre processus de déploiement. Par exemple, les développeurs peuvent utiliser l’environnement d’assurance qualité pour tester la qualité de l’application avant de transmettre les modifications à la production.
La gestion de plusieurs environnements de développement peut être un défi, car vous devez assurer le suivi du code, gérer les ressources (calcul, application web, base de données, cache, etc.) et déployer le code sur les environnements.

## <a name="set-up-a-non-production-environment-stage-dev-qa"></a>Configurer un environnement hors production (intermédiaire, développement, assurance qualité)
Une fois votre application web de production installée et fonctionnelle, l’étape suivante consiste à créer un environnement hors production. Pour utiliser les emplacements de déploiement, vérifiez que vous utilisez le mode Standard ou Premium d’Azure App Service. Les emplacements de déploiement sont des applications web dynamiques qui ont leurs propres noms d’hôte. Les éléments de contenu et de configuration des applications web peuvent être échangés entre deux emplacements de déploiement, y compris l’emplacement de production. Le déploiement de votre application sur un emplacement de déploiement offre les avantages suivants :

- Vous pouvez valider les modifications apportées à une application web dans un emplacement de déploiement intermédiaire avant de basculer vers l’emplacement de production.
- Lorsque vous déployez une application web dans un emplacement et que vous la passez en production, toutes les instances de l’emplacement sont initialisées avant d’être basculées en production. Ce processus élimine les temps d’arrêt lorsque vous déployez votre application web. La redirection du trafic est transparente et aucune demande n’est abandonnée en cas d’opération de basculement. Pour automatiser ce flux de travail, configurez [Échange automatique](web-sites-staged-publishing.md#configure-auto-swap) lorsque la validation n’est pas requise avant l’échange.
- Après un échange, l’emplacement auparavant occupé par une application web intermédiaire est maintenant occupé par la précédente application web de production. Si les modifications basculées dans l’emplacement de production sont différentes de celles escomptées, vous pouvez effectuer le même basculement pour restaurer la « dernière application fonctionnelle connue ».

Pour configurer un emplacement de déploiement intermédiaire, consultez [Configuration des environnements intermédiaires pour les applications web dans Azure App Service](web-sites-staged-publishing.md). Chaque environnement doit inclure son propre jeu de ressources. Par exemple, si votre application web utilise une base de données, les applications web intermédiaire et de production doivent utiliser différentes bases de données. Ajoutez des ressources d’environnement de développement intermédiaire, comme une base de données, un espace de stockage ou une mémoire cache pour configurer votre environnement de développement intermédiaire.

## <a name="examples-of-using-multiple-development-environments"></a>Exemples d’utilisation de plusieurs environnements de développement
Un projet doit suivre la gestion du code source avec au moins deux environnements : développement et production. Si vous utilisez des systèmes de gestion de contenus (CMS), des infrastructures d’application, etc., l’application risque de ne pas prendre en charge ce scénario sans personnalisation. Cette éventualité est possible pour certaines des infrastructures courantes qui sont abordées dans les sections suivantes. Beaucoup de questions viennent à l’esprit lorsqu’on travaille avec un service de gestion de contenus (CMS) ou des infrastructures, par exemple :

- Comment répartissez-vous les contenus entre les différents environnements ?
- Quels fichiers pouvez-vous modifier sans affecter les mises à jour des versions d’infrastructure ?
- Comment gérez-vous les configurations par environnement ?
- Comment gérez-vous les mises à jour des versions des modules, des modules complémentaires et de l’infrastructure de base ?

Différentes solutions permettent de configurer plusieurs environnements pour votre projet. Les exemples suivants décrivent une méthode pour chaque application.

### <a name="wordpress"></a>WordPress
Dans cette section, vous allez apprendre à configurer un flux de travail de déploiement à l’aide d’emplacements pour WordPress. WordPress, comme la plupart des solutions CMS, ne prend pas en charge l’utilisation de plusieurs environnements de développement sans personnalisation. La fonctionnalité Web Apps d’Azure App Service offre quelques fonctions qui facilitent le stockage des paramètres de configuration hors du code.

1. Avant de créer un emplacement intermédiaire, configurez le code de votre application pour qu’il prenne en charge plusieurs environnements. Pour prendre en charge plusieurs environnements dans WordPress, vous devez modifier `wp-config.php` dans votre application web de développement locale et ajouter le code suivant au début du fichier. Cela permet à votre application de sélectionner la configuration correcte en fonction de l’environnement sélectionné.

    ```
    // Support multiple environments
    // set the config file based on current environment
    if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
     $config_file = 'config/wp-config.local.php';
    }
    elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
    //single file for all azure development environments
     $config_file = 'config/wp-config.azure.php';
    }
    $path = dirname(__FILE__). '/';
    if (file_exists($path. $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path. $config_file;
    ```

2. Créez un dossier sous la racine de l’application web, appelé `config`, et ajoutez-y deux fichiers (`wp-config.azure.php` et `wp-config.local.php`) qui représentent respectivement votre environnement Azure et votre environnement local.

3. Copiez ce qui suit dans `wp-config.local.php` :

    ```
    <?php
    // MySQL settings
    /** The name of the database for WordPress */

    define('DB_NAME', 'yourdatabasename');

    /** MySQL database username */
    define('DB_USER', 'yourdbuser');

    /** MySQL database password */
    define('DB_PASSWORD', 'yourpassword');

    /** MySQL hostname */
    define('DB_HOST', 'localhost');
    /**
     * For developers: WordPress debugging mode.
     * * Change this to true to enable the display of notices during development.
     * It is strongly recommended that plugin and theme developers use WP_DEBUG
     * in their development environments.
     */
    define('WP_DEBUG', true);

    //Security key settings
    define('AUTH_KEY', 'put your unique phrase here');
    define('SECURE_AUTH_KEY','put your unique phrase here');
    define('LOGGED_IN_KEY','put your unique phrase here');
    define('NONCE_KEY', 'put your unique phrase here');
    define('AUTH_SALT', 'put your unique phrase here');
    define('SECURE_AUTH_SALT', 'put your unique phrase here');
    define('LOGGED_IN_SALT', 'put your unique phrase here');
    define('NONCE_SALT', 'put your unique phrase here');

    /**
     * WordPress Database Table prefix.
     *
     * You can have multiple installations in one database if you give each a unique
     * prefix. Only numbers, letters, and underscores please!
     */
    $table_prefix = 'wp_';
    ```

    La définition des clés de sécurité comme dans le code précédent permet d’empêcher le piratage de votre application web. Alors, utilisez des valeurs uniques. Si vous devez générer la chaîne des clés de sécurité mentionnées dans le code ci-dessus, [accédez au générateur automatique](https://api.wordpress.org/secret-key/1.1/salt) pour créer des paires clé/valeur.

4. Copiez le code suivant dans `wp-config.azure.php`:

    ```    
    <?php
    // MySQL settings
    /** The name of the database for WordPress */

    define('DB_NAME', getenv('DB_NAME'));

    /** MySQL database username */
    define('DB_USER', getenv('DB_USER'));

    /** MySQL database password */
    define('DB_PASSWORD', getenv('DB_PASSWORD'));

    /** MySQL hostname */
    define('DB_HOST', getenv('DB_HOST'));

    /**
    * For developers: WordPress debugging mode.
    *
    * Change this to true to enable the display of notices during development.
    * It is strongly recommended that plugin and theme developers use WP_DEBUG
    * in their development environments.
    * Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
    * do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
    * with WP_DEBUG_LOG so that errors are not displayed on the page */

    */
    define('WP_DEBUG', getenv('WP_DEBUG'));
    define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
    define('WP_DEBUG_DISPLAY',false);

    //Security key settings
    /** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));

    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
```

#### <a name="use-relative-paths"></a>Utiliser des chemins relatifs
Les chemins d’accès relatifs sont le dernier élément à configurer dans l’application WordPress. WordPress stocke les informations d’URL dans la base de données. Ce stockage complique le déplacement de contenus d’un environnement vers un autre. Vous devez mettre à jour la base de données chaque fois que vous passez de l’environnement local à l’environnement intermédiaire ou de l’environnement intermédiaire à l’environnement de production. Pour réduire les risques de problèmes liés au déploiement d’une base de données à chaque déploiement d’un environnement dans un autre, utilisez le module complémentaire [Relative Root URLs](https://wordpress.org/plugins/root-relative-urls/) que vous pouvez installer à l’aide du tableau de bord de l’administrateur WordPress.

Ajoutez les entrées suivantes à votre fichier `wp-config.php` avant le commentaire `That's all, stop editing!` :

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Activez le module complémentaire grâce au menu `Plugins` dans le tableau de bord de l’administrateur WordPress. Enregistrez les paramètres permalink pour l’application WordPress.

#### <a name="the-final-wp-configphp-file"></a>Le fichier `wp-config.php` final
Les mises à jour de WordPress n’affecteront pas vos fichiers `wp-config.php`, `wp-config.azure.php` et `wp-config.local.php`. Voici une version finale du fichier `wp-config.php` :

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
  $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>Configuration d’un environnement intermédiaire
1. Si vous exécutez une application web WordPress dans votre abonnement Azure, connectez-vous au [portail Azure](http://portal.azure.com) et accédez à votre application web WordPress. Si vous n’avez pas une application web WordPress, vous pouvez en créer une dans Azure Marketplace. Pour en savoir plus , consultez [Créer une application web WordPress dans Azure App Service](web-sites-php-web-site-gallery.md).
Cliquez sur **Paramètres** > **Emplacements de déploiement** > **Ajouter** pour créer un emplacement de déploiement nommé *stage*. Un emplacement de déploiement est une autre application web qui partage les ressources que l’application web primaire créée auparavant.

    ![Créer un emplacement de déploiement intermédiaire](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

2. Ajoutez une autre base de données MySQL, `wordpress-stage-db`, à votre groupe de ressources `wordpressapp-group`.

    ![Ajouter la base de données MySQL au groupe ressources](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

3. Mettez à jour les chaînes de connexion de votre emplacement de déploiement intermédiaire pour qu’elles pointent vers la base de données créée (`wordpress-stage-db`). Votre application web de production (`wordpressprodapp`) et l’application web intermédiaire (`wordpressprodapp-stage`) doivent pointer vers des bases de données différentes.

#### <a name="configure-environment-specific-app-settings"></a>Configurer les paramètres d’application spécifiques à l’environnement
Les développeurs peuvent stocker des paires clé/valeur dans Azure en tant qu’informations de configuration appelées **Paramètres de l’application** et associées à une application web. Lors de l’exécution, les applications web récupèrent automatiquement ces valeurs et les transmettent au code exécuté dans votre application web. Du point de vue de la sécurité, c’est un avantage car les informations sensibles comme les chaînes de connexion à une base de données qui incluent des mots de passe, ne sont jamais affichées en clair dans un fichier tel que `wp-config.php`.

Ce processus, qui est décrit dans les paragraphes suivants, est utile car il inclut les modifications apportées au fichier et à la base de données pour l’application WordPress :

* Mise à jour de la version de WordPress
* Ajout, modification ou mise à jour de modules complémentaires
* Ajout, modification ou mise à jour des thèmes

Configuration des paramètres d’application pour :

* les informations de base de données
* l’activation/la désactivation de la journalisation WordPress
* Paramètres de sécurité WordPress

![Paramètres de l’application pour l’application web WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Veillez à ajouter les paramètres d’application suivants à votre application web de production et à votre emplacement intermédiaire. Notez que l’application web de production et l’application web intermédiaire utilisent des bases de données différentes.

1. Désactivez la case à cocher **Paramètre d’emplacement** pour tous les paramètres, sauf WP_ENV. Vous échangez ainsi la configuration de votre application web, ainsi que le contenu du fichier et la base de données. Si la cache à cocher **Paramètre d’emplacement** est activée, les paramètres et la chaîne de connexion de l’application web *ne sont pas* déplacés d’un environnement à l’autre lors d’une opération d’**échange**. Les modifications apportées à la base de données ne bloqueront pas votre application web de production.

2. Déployez l’application web de l’environnement de développement local vers l’application web intermédiaire et la base de données à l’aide de WebMatrix ou des outils de votre choix comme FTP, Git ou PhpMyAdmin.

    ![Boîte de dialogue de publication de Matrix pour l’application web WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

3. Parcourir et tester votre application web intermédiaire. Si l’on prend en compte un scénario dans lequel le thème de l’application web doit être mis à jour, voici l’application web intermédiaire.

    ![Parcourir l’application web intermédiaire avant d’échanger les emplacements](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)

4. Si tout vous semble correct, cliquez sur le bouton **Échange** dans votre application web intermédiaire pour déplacer vos contenus vers l’environnement de production. Dans ce cas, vous échangez l’application web et la base de données d’un environnement à l’autre pendant chaque opération d’**échange**.

    ![Échanger les modifications de l’aperçu pour WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

    > [!NOTE]
    > Si votre scénario ne nécessite que de transmettre des fichiers (aucune mise à jour de la base de données), vérifiez le **Paramètre d’emplacement** de tous les *paramètres d’application* liés à la base de données et les *Paramètres des chaînes de connexion* dans le panneau **Paramètres de l’application web** du portail Azure avant d’effectuer l’**échange**. Dans ce cas, DB_NAME, DB_HOST, DB_PASSWORD, DB_USER et les paramètres de la chaîne de connexion par défaut ne s’affichent pas dans les modifications de présentation lors de l’**échange**. À ce stade, lorsque vous terminez l’opération d’**échange**, l’application web WordPress n’obtient que les fichiers des mises à jour.
    >
    >

    Avant de procéder à l’**échange**, voici l’application web WordPress de production.
    ![Application web de production avant l’échange d’emplacements](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

    Après l’opération d’**échange**, le thème a été mis à jour sur votre application web de production.

    ![Application web de production après l’échange d’emplacements](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

5. Pour procéder à une restauration, accédez aux **paramètres de l’application** web de production, puis cliquez sur le bouton **Échange** pour basculer l’application web et la base de données de l’emplacement de production vers l’emplacement intermédiaire. N’oubliez pas que, si les modifications de la base de données sont incluses dans une opération d’**échange**, lors du prochain déploiement de votre application web intermédiaire, vous devrez déployer les modifications de la base de données dans la base de données actuelle de votre application web intermédiaire. La base de données actuelle peut être la base de données de production précédente ou la base de données intermédiaire.

#### <a name="summary"></a>Résumé
Voici un processus générique pour une application qui possède une base de données :

1. Installez l’application dans votre environnement local.
2. Incluez les configurations propres à l’environnement (local et Azure Web Apps).
3. Configurez vos environnements intermédiaire et de production pour Web Apps.
4. Si une application de production est déjà en cours d’exécution sur Azure, synchronisez votre contenu de production (fichiers/code et base de données) avec les environnements local et intermédiaire.
5. Développez votre application sur votre environnement local.
6. Placez votre application web de production en mode maintenance ou verrouillé, synchronisez le contenu de la base de données de l’environnement de production avec celui des environnements intermédiaire et de développement.
7. Effectuez un déploiement dans l’environnement intermédiaire et testez-le.
8. Effectuez un déploiement dans l’environnement de production.
9. Répétez les étapes 4 à 6.

### <a name="umbraco"></a>Umbraco
Dans cette section, vous allez apprendre comment Umbraco CMS utilise un module personnalisé pour effectuer un déploiement sur plusieurs environnements DevOps. Cet exemple offre une approche différente de la gestion de plusieurs environnements de développement.

[Umbraco CMS](http://umbraco.com/) est une solution de CMS .NET prisée de nombreux développeurs. Son module [Courier2](http://umbraco.com/products/more-add-ons/courier-2) permet d’effectuer un déploiement de l’environnement de production vers l’environnement intermédiaire puis vers l’environnement de production. Vous pouvez facilement créer un environnement de développement local pour une application web Umbraco CMS à l’aide de Visual Studio ou de WebMatrix.

- [Créer une application web Umbraco avec Visual Studio](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget)
- [Créer une application web Umbraco avec WebMatrix](http://umbraco.tv/videos/umbraco-v7/implementor/fundamentals/installation/creating-umbraco-site-from-webmatrix-web-gallery/)

N’oubliez pas de supprimer le dossier `install` dans votre application et ne le chargez jamais dans les applications web intermédiaire ou de production. Ce didacticiel utilise WebMatrix.

#### <a name="set-up-a-staging-environment"></a>Configuration d’un environnement intermédiaire
1. Créez un emplacement de déploiement, comme indiqué précédemment, pour l’application web Umbraco CMS. Si vous n’avez pas une telle application fonctionnelle, créez-la à l’aide de Marketplace.
2. Mettez à jour la chaîne de connexion de votre emplacement de déploiement intermédiaire pour qu’elle pointe vers la nouvelle base de données **umbraco-stage-db**. Votre application web de production (umbraositecms-1) et l’application web intermédiaire (umbracositecms-1-stage) *doivent* pointer vers des bases de données différentes.

    ![Mettre à jour la chaîne de connexion pour l’application web intermédiaire vers une nouvelle base de données intermédiaire](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

3. Cliquez sur **Accéder aux paramètres de publication** de l’emplacement de déploiement **stage**. Cette opération télécharge un fichier de paramètres de publication, qui stocke toutes les informations nécessaires à Visual Studio ou WebMatrix pour publier votre application à partir de l’application web de développement locale sur l’application Web Azure.

    ![Obtenir le paramètre de publication de l’application web intermédiaire de publication](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)
4. Ouvrez votre application web de développement locale dans WebMatrix ou Visual Studio. Ce didacticiel utilise WebMatrix. Tout d’abord, vous devez importer le fichier des paramètres de publication de votre application web intermédiaire.

    ![Importer les paramètres de publication d’Umbraco à l’aide de Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

5. Vérifiez les modifications dans la boîte de dialogue et déployez votre application web locale dans votre application web Azure, *umbracositecms-1-stage*. Lorsque vous déployez des fichiers directement dans votre application web intermédiaire, vous pouvez omettre les fichiers dans le dossier `~/app_data/TEMP/`, car ils seront régénérés lors du premier démarrage de l’application intermédiaire. Vous pouvez également omettre le fichier `~/app_data/umbraco.config`, qui sera également régénéré.

    ![Passer en revue les modifications de publication dans une matrice web](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

6. Après la publication de l’application web locale Umbraco dans l’application web intermédiaire, accédez à votre application web intermédiaire et effectuez quelques tests pour éliminer les éventuels problèmes.

#### <a name="set-up-the-courier2-deployment-module"></a>Configurer le module de déploiement Courier2
Avec le module [Courier2](http://umbraco.com/products/more-add-ons/courier-2), il vous suffit de cliquer avec le bouton droit pour transmettre du contenu, les feuilles de style et les modules de développement d’une application web intermédiaire vers une application web de production. Ce processus réduit le risque de blocage de votre application web de production lorsque vous déployez une mise à jour.
Achetez une licence Courier2 pour le domaine `*.azurewebsites.net` et votre domaine personnalisé (par exemple http://abc.com). Après avoir acheté la licence, placez la licence téléchargée (fichier .lic) dans le dossier `bin`.

![Placer un fichier de licence sous un dossier bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

1. [Téléchargez le package Courier2](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Connectez-vous à votre application web intermédiaire (http://umbracocms-site-stage.azurewebsites.net/umbraco), cliquez sur le menu **Développeur**, puis sur **Packages** > **Install local package (Installer le package local)**.

    ![Programme d’installation de Umbraco Package](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

2. Téléchargez le package Courier2 en utilisant le programme d’installation.

    ![Téléchargement du package du module courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

3. Pour configurer le package, vous devez mettre à jour le fichier courier.config dans le dossier **Config** de votre application web.

    ```xml
    <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1.azurewebsites.net</url>
          <user>0</user>
          <!--<login>user@email.com</login> -->
          <!-- <password>user_password</password>-->
          <!-- <passwordEncoding>Clear</passwordEncoding>-->
          </repository>
     </repositories>
     ```

4. Sous `<repositories>`, entrez l'URL du site de production et les informations utilisateur.
    Si vous utilisez le fournisseur d’appartenances Umbraco par défaut, ajoutez l’ID de l’utilisateur Administration dans la section &lt;user&gt;.
    Si vous utilisez un fournisseur d’appartenances Umbraco personnalisé, utilisez `<login>`,`<password>` dans le module Courier2 pour vous connecter au site de production.
    Pour plus d’informations, [consultez la documentation du module Courier2](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation).

5. De même, installez le module Courier2 sur votre site de production et configurez-le pour qu’il pointe vers l’application web intermédiaire dans son fichier courier.config concerné, comme indiqué ici.

    ```xml
     <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1-stage.azurewebsites.net</url>
          <user>0</user>
          </repository>
     </repositories>
    ```

6. Cliquez sur l’onglet **Courier2** dans le tableau de bord de l’application web Umbraco CMS, puis cliquez sur **Emplacements**. Vous devez voir le nom du référentiel mentionné dans `courier.config`. Appliquez cette procédure à vos applications web de production et intermédiaire.

    ![Affichage du référentiel de l’application web](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

7. Pour déployer du contenu du site intermédiaire vers le site de production, accédez à **Contenu** et sélectionnez une page ou créez-en une. Sélectionnez une page de l’application web dont le titre est **Getting Started – new**), puis cliquez sur **Enregistrer et publier**.

    ![Modification du titre de page et publication](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

8. Avec le bouton droit, cliquez sur la page modifiée pour afficher toutes les options. Cliquez sur **Courier** pour ouvrir la boîte de dialogue **Déploiement**. Cliquez sur **Déployer** pour lancer le déploiement.

    ![Boîte de dialogue déploiement de module Courrier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

9. Passez en revue les modifications, puis cliquez sur **Continuer**.

    ![Passage en revue des modifications via la boîte de dialogue de déploiement du module Courier](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

    Le journal de déploiement indique si le déploiement a réussi.

     ![Affichage des journaux de déploiement du module Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

10. Parcourez votre application web de production pour voir si les modifications sont prises en compte.

     ![Accès à l’application web de production](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Pour en savoir plus sur la façon d’utiliser Courier, consultez la documentation associée.

#### <a name="how-to-upgrade-the-umbraco-cms-version"></a>Comment mettre à niveau la version d’Umbraco CMS
Courier ne permet pas de mettre Umbraco CMS au niveau d’une autre version. Lors de la mise à niveau de la version Umbraco CMS, vous devez rechercher les incompatibilités avec vos modules personnalisés ou des modules de partenaires et les bibliothèques principales Umbraco. Voici les meilleures pratiques :

* Sauvegardez toujours votre application web et votre base de données avant de procéder une mise à niveau. Dans les applications web sur Azure, vous pouvez configurer les sauvegardes automatiques de vos sites web grâce à la fonction de sauvegarde, et restaurer votre site si nécessaire à l’aide de la fonctionnalité de restauration. Pour plus d'informations, consultez [Sauvegarde de votre application web](web-sites-backup.md) et [Restauration de votre application web](web-sites-restore.md).
* Vérifiez si les packages de partenaires que vous utilisez sont compatibles avec la version vers laquelle vous effectuez la mise à niveau. Dans la page de téléchargement du package, vérifiez la compatibilité du projet avec la version d’Umbraco CMS.

Pour plus d’informations sur la mise à niveau de votre application web localement, [consultez les instructions générales de mise à niveau](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

Une fois la mise à niveau de votre site de développement local terminée, publiez les modifications dans l’application web intermédiaire. Testez votre application. Si tout vous semble correct, utilisez le bouton **Échange** pour basculer votre site intermédiaire vers l’application web de production. Lorsque vous effectuez l’**échange**, vous visualisez les modifications qui seront prises en compte dans la configuration de votre application web. L’opération d’**échange** bascule les applications web et les bases de données. Après l’**échange**, l’application web de production pointera vers la base de données umbraco-stage-db, et l’application web intermédiaire pointera vers la base de données umbraco-prod-db.

![Vue d’ensemble de l’échange pour le déploiement de CMS Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Voici les avantages de l’échange de l’application web et de la base de données :

* Vous pouvez restaurer la version précédente de votre application web grâce à un autre **échange** en cas de problèmes avec l’application.
* Pour une mise à niveau, vous devez déployer les fichiers et les bases de données de l’application web intermédiaire dans l’application web et la base de données de production. Beaucoup de choses peuvent aller de travers lorsque vous déployez des fichiers et des bases de données. La fonction d’**échange** d’emplacements permet de réduire l’indisponibilité pendant une mise à niveau, ainsi que les risques de panne lors du déploiement des modifications.
* Vous pouvez effectuer un **test A/B** à l’aide de la fonctionnalité de [test en production](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

Cet exemple montre la flexibilité de la plateforme sur laquelle vous pouvez élaborer des modules personnalisés similaires au module Umbraco Courier pour gérer le déploiement sur les environnements.

## <a name="references"></a>Références
[Développement logiciel agile avec Azure App Service](app-service-agile-software-development.md)

[Configurer des environnements intermédiaires pour les applications web dans Azure App Service](web-sites-staged-publishing.md)

[Comment limiter l’accès web aux emplacements de déploiement hors production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)



<!--HONumber=Feb17_HO3-->


