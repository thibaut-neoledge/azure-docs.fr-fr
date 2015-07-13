<properties 
	pageTitle="Créer une application web Umbraco à partir de MarketPlace dans Microsoft Azure" 
	description="Créez un système de gestion de contenu Umbraco et déployez-le dans Azure App Service Web Apps." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tomfitz"/>

#Créer une application web Umbraco à partir de MarketPlace dans Microsoft Azure#

Umbraco CMS est un système de gestion de contenu open source complet qui peut être utilisé pour créer de nombreuses applications simples ou complexes. Azure Marketplace met à votre disposition une large gamme d’applications web populaires, développées par Microsoft, des sociétés tierces ou des initiatives de logiciel Open Source. Cette galerie vous permet de créer une application Umbraco CMS en quelques minutes, soit en appliquant les kits de démarrage, soit en intégrant votre propre conception.

Cet article présente le portail Azure qui simplifie grandement la gestion des ressources. Ce portail est conçu pour accélérer la distribution des logiciels en regroupant les outils, technologies et services interplateformes de Microsoft et de ses partenaires dans le même espace de travail. Au lieu d’utiliser des ressources autonomes telles que [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps, des projets Visual Studio ou des bases de données, vous pouvez créer, gérer et analyser l’intégralité de votre application comme un seul et même groupe de ressources.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

- Création d’une application web dans Marketplace à l’aide de la version préliminaire du portail Azure
- Création d'un blog à l'aide d'Umbraco CMS 

##Création d’une application web à partir de Marketplace dans la version préliminaire du portail Azure

1. Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com/).

2. Cliquez sur l’icône **Marketplace**.
	
	![Choose Web Gallery][01Startboard]
	
3. Dans **Marketplace** , cliquez sur l’onglet **Web Apps** puis sélectionnez **Umbraco CMS**.
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. Pour créer une application web Umbraco CMS, cliquez sur **Créer**.
	
	![Click Create][03UmbracoCMS]
	
5. La prochaine étape consiste à configurer toutes les ressources associées à Umbraco CMS. Dans ce cas, les ressources sont une application web et une base de données SQL Server. D’abord, sélectionnez **Application web** pour configurer les paramètres de l’application web, comme l’**URL**, le **plan App Service**, les **paramètres de l’application web** et l’**emplacement**.
	
	![Configure resources][04AppSettings]
	
6. Configurez maintenant la base de données. Sélectionnez **Base de données**, puis choisissez **Serveur**. Cet exemple permet de créer un serveur SQL pour la base de données sur Azure.
	
	![Create a SQL Server on Azure][05NewServer]
	
7. Maintenant que l’application web et la base de données sont configurées, vous pouvez commencer à déployer l’application en cliquant sur **Créer** en bas du premier panneau **Umbraco CMS** visible sur l’image précédente.
	
	![Click Create][06UmbracoCMSGroup]
	
Une fois le déploiement terminé, le portail affiche le panneau du groupe de ressources de votre application web Umbraco CMS. Dans la section **Résumé**, cliquez sur le nom de l’application web pour afficher les propriétés de celle-ci. Dans la section **Résumé**, vous pouvez également sélectionner les ressources de la base de données pour voir les propriétés de la base de données correspondante.
	
![][07UmbracoCMSGroupBlade]

## Lancer et configurer votre application web Umbraco CMS ##

1. Dans le panneau de détails de votre application web, cliquez sur **Parcourir** pour trouver votre application web.
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Dans l’application web, Umbraco CMS lance son assistant d’installation. Passez en revue les informations requises, puis cliquez sur **Personnaliser**.
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Saisissez vos informations de connexion et d'authentification pour la base de données qu'Umbraco utilisera. Sélectionnez **Microsoft SQL Azure** pour le type de base de données. Vous pouvez récupérer la chaîne de connexion de votre base de données dans la section **Paramètres du site** de votre application web.
	
	![Configure your database][10ConfigureYourDatabase]
	
4. Si vous débutez dans Umbraco CMS, vous pouvez sélectionner un kit de site web pour débutant. Sinon, cliquez sur **Non merci, je ne veux pas installer de site web pour débutant**.
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. Le programme d'installation d'Umbraco terminera la configuration de votre application. Une fois votre application configurée, vous serez redirigé vers le tableau de bord administratif d'Umbraco CMS.
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. Vous allez maintenant créer une page de texte d'exemple que vous publierez. Sélectionnez **Contenu**, **Dépassement**, puis **TextPage**.
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. Saisissez un titre et du contenu pour votre page de texte, comme montré ci-dessous. Lorsque vous avez fini, cliquez sur **Enregistrer et publier**.
	
	![Enter a title and some content][16EnterAName]
	
8. Patientez pendant la publication de votre page. Une fois la publication terminée, vous recevez une petite alerte en bas à droite de l'écran. Vous pouvez maintenant parcourir le nouveau contenu sur votre application web.
	
	![Published web site page][17MyPage]
	

Et voilà ! Vous avez créé un blog à l’aide d’Umbraco CMS en quelques minutes.

##Ressources supplémentaires

[Documentation sur Umbraco](http://our.umbraco.org/documentation)

[Didacticiels vidéo sur Umbraco](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Documentation du portail Azure](../preview-portal.md)

[Portail Azure (Channel 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Documentation sur Azure App Service Web Apps](/documentation/services/websites/)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.


<!-- IMAGES -->
[01Startboard]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
[02WebGallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
[03UmbracoCMS]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
[04AppSettings]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
[05NewServer]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
[06UmbracoCMSGroup]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
[07UmbracoCMSGroupBlade]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
[08UmbracoCMSGroupRunning]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
[09InstallUmbraco7]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
[10ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
[11InstallAStarterWebsite]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
[12ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/12ConfigureYourDatabase.png
[14FriendlyCMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
[15CreateItemUnderOverflow]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
[16EnterAName]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
[17MyPage]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
 

<!---HONumber=62-->