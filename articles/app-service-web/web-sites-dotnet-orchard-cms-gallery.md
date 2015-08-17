<properties 
	pageTitle="Création d’une application Web avec système de gestion de contenu Orchard à partir d’Azure Marketplace" 
	description="Didacticiel qui explique comment créer une application Web dans Azure. Apprenez également à utiliser le portail Azure pour lancer et gérer votre application Web." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="tomfitz"/>

# Création d’une application Web avec système de gestion de contenu Orchard à partir d’Azure Marketplace

## Vue d'ensemble

Marketplace met à votre disposition une large gamme d’applications Web développées par Microsoft, par des sociétés tierces ou par des initiatives de logiciel open source. Les applications Web créées à partir de Marketplace ne nécessitent pas l’installation de logiciels autres que le navigateur utilisé pour se connecter au [portail Azure en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=529715). Pour plus d’informations sur les applications Web de Marketplace, consultez [Azure Marketplace](/marketplace/).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

- création d’une application Web à partir de Marketplace ;

- utilisation du portail Azure pour lancer et gérer votre application Web.
 
Vous allez configurer une application Web Orchard CMS avec un modèle par défaut. [Orchard](http://www.orchardproject.net/) est une application CMS gratuite, open source, basée sur .NET, qui vous permet de créer des sites et des applications Web personnalisés et orientés contenu. Orchard CMS comprend une infrastructure d’extensibilité qui vous permet de [télécharger des thèmes et modules complémentaires](http://gallery.orchardproject.net/) pour personnaliser votre application Web. L’illustration suivante présente l’application Web Orchard CMS dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) que vous allez créer.

![Blog Orchard][13]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Création d’une application Web Orchard à partir de Marketplace

1. Connectez-vous au [portail Azure en version préliminaire](http://portal.azure.com).

2. Sélectionnez **Nouveau** > **Web + Mobile** > **Azure Marketplace**.
	
	![Création][1]

3. Cliquez sur **Applications Web** > **Orchard CMS**. Dans le panneau suivant, cliquez sur **Créer**.
	
	![Créer à partir de Marketplace][2]

4. Configurez également l’URL de l’application Web, le plan App Service, le groupe de ressources et l’emplacement. Une fois ces opérations effectuées, cliquez sur **Créer**.
	
	![Configurer l’application][3]

	Une fois que votre application Web est créée, le bouton **Notifications** affiche la mention « SUCCÈS » en vert et le panneau de votre application Web apparaît.

## Lancement et gestion de l’application Web Orchard

1. Dans le panneau de votre application Web, cliquez sur **Parcourir** pour ouvrir la page d’accueil de votre application Web.

	![bouton parcourir][12]

2. Entrez les informations de configuration demandées par Orchard, puis cliquez sur **Terminer l’installation** pour achever la configuration et ouvrir la page d’accueil de l’application Web.

	![connexion à Orchard][7]

	Vous obtenez une nouvelle application Web Orchard ressemblant à la capture d’écran ci-dessous.

	![votre application Web Orchard][13]

3. Consultez les détails de la [documentation Orchard](http://docs.orchardproject.net/) pour en savoir plus sur Orchard et configurer votre nouvelle application Web.

## Étapes suivantes

* [Développement et déploiement d’une application Web avec Microsoft WebMatrix](web-sites-dotnet-using-webmatrix.md) : apprenez à modifier une application Web App Service dans WebMatrix. 
* [Création d’une application ASP.NET MVC avec authentification et base de données SQL et déploiement dans Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) : apprenez à créer une application Web dans Azure App Service à partir de Visual Studio.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png


 

<!---HONumber=August15_HO6-->