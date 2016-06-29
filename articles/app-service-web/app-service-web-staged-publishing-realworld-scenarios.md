<properties
   pageTitle="Utiliser efficacement les environnements DevOps pour votre application web"
   description="Apprenez à utiliser les emplacements de déploiement pour configurer et gérer plusieurs environnements de développement pour votre application"
   services="app-service\web"
   documentationCenter=""
   authors="sunbuild"
   manager="yochayk"
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web"
   ms.date="05/31/2016"
   ms.author="sumuth"/>

# Utiliser efficacement les environnements DevOps pour vos applications web

Cet article vous montre comment configurer et gérer les déploiements d’applications web de plusieurs versions, et notamment, les versions de développement, intermédiaire, d’assurance qualité et de production votre application. Chaque version de votre application peut être considérée comme un environnement de développement répondant à un besoin spécifique au sein de votre processus de déploiement, par exemple, votre équipe de développeurs peut utiliser l’environnement d’assurance qualité pour tester la qualité de l’application avant de transmettre les modifications apportées à la production. 
La configuration de plusieurs environnements de développement peut être une tâche complexe, car vous devez assurer le suivi et la gestion des ressources (calcul, application web, base de données, cache, etc.) sur ces différents environnements et déployer le contenu d’un environnement sur l’autre.

## Configuration d’un environnement hors production (intermédiaire, développement, assurance qualité)
Une fois que votre application web de production est installée et fonctionne, l’étape suivante consiste à créer un environnement hors production. Pour utiliser les emplacements de déploiement, assurez-vous que vous fonctionnez bien en mode plan App Service **Standard** ou **Premium**. Les emplacements de déploiement sont en fait des applications web dynamiques pourvues de leur propre nom d’hôte. Les éléments de contenu et de configuration des applications web peuvent être échangés entre deux emplacements de déploiement, y compris l’emplacement de production. Le déploiement de votre application sur un emplacement de déploiement présente les avantages suivants :

1. Vous pouvez valider les modifications d’une application web dans un emplacement de déploiement intermédiaire avant de l’échanger avec l’emplacement de production.
2. Déployer d’abord une application web vers un emplacement et la basculer ensuite en production garantit que toutes les instances de l’emplacement sont initialisées avant d’être basculées en production. Cela élimine les temps d’arrêt lorsque vous déployez votre application web. La redirection du trafic est transparente et aucune demande n’est abandonnée en cas d’opération de basculement. Ce flux de travail peut être entièrement automatisé en configurant [Échange automatique](web-sites-staged-publishing.md#configure-auto-swap-for-your-web-app) lorsqu’aucune validation n’est requise avant l’échange.
3. À l’issue d’un échange, l’emplacement occupé par une application web précédemment intermédiaire dispose désormais de l’application web précédemment en production. Si les modifications basculées sur l’emplacement de production n’ont pas les résultats escomptés, vous pouvez effectuer un basculement équivalent pour récupérer la « dernière application correcte connue ».

Pour configurer un emplacement de déploiement intermédiaire, consultez [Configuration des environnements intermédiaires pour les applications web dans Azure App Service](web-sites-staged-publishing.md). Chaque environnement doit inclure son propre ensemble de ressources, par exemple, si votre application web utilise une base de données, les applications web de production et intermédiaire doivent toutes les deux utiliser des bases de données distinctes. Ajoutez des ressources d’environnement de développement intermédiaire telles qu’une base de données, un espace de stockage ou une mémoire cache pour la configuration de votre environnement de développement intermédiaire.

## Exemples d’utilisation de plusieurs environnements de développement

N’importe quel projet doit respecter la gestion du code source dans au moins deux environnements (un environnement de développement et de production), mais lorsque vous utilisez des systèmes de gestion des contenus, des structures d’application etc., si l’application ne prend pas en charge le scénario dès le départ, vous pouvez rencontrer des problèmes. Cette affirmation est vraie pour certains des frameworks populaires décrits ci-dessous. Un grand nombre de questions viennent à l’esprit lorsqu’on travaille avec un serveur de gestion des contenus (CMS)/des frameworks, par exemple

1. Comment assurer la distribution dans les différents environnements
2. Quels sont les fichiers que je peux modifier sans que les mises à jour de la version de framework soient impactées
3. Comment gérer la configuration par environnement
4. Comment gérer les mises à jour de version de module/des compléments, les mises à jour de version de framework de base

Il existe de nombreuses façons de configurer un environnement multiple pour votre projet et les exemples ci-dessous ne représentent qu’une des méthodes d’application respectives.

### WordPress
Dans cette section, vous allez apprendre comment configurer un flux de travail de déploiement à l’aide d’emplacements pour WordPress. WordPress, comme la plupart des solutions CMS, ne prend pas en charge l’utilisation de plusieurs environnements de développement dès le départ. App Service Web Apps est doté de certaines fonctions qui facilitent le stockage des paramètres de configuration hors du code.

Avant de créer un emplacement intermédiaire, configurez votre code d’application pour prendre en charge plusieurs environnements. Pour prendre en charge plusieurs environnements dans WordPress, vous devez modifier `wp-config.php` sur votre application web de développement local, et ajouter le code suivant au début du fichier. Cela permettra à votre application de sélectionner la configuration correcte en prenant en compte l’environnement sélectionné.


	// Support multiple environments
	// set the config file based on current environment
	/**/
	if (strpos(filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING),'localhost') !== false) {
	    // local development
	    $config_file = 'config/wp-config.local.php';
	}
	elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
	      //single file for all azure development environments
	      $config_file = 'config/wp-config.azure.php';
	}
	$path = dirname(__FILE__) . '/';
	if (file_exists($path . $config_file)) {
	    // include the config file if it exists, otherwise WP is going to fail
	    require_once $path . $config_file;
	}



