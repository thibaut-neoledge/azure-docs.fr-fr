<properties pageTitle="Prise en main des sites web&nbsp;Azure pour&nbsp;ASP.NET" metaKeywords="" description="Ce didacticiel explique comment cr&eacute;er un projet web&nbsp;ASP.NET dans Visual&nbsp;Studio&nbsp;2013 et comment le d&eacute;ployer dans un site web&nbsp;Azure. En moins de 15&nbsp;minutes, vous disposerez d'une application s'ex&eacute;cutant dans le cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Prise en main d'Azure et ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="hero-article" ms.date="09/24/2014" ms.author="tdykstra" />

# Prise en main d'Azure et ASP.NET

Ce didacticiel vous montre comment créer une application web ASP.NET et la déployer vers un site web Azure en utilisant Visual Studio 2013 ou Visual Studio Express 2013 pour le web. Ce didacticiel part du principe que vous n’avez aucune expérience préalable avec Azure ou ASP.NET. À la fin de ce didacticiel, vous disposerez d’une application Web simple opérationnelle dans le cloud.

Vous pouvez ouvrir gratuitement un compte Azure. Si vous n'avez pas déjà Visual Studio 2013, le Kit de développement logiciel (SDK) installe automatiquement Visual Studio Express 2013 pour le Web. Vous pouvez donc commencer vos développements Azure gratuitement.

Vous apprendrez ce qui suit :

-   configurer votre ordinateur pour le développement Azure en installant le Kit de développement logiciel (SDK) Azure ;
-   créer un projet web Visual Studio ASP.NET et le déployer vers un site web Azure ;
-   modifier ce projet et redéployer l'application ;
-   utiliser le portail de gestion Azure pour surveiller et gérer votre site web.

L'illustration suivante présente l'application terminée :

![Page d’accueil du site Web][Page d’accueil du site Web]

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Pour suivre ce didacticiel, vous avez besoin d'un compte Azure.</h5>
  <ul>
    <li>Vous pouvez <a href="/fr-fr/pricing/free-trial/?WT.mc_id=A261C142F">ouvrir un compte Azure gratuitement</a>&nbsp;: vous obtenez alors des cr&eacute;dits dont vous pouvez vous servir pour tester les services Azure payants, et m&ecirc;me lorsqu'ils sont &eacute;puis&eacute;s, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Sites Web.</li>
    <li>Vous pouvez <a href="/fr-fr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">activer les avantages de l'abonnement MSDN</a>&nbsp;: votre abonnement MSDN vous donne droit chaque mois &agrave; des cr&eacute;dits dont vous pouvez vous servir pour les services Azure payants.</li>
  <ul>
</div>

### Sections du didacticiel

