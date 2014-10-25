<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Website from Gallery" pageTitle="Create an Orchard CMS website from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new website in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Créer un site web Orchard CMS à partir de la galerie dans Azure

La galerie met à votre disposition une large gamme d’applications Web développées par Microsoft, par des sociétés tierces ou par des initiatives de logiciel open source. Les applications Web créées à partir de la galerie ne requièrent pas l'installation de logiciel autre que le navigateur Web utilisé pour se connecter au [portail de gestion Azure][]. Pour plus d'informations sur les applications Web dans la galerie, consultez la page [Galerie d'applications Web Windows][].

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer un site via la galerie ;

-   lancer et gérer votre site depuis le portail de gestion.

Vous allez configurer un site Orchard CMS avec un modèle par défaut. [Orchard][] est une application CMS gratuite, open source, basée sur .NET, qui vous permet de créer des sites web personnalisés et orientés contenu. Orchard CMS comprend une infrastructure d'extensibilité qui vous permet de [télécharger des thèmes et modules complémentaires][] pour personnaliser votre site. L'illustration suivante présente le site Orchard CMS que vous allez créer.

![Blog Orchard][]

[WACOM.INCLUDE [create-account-and-websites-note][]]

## Créer un site web Orchard à partir de la galerie

1.  Connectez-vous au [portail de gestion Azure][].

2.  Cliquez sur l'icône **New** dans le coin inférieur gauche du portail.

    ![Création][]

3.  Cliquez sur l'icône **Site web**, puis sur **À partir de la galerie**.

    ![Créer à partir de la galerie][]

4.  Cliquez sur l'icône **Orchard CMS** dans la liste, puis cliquez sur la flèche pour continuer.

    ![Liste de provenance Orchard][]

5.  Sur la page **Configure Your App**, entrez ou sélectionnez des valeurs pour tous les champs :

-   Entrez l'URL de votre choix.
-   Sélectionnez la région la plus proche de vos utilisateurs (afin d'offrir les meilleures performances).

    ![Configurer une application][]

1.  Cliquez sur la coche dans le coin inférieur droit de la fenêtre pour lancer le déploiement de votre nouveau site web Orchard CMS.

Azure lance les opérations de configuration et de déploiement. Pendant la configuration et le déploiement du site web, l'état de ces opérations est affiché en bas de la page Sites web dans le portail de gestion. Une fois toutes les opérations achevées, un message indique que votre site web a été créé.

## Lancement et gestion du site Orchard

1.  Cliquez sur le nom du nouveau site dans la page **Sites Web**, puis cliquez sur **Parcourir** en bas du portail pour ouvrir la page d'accueil de votre site web.

    ![lancer le tableau de bord][]

    ![bouton parcourir][]

2.  Entrez les informations de configuration demandées par Orchard, puis cliquez sur **Finish Setup** pour achever la configuration et ouvrir la page d'accueil du site web.

    ![connexion à Orchard][]

    Vous obtenez un nouveau site Orchard ressemblant à la capture d'écran ci-dessous.

    ![votre site Orchard][Blog Orchard]

3.  Consultez les détails de la [documentation Orchard][] pour en savoir plus sur Orchard et configurer votre nouveau site.

## <span class="short-header">Étapes suivantes</span>Étapes suivantes

-   [Développer et déployer un site web avec Microsoft WebMatrix][] : apprenez à modifier un site web Azure dans WebMatrix.
-   [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site web Azure][] : apprenez à créer un site web avec Visual Studio.

  [portail de gestion Azure]: http://manage.windowsazure.com
  [Galerie d'applications Web Windows]: http://www.microsoft.com/web/gallery/categories.aspx
  [Orchard]: http://www.orchardproject.net/
  [télécharger des thèmes et modules complémentaires]: http://gallery.orchardproject.net/
  [Blog Orchard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Création]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
  [Créer à partir de la galerie]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
  [Liste de provenance Orchard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
  [Configurer une application]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
  [lancer le tableau de bord]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
  [bouton parcourir]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
  [connexion à Orchard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
  [documentation Orchard]: http://docs.orchardproject.net/
  [Développer et déployer un site web avec Microsoft WebMatrix]: /fr-fr/develop/net/tutorials/website-with-webmatrix/
  [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site web Azure]: /fr-fr/develop/net/tutorials/web-site-with-sql-database/
