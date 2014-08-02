<properties  linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Web Site with SQL Database" pageTitle="Deploy a Secure ASP.NET MVC app with Membership, OAuth, and SQL Database to an Azure Web Site" metaKeywords="Azure hello world tutorial, Azure getting started tutorial, SQL Database tutorial, Azure .NET hello world tutorial, Azure C# hello world tutorial, SQL Azure C# tutorial" description="Learn how to develop an ASP.NET MVC 5 web site with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title=" OAuth" authors="" solutions="" writer="riande" manager="wpickett" editor="mollybos" />

# Déploiement d'une application ASP.NET MVC 5 sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Azure

***Par [Rick Anderson][1] et Tom Dykstra. Mis à jour le 2 avril 2014.***

Ce didacticiel vous montre comment générer une application Web ASP.NET MVC 5 sécurisée qui permet aux utilisateurs de se connecter avec des informations d'identification Facebook ou Google. Vous allez également déployer l'application sur Azure.

Vous pouvez ouvrir gratuitement un compte Azure. Si vous n'avez pas déjà Visual Studio 2013, le Kit de développement logiciel (SDK) installe automatiquement Visual Studio Express 2013 pour le Web. Vous pouvez commencer vos développement Azure gratuitement. Si vous voulez utiliser Visual Studio 2012, consultez la [version précédente de ce didacticiel](/en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/). Cette version du didacticiel est bien plus simple que la version précédente.

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de ce didacticiel, vous disposerez d'une application Web pilotée par les données sécurisée et fonctionnelle dans le cloud et utilisant une base de données du cloud.

Vous apprendrez à effectuer les opérations suivantes :

* création d'un projet ASP.NET MVC 5 sécurisé et publication de ce projet vers un site Web Azure ;
* utilisation des fonctionnalités [OAuth][2] et [OpenID][3], ainsi que de la base de données d'appartenance ASP.NET pour sécuriser votre application ;
* utilisation de la nouvelle API d'appartenance pour ajouter des utilisateurs et des rôles ;
* utilisation d'une base de données SQL pour stocker des données dans Azure.

Vous développerez une application Web de liste de contacts simple basée sur ASP.NET MVC 5 et utilisant Entity Framework ADO.NET pour accéder à la base de données. L'illustration suivante montre la page de connexion une fois l'application terminée :

