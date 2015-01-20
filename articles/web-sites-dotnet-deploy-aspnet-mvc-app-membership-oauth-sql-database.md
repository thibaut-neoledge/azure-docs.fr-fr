<properties urlDisplayName="Website with SQL Database" pageTitle="Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site web Azure" metaKeywords="Azure hello world tutorial, Azure getting started tutorial, SQL Database tutorial, Azure .NET hello world tutorial, Azure C# hello world tutorial, SQL Azure C# tutorial" description="Découvrez comment développer un site web ASP.NET MVC 5 avec un serveur principal de base de données SQL et comment le déployer sur Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="riande" /> 



# Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site web Azure

*** Mise à jour le 12 octobre 2014.***

Ce didacticiel vous montre comment générer une application web ASP.NET MVC 5 sécurisée qui permet aux utilisateurs de se connecter avec des informations d'identification Facebook ou Google. Vous allez également déployer l'application sur Azure.

Vous pouvez ouvrir gratuitement un compte Azure. Si vous n'avez pas déjà Visual Studio 2013, le Kit de développement logiciel (SDK) installe automatiquement Visual Studio Express 2013 pour le Web. Vous pouvez commencer vos développement Azure gratuitement.

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de ce didacticiel, vous disposerez d'une application web pilotée par les données sécurisée et fonctionnelle dans le cloud et utilisant une base de données du cloud.

Vous allez découvrir comment :

