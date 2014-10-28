<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure Websites and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Prise en main de Sites Web Azure et ASP.NET

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio&nbsp;2013">Visual Studio&nbsp;2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio&nbsp;2012" class="current">Visual Studio&nbsp;2012</a></div>

<div class="dev-callout"><strong>Remarque</strong><p>Une <a href="/en-us/develop/net/tutorials/get-started/">version plus r&eacute;cente de ce didacticiel</a> est disponible. Vous pouvez continuer &agrave; suivre cette version si vous souhaitez utiliser Visual Studio&nbsp;2012, mais les nouvelles fonctionnalit&eacute;s du Kit de d&eacute;veloppement logiciel (SDK) Azure n'y sont pas pr&eacute;sent&eacute;es.</p></div>

Ce didacticiel vous montre comment déployer une application web ASP.NET sur un site web Azure en utilisant l'Assistant Publier le site Web dans Visual Studio 2012 ou Visual Studio Express 2012 pour le web. Si vous préférez, vous pouvez suivre les étapes du didacticiel en utilisant Visual Studio 2010 ou Visual Web Developer Express 2010.

Vous pouvez ouvrir gratuitement un compte Azure. Si vous n'avez pas déjà Visual Studio 2012, le Kit de développement logiciel (SDK) installe automatiquement Visual Studio Express 2012 pour le Web. Vous pouvez donc commencer vos développements Azure gratuitement.

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. ﻿À la fin de ce didacticiel, vous disposerez d'une application Web simple et fonctionnelle dans le cloud.

Vous apprendrez ce qui suit :

-   configurer votre ordinateur pour le développement Azure en installant le Kit de développement logiciel (SDK) Azure ;
-   créer et publier un projet Visual Studio ASP.NET MVC 4 sur un site web Azure.

L'illustration suivante présente l'application terminée :

![Exemple de site Web][Exemple de site Web]

<div class="dev-callout"><p><strong>Remarque</strong> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez <a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activer les avantages de votre abonnement MSDN</a> ou <a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">obtenir une &eacute;valuation gratuite</a>.</p></div>

### Sections du didacticiel

1.  [Configuration de l’environnement de développement][Configuration de l’environnement de développement]
2.  [Création d'un site Web dans Azure][Création d'un site Web dans Azure]
3.  [Création d'une application ASP.NET MVC 4][Création d'une application ASP.NET MVC 4]
4.  [Déploiement de l'application dans Azure][Déploiement de l'application dans Azure]
5.  [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [install-sdk-2012-only][install-sdk-2012-only]]

## <a name="setupwindowsazure"></a><span class="short-header">Création d'un site</span>Création d'un site web

L'étape suivante consiste à créer le site web Azure.

1.  Dans le [portail de gestion Azure][portail de gestion Azure], cliquez sur **Sites Web**, puis sur **Nouveau**.

 	![Nouveau site Web][Nouveau site Web]

1.  Cliquez sur **Create**.

    ![Quick create][Quick create]

2.  Lors de l'étape **Créer un site Web** de l'Assistant, entrez une chaîne dans la zone **URL** pour l'utiliser en tant qu'URL unique pour votre application. L'URL complète se composera du texte entré dans cette zone, ainsi que du suffixe affiché en regard de la zone. L'illustration montre **example1**. Cependant, si une autre personne a déjà pris cette chaîne comme URL, vous devez entrer une autre valeur.

3.  Dans la liste déroulante **Région**, sélectionnez la région la plus proche de vous.
    Ce paramètre indique le centre de données dans lequel s'exécutera votre machine virtuelle.

4.  Cliquez sur la flèche **Créer un site Web**.

    ![Créer un site Web][Créer un site Web]

    Le portail de gestion renvoie vers la page Sites Web, tandis que la colonne **Statut** indique que le site est en cours de création. Après un temps d'attente (généralement inférieur à une minute), la colonne **Statut** indique que la création du site est réussie. Dans la barre de navigation de gauche, le nombre de sites de votre compte apparaît en regard de l'icône **Sites Web**.

    ![Page Sites Web du portail de gestion, site Web créé][Page Sites Web du portail de gestion, site Web créé]

## <a name="createmvc4app"></a><span class="short-header">Création de l'application</span>Création d'une application ASP.NET MVC 4

