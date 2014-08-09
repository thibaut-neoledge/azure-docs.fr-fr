<properties  linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="How to create web sites - Azure service management" metaKeywords="Azure creating web site, Azure deleting website" description="Learn how to create a web site using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Web Site" authors="timamm" solutions="" manager="" editor="" />

# Création d'un site Web

Cette rubrique explique comment créer un site Web à partir de la galerie ou du portail de gestion.

Pour plus d'informations sur le déploiement de vos contenus vers un site Web Azure que vous avez créé, consultez la section **Déploiement** de la rubrique [Sites Web Azure](/fr-fr/documentation/services/web-sites/).

## Sommaire

* [Création d'un site Web à l'aide du portail de gestion](#createawebsiteportal)
* [Création d'un site Web à partir de la galerie](#howtocreatefromgallery)
* [Suppression d'un site Web](#deleteawebsite)
* [Étapes suivantes](#nextsteps)

## <a name="createawebsiteportal"></a>Création d'un site Web à l'aide du portail de gestion

Pour créer un site Web dans Azure, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][1].

2.  Cliquez sur l'icône **Create New** dans le coin inférieur gauche du portail de gestion.

3.  Cliquez sur l'icône **Web Site**, puis sur l'icône **Quick Create**, entrez une valeur pour l'URL, puis cliquez sur la coche affichée en regard de **Create Web Site** dans le coin inférieur droit de la page.

4.  Une fois le site Web créé, le texte **Creating web site <*nom du site Web*> succeeded** s'affiche. Vous pouvez accéder au site Web en cliquant sur **Browse** en bas de la page du portail.

5.  Dans le portail, cliquez sur le nom du site Web affiché dans la liste des sites Web pour ouvrir la page de gestion **Démarrage rapide** du site Web.

6.  La page **Quick Start** inclut plusieurs options permettant d'obtenir des outils de développement de site Web, de configurer une publication pour votre site Web ou un déploiement à partir d'un fournisseur de contrôle de code source tel que TFS ou Git. La publication FTP est configurée par défaut pour les sites Web et le nom d'hôte FTP est affiché dans la section **Quick Glance** de la page **Dashboard** sous **FTP Host Name**. Avant la publication avec FTP ou Git, sélectionnez l'option **Reset deployment credentials** sur la page **Dashboard** pour pouvoir vous authentifier auprès de l'hôte FTP ou du référentiel Git lors du déploiement de contenus vers votre site Web.

7.  La page de gestion **Configure** présente les paramètres de votre site Web dans les catégories suivantes :

* **General** : définissez la version de .NET framework ou de PHP requise par votre application Web. Pour les sites en mode Standard, l'option **Platform** vous permet de choisir entre un environnement 32 bits ou 64 bits.

* **Certificates** : en mode Standard, vous pouvez télécharger un certificat SSL pour des domaines personnalisés.

* **Domain Names** : dans les modes Standard et Partagé, vous pouvez afficher et gérer des noms de domaines personnalisés pour votre site Web.

* **SSL Bindings** : en mode Standard, vous pouvez attribuer des certificats SSL à vos noms de domaines personnalisés en utilisant une liaison SSL IP ou SNI.

* **Deployments** : ici, vous pouvez configurer un déploiement à partir d'un contrôle de code source.

* **Application Diagnostics** : configurez les options de la collecte de traces de diagnostic à partir d'une application Web instrumentée avec des suivis.

* **Site Diagnostics** : définissez les options de journalisation pour la collecte d'informations de diagnostic pour votre site Web. Les options incluent la journalisation de serveur Web, les messages d'erreur détaillés et le suivi des demandes ayant échoué.

* **Monitoring** : cette fonctionnalité permet de tester la disponibilité des points de terminaison HTTP ou HTTPS pour les sites Web en mode Standard.

* **App Settings** : indiquez les paires nom/valeur qui seront chargées par votre application Web au démarrage. Pour les sites .NET, ces paramètres seront inclus dans votre configuration .NET **AppSettings** au moment de l'exécution, en remplacement des paramètres existants. Pour les sites PHP et Node, ces paramètres sont disponibles en tant que variables d'environnement au moment de l'exécution.

* **Connection Strings** : affichez et modifiez les chaînes de connexion. Pour les sites .NET, ces chaînes de connexion seront incluses dans les paramètres **connectionStrings** de votre configuration .NET au moment de l'exécution, en remplacement des entrées existantes où la clé est identique à celle du nom de la base de données liée. Pour les sites PHP et Node, ces paramètres sont disponibles en tant que variables d'environnement au moment de l'exécution.

* **Default Documents** : un document par défaut de site Web correspond à la page affichée par défaut lorsqu'un utilisateur accède à un site Web. Ajoutez le document par défaut de votre application Web à cette liste, si ce n'est pas déjà fait. Le document par défaut de votre site Web doit être situé en haut de la liste.

* **Handler Mappings** : indiquez les processeurs de script qui gèreront les demandes pour les extensions de fichier spécifiques telles que \*.php.

## <a name="howtocreatefromgallery"></a>Création d'un site Web à partir de la galerie

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

## <a name="deleteawebsite"></a>Suppression d'un site Web

Pour supprimer un site Web, utilisez l'icône **Delete** du portail de gestion Azure. L'icône **Delete** est disponible sur le portail Azure lorsque vous cliquez sur **Web Sites** pour répertorier tous vos sites Web, ainsi qu'en bas de chaque page de gestion de site Web.

## <a name="nextsteps"></a>Étapes suivantes

Pour plus d'informations, consultez la page [Sites Web Azure](/fr-fr/documentation/services/web-sites/).



[1]: http://manage.windowsazure.com/