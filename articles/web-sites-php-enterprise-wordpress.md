<properties title="Enterprise class WordPress on Azure Websites" pageTitle="Enterprise class WordPress on Azure Websites" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

## WordPress pour entreprise sur Sites Web Azure

Sites Web Azure fournit un environnement modulable, sécurisé et facile à utiliser pour sites [WordPress][WordPress] stratégiques à grande échelle. Microsoft exécute également des sites pour entreprise tels que les blogs [Office][Office] et [Bing][Bing]. Ce document vous montre comment utiliser Sites Web Azure pour établir et gérer un site WordPress pour entreprise basé sur le cloud capable de gérer un grand nombre de visiteurs.

## Dans cet article

-   [Architecture et planification][Architecture et planification] - Apprenez-en plus sur l'architecture, les conditions requises et les questions de performance avant de créer votre site

-   [Procédures][Procédures] - Créez, déployez et configurez votre site

-   [Autres ressources][Autres ressources] - Ressources et informations supplémentaires

## <span id="plan"></span></a>Architecture et planification

Il y a seulement deux conditions requises pour l'installation WordPress de base.

-   **Base de données MySQL** - disponible via [ClearDB dans le Magasin Azure][ClearDB dans le Magasin Azure] ; vous pouvez également gérer votre propre installation MySQL sur Azure Virtual Machines en utilisant [Windows][Windows] ou [Linux][Linux].

    > [WACOM.NOTE] ClearDB fournit plusieurs configurations MySQL, avec différentes caractéristiques de performance pour chaque configuration. Rendez-vous dans le [Magasin Azure][ClearDB dans le Magasin Azure] pour plus d'informations sur les offres disponibles ou sur [Tarifs ClearDB][Tarifs ClearDB] pour voir les offres directement sur ClearDB.

-   **PHP 5.2.4 ou supérieur** - Sites Web Azure fournit actuellement [les versions PHP 5.3, 5.4 et 5.5][les versions PHP 5.3, 5.4 et 5.5].

    > [WACOM.NOTE] Nous recommandons de toujours exécuter la dernière version de PHP pour vous assurer d'avoir les derniers correctifs de sécurité.

### Déploiement basique

Vous pouvez créer une solution de base au sein d'une région Azure rien qu'avec les conditions requises de base.

![un site web Azure et une base de données MySQL hébergés dans une seule région Azure][un site web Azure et une base de données MySQL hébergés dans une seule région Azure]

Même si vous pouvez monter en charge votre application en créant plusieurs instances du site web, tout est hébergé au sein des centres de données d'une région géographique spécifique. Les visiteurs à l'extérieur de cette région peuvent rencontrer des temps de réponse lents en utilisant le site. De plus, si les centres de données de cette région tombent en panne, votre application en subira les conséquences.

### Déploiement multi-régions

À l'aide d'Azure [Traffic Manager][Traffic Manager], il est possible de mettre à l'échelle votre site WordPress dans plusieurs régions géographiques tout en fournissant une URL unique aux visiteurs. Tous les visiteurs passent par Traffic Manager et sont ensuite dirigés vers une région selon la configuration de l'équilibrage de charge.

![un site web Azure, hébergé dans de nombreuses régions, utilisant un routeur CDBR haute disponibilité pour diriger vers MySQL à travers les régions][un site web Azure, hébergé dans de nombreuses régions, utilisant un routeur CDBR haute disponibilité pour diriger vers MySQL à travers les régions]

Au sein de chaque région, le site WordPress est toujours mis à l'échelle dans plusieurs instances du site web, mais cette mise à l'échelle est spécifique à la région ; les régions à fort trafic peuvent être mises à l'échelle différemment par rapport à celles qui ont un trafic moindre.

La réplication et le routage vers plusieurs bases de données MySQL peuvent être effectués à l'aide du [Routeur CDBR haute disponibilité][Routeur CDBR haute disponibilité] ClearDB (à gauche) ou du [Cluster CGE MySQL][Cluster CGE MySQL].

### Déploiement multi-régions avec stockage multimédia et mise en cache