Vous avez créé un site web Azure, mais il est encore vide. La prochaine étape consiste à créer l'application Web Visual Studio que vous allez publier sur Azure.

### Création du projet

1.  Démarrez Visual Studio 2012 ou Visual Studio 2012 Express pour le Web.

2.  Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

![Nouveau projet dans le menu Fichier][Nouveau projet dans le menu Fichier]

1.  Dans la boîte de dialogue **Nouveau projet**, développez **C#** et sélectionnez **Web** sous **Modèles installés**. Sélectionnez ensuite **Application Web ASP.NET MVC 4**.

2.  Vérifiez que la version cible sélectionnée est bien **.NET Framework 4.5**.

3.  Nommez l'application **MonExemple**, puis cliquez sur **OK**.

![Boîte de dialogue Nouveau projet][Boîte de dialogue Nouveau projet]

1.  Dans la boîte de dialogue **Nouveau projet ASP.NET MVC 4**, sélectionnez le modèle **Application Internet**, puis cliquez sur **OK**.

![Boîte de dialogue Nouveau projet ASP.NET MVC 4][Boîte de dialogue Nouveau projet ASP.NET MVC 4]

### Exécution locale de l'application

1.  Appuyez sur **CTRL**+**F5** pour exécuter l'application. La page d’accueil de l’application apparaît dans le navigateur par défaut.

![Site Web exécuté en local][Site Web exécuté en local]

Vous avez fait tout ce qu'il fallait pour créer une application simple que vous allez déployer dans Azure.

## <a name="deploytowindowsazure"></a><span class="short-header">Déploiement de l'application</span>Déploiement de l'application sur Azure

1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.

    ![Publier dans le menu contextuel du projet][Publier dans le menu contextuel du projet]

L'Assistant **Publier le site Web** s'ouvre.

1.  Sous l'onglet **Profil** de l'Assistant **Publier le site Web**, cliquez sur **Importer**.

    ![Importer les paramètres de publication][Importer les paramètres de publication]

La boîte de dialogue **Importer le profil de publication** apparaît.

1.  Si vous n'avez pas encore ajouté votre abonnement Azure dans Visual Studio, procédez comme suit. Au cours de ces étapes, vous allez ajouter votre abonnement de telle sorte que votre site web figure dans la liste déroulante située sous **Importer à partir d'un site Web Azure**.

    -   Dans la boîte de dialogue **Importer le profil de publication**, cliquez sur **Importer à partir d'un site Web Azure**, puis sur **Ajouter un abonnement Azure**.

    ![Ajouter un abonnement Azure][Ajouter un abonnement Azure]

    -   Dans la boîte de dialogue **Import Azure Subscriptions**, cliquez sur **Télécharger le fichier d'abonnement**.

    ![Télécharger le fichier d'abonnement][Télécharger le fichier d'abonnement]

    -   Dans la fenêtre de votre navigateur, enregistrez le fichier *.publishsettings*.

    ![Télécharger le fichier .publishsettings][Télécharger le fichier .publishsettings]

    -   Dans la boîte de dialogue **Import Azure Subscriptions**, cliquez sur **Parcourir** et accédez au fichier *.publishsettings*.

    ![Télécharger l'abonnement][Télécharger l'abonnement]

    -   Cliquez sur **Importer**.

    ![Importer][Importer]

    > [WACOM.NOTE]
    > Le fichier .publishsettings contient les informations d'identification (non codées) servant à gérer vos abonnements et services Azure. Pour des raisons de sécurité, il est recommandé de stocker ce fichier temporairement en dehors de vos répertoires sources (par exemple, dans le dossier Bibliothèques\\Documents), puis de le supprimer une fois l'importation terminée. Si un utilisateur malveillant accède au fichier .publishsettings, il peut modifier, créer et supprimer vos services Azure.

2.  Dans la boîte de dialogue **Importer le profil de publication**, sélectionnez **Importer à partir d'un site Web Azure**, sélectionnez votre site web dans la liste déroulante, puis cliquez sur **OK**.

    ![Importer le profil de publication][Importer le profil de publication]

3.  Sous l'onglet **Connection**, cliquez sur **Validate Connection** pour vous assurer que les paramètres sont corrects.

    ![Valider la connexion][Valider la connexion]