![page de
connexion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png)

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez [activer les avantages de votre abonnement MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [obtenir une évaluation gratuite](/en-us/pricing/free-trial/?WT.mc_id=A261C142F)

Dans ce didacticiel :

* [Configuration de l'environnement de développement](#setupdevenv)
* [Configuration de l'environnement Azure](#bkmk_setupwindowsazure)
* [Création d'une application ASP.NET MVC 5](#bkmk_createmvc4app)
* [Déploiement de l'application dans Azure](#bkmk_deploytowindowsazure1)
* [Ajout d'une base de données à l'application](#bkmk_addadatabase)
* [Ajout d'un fournisseur OAuth](#addOauth)
* [Utilisation de l'API d'appartenance](#mbrDB)
* [Déploiement de l'application dans Azure](#bkmk_deploytowindowsazure11)
* [Étapes suivantes](#nextsteps)

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Vous devez installer [Visual Studio 2013 Update 2 RC][4] ou une version plus récente pour utiliser le nouveau certificat SSL pour l'hôte local (localhost).

<h2><a name="bkmk_setupwindowsazure"></a>Configuration de l'environnement Azure</h2>


Configurez ensuite l'environnement Azure en créant un site Web Azure et une base de données SQL.

### Création d'un site Web et d'une base de données SQL dans Azure

Votre site Web Azure est exécuté dans un environnement d'hébergement partagé, ce qui veut dire qu'il s'exécute sur des machines virtuelles partagées avec d'autres clients Azure. Un environnement d'hébergement partagé permet de commencer à utiliser le cloud à moindre frais. Plus tard, si votre trafic Web augmente, vous pourrez mettre votre application à l'échelle pour répondre à vos besoins, en exécutant des machines virtuelles dédiées. Si vous avez besoin d'une architecture plus complexe, vous pouvez migrer vers un service cloud Azure. Les services cloud sont exécutés sur des machines virtuelles dédiées pour vous permettre de les configurer selon vos besoins.

La base de données SQL Azure est un service de bases de données relationnelles sur le cloud, basé sur les technologies SQL Server. Les outils et applications fonctionnant avec SQL Server fonctionnent
également avec la base de données SQL.

1.  Dans le [portail de gestion Azure][5], dans l'onglet gauche, cliquez sur **Web Sites**, puis sur **New**.
    
    ![Bouton Nouveau du portail de
    gestion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png)

2.  Cliquez sur **Web Site**, puis sur **Custom Create**.
    
    ![Lien Create with Database dans le portail de
    gestion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png)
    
    L'Assistant **New Web Site - Custom Create** s'ouvre.

3.  Lors de l'étape **Create Web Site** de l'Assistant, entrez une chaîne dans la zone **URL** pour l'utiliser en tant qu'URL unique pour votre application. L'URL complète se composera du texte entré dans cette zone, ainsi que du 	suffixe affiché en regard de la zone. L'illustration indique une URL qui est probablement prise (il faut donc en choisir une autre).
    
    ![Lien Create with Database dans le portail de
    gestion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png)

4.  Dans la liste déroulante **Database**, sélectionnez **Create a free SQL database**.

5.  Dans la liste déroulante **Region**, sélectionnez la même région que celle de votre site Web. Ce paramètre indique le centre de données sur lequel votre machine virtuelle sera exécutée.

6.  Dans la zone **DB Connection String Name**, ne changez pas la valeur par défaut *DefaultConnection*.

7.  Cliquez sur les flèches pointant vers la droite en bas de la zone. L'Assistant passe à l'étape **Specify database settings**.

8.  Dans la zone **Name**, entrez *ContactDB* (voir l'image ci-dessous). 

9.  Dans la zone **Server**, sélectionnez **New SQL Database server**. (voir l'image ci-dessous). Par ailleurs, si vous avez déjà créé une base de données SQL Server, vous pouvez la sélectionner dans la liste déroulante.

10. Définissez la **Region** sur la même zone que celle utilisée pour la création du site Web.

11. Entrez un **Nom de connexion** et un **Mot de passe** d'administrateur. Si vous avez sélectionné **New SQL Database server**, vous ne devez pas entrer un nom et un mot de passe existant ici, mais une nouvelle paire nom/mot de passe que vous allez choisir maintenant et utiliser ultérieurement lorsque vous accèderez à la base de données. Si vous avez sélectionné une base de données SQL Server créée auparavant, vous devez entrer le mot de passe et le nom du compte SQL Server que vous aviez créé. Pour ce didacticiel, ne cochez pas la case **Advanced**. Pour une base de données gratuite, vous pouvez seulement définir le classement.

12. Cliquez sur la coche située dans le coin inférieur droit de la zone pour indiquer que vous avez terminé.
    
    ![Paramètres de base de données du nouveau site
    Web : Assistant Création avec une base de
    données](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png)
    
    L'image suivante montre l'utilisation d'un nom et d'un mot de passe
    de connexion à une base de données SQL existante.
    
    ![Paramètres de base de données du nouveau site
    Web : Assistant Création avec une base de
    données](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png)
    
    Le portail de gestion renvoie vers la page Sites Web, tandis que la colonne **Status** indique que le site est en cours de création. Après un temps d'attente (généralement inférieur à une minute), la colonne **Status** indique que la création du site est réussie. Dans la barre de navigation de gauche, le nombre de sites de votre compte apparaît en regard de l'icône **Web Sites**, tandis que le nombre de bases de données apparaît en regard de l'icône **SQL Databases**.

<h2><a name="bkmk_createmvc4app"></a>Création d'une application ASP.NET MVC 5</h2>


Vous avez créé un site Web Azure, mais il est encore vide. La prochaine
étape consiste à créer l'application Web Visual Studio que vous allez
publier sur Azure.

### Création du projet

1.  Dans le menu **Fichier**, cliquez sur **Nouveau projet**.
    
    ![Nouveau projet dans le menu
    Fichier](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

2.  Dans la boîte de dialogue **Nouveau projet**, développez **C#**, puis sélectionnez **Web** sous **Modèles installés**, puis sélectionnez **Application Web ASP.NET**.

3.  Nommez l'application **GestionnaireContacts** puis cliquez sur **OK**.
    
    ![Boîte de dialogue Nouveau
    projet](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
    
    **Remarque :** le champ Nom de l'image ci-dessus contient la valeur << MyExample >>, mais assurez-vous de bien rentrer la valeur << GestionnaireContacts >>. Les blocs de code que vous allez copier plus tard partent du principe que le nom du projet est GestionnaireContacts.

4.  Dans la boîte de dialogue **New ASP.NET Project**, sélectionnez le modèle **MVC**, **désactivez** la case à cocher **Create remote resources**, puis cliquez sur **OK**. Il est possible que la case à cocher soit nommée **Host in 	the cloud** au lieu de **Create remote resources**.
    
    ![Boîte de dialogue Nouveau projet
    ASP.NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

### Définition de l'en-tête et du pied de page de la page

1.  Dans l'**Explorateur de solutions**, ouvrez le fichier *Layout.cshtml* dans le dossier *Views\\Shared*.
    
    ![_Layout.cshtml dans l’Explorateur de
    solutions](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png)

2.  Remplacez les deux occurrences de << Mon application ASP.NET MVC >> par << Gestionnaire de contacts >>.

3.  Remplacez << Nom de l'application >> par << Démo de GC >>.

4.  Procédez à la mise à jour du premier lien Action, puis remplacez *Accueil* par *Cm* pour utiliser le contrôleur *Cm*.
    
    ![modifications du
    code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)

### Exécution locale de l'application

1.  Appuyez sur Ctrl+F5 pour exécuter l'application.
    
    La page d'accueil de l'application apparaît dans le navigateur par défaut.
    
    ![Site Web exécuté en
    local](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Voilà, vous avez fait tout ce qu'il fallait pour créer l'application que vous allez déployer dans Azure. Après cela, vous allez ajouter les fonctionnalités de base de données.

<h2><a name="bkmk_deploytowindowsazure1"></a>Déploiement de l'application dans Azure</h2>


1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.
    
    ![Publier dans le menu contextuel du
    projet](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
    
    L'Assistant **Publier le site Web** s'ouvre.

2.  Sous l'onglet **Profil** de l'Assistant **Publier le site Web**, cliquez sur **Sites Web Azure**.
    
    ![Importer les paramètres de
    publication](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss2.PNG)

3.  Cliquez sur le bouton **Sign In**, puis connectez-vous au portail Azure.

	![seconnecter](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss22.PNG)

	Après la connexion, la boîte de dialogue **Select Existing Web Site** apparaît.

4.  Sélectionnez le site Web que vous avez créé dans la première partie de ce didacticiel, puis cliquez sur **OK**.

	![sélectionner un site Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss3.png)

5.  Dans la boîte de dialogue **Publier le site Web**, cliquez sur **Publier**.
    
    ![Publier](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)
    
    L'application créée est maintenant exécutée dans le cloud. Au prochain déploiement de l'application, seuls les fichiers modifiés (ou nouveaux) seront déployés.
    
    ![Exécution dans le
    cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Ajout d'une base de données à l'application</h2>


À présent, vous allez mettre à jour l'application MVC pour y ajouter la capacité d'afficher et de mettre à jour les contacts, puis stocker les données dans une base de données. L'application va utiliser Entity Framework pour créer la base de données, ainsi que lire et mettre à jour les données associées.

### Ajout de classes de modèle de données pour les contacts

Commencez par créer un modèle de données simple dans le code.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier Modèles, cliquez sur **Ajouter**, puis sur **Classe**.
    
    ![Menu contextuel Ajouter une classe aux
    modèles](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2.  Dans la boîte de dialogue **Ajouter un nouvel élément**, nommez le nouveau fichier de classe *Contact.cs*, puis cliquez sur **Ajouter**.
    
    ![Boîte de dialogue Ajouter un nouvel
    élément](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png)

3.  Remplacez le contenu du fichier Contacts.cs par le code suivant.
    
         using System.ComponentModel.DataAnnotations;
         using System.Globalization;
         namespace GestionnaireContacts.Models
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

La fonctionnalité de génération de modèle automatique ASP.NET MVC peut générer automatiquement un code qui effectue les actions CRUD (Create, Read, Update et Delete, ou Créer, Lire, Mettre à jour et Supprimer).

<h2><a name="bkmk_addcontroller"></a>Ajout d'un contrôleur et affichage des données</h2>


1.  Développez le projet **(Ctrl+Maj+B)**. (Vous devez développer le projet avant d'utiliser le mécanisme de génération de modèle automatique.)

2.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier Contrôleurs, puis cliquez sur **Ajouter** et sur **Contrôleur**.
    
    ![Ajouter un contrôleur dans le menu contextuel du dossier
    Contrôleurs](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png)

3.  Dans la boîte de dialogue **Add Scaffold**, sélectionnez **MVC 5 Controller with views, using EF**, puis cliquez sur **Ajouter**.
    
    ![Boîte de dialogue Add
    Scaffold](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)

4.  Dans la zone déroulante **Classe de modèle**, sélectionnez **Contact (GestionnaireContacts.Models)**. (voir l'image ci-dessous).

5.  Dans la **classe du contexte des données**, sélectionnez **ApplicationDbContext (GestionnaireContacts.Models)**. **ApplicationDbContext** est utilisé pour la base de données d'appartenance et nos données de contact.

6.  Dans la zone de texte **Controller name**, entrez << CmController >> pour le nom du contrôleur.
    
    ![Boîte de dialogue New data
    ctx](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

7.  Cliquez sur **Ajouter**.
    
    Visual Studio crée des méthodes de contrôleur et des vues pour les opérations de base de données CRUD des objets **Contact**.

## Activation des migrations, création de la base de données, ajout d'exemples de données et d'un initialiseur de données

L'étape suivante consiste à activer la fonctionnalité [Migrations Code First][6] pour créer la base de données en fonction du modèle de données que vous avez créé.

1.  Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** puis **Console du Gestionnaire de package**.

	![Console du
    Gestionnaire de package dans le menu
    Outils](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2.  Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :
    
         enable-migrations
    
    La commande **enable-migrations** crée un dossier *Migrations*, dans lequel elle place un fichier *Configuration.cs* que vous pouvez modifier pour amorcer la base de données et configurer les migrations.

3.  Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :
    
         add-migration Initial
    
    La commande **add-migration Initial** génère un fichier nommé **<date\_stamp>Initial** dans le dossier *Migrations* qui crée la base de données. Le premier paramètre (**Initial**) est arbitraire et permet de créer le nom du fichier. Les nouveaux fichiers de classe sont affichés dans l'**Explorateur de solutions**. Dans la classe **Initial**, la méthode **Up** crée la table des contacts et la méthode **Down** (utilisée lorsque vous voulez revenir à l'état précédent) annule cette table.

4.  Ouvrez le fichier *Migrations\\Configuration.cs*. 

5.  Ajoutez l'espace de noms suivant.
    
    	using GestionnaireContacts.Models;

6.  Remplacez la méthode *Seed* par le code suivant :
    
         protected override void Seed(GestionnaireContacts.Models.ApplicationDbContext context)
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
    
    Ce code initialise (amorce) la base de données avec les informations de contact. Pour plus d'informations sur l'amorçage de la base de données, consultez la page [Amorçage et débogage des bases de données Entity Framework (EF)][7].

7.  Dans la **Console du Gestionnaire de package**, entrez la commande suivante :
    
         update-database
    
    ![Commandes de la Console du Gestionnaire de
    package](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png)
    
    La commande **update-database** exécute la première migration qui entraîne la création de la base de données. Par défaut, la base de données est créée en tant que base de données SQL Server Express LocalDB.

8.  Appuyez sur Ctrl+F5 pour exécuter l'application, puis cliquez sur le lien **Démo de GC** ou accédez à http://localhost:(port#)/Cm.
    
    L'application affiche les données amorcées, ainsi que des liens pour les modifier, les supprimer ou obtenir des informations supplémentaires. Vous pouvez créer, modifier, supprimer et afficher les données.
    
    ![Affichage MVC des
    données](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png)

<h2><a name="addOauth"></a><span  class="short-header">OAuth</span>Ajout d'un fournisseur OAuth2 et OpenID</h2>


[OAuth][2] est un protocole ouvert permettant de mettre en place une authentification sécurisée dans une méthode simple et standardisée à partir d'applications Web, mobiles ou de bureau. Le modèle Internet d'ASP.NET MVC utilise le protocole OAuth ainsi qu'[OpenID][3] pour exposer Facebook, Twitter, Google et Microsoft comme fournisseurs d'authentification. Même si ce didacticiel utilise uniquement Google comme fournisseur d'authentification, vous pouvez facilement modifier le code pour utiliser n'importe quel autre fournisseur. La procédure à suivre pour implémenter d'autres fournisseurs ressemble assez à celle présentée dans ce didacticiel. Pour utiliser Facebook en tant que fournisseur d'authentification, consultez le didacticiel [Création d'une application ASP.NET MVC 5 avec une authentification OAuth2 et OpenID pour Facebook et Google][8].

En plus de l'authentification, ce didacticiel va également utiliser des rôles pour l'implémentation d'autorisations. Seuls les utilisateurs ajoutés au rôle *peutModifier* pourront modifier les données (et donc créer, modifier ou supprimer des contacts).

1.  Ouvrez le fichier *App\_Start\\Startup.Auth.cs*. Supprimez les caractères du commentaire de la méthode *app.UseGoogleAuthentication()*.

2.  Exécutez l'application et cliquez sur le lien **Se connecter**. 

3.  Sous **Use another service to log in**, cliquez sur le bouton **Google**.
    
    ![Connexion à
    Google](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss23.PNG)

3.  Entrez vos informations d'identification. 

5.  Le serveur d'authentification Google vous demandera une autorisation pour votre application relative à l'affichage de votre adresse de messagerie électronique et des informations de base de votre compte. Cliquez sur **Accepter**. 

	![Demande d’autorisation de GOOGLE](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss7.PNG)

6.  Vous êtes redirigé vers la page Inscription. Le nom d'utilisateur correspond à l'alias de messagerie électronique que vous avez utilisé pour vous inscrire, vous pouvez le modifier ultérieurement si vous le voulez. Cliquez sur 	**S'inscrire**.
    
    ![s’inscrire](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss8.PNG)

<h2><a name="mbrDB"></a><span  class="short-header">Base de données d'appartenance</span>Utilisation de l'API d'appartenance</h2>


Dans cette section, vous allez ajouter un utilisateur local, ainsi que le rôle *peutModifier* à la base de données d'appartenance. Seuls les utilisateurs du rôle *peutModifier* pourront modifier les données. Il est recommandé de nommer les rôles en fonction des actions qu'ils peuvent effectuer, c'est pourquoi *peutModifier* est un meilleur nom que *admin*. Lorsque votre application évolue, vous pouvez ajouter de nouveaux rôles tels que *peutSupprimerMembres*, plus parlant que le nom *superAdmin*.

1.  Ouvrez le fichier *migrations\\configuration.cs* et ajoutez les instructions `using` suivantes :
    
         using Microsoft.AspNet.Identity;
         using Microsoft.AspNet.Identity.EntityFramework;

2.  Ajoutez la méthode **AddUserAndRole** suivante à la classe :
    
          bool AddUserAndRole(GestionnaireContacts.Models.ApplicationDbContext context)
          {
             IdentityResult ir;
             var rm = new RoleManager<IdentityRole>
                 (new RoleStore<IdentityRole>(context));
             ir = rm.Create(new IdentityRole("peutModifier"));
             var um = new UserManager<ApplicationUser>(
                 new UserStore<ApplicationUser>(context));
             var user = new ApplicationUser()
             {
                UserName = "utilisateur1@contoso.com",
             };
             ir = um.Create(user, "P_assw0rd1");
             if (ir.Succeeded == false)
                return ir.Succeeded;
             ir = um.AddToRole(user.Id, "peutModifier");
             return ir.Succeeded;
          }

3.  Appelez la nouvelle méthode à partir de la méthode **Seed** :
    
         protected override void Seed(GestionnaireContacts.Models.ApplicationDbContext context)
         {
             AddUserAndRole(context);
             context.Contacts.AddOrUpdate(p => p.Name,
                 // Code supprimé à des fins de concision
         }
    
    Les images suivantes indiquent les modifications apportées à la méthode *Seed* :
    
    ![image du
    code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)
    
    Ce code crée un rôle nommé *peutModifier*, un utilisateur local nommé *utilisateur1@contoso.com* et ajoute *utilisateur1@contoso.com* au rôle *peutModifier*. Pour plus d'informations, consultez la [page de ressources d'identité
    ASP.NET][9].

## Utilisation de code temporaire pour ajouter de nouveaux utilisateurs de réseaux sociaux au rôle peutModifier

Dans cette section, vous allez modifier temporairement la méthode**ExternalLoginConfirmation** dans le contrôleur Compte pour ajouter de nouveaux utilisateurs se connectant avec un fournisseur OAuth ou OpenID au rôle *peutModifier*. Nous allons modifier temporairement la méthode **ExternalLoginConfirmation** pour ajouter automatiquement de nouveaux utilisateurs à un rôle d'administration. Tant que nous n'avons pas fourni un outil pour ajouter et gérer des rôles, nous allons utiliser le code d'inscription automatique. Nous espérons fournir un outil similaire à [WSAT][10] à l'avenir pour vous permettre de créer et de modifier les comptes et les rôles des utilisateurs. Plus tard dans ce didacticiel, nous verrons comment utiliser l'**Explorateur de solutions** pour ajouter des utilisateurs aux rôles.

1.  Ouvrez le fichier **Controllers\\AccountController.cs** et accédez à la méthode **ExternalLoginConfirmation**.
2.  Ajoutez l'appel suivant vers **AddToRoleAsync** juste avant l'appel **SignInAsync**.
    
                 await UserManager.AddToRoleAsync(user.Id, "peutModifier");
    
    Le code ci-dessus ajoute les utilisateurs récemment enregistrés au rôle << peutModifier >>, qui leur permet d'accéder aux méthodes d'action pour modifier les données. L'image suivante illustre une modification du code.
    
    ![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Plus loin dans ce didacticiel, vous allez déployer l'application sur Azure, en vous connectant avec Google ou un autre fournisseur d'authentification tiers. Ceci vous permettra d'ajouter votre compte récemment inscrit au rôle *peutModifier*. Les utilisateurs trouvant l'URL de votre site et possédant un ID Google peuvent ensuite s'inscrire et mettre à jour votre base de données. Pour empêcher que cela se produise, vous pouvez arrêter le site. Vous pouvez vérifier quels utilisateurs sont rattachés au rôle *peutModifier* en examinant la base de données.

Dans la **Console du Gestionnaire de package**, actionnez la touche Haut pour afficher la commande suivante :

    	Update-Database

Exécutez la commande **Update-Database** qui exécutera la méthode **Seed**, qui exécutera la méthode **AddUserAndRole** que vous venez d'ajouter. **AddUserAndRole** crée *utilisateur1@contoso.com* et l'ajoute au rôle *peutEditer*.

## Protection de l'application à l'aide du protocole SSL et de l'attribut Authorize

Dans cette section, vous allez appliquer l'attribut [Authorize][11] pour limiter l'accès aux méthodes d'action. Les utilisateurs anonymes pourront uniquement afficher la méthode d'action **Index** du contrôleur d'accueil. Les utilisateurs inscrits pourront afficher les données de contact (pages **Index** et **Détails** du contrôleur Cm), les pages << À propos de >> et << Contact >>. Seuls les utilisateurs du rôle *peutModifier* pourront accéder aux méthodes d'action qui modifient les
données.

1.  Ajoutez les filtres [Authorize][11] et [RequireHttps][12] à l'application. Une autre approche consiste à ajouter les attributs [Authorize][11] et [RequireHttps][12] à chaque contrôleur, mais il est recommandé de les appliquer à l'ensemble de l'application. En les ajoutant de manière globale, les nouveaux contrôleurs et les nouvelles méthodes d'action que vous ajouterez seront automatiquement protégés, vous n'aurez pas à vous rappeler qu'il faut leur appliquer ces attributs à chaque fois. Pour plus d'informations, consultez la page [Sécurisation de votre application ASP.NET MVC et nouvel attribut AllowAnonymous][13]. Ouvrez le fichier *App\_Start\\FilterConfig.cs* et remplacez la méthode *RegisterGlobalFilters* par le code suivant (qui ajoute les deux filtres) :
    
         public static void
         RegisterGlobalFilters(GlobalFilterCollection filters)
         {
             filters.Add(new HandleErrorAttribute());
             filters.Add(new System.Web.Mvc.AuthorizeAttribute());
             filters.Add(new RequireHttpsAttribute());
         }
    
    L'image suivante montre le code modifié :
    
    ![image du
    code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss10.PNG)
    
    Le filtre [Authorize][11] appliqué dans le code ci-dessus empêchera les utilisateurs anonymes d'accéder aux méthodes de l'application. Vous allez utiliser l'attribut [AllowAnonymous][13] pour désactiver les contraintes d'autorisation dans quelques méthodes, pour que les utilisateurs anonymes puissent se connecter et afficher la page d'accueil. [RequireHttps][12] nécessite que tous les accès à l'application Web se fassent via HTTPS.

2.  Ajoutez l'attribut [AllowAnonymous][13] à la méthode **Index** du contrôleur d'accueil. L'attribut [AllowAnonymous][13] vous permet de créer une liste des méthodes pour lesquelles vous voulez désactiver l'autorisation. L'image 	ci-dessous montre une partie du HomeController :

	![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

3.  Effectuez une recherche globale pour *AllowAnonymous* : vous pouvez voir qu'il est utilisé dans les méthodes de connexion et d'inscription du contrôleur Compte.

4.  Dans *CmController.cs*, ajoutez `[Authorize(Roles = "peutModifier")]` aux méthodes HttpGet et HttpPost qui modifient les données (Create, Edit, Delete, toutes les méthodes d'action à l'exception de Index et Details) dans le 	contrôleur *Cm*. Une partie du code terminé est affichée ci-dessous :
    
    ![image du
    code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)

## Activation du protocole SSL pour le projet

1.  Activez le protocole SSL. Dans l'Explorateur de solutions, cliquez sur le projet **GestionnaireContacts**, puis appuyez sur F4 pour afficher la boîte de dialogue Propriétés. Définissez **SSL activé** sur True. Copiez l'**URL 	SSL**. L'URL SSL est https://localhost:44300/ sauf si vous avez déjà créé des sites Web SSL.
    
    ![activer
    SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png)

2.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **Gestionnaire de contacts**, puis sur **Propriétés**.

3.  Dans l'onglet de gauche, cliquez sur **Web**. 

4.  Modifiez l'**URL du projet** pour utiliser l'**URL SSL**, puis enregistrez la page (Ctrl+S).
    
    ![activer
    SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)

5.  Assurez-vous que Microsoft Internet Explorer est le navigateur par défaut pour Visual Studio, comme indiqué sur l'image ci-dessous :
    
    ![navigateur par
    défaut](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)
    
    Le sélecteur de navigateur vous permet d'indiquer le navigateur utilisé par Visual Studio.

	![sélecteur de navigateur](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

    Vous pouvez sélectionner plusieurs navigateurs et faire en sorte que Visual Studio mette à jour chaque navigateur lorsque vous apportez des modifications. Pour plus d’informations, consultez la page [Utilisation du lien de navigateur dans Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).

6.  Appuyez sur Ctrl+F5 pour exécuter l'application. Suivez les instructions pour approuver le certificat auto-signé généré par IIS Express.
    
    ![instructions pour approuver le certificat auto-signé généré par IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

7.  Lisez le contenu de la boîte de dialogue **Avertissement de sécurité**, puis cliquez sur **Oui** si vous souhaitez installer le certificat représentant **l'hôte local (localhost)**.

	![Avertissement relatif que certificat localhost IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

8.  IE affiche la *page d'accueil* sans avertissement SSL.
    
    ![IE avec SSL localhost sans
    avertissement](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)
    
    Google Chrome accepte également le certificat et affiche les contenus HTTPS sans avertissement. Firefox utilise son propre magasin de certificats. Il affiche donc un avertissement. Pour notre application, vous pouvez cliquer en toute sécurité sur **Je comprends les risques**.
    
    ![Avertissement de certificat
    Firefox](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

9.  Si vous êtes toujours connecté depuis une session précédente, cliquez sur le lien **Se déconnecter**.
10. Cliquez sur le lien **À propos de** ou **Contact**. Vous serez redirigé vers la page de connexion, car les utilisateurs anonymes ne peuvent pas afficher ces pages.
11. Cliquez sur le lien **Register as a new user** et ajoutez un utilisateur local avec l'adresse de messagerie *jerome@contoso.com*. Vérifiez que *Jerome* peut afficher les pages Accueil, Contact et À propos de.
    
    ![se
    connecter](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

12. Cliquez sur le lien *Démo de GC* et vérifiez que les données s'affichent correctement.
13. Cliquez sur le lien Modifier de la page. Vous serez redirigé vers la page de connexion (car un nouvel utilisateur local n'est pas ajouté au rôle *peutModifier*).
14. Connectez-vous avec l'identifiant *utilisateur1@contoso.com* en utilisant le mot de passe << P\_assw0rd1 >> (le << 0 >> de << word >> est un zéro). Vous serez redirigé vers la page de modification que vous aviez sélectionnée.
    
    Si vous ne pouvez pas vous connecter avec ce compte et ce mot de passe, essayez de copier le mot de passe à partir du code source, puis de le coller ici. Si vous ne pouvez toujours pas vous connecter, vérifiez la colonne **UserName** de la table **AspNetUsers** pour être sûr que *utilisateur1@contoso.com* a été ajouté.

8.  Vérifiez que vous pouvez effectuer des modifications.

<h2><a name="bkmk_deploytowindowsazure11"></a>Déploiement de l'application dans Azure</h2>


1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.
    
    ![Publier dans le menu contextuel du
    projet](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png)
    
    L'Assistant **Publier le site Web** s'ouvre.

2.  Cliquez sur l'onglet **Paramètres** sur le côté gauche de la boîte de dialogue **Publier le site Web**. Cliquez sur l'icône **v** pour sélectionner la **Chaîne de connexion distante** pour **ApplicationDbContext**, puis 	sélectionnez **ContactDB**.
    
    ![paramètres](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

3.  Sous **ContactManagerContext**, sélectionnez **Execute Code First Migrations**.
    
    ![paramètres](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

4.  Cliquez sur **Publish**.

5.  Connectez-vous en tant que *utilisateur1@contoso.com* et vérifiez que vous pouvez modifier les données.

6.  Déconnectez-vous.

7.  Connectez-vous en utilisant votre compte Google ou Facebook. Cela ajoutera votre compte Google ou Facebook au rôle **peutModifier**.

### Arrêt du site Web pour empêcher d'autres personnes de s'inscrire

1.  Dans l'**Explorateur de serveurs**, accédez à **Sites Web**. 2.  Cliquez avec le bouton droit sur chaque instance de site Web, puis
    sélectionnez **Arrêter le site Web**.
    
    ![arrêter le site
    Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png)
    
    Par ailleurs, à partir du portail de gestion Azure, vous pouvez sélectionner le site web, puis cliquez sur l'icône **arrêter** en bas de la page.
    
    ![arrêter le site
    Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Suppression de AddToRoleAsync, Publish et Test

1.  Commentez ou supprimez le code suivant à partir de la méthode **ExternalLoginConfirmation** dans le contrôleur Compte : `await UserManager.AddToRoleAsync(user.Id, "peutModifier");`
2.  Développez le projet (ce qui enregistre les modifications du fichier et recherche d'éventuelles erreurs de compilation).
3.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.
    
        ![Publier dans le menu contextuel du projet](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)

4.  Cliquez sur le bouton **Démarrer l'aperçu**. Seuls les fichiers à mettre à jour sont déployés.
5.  Démarrez le site Web à partir de Visual Studio ou du portail. **Vous ne pouvez pas publier pendant l'arrêt du site Web**.
    
    ![démarrer le site
    Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

6.  Revenez à Visual Studio, puis cliquez sur **Publier**. 
7.  Votre application Azure ouvre votre navigateur par défaut. Si vous êtes connecté, déconnectez-vous pour pouvoir afficher la page d'accueil en tant qu'utilisateur 	anonyme.
8.  Cliquez sur le lien **À propos de**. Vous êtes redirigé vers la page de connexion.
9.  Cliquez sur le lien **S'inscrire** sur la page de connexion, puis créez un compte local. Nous utiliserons ce compte local pour vérifier que vous pouvez bien accéder aux pages en lecture seule, mais pas aux pages qui permettent 	de modifier des données (qui sont protégées par le rôle *peutModifier*). Plus loin dans ce didacticiel, nous allons supprimer les accès au compte local.
    
    ![S’inscrire](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

10. Vérifiez que vous pouvez accéder aux pages *À propos de* et *Contact*.
    
    ![Se
    déconnecter](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

11. Cliquez sur le lien **Démo de GC** pour accéder au contrôleur **Cm**. Vous pouvez également ajouter *Cm* à l'URL.
    
    ![Page
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)

12. Cliquez sur un lien Modifier. Vous êtes redirigé vers la page de connexion. Sous **Use another service to log in**, cliquez sur Google ou Facebook, puis connectez-vous avec le compte que vous avez inscrit. Si vous travaillez 	rapidement et que le cookie de votre session n'est pas expiré, vous serez automatiquement connecté avec le compte Google ou Facebook que vous avez utilisé auparavant.
13. Vérifiez que vous pouvez modifier les données en étant connecté avec ce compte. **Remarque :** vous ne pouvez pas vous déconnecter de Google à partir de cette application pour ensuite vous connecter à
    un autre compte Google avec le même navigateur. Si vous utilisez un seul navigateur, vous devez accéder à Google et vous déconnecter. Vous pouvez vous connecter avec un autre compte à partir du même authentificateur tiers (tel que Google) en utilisant un autre navigateur.

## Examen de la base de données SQL Azure

1.  Dans l'**Explorateur de serveurs**, accédez à la base de données **ContactDB**
2.  Cliquez avec le bouton droit sur **ContactDB** puis sélectionnez **Open in SQL Server Object Explorer**.
    
    ![ouvrir dans
    SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)

**Remarque :** si vous ne pouvez pas développer de **bases de données SQL** et que *vous ne pouvez pas* afficher la base de données **ContactDB** dans Visual Studio, suivez les instructions ci-dessous pour ouvrir un ou plusieurs ports de pare-feu. Suivez les instructions sous **Configuration des règles de pare-feu Azure**. Vous devrez peut-être attendre quelques minutes avant d'accéder à la base de données après avoir ajouté la règle de pare-feu.

1.  Cliquez avec le bouton droit sur la table **AspNetUsers**, puis sélectionnez **Afficher les données**.
    
    ![Page
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)

2.  Notez l'ID du compte Google utilisé pour votre inscription pour lui attribuer le rôle **peutModifier**, ainsi que l'ID *utilisateur1@contoso.com*. Ces ID doivent être les seuls à avoir le rôle **peutModifier**. Nous allons 	vérifier cela à l'étape suivante.
    
    ![Page
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)

3.  Dans **SQL Server Object Explorer**, cliquez avec le bouton droit sur **AspNetUserRoles**, puis sélectionnez **Afficher les données**.
    
    ![Page
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

Vérifiez que les **UserId** sont ceux de *utilisateur1@contoso.com* et du compte Google de votre inscription.

## Configuration des règles de pare-feu Azure

Suivez la procédure de cette section si vous ne pouvez pas vous connecter à SQL Azure à partir de Visual Studio ou si vous obtenez une boîte de dialogue indiquant << Impossible d'ouvrir le serveur >>.

![erreur de
pare-feu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

Vous devez ajouter votre adresse IP aux adresses IP autorisées.

1.  Dans le portail Azure, sélectionnez **Bases de données SQL** dans l'onglet gauche.
    
    ![Sélectionner
    SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png)

2.  Cliquez sur la **ContactDB**.

3.  Cliquez sur le lien **Set up Azure firewall rules for this IP address**.
    
    ![règles de
    pare-feu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png)

4.  Lorsque l'invite << The current IP address xxx.xxx.xxx.xxx is not included in existing firewall rules. Do you want to update the firewall rules? >> s'affiche, cliquez sur **Oui**. Avec certains pare-feu d'entreprise, l'ajout de 	cette adresse ne suffit pas. Vous devez plutôt ajouter une plage d'adresses IP.

La prochaine étape consiste à ajouter une plage d'adresses IP autorisées.

1.  Dans le portail Azure, cliquez sur **Bases de données SQL**. 2.  Sélectionnez l'onglet **Serveurs**, puis cliquez sur le serveur à configurer.
    
    ![Onglet Serveurs dans Azure
    ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

3.  Cliquez sur l'onglet **Configurer**.

4.  Ajoutez un nom de règle, ainsi que le début et la fin de la plage d'adresses IP.
    
    ![plage d’adresses
    IP](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png)

5.  En bas de la page, cliquez sur **Enregistrer**. 

6.  Le cas échéant, n'hésitez pas à envoyer un commentaire pour nous faire savoir si vous avez dû ajouter une plage d'adresses IP pour vous connecter.

Finalement, vous pouvez vous connecter à l'instance de base de données SQL à partir de SQL Server Object Explorer (SSOX).

1.  Depuis le menu Affichage, cliquez sur **SQL Server Object Explorer**.
2.  Cliquez avec le bouton droit sur **SQL Server**, puis sélectionnez **Ajouter SQL Server**.
3.  Dans la boîte de dialogue **Se connecter au serveur**, définissez l'**authentification** sur **Authentification SQL Server**. Vous obtiendrez le **Nom du serveur** et le **Nom de connexion** à partir du portail Azure.
4.  Dans votre navigateur, accédez au portail, puis sélectionnez **Bases de données SQL**.
5.  Sélectionnez la base de données **ContactDB**, puis cliquez sur **View SQL Database connection strings**.
6.  Sur la page **Connection Strings**, copiez le **Serveur** et l'**ID utilisateur**.
    
    ![chaîne de
    connexion](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)

7.  Collez les valeurs **Serveur** et **ID utilisateur** dans la boîte de dialogue de Visual Studio, **Se connecter au serveur**. La valeur **ID utilisateur** va dans le champ **Connexion**. Entrez le mot de passe utilisé pour la 	création de la base de données SQL.
    
    ![Boîte de dialogue Se connecter au
    serveur](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

Vous pouvez maintenant accéder à la base de données de contacts en utilisant les instructions précédentes.

## Ajout d'un utilisateur au rôle << peutModifier >> en modifiant les tables de base de données

Précédemment dans ce didacticiel, vous avez utilisé du code pour ajouter des utilisateurs au rôle << peutModifier >>. Il existe une autre méthode consistant à manipuler directement les données des tables d'appartenance. La procédure suivante indique comment utiliser cette méthode pour ajouter un utilisateur à un rôle.

1.  Dans **SQL Server Object Explorer**, cliquez avec le bouton droit sur **AspNetUserRoles**, puis sélectionnez **Afficher les données**.
    
    ![Page
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

2.  Copiez la valeur *RoleId* et collez-la dans la (nouvelle) ligne vide.
    
    ![Page
    CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)

3.  Dans la table **AspNetUsers**, recherchez l'utilisateur auquel vous voulez attribuer le rôle, puis copiez l'*ID* de cet utilisateur et collez-le dans la colonne **UserId** de la table **AspNetUserRoles**.

Nous développons actuellement un outil qui facilitera la gestion des utilisateurs et des rôles.

## Considérations relatives à l'inscription locale

La réinitialisation de mot de passe n'est pas prise en charge pour l'inscription à l'appartenance ASP.NET. De même, ce type d'inscription n'implique pas la vérification de l'identité d'un utilisateur (par exemple, via un [CAPTCHA][14]). Une fois qu'un utilisateur est authentifié en utilisant l'un des fournisseurs tiers, il peut s'inscrire. Si vous voulez désactiver l'inscription locale, procédez comme suit :

1.  Dans AccountController, supprimez l'attribut *[AllowAnonymous]* des méthodes d'*inscription* GET et POST. Ceci empêchera les robots et les utilisateurs anonymes de s'inscrire.
2.  Dans le fichier *\_LoginPartial.cshtml* du dossier *Views\\Shared*, supprimez le lien d'action S'inscrire.
3.  Dans le fichier *Views\\Account\\Login.cshtml*, supprimez le lien d'action S'inscrire.
4.  Déployez l'application.

<h2><a name="nextsteps"></a><span  class="short-header">Étapes suivantes</span>Étapes suivantes</h2>


Pour obtenir des instructions supplémentaires sur l'ajout de données de profil à la base de données d'inscription d'utilisateur, ainsi que sur l'utilisation de Facebook comme fournisseur d'authentification, consultez le didacticiel [Création d'une application ASP.NET MVC 5 avec une authentification OAuth2 et OpenID pour Facebook et Google][8].

Pour activer les boutons de réseaux sociaux affichés en haut de ce didacticiel, consultez [Boutons de réseaux sociaux pour ASP.NET MVC 5][15].

Pour plus d'informations sur ASP.NET MVC, consultez le didacticiel [Prise en main de ASP.NET MVC 5][16]. L'excellent didacticiel de Tom Dykstra, [Prise en main d'EF et MVC][17] vous présentera des fonctions de programmation MVC et EF plus avancées.

Ce didacticiel et son exemple d'application ont été écrits par [Rick Anderson][18] (Twitter [@RickAndMSFT][1]) avec l'aide de Tom Dykstra et Barry Dorrans (Twitter [@blowdart][19]).

N'hésitez pas à nous transmettre vos commentaires sur ce qui vous a plu et ce qui pourrait être amélioré... pas seulement à propos de ce didacticiel, mais aussi en ce qui concerne les produits présentés ici. Vos commentaires nous aideront à orienter nos améliorations. Vous pouvez aussi demander de nouvelles rubriques et noter les rubriques existantes sur [Leçons de code][20].

<!-- bookmarks -->

<!-- images-->



[1]: https://twitter.com/RickAndMSFT
[2]: http://oauth.net/ "http://oauth.net/"
[3]: http://openid.net/
[4]: http://go.microsoft.com/fwlink/?LinkId=390521
[5]: https://manage.windowsazure.com
[6]: http://msdn.microsoft.com/library/hh770484.aspx
[7]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
[8]: http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
[9]: http://curah.microsoft.com/55636/aspnet-identity
[10]: http://msdn.microsoft.com/en-us/library/ms228053.aspx
[11]: http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute.aspx
[12]: http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx
[13]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
[14]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
[15]: http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/
[16]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
[17]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[18]: http://blogs.msdn.com/b/rickandy/
[19]: https://twitter.com/blowdart
[20]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code