-   [Configuration de l’environnement de développement][Configuration de l’environnement de développement]
-   [Création d'une application web ASP.NET dans Visual Studio][Création d'une application web ASP.NET dans Visual Studio]
-   [Déploiement de l'application dans Azure][Déploiement de l'application dans Azure]
-   [Modification et redéploiement][Modification et redéploiement]
-   [Surveillance et gestion du site dans le portail de gestion][Surveillance et gestion du site dans le portail de gestion]
-   [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Création d’une application web ASP.NET

Vous devez tout d’abord créer un projet d’application Web. Visual Studio crée automatiquement le site web Azure vers lequel vous allez déployer votre projet. Le schéma suivant illustre ces deux étapes.

![Diagram showing project creation and deployment steps][Diagram showing project creation and deployment steps]

1.  Ouvrez Visual Studio 2013 ou Visual Studio 2013 Express pour le Web.

2.  Dans le menu **Fichier**, cliquez sur **Nouveau projet**.

3.  Dans la boîte de dialogue **Nouveau projet**, cliquez sur **C#** \> **Web** \> **Application Web ASP.NET**. Vous pouvez, si vous préférez, sélectionner **Visual Basic**.

4.  Vérifiez que la version cible sélectionnée est **.NET Framework 4.5**.

5.  Nommez l'application **MonExemple**, puis cliquez sur **OK**.

    ![New Project dialog box][New Project dialog box]

6.  Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle **MVC**. Si vous préférez utiliser Web Forms ASP.NET, vous pouvez sélectionner le modèle **Web Forms**.

    [MVC et Web Forms][MVC et Web Forms] sont des infrastructures ASP.NET permettant de créer des sites web. Pour les besoins de ce didacticiel, vous pouvez choisir l'un ou l'autre, mais si vous sélectionnez Web Forms, vous devrez modifier *Default.aspx* ultérieurement, quand le didacticiel vous invitera à modifier *Index.cshtml*.

7.  Cliquez sur **Modifier l'authentification**.

    ![Boîte de dialogue New ASP.NET Project][Boîte de dialogue New ASP.NET Project]

8.  Dans la boîte de dialogue **Modifier l'authentification**, cliquez sur **Aucune authentification**, puis sur **OK**.

    ![Aucune authentification][Aucune authentification]

    L'exemple d'application que vous créez ne permettra pas aux utilisateurs de se connecter. La section [Étapes suivantes][Étapes suivantes] propose un lien vers un didacticiel qui implémente les fonctions d'authentification et d'autorisation.

9.  Dans la boîte de dialogue **Nouveau projet ASP.NET**, conservez les paramètres existants sous **Azure**, puis cliquez sur **OK**.

    ![Boîte de dialogue New ASP.NET Project][1]

    Les paramètres par défaut indiquent que Visual Studio va créer un site web Azure pour votre projet web. Dans la section suivante du didacticiel, vous allez déployer le projet web vers le site web nouvellement créé.

    (La légende de la case à cocher peut être **Héberger dans le cloud** ou **Créer des ressources distantes**. Dans les deux cas, l'effet est le même.)

10. Si vous n’êtes pas déjà connecté à Azure, Visual Studio vous invite à le faire. Cliquez sur **Se connecter**.

    ![Se connecter à Azure][Se connecter à Azure]

11. Dans la boîte de dialogue **Connexion à Azure**, entrez l'ID et le mot de passe du compte utilisé pour gérer votre abonnement Azure.

    Lorsque vous êtes connecté, la boîte de dialogue **Configure Azure Site Settings** vous demande les ressources que vous voulez créer.

    ![Connecté à Azure][Connecté à Azure]

12. Visual Studio fournit un **nom de site** par défaut. Azure l’utilise en tant que préfixe pour l’URL de votre application. Si vous préférez, entrez un autre nom de site.

    L'URL complète sera constituée de ce que vous avez entré ici plus *.azurewebsites.net* (comme indiqué en regard de la zone de texte **Nom du site**). Par exemple, si le nom du site est `MyExample6442`, l’URL sera `MyExample6442.azurewebsites.net`. L'URL doit être unique. Si une autre personne utilise déjà celle que vous avez entrée, un point d'exclamation rouge s'affiche à droite au lieu d'une coche verte, et vous devez entrer un autre nom de site.

13. Dans la liste déroulante **Région**, sélectionnez le lieu le plus proche de vous.

    Ce paramètre indique le centre de données Azure sur lequel votre site web sera exécuté. Pour les besoins de ce didacticiel, vous pouvez sélectionner n'importe quelle région : la différence ne sera pas sensible. Mais pour un site de production, vous souhaiterez que votre serveur web soit le plus proche possible des navigateurs qui accèdent à votre site, afin de minimiser la [latence][latence].

14. Ne modifiez pas les champs de base de données.

    Vous n’utiliserez pas de base de données dans le cadre de ce didacticiel. La section [Étapes suivantes][Étapes suivantes] située à la fin de ce didacticiel mène vers un autre didacticiel qui vous expliquera comment utiliser une base de données.

15. Cliquez sur **OK**.

    En quelques secondes, Visual Studio crée le projet web dans le dossier indiqué, puis crée le site web dans la région Azure indiquée.

    La fenêtre **Explorateur de solutions** indique les fichiers et les dossiers du nouveau projet.

    ![Explorateur de solutions][Explorateur de solutions]

    La fenêtre **Web Publish Activity** indique que le site a été créé.

    ![Site Web créé][Site Web créé]

    Et le site apparaît dans l'Explorateur de serveurs.

    ![Site Web créé][2]

## Déploiement de l'application dans Azure

1.  Dans la fenêtre **Web Publish Activity**, cliquez sur **Publish MyExample to this site now**.

    ![Site Web créé][3]

    Au bout de quelques secondes, l’Assistant **Publier le site Web** apparaît.

    Les paramètres dont Visual Studio a besoin pour déployer votre projet vers Azure ont été enregistrés dans un *profil de publication*. L'Assistant vous permet de passer en revue et de modifier ces paramètres.

2.  Dans l’onglet **Connexion** de l’Assistant **Publier le site Web**, cliquez sur **Valider la connexion** pour vous assurer que Visual Studio peut se connecter à Azure afin de déployer le projet Web.

    ![Valider la connexion][Valider la connexion]

    Une fois la connexion validée, une coche verte est affichée en regard du bouton **Validate Connection**.

3.  Cliquez sur **Suivant**.

    ![Connexion correctement validée][Connexion correctement validée]

4.  Sous l’onglet **Paramètres**, cliquez sur **Suivant**.

    ![Onglet Paramètres][Onglet Paramètres]

    Vous pouvez accepter les valeurs par défaut pour **Configuration** et **Options de publication des fichiers**.

    La liste déroulante **Configuration** vous permet de déployer une version de débogage pour le débogage distant. La section [Étapes suivantes][Étapes suivantes] renvoie vers un didacticiel qui explique comment exécuter Visual Studio en mode de débogage à distance.

    Si vous développez **Options de publication des fichiers**, vous avez accès à plusieurs paramètres vous permettant de traiter des scénarios qui ne s'appliquent pas à ce didacticiel :

    -   Supprimer les fichiers supplémentaires à la destination.

        Supprime du serveur tous les fichiers qui ne sont pas dans votre projet. Ceci peut vous être utile si vous déployez un projet vers un site que vous avez déjà utilisé précédemment pour déployer un autre projet.

    -   Précompiler pendant la publication.

        Permet de réduire les temps de préchauffage lors de la première demande pour les sites volumineux.

    -   Exclure les fichiers du dossier App\_Data.

        Pour les tests, vous conservez parfois dans le dossier App\_Data un fichier de base de données SQL Server que vous ne souhaitez pas déployer en production.

5.  Sous l'onglet **Aperçu**, cliquez sur **Démarrer l'aperçu**.

    ![Bouton Démarrer l'aperçu sous l'onglet Aperçu][Bouton Démarrer l'aperçu sous l'onglet Aperçu]

    L'onglet affiche la liste des fichiers qui seront copiés sur le serveur. L'affichage de l'aperçu n'est pas requis pour publier l'application. Cependant, il s'agit d'une fonction utile à ne pas négliger.

6.  Cliquez sur **Publier**.

    ![Fichier de résultat de la fonction Démarrer l'aperçu][Fichier de résultat de la fonction Démarrer l'aperçu]

    Visual Studio démarre le processus de copie des fichiers vers le serveur Azure.

    Les fenêtres **Output** et **Web Publish Activity** indiquent les actions de déploiement entreprises et signalent la réussite du déploiement.

    ![Fenêtre Output affichant la réussite du déploiement][Fenêtre Output affichant la réussite du déploiement]

    Une fois le déploiement réussi, le navigateur par défaut ouvre automatiquement l'URL du site web déployé, tandis
    que l'application créée est maintenant exécutée dans le cloud. L’URL de la barre d’adresse du navigateur indique que le site est chargé depuis Internet.

    ![Site Web exécuté dans Azure][Site Web exécuté dans Azure]

7.  Fermez le navigateur.

## Modification et redéploiement

Cette section du didacticiel permet de modifier le titre **h1** de la page d'accueil, d'exécuter le projet en local sur votre ordinateur de développement pour vérifier les modifications apportées, puis de déployer la version modifiée vers Azure.

1.  Ouvrez le fichier *Views/Home/Index.cshtml* ou *.vbhtml* dans l'**Explorateur de solutions**, remplacez le titre **h1** « ASP.NET » par « ASP.NET et Azure », puis enregistrez le fichier.

    ![Projet MVC, fichier index.cshtml][Projet MVC, fichier index.cshtml]

    ![Modifier le titre h1 du projet MVC][Modifier le titre h1 du projet MVC]

2.  Appuyez sur Ctrl+F5 pour voir le titre mis à jour en exécutant le site sur votre ordinateur local.

    ![Site Web exécuté en local][Site Web exécuté en local]

    L'URL `http://localhost` indique que l'application est exécutée sur votre ordinateur local. L’application est exécutée par défaut dans IIS Express, qui est une version légère d’IIS conçue pour une utilisation pendant le développement d’applications Web.

3.  Fermez le navigateur.

4.  Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

    ![Sélectionner Publier][Sélectionner Publier]

    L’onglet Aperçu de l’Assistant **Publier le site Web** apparaît. Si vous devez modifier des paramètres de publication, vous pouvez choisir un autre onglet. Mais à présent, nous partons du principe que vous voulez procéder à un redéploiement sans modifier les paramètres.

5.  Dans l’Assistant **Publier le site Web** cliquez sur **Publier**.

    ![Cliquer sur Publier][Cliquer sur Publier]

    Visual Studio déploie le projet vers Azure et ouvre le site dans le navigateur par défaut.

    ![Site modifié déployé][Page d’accueil du site Web]

**Conseil :** vous pouvez activer la barre d'outils **Publication Web en un clic** pour accélérer encore le déploiement. Cliquez sur **Affichage** \> **Barres d'outils**, puis sélectionnez **Publication Web en un clic**. La barre d'outils vous permet de sélectionner un profil, de cliquer sur un bouton pour publier ou de cliquer sur un bouton pour ouvrir l'Assistant **Publier le site Web**.

![Web One Click Publish Toolbar][Web One Click Publish Toolbar]

## Surveillance et gestion du site dans le portail de gestion

Le [portail de gestion Azure][portail de gestion Azure] est une interface web permettant de gérer et de surveiller vos services Azure, tels que le site web que vous venez de créer. Dans cette section du didacticiel, vous allez examiner certaines des possibilités offertes par le portail.

1.  Dans votre navigateur, accédez à [http://manage.windowsazure.com][http://manage.windowsazure.com] et connectez-vous avec vos informations d'identification Azure.

    Le portail affiche la liste de vos services Azure.

2.  Cliquez sur le nom de votre site web.

    ![Portal home page with new web site called out][Portal home page with new web site called out]

3.  Cliquez sur l'onglet **Tableau de bord**.

    Sous l'onglet **Tableau de bord** s'affichent un aperçu des statistiques d'utilisation et un lien pour un certain nombre de fonctions de gestion de site communément employées. Sous **Aperçu rapide**, vous trouverez également un lien vers la page d'accueil de votre application.

    ![Portal web site dashboard tab][Portal web site dashboard tab]

    À ce stade, le trafic sur votre site est encore très faible et il ne sera peut-être pas visible dans le graphique. Si vous accédez à votre application, actualisez la page à plusieurs reprises, puis actualisez la page **Tableau de bord** du portail, des statistiques commenceront à s'afficher. Vous pouvez cliquer sur l'onglet **Surveillance** pour plus de détails.

4.  Cliquez sur l’onglet **Configurer**.

    L'onglet [Configurer][Configurer] vous permet, entre autres, de contrôler la version .NET utilisée pour le site, d'activer des fonctionnalités telles que [WebSockets][WebSockets] et la [journalisation des diagnostics][journalisation des diagnostics] et de définir des [valeurs de chaîne de connexion][valeurs de chaîne de connexion].

    ![Portal web site configure tab][Portal web site configure tab]

5.  Cliquez sur l'onglet **Scale**.

    Pour les niveaux payants du service Sites Web, l'onglet [Mettre à l'échelle][Mettre à l'échelle] vous permet de contrôler la taille et le nombre des machines qui desservent votre application web pour traiter les fluctuations dans le trafic.

    Vous pouvez effectuer la mise à l'échelle manuellement ou configurer des critères ou des planifications pour la mise à l'échelle automatique.

    ![Portal website scale tab][Portal website scale tab]

Ce ne sont là que quelques-unes des fonctionnalités du portail de gestion. Vous pouvez également créer des sites web, supprimer des sites existants, arrêter et redémarrer des sites, et gérer d'autres types de services Azure, par exemple, des bases de données et des machines virtuelles.

**Conseil :** la version préliminaire d'un nouveau portail de gestion, qui remplacera un jour celle que vous avez utilisée, est d'ores et déjà disponible. Pour plus d'informations, consultez la page [Version préliminaire du portail Azure][Version préliminaire du portail Azure].

## Étapes suivantes

Dans ce didacticiel, vous avez appris à créer une application web simple et à la déployer vers un site web Azure. Pour en savoir plus, consultez les rubriques et ressources suivantes :

-   Autres méthodes de déploiement d’un projet Web

    Dans ce didacticiel, vous avez appris la méthode la plus rapide pour créer un site et le déployer en une opération. Pour découvrir d'autres méthodes de déploiement, consistant à utiliser Visual Studio ou à [automatiser le déploiement][automatiser le déploiement] à partir d'un [système de contrôle de code source][système de contrôle de code source], consultez la page [Déploiement d'un site web Azure][Déploiement d'un site web Azure].

    Visual Studio génère également des scripts Windows PowerShell qui vous permettent d'automatiser le déploiement. Pour en savoir plus, consultez la page [Tout automatiser (développement d’applications de cloud concrètes avec Azure)][Tout automatiser (développement d’applications de cloud concrètes avec Azure)].

-   Gestion d'un site web dans Visual Studio

    Pour plus d'informations sur les fonctions de gestion de site disponibles dans l'**Explorateur de serveurs**, consultez la page [Résolution des problèmes de sites web Azure dans Visual Studio][Résolution des problèmes de sites web Azure dans Visual Studio].

-   Dépannage d'un site web

    Visual Studio fournit des fonctionnalités qui facilitent l'affichage des journaux Azure durant leur génération en temps réel. Vous pouvez aussi exécuter le mode Debug à distance dans Azure. Pour plus d'informations, consultez la page [Résolution des problèmes de sites web Azure dans Visual Studio][Résolution des problèmes de sites web Azure dans Visual Studio].

-   Ajout d'une fonctionnalité de base de données et d'autorisation

    Pour accéder à un didacticiel expliquant comment accéder aux bases de données et limiter certaines fonctions de site à certains utilisateurs autorisés, consultez la page [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site web Azure][Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site web Azure].

-   Ajout d'un nom de domaine personnalisé et de SSL

    Pour des informations sur l'utilisation de SSL et de votre propre nom de domaine (par exemple, www.contoso.com au lieu de contoso.azurewebsites.net), consultez les ressources suivantes :

    -   [Configuration d'un nom de domaine personnalisé pour un site web Azure][Configuration d'un nom de domaine personnalisé pour un site web Azure].
    -   [Activation du protocole HTTPS pour un site web Azure][Activation du protocole HTTPS pour un site web Azure]
-   Manière d'éviter les délais de sortie de veille après les périodes d'inactivité

    Par défaut, les sites web sont déchargés s'ils sont inactifs depuis un certain temps. La première demande après une telle période doit patienter jusqu'à ce que le site soit rechargé. Pour éviter ce temps d'attente, vous pouvez activer la fonction AlwaysOn. Pour plus d'informations, consultez la page [Configuration des sites web][Configuration des sites web].

-   Ajout de fonctions en temps réel telles que la conversation instantanée

    Si votre site web doit inclure des fonctions en temps réel (telles qu'un service de conversation instantanée, un jeu, un afficheur en temps réel des cours de bourse, etc.), vous pouvez obtenir des performances optimales en utilisant [ASP.NET SignalR][ASP.NET SignalR] avec la méthode de transport [WebSockets][WebSockets]. Pour plus d'informations, consultez la page [Utilisation de SignalR avec Sites Web Windows Azure][Utilisation de SignalR avec Sites Web Windows Azure].

-   Comparaison entre Sites Web Azure, Cloud Services et Virtual Machines pour les applications web

    Azure vous permet d'exécuter des applications web dans des Sites Web (comme vous l'avez vu dans ce didacticiel), mais aussi dans Cloud Services ou Virtual Machines. Pour plus d'informations, consultez les pages [Modèles d'exécution Azure][Modèles d'exécution Azure] et [Sites Web Azure, Cloud Services et Virtual Machines : et Azure Virtual Machines][Sites Web Azure, Cloud Services et Virtual Machines : et Azure Virtual Machines]

  [Page d’accueil du site Web]: ./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png
  [Configuration de l’environnement de développement]: #set-up-the-development-environment
  [Création d'une application web ASP.NET dans Visual Studio]: #create-an-aspnet-web-application
  [Déploiement de l'application dans Azure]: #deploy-the-application-to-azure
  [Modification et redéploiement]: #make-a-change-and-redeploy
  [Surveillance et gestion du site dans le portail de gestion]: #monitor-and-manage-the-site-in-the-management-portal
  [Étapes suivantes]: #next-steps
  [Diagram showing project creation and deployment steps]: ./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png
  [New Project dialog box]: ./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png
  [MVC et Web Forms]: http://www.asp.net/get-started/websites
  [Boîte de dialogue New ASP.NET Project]: ./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png
  [Aucune authentification]: ./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png
  [1]: ./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png
  [Se connecter à Azure]: ./media/web-sites-dotnet-get-started-vs2013/signin.png
  [Connecté à Azure]: ./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png
  [latence]: http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090
  [Explorateur de solutions]: ./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png
  [Site Web créé]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png
  [2]: ./media/web-sites-dotnet-get-started-vs2013/siteinse.png
  [3]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png
  [Valider la connexion]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png
  [Connexion correctement validée]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png
  [Onglet Paramètres]: ./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png
  [Bouton Démarrer l'aperçu sous l'onglet Aperçu]: ./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png
  [Fichier de résultat de la fonction Démarrer l'aperçu]: ./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png
  [Fenêtre Output affichant la réussite du déploiement]: ./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png
  [Site Web exécuté dans Azure]: ./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png
  [Projet MVC, fichier index.cshtml]: ./media/web-sites-dotnet-get-started-vs2013/index.png
  [Modifier le titre h1 du projet MVC]: ./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png
  [Site Web exécuté en local]: ./media/web-sites-dotnet-get-started-vs2013/localandazure.png
  [Sélectionner Publier]: ./media/web-sites-dotnet-get-started-vs2013/choosepublish.png
  [Cliquer sur Publier]: ./media/web-sites-dotnet-get-started-vs2013/clickpublish.png
  [Web One Click Publish Toolbar]: ./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png
  [portail de gestion Azure]: /fr-fr/services/management-portal/
  [http://manage.windowsazure.com]: 
  [Portal home page with new web site called out]: ./media/web-sites-dotnet-get-started-vs2013/portalhome.png
  [Portal web site dashboard tab]: ./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png
  [Configurer]: /fr-fr/documentation/articles/web-sites-configure//
  [WebSockets]: /blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/
  [journalisation des diagnostics]: /fr-fr/documentation/articles/web-sites-enable-diagnostic-log/
  [valeurs de chaîne de connexion]: /blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [Portal web site configure tab]: ./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png
  [Mettre à l'échelle]: /fr-fr/documentation/articles/web-sites-scale/
  [Portal website scale tab]: ./media/web-sites-dotnet-get-started-vs2013/portalscale.png
  [Version préliminaire du portail Azure]: /fr-fr/overview/preview-portal/
  [automatiser le déploiement]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [système de contrôle de code source]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [Déploiement d'un site web Azure]: /fr-fr/documentation/articles/web-sites-deploy/"
  [Tout automatiser (développement d’applications de cloud concrètes avec Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Résolution des problèmes de sites web Azure dans Visual Studio]: /fr-fr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site web Azure]: /fr-fr/develop/net/tutorials/web-site-with-sql-database/
  [Configuration d'un nom de domaine personnalisé pour un site web Azure]: /fr-fr/documentation/articles/web-sites-custom-domain-name/
  [Activation du protocole HTTPS pour un site web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-configure-ssl-certificate/
  [Configuration des sites web]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [Utilisation de SignalR avec Sites Web Windows Azure]: http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites
  [Modèles d'exécution Azure]: /fr-fr/develop/net/fundamentals/compute/
  [Sites Web Azure, Cloud Services et Virtual Machines : et Azure Virtual Machines]: /fr-fr/manage/services/web-sites/choose-web-app-service/