4.  Une fois la connexion validée, une coche verte est affichée en regard du bouton **Validate Connection**. Cliquez sur **Suivant**.

    ![Connexion correctement validée][Connexion correctement validée]

5.  Sous l'onglet **Paramètres**, désactivez l'option **Use this connection string at runtime**, car cette application n'utilise pas de base de données. Vous pouvez accepter les paramètres par défaut pour les autres options de cette page. Vous déployez une configuration de build Release et il n'est pas nécessaire de supprimer les fichiers du serveur de destination, de précompiler l'application ni d'exclure les fichiers du dossier App_Data.
    Cliquez sur **Suivant**.

    ![Onglet Paramètres][Onglet Paramètres]

6.  Sous l'onglet **Aperçu**, cliquez sur **Démarrer l'aperçu**.

    ![Bouton Démarrer l'aperçu sous l'onglet Aperçu][Bouton Démarrer l'aperçu sous l'onglet Aperçu]

    L'onglet affiche la liste des fichiers qui seront copiés sur le serveur. L'affichage de l'aperçu n'est pas requis pour publier l'application. Cependant, il s'agit d'une fonction utile à ne pas négliger. Dans ce cas, vous n'avez rien à faire avec la liste des fichiers affichée.

    ![Fichier de résultat de la fonction Démarrer l'aperçu][Fichier de résultat de la fonction Démarrer l'aperçu]

7.  Cliquez sur **Publier**. Visual Studio démarre le processus de copie des fichiers vers le serveur Azure.

8.  La fenêtre **Output** indique les actions de déploiement entreprises et signale la réussite du déploiement.

    ![Fenêtre Output affichant la réussite du déploiement][Fenêtre Output affichant la réussite du déploiement]

9.  Une fois le déploiement réussi, le navigateur par défaut ouvre automatiquement l’URL du site web déployé.
    L’application que vous avez créée est maintenant exécutée dans le cloud.

    ![Site Web exécuté dans Azure][Exemple de site Web]

## <a name="nextsteps"></a><span class="short-header">Étapes suivantes</span>Étapes suivantes

Dans ce didacticiel, vous avez appris à déployer une application web simple sur un site web Azure. D'autres ressources sont disponibles pour vous montrer comment gérer le site, le mettre à l'échelle et résoudre les problèmes y afférents, comment ajouter des fonctionnalités de base de données, d'authentification et d'autorisation, et comment déterminer si votre application doit s'exécuter dans un service cloud Azure plutôt que sur un site web Azure.

### Gestion d'un site web

Dès que vous en avez terminé avec le site, vous pouvez le supprimer, le mettre temporairement hors ligne ou en modifier les paramètres. Vous pouvez effectuer certaines de ces fonctions à partir de l'**Explorateur de serveurs** de Visual Studio.

![Sites Web Azure dans l'Explorateur de serveurs][Sites Web Azure dans l'Explorateur de serveurs]

![Configuration du site Web dans Visual Studio][Configuration du site Web dans Visual Studio]

Pour supprimer votre site web, vous pouvez utiliser le portail de gestion Azure. La capture d'écran suivante illustre les boutons **Arrêter**, **Redémarrer** et **Supprimer** de l'onglet **Tableau de bord** du portail de gestion.

![Onglet Tableau de bord du portail de gestion][Onglet Tableau de bord du portail de gestion]

Vous pouvez modifier les paramètres du site sur l'onglet **Configurer**. Pour plus d'informations, consultez la page [Gestion de sites Web][Gestion de sites Web].

### Mise à l'échelle d'un site web

Lorsque votre site est public et que son trafic augmente, les temps de réponse peuvent augmenter. Pour résoudre ce problème, vous pouvez facilement ajouter des ressources serveur sous l’onglet **Scale** du portail de gestion.

![Onglet Échelle du portail de gestion][Onglet Échelle du portail de gestion]

