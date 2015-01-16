<properties pageTitle="Prise en main des sites web Azure pour ASP.NET" metaKeywords="" description="Ce didacticiel explique comment créer un projet web ASP.NET dans Visual Studio 2013 et comment le déployer dans un site web Azure. En moins de 15 minutes, vous disposerez d'une application s'exécutant dans le cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure Websites and ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="hero-article" ms.date="11/12/2014" ms.author="tdykstra" />

# Prise en main d'Azure et ASP.NET

Ce didacticiel vous montre comment créer une application web ASP.NET et la déployer vers un site web Azure en utilisant Visual Studio 2013 ou Visual Studio Express 2013 pour le web. Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure ou d'ASP.NET. À la fin de ce didacticiel, vous disposerez d'une application web simple et fonctionnelle dans le cloud.

Vous apprendrez ce qui suit :

* configurer votre ordinateur pour le développement Azure en installant le Kit de développement logiciel (SDK) Azure ;
* créer un projet web Visual Studio ASP.NET et le déployer vers un site web Azure ;
* modifier ce projet et redéployer l'application ;
* utiliser le portail de gestion Azure pour surveiller et gérer votre site web.

Pour suivre ce didacticiel, vous avez besoin d'un compte Azure :

* Vous pouvez [ouvrir un compte Azure gratuitement](/fr-fr/pricing/free-trial/?WT.mc_id=A261C142F) - Vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et une fois qu'ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Azure Web Sites.
* Vous pouvez [activer les avantages des abonnements MSDN](/fr-fr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.</li>

> [WACOM.NOTE]
> Si vous voulez prendre en main Azure Web Sites avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net">https://trywebsites.azurewebsites.net</a>, où vous pourrez immédiatement créer un site de départ ASP.NET de courte durée dans Azure Web Sites.

L'illustration suivante présente l'application terminée :

![Web site home page](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

### Sections du didacticiel

* [Inscription à Microsoft Azure (Vidéo)](#video)
* [Configuration de l'environnement de développement](#set-up-the-development-environment)
* [Création d'une application web ASP.NET dans Visual Studio](#create-an-aspnet-web-application)
* [Déploiement de l'application dans Azure](#deploy-the-application-to-azure)
* [Modification et redéploiement](#make-a-change-and-redeploy)
* [Surveillance et gestion du site dans le portail de gestion](#monitor-and-manage-the-site-in-the-management-portal)
* [Étapes suivantes](#next-steps)

##<a name="video"></a>Inscription à Microsoft Azure (Vidéo)

Dans cette vidéo, Scott Hanselman illustre à quel point il est facile de s'inscrire à une version d'évaluation gratuite de Microsoft Azure. (Durée : 1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Création d'une application web ASP.NET

Vous devez tout d'abord créer un projet d'application web. Visual Studio crée automatiquement le site web Azure vers lequel vous allez déployer votre projet. Le schéma suivant illustre ces deux étapes.

![Diagram showing project creation and deployment steps](./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png)

1. Ouvrez Visual Studio 2013 ou Visual Studio 2013 Express pour le Web.

2. Dans le menu **Fichier**, cliquez sur **Nouveau projet**.

3. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **C#** > **Web** > **Application web ASP.NET**. Vous pouvez, si vous préférez, choisir **Visual Basic**.

3. Assurez-vous que la version cible est **.NET Framework 4.5**.

4. Nommez l'application **MonExemple** et cliquez sur **OK**.

	![New Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

5. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle **MVC**. Si vous préférez utiliser Web Forms ASP.NET, vous pouvez sélectionner le modèle **Web Forms**. 

	[MVC et Web Forms](http://www.asp.net/get-started/websites) sont des infrastructures ASP.NET pour le développement de sites web. Pour les besoins de ce didacticiel, vous pouvez choisir l'un ou l'autre, mais si vous sélectionnez Web Forms, vous devrez modifier *Default.aspx* ultérieurement, quand le didacticiel vous invitera à modifier *Index.cshtml*.

7. Cliquez sur **Modifier l'authentification**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

6. Dans la boîte de dialogue **Modifier l'authentification**, cliquez sur **Aucune authentification**, puis sur **OK**.

	![No Authentication](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	L'exemple d'application que vous créez ne permettra pas aux utilisateurs de se connecter. La section [Étapes suivantes](#next-steps) renvoie vers un didacticiel qui implémente l'authentification et l'autorisation.

5. Dans la boîte de dialogue **Nouveau projet ASP.NET**, conservez les paramètres existants sous **Azure**, puis cliquez sur **OK**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

	Les paramètres par défaut indiquent que Visual Studio va créer un site web Azure pour votre projet web. Dans la section suivante du didacticiel, vous allez déployer le projet web vers le site web nouvellement créé.

	(La légende de la case à cocher peut être **Héberger dans le cloud** ou **Créer des ressources distantes**. Dans les deux cas, l'effet est le même.)
	
5. Si vous n'êtes pas déjà connecté à Azure, Visual Studio vous invite à le faire. Cliquez sur **Connexion**.

	![Sign in to Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

6. Dans la boîte de dialogue **Connexion à Azure**, entrez l'ID et le mot de passe du compte utilisé pour gérer votre abonnement Azure.
	
	Une fois que vous êtes connecté, la boîte de dialogue **Configurer les paramètres de site Azure** vous invite à choisir les ressources à créer.

	![Signed in to Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

3. Visual Studio fournit un **nom de site** par défaut, qui sera utilisé par Azure comme préfixe de l'URL de votre application. Si vous préférez, entrez un autre nom de site.

	L'URL complète consiste en ce que vous avez entré ici plus *.azurewebsites.net* (comme indiqué à côté de la zone de texte **Nom du site**). Par exemple, si le nom du site est MonExemple6442, l'URL sera MonExemple6442.azurewebsites.net. L'URL doit être unique. Si une autre personne utilise déjà celle que vous avez entrée, un point d'exclamation rouge s'affiche à droite au lieu d'une coche verte, et vous devez entrer un autre nom de site.

4. Dans la liste déroulante **Région**, choisissez l'emplacement le plus proche de vous.

	Ce paramètre indique le centre de données Azure dans lequel votre site web sera exécuté. Pour les besoins de ce didacticiel, vous pouvez sélectionner n'importe quelle région : la différence ne sera pas sensible. Mais pour un site de production, votre serveur web doit être le plus proche possible des navigateurs qui accèdent à votre site, afin de minimiser la [latence](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Ne modifiez pas les champs de base de données.

	Vous n'utiliserez pas de base de données dans le cadre de ce didacticiel. La section [Étapes suivantes](#next-steps) à la fin de ce didacticiel renvoie vers un autre didacticiel qui vous expliquera comment utiliser une base de données.

6. Cliquez sur **OK**.

	En quelques secondes, Visual Studio crée le projet web dans le dossier indiqué, puis crée le site web dans la région Azure indiquée.  

	La fenêtre **Explorateur de solutions** affiche les fichiers et les dossiers du nouveau projet.

	![Solution Explorer](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

	La fenêtre **Activité de publication web** indique que le site a été créé.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

	Et le site apparaît dans l'Explorateur de serveurs.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/siteinse.png)



## Déploiement de l'application dans Azure

7. Dans la fenêtre **Activité de publication web**, cliquez sur **Publier maintenant MonExemple sur ce site**.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

	Au bout de quelques secondes, l'Assistant **Publication du site web**s'affiche. 

	Les paramètres dont Visual Studio a besoin pour déployer votre projet sur Azure ont été enregistrés dans un *profil de publication*. L'Assistant vous permet de passer en revue et de modifier ces paramètres.

8. Sous l'onglet **Connexion** de l'Assistant **Publication du site web**, cliquez sur **Valider la connexion** pour vous assurer que Visual Studio peut se connecter à Azure afin de déployer le projet web.

	![Validate connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

	Une fois la connexion validée, une coche verte est affichée à côté du bouton **Valider la connexion**. 

9. Cliquez sur **Suivant**.

	![Successfully validated connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

10. Sous l'onglet **Paramètres**, cliquez sur **Suivant**.

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	Vous pouvez accepter les valeurs par défaut des champs **Configuration** et **Options de publication des fichiers**.

	La liste déroulante **Configuration** vous permet de déployer une version de débogage pour le débogage à distance. La section [Étapes suivantes](#next-steps) renvoie vers un didacticiel qui explique comment exécuter Visual Studio en mode de débogage à distance.

	Si vous développez **Options de publication des fichiers**, vous avez accès à plusieurs paramètres vous permettant de traiter des scénarios qui ne s'appliquent pas à ce didacticiel :
 
	* Supprimer les fichiers supplémentaires à la destination.
	  
		Supprime du serveur tous les fichiers qui ne sont pas dans votre projet. Ceci peut vous être utile si vous déployez un projet vers un site que vous avez déjà utilisé précédemment pour déployer un autre projet.

	* Précompiler pendant la publication. 
	 
		Permet de réduire les temps de préchauffage lors de la première demande pour les sites volumineux.

	* Exclure les fichiers du dossier App_Data. 
	 
		Pour les tests, vous conservez parfois dans le dossier App_Data un fichier de base de données SQL Server que vous ne souhaitez pas déployer en production.
	
11. Sous l'onglet **Aperçu**, cliquez sur **Démarrer l'aperçu**.

	![StartPreview button in the Preview tab](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

	L'onglet affiche la liste des fichiers qui seront copiés sur le serveur. L'affichage de l'aperçu n'est pas requis pour publier l'application. Cependant, il s'agit d'une fonction utile à ne pas négliger.

12. Cliquez sur **Publier**.

	![StartPreview file output](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

	Visual Studio démarre le processus de copie des fichiers vers le serveur Azure.

	Les fenêtres **Sortie** et **Activité de publication web** indiquent les actions de déploiement effectuées et signalent la réussite du déploiement.

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

	Une fois le déploiement réussi, le navigateur par défaut ouvre automatiquement l'URL du site web déployé, et l'application créée est exécutée dans le cloud. L'URL de la barre d'adresse du navigateur indique que le site est chargé depuis Internet.

	![Web site running in Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

13. Fermez le navigateur.

## Modification et redéploiement

Cette section du didacticiel permet de modifier le titre **h1** de la page d'accueil, d'exécuter le projet localement sur votre ordinateur de développement pour vérifier les modifications apportées, puis de déployer la version modifiée sur Azure.

2. Ouvrez le fichier *Views/Home/Index.cshtml* ou *.vbhtml* dans l'**Explorateur de solutions**, remplacez le titre **h1** " ASP.NET " par " ASP.NET et Azure " et enregistrez le fichier. 

	![MVC index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)

	![MVC h1 change](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

1. Appuyez sur Ctrl+F5 pour voir le titre mis à jour en exécutant le site sur votre ordinateur local.

	![Web site running locally](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

	L'URL http://localhost indique que le site est exécuté sur votre ordinateur local. L'application est exécutée par défaut dans IIS Express, qui est une version légère d'IIS conçue pour être utilisée pendant le développement d'applications web.


1. Fermez le navigateur.

1. Dans l'**Explorateur de solutions**, cliquez avec bouton droit sur le projet et choisissez **Publier**.

	![Chooose Publish](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

	L'onglet Aperçu de l'Assistant **Publication du site web** s'affiche. Si vous devez modifier des paramètres de publication, vous pouvez choisir un autre onglet. Mais à présent, nous partons du principe que vous voulez procéder à un redéploiement sans modifier les paramètres.

2. Dans l'Assistant **Publication du site web**, cliquez sur **Publier**.

	![Click Publish](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

	Visual Studio déploie le projet vers Azure et ouvre le site dans le navigateur par défaut.

	![Changed site deployed](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

**Conseil :** vous pouvez activer la barre d'outils **Publication web en un clic** pour accélérer le déploiement. Cliquez sur **Affichage** > **Barres d'outils**, puis sélectionnez **Publication web en un clic**. La barre d'outils vous permet de sélectionner un profil, de cliquer sur un bouton pour publier ou de cliquer sur un bouton pour ouvrir l'Assistant **Publication du site web**. 

![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

## Surveillance et gestion du site dans le portail de gestion

Le [portail de gestion Azure](/fr-fr/services/management-portal/) est une interface web permettant de gérer et de surveiller vos services Azure, tels que le site web que vous venez de créer. Dans cette section du didacticiel, vous allez examiner certaines des possibilités offertes par le portail.

1. Dans votre navigateur, accédez à [http://manage.windowsazure.com]() et connectez-vous avec vos informations d'identification Azure.

	Le portail affiche la liste de vos services Azure.

2. Cliquez sur le nom de votre site web.

	![Portal home page with new web site called out](./media/web-sites-dotnet-get-started-vs2013/portalhome.png)
  
3. Cliquez sur l'onglet **Tableau de bord**.

	L'onglet **Tableau de bord** affiche une vue d'ensemble des statistiques d'utilisation et un lien vers un certain nombre de fonctions de gestion communément utilisées. Sous **Aperçu rapide**, vous trouverez également un lien vers la page d'accueil de votre application.

	![Portal web site dashboard tab](./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png)
  
	À ce stade, le trafic sur votre site est encore très faible et il ne sera peut-être pas visible dans le graphique. Si vous accédez à votre application, actualisez la page plusieurs fois, puis actualisez la page **Tableau de bord** du portail, les statistiques commenceront à s'afficher. Vous pouvez cliquer sur l'onglet **Surveiller** pour plus de détails.

4. Cliquez sur l'onglet **Configurer**.

	L'onglet [Configurer](/fr-fr/documentation/articles/web-sites-configure//) vous permet de contrôler la version .NET utilisée pour le site, d'activer des fonctionnalités telles que [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) et la [journalisation de diagnostic](/fr-fr/documentation/articles/web-sites-enable-diagnostic-log/), de définir les [valeurs des chaînes de connexion](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)et bien davantage. 

	![Portal web site configure tab](./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png)
  
5. Cliquez sur l'onglet **Mettre à l'échelle**.

	Pour les niveaux payants du service Web Sites, l'onglet [Mettre à l'échelle](/fr-fr/documentation/articles/web-sites-scale/) vous permet de contrôler la taille et le nombre de machines qui gèrent votre application web pour traiter les fluctuations du trafic.

	Vous pouvez effectuer la mise à l'échelle manuellement ou configurer des critères ou des planifications pour la mise à l'échelle automatique.

	![Portal website scale tab](./media/web-sites-dotnet-get-started-vs2013/portalscale.png)

Ce ne sont là que quelques-unes des fonctionnalités du portail de gestion. Vous pouvez également créer des sites web, supprimer des sites existants, arrêter et redémarrer des sites, et gérer d'autres types de services Azure, par exemple, des bases de données et des machines virtuelles.  

**Conseil :** la version préliminaire d'un nouveau portail de gestion, qui remplacera un jour celle que vous utilisez, est d'ores et déjà disponible. Pour plus d'informations, consultez [Portail Azure Preview](/fr-fr/overview/preview-portal/).

## Étapes suivantes

Dans ce didacticiel, vous avez appris à créer une application web simple et à la déployer vers un site web Azure. Pour en savoir plus, consultez les rubriques et ressources suivantes :

* Autres méthodes de déploiement d'un projet web

	Dans ce didacticiel, vous avez appris la méthode la plus rapide pour créer un site et le déployer en une opération. Pour une vue d'ensemble des autres méthodes de déploiement disponibles, à l'aide de Visual Studio ou en [automatisant le déploiement](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) à partir d'un [système de contrôle de code source](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consultez [Déploiement d'un site web Azure](/fr-fr/documentation/articles/web-sites-deploy/"). 

	Visual Studio can also generate Windows PowerShell scripts that enable you to automate deployment. For more information, see [Automate Everything (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* How to manage a website in Visual Studio

	For information about site management functions that you can do in **Server Explorer**, see [Troubleshooting Azure Websites in Visual Studio](/fr-fr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

* Dépannage d'un site web

	Visual Studio fournit des fonctionnalités qui facilitent l'affichage des journaux Azure durant leur génération en temps réel. Vous pouvez aussi exécuter le mode Debug à distance dans Azure. Pour plus d'informations, consultez [Résolution des problèmes de sites web Azure dans Visual Studio](/fr-fr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

* Ajout d'une fonctionnalité de base de données et d'autorisation

	Pour un didacticiel expliquant comment accéder aux bases de données et limiter certaines fonctions de site aux utilisateurs autorisés, consultez [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL sur un site web Azure](/fr-fr/develop/net/tutorials/web-site-with-sql-database/).

* Ajout d'un nom de domaine personnalisé et de SSL

	Pour des informations sur l'utilisation de SSL et de votre propre nom de domaine (par exemple, www.contoso.com au lieu de contoso.azurewebsites.net), consultez les ressources suivantes :

	* [Configuration d'un nom de domaine personnalisé pour un site web Azure](/fr-fr/documentation/articles/web-sites-custom-domain-name/). 
	* [Activation du protocole HTTPS pour un site web Azure](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-configure-ssl-certificate/)

* Manière d'éviter les délais de sortie de veille après les périodes d'inactivité 

	Par défaut, les sites web sont déchargés s'ils sont inactifs depuis un certain temps. La première demande après une telle période doit patienter jusqu'à ce que le site soit rechargé. Pour éviter ce temps d'attente, vous pouvez activer la fonction AlwaysOn. Pour plus d'informations, consultez les options de configuration dans [Configuration d'Azure Web Sites](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-configure/).

* Ajout de fonctions en temps réel telles que la conversation instantanée

	Si votre site web doit inclure des fonctionnalités en temps réel (telles qu'un service de conversation instantanée, un jeu, un afficheur en temps réel des cours de la bourse, etc.), vous pouvez obtenir des performances optimales en utilisant [ASP.NET SignalR](http://www.asp.net/signalr) avec la méthode de transport [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) . Pour plus d'informations, consultez [Utilisation de SignalR avec Microsoft Azure Web Sites](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites). 

* Comparaison entre Azure Web Sites, Azure Cloud Services et Azure Virtual Machines pour les applications web

	Azure vous permet d'exécuter des applications web dans des Sites Web (comme vous l'avez vu dans ce didacticiel), mais aussi dans Cloud Services ou Virtual Machines. Pour plus d'informations, consultez [Modèles d'exécution Azure](/fr-fr/develop/net/fundamentals/compute/) et [Comparaison entre Azure Web Sites, Azure Cloud Services et Azure Virtual Machines](/fr-fr/manage/services/web-sites/choose-web-app-service/).

<!--HONumber=35.1-->
