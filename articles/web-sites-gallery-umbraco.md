<properties title="Create an Umbraco website from the gallery in Microsoft Azure" pageTitle="Create an Umbraco website from the gallery in Microsoft Azure" description="required" metaKeywords="Azure, gallery, Umbraco, web site, website" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm"></tags>

# Création d'un site web Umbraco depuis la galerie dans Microsoft Azure

Umbraco CMS est un système de gestion de contenu open source complet qui peut être utilisé pour créer de nombreuses applications simples ou complexes. La galerie de l'application Sites Web Azure met à votre disposition une large gamme d'applications web développées par Microsoft, par des sociétés tierces ou par des initiatives de logiciel open source. Cette galerie vous permet de créer un site Umbraco CMS en quelques minutes, soit en appliquant les kits de démarrage, soit en intégrant votre propre conception.

Cet article présente la nouvelle version préliminaire du portail Azure, qui simplifie grandement la gestion des ressources. Le nouveau portail Azure est conçu pour accélérer le processus de distribution des logiciels en plaçant des outils, des technologies et des services interplateforme de Microsoft et ses partenaires au sein d'un même espace de travail. Au lieu d'utiliser des ressources indépendantes telles que Sites Web Azure, les projets Visual Studio ou les bases de données, vous pouvez créer, gérer et analyser l'intégralité de votre application comme un groupe de ressources unique.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   Création d'un site dans la galerie à l'aide de la nouvelle version préliminaire du portail Azure
-   Création d'un blog à l'aide d'Umbraco CMS

## Création d'un site web à partir de la galerie dans le portail Azure

1.  Connectez-vous au [portail de gestion Microsoft Azure][portail de gestion Microsoft Azure].

2.  Choisissez l'icône **Galerie Azure**.

    ![Choose Web Gallery][Choose Web Gallery]

3.  Dans la **galerie**, sélectionnez l'onglet **Web**, puis **Umbraco CMS**.

    ![Select Umbraco in the Web Gallery][Select Umbraco in the Web Gallery]

4.  Pour créer un site web Umbraco CMS, cliquez sur **Créer**.

    ![Click Create][Click Create]

5.  La prochaine étape consiste à configurer toutes les ressources associées à Umbraco CMS. Dans ce cas, les ressources sont un site web et une base de données SQL Server. Sélectionnez d'abord **Site web** pour configurer les paramètres de site web, comme l'**URL** du site, le **plan d'hébergement web**, les **paramètres d'application web** et l'**emplacement**.

    ![Configure resources][Configure resources]

6.  Configurez maintenant la base de données. Sélectionnez **Base de données**, puis **Créer une base de données**. Cet exemple permet de créer un serveur SQL pour la base de données sur Azure.

    ![Create a SQL Server on Azure][Create a SQL Server on Azure]

7.  Maintenant que le site web et la base de données sont configurés, vous pouvez commencer à déployer l'application en cliquant sur **Créer** au bas du premier volet **Umbraco CMS**, visible sur l'image précédente.

    ![Click Create][1]

Une fois le déploiement terminé, le tableau de démarrage dans le portail affiche le groupe de ressources créé pour Umbraco CMS, dans ce cas **UmbracoCMSgroup**. Dans la section **Résumé**, cliquez sur le nom du site web (dans ce cas, **umbracocmsgroup**) pour voir les propriétés associées. Dans la section **Résumé**, vous pouvez également sélectionner les ressources de la base de données pour voir les propriétés de la base de données correspondante.

![][]

## Lancement et configuration de votre site web Umbraco CMS

1.  Dans le volet affichant les détails de votre site web, cliquez sur **Parcourir** pour parcourir votre site (dans ce cas, umbracocmsgroup.azurewebsites.net.)

    ![Browse to your site][Browse to your site]

2.  Lorsque vous parcourez votre site web, Umbraco CMS lance son Assistant d'installation. Passez en revue les informations requises, puis cliquez sur **Personnaliser**.

    ![Install Umbraco wizard][Install Umbraco wizard]

3.  Saisissez vos informations de connexion et d'authentification pour la base de données qu'Umbraco utilisera. Sélectionnez **Microsoft SQL Azure** pour le type de base de données. Vous pouvez récupérer la chaîne de connexion pour votre base de données dans la section **Paramètres du site** de votre site web.

    ![Configure your database][Configure your database]

4.  Si vous débutez dans Umbraco CMS, vous pouvez sélectionner un kit de site web pour débutant. Sinon, cliquez sur **Non merci, je ne veux pas installer de site web pour débutant**.

    ![Install a starter website][Install a starter website]

5.  Le programme d'installation d'Umbraco terminera la configuration de votre application. Une fois votre application configurée, vous serez redirigé vers le tableau de bord administratif d'Umbraco CMS.

    ![Umbraco CMS dashboard][Umbraco CMS dashboard]

6.  Vous allez maintenant créer une page de texte d'exemple que vous publierez. Sélectionnez **Contenu**, **Dépassement**, puis **TextPage**.

    ![Create a text page][Create a text page]

7.  Saisissez un titre et du contenu pour votre page de texte, comme montré ci-dessous. Lorsque vous avez fini, cliquez sur **Enregistrer et publier**.

    ![Enter a title and some content][Enter a title and some content]

8.  Patientez pendant la publication de votre page. Une fois la publication terminée, vous recevez une petite alerte en bas à droite de l'écran. Vous pouvez maintenant parcourir le nouveau contenu sur votre site web.

    ![Published web site page][Published web site page]

Et voilà ! Vous avez créé un blog à l'aide d'Umbraco CMS en quelques minutes.

## Ressources supplémentaires

[Documentation sur Umbraco][Documentation sur Umbraco]

[Didacticiels vidéo sur Umbraco][Didacticiels vidéo sur Umbraco]

[Présentation de la version préliminaire du portail Microsoft Azure][Présentation de la version préliminaire du portail Microsoft Azure]

[Documentation sur la version préliminaire du portail Microsoft Azure][Documentation sur la version préliminaire du portail Microsoft Azure]

[Version préliminaire du portail Azure (Channel 9)][Version préliminaire du portail Azure (Channel 9)]

[Documentation sur les sites web Microsoft Azure][Documentation sur les sites web Microsoft Azure]

<!-- IMAGES -->

  [portail de gestion Microsoft Azure]: https://portal.azure.com/
  [Choose Web Gallery]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
  [Select Umbraco in the Web Gallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
  [Click Create]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
  [Configure resources]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
  [Create a SQL Server on Azure]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
  [1]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
  []: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
  [Browse to your site]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
  [Install Umbraco wizard]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
  [Configure your database]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
  [Install a starter website]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
  [Umbraco CMS dashboard]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
  [Create a text page]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
  [Enter a title and some content]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
  [Published web site page]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
  [Documentation sur Umbraco]: http://our.umbraco.org/documentation
  [Didacticiels vidéo sur Umbraco]: https://umbraco.com/help-and-support/video-tutorials.aspx
  [Présentation de la version préliminaire du portail Microsoft Azure]: http://azure.microsoft.com/fr-fr/overview/preview-portal/
  [Documentation sur la version préliminaire du portail Microsoft Azure]: http://azure.microsoft.com/fr-fr/documentation/preview-portal/
  [Version préliminaire du portail Azure (Channel 9)]: http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal
  [Documentation sur les sites web Microsoft Azure]: http://azure.microsoft.com/fr-fr/documentation/services/web-sites/