Si le site accepte les chargements ou héberge des fichiers multimédias, utilisez le stockage d'objets blob Azure. S'il vous faut une mise en cache, pensez au [Cache Redis][Cache Redis], à [Memcache Cloud][Memcache Cloud], à [MemCachier][MemCachier] ou à l'une des autres offres de mise en cache du [Magasin Azure][Magasin Azure].

![un site web Azure, hébergé dans plusieurs régions, utilisant le routeur CDBR haute disponibilité pour MySQL, avec le service de cache géré, le stockage d'objets blob et le CDN][un site web Azure, hébergé dans plusieurs régions, utilisant le routeur CDBR haute disponibilité pour MySQL, avec le service de cache géré, le stockage d'objets blob et le CDN]

Le stockage d'objets blob est par défaut géo-distribué dans les régions ; vous n'avez donc pas besoin de répliquer les fichiers sur tous les sites. Vous pouvez également activer le [Réseau de distribution de contenu (CDN)][Réseau de distribution de contenu (CDN)] Azure pour le stockage d'objets blob, qui distribue des fichiers à des nœuds finaux plus proches de vos visiteurs.

### Planification

#### Conditions supplémentaires

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Action à réaliser...</th>
<th align="left">Élément à utiliser...</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Chargement ou stockage de fichiers volumineux</strong></td>
<td align="left"><a href="https://wordpress.org/plugins/windows-azure-storage/">Plug-in WordPress pour l'utilisation du stockage d'objets blob</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Envoi de courrier électronique</strong></td>
<td align="left"><a href="http://azure.microsoft.com/fr-fr/gallery/store/sendgrid/sendgrid-azure/">SendGrid</a> et le <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">plug-in WordPress pour l'utilisation de SendGrid</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Noms de domaine personnalisés</strong></td>
<td align="left"><a href="http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-custom-domain-name/">Noms de domaine personnalisés avec Sites Web Azure</a></td>
</tr>
<tr class="even">
<td align="left"><strong>HTTPS</strong></td>
<td align="left"><a href="http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-configure-ssl-certificate/">Prise en charge HTTPS dans Sites Web Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Validation de pré-production</strong></td>
<td align="left"><a href="http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-staged-publishing/">Support de publication intermédiaire pour Sites Web Azure</a>
<p>Remarque : passer un site de la version intermédiaire à la production modifie également la configuration de WordPress. Assurez-vous que tous les paramètres sont mis à jour en fonction des conditions requises pour votre site de production avant d'effectuer le basculement.</p></td>
</tr>
<tr class="even">
<td align="left"><strong>Surveillance et dépannage</strong></td>
<td align="left"><a href="http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-enable-diagnostic-log/">Journalisation de diagnostic avec Sites Web Azure</a> et <a href="http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-monitor/">Surveillance de Sites Web Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Déploiement de votre site</strong></td>
<td align="left"><a href="http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-deploy/">Déploiement d'un site web Azure</a></td>
</tr>
</tbody>
</table>

#### Disponibilité et récupération d'urgence

| Action à réaliser...                                               | Élément à utiliser...                                                     |
|--------------------------------------------------------------------|---------------------------------------------------------------------------|
| **Sites d'équilibrage de charge** ou **sites de géo-distribution** | [Router le trafic avec Azure Traffic Manager][Traffic Manager]            |
| **Sauvegarde et restauration**                                     | [Sauvegarde de sites web Azure][Sauvegarde de sites web Azure] et [Restauration d'un site web Azure][Restauration d'un site web Azure] |

#### Performances

La performance du cloud s'effectue premièrement via la mise en cache et la montée en charge ; toutefois, la mémoire, la bande passante et autres attributs de l'hébergement du site web doivent également être pris en considération.

