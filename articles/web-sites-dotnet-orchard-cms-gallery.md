<properties urlDisplayName="Website from Gallery" pageTitle="Création d'un site web Orchard CMS à partir de la galerie dans Azure" metaKeywords="zure build website, manage website Azure" description="Un didacticiel qui explique comment créer un site web dans Azure. Apprenez également à lancer et gérer votre site à l'aide du portail de gestion." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

# Créer un site web Orchard CMS à partir de la galerie dans Azure

La galerie met à votre disposition une large gamme d'applications web développées par Microsoft, par des sociétés tierces ou par des initiatives de logiciel open source. Les applications Web créées à partir de la galerie ne requièrent pas l'installation de logiciel autre que le navigateur Web utilisé pour se connecter au [portail de gestion Azure](http://manage.windowsazure.com). Pour plus d'informations sur les applications web dans la galerie, consultez la page [Galerie d'applications web Windows](http://www.microsoft.com/web/gallery/categories.aspx).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

- créer un site via la galerie ;

- lancer et gérer votre site depuis le portail de gestion.
 
Vous allez configurer un site Orchard CMS avec un modèle par défaut. [Orchard](http://www.orchardproject.net/) est une application CMS gratuite, open source, basée sur .NET, qui vous permet de créer des sites web personnalisés et orientés contenu. Orchard CMS comprend une infrastructure d'extensibilité qui vous permet de [télécharger des thèmes et modules complémentaires](http://gallery.orchardproject.net/) pour personnaliser votre site. L'illustration suivante présente le site Orchard CMS que vous allez créer.

![Orchard blog][13]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>Créer un site web Orchard à partir de la galerie</h2>

1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2. Cliquez sur l'icône **Nouveau** dans le coin inférieur gauche du portail.
	
	![Create New][1]

3. Cliquez sur l'icône **Site web**, puis sur **À partir de la galerie**.
	
	![Create From Gallery][2]

4. Cliquez sur l'icône **Orchard CMS** dans la liste, puis cliquez sur la flèche pour continuer.
	
	![Orchard from list][3]

5. Sur la page **Configurer votre application**, entrez ou sélectionnez des valeurs pour tous les champs :
	
- Entrez le nom d'URL de votre choix.	
- Sélectionnez la région la plus proche de vos utilisateurs (afin d'offrir les meilleures performances).

	![configure your app][4]

6. Cliquez sur la coche dans le coin inférieur droit de la fenêtre pour lancer le déploiement de votre nouveau site web Orchard CMS.

Azure lance les opérations de configuration et de déploiement. Pendant la configuration et le déploiement du site web, l'état de ces opérations est affiché en bas de la page Sites web dans le portail de gestion. Une fois toutes les opérations achevées, un message indique que votre site web a été créé.

<h2>Lancement et gestion du site Orchard</h2>

1. Cliquez sur le nom du nouveau site dans la page **Sites Web**, puis cliquez sur **Parcourir** en bas du portail pour ouvrir la page d'accueil de votre site web.

	![launch dashboard][5]

	![browse button][12]

2. Entrez les informations de configuration demandées par Orchard, puis cliquez sur **Terminer l'installation** pour achever la configuration et ouvrir la page d'accueil du site web.

	![login to Orchard][7]

	Vous obtenez un nouveau site Orchard ressemblant à la capture d'écran ci-dessous.  

	![your Orchard site][13]

3. Consultez les détails de la [documentation Orchard](http://docs.orchardproject.net/) pour en savoir plus sur Orchard et configurer votre nouveau site.

<h2>Étape suivante</h2>
* [Développer et déployer un site web avec Microsoft WebMatrix](/fr-fr/develop/net/tutorials/website-with-webmatrix/) : apprenez à modifier un site web Azure dans WebMatrix. 
* [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site web Azure](/fr-fr/develop/net/tutorials/web-site-with-sql-database/) : apprenez à créer un site web avec Visual Studio.

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png


