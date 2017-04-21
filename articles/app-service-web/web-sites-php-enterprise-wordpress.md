---
title: "WordPress d’entreprise sur Azure | Microsoft Docs"
description: "Découvrez comment héberger un site WordPress d’entreprise sur Azure App Service"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 22d68588-2511-4600-8527-c518fede8978
ms.service: app-service-web
ms.devlang: php
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 541dd7bed5a4a9e306642b2c36dd7c9d45aec690
ms.openlocfilehash: fcaff51b2897b1b0bce8b1c503adfd9425c9fbb4
ms.lasthandoff: 01/24/2017


---
# <a name="enterprise-class-wordpress-on-azure"></a>WordPress en version entreprise sur Azure
Azure App Service fournit un environnement modulable, sécurisé et facile à utiliser pour les sites [WordPress][wordpress] stratégiques et à grande échelle. Microsoft gère également des sites d’entreprise tels que les blogs [Office][officeblog] et [Bing][bingblog]. Cet article vous indique comment utiliser la fonction Web Apps du service Microsoft Azure App Service pour établir et gérer un site WordPress d’entreprise basé sur le cloud, capable de gérer un grand nombre de visiteurs.

## <a name="architecture-and-planning"></a>Architecture et planification
Il y a seulement deux conditions requises pour l’installation WordPress de base :

