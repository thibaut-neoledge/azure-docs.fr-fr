<properties linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="How to create websites - Azure service management" metaKeywords="Azure creating website, Azure deleting website" description="Learn how to create a website using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Création d'un site web

Cette rubrique explique comment créer un site web à partir de la galerie ou du portail de gestion.

Pour plus d'informations sur le déploiement de vos contenus vers un site web Azure que vous avez créé, consultez la section **Déploiement** dans [Azure Web Sites][Azure Web Sites].

## Sommaire

-   [création d'un site web à l'aide du portail de gestion][création d'un site web à l'aide du portail de gestion]
-   [création d'un site web à partir de la galerie][création d'un site web à partir de la galerie]
-   [suppression d'un site web][suppression d'un site web]
-   [Étapes suivantes][Étapes suivantes]

## <a name="createawebsiteportal"></a> création d'un site web à l'aide du portail de gestion

Pour créer un site web dans Azure, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Cliquez sur l’icône **Create New** dans le coin inférieur gauche du portail de gestion.

3.  Cliquez sur l'icône **Site Web**, puis sur l'icône **Création rapide**, entrez une valeur pour l'URL, puis cliquez sur la coche en regard de **Créer un site Web** dans le coin inférieur droit de la page.

4.  Une fois le site web créé, le texte **La création du site Web \<*nom du site web*\> a réussi** s'affiche. Vous pouvez accéder au site web en cliquant sur **Parcourir** en bas de la page du portail.

5.  Dans le portail, cliquez sur le nom du site web affiché dans la liste des sites web pour ouvrir la page de gestion **Démarrage rapide** du site web.

6.  La page **Démarrage rapide** inclut plusieurs options permettant d'obtenir des outils de développement de site web, de configurer une publication pour votre site web ou un déploiement à partir d'un fournisseur de contrôle de code source tel que TFS ou Git. La publication FTP est configurée par défaut pour les sites web. Par ailleurs, le nom d'hôte FTP est affiché dans la section **Aperçu rapide** de la page **Tableau de bord** sous **Nom de l'hôte FTP**. Avant la publication avec FTP ou Git, choisissez l'option **Réinitialiser les informations d'identification du déploiement** dans la page **Tableau de bord** pour pouvoir vous authentifier auprès de l'hôte FTP ou du référentiel Git durant le déploiement de contenus vers votre site web.

7.  La page de gestion **Configurer** présente les paramètres de votre site web dans les catégories suivantes :

 - **Général** : définissez la version de .NET framework ou de PHP requise par votre application Web. Pour les sites en mode Standard, l’option **Plateforme** vous permet de choisir entre un environnement 32 bits ou 64 bits.

- **Certificats** : en mode Standard, vous pouvez télécharger un certificat SSL pour des domaines personnalisés.

- **Noms de domaine** : dans les modes Standard et Partagé, vous pouvez afficher et gérer des noms de domaine personnalisés pour votre site web.

- **Liaisons SSL** : en mode Standard, vous pouvez attribuer des certificats SSL à vos noms de domaines personnalisés en utilisant une liaison SSL IP ou SNI.

 - **Déploiements** : ici, vous pouvez configurer un déploiement à partir d’un contrôle de code source.

 - **Diagnostics d'application** : configurez les options de la collecte de traces de diagnostic à partir d’une application Web instrumentée avec des suivis.

- **Diagnostics de site** : définissez les options de journalisation pour la collecte d'informations de diagnostic pour votre site web. Les options incluent la journalisation de serveur Web, les messages d’erreur détaillés et le suivi des demandes ayant échoué.

- **Surveillance** : cette fonctionnalité permet de tester la disponibilité des points de terminaison HTTP ou HTTPS pour les sites web en mode Standard.

- **Paramètres d'application** : indiquez les paires nom/valeur qui seront chargées par votre application Web au démarrage. Pour les sites .NET, ces paramètres seront inclus dans votre configuration .NET **AppSettings** au moment de l’exécution, en remplacement des paramètres existants. Pour les sites PHP et Node, ces paramètres sont disponibles en tant que variables d’environnement au moment de l’exécution.

 - **Chaînes de connexion** : affichez et modifiez les chaînes de connexion. Pour les sites .NET, ces chaînes de connexion seront incluses dans les paramètres **connectionStrings** de votre configuration .NET au moment de l’exécution, en remplacement des entrées existantes où la clé est identique à celle du nom de la base de données liée. Pour les sites PHP et Node, ces paramètres sont disponibles en tant que variables d’environnement au moment de l’exécution.

 - **Documents par défaut** : un document par défaut de site web correspond à la page affichée par défaut quand un utilisateur accède à un site web. Ajoutez le document par défaut de votre application Web à cette liste, si ce n’est pas déjà fait. Le document par défaut de votre site web doit être situé en haut de la liste.

- **Mappages de gestionnaires** : indiquez les processeurs de script qui gèreront les demandes pour les extensions de fichier spécifiques telles que \*.php.

## <a name="howtocreatefromgallery"></a> création d'un site web à partir de la galerie

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

## <a name="deleteawebsite"></a> suppression d'un site

Pour supprimer un site web, utilisez l'icône **Supprimer** du portail de gestion Azure. L'icône **Supprimer** est disponible sur le portail Azure quand vous cliquez sur **Sites Web** pour répertorier tous vos sites web, ainsi qu'en bas de chaque page de gestion de site web.

## <a name="nextsteps"></a>Étapes suivantes

Pour plus d’informations, consultez la page [Sites Web Azure][Azure Web Sites].

  [Azure Web Sites]: /fr-fr/documentation/services/web-sites/
  [création d'un site web à l'aide du portail de gestion]: #createawebsiteportal
  [création d'un site web à partir de la galerie]: #howtocreatefromgallery
  [suppression d'un site web]: #deleteawebsite
  [Étapes suivantes]: #nextsteps
  [portail de gestion Azure]: http://manage.windowsazure.com/
  [website-from-gallery]: ../includes/website-from-gallery.md
