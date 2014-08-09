<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title=" OAuth" authors="riande" solutions="" manager="wpickett" editor="mollybos" />

Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure
===================================================================================================================================================

***Par [Rick Anderson](https://twitter.com/RickAndMSFT) et Tom Dykstra. Mis à jour le 15 octobre 2013.***
[Visual Studio 2013](/en-us/develop/net/tutorials/web-site-with-sql-database/ "Visual Studio 2013")[Visual Studio 2012](/en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/ "Visual Studio 2012")

**Remarque**

Une [version plus récente de ce didacticiel](/en-us/develop/net/tutorials/web-site-with-sql-database/) est disponible. La présente version fait appel à Visual Studio 2012, mais la version plus récente est véritablement plus facile à suivre.

Ce didacticiel vous montre comment générer une application Web ASP.NET MVC 4 sécurisée qui permet aux utilisateurs de se connecter avec des informations d'identification Facebook, Yahoo ou Google. Vous allez également déployer l’application sur Azure.

Vous pouvez ouvrir gratuitement un compte Azure. Si vous n'avez pas déjà Visual Studio 2012, le Kit de développement logiciel (SDK) installe automatiquement Visual Studio Express 2012 pour le Web. Vous pouvez commencer vos développement Azure gratuitement.

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de ce didacticiel, vous disposerez d’une application Web pilotée par les données sécurisée et fonctionnelle dans le cloud et utilisant une base de données du cloud.

Vous apprendrez à effectuer les opérations suivantes :

-   activer votre ordinateur pour le développement Azure en installant le Kit de développement logiciel (SDK) Azure ;
-   créer un projet ASP.NET MVC 4 sécurisé et le publier vers un site Web Azure ;
-   utiliser OAuth et la base de données des membres ASP.NET pour sécuriser votre application ;
-   déployer une base de données des membres dans Azure ;
-   utiliser une base de données SQL pour stocker des données dans Azure ;
-   utiliser Visual Studio pour mettre à jour et gérer la base de données des membres.

Vous développerez une application Web de liste de contacts simple basée sur ASP.NET MVC 4 et utilisant Entity Framework ADO.NET pour accéder à la base de données. L’illustration suivante montre la page de connexion une fois l’application terminée :

![page de connexion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Dans ce didacticiel :

-   [Configuration de l'environnement de développement](#bkmk_setupdevenv)
-   [Configuration de l'environnement Azure](#bkmk_setupwindowsazure)
-   [Création d'une application ASP.NET MVC 4](#bkmk_createmvc4app)
-   [Déploiement de l'application dans Azure](#bkmk_deploytowindowsazure1)
-   [Ajout d'une base de données à l'application](#bkmk_addadatabase)
-   [Ajout d'un fournisseur OAuth](#addOauth)
-   [Ajout de rôles à la base de données des membres](#mbrDB)
-   [Création d'un script de déploiement des données](#ppd)
-   [Déploiement de l’application dans Azure](#bkmk_deploytowindowsazure11)
-   [Mise à jour de la base de données des membres](#ppd2)
-   [Étapes suivantes](#nextsteps)

Configuration de l'environnement de développement
-------------------------------------------------

Pour commencer, configurez l’environnement de développement en installant le Kit de développement logiciel (SDK) Azure pour .NET Framework.

1.  Pour installer le Kit de développement logiciel (SDK) Azure pour .NET, cliquez sur le lien ci-dessous. Si Visual Studio 2012 n'est pas encore installé, il le sera par l'intermédiaire de ce lien. Ce didacticiel nécessite Visual Studio 2012. [Kit de développement logiciel (SDK) Azure pour Visual Studio 2012](http://go.microsoft.com/fwlink/?LinkId=254364)
2.  Lorsque vous êtes invité à exécuter ou enregistrer l’exécutable d’installation, cliquez sur **Exécuter**.
3.  Dans la fenêtre Web Platform Installer, cliquez sur **Installer**, puis poursuivez l’installation.

![Web Platform Installer - Kit de développement logiciel (SDK) Azure pour .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png)

Une fois l’installation terminée, vous disposez de tous les éléments nécessaires pour commencer le développement.

Configuration de l'environnement Azure
--------------------------------------

Configurez ensuite l'environnement Azure en créant un site Web Azure et une base de données SQL.

### Création d'un site Web et d'une base de données SQL dans Azure

Votre site Web Azure est exécuté dans un environnement d'hébergement partagé, ce qui veut dire qu'il s'exécute sur des machines virtuelles partagées avec d'autres clients Azure. Un environnement d'hébergement partagé permet de commencer à utiliser le cloud à moindre frais. Plus tard, si votre trafic Web augmente, vous pourrez mettre votre application à l'échelle pour répondre à vos besoins, en exécutant des machines virtuelles dédiées. Si vous avez besoin d'une architecture plus complexe, vous pouvez migrer vers un service cloud Azure. Les services cloud sont exécutés sur des machines virtuelles dédiées pour vous permettre de les configurer selon vos besoins.

La base de données SQL Azure est un service de bases de données relationnelles sur le cloud, basé sur les technologies SQL Server. Les outils et applications fonctionnant avec SQL Server fonctionnent également avec la base de données SQL.

1.  Dans le [portail de gestion Azure](https://manage.windowsazure.com), dans l'onglet gauche, cliquez sur **Web Sites**, puis sur **New**.

    ![Bouton Nouveau du portail de gestion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxWSnew2.png)

1.  Cliquez sur **CUSTOM CREATE**.

    ![Lien Create with Database dans le portail de gestion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB.png)

L'Assistant **New Web Site - Custom Create** s'ouvre.

1.  Lors de l'étape **New Web Site** de l'Assistant, entrez une chaîne dans la zone **URL** pour l'utiliser en tant qu'URL unique pour votre application. L’URL complète se composera du texte entré dans cette zone, ainsi que du suffixe affiché en regard de la zone. L'illustration indique « contactmgr2 », mais cette URL est probablement prise (il faut donc en choisir une autre).

    ![Lien Create with Database dans le portail de gestion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png)

2.  Dans la liste déroulante **Base de données**, sélectionnez **Create a free SQL database**.

3.  Dans la liste déroulante **Region**, sélectionnez la même région que celle de votre site Web. Ce paramètre indique le centre de données dans lequel s'exécutera votre machine virtuelle. Dans **Nom de la chaîne de connexion à la base de données**, entrez *connectionString1*.

    ![Étape Créer un nouveau site Web du l'Assistant Créer un site Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png)

4.  Cliquez sur les flèches pointant vers la droite en bas de la zone. L'Assistant passe à l'étape **Paramètres de la base de données**.

5.  Dans la zone **Name**, entrez *ContactDB*

6.  Dans la zone **Server**, sélectionnez **New SQL Database server**. Par ailleurs, si vous avez déjà créé une base de données SQL Server, vous pouvez la sélectionner dans la liste déroulante.

7.  Entrez un **NOM DE CONNEXION** et un **MOT DE PASSE** d'administrateur. Si vous avez sélectionné **New SQL Database server**, vous ne devez pas entrer un nom et un mot de passe existant ici, mais une nouvelle paire nom/mot de passe que vous allez choisir maintenant et utiliser ultérieurement lorsque vous accèderez à la base de données. Si vous avez sélectionné une base de données SQL Server créée auparavant, vous devez entrer le mot de passe et le nom du compte SQL Server que vous aviez créé. Pour ce didacticiel, ne cochez pas la case **Advanced **. Cette case vous permet de définir la taille de la base de données (la valeur par défaut est 1 Go, mais vous pouvez l'augmenter jusqu'à 150 Go) et son classement.

8.  Cliquez sur la coche située en bas de la zone pour indiquer que vous avez terminé.

    ![Paramètres de base de données du nouveau site Web : Assistant Création avec une base de données](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-004.png)

    L'image suivante montre l'utilisation d'un nom et d'un mot de passe de connexion à une base de données SQL existante. ![Paramètres de base de données du nouveau site Web : Assistant Création avec une base de données](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxPrevDB.png)

    Le portail de gestion renvoie vers la page Sites Web, tandis que la colonne **Status** indique que le site est en cours de création. Après un temps d'attente (généralement inférieur à une minute), la colonne **Status** indique que la création du site est réussie. Dans la barre de navigation de gauche, le nombre de sites de votre compte apparaît en regard de l'icône **Web Sites**, tandis que le nombre de bases de données apparaît en regard de l'icône **SQL Databases**.

Création d'une application ASP.NET MVC 4
----------------------------------------

Vous avez créé un site Web Azure, mais il est encore vide. La prochaine étape consiste à créer l'application Web Visual Studio que vous allez publier sur Azure.

### Création du projet

1.  Démarrez Visual Studio 2012.
2.  Dans le menu **File**, cliquez sur **New Project**.
3.  Dans la boîte de dialogue **Nouveau projet**, développez **Visual C\#** et sélectionnez **Web** sous **Modèles installés**. Sélectionnez ensuite **Application Web ASP.NET MVC 4**. Conservez la valeur par défaut **.NET Framework 4.5**. Nommez l'application **GestionnaireContacts**, puis cliquez sur **OK**.

    ![Boîte de dialogue Nouveau projet](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-002.png)

4.  Dans la boîte de dialogue **New ASP.NET Project**, sélectionnez le modèle **Internet Application**. Ne modifiez pas la valeur par défaut dans **Moteur de vue** Razor, puis cliquez sur **OK**.

    ![Boîte de dialogue Nouveau projet ASP.NET MVC 4](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb2.png)

### Définition de l'en-tête et du pied de page de la page

1.  Dans l'**Explorateur de solutions**, développez le dossier Views\\Shared et ouvrez le fichier *\_Layout.cshtml*.

    ![\_Layout.cshtml dans l'Explorateur de solutions](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-004.png)

2.  Remplacez chaque occurrence de « Mon application ASP.NET MVC » par « Gestionnaire de contacts ».
3.  Remplacez « votre logo ici » par « Démo de GC ».

### Exécution locale de l'application

1.  Appuyez sur Ctrl+F5 pour exécuter l'application. La page d'accueil de l'application apparaît dans le navigateur par défaut. ![Page d'accueil Liste des tâches](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxa.png)

Voilà, vous avez fait tout ce qu'il fallait pour créer l'application que vous allez déployer dans Azure. Après cela, vous allez ajouter les fonctionnalités de base de données.

Déploiement de l'application dans Azure
---------------------------------------

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure](http://manage.windowsazure.com "portail").

2.  Sous l'onglet **Sites Web**, cliquez sur le nom du site créé plus tôt.

    ![Application de gestionnaire de contacts dans l'onglet Sites Web du portail de gestion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-006.png)

3.  À droite de la fenêtre, cliquez sur **Télécharger le profil de publication**.

    ![Onglet de démarrage rapide et bouton Télécharger le profil de publication](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-download-profile.png)

    Cette étape permet de télécharger un fichier contenant l'ensemble des paramètres dont vous avez besoin pour déployer une application vers votre site Web. Comme vous allez importer ce fichier dans Visual Studio, il n'est pas nécessaire d'entrer les informations manuellement.

4.  Enregistrez le fichier *.publishsettings* dans un dossier auquel vous pouvez accéder dans Visual Studio.

    ![enregistrer le fichier .publishsettings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-save-profile.png)

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]

5.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.

    ![Publier dans le menu contextuel du projet](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/PublishVSSolution.png)

    L'Assistant **Publier le site Web** s'ouvre.

6.  Sous l'onglet **Profil** de l'Assistant **Publier le site Web**, cliquez sur **Importer**.

    ![Importer les paramètres de publication](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishSettings.png)

    La boîte de dialogue **Importer le profil de publication** apparaît.

7.  Si vous n'avez pas encore ajouté votre abonnement Azure dans Visual Studio, procédez comme suit. Au cours de ces étapes, vous allez ajouter votre abonnement de telle sorte que votre site Web figure dans la liste déroulante située sous **Import from an Azure web site**.

    a. Dans la boîte de dialogue **Importer le profil de publication**, cliquez sur **Ajouter un abonnement Azure**.

    ![ajouter un abonnement azure](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzAddWAsub.png)

    b. Dans la boîte de dialogue **Import Azure Subscriptions**, cliquez sur **Télécharger le fichier d'abonnement**.

    ![Télécharger l'abonnement](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)

    c. Dans la fenêtre de votre navigateur, enregistrez le fichier *.publishsettings*.

    ![télécharger un fichier de publication](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDown2.png)

    > [WACOM.NOTE] Le fichier .publishsettings contient les informations d'identification (non codées) servant à gérer vos abonnements et services Azure. Pour des raisons de sécurité, il est recommandé de stocker ce fichier temporairement en dehors de vos répertoires sources (par exemple, dans le dossier Bibliothèques\\Documents), puis de le supprimer une fois l'importation terminée. Si un utilisateur malveillant accède au fichier .publishsettings, il peut modifier, créer et supprimer vos services Azure.

    d. Dans la boîte de dialogue **Import Azure Subscriptions**, cliquez sur **Parcourir** et accédez au fichier *.publishsettings*.

    ![Télécharger l'abonnement](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)

    e. Cliquez sur **Importer**.

    ![Importer](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzImp.png)

8.  Dans la boîte de dialogue **Importer le profil de publication**, sélectionnez **Import from an Azure web site**, sélectionnez votre site Web dans la liste déroulante, puis cliquez sur **OK**.

    ![Importer le profil de publication](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishProfile.png)

    L'application créée est maintenant exécutée dans le cloud. Au prochain déploiement de l’application, seuls les fichiers modifiés (ou nouveaux) seront déployés.

    ![Page d'accueil Liste des tâches exécutée dans Azure](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/newapp005.png)

Ajout d'une base de données à l'application
-------------------------------------------

À présent, vous allez mettre à jour l'application MVC pour y ajouter la capacité d'afficher et de mettre à jour les contacts, puis stocker les données dans une base de données. L'application va utiliser Entity Framework pour créer la base de données, ainsi que lire et mettre à jour les données associées.

### Ajout de classes de modèle de données pour les contacts

Commencez par créer un modèle de données simple dans le code.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier Modèles, cliquez sur **Ajouter**, puis sur **Classe**.

    ![Menu contextuel Ajouter une classe aux modèles](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-001.png)

1.  Dans la boîte de dialogue **Ajouter un nouvel élément**, nommez le nouveau fichier de classe *Contact.cs*, puis cliquez sur **Ajouter**.

    ![Boîte de dialogue Ajouter un nouvel élément](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-002.png)

1.  Remplacez le contenu du fichier Contacts.cs par le code suivant.

         using System.ComponentModel.DataAnnotations;
         using System.Globalization;
         namespace ContactManager.Models
         {
             public class Contact
             {
                 public int ContactId { get; set; }
                 public string Name { get; set; }
                 public string Address { get; set; }
                 public string City { get; set; }
                 public string State { get; set; }
                 public string Zip { get; set; }
                 [DataType(DataType.EmailAddress)]
                 public string Email { get; set; }
             }
         }

La classe **Contacts** définit les données que vous allez stocker pour chaque contact, ainsi que la clé primaire *ContactID* requise par la base de données.

### Création de pages Web permettant aux utilisateurs de l'application d'utiliser des contacts

La fonctionnalité de génération de modèles automatique ASP.NET MVC peut générer automatiquement un code qui effectue les actions CRUD (Create, Read, Update et Delete, ou Créer, Lire, Mettre à jour et Supprimer).

Ajout d'un contrôleur et affichage des données
----------------------------------------------

1.  Développez le projet **(Ctrl+Maj+B)** (vous devez développer le projet avant d'utiliser le mécanisme de génération de modèles automatique).
2.  Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier Contrôleurs, puis cliquez sur **Ajouter** et sur **Contrôleur**.

    ![Ajouter un contrôleur dans le menu contextuel du dossier Contrôleurs](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-context-menu.png)

3.  Dans la boîte de dialogue **Ajouter un contrôleur**, nommez votre contrôleur « HomeController ».
4.  Définissez le modèle **Options de vue de structure** sur **Contrôleur MVC avec actions et vues en lecture/écriture, qui utilise Entity Framework**.
5.  Sélectionnez la classe de modèle **Contact** et la classe de contexte de données **&lt; Nouveau contexte de données...\>** .

    ![Boîte de dialogue Ajouter un contrôleur](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-controller-dialog.png)

6.  Dans la boîte de dialogue **Nouveau contexte de données**, acceptez la valeur par défaut *ContactManager.Models.ContactManagerContext*.
    ![Boîte de dialogue Ajouter un contrôleur](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png)

7.  Cliquez sur **OK**, puis sur **Ajouter** dans la boîte de dialogue **Ajouter un contrôleur**.
8.  Dans la boîte de dialogue **Ajouter un contrôleur**, vérifiez que toutes les options sont activées, puis cliquez sur **OK**.

    ![Boîte de dialogue Ajouter un contrôleur](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxOverwrite.png)

Visual Studio crée des méthodes de contrôleur et des vues pour les opérations de base de données CRUD des objets **Contact**.

Activation des migrations, création de la base de données, ajout d'exemples de données et d'un initialiseur de données
----------------------------------------------------------------------------------------------------------------------

L'étape suivante consiste à activer la fonctionnalité [Migrations Code First](http://msdn.microsoft.com/library/hh770484.aspx) pour créer la base de données en fonction du modèle de données que vous avez créé.

1.  Dans le menu **Outils**, sélectionnez **Gestionnaire de package de bibliothèques**, puis **Console du Gestionnaire de package**.

    ![Console du Gestionnaire de package dans le menu Outils](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-menu.png)

2.  Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :

         enable-migrations -ContextTypeName ContactManagerContext

    ![enable-migrations](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxE.png) 
	Vous devez spécifier le nom de type de contexte (**ContactManagerContext**), car le projet contient deux classes dérivées [DbContext](http://msdn.microsoft.com/fr-fr/library/system.data.entity.dbcontext(v=VS.103).aspx) nommées **ContactManagerContext** (que nous venons d'ajouter) et **UsersContext** (qui est utilisée pour la base de données des membres). La classe **ContactManagerContext** a été ajoutée par l'Assistant Génération de modèles automatique de Visual Studio.

    La commande **enable-migrations** crée un dossier *Migrations* dans lequel elle place un fichier *Configuration.cs* que vous pouvez modifier pour configurer les migrations.

3.  Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :

         add-migration Initial

    La commande **add-migration Initial** génère un fichier nommé **&lt;date\_stamp\>Initial** dans le dossier *Migrations* qui crée la base de données. Le premier paramètre (**Initial**) est arbitraire et permet de créer le nom du fichier. Les nouveaux fichiers de classe sont affichés dans l'**Explorateur de solutions**.

    Dans la classe **Initial**, la méthode **Up** crée la table des contacts et la méthode **Down** (utilisée lorsque vous voulez revenir à l'état précédent) annule cette table.

4.  Ouvrez le fichier *Migrations\\Configuration.cs*.
5.  Ajoutez les espaces de noms suivants.

    using ContactManager.Models;

6.  Remplacez la méthode *Seed* par le code suivant :

         protected override void Seed(ContactManager.Models.ContactManagerContext context)
         {
             context.Contacts.AddOrUpdate(p => p.Name,
                new Contact
                {
                    Name = "Debra Garcia",
                    Address = "1234 Main St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "debra@example.com",
                },
                 new Contact
                 {
                     Name = "Thorsten Weinrich",
                     Address = "5678 1st Ave W",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "thorsten@example.com",
                 },
                 new Contact
                 {
                     Name = "Yuhong Li",
                     Address = "9012 State st",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "yuhong@example.com",
                 },
                 new Contact
                 {
                     Name = "Jon Orton",
                     Address = "3456 Maple St",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "jon@example.com",
                 },
                 new Contact
                 {
                     Name = "Diliana Alexieva-Bosseva",
                     Address = "7890 2nd Ave E",
                     City = "Redmond",
                     State = "WA",
                     Zip = "10999",
                     Email = "diliana@example.com",
                 }
                 );
         }

    Le code ci-dessus initialise la base de données avec les informations de contact. Pour plus d’informations sur l’amorçage de la base de données, consultez la page [Amorçage et débogage des bases de données Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).

7.  Dans la **Console du Gestionnaire de package**, entrez la commande suivante :

         update-database

    ![Commandes de la Console du Gestionnaire de package](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-console.png)

    La commande **update-database** exécute la première migration qui entraîne la création de la base de données. Par défaut, la base de données est créée en tant que base de données SQL Server Express LocalDB (par contre, si SQL Server Express est installé, la base de données est créée à l'aide de l'instance SQL Server Express).

8.  Appuyez sur Ctrl+F5 pour exécuter l'application.

L'application affiche les données amorcées, ainsi que des liens pour les modifier, les supprimer ou obtenir des informations supplémentaires.

![Affichage MVC des données](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx2.png)

OAuthAjout d'un fournisseur OAuth
---------------------------------

[OAuth](http://oauth.net/ "http://oauth.net/") est un protocole ouvert permettant de mettre en place une authentification sécurisée dans une méthode simple et standardisée à partir d’applications Web, mobiles ou de bureau. Le modèle Internet d'ASP.NET MVC utilise OAuth pour exposer Facebook, Twitter, Google et Microsoft comme fournisseurs d'authentification. Même si ce didacticiel utilise uniquement Facebook, Google et Yahoo comme fournisseurs d'authentification, vous pouvez facilement modifier le code pour utiliser un autre fournisseur. La procédure à suivre pour implémenter d’autres fournisseurs ressemble assez à celle présentée dans ce didacticiel.

En plus de l’authentification, ce didacticiel va également utiliser des rôles pour l’implémentation d’autorisations. Seuls les utilisateurs que vous ajoutez au rôle peutModifier pourront créer, modifier ou supprimer des contacts.

Inscription auprès d'un fournisseur externe
-------------------------------------------

Pour authentifier les utilisateurs avec les informations d'identification de fournisseurs externes, vous devez inscrire votre site Web auprès du fournisseur pour obtenir une clé et un secret de connexion, sauf dans le cas de Google et Yahoo.

Ce didacticiel ne montre pas toutes les étapes à exécuter dans le cadre de l'inscription auprès de ces fournisseurs. La procédure est en effet relativement simple. Pour inscrire correctement votre site, suivez les instructions fournies sur les sites en question. Pour démarrer, consultez les sites suivants destinés aux développeurs :

-   [Facebook](http://developers.facebook.com/)
-   [Microsoft](http://go.microsoft.com/fwlink/?LinkID=144070)
-   [Twitter](http://dev.twitter.com/)

Accédez à la page [https://developers.facebook.com/apps](https://developers.facebook.com/apps/) et connectez-vous, le cas échéant. Cliquez sur le bouton **Register as a Developer** et inscrivez-vous. Lorsque vous avez terminé, cliquez sur **Create New App**. Entrez le nom de l'application. Il n'est pas nécessaire d'indiquer un espace de noms d'application.

![Créer une application FB](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBapp.png)

Entrez localhost dans **App Domain** et http://localhost/ dans **Site URL**. Cliquez sur **Enabled** pour **Sandbox Mode**, puis sur **Save Changes**.

Vous aurez besoin des valeurs de **App ID** et **App Secret** pour implémenter OAuth dans cette application. ![Nouvelle application FB](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFB.png)

Création d'utilisateurs test
----------------------------

Dans le volet gauche situé sous **Settings**, cliquez sur **Developer Roles**. Cliquez sur le lien **Create** de la ligne **Test Users** (et non de la ligne **Testers**).

![Testeurs FB](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBt.png)

Cliquez sur le lien **Modify** pour obtenir l'adresse électronique de l'utilisateur test (que vous utiliserez pour vous connecter à l'application). Cliquez sur le lien **See More**, puis sur **Edit** pour définir le mot de passe de l'utilisateur test.

Ajout de l'ID et du secret de l'application à partir du fournisseur
-------------------------------------------------------------------

Ouvrez le fichier *App\_Start\\AuthConfig.cs*. Supprimez les caractères de commentaire de la méthode *RegisterFacebookClient* et ajoutez l'ID et le secret de l'application. Utilisez les valeurs obtenues (celles affichées ci-dessous ne fonctionneront pas). Supprimez les caractères de commentaire de l'appel *OAuthWebSecurity.RegisterGoogleClient* et ajoutez *OAuthWebSecurity.RegisterYahooClient*, comme indiqué ci-dessous. Les fournisseurs Google et Yahoo ne requièrent pas d'inscription ni de clés. Avertissement : sécurisez l'ID et le secret de l'application. Un utilisateur malveillant qui les obtiendrait pourrait se faire passer pour votre application.

     public static void RegisterAuth()
        {
            OAuthWebSecurity.RegisterFacebookClient(
                appId: "enter numeric key here",
                appSecret: "enter numeric secret here");

            OAuthWebSecurity.RegisterGoogleClient();
            OAuthWebSecurity.RegisterYahooClient();
        }

1.  Exécutez l’application et cliquez sur le lien **Se connecter**.
2.  Cliquez sur le bouton **Facebook**.
3.  Entrez vos informations d'identification Facebook ou celles de l'utilisateur test.
4.  Cliquez sur **Okay** pour autoriser votre application à accéder à vos ressources Facebook.
5.  Vous êtes redirigé vers la page Inscription. Si vous vous êtes connecté avec un compte test, vous pouvez remplacer le **nom d'utilisateur** par un intitulé plus court, comme « Test FB Bill ». Cliquez sur le bouton **Register** pour enregistrer le nom d'utilisateur et l'alias de messagerie électronique dans la base de données des membres.
6.  Inscrivez un autre utilisateur. Un bogue actuel du journal système vous empêche de vous déconnecter et de vous reconnecter avec un autre compte à l'aide du même fournisseur. Pour résoudre ce problème, accédez au site en utilisant un autre navigateur et inscrivez un autre utilisateur. Un des utilisateurs sera ajouté au rôle de gestionnaire et bénéficiera d'un accès en modification à l'application. Quant à l'autre utilisateur, il aura accès uniquement aux autres méthodes sur le site. Les utilisateurs anonymes n'auront accès qu'à la page d'accueil.
7.  Inscrivez un autre utilisateur à l'aide d'un fournisseur différent.
8.  **Facultatif :** vous pouvez également créer un compte local qui n'est associé à aucun des fournisseurs. Plus loin dans ce didacticiel, nous supprimerons la possibilité de créer des comptes locaux. Pour créer un compte local, cliquez sur le lien **Log out** (si vous êtes connecté), puis sur **Register link**. Vous pouvez créer un compte local à des fins d'administration, qui ne soit associé à aucun fournisseur d'authentification externe.

Base de données des membresAjout de rôles à la base de données des membres
--------------------------------------------------------------------------

Dans cette section, vous allez ajouter le rôle *peutModifier* à la base de données des membres. Seuls les utilisateurs qui ont le rôle peutModifier pourront modifier les données. Il est recommandé de nommer les rôles en fonction des actions qu’ils peuvent effectuer, c’est pourquoi *peutModifier* est un meilleur nom que *admin*. Lorsque votre application évolue, vous pouvez ajouter de nouveaux rôles tels que *peutSupprimerMembres*, plus parlant que le nom *superAdmin*.

1.  Dans le menu **View**, cliquez sur **Explorateur de serveurs**.

2.  Dans l'**Explorateur de serveurs**, développez **DefaultConnection**, puis **Tables**.

3.  Cliquez avec le bouton droit sur **UserProfile**, puis cliquez sur **Afficher les données de la table**.

    ![Afficher les données de la table](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSTD.png)

4.  Enregistrez la valeur de **UserId** pour l'utilisateur qui bénéficiera du rôle peutModifier. Dans l'image ci-dessous, l'utilisateur *ricka* associé au **UserId** 2 a le rôle peutModifier pour le site.

    ![ID des utilisateurs](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUid.png)

5.  Cliquez avec le bouton droit sur **webpages\_Roles**, puis cliquez sur **Afficher les données de la table**.
6.  Entrez **peutModifier** dans la cellule **RoleName**. Le **RoleId** est défini sur 1 si vous ajoutez un rôle pour la première fois. Enregistrez le RoleID. Veillez à ce qu'il n'y ait aucun espace de fin, sans quoi le rôle « peutModifier » de la table des rôles ne correspondra pas au rôle « peutModifier » du code du contrôleur.

    ![roleID](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxRoleID.png)

7.  Cliquez avec le bouton droit sur **webpages UsersInRoles**, puis cliquez sur **Afficher les données de la table**. Entrez le **UserId** de l'utilisateur auquel vous souhaitez octroyer un accès *peutModifier* et le **RoleId**.

    ![table des ID de rôle utilisateur](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUR.png)

La table **webpages\_OAuthMembership** contient le fournisseur OAuth, son UserID et le UserID de chaque utilisateur OAuth inscrit. La table **webpages-Membership** contient la table des membres ASP.NET. Vous pouvez y ajouter des utilisateurs à l'aide du lien d'inscription. Il est souhaitable d'ajouter un utilisateur avec le rôle *peutModifier* qui ne soit pas associé au fournisseur d'autorisation Facebook ou tiers. Vous bénéficierez ainsi d'un accès *peutModifier* permanent même lorsque le fournisseur d'autorisation tiers n'est pas disponible. Plus loin dans ce didacticiel, nous désactiverons l'inscription des membres ASP.NET.

Protection de l'application avec l'attribut Authorize
-----------------------------------------------------

Dans cette section, vous allez appliquer l'attribut [Authorize](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) pour limiter l'accès aux méthodes d'action. L'utilisateur anonyme pourra afficher uniquement la page d'accueil. Les utilisateurs inscrits pourront afficher les détails des contacts, ainsi que les pages À propos de et Contacts. Seuls les utilisateurs du rôle *peutModifier* pourront accéder aux méthodes d’action qui modifient les données.

1.  Ajoutez les filtres [Authorize](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) et [RequireHttps](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) à l’application. Une autre approche consiste à ajouter les attributs [Authorize](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) et [RequireHttps](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) à chaque contrôleur, mais il est recommandé de les appliquer à l’ensemble de l’application. En les ajoutant de manière globale, les nouveaux contrôleurs et les nouvelles méthodes d’action que vous ajouterez seront automatiquement protégés, vous n’aurez pas à vous rappeler qu’il faut leur appliquer ces attributs à chaque fois. Pour plus d'informations, consultez la page [Sécurisation de votre application ASP.NET MVC 4 et nouvel attribut AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Ouvrez le fichier *App\_Start\\FilterConfig.cs* et remplacez la méthode *RegisterGlobalFilters* par le code suivant.

         public static void
         RegisterGlobalFilters(GlobalFilterCollection filters)
         {
             filters.Add(new HandleErrorAttribute());
             filters.Add(new System.Web.Mvc.AuthorizeAttribute());
             filters.Add(new RequireHttpsAttribute());
         }

2.  Ajoutez l'attribut [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) à la méthode **Index**. L'attribut [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) vous permet de créer une liste des méthodes pour lesquelles vous voulez désactiver l'autorisation.
3.  Ajoutez [Authorize(Roles = "canEdit")] aux méthodes Get et Post et modifiez les données (Create, Edit, Delete).
4.  Ajoutez les méthodes *About* et *Contact*. Une partie du code terminé est affichée ci-dessous :

         public class HomeController : Controller
         {
             private ContactManagerContext db = new ContactManagerContext();
             [AllowAnonymous]
             public ActionResult Index()
             {
                 return View(db.Contacts.ToList());
             }

             public ActionResult About()
             {
                 return View();
             }

             public ActionResult Contact()
             {
                 return View();
             }

             [Authorize(Roles = "canEdit")]
             public ActionResult Create()
             {
                 return View();
             }
             // Méthodes déplacées et omises par souci de clarté.
         }

5.  Supprimez l'inscription de membres ASP.NET. La réinitialisation de mot de passe n’est pas prise en charge pour l’inscription à l’appartenance ASP.NET. De même, ce type d’inscription n’implique pas la vérification de l’identité d’un utilisateur (par exemple, via un [CAPTCHA](http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership)). Une fois qu’un utilisateur est authentifié en utilisant l’un des fournisseurs tiers, il peut s’inscrire. Dans AccountController, supprimez l'attribut *[AllowAnonymous]* des méthodes d'*inscription* GET et POST. Ceci empêchera les robots et les utilisateurs anonymes de s’inscrire.
6.  Dans le fichier *Views\\Shared\_LoginPartial.cshtml*, supprimez le lien d'action S'inscrire.
7.  Activez le protocole SSL. Dans l’Explorateur de solutions, cliquez sur le projet **GestionnaireContacts**, puis appuyez sur F4 pour afficher la boîte de dialogue Propriétés. Définissez **SSL activé** sur True. Copiez l’**URL SSL**.

    ![activer SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSSL.png)

8.  Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **Gestionnaire de contacts**, puis sur **Propriétés**.
9.  Dans l’onglet de gauche, cliquez sur **Web**.
10. Remplacez l'**URL du projet** par l'**URL SSL**.
11. Cliquez sur **Créer un répertoire virtuel**.

    ![activer SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxS2.png)

12. Appuyez sur Ctrl+F5 pour exécuter l'application. Le navigateur affichera un avertissement relatif au certificat. Dans le cas de notre application, vous pouvez cliquer en toute sécurité sur le lien **Poursuivre sur ce site Web**. Vérifiez que seuls les utilisateurs disposant du rôle *peutModifier* peuvent modifier les données et que les utilisateurs anonymes ne peuvent afficher que la page d'accueil.

    ![avertissement de certificat](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png)

    ![avertissement de certificat](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT2.png)

Les sites Web Azure incluant un certificat de sécurité valide, cet avertissement ne s'affiche pas lors du déploiement vers Azure.

Préparation de la base de donnéesCréation d'un script de déploiement de données
-------------------------------------------------------------------------------

La base de données des membres n'étant pas gérée par Entity Framework Code First, vous ne pouvez pas utiliser Migrations pour la déployer. Nous allons utiliser le fournisseur [dbDacFx](http://msdn.microsoft.com/fr-fr/library/dd394698.aspx) pour déployer le schéma de base de données et configurer le profil de publication pour exécuter un script visant à insérer les données initiales relatives aux membres dans les tables des membres.

Ce didacticiel utilise SQL Server Management Studio (SSMS) pour créer les scripts de déploiement de données.

Installez SSMS depuis le [Centre de téléchargement de Microsoft SQL Server 2012 Express](http://www.microsoft.com/en-us/download/details.aspx?id=29062) :

-   [ENU\\x64\\SQLManagementStudio\_x64\_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLManagementStudio_x64_ENU.exe) pour les systèmes 64 bits.
-   [ENU\\x86\\SQLManagementStudio\_x86\_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLManagementStudio_x86_ENU.exe) pour les systèmes 32 bits.

Si vous ne choisissez pas la bonne version pour votre système, l'installation échouera. Vous devrez donc utiliser l'autre version.

Notez qu'il s'agit d'un téléchargement de 600 Mo. L'installation peut prendre du temps et un redémarrage de l'ordinateur peut être nécessaire.

Sur la première page du centre d'installation de SQL Server, cliquez sur **Nouvelle installation autonome SQL Server ou ajout de fonctionnalités à une installation existante** et suivez les instructions en acceptant les valeurs par défaut. L'image suivante montre l'étape d'installation de SSMS.

![Installer SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSS.png)

### Création du script de base de données de développement

1.  Exécutez SSMS.
2.  Dans la boîte de dialogue **Se connecter au serveur**, entrez *(localdb)\\v11.0* comme nom du serveur, ne modifiez pas l'option **Authentification** qui est définie sur **Authentification Windows**, puis cliquez sur **Se connecter**. Si SQL Express est installé, entrez **.\\SQLEXPRESS**.

    ![Boîte de dialogue Se connecter au serveur](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxC2S.png)

3.  Dans la fenêtre **Explorateur d'objets**, développez **Bases de données**, cliquez avec le bouton droit sur **aspnet-ContactManager**, cliquez sur **Tâches**, puis sur **Générer des scripts**.

    ![Générer des scripts](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxGenScripts.png)

4.  Dans la boîte de dialogue **Générer et publier des scripts**, cliquez sur **Définir les options de script**. Vous pouvez ignorer l'étape **Sélectionner les objets**, car la valeur par défaut est Générer un script pour la base de données entière et tous ses objets, ce qui nous convient.

5.  Cliquez sur **Avancé**.

    ![Définir les options de script](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx11.png)

6.  Dans la boîte de dialogue **Options de script avancées**, faites défiler jusqu'à **Types de données à inclure dans le script**, puis cliquez sur **Données uniquement** dans la liste déroulante (l'étape suivante est affichée dans l'image ci-dessous).

7.  Définissez **Générer un script d'utilisation de la base de données (USE DATABASE)** sur **False**. Les instructions USE ne sont pas valides pour la base de données SQL Azure et ne sont pas nécessaires pour un déploiement vers SQL Server Express dans l'environnement de test.

    ![Définir les options de script](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAdv.png)

8.  Cliquez sur **OK**.
9.  Dans la boîte de dialogue **Générer et publier des scripts**, la zone **Nom du fichier** indique l'emplacement où le script sera créé. Remplacez le chemin d'accès par le dossier de votre solution (celui contenant le fichier *Contacts.sln*) et remplacez le nom de fichier par *aspnet-data-membership.sql*.
10. Cliquez sur **Suivant** pour accéder à l'onglet **Résumé**, puis de nouveau sur **Suivant** pour créer le script.

    ![Enregistrer ou publier](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx1.png)

11. Cliquez sur **Terminer**.

Déploiement de l’application dans Azure
---------------------------------------

1.  Ouvrez le fichier *Web.config* racine de l'application. Recherchez le balisage *DefaultConnection*, puis copiez et collez-le sous la ligne *DefaultConnection*. Renommez l'élément copié *DefaultConnectionDeploy*. Cette chaîne de connexion vous permettra de déployer les données utilisateur dans la base de données des membres. 
    ![3 chaînes de connexion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxd.png)

2.  Générez l'application.
3.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.

    ![Publier dans le menu contextuel du projet](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-publish-001.png)

L'Assistant **Publier le site Web** s'ouvre.

1.  Cliquez sur l'onglet **Paramètres**. Cliquez sur l'icône **v** pour sélectionner la **Chaîne de connexion distante** pour **ContactManagerContext** et **DefaultConnectionDeploy**. Les trois bases de données répertoriées utiliseront la même chaîne de connexion. La base de données **ContactManagerContext** stocke les contacts, **DefaultConnectionDeploy** permet uniquement de déployer les données des comptes d'utilisateur dans la base de données des membres et la base de données **UsersContext** est la base de données des membres.
2.  

    ![paramètres](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxD2.png)

2.  Sous **ContactManagerContext**, sélectionnez **Execute Code First Migrations**.

    ![paramètres](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

3.  Sous **DefaultConnectionDeploy**, sélectionnez **Mettre à jour la base de données**, puis cliquez sur le lien **Configurer les mises à jour de la base de données**.
4.  Cliquez sur le lien **Ajouter un script SQL** et accédez au fichier *aspnet-data-membership.sql*. Cette opération est unique. Lors du déploiement suivant, désactivez **Mettre à jour la base de données**, car vous n'aurez pas besoin d'ajouter les données utilisateur aux tables des membres.

    ![ajouter sql](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAddSQL2.png)

5.  Cliquez sur **Publier**.
6.  Accédez à la page [https://developers.facebook.com/apps](https://developers.facebook.com/apps/) et remplacez les paramètres **App Domains** et **Site URL** par l'URL Azure.
7.  Testez l'application Vérifiez que seuls les utilisateurs disposant du rôle *peutModifier* peuvent modifier les données et que les utilisateurs anonymes ne peuvent afficher que la page d'accueil. Vérifiez que les utilisateurs authentifiés peuvent accéder à tous les liens qui ne modifient pas les données.
8.  À la prochaine publication de l'application, n'oubliez pas de désactiver **Mettre à jour la base de données** sous **DefaultConnectionDeploy**.

    ![paramètres](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

Mise à jour de la base de donnéesMise à jour de la base de données des membres
------------------------------------------------------------------------------

Une fois que le site a été déployé dans Azure et que vous disposez d'un plus grand nombre d'utilisateurs inscrits, vous pouvez attribuer le rôle *peutModifier* à certains d'entre eux. Dans cette section, nous allons utiliser Visual Studio pour nous connecter à la base de données SQL et ajouter des utilisateurs au rôle *peutModifier*.

![paramètres](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**. ![Publier](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxP.png)

2.  Cliquez sur l'onglet **Paramètres**.
3.  Copiez la chaîne de connexion. Par exemple, la chaîne de connexion utilisée dans cet exemple est : Data Source=tcp:d015leqjqx.database.windows.net,1433; Initial Catalog=ContactDB2;User Id=ricka0@d015lxyze;Password=xyzxyz
4.  Fermez la boîte de dialogue de publication.
5.  Dans le menu **View**, cliquez sur **Explorateur de serveurs**.

6.  Cliquez sur l'icône **Connexion à une base de données**.

    ![Publier](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxc.png)

7.  Si vous êtes invité à entrer la source de données, cliquez sur **Microsoft SQL Server**.
    ![Publier](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx3.png)

8.  Copiez et collez le **nom du serveur**, qui commence par *tcp* (voir l'image ci-dessous).
9.  Cliquez sur **Utiliser l'authentification SQL Server**.
10. Entrez votre **nom d'utilisateur** et votre **mot de passe**, qui sont disponibles dans la chaîne de connexion que vous avez copiée.
11. Entrez le nom de la base de données (ContactDB ou la chaîne située après "Initial Catalog=" si vous l'avez nommée différemment). Si une erreur s'affiche, reportez-vous à la section suivante.
12. Cliquez sur **Tester la connexion**. Si une erreur s'affiche, reportez-vous à la section suivante. ![add con dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx4.png)

Erreur de connexion « Impossible d'ouvrir le serveur »
------------------------------------------------------

Si une erreur indique « Impossible d'ouvrir le serveur », vous devez ajouter votre adresse IP aux adresses IP autorisées.

![erreur de pare-feu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx5.png)

1.  Dans le portail Azure, sélectionnez **Bases de données SQL** dans l’onglet gauche.

    ![Sélectionner SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx6.png)

2.  Sélectionnez la base de données que vous voulez ouvrir.

3.  Cliquez sur le lien **Set up Azure firewall rules for this IP address**.

    ![règles de pare-feu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx7.png)

4.  Lorsque le message « L'adresse IP xxx.xxx.xxx.xxx en cours n'est pas comprise dans les règles de pare-feu existantes. Voulez-vous mettre à jour les règles de pare-feu ? » s'affiche, cliquez sur **Oui**. Vous devrez la plupart du temps ajouter une plage d'adresses IP plutôt qu'une seule adresse IP.

Ajout d'une plage d'adresses IP autorisées
------------------------------------------

1.  Dans le portail Azure, cliquez sur **Bases de données SQL**.
2.  Cliquez sur le **serveur** qui héberge votre base de données.

    ![serveur de base de données](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx8.png)

3.  Cliquez sur **Configurer** en haut de la page.
4.  Ajoutez un nom de règle, ainsi que le début et la fin de la plage d’adresses IP. ![plage d'adresses IP](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx9.png)

5.  En bas de la page, cliquez sur **Enregistrer**.
6.  Vous pouvez à présent modifier la base de données des membres en suivant la procédure précédente.

Étapes suivantesÉtapes suivantes
--------------------------------

Ce didacticiel et son exemple d'application ont été écrits par [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) avec l'aide de Tom Dykstra, Tom FitzMacken et Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

N'hésitez pas à nous transmettre vos commentaires sur ce qui vous a plu et ce qui pourrait être amélioré... pas seulement à propos de ce didacticiel, mais aussi en ce qui concerne les produits présentés ici. Vos commentaires nous aideront à orienter nos améliorations. Nous aimerions particulièrement savoir si l'automatisation du processus de configuration et de déploiement de la base de données d'appartenance vous intéresse.

Pour obtenir des boutons de connexion Facebook, Google et Yahoo colorés, consultez le billet de blog traitant de la [personnalisation des boutons de connexion externes dans ASP.NET MVC 4](http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/). Pour plus d'informations sur l'utilisation de l'authentification Windows, consultez les ressources suivantes :

-   [Authentification Azure](http://www.asp.net/vnext/overview/fall-2012-update/windows-azure-authentication)
-   [Création d'un site Intranet à l'aide d'ASP.NET MVC](http://msdn.microsoft.com/fr-fr/library/gg703322(v=vs.98).aspx)

Une autre méthode pour stocker des données dans une application Azure consiste à utiliser le stockage Azure, qui permet de stocker des données non relationnelles sous la forme d'objets blob et de tables. Pour plus d'informations sur ASP.NET MVC et Azure, consultez les liens suivants.

-   [Application ASP.NET multiniveau avec tables, files d'attente et objets blob de stockage Azure](http://www.windowsazure.com/fr-fr/develop/net/tutorials/multi-tier-web-site/1-overview/).
-   [Introduction à ASP.NET MVC 4](http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4)
-   [Mise en route d'Entity Framework avec MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)
-   [OAuth 2.0 et connexion](http://blogs.msdn.com/b/vbertocci/archive/2013/01/02/oauth-2-0-and-sign-in.aspx)

Vous savez désormais déployer une application Web dans un site Web Azure. Pour savoir comment configurer, gérer et mettre à l'échelle les sites Web Azure, consultez les procédures disponibles à la page [Tâches courantes](http://www.windowsazure.com/fr-fr/develop/net/common-tasks/).

Pour savoir comment déboguer les sites Web Azure, consultez la page [Résolution des problèmes de sites Web Azure dans Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

Pour savoir comment déployer une application dans un service cloud Azure, consultez la [version de ce didacticiel consacrée aux services cloud](http://www.windowsazure.com/fr-fr/develop/net/tutorials/cloud-service-with-sql-database/), ainsi que la page [Développement d'applications Web avec Azure](http://msdn.microsoft.com/fr-fr/library/Hh674484). Voici quelques unes des raisons qui peuvent motiver l'exécution d'une application Web ASP.NET dans un service cloud Azure plutôt que dans un site Web Azure :

-   Vous souhaitez disposer d'autorisations d'administrateur sur le serveur Web sur lequel l'application est exécutée.
-   Vous voulez utiliser la connexion Bureau à distance pour accéder au serveur Web sur lequel l'application est exécutée.
-   Votre application inclut plusieurs niveaux et vous souhaitez répartir le travail sur plusieurs serveurs virtuels (Web et de travail).

Pour plus d'informations sur la base de données SQL et Azure Storage, consultez la page [Offres de stockage de données sur Azure](http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx).

Pour savoir comment utiliser la base de données SQL, consultez la page [Utilisation de la base de données SQL Azure dans la carte de contenu d'accès aux données .NET](http://go.microsoft.com/fwlink/p/?LinkId=282414#ssdb).

Pour en savoir plus sur Entity Framework et Code First Migrations, consultez les ressources suivantes :

-   [Mise en route d'Entity Framework avec MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)
-   [Code First Migrations](http://msdn.microsoft.com/fr-fr/library/hh770484)

