<properties linkid="web-sites-hybrid-connection-getting-started" title="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" pageTitle="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" description="Create a a website on Microsoft Azure and connect it to an on-premises SQL Server database" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm"></tags>

# Connexion à une instance SQL Server locale à partir d'un site web Azure au moyen de connexions hybrides

## Introduction

La fonctionnalité Connexions hybrides permet de connecter des sites web Microsoft Azure à des ressources locales qui utilisent un port statique TCP. Les ressources prises en charge incluent Microsoft SQL Server, MySQL, les API web HTTP, les services mobiles et la plupart des services web personnalisés.

Dans ce didacticiel, vous allez apprendre à créer un site web dans la version préliminaire du portail Azure, à le connecter à votre base de données SQL Server locale avec la nouvelle fonctionnalité Connexions hybrides, à créer une application web ASP.NET simple qui utilisera la connexion hybride et à déployer l'application sur le site web Azure. Le site web terminé sur Azure stocke les informations d'identification dans une base de données d'appartenance locale. Ce didacticiel ne requiert aucune d'expérience préalable dans l'utilisation d'Azure ou ASP.NET.

> [WACOM.NOTE] La partie Sites web de la fonctionnalité Connexions hybrides n'est disponible que dans la [version préliminaire du portail Azure][]. Pour créer une connexion dans BizTalk Services, consultez la page [Connexions hybrides][].

## Configuration requise

Pour réaliser ce didacticiel, vous avez besoin des produits suivants. Tous sont disponibles gratuitement, de sorte que vous pouvez commencer à développer gratuitement pour Azure.

-   **Abonnement Azure** : pour un abonnement gratuit, consultez la page [Version d'évaluation gratuite d'Azure][].

-   **Visual Studio 2013** - Pour télécharger une version d'évaluation gratuite de Visual Studio 2013, consultez la page [Téléchargements Visual Studio][]. Installez ceci avant de poursuivre.

-   **Microsoft .NET Framework 3.5 Service Pack 1** : si votre système d'exploitation est Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 ou Windows Server 2008 R2, vous pouvez activer cette mise à jour dans Panneau de configuration \> Programmes et fonctionnalités \> Activer ou désactiver des fonctionnalités Windows. Sinon, vous pouvez la télécharger depuis le [Centre de téléchargement Microsoft][].

-   **SQL Server 2014 Express with Tools** - Téléchargez gratuitement Microsoft SQL Server Express sur la [page des bases de données Microsoft Web Platform][]. Choisissez la version **Express** (pas LocalDB). La version **Express with Tools** inclut SQL Server Management Studio, que vous utiliserez dans ce didacticiel.

-   **SQL Server Management Studio Express** : cet outil est inclus dans le téléchargement de SQL Server 2014 Express with Tools mentionné plus haut, mais si vous avez besoin de l'installer séparément, vous pouvez le télécharger et l'installer à partir de la [page de téléchargement de SQL Server Express][page des bases de données Microsoft Web Platform].

Ce didacticiel part du principe que vous possédez un abonnement Azure, que vous avez installé Visual Studio 2013 et que vous avez installé ou activé .NET Framework 3.5. Il explique comment installer SQL Server 2014 Express dans une configuration adaptée à la fonctionnalité Connexions hybrides d'Azure (une instance par défaut avec un port IP statique). Avant de commencer ce didacticiel, téléchargez SQL Server 2014 Express with Tools depuis l'emplacement mentionné ci-dessus si vous n'avez pas installé SQL Server.

### Remarques

Pour utiliser une base de données SQL Server ou SQL Server Express locale avec une connexion hybride, TCP/IP doit être activé sur un port statique. Les instances par défaut dans SQL Server utilisent le port statique 1433, mais pas les instances nommées.

L'ordinateur sur lequel vous installez l'agent Gestionnaire de connexion hybride local :

-   doit être capable de se connecter à Azure sur le port 5671 ;
-   doit être capable d'accéder au *nom_d'hôte*:*numéro_de_port* de votre ressource locale.

Les étapes décrites dans cet article partent du principe que vous utilisez le navigateur à partir de l'ordinateur qui hébergera l'agent de connexion hybride local.

