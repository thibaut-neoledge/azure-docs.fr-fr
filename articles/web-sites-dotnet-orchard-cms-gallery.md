<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Web Site from Gallery" pageTitle="Create an Orchard CMS web site from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new web site in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS web site from the gallery in Azure" authors="" solutions="" manager="" editor="" />

Création d'un site Web Orchard CMS à partir de la galerie dans Azure
====================================================================

La galerie met à votre disposition une large gamme d’applications Web développées par Microsoft, par des sociétés tierces ou par des initiatives de logiciel open source. Les applications Web créées à partir de la galerie ne requièrent pas l'installation de logiciel autre que le navigateur Web utilisé pour se connecter au [portail de gestion Azure](http://manage.windowsazure.com). Pour plus d'informations sur les applications Web dans la galerie, consultez la page [Galerie d'applications Web Windows](http://www.microsoft.com/web/gallery/categories.aspx).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer un site via la galerie ;

-   lancer et gérer votre site depuis le portail de gestion.

Vous allez configurer un site Orchard CMS avec un modèle par défaut. [Orchard](http://www.orchardproject.net/) est une application CMS gratuite, open-source, basée sur .NET qui vous permet de créer des sites Web personnalisés et orientés contenu. Orchard CMS comprend une infrastructure d'extensibilité qui vous permet de [télécharger des thèmes et modules complémentaires](http://gallery.orchardproject.net/) pour personnaliser votre site. L'illustration suivante présente le site Orchard CMS que vous allez créer.

![Blog Orchard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Création d'un site Web Orchard à partir de la galerie
-----------------------------------------------------

1.  Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2.  Cliquez sur l'icône **New** dans le coin inférieur gauche du portail.

    ![Création](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png)

3.  Cliquez sur l’icône **Web Site**, puis sur **From Gallery**.

    ![Créer à partir de la galerie](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png)

4.  Cliquez sur l'icône **Orchard CMS** dans la liste, puis cliquez sur la flèche pour continuer.

    ![Liste de provenance Orchard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png)

5.  Sur la page **Configure Your App**, entrez ou sélectionnez des valeurs pour tous les champs :

-   Entrez le nom d'URL de votre choix.
-   Sélectionnez la région la plus proche de vos utilisateurs (afin d'offrir les meilleures performances).

    ![Configurer une application](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png)

1.  Cliquez sur la coche dans le coin inférieur droit de la fenêtre pour lancer le déploiement de votre nouveau site Web Orchard CMS.

Azure lance les opérations de configuration et de déploiement. Pendant la configuration et le déploiement du site Web, l'état de ces opérations est affiché en bas du portail de gestion des sites Web. Une fois toutes les opérations achevées, un message indique que votre site Web a été créé.

Lancement et gestion du site Orchard
------------------------------------

1.  Cliquez sur le nom du nouveau site sur la page **Web Sites**, puis cliquez sur **Browse** en bas du portail pour ouvrir la page d'accueil de votre site Web.

    ![lancer le tableau de bord](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png)

    ![bouton parcourir](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png)

2.  Entrez les informations de configuration demandées par Orchard, puis cliquez sur **Finish Setup** pour achever la configuration et ouvrir la page d'accueil du site Web.

    ![connexion à Orchard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png)

    Vous obtenez un nouveau site Orchard ressemblant à la capture d'écran ci-dessous.

    ![votre site Orchard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png)

3.  Consultez les détails de la [documentation Orchard](http://docs.orchardproject.net/) pour en savoir plus sur Orchard et configurer votre nouveau site.

Étapes suivantesÉtapes suivantes
--------------------------------

-   [Développement et déploiement d'un site Web avec Microsoft WebMatrix](/en-us/develop/net/tutorials/website-with-webmatrix/) : apprenez à modifier un site Web Azure dans WebMatrix.
-   [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/) : apprenez à créer un site Web avec Visual Studio.

