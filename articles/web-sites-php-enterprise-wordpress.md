<properties 
	pageTitle="WordPress d'entreprise sur Azure App Service" 
	description="Découvrez comment héberger un site WordPress d'entreprise sur Azure App Service" 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>

#WordPress d'entreprise sur Azure App Service

Azure App Service fournit un environnement modulable, sécurisé et facile à utiliser pour les sites [WordPress][wordpress] stratégiques à grande échelle. Microsoft exécute justement des sites d'entreprise tels que les blogs [Office][officeblog] et [Bing][bingblog]. Ce document vous indique comment utiliser les applications web Azure App Service Web Apps pour établir et gérer un site WordPress pour entreprise basé sur le cloud capable de gérer un grand nombre de visiteurs.

## Architecture et planification

Il y a seulement deux conditions requises pour l'installation WordPress de base.

* **Base de données MySQL** : disponible via [ClearDB dans l'Azure Store][cdbnstore], ou vous pouvez gérer votre propre installation de MySQL sur les machines virtuelles Azure exécutant [Windows][mysqlwindows] ou [Linux][mysqllinux].

    > [AZURE.NOTE] ClearDB fournit plusieurs configurations MySQL, avec différentes caractéristiques de performance pour chaque configuration. Consultez l'[Azure Store][cdbnstore] pour plus d'informations sur les offres fournies par le biais de l'Azure Store ou la page [tarification ClearDB](http://www.cleardb.com/pricing.view) pour les offres directement à partir de ClearDB.
    
* **PHP version 5.2.4 ou ultérieure** : Azure App Service fournit actuellement les [versions PHP 5.3, 5.4 et 5.5][phpwebsite].

	> [AZURE.NOTE] Nous recommandons de toujours exécuter la dernière version de PHP pour vous assurer de disposer des derniers correctifs de sécurité.

###Déploiement basique

Vous pouvez créer une solution de base au sein d'une région Azure rien qu'avec les conditions requises de base.

![une application web Azure et une base de données MySQL hébergées dans une seule région Azure][basic-diagram]

Même si vous pouvez mettre votre application à l'échelle en créant plusieurs instances Web Apps du site, tout est hébergé au sein des centres de données d'une région géographique spécifique. Les visiteurs à l'extérieur de cette région peuvent rencontrer des temps de réponse lents en utilisant le site. De plus, si les centres de données de cette région tombent en panne, votre application en subira les conséquences.


###Déploiement multi-régions

À l'aide d'Azure [Traffic Manager][trafficmanager], il est possible de mettre à l'échelle votre site WordPress dans plusieurs régions géographiques tout en fournissant une URL unique aux visiteurs. Tous les visiteurs passent par Traffic Manager et sont ensuite dirigés vers une région selon la configuration de l'équilibrage de charge.

![une application web Azure, hébergée dans de nombreuses régions, utilisant un routeur CDBR haute disponibilité pour assurer un acheminement vers MySQL à travers les régions][multi-region-diagram]

Au sein de chaque région, le site WordPress est toujours mis à l'échelle dans plusieurs instances Web Apps, mais cette mise à l'échelle est spécifique à la région ; les régions à fort trafic peuvent être mises à l'échelle différemment par rapport à celles qui ont un trafic moindre.

La réplication et le routage vers plusieurs bases de données MySQL peuvent être effectués à l'aide du [Routeur CDBR haute disponibilité][cleardbscale] ClearDB (à gauche) ou du [Cluster CGE MySQL][cge]. 

###Déploiement multi-régions avec stockage multimédia et mise en cache

Si le site accepte les chargements ou héberge des fichiers multimédias, utilisez le stockage d'objets blob Azure. Si vous devez disposer d'une mise en cache, pensez au [Cache Redis][rediscache], à [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), à [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) ou à l'une des autres offres de mise en cache de l'[Azure Store](http://azure.microsoft.com/gallery/store/).

![une application web Azure, hébergée dans de nombreuses régions, utilisant un routeur CDBR haute disponibilité pour MySQL, avec le service de cache géré, le stockage d'objets blob et le CDN][performance-diagram]

Le stockage d'objets blob est par défaut géo-distribué dans les régions ; vous n'avez donc pas besoin de répliquer les fichiers sur tous les sites. Vous pouvez également activer le [Réseau de distribution de contenu (CDN)][cdn] Azure pour le stockage d'objets Blob, qui distribue des fichiers à des nœuds finaux plus proches de vos visiteurs.

###Planification

####Conditions supplémentaires

Action à réaliser... | Élément à utiliser...
------------------------|-----------
**Téléchargement ou stockage des fichiers volumineux** | [Plug-in WordPress pour l'utilisation du stockage d'objets Blob][storageplugin]
**Envoi de courrier électronique** | [SendGrid][storesendgrid] et le [plug-in WordPress pour l'utilisation de SendGrid][sendgridplugin]
**Noms de domaines personnalisés** | [Configuration d'un nom de domaine personnalisé dans Azure App Service][customdomain]
**HTTPS** | [Activation du protocole HTTPS pour une application web dans Azure App Service][httpscustomdomain]
**Validation de préproduction** | [Configuration des environnements intermédiaires pour les applications web dans Azure App Service][staging] <p>Remarque : le fait de passer une application web de la version intermédiaire à la production modifie également la configuration de WordPress. Assurez-vous que tous les paramètres sont mis à jour en fonction des conditions requises pour votre application de production avant d'effectuer le basculement.</p> 
**Surveillance et dépannage** | [Activation de la journalisation des diagnostics pour les applications web dans Azure App Service][log] et [surveillance des applications web dans Azure App Service][monitor]
**Déploiement de votre site** | [Déploiement d'une application web dans Azure App Service][déploiement]

####Disponibilité et récupération d'urgence

Action à réaliser... | Élément à utiliser...
------------------------|-----------
**Équilibrage de la charge des sites** ou **géodistribution des sites** | [Routage du trafic avec Azure Traffic Manager][trafficmanager]
**Sauvegarde et restauration** | [Sauvegarde d'une application web dans Azure App Service][backup] et [restauration d'une application web dans Azure App Service][restore]

####Performances

La performance du cloud est principalement assurée via la mise en cache et la mise à l'échelle. Toutefois, la mémoire, la bande passante et d'autres attributs de l'hébergement des applications web doivent également être pris en considération.

Action à réaliser... | Élément à utiliser...
------------------------|-----------
**Présentation des fonctionnalités des instances d'App Service** |  [Tarification, notamment fonctionnalités des niveaux d'App Service][websitepricing]
**Ressources de cache** | [Cache Redis][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) ou l'une des autres offres de mise en cache dans l'[Azure Store](/gallery/store/)
**Mise à l'échelle de votre application** | [Mise à l'échelle d'une application web dans Azure App Service][websitescale] et [Routage haute disponibilité ClearDB][cleardbscale]. Si vous choisissez d'héberger et de gérer votre propre installation MySQL, pensez au [Cluster CGE MySQL][cge] pour la mise à l'échelle

####Migration

Il existe deux méthodes permettant de migrer un site WordPress existant vers Azure App Service.

* **[Exportation WordPress][exporter]** : exporte le contenu de votre blog, qui peut alors être importé dans un nouveau site WordPress sur Azure App Service à l'aide du [plug-in Importer de WordPress][import].

	> [AZURE.NOTE] Ce processus vous permet de migrer votre contenu, mais pas vos plug-ins, thèmes ou autres personnalisations. Ces éléments doivent être installés à nouveau manuellement.

* **Migration manuelle** : [sauvegardez votre site][wordpressbackup] et sa [base de données][wordpressdbbackup], puis restaurez-le dans une application web dans Azure App Service et sa base de données MySQL associée pour migrer des sites très personnalisés et éviter d'avoir à installer manuellement les plug-ins, les thèmes et autres personnalisations.

## Instructions pas à pas

### Création d'un site WordPress

1. Utilisez l'[Azure Store][cdbnstore] pour créer une base de données MySQL de la taille que vous avez identifiée dans la section [Architecture et planification](#planning) dans la ou les régions où vous allez héberger votre site.

2. Suivez les étapes mentionnées dans [Créer une application web WordPress dans Azure App Service][createwordpress] pour créer une nouvelle application web WordPress. Lorsque vous créez le site, sélectionnez **Utiliser une base de données MySQL existante** et choisissez la base de données créée à l'étape 1.

Si vous migrez un site WordPress existant, consultez la section [Migration d'un site WordPress existant sur Azure](#Migrate-an-existing-WordPress-site-to-Azure) après avoir créé une nouvelle application web.

### Migration d'un site WordPress existant sur Azure

Comme indiqué dans la section [Architecture et planification](#planning) il existe deux moyens permettant de migrer un site WordPress.

* **Exportation et importation** : pour les sites sans grande personnalisation, ou pour lesquels vous voulez simplement déplacer le contenu.

* **Sauvegarde et restauration** : pour les sites très personnalisés pour lesquels vous voulez tout déplacer.

Utilisez l'une des sections suivantes pour migrer votre site.

####Méthode d'exportation et d'importation

1. Utilisez [Exportation WordPress][exporter] pour exporter votre site existant.

2. Créez une nouvelle application web en suivant les étapes de la section [Création d'un site WordPress](#Create-a-new-WordPress-site) .

3. Connectez-vous à votre site WordPress sur Web Apps et cliquez sur **Plug-ins** -> **Ajouter**. Recherchez et installez le plug-in **Importer de WordPress**.

4. Une fois le plug-in Importer installé, cliquez sur **Outils** -> **Importation**, puis sélectionnez **WordPress** pour utiliser le plug-in Importer de WordPress.

5. Sur la page **Importation WordPress**, cliquez sur **Choisir un fichier**. Recherchez le fichier WXR exporté de votre site WordPress existant, puis sélectionnez **Chargement du fichier et importation**.

6. Cliquez sur **Envoyer**. Un message vous signalera que l'importation est terminée.

8. Une fois que vous avez procédé à toutes les étapes, redémarrez votre site depuis le panneau de son application web sur le [portail Azure][mgmtportal].

Après avoir importé le site, vous devez suivre les étapes suivantes pour activer les paramètres non présents dans le fichier d'importation.

Si vous utilisiez ceci... | Procédez comme suit...
------------------ | ----------
**Permaliens** | À partir du tableau de bord WordPress du nouveau site, cliquez sur **Réglages** -> **Permaliens**, puis mettez à jour la structure Permaliens
**image/media links** | Pour mettre à jour les liens vers le nouvel emplacement, utilisez le [plug-in Velvet Blues Update URLs][velvet], un outil Rechercher et remplacer ou procédez manuellement dans votre base de données
**Thèmes** | Accédez à**Apparence** -> **Thème** et mettez à jour le thème du site web à votre guise
**Menus** | Si votre thème prend en charge les menus, les liens vers votre page d'accueil peuvent toujours présenter l'ancien sous-répertoire incorporé. Accédez à **Apparence** -> **Menus** et mettez-les à jour

####Méthode de sauvegarde et de restauration

1. Sauvegardez votre site WordPress existant à l'aide des informations sur les [Sauvegardes WordPress][wordpressbackup].

2. Sauvegardez votre base de données existante à l'aide des informations de la page [Sauvegarde de votre base de données][wordpressdbbackup].

3. Créez une base de données et restaurez la sauvegarde.

	1. Achetez une nouvelle base de données dans l'[Azure Store][cdbnstore] ou configurez une base de données MySQL sur une machine virtuelle [Windows][mysqlwindows] ou [Linux][mysqllinux].

	2. À l'aide d'un client MySQL tel que [MySQL Workbench][workbench], connectez-vous à la nouvelle base de données et importez votre base de données WordPress.

	3. Mettez à jour la base de données pour modifier les entrées de domaine sur votre nouveau domaine Azure App Service. Par exemple, mywordpress.azurewebsites.net. Utilisez l'outil [Rechercher et remplacer du script de bases de données WordPress][searchandreplace] pour modifier de manière sécurisée toutes les instances.

4. Créez une nouvelle application web sur le portail Azure et publiez la sauvegarde WordPress.

	1. Créez une nouvelle application web sur le [portail Azure][mgmtportal] à l'aide d'une base de données en cliquant sur **Nouveau** -> **Web + Mobile** -> **Azure Marketplace** -> **Web Apps** -> **Web app + SQL** (ou **Web app + MySQL**) -> **Créer**. Définissez tous les paramètres requis pour créer une application web vierge.

	2. Dans votre sauvegarde WordPress, localisez le fichier **wp-config.php** et ouvrez-le avec un éditeur. Remplacez les entrées suivantes avec les informations de votre nouvelle base de données MySQL.

		* **DB_NAME** : nom d'utilisateur de la base de données

		* **DB_USER** : nom d'utilisateur pour accéder à la base de données

		* **DB_PASSWORD** : mot de passe de l'utilisateur

		Après avoir modifié ces entrées, enregistrez et fermez le fichier **wp-config.php**.

	3. Utilisez les informations [Déployer une application web dans Azure App Service][déploiement] pour activer la méthode de déploiement que vous souhaitez utiliser, puis déployer votre sauvegarde WordPress vers votre application web dans Azure App Service.

5. Une fois le site WordPress déployé, vous devez pouvoir accéder au nouveau site (sous forme d'application web App Service) en utilisant l'URL *.azurewebsite.net du site.

###Configuration de votre site

Une fois que le site WordPress a été créé ou migré, utilisez les informations suivantes pour améliorer les performances ou activer des fonctionnalités supplémentaires.

Action à réaliser... | Élément à utiliser...
------------- | -----------
**Définition du mode de plan d'App Service, de la taille et activation de la mise à l'échelle** | [Mise à l'échelle d'une application web dans Azure App Service][websitescale]
**Activation des connexions permanentes de base de données** <p>Par défaut, WordPress n'utilise pas de connexions permanentes de base de données, qui peuvent ralentir les connexions à votre base de données après plusieurs connexions.</p>  | <ol><li><p>Modifiez le fichier <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Recherchez la ligne suivante :</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Remplacez la ligne précédente par celle-ci :</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Recherchez la ligne suivante :</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Remplacez la ligne ci-dessus par celle-ci :</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Enregistrez le fichier <strong>wp-includes/wp-db.php</strong> et redéployez le site.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><p>Ces modifications peuvent être remplacées lorsque WordPress est mis à jour.</p><p>WordPress active les mises à jour automatiques par défaut, qui peuvent être désactivées en modifiant le fichier <strong>wp-config.php</strong> et en ajoutant <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Pour gérer les mises à jour, vous pouvez également utiliser une tâche web qui surveille le fichier <strong>wp-db.php</strong> et exécute les modifications ci-dessus chaque fois que le fichier est mis à jour. Consultez <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Présentation des tâches web</a> pour plus d'informations.</p></div>
**Amélioration des performances** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Désactiver le cookie ARR</a> : peut améliorer les performances lorsque vous exécutez WordPress sur plusieurs instances Web Apps.</p></li><li><p>Activer la mise en cache. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Cache Redis</a> (version préliminaire) peut être utilisé avec le <a href="https://wordpress.org/plugins/redis-object-cache/">plug-in WordPress Redis object cache</a> ou l'une des offres de mise en cache proposées dans l'<a href="/gallery/store/">Azure Store</a>.</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Accélérer le fonctionnement de WordPress avec Wincache</a> : Wincache est activé par défaut pour Web Apps</p></li><li><p><a href="../web-sites-scale/">Mise à l'échelle d'une application dans Azure App Service</a> et <a href="http://www.cleardb.com/developers/cdbr/introduction">Routage haute disponibilité ClearDB</a> ou <a href="http://www.mysql.com/products/cluster/">Cluster CGE MySQL</a></p></li></ul>
**Utilisation d'objets blob pour le stockage** | <ol><li><p><a href="../storage-create-storage-account/">Création d'un compte de stockage Azure</a></p></li><li><p>Apprenez comment <a href="../cdn-how-to-use/">Utiliser le réseau de distribution de contenu (CDN)</a> pour géodistribuer les données stockées dans les objets blob.</p></li><li><p>Installez et configurez le <a href="https://wordpress.org/plugins/windows-azure-storage/">plug-in Azure Storage pour WordPress</a>.</p><p>Pour obtenir des informations détaillées sur l'installation et la configuration du plug-in, consultez le <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guide d'utilisation</a>.</p> </li></ol>
**Activation du courrier électronique** | <ol><li><p><a href="/gallery/store/sendgrid/sendgrid-azure/">Activation de SendGrid via l'Azure Store</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Installation du plug-in SendGrid pour WordPress</a></p></li></ol>
**Configuration d'un nom de domaine personnalisé** | [Configuration d'un nom de domaine personnalisé dans Azure App Service][customdomain]
**Activation du protocole HTTPS pour un nom de domaine personnalisé** | [Activation du protocole HTTPS pour une application web dans Azure App Service][httpscustomdomain]
**Équilibrage de la charge ou géodistribution de votre site** | [Acheminement du trafic avec Azure Traffic Manager][trafficmanager]. Si vous utilisez un domaine personnalisé, consultez la rubrique [Configuration d'un nom de domaine personnalisé dans Azure App Service][customdomain] pour plus d'informations sur l'utilisation de Traffic Manager avec des noms de domaine personnalisés
**Activation des sauvegardes automatisées** | [Sauvegarde d'une application web dans Azure App Service][backup]
**Activation de la journalisation des diagnostics** | [Activation de la journalisation des diagnostics pour les applications web dans Azure App Service][log]

## Étapes suivantes

* [Optimisation de WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Conversion de WordPress en WordPress multisite dans Azure App Service](web-sites-php-convert-wordpress-multisite.md)

* [Assistant de mise à niveau ClearDB pour Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hébergement de WordPress dans un sous-dossier de votre application web dans Azure App Service](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Pas-à-pas : Création d'un site WordPress avec Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Hébergement de votre blog WordPress existant sur Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Activation de permaliens conviviaux dans WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Migration et exécution de votre blog WordPress dans Azure App Service](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Exécution gratuite de WordPress dans Azure App Service](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress sur Azure en moins de 2 minutes](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Déplacement d'un blog WordPress vers Azure - Partie 1 : création d'un blog WordPress sur Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Déplacement d'un blog WordPress vers Azure - Partie 2 : transfert de votre contenu](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Déplacement d'un blog WordPress vers Azure - Partie 3 : paramétrage de votre domaine personnalisé](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Déplacement d'un blog WordPress vers Azure - Partie 4 : permaliens conviviaux et règles de réécriture d'URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Déplacement d'un blog WordPress vers Azure - Partie 5 : passage d'un sous-dossier à la racine](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Paramétrage d'une application web dans votre compte Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Soutien de WordPress sur Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Astuces pour WordPress sur Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d'ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n'est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir des informations détaillées sur le passage de Sites Web à App Service, consultez : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir des informations détaillées sur le passage de l'ancien portail au nouveau portail, consultez : [Référence pour la navigation dans le portail en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=529715)

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: /blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: virtual-machines-mysql-windows-server-2008r2.md
[mysqllinux]: virtual-machines-linux-mysql-use-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[exporter]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[déploiement]: web-sites-deploy.md
[posh]: install-configure-powershell.md
[xplat-cli]: xplat-cli.md
[storesendgrid]: /gallery/store/sendgrid/sendgrid-azure/
[cdn]: cdn-how-to-use.md

<!--HONumber=52-->