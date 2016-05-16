<properties
	pageTitle="Prise en main d’Azure App Service et de l’API Web ASP.NET 2 | Microsoft Azure"
	description="Apprenez à créer un projet d’API Web ASP.NET 2 dans Visual Studio et à le déployer dans une nouvelle application API dans Azure App Service."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="tdykstra"/>

# Prise en main d’Azure App Service et de l’API Web ASP.NET 2 avec Visual Studio

## Vue d'ensemble

Ce didacticiel montre comment déployer une application d’API Web ASP.NET 2 dans une [application API](app-service-api-apps-why-best-platform.md) dans Azure App Service en utilisant Visual Studio 2015. Vous allez créer un projet Visual Studio et le déployer sur une application API, comme l’illustre le diagramme.

![Visual Studio crée et déploie le diagramme](./media/app-service-api-dotnet-get-started-template/Create_App.png)

Ce didacticiel part du principe que vous êtes un développeur ASP.NET sans expérience préalable avec Azure. À la fin de ce didacticiel, vous disposerez d’une API Web simple et fonctionnelle dans le cloud.

Vous apprendrez ce qui suit :

* Comment créer une application API App Service pendant la création d’un projet d’API Web 2 dans Visual Studio.
* Comment déployer un projet d’API Web 2 sur une application API App Service à l’aide de Visual Studio.
* Comment utiliser le [portail Azure](/features/azure-portal/) pour surveiller et gérer votre application API.