* créer un projet ASP.NET MVC 5 sécurisé et le publier sur un site web Azure ;
* utiliser [OAuth](http://oauth.net/ "http://oauth.net/") et la base de données des membres ASP.NET pour sécuriser votre application ;
* utiliser une base de données SQL pour stocker des données dans Azure ;

Vous développerez une application web de liste de contacts simple basée sur ASP.NET MVC 5 et utilisant Entity Framework ADO.NET pour accéder à la base de données. L'illustration suivante montre la page de connexion une fois l'application terminée :

![login page][rxb]

>[WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez <a href="/fr-fr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activer vos avantages d'abonné MSDN</a> ou <a href="/fr-fr/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">vous inscrire pour obtenir une version d'évaluation gratuite</a>. Si vous souhaitez prendre en main Sites Web Azure avant d'ouvrir un compte, accédez à <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pouvez créer immédiatement et gratuitement un site de démarrage ASP.NET à courte durée de vie dans Sites Web Azure . Carte de crédit non nécessaire, aucun engagement.


Dans ce didacticiel :

- [Configuration de l'environnement de développement](#setupdevenv)
- [Création d'une application ASP.NET MVC 5][createapplication]
- [Déploiement de l'application dans Azure][deployapp1]
- [Ajout d'une base de données à l'application][adddb]
- [Ajout d'un fournisseur OAuth][]
- [Déploiement de l'application dans Azure][deployapp11]
- [Étapes suivantes][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Vous devez installer [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?LinkId=390521) ou version ultérieure pour utiliser le nouveau certificat SSL pour l'hôte local (localhost).

<h2><a name="bkmk_createmvc4app"></a>Création d'une application ASP.NET MVC 5</h2>

### Création du projet

1. Dans le menu **Fichier**, cliquez sur **Nouveau projet**.

	![New Project in File menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. Dans la boîte de dialogue **Nouveau projet**, développez **C#**, sélectionnez **Web** sous **Modèles installés**, puis sélectionnez **Application Web ASP.NET**.

1. Nommez l'application **ContactManager**, puis cliquez sur **OK**.

	![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**Remarque :** assurez-vous que vous entrez " ContactManager ". Les blocs de code que vous allez copier plus tard partent du principe que le nom du projet est ContactManager. 

1. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle **MVC**. Vérifiez que **Authentification** a la valeur **Comptes d'utilisateur individuels*, que l'option **Hôte du cloud** est sélectionnée et que **Site Web** est sélectionné.

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

1. L'Assistant Configuration suggère un nom unique basé sur *ContactManager* (voir l'image ci-dessous). Sélectionnez une région proche de chez vous. Vous pouvez utiliser [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") pour trouver le centre de données de à la latence la plus faible. 
2. Si vous n'avez pas encore créé de serveur de bases de données, sélectionnez **Créer un serveur**, entrez un nom d'utilisateur de base de données et un mot de passe.

	![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

Si vous avez un serveur de bases de données, utilisez-le pour créer une base de données. Les serveurs de bases de données constituent une ressource essentielle et il est généralement préférable de créer plusieurs bases de données sur le même serveur à des fins de test et de développement plutôt que de créer un serveur de bases de données par base de données. Assurez-vous que votre site web et la base de données sont dans la même région.

![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### Définition de l'en-tête et du pied de page de la page


1. Dans l'**Explorateur de solutions**, ouvrez le fichier *Layout.cshtml* dans le dossier *Views\Shared*.

	![_Layout.cshtml in Solution Explorer][newapp004]

1. Remplacez le balisage du fichier *Layout.cshtml* par le code suivant. Les modifications sont mises en surbrillance ci-dessous.

<pre>
			&lt;!DOCTYPE html&gt;
			&lt;html&gt;
			&lt;head&gt;
			    &lt;meta charset="utf-8" /&gt;
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
			    &lt;title&gt;@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title&gt;
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head&gt;
			&lt;body&gt;
			    &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt;
			        &lt;div class="container"&gt;
			            &lt;div class="navbar-header"&gt;
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                &lt;/button&gt;
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div&gt;
			            &lt;div class="navbar-collapse collapse"&gt;
			                &lt;ul class="nav navbar-nav"&gt;
			                    &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt;
			                &lt;/ul&gt;
			                @Html.Partial("_LoginPartial")
			            &lt;/div&gt;
			        &lt;/div&gt;
			    &lt;/div&gt;
			    &lt;div class="container body-content"&gt;
			        @RenderBody()
			        &lt;hr /&gt;
			        &lt;footer&gt;
			            &lt;p&gt;&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p&gt;
			        &lt;/footer&gt;
			    &lt;/div&gt;
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body&gt;
			&lt;/html&gt;
</pre>


![code changes](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### Exécution locale de l'application

1. Appuyez sur Ctrl+F5 pour exécuter l'application.

	La page d'accueil de l'application apparaît dans le navigateur par défaut.

	![Web site running locally](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Voilà, vous avez fait tout ce qu'il fallait pour créer l'application que vous allez déployer dans Azure. 

## Activation du protocole SSL pour le projet ##

1. Activez le protocole SSL. Dans l'Explorateur de solutions, cliquez sur le projet **ContactManager**, puis appuyez sur F4 pour afficher la boîte de dialogue Propriétés. Affectez la valeur True à **SSL activé**. Copiez l'**URL SSL**. L'URL SSL est https://localhost:44300/, sauf si vous avez déjà créé des sites web SSL.

	![enable SSL][rxSSL]
 
1. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **Contact Manager**, puis sur **Propriétés**.
1. Sous l'onglet de gauche, cliquez sur **Web**.
1. Modifiez l'**URL du projet** pour utiliser l'**URL SSL**, puis enregistrez la page (Ctrl+S).

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Assurez-vous que Microsoft Internet Explorer est le navigateur par défaut pour Visual Studio, comme indiqué sur l'image ci-dessous :

	![default browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	Le sélecteur de navigateur vous permet d'indiquer le navigateur utilisé par Visual Studio.

 	![browser selector](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	Vous pouvez sélectionner plusieurs navigateurs et faire en sorte que Visual Studio mette à jour chaque navigateur lorsque vous apportez des modifications. Pour plus d'informations, consultez la page [Utilisation du lien de navigateur dans Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Appuyez sur CTRL+F5 pour exécuter l'application. Suivez les instructions pour approuver le certificat auto-signé généré par IIS Express.

	 ![instructions to trust the self-signed certificate that IIS Express has generated](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. Lisez le contenu de la boîte de dialogue **Avertissement de sécurité**, puis cliquez sur **Oui** si vous souhaitez installer le certificat représentant l'**hôte local** (localhost).

 	![localhost IIS Express certificate warning ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. IE affiche la page d'accueil sans avertissement SSL.

	 ![IE with localhost SSL and no warnings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Google Chrome accepte également le certificat et affiche les contenus HTTPS sans avertissement. Firefox utilise son propre magasin de certificats. Il affiche donc un avertissement.

	 ![FireFox Cert Warning](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>Déploiement de l'application dans Azure</h2>

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.

	![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   L'Assistant **Publier le site Web** s'ouvre.

1. Dans la boîte de dialogue **Publier le site Web**, cliquez sur **Publier**.

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	L'application créée est maintenant exécutée dans le cloud. Au prochain déploiement de l'application, seuls les fichiers modifiés (ou nouveaux) seront déployés.

	![Running in Cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Ajout d'une base de données à l'application</h2>

﻿À présent, vous allez mettre à jour l'application pour y ajouter la capacité à afficher et à mettre à jour les contacts, puis stocker les données dans une base de données. L'application va utiliser Entity Framework (EF) pour créer la base de données et pour lire et mettre à jour les données.

### Ajout de classes de modèle de données pour les contacts

Commencez par créer un modèle de données simple dans le code.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier Modèles, cliquez sur **Ajouter**, puis sur **Classe**.

	![Add Class in Models folder context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. Dans la boîte de dialogue **Ajouter un nouvel élément**, nommez le nouveau fichier de classe *Contact.cs*, puis cliquez sur **Ajouter**.

	![Add New Item dialog box][adddb002]

3. Remplacez le contenu du fichier Contacts.cs par le code suivant.

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

### Création de pages web permettant aux utilisateurs de l'application d'utiliser des contacts

La fonctionnalité de génération de modèles automatique ASP.NET MVC peut générer automatiquement un code qui effectue les actions CRUD (Create, Read, Update et Delete, ou Créer, Lire, Mettre à jour et Supprimer).

<h2><a name="bkmk_addcontroller"></a>Ajout d'un contrôleur et affichage des données</h2>

1. Générez le projet **(Ctrl+Maj+B)**. (Vous devez générer le projet avant d'utiliser le mécanisme de génération de modèle automatique.) 
1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier Contrôleurs, puis cliquez sur **Ajouter** et sur **Contrôleur**.

	![Add Controller in Controllers folder context menu][addcode001]

5. Dans la boîte de dialogue **Add Scaffold**, sélectionnez **MVC 5 Controller with views, using EF**, puis cliquez sur **Ajouter**.
	
	![Add Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. Dans la zone déroulante **Classe de modèle**, sélectionnez **Contact (ContactManager.Models)**. (Voir l'image ci-dessous).
1. Dans la **classe du contexte des données**, sélectionnez **ApplicationDbContext (ContactManager.Models)**. **ApplicationDbContext** est utilisé pour la base de données d'appartenance et nos données de contact.
1. Dans la zone de texte **Nom du contrôleur**, entrez " CmController " comme nom du contrôleur. 

	![New data ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. Cliquez sur **Ajouter**.

   Visual Studio crée des méthodes de contrôleur et des vues pour les opérations de base de données CRUD des objets **Contact**.

## Activation des migrations, création de la base de données, ajout d'exemples de données et d'un initialiseur de données ##

L'étape suivante consiste à activer la fonctionnalité [Migrations Code First](http://msdn.microsoft.com/library/hh770484.aspx) pour créer la base de données en fonction du modèle de données que vous avez créé.

1. Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** puis **Console du Gestionnaire de package**.
	![Package Manager Console in Tools menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :

		enable-migrations
	La commande **enable-migrations** crée un dossier *Migrations* dans lequel elle place un fichier *Configuration.cs* que vous pouvez modifier pour amorcer la base de données et configurer les migrations. 

2. Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :

		add-migration Initial


	La commande **add-migration Initial** génère un fichier nommé **&lt;date_stamp&gt;Initial** dans le dossier *Migrations* qui crée la base de données. Le premier paramètre (**Initial**) est arbitraire et permet de créer le nom du fichier. Les nouveaux fichiers de classe sont affichés dans l'**Explorateur de solutions**.
	Dans la classe **Initial**, la méthode **Up** crée la table des contacts et la méthode **Down** (utilisée lorsque vous voulez revenir à l'état précédent) annule cette table.
3. Ouvrez le fichier *Migrations\Configuration.cs*. 
4. Ajoutez l'espace de noms suivant. 

    	 using ContactManager.Models;



5. Remplacez la méthode *Seed* par le code suivant :

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

	Ce code initialise (amorce) la base de données avec les informations de contact. Pour plus d'informations sur l'amorçage de la base de données, consultez la page [Amorçage et débogage des bases de données Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. Dans la **Console du Gestionnaire de package**, entrez la commande suivante :

		update-database

	![Package Manager Console commands][addcode009]

	La commande **update-database** exécute la première migration qui entraîne la création de la base de données. Par défaut, la base de données est créée en tant que base de données SQL Server Express LocalDB. 

7. Appuyez sur Ctrl+F5 pour exécuter l'application, puis cliquez sur le lien **Démo de GC** ou accédez à http://localhost:(port#)/Cm. 

	L'application affiche les données de départ, ainsi que des liens pour les modifier, les supprimer ou obtenir des informations supplémentaires. Vous pouvez créer, modifier, supprimer et afficher les données.

	![MVC view of data][rx2]



<h2><a name="addOauth"></a>Ajout d'un fournisseur OAuth2</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") est un protocole ouvert permettant de mettre en place une authentification sécurisée de manière simple et standardisée à partir d'applications web, mobiles ou de bureau. Le modèle Internet d'ASP.NET MVC utilise OAuth pour exposer Facebook, Twitter, Google et Microsoft comme fournisseurs d'authentification. Même si ce didacticiel utilise uniquement Google comme fournisseur d'authentification, vous pouvez facilement modifier le code pour utiliser n'importe lequel de ces fournisseurs. La procédure à suivre pour implémenter d'autres fournisseurs ressemble assez à celle présentée dans ce didacticiel. Pour utiliser Facebook comme fournisseur d'authentification, consultez le didacticiel [Application MVC 5 avec authentification OAuth2 Facebook, Twitter, LinkedIn et Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

En plus de l'authentification, ce didacticiel va également utiliser des rôles pour l'implémentation d'autorisations. Seuls les utilisateurs ajoutés au rôle *canEdit* pourront modifier les données (et donc créer, modifier ou supprimer des contacts).

Suivez les instructions du didacticiel [Application MVC 5 avec authentification OAuth2 Facebook, Twitter, LinkedIn et Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) sous **Création d'une application Google pour OAuth 2 pour configurer une application Google pour OAuth2**. Exécutez et testez l'application pour vérifier que vous pouvez vous connecter à l'aide de l'authentification Google.

<h2><a name="mbrDB"></a>Utilisation de l'API d'appartenance</h2>
Dans cette section, vous allez ajouter un utilisateur local, ainsi que le rôle *canEdit* à la base de données d'appartenance. Seuls les utilisateurs qui ont le rôle *canEdit* pourront modifier les données. Il est recommandé de nommer les rôles en fonction des actions qu'ils peuvent effectuer, c'est pourquoi *canEdit* est un meilleur nom que *admin*. Lorsque votre application évolue, vous pouvez ajouter de nouveaux rôles tels que *canDeleteMembers*, plus parlant que le nom *superAdmin*.

1. Ouvrez le fichier *migrations\configuration.cs* et ajoutez les instructions `using` suivantes :

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Ajoutez la méthode **AddUserAndRole** suivante à la classe :

    
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

1. Appelez la nouvelle méthode à partir de la méthode **Seed** :
	<pre>
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }
	</pre>  
<span></span>
	Les images suivantes indiquent les modifications apportées à la méthode *Seed* :

	![code image](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   Ce code crée un rôle nommé *canEdit*, un utilisateur local nommé *user1@contoso.com* et ajoute *user1@contoso.com* au rôle *canEdit*. Pour plus d'informations, consultez la [page de ressources ASP.NET Identity](http://curah.microsoft.com/55636/aspnet-identity).

## Utilisation de code temporaire pour ajouter de nouveaux utilisateurs de réseaux sociaux au rôle canEdit ##
Dans cette section, vous allez modifier temporairement la méthode **ExternalLoginConfirmation** dans le contrôleur Account pour ajouter de nouveaux utilisateurs se connectant avec un fournisseur OAuth ou OpenID au rôle *canEdit*. Nous allons modifier temporairement la méthode **ExternalLoginConfirmation** pour ajouter automatiquement de nouveaux utilisateurs à un rôle d'administration. Tant que nous n'aurons pas fourni d'outil pour ajouter et gérer des rôles, nous utiliserons le code d'inscription automatique ci-dessous. Nous espérons fournir un outil similaire à [WSAT](http://msdn.microsoft.com/fr-fr/library/ms228053.aspx) à l'avenir pour vous permettre de créer et de modifier les comptes et les rôles des utilisateurs. Plus loin dans ce didacticiel, nous verrons comment utiliser l'**Explorateur de solutions** pour ajouter des utilisateurs aux rôles.  

1. Ouvrez le fichier **Controllers\AccountController.cs** et accédez à la méthode **ExternalLoginConfirmation**.
1. Ajoutez l'appel suivant à **AddToRoleAsync** juste avant l'appel **SignInAsync**.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   Le code ci-dessus ajoute les utilisateurs récemment enregistrés au rôle " canEdit ", qui leur permet d'accéder aux méthodes d'action pour modifier les données. L'image suivante illustre une modification du code.

   ![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Plus loin dans ce didacticiel, vous allez déployer l'application sur Azure, en vous connectant avec Google ou un autre fournisseur d'authentification tiers. Ceci vous permettra d'ajouter votre compte récemment inscrit au rôle *canEdit*. Les utilisateurs trouvant l'URL de votre site et possédant un ID Google peuvent ensuite s'inscrire et mettre à jour votre base de données. Pour empêcher que cela se produise, vous pouvez arrêter le site. Vous pouvez vérifier quels utilisateurs sont rattachés au rôle *canEdit* en examinant la base de données.

Dans la **Console du Gestionnaire de package**, actionnez la touche Haut pour afficher la commande suivante :

		Update-Database

Exécutez la commande **Update-Database**, qui exécutera la méthode **Seed**, qui exécutera la méthode **AddUserAndRole** que vous venez d'ajouter. **AddUserAndRole** crée l'utilisateur *user1@contoso.com* et l'ajoute au rôle *canEdit*.

## Protection de l'application à l'aide du protocole SSL et de l'attribut Authorize ##

Dans cette section, vous allez appliquer l'attribut [Authorize](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.authorizeattribute.aspx) pour limiter l'accès aux méthodes d'action. Les utilisateurs anonymes pourront uniquement afficher la méthode d'action **Index** du contrôleur d'accueil. Les utilisateurs inscrits pourront afficher les données de contact (pages **Index** et **Details** du contrôleur Cm), les pages " About" et " Contact ". Seuls les utilisateurs du rôle *canEdit* pourront accéder aux méthodes d'action qui modifient les données.

1. Ajoutez les filtres [Authorize](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.authorizeattribute.aspx) filter et [RequireHttps](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.requirehttpsattribute.aspx) à l'application. Une autre approche consiste à ajouter les attributs [Authorize](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.authorizeattribute.aspx) et [RequireHttps](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.requirehttpsattribute.aspx) à chaque contrôleur, mais il est recommandé de les appliquer à l'ensemble de l'application. Si vous les ajoutez de manière globale, les nouveaux contrôleurs et les nouvelles méthodes d'action que vous ajouterez seront automatiquement protégés ; vous n'aurez pas à vous rappeler qu'il faut leur appliquer ces attributs à chaque fois. Pour plus d'informations, consultez la page [Sécurisation de votre application ASP.NET MVC et nouvel attribut AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Ouvrez le fichier *App_Start\FilterConfig.cs* et remplacez la méthode *RegisterGlobalFilters* par le code suivant (qui ajoute les deux filtres) :
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>


	L'image suivante montre le code modifié :


	Le filtre [Authorize](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.authorizeattribute.aspx) appliqué dans le code ci-dessus empêchera les utilisateurs anonymes d'accéder aux méthodes de l'application. Vous allez utiliser l'attribut [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) pour désactiver les contraintes d'autorisation dans quelques méthodes, pour que les utilisateurs anonymes puissent se connecter et afficher la page d'accueil. [RequireHttps](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.requirehttpsattribute.aspx) nécessite que tous les accès à l'application web se fassent via HTTPS.

1. Ajoutez l'attribut [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) à la méthode **Index** du contrôleur Home. L'attribut [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) vous permet de créer une liste des méthodes pour lesquelles vous voulez désactiver l'autorisation. L'image ci-dessous montre une partie du HomeController :	

  	![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Effectuez une recherche globale pour *AllowAnonymous* : vous pouvez voir qu'il est utilisé dans les méthodes de connexion et d'inscription du contrôleur Account.
1. Dans *CmController.cs*, ajoutez `[Authorize(Roles = "canEdit")]` aux méthodes HttpGet et HttpPost qui modifient les données (Create, Edit, Delete, toutes les méthodes d'action à l'exception de Index et Details) dans le contrôleur *Cm*. Une partie du code terminé est affichée ci-dessous : 

   	![img of code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. Si vous êtes toujours connecté depuis une session précédente, cliquez sur le lien **Log out**.
1. Cliquez sur le lien **About** ou **Contact**. Vous serez redirigé vers la page de connexion, car les utilisateurs anonymes ne peuvent pas afficher ces pages. 
1. Cliquez sur le lien **Enregistrer comme nouvel utilisateur** et ajoutez un utilisateur local avec l'adresse de messagerie *joe@contoso.com*. Vérifiez que *Joe* peut afficher les pages Home, About et Contact . 

	![login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Cliquez sur le lien *CM Demo* et vérifiez que les données s'affichent correctement. 
1. Cliquez sur le lien Modifier de la page. Vous serez redirigé vers la page de connexion (car un nouvel utilisateur local n'est pas ajouté au rôle *canEdit*).
1. Connectez-vous avec l'identifiant *user1@contoso.com* et le mot de passe " P_assw0rd1 " (le " 0 " de " word " est un zéro). Vous serez redirigé vers la page de modification que vous aviez sélectionnée. <br/>
   Si vous ne pouvez pas vous connecter avec ce compte et ce mot de passe, essayez de copier le mot de passe à partir du code source, puis de le coller ici. Si vous ne pouvez toujours pas vous connecter, vérifiez la colonne **UserName** de la table **AspNetUsers** pour être sûr que *user1@contoso.com* a été ajouté. 

1. Vérifiez que vous pouvez effectuer des modifications.

<h2><a name="bkmk_deploytowindowsazure11"></a>Déploiement de l'application dans Azure</h2>

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.

	![Publish in project context menu][firsdeploy003]

	L'Assistant **Publier le site Web** s'ouvre.

1. Cliquez sur l'onglet **Paramètres** sur le côté gauche de la boîte de dialogue **Publier le site Web**. Cliquez sur l'icône **v** pour sélectionner la **chaîne de connexion à distance** de **ApplicationDbContext** et sélectionnez **ContactManagerNN_db**.

   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. Sous **ContactManagerContext**, sélectionnez **Exécuter les migrations Code First**.

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Cliquez sur **Publier**.
1. Connectez-vous en tant que *user1@contoso.com* (avec le mot de passe " P_assw0rd1 ") et vérifiez que vous pouvez modifier les données.
1. Déconnectez-vous.
1. Accédez à la [Console pour développeurs Google](https://console.developers.google.com/) et, sous l'onglet **Informations d'identification**, mettez à jour les URI de redirection et origines JavaScript de façon à utiliser l'URL Azure.
1. Connectez-vous en utilisant votre compte Google ou Facebook. Cela ajoutera votre compte Google ou Facebook au rôle **canEdit**. Si vous obtenez une erreur HTTP 400 avec le message *L'URI de redirection de la demande: https://contactmanager{my version}.azurewebsites.net/signin-google did not match a registered redirect URI.*, you'll have to wait until the changes you made are propagated. If you get this error after more than a minute, verify the URIs are correct.

### Arrêtez le site web pour empêcher que d'autres personnes s'inscrivent  

1. Dans l'**Explorateur de serveurs**, accédez à **Sites Web**.
4. Cliquez avec le bouton droit sur chaque instance de site web, puis sélectionnez **Arrêter le site Web**. 

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	En guise d'alternative, à partir du portail de gestion Azure, vous pouvez sélectionner le site web, puis cliquer sur l'icône **Arrêter** en bas de la page.

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Suppression de AddToRoleAsync, Publish et Test

1. Placez le code suivant en commentaire ou supprimez-le de la méthode **ExternalLoginConfirmation** dans le contrôleur Account : 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Générez le projet (ce qui enregistre les modifications apportées au fichier et recherche d'éventuelles erreurs de compilation).
5. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

	   ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Cliquez sur le bouton **Démarrer l'aperçu**. Seuls les fichiers à mettre à jour sont déployés.
5. Démarrez le site web à partir de Visual Studio ou du portail. **Vous ne pourrez pas publier pendant l'arrêt du site Web**.

	![start web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Revenez à Visual Studio, puis cliquez sur **Publier**.
3. Votre application Azure ouvre votre navigateur par défaut. Si vous êtes connecté, déconnectez-vous pour pouvoir afficher la page d'accueil en tant qu'utilisateur anonyme.  
4. Cliquez sur le lien **About**. Vous êtes redirigé vers la page de connexion.
5. Cliquez sur le lien **Register** dans la page de connexion, puis créez un compte local. Nous utiliserons ce compte local pour vérifier que vous pouvez bien accéder aux pages en lecture seule, mais pas aux pages qui permettent de modifier des données (qui sont protégées par le rôle *canEdit*). Plus loin dans ce didacticiel, nous allons supprimer les accès au compte local. 

	![Register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. Vérifiez que vous pouvez accéder aux pages *About* et *Contact*.

	![Log off](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. Cliquez sur le lien **CM Demo** pour accéder au contrôleur **Cm**. Vous pouvez également ajouter *Cm* à l'URL. 

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Cliquez sur un lien Modifier. Vous êtes redirigé vers la page de connexion. Sous **Use another service to log in**, cliquez sur Google ou Facebook, puis connectez-vous avec le compte que vous avez inscrit. Si vous travaillez rapidement et que le cookie de votre session n'est pas expiré, vous serez automatiquement connecté avec le compte Google ou Facebook que vous avez utilisé auparavant.
2. Vérifiez que vous pouvez modifier les données en étant connecté avec ce compte.
 	**Remarque :** vous ne pouvez pas vous déconnecter de Google à partir de cette application pour ensuite vous connecter à un autre compte Google avec le même navigateur. Si vous utilisez un seul navigateur, vous devez accéder à Google et vous déconnecter. Vous pouvez vous connecter avec un autre compte à partir du même authentificateur tiers (tel que Google) en utilisant un autre navigateur.

Si vous n'avez pas indiqué le prénom et le nom de vos informations de compte Google, une NullReferenceException se produit.


## Examen de la base de données SQL Azure ##

1. Dans l'**Explorateur de serveurs**, accédez à la base de données **ContactDB**
2. Cliquez avec le bouton droit sur **ContactDB**, puis sélectionnez **Ouvrir dans l'Explorateur d'objets SQL Server**.
 
	![open in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**Remarque :** si vous ne pouvez pas développer **Bases de données SQL** et que vous ne pouvez *pas* afficher la base de données **ContactDB** dans Visual Studio, suivez les instructions ci-dessous pour ouvrir un ou plusieurs ports de pare-feu. Suivez les instructions sous **Configuration des règles de pare-feu Azure**. Vous devrez peut-être attendre quelques minutes avant d'accéder à la base de données après avoir ajouté la règle de pare-feu.
 
1. Cliquez avec le bouton droit sur la table **AspNetUsers**, puis sélectionnez **Afficher les données**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Notez l'ID du compte Google utilisé pour votre inscription pour lui attribuer le rôle **canEdit**, ainsi que l'ID *user1@contoso.com*. Ces ID doivent être les seuls à avoir le rôle **canEdit**. (Nous allons vérifier cela à l'étape suivante.)

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. Dans l'**Explorateur d'objets SQL Server**, cliquez avec le bouton droit sur **AspNetUserRoles**, puis sélectionnez **Afficher les données**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Vérifiez que les **UserId** sont ceux de *user1@contoso.com* et du compte Google de votre inscription. 


## Configuration des règles de pare-feu Azure ##

Suivez la procédure de cette section si vous ne pouvez pas vous connecter à SQL Azure à partir de Visual Studio ou si vous obtenez une boîte de dialogue indiquant " Impossible d'ouvrir le serveur ".

![firewall error](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

Vous devez ajouter votre adresse IP aux adresses IP autorisées.

1. Dans le portail Azure, sélectionnez **Bases de données SQL** sous l'onglet gauche.

	![Select SQL][rx6]

1. Cliquez sur **ContactDB**.

1. Cliquez sur le lien **Configurer des règles de pare-feu Azure pour cette adresse IP**.

	![firewall rules][rx7]

1. Lorsque le message " L'adresse IP xxx.xxx.xxx.xxx en cours n'est pas comprise dans les règles de pare-feu existantes. Voulez-vous mettre à jour les règles de pare-feu ? " s'affiche, cliquez sur **Oui**. Avec certains pare-feu d'entreprise, l'ajout de cette adresse ne suffit pas. Vous devrez ajouter une plage d'adresses IP.

L'étape suivante consiste à ajouter une plage d'adresses IP autorisées.

1. Dans le portail Azure, cliquez sur **Bases de données SQL**.
1. Sélectionnez l'onglet **Serveurs**, puis cliquez sur le serveur à configurer.

	![Servers tab in Azure ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. Cliquez sur l'onglet **Configurer**.

1. Ajoutez un nom de règle, ainsi que les adresses IP de départ et de fin.

	![ip range][rx9]

1. En bas de la page, cliquez sur **Enregistrer**.
1. Le cas échéant, n'hésitez pas à envoyer un commentaire pour nous faire savoir si vous avez dû ajouter une plage d'adresses IP pour vous connecter.

Finalement, vous pouvez vous connecter à l'instance de base de données SQL à partir de SQL Server Object Explorer (SSOX).

1. Dans le menu Affichage, cliquez sur **Explorateur d'objets SQL Server**.
1. Cliquez avec le bouton droit sur **SQL Server**, puis sélectionnez **Ajouter SQL Server**.
1. Dans la boîte de dialogue **Se connecter au serveur**, définissez l'**Authentification** sur **Authentification SQL Server**. Vous obtiendrez le **Nom du serveur** et le **Nom de connexion** à partir du portail Azure.
1. Dans votre navigateur, accédez au portail, puis sélectionnez **Bases de données SQL**.
1. Sélectionnez la base de données **ContactDB**, puis cliquez sur **Afficher les chaînes de connexion de la base de données SQL**.
1. Dans la page **Chaînes de connexion**, copiez le **Serveur** et l'**ID utilisateur**.
 
	![con string](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. Collez les valeurs **Serveur** et **ID utilisateur** dans la boîte de dialogue de Visual Studio **Se connecter au serveur**. La valeur **ID utilisateur** va dans le champ **Connexion**. Entrez le mot de passe utilisé pour la création de la base de données SQL.

	![Connect to Server DLG](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

Vous pouvez maintenant accéder à la base de données de contacts en utilisant les instructions précédentes.


## Ajout d'un utilisateur au rôle canEdit en modifiant les tables de base de données

Précédemment dans ce didacticiel, vous avez utilisé du code pour ajouter des utilisateurs au rôle " peutModifier ". Il existe une autre méthode consistant à manipuler directement les données des tables d'appartenance. La procédure suivante indique comment utiliser cette méthode pour ajouter un utilisateur à un rôle.

2. Dans l'**Explorateur d'objets SQL Server**, cliquez avec le bouton droit sur **AspNetUserRoles**, puis sélectionnez **Afficher les données**.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. Copiez la valeur *RoleId* et collez-la dans la (nouvelle) ligne vide.
	
	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. Dans la table **AspNetUsers**, recherchez l'utilisateur auquel vous voulez attribuer le rôle, puis copiez l'*ID* de cet utilisateur et collez-le dans la colonne **UserId** de la table **AspNetUserRoles**.

Nous développons actuellement un outil qui facilitera la gestion des utilisateurs et des rôles.


<h2><a name="nextsteps"></a>Étapes suivantes</h2>

Suivez mon didacticiel qui s'appuie sur cet exemple :

1.	[Créer une application web ASP.NET MVC 5 sécurisée avec connexion, confirmation de l'adresse de messagerie et réinitialisation du mot de passe](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[Application ASP.NET MVC 5 avec authentification à deux facteurs par SMS et message électronique](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Meilleures pratiques pour le déploiement de mots de passe et autres données sensibles dans ASP.NET et Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	[Créer une application ASP.NET MVC 5 avec authentification OAuth2 Facebook et Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ) Comprend des instructions sur l'ajout de données de profil à la base de données d'inscription d'utilisateur, ainsi que des instructions détaillées sur l'utilisation de Facebook comme fournisseur d'authentification.
5.	[Prise en main d'ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Pour activer les boutons de connexion aux réseaux sociaux affichés en haut de ce didacticiel, consultez [Boutons de réseaux sociaux pour ASP.NET MVC 5](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/).

L'excellent didacticiel de Tom Dykstra, [Prise en main d'EF et MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) présente des fonctions de programmation MVC et EF plus avancées.

Ce didacticiel et son exemple d'application ont été écrits par [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) avec l'aide de Tom Dykstra et Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

***N'hésitez pas à nous transmettre vos commentaires*** sur ce qui vous a plu et ce qui pourrait être amélioré... pas seulement à propos de ce didacticiel, mais aussi en ce qui concerne les produits présentés ici. Vos commentaires nous aideront à orienter nos améliorations. Vous pouvez aussi demander de nouvelles rubriques et noter les rubriques existantes sur [Leçons de code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

<!-- bookmarks -->
[Ajouter un fournisseur OAuth]: #addOauth
[Utilisation de l'API d'appartenance]:#mbrDB
[Création d'un script de déploiement des données]:#ppd
[Mise à jour de la base de données des membres]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png

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

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure Web Sites]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png

























<!--HONumber=35.2-->