* **Base de données MySQL** : cette spécification est disponible via [ClearDB dans Azure Marketplace][cdbnstore]. Comme alternative, vous pouvez gérer votre propre installation MySQL sur des machines virtuelles Azure, en utilisant [Windows][mysqlwindows] ou [Linux][mysqllinux].

  > [!NOTE]
  > ClearDB fournit plusieurs configurations MySQL. Chaque configuration présente des caractéristiques de performances différentes. Rendez-vous dans [Microsoft Azure Store][cdbnstore] pour en savoir plus sur les offres fournies dans Microsoft Azure Store, ou directement comme sur le [site web ClearDB](http://www.cleardb.com/pricing.view).
  >
  >
* **PHP 5.2.4 ou version ultérieure** : Azure App Service fournit actuellement [PHP 5.4, 5.5 et 5.6][phpwebsite].

  > [!NOTE]
  > Nous recommandons de toujours exécuter la dernière version de PHP, afin de vous assurer de disposer des derniers correctifs de sécurité.
  >
  >

### <a name="basic-deployment"></a>Déploiement basique
Vous pouvez créer une solution de base au sein d’une région Azure rien qu’avec les conditions requises de base.

![Une application web Azure et une base de données MySQL hébergées dans une seule région Azure][basic-diagram]

Tout est hébergé au sein des centres de données d’une région géographique spécifique, même si cela vous permet de créer plusieurs instances Web Apps du site afin d’augmenter la taille des instances de votre application. Lorsqu’ils utilisent ce site, les visiteurs qui n’appartiennent pas à cette région peuvent être confrontés à des temps de réponse très lents. Si les centres de données se trouvant dans cette région tombent en panne, l’application cesse de fonctionner.

### <a name="multi-region-deployment"></a>Déploiement multi-régions
Lorsque vous utilisez Azure [Traffic Manager][trafficmanager], vous pouvez mettre à l’échelle votre site WordPress sur différentes régions géographiques, tout en fournissant à tous les visiteurs une même URL. Tous les visiteurs passent par Traffic Manager et sont ensuite dirigés vers une région qui dépend de la configuration de l’équilibrage de charge.

![Une application web Azure, hébergée dans de nombreuses régions et qui utilise un routeur CDBR haute disponibilité pour assurer un acheminement vers MySQL à travers les régions][multi-region-diagram]

Au sein de chaque région, le site WordPress est toujours mis à l’échelle dans plusieurs instances Web Apps, mais cette mise à l’échelle est spécifique à la région. Les régions à fort trafic peuvent être mises à l’échelle différemment de celles qui présentent un trafic moins important.

Pour répliquer et acheminer le trafic vers plusieurs bases de données MySQL, vous pouvez utiliser des [routeurs haute disponibilité ClearDB (CDBR)] [ cleardbscale] (illustrés à gauche) ou [MySQL Cluster Carrier Grade Edition (CGE)][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Déploiement multi-régions avec stockage multimédia et mise en cache
Si le site accepte les chargements ou héberge des fichiers multimédias, utilisez le Stockage Blob Azure. S’il vous faut une mise en cache, pensez au [Cache Redis][rediscache], à [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), à [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/), ou à l’une des autres offres de mise en cache de [Microsoft Azure Store](http://azure.microsoft.com/gallery/store/).

![Une application web Azure, hébergée dans de nombreuses régions, utilisant un routeur CDBR haute disponibilité pour MySQL, avec le service de cache géré, le Stockage Blob et le réseau de distribution de contenu][performance-diagram]

Le stockage d’objets blob est par défaut géo-distribué dans les régions ; vous n’avez donc pas besoin de répliquer les fichiers sur tous les sites. Vous pouvez également activer le [réseau de distribution de contenu][cdn] Azure pour le Stockage Blob, qui distribue des fichiers à des nœuds finaux plus proches de vos visiteurs.

### <a name="planning"></a>Planification
#### <a name="additional-requirements"></a>Conditions supplémentaires
| Action à réaliser... | Élément à utiliser... |
| --- | --- |
| **Téléchargement ou stockage de fichiers volumineux** |[Plug-in WordPress pour l’utilisation du Stockage Blob][storageplugin] |
| **Envoi d’e-mail** |[SendGrid][storesendgrid] et le [plug-in WordPress pour l’utilisation de SendGrid][sendgridplugin] |
| **Noms de domaines personnalisés** |[Configuration d’un nom de domaine personnalisé dans Azure App Service][customdomain] |
| **HTTPS** |[Activation du protocole HTTPS pour une application web dans Azure App Service][httpscustomdomain] |
| **Validation de pré-production** |[Configurer des environnements intermédiaires pour les applications web dans Azure App Service][staging] <p>Lorsque vous déplacez une application web de l’environnement intermédiaire vers l’environnement de production, vous déplacez également la configuration de WordPress. Assurez-vous que tous les paramètres sont mis à jour en fonction des conditions requises pour votre application de production avant de passer de l’application en version intermédiaire vers l’environnement de production.</p> |
| **Surveillance et résolution de problèmes** |[Activation de la journalisation des diagnostics pour les applications web dans Azure App Service][log] et [Surveillance des applications web dans Azure App Service][monitor] |
| **Déploiement de votre site** |[Déployer une application web dans Azure App Service][deploy] |

#### <a name="availability-and-disaster-recovery"></a>Disponibilité et récupération d’urgence
| Action à réaliser... | Élément à utiliser... |
| --- | --- |
| **Sites d’équilibrage de charge** ou **sites de géo-distribution** |[Router le trafic avec Azure Traffic Manager][trafficmanager] |
| **Sauvegarder et restaurer** |[Sauvegarde d’une application web dans Azure App Service][backup] et [Restauration d’une application web dans Azure App Service][restore] |

#### <a name="performance"></a>Performances
Le système améliore les performances dans le cloud en tirant parti de deux technologies principales : la mise en cache et l’augmentation de la taille des instances. Toutefois, la mémoire, la bande passante et d’autres attributs d’hébergement Web Apps doivent être pris en compte.

| Action à réaliser... | Élément à utiliser... |
| --- | --- |
| **Compréhension des capacités des instances App Service** |[Détails sur la tarification, y compris les capacités des niveaux App Service][websitepricing] |
| **Ressources de cache** |[Cache Redis][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) ou l’une des autres offres de mise en cache de [Microsoft Azure Store](/gallery/store/) |
| **Mise en échelle de votre application** |[Mise à l’échelle d’une application web dans Azure App Service][websitescale] et [Routage haute disponibilité ClearDB][cleardbscale] Si vous choisissez d’héberger et de gérer votre propre installation MySQL, pensez au [cluster CGE MySQL][cge] pour l’augmentation de la taille des instances. |

#### <a name="migration"></a>Migration
Il existe deux méthodes permettant de migrer un site WordPress existant vers Azure App Service :

* **[Outil Exporter de WordPress][export]** : cette méthode permet d’exporter le contenu de votre blog. Vous pouvez ensuite importer le contenu vers un nouveau site WordPress sur Azure App Service à l’aide du [plug-in Importer de WordPress][import].

  > [!NOTE]
  > Ce processus vous permet de migrer votre contenu, mais non vos plug-ins, thèmes ou autres personnalisations. Vous devez installer ces composants à nouveau, manuellement.
  >
  >
* **Migration manuelle** : [sauvegardez votre site][wordpressbackup] et votre [base de données][wordpressdbbackup], puis restaurez-les manuellement sur une application web dans Azure App Service et la base de données MySQL associée. Cette méthode permet de migrer des sites très personnalisés ; elle vous évite de devoir installer manuellement des plug-ins, des thèmes et d’autres personnalisations.

## <a name="step-by-step-instructions"></a>Instructions pas à pas
### <a name="create-a-wordpress-site"></a>Créer un site WordPress
1. Utilisez [Azure Marketplace][cdbnstore] pour créer une base de données MySQL présentant la taille identifiée dans la section [Architecture et planning](#planning), dans la ou les régions où vous hébergerez votre site.
2. Suivez les étapes de la section [Créer une application web WordPress dans Azure App Service][createwordpress] pour créer une application web WordPress. Lors de la création de l’application web, sélectionnez **Utiliser une base de données MySQL existante**, puis choisissez la base de données que vous avez créée à l’étape 1.

Si vous migrez un site WordPress existant, consultez la section [Migration d’un site WordPress existant dans Azure](#Migrate-an-existing-WordPress-site-to-Azure) après la création d’une application web.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Migration d'un site WordPress existant dans Azure
Comme mentionné dans la section [Architecture et planification](#planning), il existe deux méthodes pour migrer un site WordPress :

* **exportation et importation** pour les sites sans grande personnalisation, ou pour lesquels vous voulez simplement déplacer le contenu ;
* **sauvegarde et restauration** pour les sites très personnalisés, dont vous voulez déplacer tous les éléments.

Utilisez l'une des sections suivantes pour migrer votre site.

#### <a name="the-export-and-import-method"></a>Méthode d'exportation et d'importation
1. Utilisez l’outil [Exporter de WordPress][export] pour exporter votre site existant.
2. Créez une application web en suivant les étapes de la section [Créer un site WordPress](#Create-a-new-WordPress-site).
3. Connectez-vous à votre site WordPress via le [portail Azure][mgmtportal], puis cliquez sur **Plug-ins** > **Ajouter nouveau**. Recherchez et installez le plug-in **Importer de WordPress**.
4. Une fois que le plug-in Importer est installé, cliquez sur **Outils** > **Importation**, puis sélectionnez **WordPress** pour utiliser l’extension Importer de WordPress.
5. Sur la page **Importation WordPress**, cliquez sur **Choisir un fichier**. Recherchez le fichier WXR exporté depuis votre site WordPress existant, puis cliquez sur **Chargement du fichier et importation**.
6. Cliquez sur **Submit**. Un message vous signale que l’importation est terminée.
7. Une fois que vous avez effectué toutes les étapes, redémarrez votre site depuis le panneau **App Services**, dans le [portail Azure][mgmtportal].

Après avoir importé le site, vous devrez peut-être suivre les étapes suivantes pour activer les paramètres non présents dans le fichier d’importation.

| Si vous utilisiez ceci... | Procédez comme suit... |
| --- | --- |
| **Permaliens** |À partir du tableau de bord WordPress du nouveau site, cliquez sur **Réglages** > **Permaliens**, puis mettez à jour la structure Permaliens. |
| **image/liens média** |Pour mettre à jour les liens vers le nouvel emplacement, utilisez le plug-in [Velvet Blues Update URLs][velvet] ou un outil de type rechercher/remplacer, ou effectuez cette opération manuellement dans votre base de données. |
| **Thèmes** |Cliquez sur **Apparence** > **Thème**, puis mettez à jour le thème du site comme vous le souhaitez. |
| **Menus** |Si votre thème prend en charge les menus, l’ancien sous-répertoire peut toujours être incorporé dans les liens vers votre page d’accueil. Cliquez sur **Apparence** > **Menus**, puis mettez-les à jour. |

#### <a name="the-backup-and-restore-method"></a>Méthode de sauvegarde et de restauration
1. Sauvegardez votre site WordPress existant à l’aide des informations sur les [sauvegardes WordPress][wordpressbackup].
2. Sauvegardez votre base de données existante en utilisant les informations de la section [Sauvegarde de votre base de données][wordpressdbbackup].
3. Créez une base de données et restaurez la sauvegarde.

   1. Achetez une nouvelle base de données dans [Azure Marketplace][cdbnstore] ou configurez une base de données MySQL sur une machine virtuelle [Windows][mysqlwindows] ou [Linux][mysqllinux].
   2. À l’aide d’un client MySQL comme [MySQL Workbench][workbench], connectez-vous à la nouvelle base de données et importez votre base de données WordPress.
   3. Mettez à jour la base de données pour modifier les entrées de domaine sur votre nouveau domaine Azure App Service, par exemple mywordpress.azurewebsites.net. Utilisez la fonction [Rechercher et remplacer du script de bases de données WordPress][searchandreplace] pour modifier de manière sécurisée toutes les instances.
4. Créez une application web sur le portail Azure et publiez la sauvegarde WordPress.

   1. Pour créer une application web incluant une base de données, accédez au [portail Azure][mgmtportal], cliquez sur **Nouveau** > **Web et mobilité** > **Azure Marketplace** > **Web Apps** > **Application web + SQL** (ou **Application web + MySQL**) > **Créer**. Définissez tous les paramètres requis pour créer une application web vierge.
   2. Dans votre sauvegarde WordPress, localisez le fichier **wp-config.php** et ouvrez-le dans un éditeur. Remplacez les entrées suivantes par les informations de votre nouvelle base de données MySQL :

      * **DB_NAME** : nom d’utilisateur de la base de données.
      * **DB_USER** : nom d’utilisateur permettant d’accéder à la base de données.
      * **DB_PASSWORD** : mot de passe de l’utilisateur.

        Après avoir modifié ces entrées, enregistrez et fermez le fichier **wp-config.php**.
   3. Utilisez les informations de la section [Déployer une application web dans Azure App Service][deploy] pour activer la méthode de déploiement que vous souhaitez utiliser, puis déployer votre sauvegarde WordPress vers votre application web dans Azure App Service.
5. Une fois le site WordPress déployé, vous devez pouvoir accéder au nouveau site (sous la forme d’une application web App Service) en utilisant l’URL *.azurewebsite.net du site.

### <a name="configure-your-site"></a>Configuration de votre site
Une fois que le site WordPress a été créé ou migré, utilisez les informations suivantes pour améliorer les performances ou activer des fonctionnalités supplémentaires.

| Action à réaliser... | Élément à utiliser... |
| --- | --- |
| **Définition du mode de plan d’App Service, la taille et activation de la mise à l'échelle** |[Faire évoluer une application web dans Azure App Service][websitescale] |
| **Activation des connexions permanentes de base de données** |Par défaut, WordPress n’utilise aucune connexion de base de données permanente, ce qui peut ralentir les connexions à votre base de données après plusieurs connexions. Pour permettre la mise en place de connexions persistantes, installez le [plug-in de l’adaptateur de connexions persistantes](https://wordpress.org/plugins/persistent-database-connection-updater/installation/). |
| **Amélioration des performances** |<ul><li><p><a href="https://azure.microsoft.com/en-us/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/">Désactiver le cookie ARR</a> : peut améliorer les performances lorsque le système WordPress est exécuté sur plusieurs instances Web Apps.</p></li><li><p>Activation de la mise en cache. Vous pouvez utiliser le <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Cache Redis</a> (version préliminaire) avec le <a href="https://wordpress.org/plugins/redis-object-cache/">plug-in WordPress Redis Object Cache</a> ou l’une des offres de mise en cache proposées dans <a href="/gallery/store/">Microsoft Azure Store</a>.</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Accélération de WordPress avec Wincache</a>. Wincache est activé par défaut pour les applications Web Apps.</p></li><li><p>[Mise à l’échelle d’une application dans Azure App Service][websitescale] et <a href="http://www.cleardb.com/developers/cdbr/introduction">Routage haute disponibilité ClearDB</a> ou <a href="http://www.mysql.com/products/cluster/">Cluster CGE MySQL</a>.</p></li></ul> |
| **Utilisation d’objets blob pour le stockage** |<ol><li><p>[Création d’un compte de stockage Azure](../storage/storage-create-storage-account.md).</p></li><li><p>Apprenez à [utiliser le réseau de distribution de contenu](../cdn/cdn-create-new-endpoint.md) pour géo-distribuer les données stockées dans les objets blob.</p></li><li><p>Installez et configurez le <a href="https://wordpress.org/plugins/windows-azure-storage/">Plug-in Azure Storage pour WordPress</a>.</p><p>Pour des informations détaillées sur l’installation et la configuration du plug-in, consultez le <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guide d’utilisation</a>.</p> </li></ol> |
| **Activation d’e-mail** |Activez <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> via Microsoft Azure Store. Installez le <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">plug-in SendGrid</a> pour WordPress. |
| **Configurer un nom de domaine personnalisé** |[Configurer un nom de domaine personnalisé dans Azure App Service][customdomain] |
| **Activation du protocole HTTPS pour un nom de domaine personnalisé** |[Activer le protocole HTTPS pour une application web dans Azure App Service][httpscustomdomain] |
| **Équilibrage de charge ou géo-distribution de votre site** |[Router le trafic avec Azure Traffic Manager][trafficmanager]. Si vous utilisez un domaine personnalisé, consultez la rubrique [Configuration d’un nom de domaine personnalisé dans Azure App Service][customdomain] pour en savoir plus sur l’utilisation de Traffic Manager avec des noms de domaine personnalisés. |
| **Activation des sauvegardes automatisées** |[Sauvegarde d’une application web dans Azur  App Service][backup] |
| **Activer la journalisation des diagnostics** |[Activer la journalisation des diagnostics pour les applications web dans Azure App Service][log] |

## <a name="next-steps"></a>Étapes suivantes
* [Optimisation de WordPress](http://codex.wordpress.org/WordPress_Optimization)
* [Conversion de WordPress en WordPress multisite dans Azure App Service](web-sites-php-convert-wordpress-multisite.md)
* [Assistant de mise à niveau ClearDB pour Azure](http://www.cleardb.com/store/azure/upgrade)
* [Hébergement de WordPress dans un sous-dossier de votre application web dans Azure App Service](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)
* [Pas à pas : création d’un site WordPress avec Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)
* [Hébergement de votre blog WordPress existant sur Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)
* [Activation de permaliens conviviaux dans WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)
* [Migration et exécution de votre blog WordPress dans Azure App Service](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)
* [Exécution gratuite de WordPress dans Azure App Service](http://architects.dzone.com/articles/how-run-wordpress-azure)
* [WordPress sur Azure en moins de 2 minutes](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)
* [Déplacement d'un blog WordPress vers Azure - partie 1 : création d'un blog WordPress sur Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)
* [Déplacement d’un blog WordPress vers Azure - Partie 2 : transfert de votre contenu](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)
* [Déplacement d'un blog WordPress vers Azure - partie 3 : configuration de votre domaine personnalisé](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)
* [Déplacement d'un blog WordPress vers Azure - partie 4 : règles de réécriture des permaliens et des URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)
* [Déplacement d'un blog WordPress vers Azure - partie 5 : déplacement d'un sous-dossier à la racine](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)
* [Configuration d’une application web dans votre compte Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)
* [Soutien de WordPress sur Azure](http://www.johnpapa.net/wordpress-on-azure/)
* [Astuces pour WordPress sur Azure](http://www.johnpapa.net/azurecleardbmysql/)

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant de créer un compte Azure, accédez à la page [Essayez Azure App Service](https://azure.microsoft.com/try/app-service/), qui vous permet de créer immédiatement une application web temporaire de démarrage dans App Service. Aucune carte de crédit n’est nécessaire ; vous ne prenez aucun engagement.
>
>

## <a name="whats-changed"></a>Changements apportés
Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, voir [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]:../virtual-machines/windows/classic/mysql-2008r2.md
[mysqllinux]:../virtual-machines/linux/classic/mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
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
[deploy]: web-sites-deploy.md
[posh]: /powershell/azureps-cmdlets-docs
[Azure CLI]:../cli-install-nodejs.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md