Pour plus d'informations, consultez la page [Mise à l'échelle d’un site Web][Mise à l'échelle d’un site Web]. Notez que l'ajout de ressources serveur pour la mise à l'échelle d'un site web entraîne des frais.

### Dépannage d'un site web

Vous pouvez consulter des résultats de suivi ou de journalisation pour pouvoir résoudre un problème. Visual Studio fournit des outils intégrés qui facilitent l'affichage des journaux Azure durant leur génération en temps réel.

![Journaux dans Visual Studio][Journaux dans Visual Studio]

Pour plus d'informations, consultez la page [Résolution des problèmes de sites Web Azure dans Visual Studio][Résolution des problèmes de sites Web Azure dans Visual Studio].

### Ajout d'une fonctionnalité de base de données et d'autorisation

La plupart des sites web de production utilisent une base de données et limitent certaines fonctions de site à certains utilisateurs autorisés. Pour consulter un didacticiel traitant de l'accès aux bases de données, de l'authentification et de l'autorisation, consultez la page [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure][Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure].

### Détermination de l'exécution ou non de votre application dans un service cloud

Dans certains cas, vous pouvez exécuter votre application dans un service cloud Azure au lieu d'un site web Azure. Pour plus d'informations, consultez les pages [Modèles d'exécution Azure][Modèles d'exécution Azure] et [Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines][Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines]. Pour accéder à une série de didacticiels qui vous expliquent comment créer une application Web ASP.NET multiniveau et la déployer sur un service cloud, consultez la page [Application .NET multiniveau avec tables, files d'attente et objets blob de stockage][Application .NET multiniveau avec tables, files d'attente et objets blob de stockage].

  [Visual Studio 2013]: /en-us/develop/net/tutorials/get-started/ "Visual Studio 2013"
  [Visual Studio 2012]: /en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
  [version plus récente de ce didacticiel]: /en-us/develop/net/tutorials/get-started/
  [Exemple de site Web]: ./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png
  [activer les avantages de votre abonnement MSDN]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [obtenir une évaluation gratuite]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [Configuration de l’environnement de développement]: #setupdevenv
  [Création d'un site Web dans Azure]: #setupwindowsazure
  [Création d'une application ASP.NET MVC 4]: #createmvc4app
  [Déploiement de l'application dans Azure]: #deploytowindowsazure
  [Étapes suivantes]: #nextsteps
  [install-sdk-2012-only]: ../includes/install-sdk-2012-only.md
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Nouveau site Web]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png
  [Quick create]: ./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png
  [Créer un site Web]: ./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png
  [Page Sites Web du portail de gestion, site Web créé]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png
  [Nouveau projet dans le menu Fichier]: ./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png
  [Boîte de dialogue Nouveau projet]: ./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png
  [Boîte de dialogue Nouveau projet ASP.NET MVC 4]: ./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png
  [Site Web exécuté en local]: ./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png
  [Publier dans le menu contextuel du projet]: ./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png
  [Importer les paramètres de publication]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png
  [Ajouter un abonnement Azure]: ./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png
  [Télécharger le fichier d'abonnement]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png
  [Télécharger le fichier .publishsettings]: ./media/web-sites-dotnet-get-started-vs2012/rzDown2.png
  [Télécharger l'abonnement]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png
  [Importer]: ./media/web-sites-dotnet-get-started-vs2012/rzImp.png
  [Importer le profil de publication]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png
  [Valider la connexion]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png
  [Connexion correctement validée]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png
  [Onglet Paramètres]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png
  [Bouton Démarrer l'aperçu sous l'onglet Aperçu]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png
  [Fichier de résultat de la fonction Démarrer l'aperçu]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png
  [Fenêtre Output affichant la réussite du déploiement]: ./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png
  [Sites Web Azure dans l'Explorateur de serveurs]: ./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png
  [Configuration du site Web dans Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png
  [Onglet Tableau de bord du portail de gestion]: ./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png
  [Gestion de sites Web]: /en-us/manage/services/web-sites/how-to-manage-websites/
  [Onglet Échelle du portail de gestion]: ./media/web-sites-dotnet-get-started-vs2012/MPScale.png
  [Mise à l'échelle d’un site Web]: /en-us/manage/services/web-sites/how-to-scale-websites/
  [Journaux dans Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png
  [Résolution des problèmes de sites Web Azure dans Visual Studio]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure]: /en-us/develop/net/tutorials/web-site-with-sql-database/
  [Modèles d'exécution Azure]: /en-us/develop/net/fundamentals/compute/
  [Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines]: http://azure.microsoft.com/fr-fr/documentation/articles/choose-web-site-cloud-service-vm/
  [Application .NET multiniveau avec tables, files d'attente et objets blob de stockage]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
