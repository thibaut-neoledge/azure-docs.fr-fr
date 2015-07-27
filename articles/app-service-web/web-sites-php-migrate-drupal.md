<properties 
	pageTitle="Migration Drupal vers Azure App Service" 
	description="Migrez un site PHP Drupal vers Azure App Service." 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>


# Migration Drupal vers Azure App Service

Dans la mesure où Azure App Service prend en charge les codes PHP et MySQL, la migration d’un site Drupal vers Azure App Service est relativement directe. Drupal et PHP s’exécutant sur toutes les plateformes, le processus doit fonctionner pour le déplacement de Drupal vers Azure App Service, quelle que soit votre plateforme actuelle. Cela dit, les installations Drupal peuvent fortement varier. Certaines étapes de migration uniques peuvent donc ne pas être présentées dans les étapes ci-dessous. Notez que l’outil Drush n’est pas utilisé, car il n’est pas pris en charge par Azure App Service.

Si vous déplacez une application Drupal complexe et volumineuse, vous pouvez également utiliser Azure Cloud Services. Pour plus d’informations sur les différences entre App Service et Cloud Services, consultez <a href="http://go.microsoft.com/fwlink/?LinkId=310123">Azure App Service, Azure Cloud Services et Azure Virtual Machines : que choisir ?</a> Pour obtenir de l’aide sur la migration de Drupal vers Cloud Services, consultez la rubrique <a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">Migration d’un site Drupal de LAMP vers Azure</a>.
 
## Création d’une application web et d’une base de données MySQL

Tout d’abord, parcourez le didacticiel pas à pas pour apprendre à créer une nouvelle application web avec MySQL : [Création d’une application web PHP-MySQL dans Azure App Service et déploiement à l’aide de Git][]. Si vous souhaitez utiliser Git pour publier votre site Drupal, suivez les étapes du didacticiel qui expliquent comment configurer un référentiel Git. Veillez à suivre les instructions de la section **Obtention des informations de connexion MySQL distantes**, car vous aurez besoin de ces informations ultérieurement. Vous pouvez ignorer le reste du didacticiel visant à déployer votre site Drupal, mais si vous n’êtes pas familiarisé avec Azure App Service (et avec Git), vous y trouverez des informations utiles.

Après avoir configuré une nouvelle application web avec une base de données MySQL, vous disposez des informations de connexion à votre base de données MySQL et d’un référentiel Git (facultatif). L’étape suivante consiste à copier votre base de données dans la base de données MySQL de l’application web.

## Copie de la base de données dans la base de données MySQL de l’application web

Il existe différentes façons de migrer une base de données dans Azure. L’utilisation de l’outil [MySqlDump][] est très pratique avec les bases de données MySQL. La commande suivante fournit un exemple de copie à partir d’un ordinateur local vers Azure :

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

Vous devez bien sûr fournir le nom d’utilisateur et le mot de passe de votre base de données Drupal existante. Vous devez également fournir le nom d'hôte, le nom d'utilisateur, le mot de passe et le nom de la base de données MySQL que vous avez créée lors de la première étape. Ces informations sont disponibles dans les informations de chaîne de connexion que vous avez collectées précédemment. La chaîne de connexion doit posséder un format similaire à la chaîne suivante :

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

Selon la taille de votre base de données, le processus de copie peut durer plusieurs minutes.

Votre base de données Drupal est désormais présente dans Azure. Avant de déployer votre code Drupal, vous devez le modifier afin qu’il puisse se connecter à la nouvelle base de données.

## Modifier des informations de connexion de base de données dans le fichier settings.php

Vous avez de nouveau besoin de vos informations de connexion à la nouvelle base de données. Ouvrez le fichier **/drupal/sites/default/setting.php** dans un éditeur de texte, puis remplacez les valeurs « database », « username », « password » et « host » dans le tableau **$databases** par les valeurs correctes correspondant à votre nouvelle base de données. Lorsque vous avez terminé, vous devez obtenir un résultat semblable à celui-ci :

    $databases = array (
       'default' => 
       array (
         'default' => 
         array (
           'database' => 'remote_db_name',
           'username' => 'remote_username',
           'password' => 'remote_password',
           'host' => 'remote_host',
           'port' => '',
           'driver' => 'mysql',
           'prefix' => '',
         ),
       ),
     );

Enregistrez le fichier **settings.php**. Vous pouvez désormais procéder au déploiement.

## Déploiement de code Drupal via Git ou FTP

La dernière étape consiste à déployer votre code dans Web Apps grâce à Git ou au protocole FTP.

Si vous utilisez la méthode FTP, obtenez le nom d’hôte et le nom d’utilisateur FTP dans le panneau de votre application web dans le [portail Azure en version préliminaire](https://portal.azure.com). Ensuite, utilisez un client FTP pour télécharger les fichiers Drupal dans le dossier **/site/wwwroot** du site distant.

Si vous utilisez la méthode Git, vous devez avoir configuré un référentiel Git lors des étapes précédentes. Vous devez installer Git sur votre ordinateur local. Ensuite, suivez les instructions fournies après avoir créé le référentiel.

> [AZURE.NOTE]Selon vos paramètres Git, vous devrez peut-être modifier votre fichier .gitignore (un fichier masqué et un jumeau dans le dossier .git créé dans votre répertoire racine local après l’exécution de git commit). Ce fichier spécifie les fichiers de votre application Drupal qui peuvent être ignorés. S’il contient des fichiers qui doivent être déployés, supprimez ces entrées afin que ces fichiers ne soient pas ignorés.

Après avoir déployé Drupal dans Web Apps, vous pouvez continuer à déployer des mises à jour via Git ou FTP.

## Informations connexes

Pour plus d’informations, consultez les billets et rubriques ci-dessous :

- [Azure App Service Web Apps : une perspective PHP][]
- [Azure App Service, Azure Cloud Services et Azure Virtual Machines virtuelles : que choisir ?][]
- [Configuration PHP dans Azure App Service Web Apps avec des fichiers .user.ini][]
- [Module d’intégration Azure](https://drupal.org/project/azure_auth)
- [Module de stockage d’objets blob Azure](https://drupal.org/project/azure_blob)

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

  [Création d’une application web PHP-MySQL dans Azure App Service et déploiement à l’aide de Git]: /develop/php/tutorials/website-w-mysql-and-git/
  
  [Azure App Service Web Apps : une perspective PHP]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Azure App Service, Azure Cloud Services et Azure Virtual Machines virtuelles : que choisir ?]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Configuration PHP dans Azure App Service Web Apps avec des fichiers .user.ini]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Azure Integration Module]: http://drupal.org/project/azure
 

<!---HONumber=July15_HO3-->