Créez un dossier sous la racine de l’application web appelée `config` et ajoutez deux fichiers : `wp-config.azure.php` et `wp-config.local.php`, qui représentent respectivement votre environnement azure et à votre environnement local.

Copiez ce qui suit dans `wp-config.local.php` :

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
	define('AUTH_KEY',         'put your unique phrase here');
	define('SECURE_AUTH_KEY',  'put your unique phrase here');
	define('LOGGED_IN_KEY',    'put your unique phrase here');
	define('NONCE_KEY',        'put your unique phrase here');
	define('AUTH_SALT',        'put your unique phrase here');
	define('SECURE_AUTH_SALT', 'put your unique phrase here');
	define('LOGGED_IN_SALT',   'put your unique phrase here');
	define('NONCE_SALT',       'put your unique phrase here');
	
	/**
	 * WordPress Database Table prefix.
	 *
	 * You can have multiple installations in one database if you give each a unique
	 * prefix. Only numbers, letters, and underscores please!
	 */
	$table_prefix  = 'wp_';
```

la définition des clés de sécurité ci-dessus peut empêcher le piratage de votre application web. Alors, utilisez des valeurs uniques. Si vous devez générer la chaîne des clés de sécurité mentionnée ci-dessus, vous pouvez accéder au générateur automatique pour créer de nouvelles clés/valeurs en utilisant ce [lien](https://api.wordpress.org/secret-key/1.1/salt)

Copiez le code suivant dans `wp-config.azure.php` :


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
	define('AUTH_KEY' ,getenv('DB_AUTH_KEY'));
	define('SECURE_AUTH_KEY',  getenv('DB_SECURE_AUTH_KEY'));
	define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
	define('NONCE_KEY', getenv('DB_NONCE_KEY'));
	define('AUTH_SALT',  getenv('DB_AUTH_SALT'));
	define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
	define('LOGGED_IN_SALT',   getenv('DB_LOGGED_IN_SALT'));
	define('NONCE_SALT',   getenv('DB_NONCE_SALT'));
	
	/**
	* WordPress Database Table prefix.
	*
	* You can have multiple installations in one database if you give each a unique
	* prefix. Only numbers, letters, and underscores please!
	*/
	$table_prefix  = getenv('DB_PREFIX');
```

