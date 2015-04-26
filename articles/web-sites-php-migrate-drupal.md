<properties 
	pageTitle="Migration de Drupal vers Sites Web Azure" 
	description="Migrez un site PHP Drupal vers Sites Web Azure." 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="tomfitz"/>


# Migration de Drupal vers Sites Web Azure

Dans la mesure où Sites Web Azure prend en charge les codes PHP et MySQL, la migration d'un site Drupal vers Sites Web Azure est relativement simple. Drupal et PHP s'exécutant sur toutes les plateformes, le processus doit fonctionner pour le basculement de Drupal vers Sites Web Azure, quelle que soit votre plateforme actuelle. Cela dit, les installations Drupal peuvent fortement varier. Certaines étapes de migration uniques peuvent donc ne pas être présentées dans les étapes ci-dessous. Notez que l'outil Drush n'est pas utilisé, car il n'est pas pris en charge par Sites Web Azure.

> [AZURE.NOTE]
> Si vous déplacez une application Drupal complexe et volumineuse, vous pouvez également utiliser Azure Cloud Services. Pour plus d'informations sur les différences entre Sites Web et Cloud Services, reportez-vous à la section <a href="http://go.microsoft.com/fwlink/?LinkId=310123">Comparaison entre Sites Web Azure, Cloud Services et les machines virtuelles : Quand les utiliser ?</a>. Pour obtenir de l'aide sur la migration de Drupal vers Cloud Services, consultez la rubrique <a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">Migration d'un site Drupal de LAMP vers Windows Azure</a>.

## Sommaire

- [Création d'un site Web Azure][]
- [Copie de la base de données][]
- [Modification du fichier Settings.php][]
- [Déploiement du code Drupal][]
- [Informations connexes][]
 
##<a name="create-siteanddb"></a>1. Création d'un site Web Azure et d'une base de données MySQL

Tout d'abord, parcourez le didacticiel étape par étape pour apprendre à créer un site Web avec MySQL : [Création et déploiement d'un site Web Azure PHP-MySQL avec Git][]. Si vous souhaitez utiliser Git pour publier votre site Drupal, suivez les étapes du didacticiel qui vous expliquent comment configurer un référentiel Git. Veillez à suivre les instructions de la section **Obtention des informations de connexion MySQL distantes**, car vous aurez besoin de ces informations ultérieurement. Vous pouvez ignorer le reste du didacticiel visant à déployer votre site Drupal, mais si vous débutez avec Sites Web Azure (ou Git), vous y trouverez des informations utiles.

Après avoir configuré un nouveau site Web avec une base de données MySQL, vous disposez à présent des informations de connexion à votre base de données MySQL et d'un référentiel Git (facultatif). L'étape suivante consiste à copier votre base de données vers MySQL dans Sites Web Azure.

##<a name="copy-database"></a>2. Copie de la base de données vers MySQL dans Sites Web Azure

Il existe différentes façons de migrer une base de données dans Azure. L'utilisation de l'outil [MySqlDump][] est très pratique avec les bases de données MySQL. La commande suivante fournit un exemple de copie à partir d'un ordinateur local vers Sites Web Azure :

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

Vous devez bien sûr fournir le nom d'utilisateur et le mot de passe de votre base de données Drupal existante. Vous devez également fournir le nom d'hôte, le nom d'utilisateur, le mot de passe et le nom de la base de données MySQL que vous avez créée lors de la première étape. Ces informations sont disponibles dans les informations de chaîne de connexion que vous avez collectées précédemment. La chaîne de connexion doit posséder un format similaire à la chaîne suivante :

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

Selon la taille de votre base de données, le processus de copie peut durer plusieurs minutes.

Votre base de données Drupal est désormais présente dans Sites Web Azure. Avant de déployer votre code Drupal, vous devez le modifier afin qu'il puisse se connecter à la nouvelle base de données.

##<a name="modify-settingsphp"></a>3. Modification des informations de connexion de base de données dans le fichier settings.php

Vous avez de nouveau besoin de vos informations de connexion à la nouvelle base de données. Ouvrez le fichier **/drupal/sites/default/setting.php** dans un éditeur de texte et remplacez les valeurs de 'database', 'username', 'password' et 'host' dans le tableau de **$databases** avec les valeurs correctes pour votre nouvelle base de données. Lorsque vous avez terminé, vous devez obtenir un résultat semblable à celui-ci :

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

##<a name="deploy-drupalcode"></a>4. Déploiement du code Drupal via Git ou FTP

La dernière étape consiste à déployer votre code dans Sites Web Azure grâce à Git ou FTP.

Si vous utilisez la méthode FTP, obtenez le nom d'hôte et le nom d'utilisateur FTP grâce au tableau de bord de votre site Web. Ensuite, utilisez un client FTP pour télécharger les fichiers Drupal dans le dossier **/site/wwwroot** du site distant.

Si vous utilisez la méthode Git, vous devez avoir configuré un référentiel Git lors des étapes précédentes. Vous devez installer Git sur votre ordinateur local. Ensuite, suivez les instructions fournies après avoir créé le référentiel.

> [AZURE.NOTE]
> Selon vos paramètres Git, vous devrez peut-être modifier votre fichier .gitignore (un fichier masqué et frère dans le dossier .git créé dans votre répertoire racine local après l'exécution de git commit). Ce fichier spécifie les fichiers de votre application Drupal qui peuvent être ignorés. S'il contient des fichiers qui doivent être déployés, supprimez ces entrées afin que ces fichiers ne soient pas ignorés.

Après avoir déployé Drupal dans Sites Web Azure, vous pouvez continuer à déployer des mises à jour via Git ou FTP.

##<a name="related-information"></a>Informations connexes

Pour plus d'informations, consultez les billets et rubriques ci-dessous :

- [Sites Web Azure, une perspective PHP][]
- [Sites Web Azure, Cloud Services et les machines virtuelles : Quand les utiliser ?][]
- [Configuration de code PHP dans Sites Web Azure grâce à des fichiers .user.ini][]
- [Module d'intégration Azure](https://drupal.org/project/azure_auth)
- [Module de stockage d'objets blob Azure](https://drupal.org/project/azure_blob)

  [Création d'un site Web Azure]: #create-siteanddb
  [Copie de la base de données]: #copy-database
  [Modification du fichier Settings.php]: #modify-settingsphp
  [Déploiement du code Drupal]: #deploy-drupalcode
  [Informations connexes]: #related-information
  [Création et déploiement d'un site Web Azure PHP-MySQL avec Git]: http://azure.microsoft.com/develop/php/tutorials/website-w-mysql-and-git/
  
  [Sites Web Azure, une perspective PHP]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Sites Web Azure, Cloud Services et les machines virtuelles : Quand les utiliser ?]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Configuration de code PHP dans Sites Web Azure grâce à des fichiers .user.ini]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Module d'intégration Azure]: http://drupal.org/project/azure



<!--HONumber=42-->
