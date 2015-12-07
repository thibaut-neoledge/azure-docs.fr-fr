<properties
	pageTitle="Connexion à un serveur SQL Server local à partir d’une application web dans Azure App Service au moyen de connexions hybrides"
	description="Créer une application web sur Microsoft Azure et la connecter à une base de données SQL Server locale"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/13/2015"
	ms.author="cephalin"/>

# Connexion à un serveur SQL Server local à partir d’une application web dans Azure App Service au moyen de connexions hybrides

Les connexions hybrides permettent de connecter des applications web [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) à des ressources locales qui utilisent un port TCP statique. Les ressources prises en charge incluent Microsoft SQL Server, MySQL, les API web HTTP, les services mobiles et la plupart des services web personnalisés.

Dans ce didacticiel, vous allez apprendre à créer une application web App Service dans la [version préliminaire du portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), à la connecter à votre base de données SQL Server locale à l’aide de la nouvelle fonctionnalité de connexion hybride, à créer une application ASP.NET simple qui utilisera la connexion hybride et à déployer l’application sur l’application web App Service. L’application web finalisée sur Azure stocke les informations d’identification des membres dans une base de données locale. Ce didacticiel ne requiert aucune d'expérience préalable dans l'utilisation d'Azure ou ASP.NET.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
>
>La partie Web Apps de la fonctionnalité Connexions hybrides n’est disponible que dans la [version préliminaire du portail Azure](https://portal.azure.com). Pour créer une connexion dans BizTalk Services, consultez la page [Connexions hybrides](http://go.microsoft.com/fwlink/p/?LinkID=397274).

## Configuration requise ##

Pour réaliser ce didacticiel, vous avez besoin des produits suivants. Tous sont disponibles gratuitement, de sorte que vous pouvez commencer à développer gratuitement pour Azure.

- **Abonnement Azure** : pour un abonnement gratuit, consultez la page [Version d'évaluation gratuite d'Azure](/pricing/free-trial/).

- **Visual Studio 2013** - Pour télécharger une version d'évaluation gratuite de Visual Studio 2013, consultez la page [Téléchargements Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Installez ceci avant de poursuivre.

- **Microsoft .NET Framework 3.5 Service Pack 1** : si votre système d'exploitation est Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 ou Windows Server 2008 R2, vous pouvez activer cette mise à jour dans Panneau de configuration > Programmes et fonctionnalités > Activer ou désactiver des fonctionnalités Windows. Sinon, vous pouvez la télécharger depuis le [Centre de téléchargement Microsoft](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).

- **SQL Server 2014 Express with Tools** - Téléchargez gratuitement Microsoft SQL Server Express sur la [page des bases de données Microsoft Web Platform](http://www.microsoft.com/web/platform/database.aspx). Choisissez la version **Express** (pas LocalDB). La version **Express with Tools** inclut SQL Server Management Studio, que vous utiliserez dans ce didacticiel.

- **SQL Server Management Studio Express** : cet outil est inclus dans le téléchargement de SQL Server 2014 Express with Tools mentionné plus haut, mais si vous avez besoin de l'installer séparément, vous pouvez le télécharger et l'installer à partir de la [page de téléchargement de SQL Server Express](http://www.microsoft.com/web/platform/database.aspx).

Ce didacticiel part du principe que vous possédez un abonnement Azure, que vous avez installé Visual Studio 2013 et que vous avez installé ou activé .NET Framework 3.5. Il explique comment installer SQL Server 2014 Express dans une configuration adaptée à la fonctionnalité Connexions hybrides d'Azure (une instance par défaut avec un port IP statique). Avant de commencer ce didacticiel, téléchargez SQL Server 2014 Express with Tools depuis l'emplacement mentionné ci-dessus si vous n'avez pas installé SQL Server.

### Remarques ###
Pour utiliser une base de données SQL Server ou SQL Server Express locale avec une connexion hybride, TCP/IP doit être activé sur un port statique. Les instances par défaut dans SQL Server utilisent le port statique 1433, mais pas les instances nommées.

L'ordinateur sur lequel vous installez l'agent Gestionnaire de connexion hybride local :

- doit disposer d'une connectivité à Azure via :

Port|Pourquoi
---|---
80|**Obligatoire** pour le port HTTP pour la validation du certificat et éventuellement la connectivité des données.
443|**Facultatif** pour la connectivité des données. Si la connectivité sortante à 443 n'est pas disponible, le port TCP 80 est utilisé.
5671 et 9352|**Recommandé** mais facultatif pour la connectivité des données. Notez que ce mode entraîne habituellement un débit plus élevé. Si la connectivité sortante à ces ports n'est pas disponible, le port TCP 443 est utilisé.
- doit être capable d'accéder au *nom\_d'hôte*:*numéro\_de\_port* de votre ressource locale.

Les étapes décrites dans cet article partent du principe que vous utilisez le navigateur à partir de l'ordinateur qui hébergera l'agent de connexion hybride local.

Si vous avez déjà installé SQL Server dans une configuration et dans un environnement qui répondent aux conditions décrites ci-dessus, vous pouvez passer directement à [Création d'une base de données SQL Server en local](#CreateSQLDB).

<a name="InstallSQL"></a>
## A. Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local ##

Cette section explique comment installer SQL Server Express, activer TCP/IP et créer une base de données de telle sorte que votre application web fonctionne avec l'environnement de la version préliminaire du portail Azure.

### Installation de SQL Server Express ###

1. Pour installer SQL Server Express, exécutez le fichier **SQLEXPRWT\_x64\_ENU.exe** ou **SQLEXPR\_x86\_ENU.exe** que vous avez téléchargé. Le Centre d'installation SQL Server s'affiche.

	![SQL Server Install][SQLServerInstall]

2. Choisissez **Nouvelle installation autonome SQL Server ou ajout de fonctionnalités à une installation existante**. Suivez les instructions, en acceptant les options et les paramètres par défaut, jusqu'à l'affichage de la page **Configuration de l'instance**.

3. Sur la page **Configuration de l'instance**, choisissez **Instance par défaut**.

	![Choose Default Instance][ChooseDefaultInstance]

	Par défaut, l'instance par défaut de SQL Server écoute les demandes des clients SQL Server sur le port statique 1433. C'est une exigence de la fonctionnalité Connexions hybrides. Les instances nommées utilisent des ports dynamiques et UDP, qui ne sont pas pris en charge par Connexions hybrides.

4. Acceptez les valeurs par défaut sur la page **Configuration du serveur**.

5. Sur la page **Configuration du moteur de base de données**, sous **Mode d'authentification**, choisissez **Mode mixte (authentification SQL Server et authentification Windows)** et fournissez un mot de passe.

	![Choose Mixed Mode][ChooseMixedMode]

	Dans ce didacticiel, vous allez utiliser l'authentification SQL Server. N'oubliez pas votre mot de passe, car vous en aurez besoin ultérieurement.

6. Parcourez les autres étapes de l'Assistant pour terminer l'installation.

### Activation de TCP/IP ###
Pour activer TCP/IP, vous allez utiliser le Gestionnaire de configuration SQL Server, qui a été installé en même temps que SQL Server Express. Suivez la procédure décrite à la page [Activation du protocole réseau TCP/IP pour SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) avant de continuer.

<a name="CreateSQLDB"></a>
### Création d'une base de données SQL Server locale ###

Votre application Visual Studio nécessite une base de données d'appartenance à laquelle Azure puisse accéder. Il doit s'agir d'une base de données SQL Server ou SQL Server Express (et non de la base de données LocalDB utilisée par défaut par le modèle MVC). Vous allez donc créer ensuite la base de données d'appartenance.

1. Dans SQL Server Management Studio, connectez-vous à l'instance SQL Server que vous venez d'installer. (Si la boîte de dialogue **Se connecter au serveur** ne s’affiche pas automatiquement, accédez à l’**Explorateur d’objets** dans le volet gauche, cliquez sur **Se connecter**, puis sur **Moteur de base de données**.) ![Se connecter au serveur][SSMSConnectToServer]

	Dans la zone **Type de serveur**, choisissez **Moteur de base de données**. Dans la zone **Nom du serveur**, vous pouvez utiliser **localhost** ou le nom de l'ordinateur dont vous vous servez. Choisissez **Authentification SQL Server**, puis ouvrez une session avec le nom d'utilisateur sa et le mot de passe que vous avez créé précédemment.

2. Pour créer une base de données avec SQL Server Management Studio, cliquez avec le bouton droit sur **Bases de données** dans l'Explorateur d'objets, puis cliquez sur **Nouvelle base de données**.

	![Create new database][SSMScreateNewDB]

3. Dans la boîte de dialogue **Nouvelle base de données**, entrez MembershipDB comme nom de base de données, puis cliquez sur **OK**.

	![Provide database name][SSMSprovideDBname]

	Notez qu'à ce stade, vous n'apportez aucune modification à la base de données. Les informations d'appartenance seront ajoutées automatiquement plus tard par votre application web, lorsque vous l'exécuterez.

4. Dans l'Explorateur d'objets, si vous développez **Bases de données**, vous pouvez constater que la base de données d'appartenance a été créée.

	![MembershipDB created][SSMSMembershipDBCreated]

<a name="CreateSite"></a>
## B. Création d’une application web dans la version préliminaire du portail Azure ##

> [AZURE.NOTE]Si vous avez déjà créé dans la version préliminaire du portail Azure une application web que vous voulez utiliser pour ce didacticiel, passez directement à la section [Création d’une connexion hybride et d’un service BizTalk](#CreateHC).

1. Dans la [version préliminaire du portail Azure](https://portal.azure.com), cliquez sur **Nouveau** > **Web et mobilité** > **Application web**.

	![New button][New]

2. Configurez votre application web, puis cliquez sur **Créer**.

	![Website name][WebsiteCreationBlade]

3. Après quelques instants, l’application web est créée et son panneau apparaît. Le panneau est un tableau de bord à défilement vertical, qui vous permet de gérer votre application web.

	![Website running][WebSiteRunningBlade]

	Pour vérifier que l’application est active, cliquez sur l’icône **Parcourir** afin d’afficher la page par défaut.

Vous allez ensuite créer une connexion hybride et un service BizTalk pour l’application web.

<a name="CreateHC"></a>
## C. Création d'une connexion hybride et d'un service BizTalk ##

1. De retour dans le portail, accédez aux paramètres et cliquez sur **Mise en réseau** > **Configurer les points de terminaison de connexion hybride**.

	![Hybrid connections][CreateHCHCIcon]

2. Dans le panneau Connexions hybrides, cliquez sur **Ajouter** > **Nouvelle connexion hybride**.

3. Dans le panneau **Créer une connexion hybride** :
	- Sous **Nom**, entrez un nom pour la connexion.
	- Sous **Nom d'hôte**, entrez le nom de votre ordinateur hôte SQL Server.
	- Sous **Port**, entrez 1433 (le port par défaut pour SQL Server).
	- Cliquez sur **Service BizTalk** > **Nouveau service BizTalk** et entrez un nom pour le service BizTalk.

	![Create a hybrid connection][TwinCreateHCBlades]

5. Cliquez sur **OK** deux fois.

	Lorsque le processus est terminé, la zone **Notifications** zone affiche la mention **SUCCÈS** en vert clignotant et le panneau **Connexion hybride** affiche la nouvelle connexion hybride dans l’état **Non connecté**.

	![One hybrid connection created][CreateHCOneConnectionCreated]

À ce stade, vous avez terminé une partie importante de l'infrastructure de connexion hybride cloud. Vous allez ensuite créer un élément local.

<a name="InstallHCM"></a>
## D. Installation du Gestionnaire de connexions hybrides local pour terminer la connexion ##

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Maintenant que l’infrastructure de connexion hybride est terminée, vous pouvez créer une application web qui l’utilise.

<a name="CreateASPNET"></a>
## E. Création d’un projet web ASP.NET de base, modification de la chaîne de connexion à la base de données et exécution locale du projet ##

### Création d'un projet ASP.NET de base ###
1. À partir du menu **Fichier** de Visual Studio, créez un nouveau projet :

	![New Visual Studio project][HCVSNewProject]

2. Dans la section **Modèles** de la boîte de dialogue **Nouveau projet**, sélectionnez **Web** et choisissez **Application Web ASP.NET**, puis cliquez sur **OK**.

	![Choose ASP.NET Web Application][HCVSChooseASPNET]

3. Dans la boîte de dialogue **Nouveau projet ASP.NET**, choisissez **MVC**, puis cliquez sur **OK**.

	![Choose MVC][HCVSChooseMVC]

4. Lorsque le projet a été créé, la page lisezmoi (readme) de l'application s'affiche. N'exécutez pas le projet web immédiatement.

	![Readme page][HCVSReadmePage]

### Modification de la chaîne de connexion à la base de données pour l'application ###

Au cours de cette étape, vous allez modifier la chaîne de connexion qui indique à votre application où se trouve votre base de données SQL Server Express locale. La chaîne de connexion se trouve dans le fichier Web.config de l'application, qui contient les informations de configuration pour l'application.

> [AZURE.NOTE]Pour vous assurer que votre application utilise la base de données que vous avez créée dans SQL Server Express, et non la base de données LocalDB par défaut de Visual Studio, il est important d'effectuer cette opération avant d'exécuter votre projet.

1. Dans l'Explorateur de solutions, double-cliquez sur le fichier Web.config.

	![Web.config][HCVSChooseWebConfig]

2. Modifiez la section **connectionStrings** pour pointer vers la base de données SQL Server sur votre ordinateur local, en utilisant la syntaxe de l'exemple suivant :

	![Chaîne de connexion][HCVSConnectionString]

	Lors de la composition de la chaîne de connexion, gardez à l'esprit ce qui suit :

	- Si vous vous connectez à une instance nommée plutôt qu'à une instance par défaut (par exemple, VotreServeur\\SQLEXPRESS), vous devez configurer votre instance SQL Server de manière à ce qu'elle utilise des ports statiques. Pour des informations sur la configuration des ports statiques, consultez la page [Configuration de SQL Server pour qu'il écoute sur un port spécifique](http://support.microsoft.com/kb/823938). Par défaut, les instances nommées utilisent UDP et des ports dynamiques, qui ne sont pas pris en charge par Connexions hybrides.

	- Il est recommandé de spécifier le port (1433 par défaut, comme indiqué dans l'exemple) dans la chaîne de connexion pour pouvoir être sûr que TCP est activé sur votre instance SQL Server locale et que cette dernière utilise le port correct.

	- Pensez à utiliser Authentification SQL Server pour la connexion, en indiquant l'ID utilisateur et le mot de passe dans votre chaîne de connexion.

3. Cliquez sur **Enregistrer** dans Visual Studio pour enregistrer le fichier Web.config.

### Exécution du projet localement et inscription d'un nouvel utilisateur ###

1. Exécutez maintenant votre nouveau projet web localement en cliquant sur le bouton Parcourir sous Débogage. Cet exemple utilise Internet Explorer.

	![Run project][HCVSRunProject]

2. En haut à droite de la page web par défaut, choisissez **Inscription** pour enregistrer un nouveau compte :

	![Register a new account][HCVSRegisterLocally]

3. Entrez un nom d'utilisateur et un mot de passe :

	![Enter user name and password][HCVSCreateNewAccount]

	Ceci crée automatiquement sur votre instance SQL Server locale une base de données qui contient les informations d'appartenance pour votre application. L’une des tables (**dbo.AspNetUsers**) contient les informations d’identification des utilisateurs de l’application web, comme celles que vous venez d’entrer. Cette table sera abordée plus loin dans le didacticiel.

4. Fermez la fenêtre de navigateur de la page web par défaut. L'application est alors arrêtée dans Visual Studio.

Vous êtes prêt à passer à l'étape suivante, qui consiste à publier l'application sur Azure et à la tester.

<a name="PubNTest"></a>
## F. Publication de l’application web dans Azure et test de l’application ##

Vous allez maintenant publier votre application dans votre application web App Service, puis la tester pour voir comment la connexion hybride configurée précédemment connecte votre application web à la base de données sur votre ordinateur local.

### Publication de l'application web ###

1. Vous pouvez télécharger votre profil de publication de l’application web App Service dans la version préliminaire du portail Azure. Dans le panneau de votre application web, cliquez sur **Obtenir le profil de publication**, puis enregistrez le fichier sur votre ordinateur.

	![Télécharger le profil de publication][PortalDownloadPublishProfile]

	Vous allez ensuite importer ce fichier dans votre application web Visual Studio.

2. Dans Visual Studio, cliquez avec le bouton droit sur le nom du projet dans l'Explorateur de solutions et sélectionnez **Publier**.

	![Select publish][HCVSRightClickProjectSelectPublish]

3. Dans la boîte de dialogue **Publier le site Web**, sous l'onglet **Profil**, choisissez **Importer**.

	![Importer][HCVSPublishWebDialogImport]

4. Recherchez votre profil de publication téléchargé, sélectionnez-le, puis cliquez sur **OK**.

	![Browse to profile][HCVSBrowseToImportPubProfile]

5. Vos informations de publication sont importées et s'affichent sous l'onglet **Connexion** de la boîte de dialogue.

	![Cliquer sur Publier][HCVSClickPublish]

	Cliquez sur **Publier**.

	Lorsque la publication est terminée, votre navigateur démarre et affiche votre application ASP.NET qui est maintenant active dans le cloud Azure !

Vous allez ensuite utiliser votre application web active pour voir sa connexion hybride en action.

### Test de l'application web terminée sur Azure ###

1. En haut à droite de votre page web sur Azure, choisissez **Ouvrir une session**.

	![Test log in][HCTestLogIn]

2. Votre application web App Service est maintenant connectée à la base de données des membres de votre application web sur votre ordinateur local. Pour le vérifier, ouvrez une session avec les mêmes informations d'identification que celles entrées préalablement dans la base de données locale.

	![Hello greeting][HCTestHelloContoso]

3. Pour tester davantage votre nouvelle connexion hybride, déconnectez-vous de votre application web Azure et inscrivez-vous avec d'autres informations d'identification. Fournissez un nouveau nom d'utilisateur et un nouveau mot de passe, puis cliquez sur **Inscription**.

	![Test register another user][HCTestRegisterRelecloud]

4. Pour vérifier que les informations d'identification du nouvel utilisateur ont été stockées dans votre base de données locale par l'intermédiaire de votre connexion hybride, ouvrez SQL Management Studio sur votre ordinateur local. Dans l'Explorateur d'objets, développez la base de données **MembershipDB**, puis développez **Tables**. Cliquez avec le bouton droit sur la table d'appartenance **dbo.AspNetUsers** et choisissez **Sélectionner les 1000 premières lignes** pour afficher les résultats.

	![View the results][HCTestSSMSTree]

5. Votre table d'appartenance locale indique à présent les deux comptes : celui que vous avez créé localement et celui que vous avez créé dans le cloud Azure. Celui que vous avez créé dans le cloud a été enregistré dans votre base de données locale par le biais de la fonctionnalité Connexions hybrides d'Azure.

	![Registered users in on-premises database][HCTestShowMemberDb]

Vous venez de créer et déployer une application web ASP.NET qui utilise une connexion hybride entre une application web dans le cloud Azure et une base de données SQL Server locale. Félicitations !

## Voir aussi ##
[Aperçu des connexions hybrides](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist présente les connexions hybrides (vidéo Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Aperçu des connexions hybrides](/services/biztalk-services/)

[BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l’échelle, Configurer et Connexion hybride](../biztalk-dashboard-monitor-scale-tabs/)

[Création d’un cloud hybride réel avec la portabilité transparente des applications (vidéo Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Connexion à un serveur SQL Server local à partir d’un service mobile Azure au moyen de connexions hybrides](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Connexion à une instance SQL Server locale à partir d’Azure Mobile Services au moyen de connexions hybrides (vidéo Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Vue d’ensemble d’ASP.NET Identity](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


<!-- IMAGES -->
[SQLServerInstall]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png

<!---HONumber=AcomDC_1125_2015-->