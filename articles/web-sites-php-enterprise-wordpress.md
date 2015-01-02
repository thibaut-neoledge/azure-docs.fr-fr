<properties title="Enterprise class WordPress on Azure Websites" pageTitle="WordPress pour entreprise dans Azure Web Sites" description="Découvrez comment héberger un site WordPress pour entreprise dans Azure Web Sites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="tomfitz" manager="wpickett" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.devlang="php" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="11/11/2014" ms.author="tomfitz" />

#WordPress pour entreprise dans Azure Web Sites

Azure Web Sites fournit un environnement modulable, sécurisé et facile à utiliser pour les sites [WordPress][wordpress] stratégiques et à grande échelle. Microsoft également exécute des sites pour entreprise tels que les blogs [Office][officeblog] et [Bing][bingblog]. Ce document vous montre comment utiliser Sites Web Azure pour établir et gérer un site WordPress pour entreprise basé sur le cloud capable de gérer un grand nombre de visiteurs.

##Dans cet article 

* [Architecture et planification](#planning) - Découvrez l'architecture, les conditions requises et les questions de performance avant de créer votre site

* [Procédure :](#howto) créer, déployer et configurer votre site

* [Autres ressources](#resources) - Ressources et informations supplémentaires

##<a id="plan"></a>Architecture et planification

Il y a seulement deux conditions requises pour l'installation WordPress de base.

* **Base de données MySQL** : disponible via [ClearDB dans Azure Store][cdbnstore], sinon, vous pouvez gérer votre propre installation de MySQL dans Azure Virtual Machines sous [Windows][mysqlwindows] ou [Linux][mysqllinux].

    > [WACOM.NOTE] ClearDB fournit plusieurs configurations MySQL, avec différentes caractéristiques de performance pour chaque configuration. Consultez l'[Azure Store][cdbnstore] pour plus d'informations sur les offres proposées via Azure Store ou sur la [tarification ClearDB](http://www.cleardb.com/pricing.view) pour les offres proposées directement par ClearDB.
    
* **PHP 5.2.4 ou supérieur** : Azure Web Sites fournit actuellement les [versions PHP 5.3, 5.4 et 5.5][phpwebsite].

	> [WACOM.NOTE] Nous recommandons de toujours exécuter la dernière version de PHP pour vous assurer de disposer des derniers correctifs de sécurité.

###Déploiement basique

Vous pouvez créer une solution de base au sein d'une région Azure rien qu'avec les conditions requises de base.

![an Azure Website and MySQL Database hosted in a single Azure region][basic-diagram]

Même si vous pouvez monter en charge votre application en créant plusieurs instances du site web, tout est hébergé au sein des centres de données d'une région géographique spécifique. Les visiteurs à l'extérieur de cette région peuvent rencontrer des temps de réponse lents en utilisant le site. De plus, si les centres de données de cette région tombent en panne, votre application en subira les conséquences.


###Déploiement multi-régions

Avec Azure [Traffic Manager][trafficmanager], vous pouvez mettre à l'échelle votre site WordPress dans plusieurs régions géographiques tout en fournissant une URL unique aux visiteurs. Tous les visiteurs passent par Traffic Manager et sont ensuite dirigés vers une région selon la configuration de l'équilibrage de charge.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][multi-region-diagram]

Au sein de chaque région, le site WordPress est toujours mis à l'échelle dans plusieurs instances du site web, mais cette mise à l'échelle est spécifique à la région ; les régions à fort trafic peuvent être mises à l'échelle différemment par rapport à celles qui ont un trafic moindre.

La réplication et le routage vers plusieurs bases de données MySQL peuvent être effectués à l'aide du [routeur CDBR à haut niveau de disponibilité][cleardbscale] (à gauche) ClearDB ou du [cluster CGE MySQL][cge]. 

###Déploiement multi-régions avec stockage multimédia et mise en cache

Si le site accepte les chargements ou héberge des fichiers multimédias, utilisez le stockage d'objets blob Azure. Si vous utilisez la mise en cache, envisagez [Cache Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/fr-fr/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/fr-fr/gallery/store/memcachier/memcachier/) ou l'une des autres offres de mise en cache d'[Azure Store](http://azure.microsoft.com/fr-fr/gallery/store/).

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][performance-diagram]

Le stockage d'objets blob est par défaut géo-distribué dans les régions ; vous n'avez donc pas besoin de répliquer les fichiers sur tous les sites. Vous pouvez également activer Azure [CDN][cdn] pour le stockage d'objets blob, qui distribue des fichiers à des nœuds finaux plus proches de vos visiteurs.

###Planification

####Conditions supplémentaires

Action à réaliser... Élément à utiliser...
------------------------|-----------
**Télécharger ou stocker des fichiers volumineux** | [Plug-in WordPress pour l'utilisation du stockage d'objets blob][storageplugin]
**Envoyer du courrier électronique** | [SendGrid][storesendgrid] et le plug-in [WordPress pour l'utilisation de SendGrid][sendgridplugin]
**Noms de domaine personnalisés** | [Noms de domaine personnalisés avec Azure Web Sites][customdomain]
**HTTPS** | [Prise en charge de HTTPS dans Azure Web Sites][httpscustomdomain]
**Validation de préproduction** | [Prise en charge de la publication intermédiaire pour les sites web Azure][staging] <p>Remarque : passer un site de la version intermédiaire à la production modifie également la configuration de WordPress. Assurez-vous que tous les paramètres sont mis à jour en fonction des conditions requises pour votre site de production avant d'effectuer le basculement.</p> 
**Surveillance et résolution de problèmes** | [Journalisation de diagnostics avec Azure Web Sites][log] et [Surveillance des sites web Azure][monitor]
**Déploiement de votre site** | [Déployer un site web Azure][deploy]

####Disponibilité et récupération d'urgence

Action à réaliser... Élément à utiliser...
------------------------|-----------
**Équilibrage de la charge des sites ** ou **géodistribution des sites** | [Router le trafic avec Azure Traffic Manager][trafficmanager]
**Sauvegarde et restauration** | [Sauvegarder des sites web Azure][backup] et [restaurer un site web Azure][restore]

####Performances

La performance du cloud s'effectue premièrement via la mise en cache et la montée en charge ; toutefois, la mémoire, la bande passante et autres attributs de l'hébergement du site web doivent également être pris en considération.

Action à réaliser... Élément à utiliser...
------------------------|-----------
**Présentation des capacités des instances de site web** |  [Informations de tarif, y compris les capacités en termes de taille et de mode des sites web][websitepricing]
**Ressources de cache** | [Cache Redis][rediscache], [Memcache Cloud](http://azure.microsoft.com/fr-fr/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/fr-fr/gallery/store/memcachier/memcachier/) ou l'une des autres offres de mise en cache d'[Azure Store](http://azure.microsoft.com/fr-fr/gallery/store/)
**Mise à l'échelle de votre application** | [Mettre à l'échelle un site web Azure][websitescale] et [Routage ClearDB à haut niveau de disponibilité][cleardbscale]. Si vous choisissez d'héberger et de gérer votre propre installation MySQL, pensez au [cluster CGE MySQL][cge] pour la monter en puissance

####Migration

Il existe deux méthodes pour migrer un site WordPress existant vers Sites Web Azure.

* **[Exportation WordPress][export]** : exporte le contenu de votre blog, qui peut alors être importé dans un nouveau site WordPress sur Azure à l'aide du [plug-in d'importation WordPress][import].

	> [WACOM.NOTE] Ce processus vous permet de migrer votre contenu, mais pas vos plug-ins, thèmes ou autres personnalisations. Ces éléments doivent être installés à nouveau manuellement.

* **Migration manuelle** : [sauvegardez votre site][wordpressbackup] et sa [base de données][wordpressdbbackup], puis restaurez-le dans un site web Azure et sa base de données MySQL associée pour migrer des sites très personnalisés et éviter d'avoir à installer manuellement les plug-ins, les thèmes et autres personnalisations.

##Procédures

###<a id="create"></a>Création d'un site WordPress

1. Utilisez [Azure Store][cdbnstore] pour créer une base de données MySQL de la taille que vous avez identifiée dans la section [Architecture et planification],(#planning) dans la ou les régions où vous allez héberger votre site.

2. Procédez comme indiqué dans [Création d'un site web WordPress à partir de la galerie dans Azure][createwordpress] pour créer un site WordPress. Pendant la création du site, sélectionnez **Utiliser une base de données MySQL existante** et la base de données créée à l'étape 1.

Si vous migrez un site WordPress existant, consultez [Migration d'un site WordPress existant](#migrate) après avoir créé un site.

###<a id="migrate"></a>Migration d'un site WordPress existant sur Azure

Comme indiqué dans la section [Architecture et planification],(#planning) il existe deux moyens de migrer un site web WordPress.

* **Exportation et importation** : pour les sites avec peu de personnalisation ou pour lesquels vous voulez simplement déplacer le contenu.

* **Sauvegarde et restauration** : pour les sites très personnalisés pour lesquels vous voulez tout déplacer.

Utilisez l'une des sections suivantes pour migrer votre site.

####Méthode d'exportation et d'importation

1. Utilisez l'[exportation WordPress][export] pour exporter votre site existant.

2. Créez un site web en suivant les étapes de la section [Création d'un site WordPress](#create) .

3. Connectez-vous à votre site WordPress dans Azure Web Sites et cliquez sur **Plug-ins** -> **Ajouter nouveau**. Recherchez et installez le plug-in d'**importation WordPress**.

4. Après l'installation du plug-in d'importation, cliquez sur **Outils** -> **Importer**, puis sélectionnez **WordPress** pour utiliser le plug-in d'importation WordPress.

5. Dans la page **Importer WordPress**, cliquez sur **Choisir un fichier**. Recherchez le fichier WXR exporté de votre site WordPress existant, puis sélectionnez **Télécharger le fichier et importer**.

6. Cliquez sur **Envoyer**. Un message vous signalera que l'importation est terminée.

8. Une fois que vous avez effectué toutes ces étapes, redémarrez votre site web à partir de son tableau de bord sur le [portail de gestion Azure][mgmtportal].

Après avoir importé le site, vous devez suivre les étapes suivantes pour activer les paramètres non présents dans le fichier d'importation.

Si vous utilisiez ceci... Procédez comme suit...
------------------ | ----------
**Liens permanents** | À partir du tableau de bord WordPress du nouveau site, cliquez sur **Paramètres** -> **Liens permanents** puis mettez à jour la structure des liens permanents
**Liens image/multimédia** | Pour mettre à jour les liens vers le nouvel emplacement, utilisez le [plug-in Velvet Blues Update URLs][velvet], un outil de type rechercher/remplacer, ou faites-le manuellement dans votre base de données
**Thèmes** | Accédez à**Apparence** -> **Thème** et mettez à jour le thème du site web comme vous le souhaitez
**Menus** | Si votre thème prend en charge les menus, les liens vers votre page d'accueil peuvent toujours avoir l'ancien sous-répertoire incorporé. Accédez à **Apparence** -> **Menus** et mettez-les à jour

####Méthode de sauvegarde et de restauration

1. Sauvegardez votre site WordPress existant à l'aide des informations de la section [Sauvegardes WordPress][wordpressbackup].

2. Sauvegardez votre base de données existante en utilisant les informations de la section [Sauvegarde de votre base de données][wordpressdbbackup].

3. Créez une base de données et restaurez la sauvegarde.

	1. Achetez une nouvelle base de données dans [Azure Store][cdbnstore] ou configurez une base de données MySQL sur une machine virtuelle [Windows][mysqlwindows] ou [Linux][mysqllinux].

	2. À l'aide d'un client MySQL comme [MySQL Workbench][workbench], connectez-vous à la nouvelle base de données et importez votre base de données WordPress.

	3. Mettez à jour la base de données pour modifier les entrées de domaine sur votre nouveau domaine de site web Azure. Par exemple, mywordpress.azurewebsites.net. Utilisez l'outil [Rechercher et remplacer du script de bases de données WordPress][searchandreplace] pour modifier de manière sécurisée toutes les instances.

4. Créez un site web et publiez la sauvegarde WordPress.

	1. Créez un site web dans le [portail de gestion Azure][mgmtportal] avec une base de données à l'aide de **Nouveau** -> **Site web** -> **Création personnalisée**. Un site vide sera alors créé.

	2. Dans votre sauvegarde WordPress, localisez le fichier **wp-config.php** et ouvrez-le dans un éditeur. Remplacez les entrées suivantes avec les informations de votre nouvelle base de données MySQL.

		* **DB_NAME** : nom d'utilisateur de la base de données

		* **DB_USER** : nom d'utilisateur pour accéder à la base de données

		* **DB_PASSWORD** : mot de passe de l'utilisateur

		Après avoir modifié ces entrées, enregistrez et fermez le fichier **wp-config.php**.

	3. Utilisez les informations sur le [Déploiement d'un site web Azure][deploy] pour activer la méthode de déploiement que vous souhaitez utiliser, puis déployer votre sauvegarde WordPress sur votre site web Azure..

5. Une fois que le site WordPress a été déployé, vous devez pouvoir accéder au nouveau site en utilisant l'URL *.azurewebsite.net pour le site.

###Configuration de votre site

Une fois que le site WordPress a été créé ou migré, utilisez les informations suivantes pour améliorer les performances ou activer des fonctionnalités supplémentaires.

Action à réaliser... Élément à utiliser...
------------- | -----------
**Définition du mode et de la taille du site web, et activation de la mise à l'échelle** | [Mise à l'échelle de sites web][websitescale]
**Activation des connexions permanentes de base de données** <p>Par défaut, WordPress n'utilise pas de connexions permanentes de base de données, qui peuvent ralentir les connexions à votre base de données après plusieurs connexions.</p>  | <ol><li><p>Edit the <strong>wp-includes/wp-db.php</strong> file.</p></li><li><p>Find the following line.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Replace the previous line with the following.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Find the following line.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Replace the above line with the following.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Save the file <strong>wp-includes/wp-db.php</strong> file and redeploy the site.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>REMARQUE :</h5><p>ces modifications peuvent être remplacées lorsque WordPress est mis à jour.</p><p>WordPress active les mises à jour automatiques par défaut, qui peuvent être désactivées en modifiant le fichier <strong>wp-config.php</strong> et en ajoutant <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Pour gérer les mises à jour, vous pouvez aussi utiliser une tâche web qui surveille le fichier <strong>wp-db.php</strong> et effectue les modifications ci-dessus chaque fois que le fichier est mis à jour. Consultez <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Présentation des tâches web</a> pour plus d'informations.</p></div>
**Amélioration des performances** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Désactiver le cookie ARR</a>  : cette opération peut améliorer les performances lorsque vous exécutez WordPress sur plusieurs instances de site web.</p></li><li><p>Activer la mise en cache. <a href="http://msdn.microsoft.com/fr-fr/library/azure/dn690470.aspx">Cache Redis</a> (version préliminaire) peut être utilisé avec le plug-in <a href="https://wordpress.org/plugins/redis-object-cache/">WordPress de cache d'objet Redis</a>ou l'une des autres offres de mise en cache <a href="http://azure.microsoft.com/fr-fr/gallery/store/">d'Azure Store</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Accélération de WordPress avec Wincache</a> - Wincache est activé par défaut pour les sites web</p></li><li><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-scale/">Mettez à l'échelle votre site web Azure</a> et utilisez <a href="http://www.cleardb.com/developers/cdbr/introduction">le routage ClearDB à haut niveau de disponibilité</a> ou <a href="http://www.mysql.com/products/cluster/">le cluster CGE MySQL</a></p></li></ul>
**Utilisation d'objets blob pour le stockage** | <ol><li><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/storage-create-storage-account/">Création d'un compte Azure Storage</a></p></li><li><p>Découvrez comment <a href="http://azure.microsoft.com/fr-fr/documentation/articles/cdn-how-to-use/">utiliser le Réseau de distribution de contenu (CDN)</a> pour distribuer géographiquement les données stockées dans des objets blob.</p></li><li><p>Installez et configurez <a href="https://wordpress.org/plugins/windows-azure-storage/">Azure Storage pour le plug-in WordPress</a>.</p><p>Pour des informations détaillées sur l'installation et la configuration du plug-in, consultez le <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guide d'utilisation</a>.</p> </li></ol>
**Activation du courrier électronique** | <ol><li><p><a href="http://azure.microsoft.com/fr-fr/gallery/store/sendgrid/sendgrid-azure/">Activation de SendGrid via le Magasin Azure</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Installation du plug-in SendGrid pour WordPress</a></p></li></ol>
**Configuration d'un nom de domaine personnalisé** | [Utiliser un nom de domaine personnalisé avec un site web Azure][customdomain]
**Activation de HTTPS pour un nom de domaine personnalisé** | [Utiliser HTTPS avec un site web Azure][httpscustomdomain]
**Équilibrage de la charge ou géodistribution de votre site** | [Router le trafic avec Azure Traffic Manager][trafficmanager]. Si vous utilisez un domaine personnalisé, consultez [Utilisation d'un nom de domaine personnalisé avec un site web Azure][customdomain] pour plus d'informations sur l'utilisation d'Azure Traffic Manager avec des noms de domaine personnalisés
**Activation des sauvegardes automatiques de sites web** | [Sauvegarder des sites web Azure][backup]
**Activation de la journalisation de diagnostics** | [Activer la journalisation pour les sites web][log]

##<a href="resources"></a>Ressources supplémentaires

* [Optimisation de WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Conversion d'un site WordPress en multisite](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-php-convert-wordpress-multisite/)

* [Assistant Mise à niveau de ClearDB pour Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hébergement de WordPress dans un sous-dossier pour votre site web Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Procédure étape par étape : Création d'un site WordPress avec Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Hébergement de votre blog WordPress existant sur Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Activation de liens permanents conviviaux dans WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Migration et exécution de votre blog WordPress Azure Web Sites](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Exécution gratuite de WordPress dans Azure Web Sites](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress sur Azure en moins de 2 minutes](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Déplacement d'un blog WordPress sur Azure - Partie 1 : Création d'un blog WordPress sur Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Déplacement d'un blog WordPress sur Azure - Partie 2 : Transfert de votre contenu](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Déplacement d'un blog WordPress sur Azure - Partie 3 : Paramétrage de votre domaine personnalisé](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Déplacement d'un blog WordPress sur Azure - Partie 4 : Liens permanents conviviaux et règles de réécriture d'URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Déplacement d'un blog WordPress sur Azure - Partie 5 : Déplacement d'un sous-dossier vers la racine](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Paramétrage d'un site web WordPress dans votre compte Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Soutenir WordPress sur Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Astuces pour WordPress sur Azure](http://www.johnpapa.net/azurecleardbmysql/)

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-php-configure/
[customdomain]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-custom-domain-name/
[trafficmanager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-backup/
[restore]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-restore/
[rediscache]: http://msdn.microsoft.com/fr-fr/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/fr-fr/library/azure/dn386122.aspx
[websitescale]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-scale/
[managedcachescale]: http://msdn.microsoft.com/fr-fr/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-staged-publishing/
[monitor]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-monitor/
[log]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-enable-diagnostic-log/
[httpscustomdomain]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-configure-ssl-certificate/
[mysqlwindows]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
[mysqllinux]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: https://azure.microsoft.com/fr-fr/pricing/details/web-sites/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-php-web-site-gallery/
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://manage.windowsazure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-deploy/
[posh]: http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/
[xplat-cli]: http://azure.microsoft.com/fr-fr/documentation/articles/xplat-cli/
[storesendgrid]: http://azure.microsoft.com/fr-fr/gallery/store/sendgrid/sendgrid-azure/
[cdn]: http://azure.microsoft.com/fr-fr/documentation/articles/cdn-how-to-use/

<!--HONumber=35_1-->
