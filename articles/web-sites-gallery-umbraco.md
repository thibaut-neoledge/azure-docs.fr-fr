<properties 
	pageTitle="Création d'un site web Umbraco depuis la galerie dans Microsoft Azure" 
	description="obligatoire" 
	services="web-sites" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="tomfitz"/>

#Création d'un site web Umbraco depuis la galerie dans Microsoft Azure#

Umbraco CMS est un système de gestion de contenu open source complet qui peut être utilisé pour créer de nombreuses applications simples ou complexes. La galerie de l'application Sites Web Azure met à votre disposition une large gamme d'applications web développées par Microsoft, par des sociétés tierces ou par des initiatives de logiciel open source. Cette galerie vous permet de créer un site Umbraco CMS en quelques minutes, soit en appliquant les kits de démarrage, soit en intégrant votre propre conception. 

Cet article présente la nouvelle version préliminaire du portail Azure, qui simplifie grandement la gestion des ressources. Le nouveau portail Azure est conçu pour accélérer le processus de distribution des logiciels en plaçant des outils, des technologies et des services interplateforme de Microsoft et ses partenaires au sein d'un même espace de travail. Au lieu d'utiliser des ressources indépendantes telles que Sites Web Azure, les projets Visual Studio ou les bases de données, vous pouvez créer, gérer et analyser l'intégralité de votre application comme un groupe de ressources unique. 

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

- Création d'un site dans la galerie à l'aide de la nouvelle version préliminaire du portail Azure
- Création d'un blog à l'aide d'Umbraco CMS 

##Création d'un site web à partir de la galerie dans le portail Azure

1. Connectez-vous au [portail de gestion Microsoft Azure](https://portal.azure.com/).

2. Choisissez l'icône **Galerie Azure**.
	
	![Choose Web Gallery][01Startboard]
	
3. Dans la **galerie**, sélectionnez l'onglet**Web** puis **Umbraco CMS**.
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. Pour créer un site web Umbraco CMS, cliquez sur **Créer**.
	
	![Click Create][03UmbracoCMS]
	
5. L'étape suivante consiste à configurer toutes les ressources associées à Umbraco CMS. Dans ce cas, les ressources sont un site web et une base de données SQL Server. Sélectionnez d'abord **Site web** pour configurer les paramètres de site web, comme l'**URL** du site, le **plan d'hébergement web**, les **paramètres d'application web** et l'**emplacement**. 
	
	![Configure resources][04AppSettings]
	
6. Configurez maintenant la base de données. Sélectionnez **Base de données**, puis **Créer une base de données**. Cet exemple permet de créer un serveur SQL pour la base de données sur Azure.
	
	![Create a SQL Server on Azure][05NewServer]
	
7. Maintenant que le site web et la base de données sont configurés, vous pouvez commencer à déployer l'application en cliquant sur **Créer** au bas du premier panneau **Umbraco CMS**, visible sur l'image précédente.
	
	![Click Create][06UmbracoCMSGroup]
	
Une fois le déploiement terminé, le tableau de démarrage dans le portail affiche le groupe de ressources créé pour Umbraco CMS, dans ce cas **UmbracoCMSgroup**. Dans la section **Résumé**, cliquez sur le nom du site web (dans ce cas, **umbracocmsgroup**) pour voir les propriétés associées. Dans la section **Résumé**, vous pouvez également sélectionner les ressources de la base de données pour voir les propriétés de la base de données correspondante.
	
![][07UmbracoCMSGroupBlade]

## Lancement et configuration de votre site web Umbraco CMS ##

1. Dans le panneau affichant les détails de votre site web, cliquez sur **Parcourir** pour parcourir votre site (dans ce cas, umbracocmsgroup.azurewebsites.net.)
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Lorsque vous parcourez votre site web, Umbraco CMS lance son Assistant Installation. Passez en revue les informations requises, puis cliquez sur **Personnaliser**.
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Saisissez vos informations de connexion et d'authentification pour la base de données qu'Umbraco utilisera. Sélectionnez **Microsoft SQL Azure** pour le type de base de données.  Vous pouvez récupérer la chaîne de connexion pour votre base de données dans la section **Paramètres du site** de votre site web.
	
	![Configure your database][10ConfigureYourDatabase] 
	
4. Si vous débutez dans Umbraco CMS, vous pouvez sélectionner un kit de site web pour débutant. Sinon, cliquez sur **Non merci, je ne veux pas installer de site web pour débutant**.
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. Le programme d'installation d'Umbraco terminera la configuration de votre application. Une fois votre application configurée, vous serez redirigé vers le tableau de bord administratif d'Umbraco CMS.
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. Vous allez maintenant créer une page de texte d'exemple que vous publierez. Sélectionnez **Contenu**, **Dépassement**, puis **TextPage**.
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. Saisissez un titre et du contenu pour votre page de texte, comme indiqué ci-dessous. Lorsque vous avez fini, cliquez sur **Enregistrer et publier**.
	
	![Enter a title and some content][16EnterAName]
	
8. Patientez pendant la publication de votre page. Une fois la publication terminée, vous recevez une petite alerte en bas à droite de l'écran. Vous pouvez maintenant parcourir le nouveau contenu sur votre site web. 
	
	![Published web site page][17MyPage]
	

Et voilà ! Vous avez créé un blog à l'aide d'Umbraco CMS en quelques minutes. 

##Ressources supplémentaires

[Documentation sur Umbraco](http://our.umbraco.org/documentation)

[Didacticiels vidéo sur Umbraco](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Présentation de la version préliminaire du portail Microsoft Azure](http://azure.microsoft.com/overview/preview-portal/)

[Documentation sur la version préliminaire du portail Microsoft Azure](http://azure.microsoft.com/documentation/preview-portal/)

[Version préliminaire du portail Azure (Channel 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal) 

[Documentation sur les sites web Microsoft Azure](http://azure.microsoft.com/documentation/services/web-sites/)


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

<!--HONumber=46--> 
