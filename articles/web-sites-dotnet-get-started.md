<properties  linkid="develop-net-tutorials-get-started-vs2013" urlDisplayName="Get started with Azure for ASP.NET" pageTitle="Get started with Azure for ASP.NET" metaKeywords="" description="This tutorial shows you how to create an ASP.NET web project in Visual Studio 2013 and deploy it to an Azure Web Site. In less than 15 minutes you'll have an app up and running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Prise en main d'Azure et ASP.NET

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

 Ce didacticiel vous montre comment créer une application Web ASP.NET et la déployer dans un site Web Azure en utilisant Visual Studio 2013 ou Visual Studio Express 2013 pour le Web. Ce didacticiel part du principe que vous n'avez aucune expérience préalable avec Azure ou ASP.NET. À la fin de ce didacticiel, vous disposerez d'une application Web simple opérationnelle dans le cloud.

Vous pouvez ouvrir gratuitement un compte Azure. Si vous n'avez pas déjà Visual Studio 2013, le Kit de développement logiciel (SDK) installe automatiquement Visual Studio Express 2013 pour le Web. Vous pouvez donc commencer vos développements Azure gratuitement.

Vous apprendrez à effectuer les opérations suivantes :

* configurer votre machine pour le développement Azure en installant le
  Kit de développement logiciel (SDK) Azure ;
* créer un projet Web Visual Studio ASP.NET et le déployer vers un site
  Web Azure ;
* modifier ce projet et le redéployer.

L'illustration suivante présente l'application terminée :