| Action à réaliser...                                      | Élément à utiliser...                                                                                                                                                                                                                                    |
|-----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Compréhension des capacités des instances du site web** | [Détails sur les tarifs, incluant les capacités en termes de tailles et de modes pour les sites web][Détails sur les tarifs, incluant les capacités en termes de tailles et de modes pour les sites web]                                                                                                                                                   |
| **Ressources de cache**                                   | [Cache Redis][Cache Redis], [Memcache Cloud][Memcache Cloud], [MemCachier][MemCachier] ou l'une des autres offres de mise en cache du [Magasin Azure][Magasin Azure].                                                                                                                                    |
| **Mise en échelle de votre application**                  | [Mise à l'échelle d'un site web Azure][Mise à l'échelle d'un site web Azure] et [Routage ClearDB haute disponibilité][Routeur CDBR haute disponibilité]. Si vous choisissez d'héberger et de gérer votre propre installation MySQL, pensez au [Cluster CGE MySQL][Cluster CGE MySQL] pour la montée en charge |

#### Migration

Il existe deux méthodes pour migrer un site WordPress existant vers Sites Web Azure.

-   **[Exportation WordPress][Exportation WordPress]** - Exporte le contenu de votre blog, qui peut alors être importé vers un nouveau site WordPress sur Azure à l'aide du [plug-in d'importation WordPress][plug-in d'importation WordPress].

    > [WACOM.NOTE] Ce processus vous permet de migrer votre contenu, mais pas vos plug-ins, thèmes ou autres personnalisations. Ces éléments doivent être installés à nouveau manuellement.

-   **Migration manuelle** - [Sauvegardez votre site][Sauvegardez votre site] et sa [base de données][base de données], puis restaurez-le sur un site web Azure et sa base de données MySQL associée pour migrer des sites hautement personnalisés et éviter d'avoir à installer manuellement les plug-ins, les thèmes et autres personnalisations.

## Procédures

### <span id="create"></span></a>Création d'un site WordPress

1.  Utilisez le [Magasin Azure][ClearDB dans le Magasin Azure] pour créer une base de données MySQL de la taille identifiée dans la section [Architecture et planification][Architecture et planification], dans la ou les régions où vous hébergez votre site.

2.  Suivez les étapes indiquées à la page [Création d'un site web WordPress à partir de la galerie dans Azure][Création d'un site web WordPress à partir de la galerie dans Azure] pour créer un site WordPress. Lorsque vous créez le site, sélectionnez **Utiliser une base de données MySQL existante** et choisissez la base de données créée à l'étape 1.

Si vous migrez un site WordPress existant, consultez la section [Migration d'un site WordPress existant][Migration d'un site WordPress existant] après avoir créé un site.

### <span id="migrate"></span></a>Migration d'un site WordPress existant sur Azure

Comme mentionné dans la section [Architecture et planification][Architecture et planification], il existe deux méthodes pour migrer un site web WordPress.

-   **Exportation et importation** - Pour les sites sans grande personnalisation, ou pour lesquels vous voulez simplement déplacer le contenu.

-   **Sauvegarde et restauration** - Pour les sites très personnalisés pour lesquels vous voulez tout déplacer.

Utilisez l'une des sections suivantes pour migrer votre site.

#### Méthode d'exportation et d'importation

1.  Utilisez l'outil [Exporter de WordPress][Exportation WordPress] pour exporter votre site existant.

2.  Créez un site web en suivant les étapes de la section [Création d'un site WordPress][Création d'un site WordPress].

3.  Connectez-vous à votre site WordPress sur Sites Web Azure et cliquez sur **Extensions** -\> **Ajouter**. Recherchez et installez l'extension **Importer de WordPress**.

4.  Une fois que l'extension Importer est installée, cliquez sur **Outils** -\> **Importation**, puis sélectionnez **WordPress** pour utiliser l'extension Importer de WordPress.

5.  Sur la page **Importation WordPress**, cliquez sur **Choisir un fichier**. Recherchez le fichier WXR exporté de votre site WordPress existant, puis sélectionnez **Chargement du fichier et importation**.

6.  Cliquez sur **Envoyer**. Un message vous signalera que l'importation est terminée.

7.  Une fois que vous avez effectué toutes ces étapes, redémarrez votre site web à partir de son tableau de bord sur le [portail de gestion Azure][portail de gestion Azure].

Après avoir importé le site, vous devez suivre les étapes suivantes pour activer les paramètres non présents dans le fichier d'importation.

| Si vous utilisiez ceci... | Procédez comme suit...                                                                                                                                                                                        |
|---------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Permaliens**            | À partir du tableau de bord WordPress du nouveau site, cliquez sur **Réglages** -\> **Permaliens**, puis mettez à jour la structure Permaliens.                                                               |
| **image/liens média**     | Pour mettre à jour les liens vers le nouvel emplacement, utilisez le [plug-in Velvet Blues Update URLs][plug-in Velvet Blues Update URLs], un outil Rechercher et remplacer ou procédez manuellement dans votre base de données.              |
| **Thèmes**                | Rendez-vous dans **Apparence** -\> **Thème** et mettez à jour le thème du site web à votre guise.                                                                                                             |
| **Menus**                 | Si votre thème prend en charge les menus, les liens vers votre page d'accueil peuvent toujours avoir l'ancien sous-répertoire d'incorporé. Rendez-vous dans **Apparence** -\> **Menus** et mettez-les à jour. |

#### Méthode de sauvegarde et de restauration

1.  Sauvegardez votre site WordPress existant à l'aide des informations sur les [Sauvegardes WordPress][Sauvegardez votre site].

2.  Sauvegardez votre base de données existante en utilisant les informations de la page [Sauvegarde de votre base de données][base de données].

3.  Créez une base de données et restaurez la sauvegarde.

    1.  Achetez une nouvelle base de données dans le [Magasin Azure][ClearDB dans le Magasin Azure] ou configurez une base de données MySQL sur une machine virtuelle [Windows][Windows] ou [Linux][Linux].

    2.  À l'aide d'un client MySQL comme [MySQL Workbench][MySQL Workbench], connectez-vous à la nouvelle base de données et importez votre base de données WordPress.

    3.  Mettez à jour la base de données pour modifier les entrées de domaine sur votre nouveau domaine de site web Azure. Par exemple, mywordpress.azurewebsites.net. Utilisez l'outil [Rechercher et remplacer du script de bases de données WordPress][Rechercher et remplacer du script de bases de données WordPress] pour modifier toutes les instances en toute sécurité.

4.  Créez un site web et publiez la sauvegarde WordPress.

    1.  Créez un site web sur le [portail de gestion Azure][portail de gestion Azure] avec une base de données en utilisant **Nouveau** -\> **Site web** -\> **Création personnalisée**. Un site vide sera alors créé.

    2.  Dans votre sauvegarde WordPress, localisez le fichier **wp-config.php** et ouvrez-le avec un éditeur. Remplacez les entrées suivantes avec les informations de votre nouvelle base de données MySQL.

        -   **DB\_NAME** - Nom d'utilisateur de la base de données

        -   **DB\_USER** - Nom d'utilisateur pour accéder à la base de données

        -   **DB\_PASSWORD** - Mot de passe de l'utilisateur

        Après avoir modifié ces entrées, enregistrez et fermez le fichier **wp-config.php**.

    3.  Utilisez les informations sur le [Déploiement d'un site web Azure][Déploiement d'un site web Azure] pour activer la méthode de déploiement que vous souhaitez utiliser, puis déployer votre sauvegarde WordPress vers votre site web Azure.

5.  Une fois que le site WordPress a été déployé, vous devez pouvoir accéder au nouveau site en utilisant l'URL \*.azurewebsite.net pour le site.

### Configuration de votre site

Une fois que le site WordPress a été créé ou migré, utilisez les informations suivantes pour améliorer les performances ou activer des fonctionnalités supplémentaires.

Action à réaliser... | Élément à utiliser...
------------- | -----------
**Définition du mode de site web, de la taille et activation de la mise à l'échelle** | [Mise à l'échelle de sites web](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-scale/)
**Activation des connexions permanentes de base de données** <p>Par défaut, WordPress n'utilise pas de connexions permanentes de base de données, qui peuvent ralentir les connexions à votre base de données après plusieurs connexions.</p>  | <ol><li><p>Modifiez le fichier <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Recherchez la ligne suivante :</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></p></li><li><p>Remplacez la ligne précédente par celle-ci :</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></p></li><li><p>Recherchez la ligne suivante :<p><code>$this-&gt;dbh = @mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags ); </code></li><li><p>Remplacez la ligne ci-dessus par celle-ci :<p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Enregistrez le fichier <strong>wp-includes/wp-db.php</strong> et redéployez le site.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>REMARQUE&nbsp;:</h5><p>ces modifications peuvent &ecirc;tre &eacute;cras&eacute;es lorsque WordPress est mis &agrave; jour.</p><p>WordPress active les mises &agrave; jour automatiques par d&eacute;faut, qui peuvent &ecirc;tre d&eacute;sactiv&eacute;es en modifiant le fichier <strong>wp-config.php</strong> et en ajoutant <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Pour g&eacute;rer les mises &agrave; jour, vous pouvez &eacute;galement utiliser une t&acirc;che web qui surveille le fichier <strong>wp-db.php</strong> et ex&eacute;cute les modifications ci-dessus chaque fois que le fichier est mis &agrave; jour. Consultez <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Pr&eacute;sentation des t&acirc;ches web</a> pour plus d'informations.</p></div>
**Performances améliorées** | <ul><li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Désactivation du cookie ARR</a> - Peut améliorer les performances lorsque vous exécutez WordPress sur plusieurs instances de site web.</p></li><li><p>Active la mise en cache. <a href="http://msdn.microsoft.com/fr-fr/library/azure/dn690470.aspx">Cache Redis</a> (version préliminaire) peut être utilisé avec le <a href="https://wordpress.org/plugins/redis-object-cache/">Plug-in WordPress Redis object cache</a> ou l'une des offres de mise en cache proposées dans le <a href="http://azure.microsoft.com/fr-fr/gallery/store/">Magasin Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Comment rendre WordPress plus rapide avec Wincache</a> - Wincache est activé par défaut pour les sites web</p></li><li><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-scale/">Mettez votre site web Azure à l'échelle</a> et utilisez le <a href="http://www.cleardb.com/developers/cdbr/introduction">Routeur ClearDB haute disponibilité</a> ou le <a href="http://www.mysql.com/products/cluster/">Cluster CGE MySQL</a></p></li></ul>
**Utilisation d'objets blob pour le stockage** | <ol><li><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/storage-create-storage-account/">Création d'un compte Azure Storage</a></p></li><li><p>Apprenez comment <a href="http://azure.microsoft.com/fr-fr/documentation/articles/cdn-how-to-use/">Utiliser le réseau de distribution de contenu (CDN)</a> pour géo-distribuer les données stockées dans les objets blob.</p></li><li><p>Installez et configurez le <a href="https://wordpress.org/plugins/windows-azure-storage/">Plug-in Azure Storage pour WordPress</a>.</p><p>Pour des informations détaillées sur l'installation et la configuration du plug-in, consultez le <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guide d'utilisation</a>.</p> </li></ol>
**Activation du courrier électronique** | <ol><li><p><a href="http://azure.microsoft.com/fr-fr/gallery/store/sendgrid/sendgrid-azure/">Activation de SendGrid via le Magasin Azure</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Installation du plug-in SendGrid pour WordPress</a></p></li></ol>
**Configuration d'un nom de domaine personnalisé** | [Utilisation d'un nom de domaine personnalisé avec un site web Azure](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-custom-domain-name/)
**Activation du protocole HTTPS pour un nom de domaine personnalisé** | [Utilisation du protocole HTTPS pour un site web Azure](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-configure-ssl-certificate/)
**Équilibrage de charge ou géo-distribution de votre site** | [Router le trafic avec Azure Traffic Manager](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/). Si vous utilisez un domaine personnalisé, consultez [Utilisation d'un nom de domaine personnalisé avec un site web Azure](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-custom-domain-name/) pour plus d'informations sur l'utilisation de Traffic Manager avec des noms de domaine personnalisés.
**Activation des sauvegardes de site web automatisées** | [Sauvegarde de sites web Azure](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-backup/)
**Activation de la journalisation de diagnostic** | [Activation de la journalisation de diagnostic pour les sites web](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-enable-diagnostic-log/)

## [][]Ressources supplémentaires

-   [Optimisation de WordPress][Optimisation de WordPress]

-   [Conversion d'un site WordPress en multisite][Conversion d'un site WordPress en multisite]

-   [Assistant Mise à niveau ClearDB pour Azure][Assistant Mise à niveau ClearDB pour Azure]

-   [Hébergement de WordPress dans un sous-dossier pour votre site web Azure][Hébergement de WordPress dans un sous-dossier pour votre site web Azure]

-   [Étape par étape : Création d'un site WordPress avec Azure][Étape par étape : Création d'un site WordPress avec Azure]

-   [Hébergement de votre blog WordPress existant sur Azure][Hébergement de votre blog WordPress existant sur Azure]

-   [Activation de permaliens conviviaux dans WordPress][Activation de permaliens conviviaux dans WordPress]

-   [Migration et exécution de votre blog WordPress sur Sites Web Azure][Migration et exécution de votre blog WordPress sur Sites Web Azure]

-   [Exécution gratuite de WordPress sur Sites Web Azure][Exécution gratuite de WordPress sur Sites Web Azure]

-   [WordPress sur Azure en moins de 2 minutes][WordPress sur Azure en moins de 2 minutes]

-   [Déplacement d'un blog WordPress sur Azure - Partie 1 : Création d'un blog WordPress sur Azure][Déplacement d'un blog WordPress sur Azure - Partie 1 : Création d'un blog WordPress sur Azure]

-   [Déplacement d'un blog WordPress sur Azure - Partie 2 : Transfert de votre contenu][Déplacement d'un blog WordPress sur Azure - Partie 2 : Transfert de votre contenu]

-   [Déplacement d'un blog WordPress sur Azure - Partie 3 : Paramétrage de votre domaine personnalisé][Déplacement d'un blog WordPress sur Azure - Partie 3 : Paramétrage de votre domaine personnalisé]

-   [Déplacement d'un blog WordPress sur Azure - Partie 4 : permaliens conviviaux et règles de réécriture d'URL][Déplacement d'un blog WordPress sur Azure - Partie 4 : permaliens conviviaux et règles de réécriture d'URL]

-   [Déplacement d'un blog WordPress sur Azure - Partie 5 : Passage d'un sous-dossier à la racine][Déplacement d'un blog WordPress sur Azure - Partie 5 : Passage d'un sous-dossier à la racine]

-   [Paramétrage d'un site web WordPress sur votre compte Azure][Paramétrage d'un site web WordPress sur votre compte Azure]

-   [Soutenir WordPress sur Azure][Soutenir WordPress sur Azure]

-   [Astuces pour WordPress sur Azure][Astuces pour WordPress sur Azure]

  [WordPress]: http://www.microsoft.com/web/wordpress
  [Office]: http://blogs.office.com/
  [Bing]: http://blogs.bing.com/
  [Architecture et planification]: #planning
  [Procédures]: #howto
  [Autres ressources]: #resources
  [ClearDB dans le Magasin Azure]: http://www.cleardb.com/store/azure
  [Windows]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
  [Linux]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
  [Tarifs ClearDB]: http://www.cleardb.com/pricing.view
  [les versions PHP 5.3, 5.4 et 5.5]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-php-configure/
  [un site web Azure et une base de données MySQL hébergés dans une seule région Azure]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
  [Traffic Manager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
  [un site web Azure, hébergé dans de nombreuses régions, utilisant un routeur CDBR haute disponibilité pour diriger vers MySQL à travers les régions]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
  [Routeur CDBR haute disponibilité]: http://www.cleardb.com/developers/cdbr/introduction
  [Cluster CGE MySQL]: http://www.mysql.com/products/cluster/
  [Cache Redis]: http://msdn.microsoft.com/fr-fr/library/azure/dn690470.aspx
  [Memcache Cloud]: http://azure.microsoft.com/fr-fr/gallery/store/garantiadata/memcached/
  [MemCachier]: http://azure.microsoft.com/fr-fr/gallery/store/memcachier/memcachier/
  [Magasin Azure]: http://azure.microsoft.com/fr-fr/gallery/store/
  [un site web Azure, hébergé dans plusieurs régions, utilisant le routeur CDBR haute disponibilité pour MySQL, avec le service de cache géré, le stockage d'objets blob et le CDN]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
  [Réseau de distribution de contenu (CDN)]: http://azure.microsoft.com/fr-fr/documentation/articles/cdn-how-to-use/
  [Plug-in WordPress pour l'utilisation du stockage d'objets blob]: https://wordpress.org/plugins/windows-azure-storage/
  [SendGrid]: http://azure.microsoft.com/fr-fr/gallery/store/sendgrid/sendgrid-azure/
  [plug-in WordPress pour l'utilisation de SendGrid]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
  [Noms de domaine personnalisés avec Sites Web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-custom-domain-name/
  [Prise en charge HTTPS dans Sites Web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-configure-ssl-certificate/
  [Support de publication intermédiaire pour Sites Web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-staged-publishing/
  [Journalisation de diagnostic avec Sites Web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-enable-diagnostic-log/
  [Surveillance de Sites Web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-monitor/
  [Déploiement d'un site web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-deploy/
  [Sauvegarde de sites web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-backup/
  [Restauration d'un site web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-restore/
  [Détails sur les tarifs, incluant les capacités en termes de tailles et de modes pour les sites web]: https://azure.microsoft.com/fr-fr/pricing/details/web-sites/
  [Mise à l'échelle d'un site web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-scale/
  [Exportation WordPress]: http://en.support.wordpress.com/export/
  [plug-in d'importation WordPress]: http://wordpress.org/plugins/wordpress-importer/
  [Sauvegardez votre site]: http://codex.wordpress.org/WordPress_Backups
  [base de données]: http://codex.wordpress.org/Backing_Up_Your_Database
  [Création d'un site web WordPress à partir de la galerie dans Azure]:  "http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-php-web-site-gallery/"
  [Migration d'un site WordPress existant]: #migrate
  [Création d'un site WordPress]: #create
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [plug-in Velvet Blues Update URLs]: https://wordpress.org/plugins/velvet-blues-update-urls/
  [MySQL Workbench]: http://www.mysql.com/products/workbench/
  [Rechercher et remplacer du script de bases de données WordPress]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
  [Présentation des tâches web]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
  [Désactivation du cookie ARR]: http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx
  [Plug-in WordPress Redis object cache]: https://wordpress.org/plugins/redis-object-cache/
  [Comment rendre WordPress plus rapide avec Wincache]: http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/
  [Création d'un compte Azure Storage]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-create-storage-account/
  [guide d'utilisation]: http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx
  []: resources
  [Optimisation de WordPress]: http://codex.wordpress.org/WordPress_Optimization
  [Conversion d'un site WordPress en multisite]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-php-convert-wordpress-multisite/
  [Assistant Mise à niveau ClearDB pour Azure]: http://www.cleardb.com/store/azure/upgrade
  [Hébergement de WordPress dans un sous-dossier pour votre site web Azure]: http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx
  [Étape par étape : Création d'un site WordPress avec Azure]: http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx
  [Hébergement de votre blog WordPress existant sur Azure]: http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx
  [Activation de permaliens conviviaux dans WordPress]: http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress
  [Migration et exécution de votre blog WordPress sur Sites Web Azure]: http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/
  [Exécution gratuite de WordPress sur Sites Web Azure]: http://architects.dzone.com/articles/how-run-wordpress-azure
  [WordPress sur Azure en moins de 2 minutes]: http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/
  [Déplacement d'un blog WordPress sur Azure - Partie 1 : Création d'un blog WordPress sur Azure]: http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1
  [Déplacement d'un blog WordPress sur Azure - Partie 2 : Transfert de votre contenu]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content
  [Déplacement d'un blog WordPress sur Azure - Partie 3 : Paramétrage de votre domaine personnalisé]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain
  [Déplacement d'un blog WordPress sur Azure - Partie 4 : permaliens conviviaux et règles de réécriture d'URL]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules
  [Déplacement d'un blog WordPress sur Azure - Partie 5 : Passage d'un sous-dossier à la racine]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root
  [Paramétrage d'un site web WordPress sur votre compte Azure]: http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/
  [Soutenir WordPress sur Azure]: http://www.johnpapa.net/wordpress-on-azure/
  [Astuces pour WordPress sur Azure]: http://www.johnpapa.net/azurecleardbmysql/