#### Utiliser des chemins relatifs
Enfin, il s’agit de permettre à l’application WordPress d’utiliser des chemins d’accès relatifs. WordPress stocke les informations d’URL dans la base de données. Le déplacement du contenu d’un environnement vers un autre devient alors plus difficile, car vous devez mettre à jour la base de données à chaque fois que vous passez d’un environnement local à un environnement intermédiaire ou d’un emplacement intermédiaire à des environnements de production. Pour réduire les risques de problèmes liés au déploiement d’une base de données à chaque déploiement d’un environnement à l’autre, utilisez le [complément de liens à la racine relative](https://wordpress.org/plugins/root-relative-urls/) qui peut être installé à l’aide du tableau de bord Administrateur WordPress ou effectuez un téléchargement manuel de ce dernier à partir d’[ici](https://downloads.wordpress.org/plugin/root-relative-urls.zip).

Ajoutez les entrées suivantes à votre fichier `wp-config.php` avant le commentaire `That's all, stop editing!` :

```

    define('WP_HOME', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_SITEURL', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_CONTENT_URL', '/wp-content');
	define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Activez le module complémentaire grâce au menu `Plugins` dans le tableau de bord WordPress Administrator. Enregistrez les paramètres permalink pour l’application WordPress.

#### Le fichier `wp-config.php` final
Les mises à jour WordPress Core n’affecteront pas vos fichiers `wp-config.php`, `wp-config.azure.php` et `wp-config.local.php`. À la fin de cette procédure, le fichier `wp-config.php` se présentera comme suit :

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
	elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
	    $config_file = 'config/wp-config.azure.php';
	}
	
	
	$path = dirname(__FILE__) . '/';
	if (file_exists($path . $config_file)) {
	    // include the config file if it exists, otherwise WP is going to fail
	    require_once $path . $config_file;
	}
	
	/** Database Charset to use in creating database tables. */
	define('DB_CHARSET', 'utf8');
	
	/** The Database Collate type. Don't change this if in doubt. */
	define('DB_COLLATE', '');
	
	
	/* That's all, stop editing! Happy blogging. */
	
	define('WP_HOME', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_SITEURL', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_CONTENT_URL', '/wp-content');
	define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	
	/** Absolute path to the WordPress directory. */
	if ( !defined('ABSPATH') )
		define('ABSPATH', dirname(__FILE__) . '/');
	
	/** Sets up WordPress vars and included files. */
	require_once(ABSPATH . 'wp-settings.php');
```

#### Configuration d’un environnement intermédiaire
Supposons que vous avez déjà une application web WordPress en cours d’exécution sur Azure Web. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre application web WordPress. Dans le cas contraire, vous pouvez en créer une dans marketplace. Cliquez [ici](web-sites-php-web-site-gallery.md) pour en savoir plus. 
Cliquez sur **Paramètres** -> **Emplacements de déploiement** -> **Ajouter** pour créer un emplacement de déploiement avec la copie intermédiaire. Un emplacement de déploiement est une autre application web qui partage les ressources de l’application web primaire créée plus haut.

![Créer un emplacement de déploiement intermédiaire](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

Ajoutez une autre base de données MySQL, `wordpress-stage-db`, à votre groupe de ressources `wordpressapp-group`.

 ![Ajouter la base de données MySQL au groupe ressources](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

Mettez à jour les chaînes de connexion de votre emplacement de déploiement intermédiaire pour qu’elles pointent vers la base de données nouvellement créée, `wordpress-stage-db`. Notez que votre application web de production `wordpressprodapp` et l’application web intermédiaire `wordpressprodapp-stage` doivent référencer des bases de données différentes.

#### Configurer les paramètres d’application spécifiques à l’environnement
Les développeurs peuvent stocker des paires clés de valeur dans Azure en tant qu’informations de configuration associées à une application web appelée Paramètres de l’application. Lors de l’exécution, App Service Web Apps récupère automatiquement ces valeurs pour vous et les met à la disposition du code exécuté dans votre application web. Du point de vue de la sécurité, il s’agit d’un avantage, car les informations sensibles telles que les chaînes de connexion de base de données avec des mots de passe ne sont jamais affichées sous forme de texte en clair dans un fichier tel que `wp-config.php`.

La procédure définie ci-dessous est utile lorsque vous exécutez des mises à jour, car elle inclut à la fois les modifications de fichier et les modifications de base de données pour une application WordPress :

- Mise à jour de la version de WordPress
- Ajout, modification ou mise à jour d’un module complémentaire
- Ajout, modification ou mise à jour des thèmes

Configuration des paramètres d’application pour :

- les informations de base de données
- l’activation/Désactivation de la journalisation WordPress
- Paramètres de sécurité WordPress

![Paramètres de l’application pour l’application web WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Assurez-vous que vous avez ajouté les paramètres d’application suivants pour votre application web de production et votre emplacement intermédiaire. Notez que l’application web de production et l’application web intermédiaire doivent utiliser des bases de données différentes. Désactivez la case à cocher **Définitions d’emplacement** pour tous les paramètres, sauf WP\_ENV. Vous remplacerez ainsi la configuration de votre application web, ainsi que le contenu du fichier et de la base de données. Si l’option **Paramètre d’emplacement** est **cochée** , les paramètres d’application, les chaînes de connexion de l’application Web NE se déplacent PAS d’un environnement à l’autre au moment d’effectuer une opération SWAP et donc, s’il existe des changements de base de données, ils n’affecteront pas votre application web de production.

Déployez l’application web d’environnement de développement local vers l’application web et la base de données intermédiaires à l’aide de WebMatrix ou des outils de votre choix tels que FTP, Git ou PhpMyAdmin.

![Boîte de dialogue de publication de Matrix pour l’application web WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

Parcourir et tester votre application web intermédiaire. Si l’on prend en compte un scénario dans lequel le thème de l’application web doit être mis à jour, voici l’application web intermédiaire.

![Parcourir l’application web intermédiaire avant d’échanger les emplacements](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 Si tout vous semble correct, cliquez sur le bouton **Échange** de l’application web intermédiaire pour déplacer le contenu de votre environnement de production. Dans ce cas, vous échangez l’application web et la base de données d’un environnement à l’autre pendant chaque opération d’**échange**.

![Échanger les modifications de l’aperçu pour WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 > [AZURE.NOTE]
 Si, pour votre scénario, vous n’avez besoin que de fichiers Push (sans mise à jour de base de données), **vérifiez** le **Paramètre d’emplacement** de tous les *paramètres d’application* et les *paramètres de chaînes de connexion* associés à la base de données dans le panneau de paramètre d’application web au sein du portail Azure avant de procéder à l’échange. Dans ce cas DB\_NAME, DB\_HOST, DB\_PASSWORD, DB\_USER, le paramètre de chaîne de connexion par défaut ne s’affichent pas dans les modifications de présentation au moment de procéder à l’**échange**. À ce stade, lorsque vous terminez l’opération d’**échange**, l’application web WordPress obtient **UNIQUEMENT** les fichiers mis à jour.

Avant de procéder à l’échange, voici l’application web de production WordPress ![Application web de production avant l’échange d’emplacements](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

Après l’opération d’échange, le thème a été mis à jour sur votre application web de production.

![Application web de production après l’échange d’emplacements](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

Si vous devez procéder à une **restauration**, vous pouvez accéder aux paramètres de l’application web de production, puis cliquer sur le bouton **Échange** pour basculer l’application web et la base de données de l’emplacement de production vers l’emplacement intermédiaire. Il est important de retenir que, si des modifications de base de données sont incluses dans une opération d’**échange** à un moment donné, lors du redéploiement de l’application web intermédiaire suivant, vous devez déployer les modifications de la base de données à la base de données actuelle pour votre application web intermédiaire qui peut être la base de données de production ou la base de données intermédiaire précédente.

#### Résumé
Pour généraliser le processus pour une application avec base de données

1. Installer l’application dans votre environnement local
2. Inclure la configuration spécifique de l’environnement (local et Azure Web App)
3. Définir les environnements dans App Service Web Apps – intermédiaire, Production
4. Si une application de production est déjà en cours d’exécution sur Azure, synchroniser le contenu de production (fichiers/code + base de données) avec les environnements locaux et intermédiaires.
5. Développer l’application sur votre environnement local
6. Placer votre application web de production en mode maintenance ou verrouillé et le contenu de la base de données de synchronisation de l’environnement de production avec les environnements intermédiaires et de développement.
7. Effectuer un déploiement dans un environnement intermédiaire et de test
8. Déployer dans un environnement de production
9. Répéter les étapes 4 à 6

### Umbraco
Dans cette section, vous allez apprendre comment Umbraco CMS utilise un module personnalisé pour effectuer un déploiement sur plusieurs environnements DevOps. Cet exemple offre une approche différente de la gestion de plusieurs environnements de développement.

[Umbraco CMS](http://umbraco.com/) figure parmi les solutions .NET CMS populaires utilisées par de nombreux développeurs. Elle fournit le module [Courier2](http://umbraco.com/products/more-add-ons/courier-2), qui assure le déploiement des environnements de développement vers les environnements intermédiaires, puis vers les environnements de production. Vous pouvez facilement créer un environnement de développement local pour une application web CMS Umbraco à l’aide de Visual Studio ou WebMatrix.

1. Pour créer une application web Umbraco avec Visual Studio, cliquez [ici](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget) .
2. Pour créer une application web Umbraco avec WebMatrix, cliquez [ici](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix) .

N’oubliez pas de supprimer le dossier `install` sous votre application et ne le téléchargez jamais sur les applications web intermédiaires ou de production. Pour ce didacticiel, c’est WebMatrix qui est utilisé

#### Configuration d’un environnement intermédiaire
Comme indiqué plus haut, créez un emplacement de déploiement pour l’application web Umbraco CMS, en supposant que vous disposez déjà d’une application web Umbraco CMS fonctionnelle. Dans le cas contraire, vous pouvez en créer une dans marketplace.

Mettez à jour la chaîne de connexion de votre emplacement de déploiement intermédiaire de manière à ce qu’elle pointe sur la base de données **umbraco-stage-db** récemment créée. Votre application web de production (umbraositecms-1) et l'application web intermédiaire (umbracositecms-1-étape) **DOIVENT** référencer des bases de données différentes.

![Mettre à jour la chaîne de connexion pour l’application web intermédiaire vers une nouvelle base de données intermédiaire](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

Cliquez sur **Accéder aux paramètres de publication** pour l'emplacement de déploiement **intermédiaire**. Cette opération permet de télécharger un fichier de paramètres de publication qui stocke toutes les informations nécessaires à Visual Studio ou Web Matrix pour publier votre application à partir de l’application web de développement local sur l’application Web Azure.

 ![Obtenir le paramètre de publication de l’application web intermédiaire de publication](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- Ouvrez votre application web de développement local dans **WebMatrix** ou **Visual Studio**. Dans ce didacticiel, c’est Web Matrix qui est utilisé et vous devez dans un premier temps importer le fichier de paramètres de publication de votre application web intermédiaire

![Importer les paramètres de publication d’Umbraco à l’aide de Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- Vérifiez les modifications de la boîte de dialogue et déployez votre application web locale dans votre application web Azure, *umbracositecms-1-stage*. Lors du déploiement des fichiers directement dans votre application web intermédiaire, omettez tous les fichiers dans le dossier `~/app_data/TEMP/`, car ils seront régénérés lors du premier démarrage de l'application intermédiaire. Vous devez également omettre le fichier `~/app_data/umbraco.config`, car lui aussi sera régénéré.

![Passer en revue les modifications de publication dans une matrice web](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Après la publication de l’application web locale Umbraco sur l’application web intermédiaire, accédez à votre application web et effectuez quelques tests pour éliminer les éventuels problèmes.

#### Configuration de module de déploiement Courier2
Avec le module [Courier2](http://umbraco.com/products/more-add-ons/courier-2), vous pouvez transmettre des contenus, des feuilles de style, des modules de développement etc. d'un simple clic droit à partir d'une application web intermédiaire vers l’application web, pour des déploiements moins problématiques et une limitation des risques d'interruption de votre application web de production au moment du déploiement et de la mise à jour. 
Achetez une licence Courier2 pour le domaine `*.azurewebsites.net` et votre domaine personnalisé (par exemple http://abc.com). Une fois que vous avez acheté la licence, placez la licence téléchargée (fichier .LIC) dans le dossier `bin`.

![Placer un fichier de licence sous un dossier bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Téléchargez le package Courier2 [ici](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/) . Connectez-vous à votre application web intermédiaire, par exemple, http://umbracocms-site-stage.azurewebsites.net/umbraco, puis cliquez sur le menu **Developer** et sélectionnez **Packages**. Cliquez sur **Installer** le package local

![Programme d’installation de Umbraco Package](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

Téléchargez le package courier2 en utilisant l’installateur.

![Téléchargement du package du module courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

Pour réaliser la configuration, vous devez mettre à jour le fichier courier.config dans le dossier **Config** de votre application web.

```xml
<!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1.azurewebsites.net</url>
            <user>0</user>
            <!--<login>user@email.com</login> -->
            <!-- <password>user_password</password>-->
           <!-- <passwordEncoding>Clear</passwordEncoding>-->
           </repository>
  </repositories>
 ```

Sous `<repositories>`, entrez l'URL du site de production et les informations utilisateur.  
Si vous utilisez le fournisseur d'appartenances Umbraco par défaut, ajoutez l'ID de l'utilisateur Administration dans la section <user>. Si vous utilisez un fournisseur d'appartenances Umbraco personnalisé, utilisez les éléments `<login>`, `<password>` pour que le module Courier2 sache comment se connecter au site de production.  
Pour plus d'informations, consultez la [documentation](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) du module Courier.  


De même, installez le module Courier sur votre site de production et configurez-le de manière à ce qu'il pointe vers l'application web intermédiaire dans le fichier courier.config concerné, comme indiqué ici

```xml
  <!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1-stage.azurewebsites.net</url>
            <user>0</user>
           </repository>
  </repositories>
```

Cliquez sur l’onglet Courier2 dans le tableau de bord de l’application web Umbraco CMS et sélectionnez des emplacements. Vous devez voir le nom du référentiel mentionné dans `courier.config`. Exécutez cette opération à la fois sur votre application de production et sur l’application web intermédiaire.

![Affichage du référentiel de l’application web](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

Nous allons maintenant déployer un contenu du site intermédiaire sur le site de production. Accédez à du contenu, sélectionnez une page existante ou créez une nouvelle page. Sélectionnez une page existante à partir de l'application web (son titre a été changé en **Prise en main – nouveau**), puis cliquez sur **Enregistrer et publier**.

![Modification du titre de page et publication](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

Sélectionnez maintenant la page modifiée et *cliquez avec le bouton droit* pour afficher toutes les options. Cliquez sur **Courier** pour afficher la boîte de dialogue Déploiement. Cliquez sur **Déployer** pour lancer le déploiement

![Boîte de dialogue déploiement de module Courrier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

Passez en revue les modifications et cliquez sur Continuer.

![Passage en revue des modifications via la boîte de dialogue de déploiement du module Courier](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

Le journal de déploiement indique si le déploiement a réussi.

 ![Affichage des journaux de déploiement du module Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

Parcourez votre application web de production pour voir si les modifications sont prises en compte.

 ![Accès à l’application web de production](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Pour en savoir plus sur la façon d’utiliser Courier, consultez la documentation associée.

#### Mise à niveau la version d’Umbraco CMS

Courier ne permet pas le déploiement avec mise à niveau d’une version de CMS Umbraco sur l’autre. Lors de la mise à niveau de la version Umbraco CMS, vous devez vérifier les incompatibilités avec vos modules personnalisés ou des modules tiers et les bibliothèques principales Umbraco. En tant que meilleure pratique

1. Sauvegardez TOUJOURS votre application web et votre base de données avant de procéder une mise à niveau. Sur Azure Web App, vous pouvez configurer des sauvegardes automatiques de vos sites web grâce à la fonction de sauvegarde, et restaurer votre site si nécessaire à l’aide de la fonctionnalité de restauration. Pour plus d'informations, consultez [Sauvegarde de votre application web](web-sites-backup.md) et [Restauration de votre application web](web-sites-restore.md).

2. Vérifiez si les packages tiers que vous utilisez sont compatibles avec la version vers laquelle vous effectuez la mise à niveau. Sur la page de téléchargement du package, vérifiez la compatibilité du projet avec la version d’Umbraco CMS.

Pour plus de détails sur la mise à niveau de votre application Web locale, suivez les instructions mentionnées [ici](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

Une fois la mise à niveau de votre site de développement local terminée, publiez les modifications dans l’application web intermédiaire. Testez votre application et, si tout vous semble correct, utilisez le bouton **Échange** pour **faire basculer** votre site intermédiaire vers l'application web de production. Lorsque vous effectuez l'opération d'**échange**, vous pouvez afficher les modifications qui seront prises en compte dans la configuration de votre application web. Grâce à cette opération d'**échange**, vous faites basculer les applications web et les bases de données. Cela signifie que, après l’ÉCHANGE, que l’application pointera vers la base de données umbraco-stage-db et l’application web intermédiaire qui pointera sur la base de données umbraco-prod-db.

![Vue d’ensemble de l’échange pour le déploiement de CMS Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Avantage de l’échange de l’application Web et de base de données :
1. Vous avez la possibilité de revenir à la version précédente de votre application web grâce à un autre **échange** en cas de problème d'application.
2. En cas de mise à niveau, vous devez déployer des fichiers et une base de données d’application web intermédiaire vers l’application web de production et la base de données. De nombreuses choses peuvent mal se passer lors du déploiement de fichiers et d’une base de données. Grâce à la fonction d’**échange** d’emplacements, il est possible de réduire les temps d’arrêt pendant une mise à niveau, ainsi que les risques de panne susceptibles de se produire lors du déploiement de modifications.
3. Vous avez la possibilité d'exécuter un **test A/B** à l'aide de la fonction [Test en production](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

Cet exemple montre la flexibilité de la plateforme sur laquelle vous pouvez élaborer des modules personnalisés similaires au module Umbraco Courier pour gérer le déploiement sur les environnements.

## Références
[Développement logiciel agile avec Azure App Service](app-service-agile-software-development.md)

[Configurer des environnements intermédiaires pour les applications web dans Azure App Service](web-sites-staged-publishing.md)

[Comment limiter l’accès web aux emplacements de déploiement hors production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

<!---HONumber=AcomDC_0615_2016-->