Si vous avez déjà installé SQL Server dans une configuration et dans un environnement qui répondent aux conditions décrites ci-dessus, vous pouvez passer directement à [Création d'une base de données SQL Server en local][].

## Dans cet article

[A. Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local][]

[B. Création d'un site web dans la version préliminaire du portail Azure][]

[C. Création d'une connexion hybride et d'un service BizTalk][]

[D. Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion][]

[E. Création d'un projet web ASP.NET de base, modification de la chaîne de connexion à la base de données et exécution locale du projet][]

[F. Publication de l'application web sur Azure et test de l'application][]

<a name="InstallSQL"></a>

## A. Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local

Cette section explique comment installer SQL Server Express, activer TCP/IP et créer une base de données de telle sorte que votre application web fonctionne avec l'environnement de la version préliminaire du portail Azure.

### Installation de SQL Server Express

1.  Pour installer SQL Server Express, exécutez le fichier **SQLEXPRWT\_x64\_ENU.exe** ou **SQLEXPR\_x86\_ENU.exe** que vous avez téléchargé. Le Centre d'installation SQL Server s'affiche.

    ![SQL Server Install][]

2.  Choisissez **Nouvelle installation autonome SQL Server ou ajout de fonctionnalités à une installation existante**. Suivez les instructions, en acceptant les options et les paramètres par défaut, jusqu'à l'affichage de la page **Configuration de l'instance**.

3.  Sur la page **Configuration de l'instance**, choisissez **Instance par défaut**.

    ![Choose Default Instance][]

    Par défaut, l'instance par défaut de SQL Server écoute les demandes des clients SQL Server sur le port statique 1433. C'est une exigence de la fonctionnalité Connexions hybrides. Les instances nommées utilisent des ports dynamiques et UDP, qui ne sont pas pris en charge par Connexions hybrides.

4.  Acceptez les valeurs par défaut sur la page **Configuration du serveur**.

5.  Sur la page **Configuration du moteur de base de données**, sous **Mode d'authentification**, choisissez **Mode mixte (authentification SQL Server et authentification Windows)** et fournissez un mot de passe.

    ![Choose Mixed Mode][]

    Dans ce didacticiel, vous allez utiliser l'authentification SQL Server. N'oubliez pas votre mot de passe, car vous en aurez besoin ultérieurement.

6.  Parcourez les autres étapes de l'Assistant pour terminer l'installation.

### Activation de TCP/IP

Pour activer TCP/IP, vous allez utiliser le Gestionnaire de configuration SQL Server, qui a été installé en même temps que SQL Server Express. Suivez la procédure décrite à la page [Activation du protocole réseau TCP/IP pour SQL Server][] avant de continuer.

<a name="CreateSQLDB"></a>

### Création d'une base de données SQL Server locale

Votre application Visual Studio nécessite une base de données d'appartenance à laquelle Azure puisse accéder. Il doit s'agir d'une base de données SQL Server ou SQL Server Express (et non de la base de données LocalDB utilisée par défaut par le modèle MVC). Vous allez donc créer ensuite la base de données d'appartenance.

1.  Dans SQL Server Management Studio, connectez-vous à l'instance SQL Server que vous venez d'installer. (Si la boîte de dialogue **Connexion au serveur** ne s'affiche pas automatiquement, recherchez l'**Explorateur d'objets** dans le volet de gauche, cliquez sur **Connexion**, puis cliquez sur **Moteur de base de données**.)
    ![Connect to Server][]

    Dans la zone **Type de serveur**, choisissez **Moteur de base de données**. Dans la zone **Nom du serveur**, vous pouvez utiliser **localhost** ou le nom de l'ordinateur dont vous vous servez. Choisissez **Authentification SQL Server**, puis ouvrez une session avec le nom d'utilisateur sa et le mot de passe que vous avez créé précédemment.

2.  Pour créer une base de données avec SQL Server Management Studio, cliquez avec le bouton droit sur **Bases de données** dans l'Explorateur d'objets, puis cliquez sur **Nouvelle base de données**.

    ![Create new database][]

3.  Dans la boîte de dialogue **Nouvelle base de données**, entrez MembershipDB comme nom de base de données, puis cliquez sur **OK**.

    ![Provide database name][]

    Notez qu'à ce stade, vous n'apportez aucune modification à la base de données. Les informations d'appartenance seront ajoutées automatiquement plus tard par votre application web, lorsque vous l'exécuterez.

4.  Dans l'Explorateur d'objets, si vous développez **Bases de données**, vous pouvez constater que la base de données d'appartenance a été créée.

    ![MembershipDB created][]

<a name="CreateSite"></a>

## B. Création d'un site web dans la version préliminaire du portail Azure

> [WACOM.NOTE] Si vous avez déjà créé dans la version préliminaire du portail Azure un site web que vous voulez utiliser pour ce didacticiel, vous pouvez passer directement à la section [Création d'une connexion hybride et d'un service BizTalk][C. Création d'une connexion hybride et d'un service BizTalk].

1.  Dans le coin inférieur gauche de la [version préliminaire du portail Azure][], cliquez sur **Nouveau**, puis choisissez **Site Web**.

    ![New button][]

    ![New website][]

2.  Dans le volet **Site Web**, fournissez un nom pour votre site web, puis cliquez sur **Créer**.

    ![Website name][]

3.  Après quelques instants, le site web est créé et son volet apparaît. Le volet est un tableau de bord vertical qu'il est possible de faire défiler et qui vous permet de gérer votre site.

    ![Website running][]

4.  Pour vérifier que le site est actif, cliquez sur l'icône **Parcourir** afin d'afficher la page par défaut.

    ![Click browse to see your website][]

    ![Default website page][]

Vous allez ensuite créer une connexion hybride et un service BizTalk pour le site web.

<a name="CreateHC"></a>

## C. Création d'une connexion hybride et d'un service BizTalk

1.  Une fois de retour dans la version préliminaire du portail, faites défiler jusqu'au volet correspondant à votre site web et cliquez sur **Connexions hybrides**.

    ![Hybrid connections][]

2.  Dans le volet Connexions hybrides, cliquez sur **Ajouter**.

    ![Add a hybrid connection][]

3.  Le volet **Ajouter une connexion hybride** s'ouvre. Comme il s'agit de votre première connexion hybride, l'option **Nouvelle connexion hybride** est présélectionnée, et le volet **Créer une connexion hybride** s'ouvre.

    ![Create a hybrid connection][]

    Dans le volet **Créer une connexion hybride** :

    -   Sous **Nom**, entrez un nom pour la connexion.
    -   Sous **Nom d'hôte**, entrez le nom de votre ordinateur hôte SQL Server.
    -   Sous **Port**, entrez 1433 (le port par défaut pour SQL Server).
    -   Cliquez sur **Service BizTalk**.

4.  Le volet **Créer un service BizTalk** s'ouvre. Entrez un nom pour le service BizTalk, puis cliquez sur **OK**.

    ![Create BizTalk service][]

    Le volet **Créer un service BizTalk** se ferme et vous revenez au volet **Créer une connexion hybride**.

5.  Dans le volet Créer une connexion hybride, cliquez sur **OK**.

    ![Click OK][]

6.  Lorsque le processus est terminé, la zone des notifications du portail vous informe que la connexion a été créée.

    ![Success notification][]

7.  Dans le volet du site web, l'icône **Connexions hybrides** indique à présent que 1 connexion hybride a été créée.

    ![One hybrid connection created][]

À ce stade, vous avez terminé une partie importante de l'infrastructure de connexion hybride cloud. Vous allez ensuite créer un élément local.

<a name="InstallHCM"></a>

## D. Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion

1.  Dans le volet du site web, cliquez sur l'icône Connexions hybrides.

    ![Hybrid connections icon][]

2.  Dans le volet **Connexions hybrides**, la colonne **Statut** correspondant au point de terminaison ajouté récemment indique **Non connecté**. Cliquez sur la connexion pour la configurer.

    ![Not connected][]

    Le volet Connexion hybride s'ouvre.

    ![NotConnectedBlade][]

3.  Dans le volet, cliquez sur **Configuration de l'écouteur**.

    ![Click Listener Setup][]

4.  Le volet **Propriétés de la connexion hybride** s'ouvre. Sous **Gestionnaire de connexions hybrides local**, cliquez sur **Cliquez ici pour l'installer**.

    ![Click here to install][]

5.  Dans la boîte de dialogue Exécution de l'application - Avertissement de sécurité, cliquez sur **Exécuter** pour continuer.

    ![Choose Run to continue][]

6.  Dans la boîte de dialogue **Contrôle de compte d'utilisateur**, cliquez sur **Oui**.

    ![Choose Yes][]

7.  Le Gestionnaire de connexion hybride est téléchargé et installé pour vous.

    ![Installation][]

8.  Une fois l'installation terminée, cliquez sur **Fermer**.

    ![Click Close][]

    Dans le volet **Connexions hybrides**, la colonne **Statut** indique à présent **Connecté**.

    ![Connected Status][]

Maintenant que l'infrastructure de connexion hybride est terminée, vous pouvez créer une application web qui l'utilise.

<a name="CreateASPNET"></a>

## E. Création d'un projet web ASP.NET de base, modification de la chaîne de connexion à la base de données et exécution locale du projet

### Création d'un projet ASP.NET de base

1.  À partir du menu **Fichier** de Visual Studio, créez un nouveau projet :

    ![New Visual Studio project][]

2.  Dans la section **Modèles** de la boîte de dialogue **Nouveau projet**, sélectionnez **Web** et choisissez **Application Web ASP.NET**, puis cliquez sur **OK**.

    ![Choose ASP.NET Web Application][]

3.  Dans la boîte de dialogue **Nouveau projet ASP.NET**, choisissez **MVC**, puis cliquez sur **OK**.

    ![Choose MVC][]

4.  Lorsque le projet a été créé, la page lisezmoi (readme) de l'application s'affiche. N'exécutez pas le projet web immédiatement.

    ![Readme page][]

### Modification de la chaîne de connexion à la base de données pour l'application

Au cours de cette étape, vous allez modifier la chaîne de connexion qui indique à votre application où se trouve votre base de données SQL Server Express locale. La chaîne de connexion se trouve dans le fichier Web.config de l'application, qui contient les informations de configuration pour l'application.

> [WACOM.NOTE] Pour s'assurer que votre application utilise la base de données que vous avez créée dans SQL Server Express, et non la base de données LocalDB par défaut de Visual Studio, il est important d'effectuer cette opération avant d'exécuter votre projet.

1.  Dans l'Explorateur de solutions, double-cliquez sur le fichier Web.config.

    ![Web.config][]

2.  Modifiez la section **connectionStrings** pour pointer vers la base de données SQL Server sur votre ordinateur local, en utilisant la syntaxe de l'exemple suivant :

    ![Chaîne de connexion][]

    Lors de la composition de la chaîne de connexion, gardez à l'esprit ce qui suit :

    -   Si vous vous connectez à une instance nommée plutôt qu'à une instance par défaut (par exemple, VotreServeur\SQLEXPRESS), vous devez configurer votre instance SQL Server de manière à ce qu'elle utilise des ports statiques. Pour des informations sur la configuration des ports statiques, consultez la page [Configuration de SQL Server pour qu'il écoute sur un port spécifique][]. Par défaut, les instances nommées utilisent UDP et des ports dynamiques, qui ne sont pas pris en charge par Connexions hybrides.

    -   Il est recommandé de spécifier le port (1433 par défaut, comme indiqué dans l'exemple) dans la chaîne de connexion pour pouvoir être sûr que TCP est activé sur votre instance SQL Server locale et que cette dernière utilise le port correct.

    -   Pensez à utiliser Authentification SQL Server pour la connexion, en indiquant l'ID utilisateur et le mot de passe dans votre chaîne de connexion.

3.  Cliquez sur **Enregistrer** dans Visual Studio pour enregistrer le fichier Web.config.

### Exécution du projet localement et inscription d'un nouvel utilisateur

1.  Exécutez maintenant votre nouveau projet web localement en cliquant sur le bouton Parcourir sous Débogage. Cet exemple utilise Internet Explorer.

    ![Run project][]

2.  En haut à droite de la page web par défaut, choisissez **Inscription** pour enregistrer un nouveau compte :

    ![Register a new account][]

3.  Entrez un nom d'utilisateur et un mot de passe :

    ![Enter user name and password][]

    Ceci crée automatiquement sur votre instance SQL Server locale une base de données qui contient les informations d'appartenance pour votre application. L'une des tables (**dbo.AspNetUsers**) contient les informations d'identification des utilisateurs du site web, comme celles que vous venez d'entrer. Cette table sera abordée plus loin dans le didacticiel.

4.  Fermez la fenêtre de navigateur de la page web par défaut. L'application est alors arrêtée dans Visual Studio.

Vous êtes prêt à passer à l'étape suivante, qui consiste à publier l'application sur Azure et à la tester.

<a name="PubNTest"></a>

## F. Publication de l'application web sur Azure et test de l'application

Vous allez maintenant publier votre application sur votre site web sur Azure, puis la tester pour voir comment la connexion hybride que vous avez configurée précédemment est utilisée pour connecter votre application de site web à la base de données sur votre ordinateur local.

### Publication de l'application web

1.  Vous pouvez télécharger votre profil de publication pour le site web dans le portail Azure. Dans le volet de votre site web, choisissez **Télécharger le profil de publication**, puis enregistrez le fichier sur votre ordinateur.

    ![Télécharger le profil de publication][]

    ![Publish profile in downloads folder][]

    Vous allez ensuite importer ce fichier dans votre application web Visual Studio.

2.  Dans Visual Studio, cliquez avec le bouton droit sur le nom du projet dans l'Explorateur de solutions et sélectionnez **Publier**.

    ![Select publish][]

3.  Dans la boîte de dialogue **Publier le site Web**, sous l'onglet **Profil**, choisissez **Importer**.

    ![Importer][]

4.  Recherchez votre profil de publication téléchargé, sélectionnez-le, puis cliquez sur **OK**.

    ![Browse to profile][]

5.  Vos informations de publication sont importées et s'affichent sous l'onglet **Connexion** de la boîte de dialogue.

    ![Cliquer sur Publier][]

    Cliquez sur **Publier**.

    Lorsque la publication est terminée, votre navigateur est lancé et affiche votre site web, qui est maintenant actif dans le cloud Azure !

Vous allez ensuite utiliser votre application web active pour voir sa connexion hybride en action.

### Test de l'application web terminée sur Azure

1.  En haut à droite de votre page web sur Azure, choisissez **Ouvrir une session**.

    ![Test log in][]

2.  Votre site web Azure est maintenant connecté à la base de données des membres de votre application web sur votre ordinateur local. Pour le vérifier, ouvrez une session avec les mêmes informations d'identification que celles entrées préalablement dans la base de données locale.

    ![Hello greeting][]

3.  Pour tester davantage votre nouvelle connexion hybride, déconnectez-vous de votre application web Azure et inscrivez-vous avec d'autres informations d'identification. Fournissez un nouveau nom d'utilisateur et un nouveau mot de passe, puis cliquez sur **Inscription**.

    ![Test register another user][]

4.  Pour vérifier que les informations d'identification du nouvel utilisateur ont été stockées dans votre base de données locale par l'intermédiaire de votre connexion hybride, ouvrez SQL Management Studio sur votre ordinateur local. Dans l'Explorateur d'objets, développez la base de données **MembershipDB**, puis développez **Tables**. Cliquez avec le bouton droit sur la table d'appartenance **dbo.AspNetUsers** et choisissez **Sélectionner les 1000 premières lignes** pour afficher les résultats.

    ![View the results][]

5.  Votre table d'appartenance locale indique à présent les deux comptes : celui que vous avez créé localement et celui que vous avez créé dans le cloud Azure. Celui que vous avez créé dans le cloud a été enregistré dans votre base de données locale par le biais de la fonctionnalité Connexions hybrides d'Azure.

    ![Registered users in on-premises database][]

Vous avez maintenant créé et déployé une application web ASP.NET qui utilise une connexion hybride entre un site web dans le cloud Azure et une base de données SQL Server locale. Félicitations !

## Voir aussi

[Aperçu des connexions hybrides][Connexions hybrides]

[Josh Twist présente les connexions hybrides (vidéo Channel 9)][]

[Site web des connexions hybrides][]

[BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l'échelle, Configurer et Connexion hybride][]

[Création d'un cloud hybride réel avec la portabilité transparente des applications (vidéo Channel 9)][]

[Connexion à un serveur SQL Server local à partir d'un service mobile Azure au moyen de connexions hybrides][]

[Connexion à une instance SQL Server locale à partir d'Azure Mobile Services au moyen de connexions hybrides (vidéo Channel 9)][]

[Vue d'ensemble d'ASP.NET Identity][]

<!-- IMAGES -->

  [version préliminaire du portail Azure]: https://portal.azure.com
  [Connexions hybrides]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Téléchargements Visual Studio]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [Centre de téléchargement Microsoft]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22
  [page des bases de données Microsoft Web Platform]: http://www.microsoft.com/web/platform/database.aspx
  [Création d'une base de données SQL Server en local]: #CreateSQLDB
  [A. Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local]: #InstallSQL
  [B. Création d'un site web dans la version préliminaire du portail Azure]: #CreateSite
  [C. Création d'une connexion hybride et d'un service BizTalk]: #CreateHC
  [D. Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion]: #InstallHCM
  [E. Création d'un projet web ASP.NET de base, modification de la chaîne de connexion à la base de données et exécution locale du projet]: #CreateASPNET
  [F. Publication de l'application web sur Azure et test de l'application]: #PubNTest
  [SQL Server Install]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
  [Choose Default Instance]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
  [Choose Mixed Mode]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
  [Activation du protocole réseau TCP/IP pour SQL Server]: http://technet.microsoft.com/en-us/library/hh231672%28v=sql.110%29.aspx
  [Connect to Server]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
  [Create new database]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
  [Provide database name]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
  [MembershipDB created]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
  [New button]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
  [New website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
  [Website name]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
  [Website running]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
  [Click browse to see your website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
  [Default website page]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
  [Hybrid connections]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
  [Add a hybrid connection]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
  [Create a hybrid connection]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
  [Create BizTalk service]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
  [Click OK]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
  [Success notification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
  [One hybrid connection created]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
  [Hybrid connections icon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
  [Not connected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
  [Click Listener Setup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
  [Click here to install]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
  [Choose Run to continue]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
  [Choose Yes]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
  [Installation]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
  [Click Close]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
  [Connected Status]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
  [New Visual Studio project]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
  [Choose ASP.NET Web Application]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
  [Choose MVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
  [Readme page]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
  [Web.config]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
  [Chaîne de connexion]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
  [Configuration de SQL Server pour qu'il écoute sur un port spécifique]: http://support.microsoft.com/kb/823938
  [Run project]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
  [Register a new account]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
  [Enter user name and password]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
  [Télécharger le profil de publication]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
  [Publish profile in downloads folder]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
  [Select publish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
  [Importer]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
  [Browse to profile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
  [Cliquer sur Publier]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
  [Test log in]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
  [Hello greeting]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
  [Test register another user]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
  [View the results]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
  [Registered users in on-premises database]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
  [Josh Twist présente les connexions hybrides (vidéo Channel 9)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [Site web des connexions hybrides]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l'échelle, Configurer et Connexion hybride]: http://azure.microsoft.com/fr-fr/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Création d'un cloud hybride réel avec la portabilité transparente des applications (vidéo Channel 9)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [Connexion à un serveur SQL Server local à partir d'un service mobile Azure au moyen de connexions hybrides]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Connexion à une instance SQL Server locale à partir d'Azure Mobile Services au moyen de connexions hybrides (vidéo Channel 9)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
  [Vue d'ensemble d'ASP.NET Identity]: http://www.asp.net/identity