À la fin du didacticiel, une section [Résolution des problèmes](#troubleshooting) contient des suggestions sur la conduite à tenir en cas de dysfonctionnement, et la section [Étapes suivantes](#next-steps) vous oriente vers d’autres didacticiels plus détaillés sur l’utilisation d’Azure App Service.

## Configuration requise

### Compte Azure

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Vous pouvez :

* [Ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F). Vous obtenez des crédits que vous pouvez utiliser pour essayer des services Azure payants. Même après que les crédits sont épuisés, vous pouvez conserver le compte et utiliser les services et fonctionnalités Azure gratuits.
* [Activez les avantages d’abonnement Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

Si vous souhaitez commencer à utiliser Azure App Service avant d’ouvrir un compte Azure, accédez à [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). De là, vous pouvez créer tout de suite une première application temporaire dans App Service. Aucune carte de crédit ni aucun engagement ne sont nécessaires.

### <a name="setupdevenv"></a>Visual Studio 2015 avec le Kit de développement logiciel (SDK) Azure pour .NET

Ce didacticiel a été rédigé pour Visual Studio 2015 avec le [Kit de développement logiciel (SDK) Azure pour .NET](../dotnet-sdk.md) version 2.8.2 ou ultérieure. [Cliquez ici pour télécharger la dernière version du Kit de développement logiciel (SDK) Azure pour Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio est automatiquement installé avec le SDK si vous n’en disposez pas déjà.

Si vous utilisez Visual Studio 2013, vous pouvez [télécharger la dernière version du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Certains écrans diffèrent des illustrations.

>[AZURE.NOTE] en fonction du nombre de dépendances du Kit de développement logiciel (SDK) qui se trouvent déjà sur votre ordinateur, l'installation du SDK peut prendre un certain temps (de plusieurs minutes à une demi-heure, voire plus).

### API Web ASP.NET 2

Ce didacticiel porte sur l’utilisation de l’API Web ASP.NET 2 avec Azure App Service. Il n’explique pas comment développer une API Web ASP.NET. Pour une présentation de l’API Web ASP.NET 2, consultez [Prise en main de l’API Web ASP.NET 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) sur le site [ASP.NET](http://asp.net/).

## Créer un projet et une application API dans Azure App Service

La première étape consiste à créer un projet d’API Web ASP.NET 2 dans Visual Studio et une application API dans Azure App Service. Une fois cette opération effectuée, vous déploierez le projet sur l’application API pour le rendre l’API Web accessible sur Internet.

1. Ouvrez Visual Studio 2015.

2. Cliquez sur **Fichier > Nouveau > Projet**.

3. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Visual C# > Web > Application Web ASP.NET**. (Si vous préférez, vous pouvez sélectionner **Visual Basic**.)

3. Vérifiez que la version cible sélectionnée est **.NET Framework 4.5.2**.

4.  [Azure Application Insights](../application-insights/app-insights-overview.md) surveille la disponibilité, les performances et l’utilisation de votre application API. La case **Ajouter Application Insights au projet** est cochée par défaut à la première création d’un projet web après l’installation de Visual Studio. Décochez la case si vous ne voulez pas essayer Application Insights.

4. Nommez l’application **MonExemple**.

5. Cliquez sur **OK**.

	![New Project dialog box](./media/app-service-api-dotnet-get-started-template/GS13newprojdb.png)

5. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle **Application API Azure**.

5. Dans la section **Microsoft Azure** de la boîte de dialogue **Nouveau projet ASP.NET**, vérifiez que l’option **Hôte dans le cloud** est sélectionnée et que **App Service** est sélectionné dans la liste déroulante.

	![Boîte de dialogue New ASP.NET Project](./media/app-service-api-dotnet-get-started-template/GS13newaspnetprojdb.png)

	Ces paramètres font en sorte que Visual Studio crée une application API Azure pour votre projet web.

6. Cliquez sur **OK**.

5. Si vous n’êtes pas déjà connecté à Azure, Visual Studio vous invite à le faire. Connectez-vous avec l’ID et le mot de passe du compte que vous utilisez pour gérer votre abonnement Azure.

	Lorsque vous êtes connecté, la boîte de dialogue **Céer App Service** vous demande les ressources que vous voulez créer.

	![Connecté à Azure](./media/app-service-api-dotnet-get-started-template/configuresitesettings.png)

3. Dans la boîte de dialogue **Créer App Service**, saisissez un **Nom de l’application API** unique dans le domaine *azurewebsites.net*. Par exemple, vous pouvez le nommer MonExemple avec des chiffres à droite pour le rendre unique, comme MonExemple810. Si un nom par défaut est créé pour vous, il sera unique et vous pourrez l'utiliser.

	Si une autre personne a déjà utilisé le nom que vous avez entré, un point d’exclamation rouge (et non une coche verte) s’affiche à droite, et vous devez saisir un autre nom.

	L’URL de base de votre API Web se compose de ce nom suivi de *.azurewebsites.net* (comme indiqué en regard de la zone de texte **Nom de l’application web**). Par exemple, si le nom est `MyExample810`, l’URL est `myexample810.azurewebsites.net`.

6. En regard de la zone **Groupe de ressources**, cliquez sur **Nouveau**, puis entrez « MyExample » ou un autre nom si vous préférez.

	Cette zone de liste modifiable vous permet de sélectionner un groupe de ressources existant ou d’en créer un en tapant un nom différent des groupes de ressources existants de votre abonnement.

	Un groupe de ressources est une collection de ressources Azure telles que des applications API, des bases de données, des machines virtuelles. Pour un didacticiel, il est généralement préférable de créer un groupe de ressources, car cela permet de supprimer en une étape les ressources Azure que vous créez pour le didacticiel. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](../resource-group-overview.md).

4. Cliquez sur le bouton **Nouveau** situé en regard de la liste déroulante **Plan App Service**.

	![Boîte de dialogue Créer App Service](./media/app-service-api-dotnet-get-started-template/createas.png)

	La capture d’écran montre des exemples de valeur pour **Nom de l’application web**, **Abonnement** et **Groupe de ressources**. Vos valeurs sont différentes.

	Au cours des étapes suivantes, vous allez créer un plan de service d’application pour le nouveau groupe de ressources. Un plan de service d’application spécifie les ressources de calcul sur lesquelles votre application API s’exécute. Par exemple, si vous choisissez le niveau Gratuit, votre application API s’exécute sur des machines virtuelles partagées, tandis que pour certains niveaux payants, elle s’exécute sur des machines virtuelles dédiées. Pour plus d’informations, consultez [Présentation des plans Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Dans la boîte de dialogue **Configurer le plan App Service**, entrez « MyExamplePlan » ou un autre nom si vous préférez.

5. Dans la liste déroulante **Emplacement**, sélectionnez le lieu le plus proche de vous.

	Ce paramètre indique le centre de données Azure dans lequel votre application sera exécutée. Pour les besoins de ce didacticiel, vous pouvez sélectionner n’importe quelle région : la différence ne sera pas sensible. Toutefois, pour une application de production, votre serveur doit être aussi proche que possible des clients qui y accèdent pour minimiser la [latence](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Dans la liste déroulante **Taille**, cliquez sur **Gratuit**.

	Pour ce didacticiel, le niveau tarifaire Gratuit fournit des performances suffisantes.

6. Dans la boîte de dialogue **Configurer le plan App Service**, cliquez sur **OK**.

	![Boîte de dialogue Configurer App Service](./media/app-service-api-dotnet-get-started-template/configasp.png)

7. Dans la boîte de dialogue **Créer App Service**, cliquez sur **Créer**.

	![Boîte de dialogue Créer App Service](./media/app-service-api-dotnet-get-started-template/clickcreate.png)

	Visual Studio crée rapidement, généralement en moins d’une minute, le projet web et l’application API.

	La fenêtre **Explorateur de solutions** indique les fichiers et les dossiers du nouveau projet.

	![Explorateur de solutions](./media/app-service-api-dotnet-get-started-template/solutionexplorer.png)

	La fenêtre **Activité Azure App Service** indique que l’application API a été créée. (Bien que le message puisse l’appeler application web.)

	![Application API créée dans la fenêtre Activité d’Azure App Service](./media/app-service-api-dotnet-get-started-template/GS13sitecreated1.png)

	Et vous pouvez voir l’application API dans la fenêtre **Cloud Explorer** de Visual Studio.

	![Application API créée dans Cloud Explorer](./media/app-service-api-dotnet-get-started-template/siteinse.png)
	
	Cette fenêtre vous permet d’afficher et de gérer une large plage de ressources Azure. Dans votre fenêtre **Cloud Explorer**, vous pouvez voir des types de ressources différents de ceux de cet exemple. Cliquez avec le bouton droit sur une ressource, par exemple votre application API, pour afficher les options de gestion des données.

## Déployer le projet Visual Studio sur l'application API Azure

Dans cette section, vous allez déployer le projet web dans l’application API, comme l’illustre l’étape 2 du diagramme.

![Visual Studio crée et déploie le diagramme](./media/app-service-api-dotnet-get-started-template/Create_App.png)

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

	![Cliquez sur Publier dans le menu Visual Studio](./media/app-service-api-dotnet-get-started-template/choosepublish.png)

	Au bout de quelques secondes, l’Assistant **Publier le site web** s’ouvre. L’Assistant s’ouvre sur un *profil de publication* présentant des paramètres de déploiement du projet sur la nouvelle application API. Si vous souhaitez exécuter le déploiement sur une autre application API, cliquez sur l’onglet **Profil** pour créer un autre profil. Pour ce didacticiel, vous devez accepter les paramètres de déploiement sur l’application API que vous avez créée plus tôt.

	Le profil de publication contient le nom d’utilisateur et le mot de passe pour le déploiement. Ces informations d’identification ont été générées pour vous et vous n’êtes pas obligé de les saisir ou de les modifier. Le mot de passe est chiffré dans un fichier utilisateur spécifique masqué dans le dossier `Properties\PublishProfiles`.

8. Sous l’onglet **Connexion** de l’Assistant **Publier le site web**, cliquez sur **Suivant**.

	![Sur l’onglet Connexion de l’assistant Publier le site Web, cliquez sur Suivant](./media/app-service-api-dotnet-get-started-template/GS13ValidateConnection.png)

	L’onglet suivant est l’onglet **Paramètres** (illustré ci-dessous). Vous pouvez y modifier l’onglet Configuration de build pour déployer un build de débogage pour le [débogage à distance](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). L’onglet offre également plusieurs **Options de publication des fichiers** :

	* Supprimer les fichiers supplémentaires de la destination
	* Précompiler durant la publication
	* Exclure les fichiers du dossier App\_Data

	Pour ce didacticiel, vous n’avez besoin d’aucune de ces options. Pour obtenir des explications détaillées sur leur action, consultez la rubrique [Déploiement d’un projet web à l’aide de la publication en un clic dans Visual Studio (en anglais)](https://msdn.microsoft.com/library/dd465337.aspx).

10. Sous l’onglet **Paramètres**, cliquez sur **Suivant**.

	![Onglet Paramètres de l’Assistant Publier le site web](./media/app-service-api-dotnet-get-started-template/GS13SettingsTab.png)

	L’onglet **Aperçu** (illustré ci-dessous) vient ensuite. vous pouvez voir les fichiers qui vont être copiés de votre projet vers l’application API. Lorsque vous déployez un projet vers une application API vers laquelle vous avez déjà déployé auparavant le projet, seuls les fichiers modifiés sont copiés. Si vous souhaitez afficher la liste de ce qui sera copié, vous pouvez cliquer sur le bouton **Démarrer l’aperçu**.

11. Dans l’onglet **Aperçu**, cliquez sur **Publier**.

	![Onglet d’aperçu de l’Assistant Publier le site Web](./media/app-service-api-dotnet-get-started-template/GS13previewoutput.png)

	Lorsque vous cliquez sur **Publier**, Visual Studio démarre le processus de copie des fichiers sur le serveur Azure. Cela peut prendre une à deux minutes.

	Les fenêtres **Sortie** et **Activité Azure App Service** indiquent les actions de déploiement effectuées et signalent la réussite du déploiement.

	![Fenêtre Visual Studio Output (Sortie Visual Studio) affichant la réussite du déploiement](./media/app-service-api-dotnet-get-started-template/PublishOutput.png)

	En cas de déploiement réussi, le navigateur par défaut ouvre automatiquement l'URL de base de l’application API déployée. Le navigateur affiche une page avec un message qui indique « Cette application web a été créée avec succès ».

	![Fenêtre Visual Studio Output (Sortie Visual Studio) affichant la réussite du déploiement](./media/app-service-api-dotnet-get-started-template/successfullycreated.png)

> [AZURE.TIP] Vous pouvez activer la barre d’outils **Publication web en un clic** pour accélérer le déploiement. Cliquez sur **Affichage > Barres d’outils**, puis sélectionnez **Publication Web en un clic**. Vous pouvez utiliser cette barre d’outils pour sélectionner un profil ou encore cliquer sur un bouton pour publier ou pour ouvrir l’Assistant **Publier le site web**. ![Barre d’outil Publication Web en un clic](./media/app-service-api-dotnet-get-started-template/weboneclickpublish.png)

## Tester l'API Web déployée

1. Envoyez une requête HTTP GET à l'URL *{nom de votre application API}.azurewebsites.net/api/values*, à l'aide de l'outil client HTTP que vous préférez.

	Le modèle de projet de l’API Web définit un contrôleur `Values` qui retourne un tableau de deux chaînes au format JSON pour une requête GET. L'illustration suivante montre une requête envoyée par [Postman](http://www.getpostman.com/), avec le JSON retourné dans le corps de la réponse.

	![Fenêtre Visual Studio Output (Sortie Visual Studio) affichant la réussite du déploiement](./media/app-service-api-dotnet-get-started-template/postman.png)

2. Vous pouvez maintenant modifier le code, redéployer le projet de la même manière que le déploiement initial et constater l’application de vos modifications dans Azure, en quelques secondes.

## <a id="portal"></a> Facultatif : Surveillance et gestion de l’application API dans le portail Azure

Le [portail Azure](/services/management-portal/) est une interface web qui vous permet de gérer et de surveiller vos services Azure, tels que l’application API que vous venez de créer. Dans cette section du didacticiel, vous allez examiner certaines des possibilités offertes par le portail.

1. Dans votre navigateur, accédez à [https://portal.azure.com](https://portal.azure.com) et connectez-vous avec les informations d’identification que vous utilisez pour la gestion de votre compte Azure.
	
2. Cliquez sur **App Services**, puis sur le nom de votre application API.

	![App Services dans le portail Azure](./media/app-service-api-dotnet-get-started-template/selinportal.png)

	Le panneau **Application API** affiche une vue d’ensemble des paramètres et des statistiques d’utilisation de votre application API. Les fenêtres qui s’ouvrent sur la droite du portail sont appelées des *panneaux*.

	![Panneau Application API sur le portail Azure](./media/app-service-api-dotnet-get-started-template/portaldashboard.png)

	Le trafic qui transite sur votre application API est encore très faible et il ne sera peut-être pas visible dans le graphique. Si vous envoyez d’autres requêtes à votre API Web, puis actualisez la page du portail, vous verrez des statistiques s'afficher.

3. Le panneau **Paramètres** affiche d’autres options de configuration de votre application API.

	![Panneau Paramètres dans le portail Azure](./media/app-service-api-dotnet-get-started-template/portalconfigure1.png)

	La section **API** renvoie aux panneaux de configuration d’un point de terminaison de métadonnées API et du CORS. Consultez la section [Étapes suivantes](#next-steps) pour accéder à des didacticiels qui présentent ces fonctionnalités.

	Notez le lien **Informations d’identification du déploiement** dans la section **Publication**. C’est à ce stade que vous créez un nom d’utilisateur personnalisé et un mot de passe pour le déploiement. Cliquez sur le bouton **Enregistrer** en haut du panneau pour soumettre votre modification. Si vous créez un nom d’utilisateur et un mot de passe, vous devez entrer les mêmes valeurs dans l’onglet **Connexion** de l’Assistant **Publier le site web** dans votre projet web.
	
	La capture d’écran ne représente qu’une vue partielle du panneau **Paramètres**. Ce panneau comporte d’autres sections que celles qui sont affichées.

Ce ne sont là que quelques-unes des fonctionnalités du portail. Vous pouvez créer, supprimer, arrêter et redémarrer des applications API, et gérer d’autres types de services Azure, par exemple des bases de données et des machines virtuelles.

## Facultatif : Supprimer l'application API Azure

Lorsque vous n’avez plus besoin de l’application API que vous avez créée pour ce didacticiel, vous pouvez la supprimer.

Un moyen simple de supprimer votre application API consiste à cliquer sur le bouton **Supprimer** en haut du panneau **Application API** dans le portail Azure. Il est toutefois encore plus judicieux de supprimer le groupe de ressources que vous avez créé pour y placer l’application API. Pour ce didacticiel, le groupe de ressources contient uniquement l’application API, mais en général, un groupe de ressources est constitué d’une collection de ressources liées. Par exemple, votre application API peut utiliser une base de données ou un compte de stockage Azure qui devient inutile lorsque vous supprimez l’application API. Lorsque vous supprimez un groupe de ressources, tout ce qu’il contient est supprimé. Pour supprimer un groupe de ressources à l’aide du portail Azure, exécutez les opérations qui suivent.

1. Accédez à la page d’accueil du [portail Azure](https://portal.azure.com).

2. Cliquez sur **Groupes de ressources**.

3. Dans la liste des groupes de ressources, cliquez sur le groupe de ressources que vous souhaitez supprimer.

	Lorsque le panneau **Groupe de ressources** s’affiche, il inclut une liste des ressources qu’il contient.

4. Dans le panneau **Groupe de ressources**, cliquez sur **Supprimer**.

	![Supprimer le groupe de ressources dans le portail Azure](./media/app-service-api-dotnet-get-started-template/delresgrp.png)

## Résolution de problèmes

Si vous rencontrez un problème pendant que vous progressez dans ce didacticiel, assurez-vous que vous utilisez la dernière version du kit de développement logiciel Azure pour .NET. Le moyen le plus simple pour ce faire consiste à [télécharger le kit de développement logiciel (SDK) Azure pour Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Si la version actuelle est installée, le programme d’installation de la plateforme web vous informe qu’aucune installation n’est nécessaire.

Si vous vous trouvez sur un réseau d’entreprise et que vous essayez d’exécuter un déploiement au-delà d’un pare-feu dans Azure App Service, assurez-vous que les ports 443 et 8172 sont ouverts pour le déploiement Web. Si vous ne pouvez pas ouvrir ces ports, consultez la section Étapes suivantes pour connaître les autres options de déploiement.

Une fois que vous aurez configuré votre application web ASP.NET dans Azure App Service, vous souhaiterez peut-être en savoir plus sur les fonctionnalités de Visual Studio qui simplifient la résolution des problèmes. Pour plus d’informations sur la journalisation, le débogage à distance, etc. consultez la section [Résolution des problèmes des applications API Azure dans Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## Étapes suivantes

Dans ce didacticiel, vous avez appris à créer une API Web simple et à la déployer sur une application API dans Azure App Service. Pour une présentation des fonctionnalités App Service qui facilitent le développement et l’utilisation des API Web, consultez la série de didacticiels qui commence par [Prise en main des applications API et d’ASP.NET](app-service-api-dotnet-get-started.md).

<!---HONumber=AcomDC_0504_2016-->