![Page d’accueil du site
Web](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez [activer les avantages de votre abonnement MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [obtenir une évaluation gratuite](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).

### Sections du didacticiel

* [Configuration de l'environnement de
  développement](#set-up-the-development-environment)
* [Création d'un projet Web ASP.NET dans Visual
  Studio](#create-an-asp.net-web-project)
* [Déploiement de l'application dans
  Azure](#deploy-the-application-to-azure)
* [Modification et redéploiement](#make-a-change-and-redeploy)
* [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Création d'une application web ASP.NET

Vous devez tout d'abord créer un projet d'application Web. Visual Studio crée automatiquement le site Web Azure dans lequel vous allez déployer votre projet.

1.  Ouvrez Visual Studio 2013 ou Visual Studio 2013 Express pour le Web.

2.  Dans le menu **Fichier**, cliquez sur **Nouveau projet**.
    
    ![Nouveau projet dans le menu
    Fichier](./media/web-sites-dotnet-get-started-vs2013/gs13newproj.png)

3.  Dans la boîte de dialogue **Nouveau projet**, développez **C#** ou **Visual Basic**, puis sélectionnez **Web** sous **Modèles installés**, puis sélectionnez **Application Web ASP.NET**.

4.  Vérifiez que la version cible sélectionnée est **.NET Framework 4.5**.

5.  Nommez l'application **MonExemple**, puis cliquez sur **OK**.
    
    ![Boîte de dialogue Nouveau
    projet](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

6.  Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle **MVC** ou **Web Forms**, puis cliquez sur **Modifier l'authentification**.
    
    [MVC et Web Forms][1] sont des infrastructures ASP.NET permettant de créer des sites Web. Si vous n'avez pas de préférence et que vous envisagez de suivre d'autres didacticiels Azure, MVC est un bon choix, car ce modèle est davantage utilisé dans les didacticiels Azure.
    
    ![Boîte de dialogue Nouveau projet
    ASP.NET](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

7.  Dans la boîte de dialogue **Modifier l'authentification**, cliquez sur **Aucune authentification**, puis sur **OK**.
    
    ![Aucune
    authentification](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)
    
    L'exemple d'application que vous créez n'aura pas les fonctionnalités obligeant les utilisateurs à se connecter. La section [Étapes suivantes](#next-steps) située à la fin du didacticiel mène vers un autre didacticiel qui implémente les fonctions d'authentification et d'autorisation.

8.  Sous **Azure** dans la boîte de dialogue, laissez la case à cocher activée et la liste déroulante définie sur **Site Web**.
    
    La légende de la case à cocher peut être **Host in the cloud** ou **Create remote resources**. Dans tous les cas, l'effet est le même.
    
    Ces paramètres indiquent que Visual Studio va créer un site Web Azure pour votre projet Web. Vous allez déployer le projet Web vers ce site Web. Une autre méthode consiste à modifier la sélection de la liste déroulante pour que Visual Studio crée une machine virtuelle Azure exécutant IIS, mais ce didacticiel ne détaille pas cette procédure.

9.  Dans la boîte de dialogue **Nouveau projet ASP.NET**, cliquez sur **OK**.
    
    ![Boîte de dialogue Nouveau projet
    ASP.NET](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)
    
    La capture d'écran montre le modèle MVC sélectionné. Si vous choisissez Web Forms, **Web Forms** est sélectionné.

10. Si vous n'êtes pas déjà connecté à Azure, Visual Studio vous invite à le faire. Cliquez sur **Se connecter**.
    
    ![Se connecter à Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

11. Entrez l'ID et le mot de passe du compte que vous utilisez pour gérer votre abonnement Azure.
    
    ![Se connecter à
    Azure](./media/web-sites-dotnet-get-started-vs2013/signindb.png)
    
    Lorsque vous êtes connecté, la boîte de dialogue **Configure Azure Site Settings** vous demande les ressources que vous voulez créer.
    
    ![Connecté à
    Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

12. Visual Studio fournit un **nom de site** par défaut. Azure l'utilise en tant que préfixe pour l'URL de votre application. Si vous préférez, entrez un autre nom de site.
    
    L'URL complète se compose de ce que vous entrez ici et du nom de domaine affiché en regard de la zone de texte. Par exemple, si le nom du site est `MonExemple6442`, l'URL sera `MonExemple6442.azurewebsites.net`. Si une autre personne utilise déjà cette URL, un point d'exclamation rouge s'affichera au lieu d'une coche verte, et vous devrez entrer une autre valeur.

13. Dans la liste déroulante **Région**, sélectionnez le lieu le plus proche de vous.
    
    Ce paramètre indique le centre de donné Azure sur lequel votre site Web sera exécuté.

14. Ne modifiez pas les champs de base de données.
    
    Vous n'utiliserez pas de base de données dans le cadre de ce didacticiel. La section [Étapes suivantes](#next-steps) située à la fin de ce didacticiel mène vers un autre didacticiel qui vous expliquera comment utiliser une base de données.

15. Cliquez sur **OK**.
    
    En quelques secondes, Visual Studio crée le projet Web dans le dossier indiqué, puis crée le site Web dans la région Azure indiquée.
    
    La fenêtre **Explorateur de solutions** indique les fichiers et les dossiers du nouveau projet. La capture d'écran suivante montre un projet Web Forms. Un projet MVC n'a pas les mêmes dossiers et fichiers.
    
    ![Explorateur de
    solutions](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)
    
    La fenêtre **Web Publish Activity** indique que le site a été créé.
    
    ![Site Web
    créé](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

## Déploiement de l'application dans Azure

1.  Dans la fenêtre **Web Publish Activity**, cliquez sur **Publish MyExample to this site now**.
    
    ![Site Web
    créé](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)
    
    Au bout de quelques secondes, l'Assistant **Publier le site Web** apparaît. L'Assistant crée un nouveau *profil de publication* contenant des paramètres tels que l'URL du site Web requise par Visual Studio pour le déploiement de votre projet vers Azure. Le profil est enregistré automatiquement. Ainsi, lorsque vous modifierez le projet, vous pourrez facilement redéployer le projet vers le même site.

2.  Dans l'onglet **Connexion** de l'Assistant **Publier le site Web**, cliquez sur **Valider la connexion** pour vous assurer que Visual Studio peut se connecter à Azure afin de déployer le projet Web.
    
    ![Valider la
    connexion](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)
    
    Une fois la connexion validée, une coche verte est affichée en regard du bouton **Valider la connexion**.

3.  Cliquez sur **Suivant**.
    
    ![Connexion correctement
    validée](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

4.  Sous l'onglet **Paramètres**, cliquez sur **Suivant**.
    
    ![Onglet
    Paramètres](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)
    
    Vous pouvez accepter les paramètres par défaut sous cet onglet. Vous déployez une version Release et il n'est pas nécessaire de supprimer les fichiers du serveur de destination, de précompiler l'application ni d'exclure les fichiers du dossier App\_Data. La section [Étapes suivantes](#next-steps) située à la fin du didacticiel mène vers un autre didacticiel déployant une version Debug et indiquant comment exécuter Visual Studio en mode débogage à distance.

5.  Sous l'onglet **Aperçu**, cliquez sur **Démarrer l'aperçu**.
    
    ![Bouton Démarrer l’aperçu sous l’onglet
    Aperçu](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)
    
    L'onglet affiche la liste des fichiers qui seront copiés sur le serveur. L'affichage de l'aperçu n'est pas requis pour publier l'application. Cependant, il s'agit d'une fonction utile à ne pas négliger.

6.  Cliquez sur **Publier**.
    
    ![Fichier de résultat de la fonction Démarrer
    l’aperçu](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)
    
    Visual Studio démarre le processus de copie des fichiers vers le serveur Azure.
    
    Les fenêtres **Output** et **Web Publish Activity** indiquent les actions de déploiement entreprises et signalent la réussite du déploiement.
    
    ![Fenêtre Output affichant la réussite du
    déploiement](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)
    
    Une fois le déploiement réussi, le navigateur par défaut ouvre automatiquement l'URL du site Web déployé, tandis que l'application créée est maintenant exécutée dans le cloud. L'URL de la barre d'adresse du navigateur indique que le site est chargé depuis Internet.
    
    ![Site Web exécuté dans
    Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

7.  Fermez le navigateur.

## Modification et redéploiement

Cette section facultative du didacticiel permet d'apprendre à modifier le projet Web, exécuter le projet en local sur votre ordinateur de développement pour vérifier les modifications apportées, puis déployer la version modifiée vers Azure.

1.  Si vous avez créé un projet MVC, ouvrez le fichier *Views/Home/Index.cshtml* ou *.vbhtml* dans l'**Explorateur de solutions**, remplacez le titre **h1** << ASP.NET >> par << ASP.NET et Azure >>, puis enregistrez le fichier.
    
    ![Projet MVC, fichier
    index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)
    
    ![Modifier le titre h1 du projet
    MVC](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

2.  Si vous avez créé un projet Web Forms, ouvrez le fichier *Default.aspx* dans l'**Explorateur de solutions**, remplacez le titre **h1** << ASP.NET >> par << ASP.NET et Azure >>, puis enregistrez le fichier.
    
    ![Projet Web Forms, fichier
    default.aspx](./media/web-sites-dotnet-get-started-vs2013/default.png)
    
    ![Modifier le titre h1 du projet Web
    Forms](./media/web-sites-dotnet-get-started-vs2013/wfandazure.png)

3.  Appuyez sur CTRL+F5 pour tester vos modifications en exécutant le site sur votre ordinateur local.
    
    ![Site Web exécuté en
    local](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)
    
    L'URL `http://localhost` indique que l'application est exécutée sur votre ordinateur local. L'application est exécutée par défaut dans IIS Express, qui est une version légère d'IIS conçue pour une utilisation pendant le développement d'applications Web.

4.  Fermez le navigateur.

5.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.
    
    ![Sélectionner
    Publier](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)
    
    L'onglet Aperçu de l'Assistant **Publier le site Web** apparaît. Si vous devez modifier des paramètres de publication, vous pouvez choisir un autre onglet. Mais à présent, nous partons du principe que vous voulez procéder à un redéploiement sans modifier les paramètres.

6.  Dans l'Assistant **Publier le site Web** cliquez sur **Publier**.
    
    ![Cliquer sur
    Publier](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)
    
    Visual Studio déploie le projet vers Azure et ouvre le site dans le navigateur par défaut.
    
    ![Site modifié
    déployé](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

Si vous voulez procéder à un redéploiement lorsque vous n'avez pas besoin de modifier les paramètres de publication, utilisez la barre d'outils **Publication Web en un clic** : cette méthode est plus rapide.

![Barre d’outil Publication Web en un
clic](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

La barre d'outils n'est pas activée par défaut. Pour l'activer, accédez au menu **Affichage - Barres d'outils**. Vous pouvez l'utiliser pour sélectionner un profil, cliquer sur un bouton pour publier ou cliquer sur un bouton pour ouvrir l'Assistant **Publier le site Web**.

## Étapes suivantes

Dans ce didacticiel, vous avez appris à créer une application Web simple et à la déployer vers un site Web Azure. Pour en savoir plus, consultez les rubriques et ressources suivantes :

* Autres méthodes de déploiement d'un projet Web
* Gestion, mise à l'échelle et résolution des problèmes d'un site
* Ajout d'une fonctionnalité de base de données et d'autorisation
* Comparaison entre les sites Web, services Cloud et machines virtuelles
  pour les applications Web

### Autres méthodes de déploiement d'un projet Web

Dans ce didacticiel, vous avez appris la méthode la plus rapide pour créer un site et le déployer en une opération. Pour découvrir d'autres méthodes de déploiement, en utilisant Visual Studio ou en [automatisant le déploiement][2] à partir d'un [système de contrôle de code source][3], consultez l'article [Déploiement d'un site Web Azure](/fr-fr/documentation/articles/web-sites-deploy/").

Pour automatiser le déploiement, vous pouvez utiliser des scripts Windows PowerShell. Visual Studio et Azure simplifient cette tâche en générant des scripts PowerShell qui permettent d'effectuer les mêmes opérations de déploiement que dans Visual Studio. Pour en savoir plus, consultez la page [Tout automatiser (développement d'applications de cloud concrètes avec Azure)][4].

### Gestion d'un site Web

Le [portail de gestion Azure](en-us/services/management-portal/) est une interface Web permettant de gérer et de surveiller vos services Azure, tels que les sites Web, les services cloud, les machines virtuelles, les bases de données, etc. Pour découvrir les possibilités de ce portail, rendez-vous sur la page [https://manage.windowsazure.com]() et connectez-vous avec le nom d'utilisateur et le mot de passe de votre compte doté des droits d'administration de votre abonnement Azure. Pour plus d'informations, consultez la page [Gestion de sites Web](/en-us/manage/services/web-sites/how-to-manage-websites/).

Vous pouvez également créer des fonctions de gestion de site Web à partir de l'**Explorateur de serveurs** dans Visual Studio. Pour plus d'informations sur les possibilités de l'**Explorateur de serveurs**, consultez [Résolution des problèmes de sites Web Azure dans Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Mise à l'échelle d'un site Web

Lorsque votre site est public et que son trafic augmente, les temps de réponse peuvent augmenter. Pour résoudre ce problème, vous pouvez facilement ajouter des ressources serveur sous l'onglet **Scale** du portail de gestion. Pour plus d'informations, consultez la page [Mise à l'échelle d'un site Web](/en-us/manage/services/web-sites/how-to-scale-websites/). Notez que l'ajout de ressources serveur pour la mise à l'échelle de sites Web entraîne des frais.

### Résolution des problèmes d'un site Web

Vous pouvez consulter des résultats de suivi ou de journalisation pour pouvoir résoudre un problème. Visual Studio fournit des outils intégrés qui facilitent l'affichage des journaux Azure durant leur génération en temps réel. Vous pouvez aussi exécuter le mode Debug à distance dans Azure. Pour plus d'informations, consultez [Résolution des problèmes de sites Web Azure dans Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Ajout d'une fonctionnalité de base de données et d'autorisation

La plupart des sites Web de production utilisent une base de données et limitent certaines fonctions de site à certains utilisateurs autorisés. Pour consulter un didacticiel traitant de l'accès aux bases de données, de l'authentification et de l'autorisation, consultez la page [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/).

### Détermination de l'exécution ou non de votre application dans un service cloud

Azure vous permet d'exécuter des applications Web dans des sites Web (comme vous l'avez vu dans ce didacticiel), mais aussi dans des services
cloud ou des machines virtuelles. Pour plus d'informations, consultez les articles [Modèles d'exécution Azure](/en-us/develop/net/fundamentals/compute/) et [Sites Web, services cloud et machines virtuelles Azure : quand les utiliser ?](/en-us/manage/services/web-sites/choose-web-app-service/).



[1]: http://www.asp.net/get-started/websites
[2]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
[3]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything