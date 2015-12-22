<properties 
	pageTitle="Créer une application ASP.NET MVC avec authentification et base de données SQL et la déployer dans Azure App Service" 
	description="Découvrez comment développer une application ASP.NET MVC 5 avec une base de données SQL principale, ajouter l’authentification et l’autorisation et la déployer dans Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="get-started-article" 
	ms.date="12/07/2015" 
	ms.author="riande"/>

# Créer une application ASP.NET MVC avec authentification et base de données SQL et la déployer dans Azure App Service

Ce didacticiel vous montre comment générer une application Web ASP.NET MVC 5 sécurisée qui permet aux utilisateurs de se connecter avec des informations d’identification Facebook ou Google. L’application est une liste de contacts utilisant Entity Framework ADO.NET pour accéder à la base de données. Vous allez déployer cette application dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

À la fin du didacticiel, vous disposerez d’une application web pilotée par les données sécurisée et fonctionnelle dans le cloud et utilisant une base de données du cloud. L’illustration suivante montre la page de connexion une fois l’application terminée :

![page de connexion][rxb]

Vous apprendrez ce qui suit :

* Créer un projet web ASP.NET MVC 5 sécurisé dans Visual Studio
* Authentifier et autoriser des utilisateurs qui se connectent avec leurs informations d’identification à partir de leurs comptes Google ou Facebook (authentification de fournisseur de réseaux sociaux à l’aide de [OAuth 2.0](http://oauth.net/2 "http://oauth.net/2")).
* Authentifier et autoriser des utilisateurs qui s’inscrivent dans une base de données gérée par l’application (authentification locale utilisant [Identité ASP.NET](http://asp.net/identity/)).
* Utiliser le code d’ADO.NET Entity Framework 6 avant de lire et d’écrire des données dans une base de données SQL.
* Utiliser Migrations Code First d’Entity Framework avant de déployer une base de données.
* Stocker des données relationnelles dans le cloud à l’aide d’une base de données SQL Azure.
* Déployer un projet web qui utilise une base de données associée à une [application web](http://go.microsoft.com/fwlink/?LinkId=529714) dans Azure App Service.

>[AZURE.NOTE]Ce didacticiel est long. Pour obtenir une présentation rapide des projets web Azure App Service et Visual Studio, consultez [Création d’une application web ASP.NET dans Azure App Service](web-sites-dotnet-get-started.md). Pour des informations relatives à la résolution des problèmes, consultez la section [Résolution des problèmes](#troubleshooting).
>
>Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Configuration requise

Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous ne possédez pas de compte, vous pouvez [activer les avantages de votre abonnement Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [obtenir un essai gratuit](/pricing/free-trial/?WT.mc_id=A261C142F).

Pour configurer votre environnement de développement, vous devez installer [Visual Studio 2013 Update 5](http://go.microsoft.com/fwlink/?LinkId=390521) ou une version ultérieure, ainsi que la dernière version du [Kit de développement logiciel Azure pour .NET](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Cet article a été écrit pour Visual Studio Update 4 et le kit de développement logiciel 2.8.1. Les mêmes instructions s’appliquent à Visual Studio 2015 avec la dernière version du [Kit de développement logiciel (SDK) Azure pour .NET](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) installée, mais la présentation de certains écrans peut différer des illustrations.

## Création d'une application ASP.NET MVC 5

### Création du projet

1. Dans le menu **Fichier**, cliquez sur **Nouveau projet**.

	![Nouveau projet dans le menu Fichier](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)

1. Dans la boîte de dialogue **Nouveau projet**, développez **C#**, puis sélectionnez **Web** sous **Modèles installés**, puis sélectionnez **Application Web ASP.NET**.

1. Nommez l’application **ContactManager**, puis cliquez sur **OK**.

	![Boîte de dialogue Nouveau projet](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
 
	**Remarque :** veillez à entrer « ContactManager ». Les blocs de code que vous allez copier plus tard partent du principe que le nom du projet est GestionnaireContacts.

1. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle **MVC**. Vérifiez que l’option **Authentification** est définie sur **Comptes d’utilisateur individuels**, que l’option **Héberger dans le cloud** est cochée et que **App Service** est sélectionné.

	![Boîte de dialogue New ASP.NET Project](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)

1. Cliquez sur **OK**.

3. Lorsque la boîte de dialogue **Configurer les paramètres Microsoft Azure Web App** apparaît, vérifiez que vous êtes connecté à Azure. Connectez-vous si ce n’est pas le cas, ou entrez de nouveau vos informations d’identification si votre session a expiré.

	![Entrez à nouveau les informations d’identification](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/reentercredentials.png)

2. Si vous souhaitez spécifier un nom pour votre application web, modifiez la valeur dans la zone **Nom de l’application web**.

	Ce nom doit être unique dans le domaine azurewebsites.net, car l’URL de l’application web sera {nom}.azurewebsites.net. L’Assistant Configuration suggère un nom unique en ajoutant un numéro au nom du projet « Gestionnairecontacts ». Nous allons l’utiliser pour ce didacticiel.

5. Dans la liste déroulante **Plan App Service**, sélectionnez **Créer un plan App Service** et entrez un nom, par exemple « StandardWeb », comme illustré ci-dessous.

	Si vous préférez, vous pouvez sélectionner un plan App Service que vous avez déjà. Pour plus d’informations sur les plans App Service, consultez la rubrique [Présentation détaillée des plans d’Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Dans la liste déroulante **Groupe de ressources**, sélectionnez **Créer un groupe de ressources** et entrez un nom, par exemple « ExampleMVC », comme illustré ci-dessous.

	Si vous préférez, vous pouvez sélectionner un groupe de ressources que vous avez déjà. Cependant, si vous créez un groupe de ressources et que vous l’utilisez uniquement dans le cadre de ce didacticiel, vous pourrez supprimer facilement toutes les ressources Azure créées aux fins du didacticiel lorsque vous n’en aurez plus besoin. Pour plus d’informations sur les groupes de ressources, consultez la page [Présentation d’Azure Resource Manager](../resource-group-overview.md).

7. Sélectionnez une région proche de chez vous.

	Ne cliquez pas tout de suite sur **OK**. Dans l’étape suivante, vous allez configurer la ressource de base de données. La boîte de dialogue doit maintenant se présenter comme suit.

	![Nouveau plan et groupe de ressources](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newplanandgroup.png)
 
2. Sélectionnez **Créer un serveur**, entrez un nom de serveur, un nom d’utilisateur et un mot de passe.

	Le nom de serveur doit être unique. Il peut contenir des lettres minuscules, des chiffres et des traits d’union. Il ne peut pas se terminer par un trait d’union. Le nom d’utilisateur et le mot de passe sont les nouvelles informations d’identification que vous créez pour le nouveau serveur.

	Si vous avez déjà un serveur de base de données, vous pouvez le sélectionner au lieu d’en créer un. Les serveurs de base de données sont une ressource essentielle, et vous souhaitez généralement créer plusieurs bases de données sur le même serveur à des fins de test et développement au lieu de créer un serveur de base de données par base de données. Toutefois, pour ce didacticiel, vous n’avez besoin du serveur que temporairement. En créant le serveur dans le même groupe de ressources que le site web, vous pourrez supprimer facilement l’application web et les ressources de base de données en supprimant le groupe de ressources lorsque vous aurez terminé ce didacticiel.

	Si vous sélectionnez un serveur de base de données existant, veillez à ce que votre application web et la base de données se trouvent dans la même région.

	![Utiliser la nouvelle base de données](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

4. Cliquez sur **OK**.

	Visual Studio crée le projet web GestionnaireContacts, crée le groupe de ressources et le plan App Service que vous avez spécifié, et crée une application web dans Azure App Service avec le nom spécifié.

### Définition de l'en-tête et du pied de page de la page

1. Dans l’**Explorateur de solutions**, ouvrez le fichier *Layout.cshtml* dans le dossier *Views\\Shared*.

	![\_Layout.cshtml in Solution Explorer][newapp004]

1. Remplacez le contenu du fichier *Layout.cshtml* par le code suivant.

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title - Contact Manager</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		
		</head>
		<body>
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                @Html.ActionLink("CM Demo", "Index", "Cm", new { area = "" }, new { @class = "navbar-brand" })
		            </div>
		            <div class="navbar-collapse collapse">
		                <ul class="nav navbar-nav">
		                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
		                    <li>@Html.ActionLink("About", "About", "Home")</li>
		                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
		                </ul>
		                @Html.Partial("_LoginPartial")
		            </div>
		        </div>
		    </div>
		    <div class="container body-content">
		        @RenderBody()
		        <hr />
		        <footer>
		            <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		        </footer>
		    </div>
		
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
		</body>
		</html>

	Ce code remplace le nom de l’application dans l’en-tête et le pied de page « Mon application ASP.NET » et « Nom de l’application » par « Gestionnaire de contacts » et « Démonstration GC ».
 
### Exécution locale de l'application

1. Appuyez sur Ctrl+F5 pour exécuter l'application.

	La page d’accueil de l’application apparaît dans le navigateur par défaut.

	![Application web exécutée en local](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

Voilà, vous avez fait tout ce qu'il fallait pour créer l'application que vous allez déployer dans Azure.

## Déploiement de l’application dans Azure

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.

	![Publier dans le menu contextuel du projet](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
	L'Assistant **Publier le site Web** s'ouvre.

1. Dans la boîte de dialogue **Publier le site Web**, cliquez sur **Publier**.

	![Publier](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

	L'application créée est maintenant exécutée dans le cloud. Au prochain déploiement de l’application, seuls les fichiers modifiés (ou nouveaux) seront déployés.

	![Exécution dans le cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## Activation du protocole SSL pour le projet ##

1. Dans l’**Explorateur de solutions**, cliquez sur le projet **ContactManager**, puis appuyez sur F4 pour ouvrir la fenêtre **Propriétés**.

3. Définissez **SSL activé** sur **True**.

4. Copiez l’**URL SSL**.

	L’URL SSL est https://localhost:44300/ sauf si vous avez déjà créé des applications Web SSL.

	![activer SSL][rxSSL]
 
1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Contact Manager**, puis sur **Propriétés**.

1. Cliquez sur l’onglet **Web**.

1. Modifiez l’**URL du projet** pour utiliser l’**URL SSL**, puis enregistrez la page (Ctrl+S).

	![activer SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. Assurez-vous que Microsoft Internet Explorer est le navigateur par défaut pour Visual Studio, comme indiqué sur l’image ci-dessous :

	![navigateur par défaut](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

	Le sélecteur de navigateur vous permet d’indiquer le navigateur utilisé par Visual Studio. Vous pouvez sélectionner plusieurs navigateurs et faire en sorte que Visual Studio mette à jour chaque navigateur lorsque vous apportez des modifications. Pour plus d’informations, consultez [Utilisation du lien de navigateur dans Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link) (en anglais).

 	![sélecteur de navigateur](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

1. Appuyez sur Ctrl+F5 pour exécuter l’application. Cliquez sur **Oui** pour démarrer le processus d’approbation du certificat auto-signé généré par IIS Express.

	 ![instructions pour approuver le certificat auto-signé généré par IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. Lisez le contenu de la boîte de dialogue **Avertissement de sécurité**, puis cliquez sur **Oui** si vous souhaitez installer le certificat représentant **l’hôte local (localhost)**.

 	![Avertissement relatif que certificat localhost IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. IE affiche la *page d’accueil* sans avertissement SSL.

	 ![IE avec SSL localhost sans avertissement](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

	 Internet Explorer est l’option recommandée lorsque vous utilisez SSL, car il accepte le certificat et affiche le contenu HTTPS sans émettre d’avertissement. Microsoft Edge et Google Chrome acceptent également le certificat. Firefox utilise son propre magasin de certificats. Il affiche donc un avertissement.

	 ![Avertissement de certificat Firefox](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## Ajout d'une base de données à l'application

À présent, vous allez mettre à jour l’application pour y ajouter la capacité à afficher et à mettre à jour les contacts, puis stocker les données dans une base de données. L’application va utiliser Entity Framework (EF) pour créer la base de données et pour lire et mettre à jour les données.

### Ajout de classes de modèle de données pour les contacts

Commencez par créer un modèle de données simple dans le code.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier Modèles, cliquez sur **Ajouter**, puis sur **Classe**.

	![Menu contextuel Ajouter une classe aux modèles](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)

2. Dans la boîte de dialogue **Ajouter un nouvel élément**, nommez le nouveau fichier de classe *Contact.cs*, puis cliquez sur **Ajouter**.

	![Boîte de dialogue Ajouter un nouvel élément][adddb002]

3. Remplacez le contenu du fichier Contact.cs par le code suivant.

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
La classe **Contact** définit les données que vous allez stocker pour chaque contact, ainsi que la clé primaire *ContactID* dont la base de données a besoin.

### Création de pages Web permettant aux utilisateurs de l’application d’utiliser des contacts

La fonctionnalité de génération de modèles automatique ASP.NET MVC peut générer automatiquement un code qui effectue les actions CRUD (Create, Read, Update et Delete, ou Créer, Lire, Mettre à jour et Supprimer).

1. Développez le projet **(Ctrl+Maj+B)** (Vous devez développer le projet avant d’utiliser le mécanisme de génération de modèle automatique.)
 
1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier Contrôleurs, puis cliquez sur **Ajouter** et sur **Contrôleur**.

	![Ajouter un contrôleur dans le menu contextuel du dossier Contrôleurs][addcode001]

5. Dans la boîte de dialogue **Add Scaffold**, sélectionnez **MVC 5 Controller with views, using EF**, puis cliquez sur **Ajouter**.
	
	![Boîte de dialogue Add Scaffold](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)

1. Dans la zone déroulante **Classe de modèle**, sélectionnez **Contact (GestionnaireContacts.Models)**. (voir l’image ci-dessous).

1. Dans la **classe du contexte des données**, sélectionnez **ApplicationDbContext (GestionnaireContacts.Models)**. **ApplicationDbContext** est utilisé pour la base de données d’appartenance et nos données de contact.

1. Dans la zone de texte **Controller name**, entrez « CmController » pour le nom du contrôleur.

	![Boîte de dialogue New data ctx](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. Cliquez sur **Add**.

   Visual Studio crée un contrôleur avec des méthodes et des vues pour les opérations de base de données CRUD des objets **Contact**.

## Activation des migrations, création de la base de données, ajout d'exemples de données et d'un initialiseur de données ##

L’étape suivante consiste à activer la fonctionnalité [Migrations Code First](http://msdn.microsoft.com/library/hh770484.aspx) pour créer des tables de base de données en fonction du modèle de données que vous avez créé.

1. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**, puis **Console du Gestionnaire de package**.

	![Console du Gestionnaire de package dans le menu Outils](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :

		enable-migrations

	La commande **enable-migrations** crée un dossier *Migrations*, dans lequel elle place un fichier *Configuration.cs* que vous pouvez modifier pour amorcer la base de données et configurer les migrations.

2. Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :

		add-migration Initial


	La commande **add-migration Initial** génère un fichier nommé **&lt;date\_stamp&gt;Initial** dans le dossier *Migrations*. Le code de ce fichier crée les tables de la base de données. Le premier paramètre (**Initial**) permet de créer le nom du fichier. Les nouveaux fichiers de classe sont affichés dans l'**Explorateur de solutions**.

	Dans la classe **Initial**, la méthode **Up** crée la table des contacts et la méthode **Down** (utilisée lorsque vous voulez revenir à l'état précédent) annule cette table.

3. Ouvrez le fichier *Migrations\\Configuration.cs*.

4. Ajoutez l’instruction `using` suivante :

    	 using ContactManager.Models;

5. Remplacez la méthode *Seed* par le code suivant :

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
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

	Ce code initialise (amorce) la base de données avec les informations de contact. Pour plus d’informations sur l’amorçage de la base de données, consultez la page [Amorçage et débogage des bases de données Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).

6. Dans la **Console du Gestionnaire de package**, entrez la commande suivante :

		update-database

	![Commandes de la Console du Gestionnaire de package][addcode009]

	La commande **update-database** exécute la première migration qui entraîne la création de la base de données. Par défaut, la base de données est créée en tant que base de données SQL Server Express LocalDB.

7. Appuyez sur Ctrl+F5 pour exécuter l’application, puis cliquez sur le lien **CM Demo** ou accédez à https://localhost:(port#)/Cm.

	L'application affiche les données amorcées, ainsi que des liens pour les modifier, les supprimer ou obtenir des informations supplémentaires. Vous pouvez créer, modifier, supprimer et afficher les données.

	![Affichage MVC des données][rx2]

## Ajout d'un fournisseur OAuth2

>[AZURE.NOTE]Pour des instructions détaillées sur la façon d’utiliser les portails de développeurs Google et Facebook, ce didacticiel renvoie à des didacticiels sur le site ASP.NET. Toutefois, Google et Facebook modifient leurs sites plus fréquemment que ces didacticiels sont mis à jour, et ils sont désormais obsolètes. Si vous rencontrez un problème de suivi des instructions, consultez le commentaire Disqus proposé à la fin de ce didacticiel pour obtenir la liste de ce qui a changé.

[OAuth](http://oauth.net/ "http://oauth.net/") est un protocole ouvert permettant de mettre en place une authentification sécurisée dans une méthode simple et standardisée à partir d’applications Web, mobiles ou de bureau. Le modèle Internet d'ASP.NET MVC utilise OAuth pour exposer Facebook, Twitter, Google et Microsoft comme fournisseurs d'authentification. Même si ce didacticiel utilise uniquement Google comme fournisseur d'authentification, vous pouvez facilement modifier le code pour utiliser n'importe quel autre fournisseur. La procédure à suivre pour implémenter d’autres fournisseurs ressemble assez à celle présentée dans ce didacticiel. Pour utiliser Facebook comme fournisseur d’authentification, consultez le didacticiel [Application MVC 5 avec Facebook, Twitter, LinkedIn et authentification Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) (en anglais).

En plus de l’authentification, ce didacticiel utilise également des rôles pour l’implémentation d’autorisations. Seuls les utilisateurs ajoutés au rôle *canEdit* peuvent modifier les données (et donc créer, modifier ou supprimer des contacts).

1. Suivez les instructions du didacticiel [Application MVC 5 avec Facebook, Twitter, LinkedIn et authentification Google OAuth2 ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) (en anglais) sous **Création d’une application Google pour OAuth2 afin de configurer une application Google pour OAuth2**.

3. Exécutez et testez l’application pour vérifier que vous pouvez vous connecter à l’aide de l’authentification Google.

2. Pour créer des boutons de réseaux sociaux avec icônes fournisseur, consultez [Boutons de réseaux sociaux pour ASP.NET MVC 5](http://www.jerriepelser.com/blog/pretty-social-login-buttons-for-asp-net-mvc-5).

## Utilisation de l’API d’appartenance

Dans cette section, vous allez ajouter un utilisateur local, ainsi que le rôle *peutModifier* à la base de données d’appartenance. Seuls les utilisateurs du rôle *peutModifier* pourront modifier les données. Il est recommandé de nommer les rôles en fonction des actions qu’ils peuvent effectuer, c’est pourquoi *peutModifier* est un meilleur nom que *admin*. Lorsque votre application évolue, vous pouvez ajouter de nouveaux rôles tels que *canDeleteMembers*, plus parlant que le nom *superAdmin*.

1. Ouvrez le fichier *migrations\\configuration.cs* et ajoutez les instructions `using` suivantes :

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Ajoutez la méthode **AddUserAndRole** suivante à la classe :

		bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
		{
		    IdentityResult ir;
		    var rm = new RoleManager<IdentityRole>
		        (new RoleStore<IdentityRole>(context));
		    ir = rm.Create(new IdentityRole("canEdit"));
		    var um = new UserManager<ApplicationUser>(
		        new UserStore<ApplicationUser>(context));
		    var user = new ApplicationUser()
		    {
		        UserName = "user1@contoso.com",
		    };
		    ir = um.Create(user, "P_assw0rd1");
		    if (ir.Succeeded == false)
		        return ir.Succeeded;
		    ir = um.AddToRole(user.Id, "canEdit");
		    return ir.Succeeded;
		}

1. Appelez la nouvelle méthode à partir de la méthode **Seed** :

		protected override void Seed(ContactManager.Models.ApplicationDbContext context)
		{
		    AddUserAndRole(context);
		    context.Contacts.AddOrUpdate(p => p.Name,
		        // Code removed for brevity
		}

	Les images suivantes indiquent les modifications apportées à la méthode *Seed* :

	![image du code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

	Ce code crée un rôle nommé *canEdit*, créé un utilisateur local nommé **user1@contoso.com* et ajoute **user1@contoso.com* au rôle *canEdit*. Pour plus d’informations, consultez les [didacticiels Identité ASP.NET](http://www.asp.net/identity/overview/features-api) sur le site ASP.NET.

## Utilisation de code temporaire pour ajouter de nouveaux utilisateurs de réseaux sociaux au rôle peutModifier  ##

Dans cette section, vous allez modifier temporairement la méthode **ExternalLoginConfirmation** dans le contrôleur Account afin d’ajouter des utilisateurs se connectant avec un fournisseur OAuth au rôle *canEdit*. Nous espérons fournir un outil similaire à [WSAT](http://msdn.microsoft.com/library/ms228053.aspx) à l’avenir pour vous permettre de créer et de modifier les comptes et les rôles des utilisateurs. En attendant, vous pouvez effectuer les mêmes tâches à l’aide d’un code temporaire.

1. Ouvrez le fichier **Controllers\\AccountController.cs** et accédez à la méthode **ExternalLoginConfirmation**.

1. Ajoutez l’appel suivant vers **AddToRoleAsync** juste avant l’appel **SignInAsync**.

		await UserManager.AddToRoleAsync(user.Id, "canEdit");

   Le code ci-dessus ajoute les utilisateurs récemment enregistrés au rôle « peutModifier », qui leur permet d’accéder aux méthodes d’action pour modifier les données. L’extrait suivant montre la nouvelle ligne de code en contexte.

		  // POST: /Account/ExternalLoginConfirmation
		  [HttpPost]
		  [AllowAnonymous]
		  [ValidateAntiForgeryToken]
		  public async Task ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl)
		  {
		     if (User.Identity.IsAuthenticated)
		     {
		        return RedirectToAction("Index", "Manage");
		     }
		     if (ModelState.IsValid)
		     {
		        // Get the information about the user from the external login provider
		        var info = await AuthenticationManager.GetExternalLoginInfoAsync();
		        if (info == null)
		        {
		           return View("ExternalLoginFailure");
		        }
		        var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
		        var result = await UserManager.CreateAsync(user);
		        if (result.Succeeded)
		        {
		           result = await UserManager.AddLoginAsync(user.Id, info.Login);
		           if (result.Succeeded)
		           {
		              await UserManager.AddToRoleAsync(user.Id, "canEdit");
		              await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false);
		              return RedirectToLocal(returnUrl);
		           }
		        }
		        AddErrors(result);
		     }
		     ViewBag.ReturnUrl = returnUrl;
		     return View(model);
		  }

Plus loin dans ce didacticiel, vous allez déployer l’application sur Azure, en vous connectant avec Google ou un autre fournisseur d’authentification tiers. Ceci vous permettra d’ajouter votre compte récemment inscrit au rôle *peutModifier*. Les utilisateurs trouvant l’URL de votre application web et possédant un ID Google peuvent ensuite s’inscrire et mettre à jour votre base de données. Pour empêcher que cela se produise, vous pouvez arrêter le site. Vous pouvez vérifier quels utilisateurs sont rattachés au rôle *peutModifier* en examinant la base de données.

Dans la **Console du Gestionnaire de package**, actionnez la touche Haut pour afficher la commande suivante :

		Update-Database

La commande **Update-Database** exécute la méthode **Seed**, ce qui a pour effet d’exécuter la méthode **AddUserAndRole** ajoutée précédemment. La méthode **AddUserAndRole** crée l’utilisateur **user1@contoso.com* et l’ajoute au rôle *canEdit*.

## Protection de l’application à l’aide du protocole SSL et de l’attribut Authorize ##

Dans cette section, vous allez appliquer l’attribut [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) pour limiter l’accès aux méthodes d’action. Les utilisateurs anonymes pourront uniquement afficher la méthode d’action **Index** du contrôleur d’accueil. Les utilisateurs inscrits pourront afficher les données de contact (pages **Index** et **Détails** du contrôleur Cm), les pages « À propos de » et « Contact ». Seuls les utilisateurs du rôle *peutModifier* pourront accéder aux méthodes d’action qui modifient les données.

1. Ouvrez le fichier *App\_Start\\FilterConfig.cs* et remplacez la méthode *RegisterGlobalFilters* par le code suivant (qui ajoute les deux filtres) :

		public static void
		RegisterGlobalFilters(GlobalFilterCollection filters)
		{
		    filters.Add(new HandleErrorAttribute());
		    filters.Add(new System.Web.Mvc.AuthorizeAttribute());
		    filters.Add(new RequireHttpsAttribute());
		}
		
	Ce code ajoute les filtres [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) et [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) à l’application. Le filtre [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) empêche les utilisateurs anonymes d’accéder aux méthodes de l’application. Vous allez utiliser l’attribut [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) pour désactiver les contraintes d’autorisation dans quelques méthodes, pour que les utilisateurs anonymes puissent se connecter et afficher la page d’accueil. [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) nécessite que tous les accès à l’application web se fassent via HTTPS.

	Une autre approche consiste à ajouter les attributs [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) et [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) à chaque contrôleur, mais il est recommandé de les appliquer à l’ensemble de l’application. En les ajoutant de manière globale, les nouveaux contrôleurs et les nouvelles méthodes d’action que vous ajouterez seront automatiquement protégés.Vous n’aurez pas à vous rappeler qu’il faut leur appliquer ces attributs à chaque fois. Pour plus d’informations, consultez la page [Sécurisation de votre application ASP.NET MVC et nouvel attribut AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx).

1. Ajoutez l’attribut [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) à la méthode **Index** du contrôleur d’accueil. L’attribut [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) vous permet de créer une liste des méthodes pour lesquelles vous voulez désactiver l’autorisation.

		public class HomeController : Controller
		{
		  [AllowAnonymous]
		  public ActionResult Index()
		  {
		     return View();
		  }

	Si vous effectuez une recherche globale pour *AllowAnonymous*, vous pouvez voir qu’il est utilisé dans les méthodes de connexion et d’inscription du contrôleur Compte.

1. Dans *CmController.cs*, ajoutez `[Authorize(Roles = "canEdit")]` aux méthodes HttpGet et HttpPost qui modifient les données (Create, Edit, Delete, toutes les méthodes d’action à l’exception d’Index et Details) dans le contrôleur *Cm*. Une partie du code terminé est affichée ci-dessous :

		// GET: Cm/Create
		[Authorize(Roles = "canEdit")]
		public ActionResult Create()
		{
		   return View(new Contact { Address = "123 N 456 W",
		    City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT",
		   Zip = "59405"});
		}
		// POST: Cm/Create
		// To protect from overposting attacks, please enable the specific properties you want to bind to, for 
		// more details see http://go.microsoft.com/fwlink/?LinkId=317598.
		[HttpPost]
		[ValidateAntiForgeryToken]
		 [Authorize(Roles = "canEdit")]
		public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact)
		{
		    if (ModelState.IsValid)
		    {
		        db.Contacts.Add(contact);
		        db.SaveChanges();
		        return RedirectToAction("Index");
		    }
		    return View(contact);
		}
		// GET: Cm/Edit/5
		[Authorize(Roles = "canEdit")]
		public ActionResult Edit(int? id)
		{
		    if (id == null)
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
		    Contact contact = db.Contacts.Find(id);
		    if (contact == null)
		    {
		        return HttpNotFound();
		    }
		    return View(contact);
		}
		
1. Appuyez sur Ctrl+F5 pour exécuter l’application.

1. Si vous êtes toujours connecté depuis une session précédente, cliquez sur le lien **Se déconnecter**.

1. Cliquez sur le lien **À propos de** ou **Contact**. Vous êtes redirigé vers la page de connexion, car les utilisateurs anonymes ne peuvent pas afficher ces pages.

1. Cliquez sur le lien **Enregistrer comme nouvel utilisateur** et ajoutez un utilisateur local avec l’adresse de messagerie **joe@contoso.com*. Vérifiez que *Jerome* peut afficher les pages Accueil, Contact et À propos de.

	![se connecter](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. Cliquez sur le lien *Démo de GC* et vérifiez que les données s’affichent correctement.

1. Cliquez sur le lien Modifier de la page. Vous serez redirigé vers la page de connexion (car un nouvel utilisateur local n’est pas ajouté au rôle *canEdit*).

1. Connectez-vous en tant que **user1@contoso.com* avec le mot de passe « P\_assw0rd1 » (le « 0 » de « word » est un zéro). Vous êtes redirigé vers la page de modification que vous aviez sélectionnée.

	Si vous ne pouvez pas vous connecter avec ce compte et ce mot de passe, essayez de copier le mot de passe à partir du code source, puis de le coller ici. Si vous ne pouvez toujours pas vous connecter, vérifiez la colonne **UserName** de la table **AspNetUsers** pour être sûr que **user1@contoso.com* a été ajoutée.

1. Vérifiez que vous pouvez effectuer des modifications.

## Déploiement de l’application dans Azure

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.

	![Publier dans le menu contextuel du projet][firsdeploy003]

	L'Assistant **Publier le site Web** s'ouvre.

1. Cliquez sur l’onglet **Paramètres** sur le côté gauche de la boîte de dialogue **Publier le site Web**.

2. Cliquez sur l’icône **v** afin de sélectionner la **Chaîne de connexion distante** pour **ApplicationDbContext**, puis sélectionnez la base de données créée lors de la création du projet.
   
	![paramètres](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. Sous **ContactManagerContext**, sélectionnez **Execute Code First Migrations**.

	![paramètres](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc3.png)

1. Cliquez sur **Publier**.

1. Connectez-vous en tant que **user1@contoso.com* (avec le mot de passe « P\_assw0rd1 ») et vérifiez que vous pouvez modifier les données.

1. Déconnectez-vous.

1. Accédez à la [Google Developers Console](https://console.developers.google.com/) et, sur l’onglet, **Informations d’identification**, mettez à jour les URI de redirection et les origines JavaScript pour utiliser l’URL Azure.

1. Connectez-vous en utilisant votre compte Google ou Facebook. Cela ajoutera votre compte Google ou Facebook au rôle **peutModifier**. Si vous obtenez une erreur HTTP 400 avec le message *L’URI de redirection dans la demande : https://contactmanager{my version}.azurewebsites.net/signin-google ne correspond pas à une URI de redirection enregistrée.*, vous devrez patienter jusqu’à ce que les modifications apportées soient répercutées. Si vous obtenez cette erreur après plus de quelques minutes, vérifiez que les URI sont corrects.

### Arrêtez l’application web pour empêcher d’autres personnes de s’inscrire.  

1. Dans l’**Explorateur de serveurs**, accédez à **Azure > App Service > {votre groupe de ressources} > {votre application web}**.

4. Cliquez avec le bouton droit sur l’application web et sélectionnez **Arrêter**.

	Sur le [portail Azure](https://portal.azure.com/), vous pouvez également accéder au panneau de l’application web, puis cliquer sur l’icône **Arrêter** en haut du panneau.

	![arrêter le portail d’application Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### Suppression de AddToRoleAsync, Publish et Test

1. Commentez ou supprimez le code suivant à partir de la méthode **ExternalLoginConfirmation** dans le contrôleur Compte :

		await UserManager.AddToRoleAsync(user.Id, "canEdit");

1. Générez le projet (ce qui enregistre les modifications apportées au fichier et recherche d’éventuelles erreurs de compilation).

5. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

	   ![Publier dans le menu contextuel du projet](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
4. Cliquez sur le bouton **Démarrer l’aperçu**. Seuls les fichiers à mettre à jour sont déployés.

5. Démarrez l’application web à partir de Visual Studio ou du portail. **Vous ne pouvez pas procéder à une publication pendant l’arrêt de l’application web**.

	![démarrer l’application Web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. Revenez à Visual Studio, puis cliquez sur **Publier**.

3. Votre application Azure ouvre votre navigateur par défaut. Si vous êtes connecté, déconnectez-vous pour pouvoir afficher la page d’accueil en tant qu’utilisateur anonyme.

4. Cliquez sur le lien **À propos de**. Vous êtes redirigé vers la page de connexion.

5. Cliquez sur le lien **S’inscrire** sur la page de connexion, puis créez un compte local. Nous utiliserons ce compte local pour vérifier que vous pouvez bien accéder aux pages en lecture seule, mais pas aux pages qui permettent de modifier des données (qui sont protégées par le rôle *peutModifier*). Plus loin dans ce didacticiel, vous allez supprimer les accès au compte local.

	![S’inscrire](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. Vérifiez que vous pouvez accéder aux pages *À propos de* et *Contact*.

	![Fermer la session](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. Cliquez sur le lien **Démo de GC** pour accéder au contrôleur **Cm**. Vous pouvez également ajouter *Cm* à l’URL.

	![Page CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. Cliquez sur un lien Modifier.

	Vous êtes redirigé vers la page de connexion.

2. Sous **Use another service to log in**, cliquez sur Google ou Facebook, puis connectez-vous avec le compte que vous avez inscrit. Si vous travaillez rapidement et que le cookie de votre session n’est pas expiré, vous serez automatiquement connecté avec le compte Google ou Facebook que vous avez utilisé auparavant.

2. Vérifiez que vous pouvez modifier les données en étant connecté avec ce compte.

	**Remarque :** vous ne pouvez pas vous déconnecter de Google à partir de cette application pour ensuite vous connecter à un autre compte Google avec le même navigateur. Si vous utilisez un seul navigateur, vous devez accéder à Google et vous déconnecter. Vous pouvez vous connecter avec un autre compte à partir du même authentificateur tiers (tel que Google) en utilisant un autre navigateur.

	Si vous n'avez pas indiqué le prénom et le nom de vos informations de compte Google, une NullReferenceException se produit.

## Examen de la base de données SQL Azure ##

1. Dans l’**Explorateur de serveurs**, accédez à **Azure > Bases de données SQL > {votre base de données}**

2. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**.
 
	![ouvrir dans SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. Si vous ne vous êtes pas encore connecté à cette base de données, vous serez peut-être invité à ajouter une règle de pare-feu afin d’autoriser l’accès à votre adresse IP actuelle. L’adresse IP sera automatiquement préremplie. Il vous suffira de cliquer sur **Ajouter une règle de pare-feu** pour activer l’accès.

	![obtenir une règle de pare-feu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)

3. Connectez-vous à la base de données avec le nom d’utilisateur et le mot de passe que vous avez indiqués lors de la création du serveur de base de données.
 
1. Cliquez avec le bouton droit sur la table **AspNetUsers**, puis sélectionnez **Afficher les données**.

	![Page CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. Notez l’ID du compte Google utilisé pour votre inscription afin de lui attribuer le rôle **canEdit**, ainsi que l’ID de **user1@contoso.com*. Ces ID doivent être les seuls à avoir le rôle **peutModifier**. Nous allons vérifier cela à l’étape suivante.

	![Page CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. Dans **SQL Server Object Explorer**, cliquez avec le bouton droit sur **AspNetUserRoles**, puis sélectionnez **Afficher les données**.

	![Page CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
3. Vérifiez que les **UserId** sont ceux de **user1@contoso.com* et du compte Google de votre inscription.

## Résolution de problèmes

Si vous rencontrez des problèmes, voici quelques suggestions pour essayer de les résoudre.

* Erreurs de configuration de base de données SQL : assurez-vous que le Kit de développement logiciel (SDK) actuel est installé. Les versions antérieures à la version 2.8.1 contiennent un bogue qui, dans certains scénarios, provoque des erreurs au moment où Visual Studio essaie de créer le serveur de base de données ou la base de données.
* Message d’erreur « Opération non prise en charge pour votre type d’offre d’abonnement » lors de la création de ressources Azure : comme ci-dessus.
* Erreurs lors du déploiement : pensez à consulter l’article [Déploiement ASP.NET de base](web-sites-dotnet-get-started.md). Ce scénario de déploiement est plus simple et, si vous avez le même problème, il sera certainement plus facile à isoler. Par exemple, dans certains environnements d’entreprise, un pare-feu d’entreprise peut empêcher Web Deploy d’établir les genres de connexions à Azure nécessaires.
* Aucune option pour sélectionner la chaîne de connexion dans l’Assistant de publication Web lors du déploiement : si vous avez utilisé une autre méthode pour créer vos ressources Azure (par exemple, vous essayez de déployer vers une application web et une base de données SQL créée dans le portail), la base de données SQL peut ne pas être associée à l’application web. La solution la plus simple consiste à créer une application web et une base de données en utilisant Visual Studio comme indiqué dans le didacticiel. Vous n’êtes pas obligé de reprendre le didacticiel du début. Dans l’Assistant de publication web, vous pouvez choisir de créer une application web ; vous obtiendrez la même boîte de dialogue de création de ressources Azure que lorsque vous créez le projet.
* Les indications pour les portails de développeurs Google ou Facebook sont obsolètes : consultez le commentaire Disqus proposé à la fin de ce didacticiel.

## Étapes suivantes

Vous avez créé une application web MVC ASP.NET de base qui authentifie les utilisateurs. Pour plus d’informations sur les tâches d’authentification courantes et la manière de sécuriser les données sensibles, reportez-vous aux didacticiels suivants.

- [Création d’une application web ASP.NET MVC 5 sécurisée avec authentification, confirmation par courrier électronique et réinitialisation des mots de passe](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset) (en anglais)
- [Application ASP.NET MVC 5 avec SMS et authentification à deux facteurs par courrier électronique](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication) (en anglais)
- [Meilleures pratiques pour le déploiement de mots de passe et d’autres données sensibles dans ASP.NET et Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) (en anglais) 
- [Création d’une application ASP.NET MVC 5 avec une authentification OAuth2 pour Facebook et Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) en anglais). Inclut des instructions sur l’ajout de données de profil à la base de données d’inscription d’utilisateur, ainsi que sur l’utilisation de Facebook comme fournisseur d’authentification.
- [Prise en main d’ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Pour un didacticiel plus avancé sur l’utilisation d’Entity Framework, consultez [Prise en main d’Entity Framework et de MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) (en anglais).

Ce didacticiel a été écrit par [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) avec l’aide de Tom Dykstra et de Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

***N’hésitez pas à nous transmettre vos commentaires*** sur ce qui vous a plu et ce qui pourrait être amélioré… pas seulement à propos de ce didacticiel, mais aussi en ce qui concerne les produits présentés ici. Vos commentaires nous aideront à orienter nos améliorations. Vous pouvez aussi demander de nouvelles rubriques et noter les rubriques existantes sur [Leçons de code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

## Changements apportés

* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page : [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
 

<!---HONumber=AcomDC_1217_2015-->