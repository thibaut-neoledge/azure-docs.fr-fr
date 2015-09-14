<properties 
	pageTitle="Créer un service REST à l’aide de l’API Web ASP.NET et de Base de données SQL dans Azure App Service"
	description="Didacticiel expliquant comment déployer une application qui utilise l’API Web ASP.NET dans une application web Azure à l’aide de Visual Studio."
	services="app-service\web"
	documentationCenter=".net"
	authors="Rick-Anderson"
	manager="wpickett"
	editor=""/>

<tags 
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="riande"/>

# Créer un service REST à l’aide de l’API Web ASP.NET et de Base de données SQL dans Azure App Service

Ce didacticiel explique comment déployer une application Web ASP.NET dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) à l’aide de l’Assistant Publier le site Web de Visual Studio 2013 ou Visual Studio Express 2013 pour le Web.

Vous pouvez ouvrir gratuitement un compte Azure. Si vous n'avez pas déjà Visual Studio 2013, le Kit de développement logiciel (SDK) installe automatiquement Visual Studio Express 2013 pour le Web. Vous pouvez donc commencer vos développements Azure gratuitement.

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de ce didacticiel, vous disposerez d’une application web simple et fonctionnelle dans le cloud.
 
Vous apprendrez ce qui suit :

* configurer votre ordinateur pour le développement Azure en installant le Kit de développement logiciel (SDK) Azure ;
* créer un projet Visual Studio ASP.NET MVC 5 et le publier dans une application Azure ;
* utiliser l'API Web ASP.NET pour activer les appels d'API Restful ;
* utiliser une base de données SQL pour stocker des données dans Azure ;
* publier des mises à jour d'application dans Azure.

Vous développerez une application Web de liste de contacts simple basée sur ASP.NET MVC 5 et utilisant Entity Framework ADO.NET pour accéder à la base de données. L’illustration suivante présente l’application terminée :

![capture d’écran du site Web][intro001]

<!-- the next line produces the "Set up the development environment" section as see at http://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/ -->
[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

### Création du projet

1. Démarrez Visual Studio 2013.
1. Dans le menu **File**, cliquez sur **New Project**.
3. Dans la boîte de dialogue **New Project**, développez **Visual C#** et sélectionnez **Web**, puis **ASP.NET MVC 5 Web Application**. Nommez l'application **GestionnaireContacts**, puis cliquez sur **OK**.

	![New Project dialog box](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG)]

1. Dans la boîte de dialogue **New ASP.NET Project**, sélectionnez le modèle **MVC**, activez la case à cocher **Web API**, puis cliquez sur **Change Authentication**.

	![Boîte de dialogue New ASP.NET Project](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

1. Dans la boîte de dialogue **Modifier l'authentification**, cliquez sur **Aucune authentification**, puis sur **OK**.

	![Aucune authentification](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/GS13noauth.png)

	L’exemple d’application que vous créez ne sera pas doté de fonctionnalités nécessitant la connexion de l’utilisateur. Pour plus d’informations sur l’implémentation de fonctionnalités d’authentification et d’autorisation, consultez la section [Étapes suivantes](#nextsteps) à la fin de ce didacticiel.

1. Dans la boîte de dialogue **New ASP.NET Project**, cliquez sur **OK**.

	![Boîte de dialogue New ASP.NET Project](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

Si vous n’êtes pas déjà connecté à Azure, vous serez invité à vous connecter.

1. L’Assistant Configuration suggère un nom unique basé sur *ContactManager* (voir l’image ci-dessous). Sélectionnez une région proche de chez vous. Vous pouvez utiliser [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") pour trouver le centre de données affichant la plus faible latence. 
2. Si vous n’avez pas créé de serveur de base de données, sélectionnez **Créer un serveur**, entrez un nom d’utilisateur et un mot de passe de base de données.

	![Configuration du site Web Azure](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configAz.PNG)

Si vous avez un serveur de bases de données, utilisez-le pour créer une base de données. Les serveurs de base de données sont une ressource essentielle, et vous souhaitez généralement créer plusieurs bases de données sur le même serveur à des fins de test et développement au lieu de créer un serveur de base de données par base de données. Assurez-vous que votre site web et la base de données sont dans la même région.

![Configuration du site Web Azure](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configWithDB.PNG)

### Définition de l'en-tête et du pied de page de la page


1. Dans l'**Explorateur de solutions**, développez le dossier *Views\\Shared* et ouvrez le fichier *\_Layout.cshtml*.

	![\_Layout.cshtml in Solution Explorer][newapp004]

1. Remplacez le contenu du fichier *Views\\Shared\_Layout.cshtml* par le code suivant :


		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="utf-8" />
		    <title>@ViewBag.Title - Contact Manager</title>
		    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
		    <meta name="viewport" content="width=device-width" />
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		</head>
		<body>
		    <header>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
		            </div>
		        </div>
		    </header>
		    <div id="body">
		        @RenderSection("featured", required: false)
		        <section class="content-wrapper main-content clear-fix">
		            @RenderBody()
		        </section>
		    </div>
		    <footer>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		            </div>
		        </div>
		    </footer>
		    @Scripts.Render("~/bundles/jquery")
		    @RenderSection("scripts", required: false)
		</body>
		</html>
			
Le balisage ci-dessus remplace le nom de l'application « My ASP.NET App » par « Gestionnaire de contacts » et supprime les liens vers les pages **Accueil**, **À propos de** et **Contact**.

### Exécution locale de l'application

1. Appuyez sur Ctrl+F5 pour exécuter l’application. La page d’accueil de l’application apparaît dans le navigateur par défaut. ![Page d'accueil Liste des tâches](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG)

Voilà, vous avez fait tout ce qu'il fallait pour créer l'application que vous allez déployer dans Azure. Après cela, vous allez ajouter les fonctionnalités de base de données.

## Déploiement de l'application dans Azure

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis dans le menu contextuel, sélectionnez **Publier**.

	![Publier dans le menu contextuel du projet][PublishVSSolution]

	L'Assistant **Publier le site Web** s'ouvre.

12. Cliquez sur **Publier**.

![Onglet Paramètres](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/pw.png)

Visual Studio démarre le processus de copie des fichiers vers le serveur Azure. La fenêtre **Output** indique les actions de déploiement entreprises et signale la réussite du déploiement.

14. Le navigateur par défaut ouvre automatiquement l'URL du site déployé.

	L'application créée est maintenant exécutée dans le cloud.
	
	![Page d'accueil Liste des tâches exécutée dans Azure][rxz2]

## Ajout d'une base de données à l'application

À présent, vous allez mettre à jour l'application MVC pour y ajouter la capacité d'afficher et de mettre à jour les contacts, puis stocker les données dans une base de données. L'application va utiliser Entity Framework pour créer la base de données, ainsi que lire et mettre à jour les données associées.

### Ajout de classes de modèle de données pour les contacts

Commencez par créer un modèle de données simple dans le code.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier Modèles, cliquez sur **Ajouter**, puis sur **Classe**.

	![Menu contextuel Ajouter une classe aux modèles][adddb001]

2. Dans la boîte de dialogue **Ajouter un nouvel élément**, nommez le nouveau fichier de classe *Contact.cs*, puis cliquez sur **Ajouter**.

	![Boîte de dialogue Ajouter un nouvel élément][adddb002]

3. Remplacez le contenu du fichier Contacts.cs par le code suivant.

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
				public string Email { get; set; }
				public string Twitter { get; set; }
				public string Self
        		{
            		get { return string.Format(CultureInfo.CurrentCulture,
				         "api/contacts/{0}", this.ContactId); }
            		set { }
        		}
    		}
		}

La classe **Contacts** définit les données que vous allez stocker pour chaque contact, ainsi que la clé primaire ContactID requise par la base de données. Pour plus d'informations sur les modèles de données, consultez la section [Étapes suivantes](#nextsteps) à la fin de ce didacticiel.

### Création de pages Web permettant aux utilisateurs de l'application d'utiliser des contacts

La fonctionnalité de génération de modèle automatique ASP.NET MVC peut générer automatiquement un code qui effectue les actions CRUD (Create, Read, Update et Delete, ou Créer, Lire, Mettre à jour et Supprimer).

## Ajout d'un contrôleur et affichage des données

1. Dans l'**Explorateur de solutions**, développez le dossier Contrôleurs.

3. Développez le projet **(Ctrl+Maj+B)** (vous devez développer le projet avant d'utiliser le mécanisme de génération de modèle automatique).

4. Cliquez avec le bouton droit sur le dossier Contrôleurs, cliquez sur **Ajouter**, puis sur **Contrôleur**.

	![Ajouter un contrôleur dans le menu contextuel du dossier Contrôleurs][addcode001]

1. Dans la boîte de dialogue **Add Scaffold**, sélectionnez **MVC Controller with views, using Entity Framework** et cliquez sur **Ajouter**.

 ![Ajouter un contrôleur](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG)

6. Définissez le nom du contrôleur sur **HomeController**. Sélectionnez **Contact** comme classe de modèle. Cliquez sur le bouton **New data context** et acceptez la valeur par défaut « ContactManager.Models.ContactManagerContext » pour le **Type du nouveau contexte de données**. Cliquez sur **Ajouter**.

	![Boîte de dialogue Ajouter un contrôleur](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG)

	Une boîte de dialogue affiche le message suivant : « Un fichier ayant le nom HomeController existe déjà. Voulez-vous le remplacer ? ». Cliquez sur **Oui**. Nous allons remplacer le contrôleur d'accueil créé avec le nouveau projet. Nous allons utiliser le nouveau contrôleur d'accueil pour notre liste de contacts.

	Visual Studio crée des méthodes de contrôleur et des vues pour les opérations de base de données CRUD des objets **Contact**.

## Activation des migrations, création de la base de données, ajout d'exemples de données et d'un initialiseur de données ##

L'étape suivante consiste à activer la fonctionnalité [Migrations Code First](http://curah.microsoft.com/55220) pour créer la base de données en fonction du modèle de données que vous avez créé.

1. Dans le menu **Outils**, sélectionnez **Gestionnaire de package de bibliothèques**, puis **Console du Gestionnaire de package**.

	![Console du Gestionnaire de package dans le menu Outils][addcode008]

2. Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :

		enable-migrations 
  
	La commande **enable-migrations** crée un dossier *Migrations* dans lequel elle place un fichier *Configuration.cs* que vous pouvez modifier pour configurer les migrations.

2. Dans la fenêtre **Console du Gestionnaire de package**, entrez la commande suivante :

		add-migration Initial

	La commande **add-migration Initial** génère une classe nommée **&lt;date\_stamp&gt;Initial** qui crée la base de données. Le premier paramètre (*Initial*) est arbitraire et permet de créer le nom du fichier. Les nouveaux fichiers de classe sont affichés dans l'**Explorateur de solutions**.

	Dans la classe **Initial**, la méthode **Up** crée la table des contacts et la méthode **Down** (utilisée lorsque vous voulez revenir à l'état précédent) annule cette table.

3. Ouvrez le fichier *Migrations\\Configuration.cs*.

4. Ajoutez les espaces de noms suivants.

    	 using ContactManager.Models;

5. Remplacez la méthode *Seed* par le code suivant :
		
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
                   Twitter = "debra_example"
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                    Twitter = "thorsten_example"
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                    Twitter = "yuhong_example"
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                    Twitter = "jon_example"
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                    Twitter = "diliana_example"
                }
                );
        }

	Le code ci-dessus initialise la base de données avec les informations de contact. Pour plus d'informations sur l'amorçage de la base de données, consultez la page [Débogage des bases de données Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


1. Dans la **Console du Gestionnaire de package**, entrez la commande suivante :

		update-database

	![Commandes de la Console du Gestionnaire de package][addcode009]

	La commande **update-database** exécute la première migration qui entraîne la création de la base de données. Par défaut, la base de données est créée en tant que base de données SQL Server Express LocalDB.

1. Appuyez sur Ctrl+F5 pour exécuter l'application.

L'application affiche les données amorcées, ainsi que des liens pour les modifier, les supprimer ou obtenir des informations supplémentaires.

![Affichage MVC des données][rxz3]

## Modifier la vue

1. Ouvrez le fichier *Views\\Home\\Index.cshtml*. Dans l'étape suivante, nous allons remplacer le balisage généré par un code utilisant [jQuery](http://jquery.com/) et [Knockout.js](http://knockoutjs.com/). Ce nouveau code récupère la liste des contacts à l’aide de l’API web et de JSON, puis relie les données de contact à l’interface utilisateur à l’aide de knockout.js. Pour plus d’informations, consultez la section [Étapes suivantes](#nextsteps) à la fin de ce didacticiel. 


2. Remplacez le contenu du fichier par le code suivant.

		@model IEnumerable<ContactManager.Models.Contact>
		@{
		    ViewBag.Title = "Home";
		}
		@section Scripts {
		    @Scripts.Render("~/bundles/knockout")
		    <script type="text/javascript">
		        function ContactsViewModel() {
		            var self = this;
		            self.contacts = ko.observableArray([]);
		            self.addContact = function () {
		                $.post("api/contacts",
		                    $("#addContact").serialize(),
		                    function (value) {
		                        self.contacts.push(value);
		                    },
		                    "json");
		            }
		            self.removeContact = function (contact) {
		                $.ajax({
		                    type: "DELETE",
		                    url: contact.Self,
		                    success: function () {
		                        self.contacts.remove(contact);
		                    }
		                });
		            }

		            $.getJSON("api/contacts", function (data) {
		                self.contacts(data);
		            });
		        }
		        ko.applyBindings(new ContactsViewModel());	
		</script>
		}
		<ul id="contacts" data-bind="foreach: contacts">
		    <li class="ui-widget-content ui-corner-all">
		        <h1 data-bind="text: Name" class="ui-widget-header"></h1>
		        <div><span data-bind="text: $data.Address || 'Address?'"></span></div>
		        <div>
		            <span data-bind="text: $data.City || 'City?'"></span>,
		            <span data-bind="text: $data.State || 'State?'"></span>
		            <span data-bind="text: $data.Zip || 'Zip?'"></span>
		        </div>
		        <div data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>
		        <div data-bind="ifnot: $data.Email"><span>Email?</span></div>
		        <div data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>
		        <div data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>
		        <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
		    </li>
		</ul>
		<form id="addContact" data-bind="submit: addContact">
		    <fieldset>
		        <legend>Add New Contact</legend>
		        <ol>
		            <li>
		                <label for="Name">Name</label>
		                <input type="text" name="Name" />
		            </li>
		            <li>
		                <label for="Address">Address</label>
		                <input type="text" name="Address" >
		            </li>
		            <li>
		                <label for="City">City</label>
		                <input type="text" name="City" />
		            </li>
		            <li>
		                <label for="State">State</label>
		                <input type="text" name="State" />
		            </li>
		            <li>
		                <label for="Zip">Zip</label>
		                <input type="text" name="Zip" />
		            </li>
		            <li>
		                <label for="Email">E-mail</label>
		                <input type="text" name="Email" />
		            </li>
		            <li>
		                <label for="Twitter">Twitter</label>
		                <input type="text" name="Twitter" />
		            </li>
		        </ol>
		        <input type="submit" value="Add" />
		    </fieldset>
		</form>

3. Cliquez avec le bouton droit sur le dossier Contenu, puis cliquez sur **Ajouter** et sur **Nouvel élément...**.

	![Ajouter une feuille de style dans le menu contextuel du dossier Contenu][addcode005]

4. Dans la boîte de dialogue **Ajouter un nouvel élément**, entrez **Style** dans la zone de recherche située en haut à droite, puis sélectionnez **Feuille de style**. ![Boîte de dialogue Ajouter un nouvel élément][rxStyle]

5. Nommez le fichier *Contacts.css*, puis cliquez sur **Ajouter**. Remplacez le contenu du fichier par le code suivant.
    
        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }

	Nous allons utiliser cette feuille de style pour la disposition, les couleurs et les styles de l'application Gestionnaire de contacts.

6. Ouvrez le fichier *App\_Start\\BundleConfig.cs*.


7. Ajoutez le code suivant pour inscrire le plug-in [Knockout](http://knockoutjs.com/index.html "KO").

		bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
		            "~/Scripts/knockout-{version}.js"));
	Cet exemple utilise Knockout pour simplifier le code JavaScript dynamique gérant les modèles d'écran.

8. Modifiez l'entrée contents/css pour inscrire la feuille de style *contacts.css*. Remplacez la ligne suivante :

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
Par :

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

1. Dans la console du Gestionnaire de package, exécutez la commande suivante pour installer Knockout.

	Install-Package knockoutjs
## Ajouter un contrôleur pour l’interface d’API Web Restful

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur Contrôleurs, cliquez sur **Ajouter**, puis sur **Contrôleur...**

1. Dans la boîte de dialogue **Add Scaffold**, entrez **Web API 2 Controller with actions, using Entity Framework**, puis cliquez sur **Ajouter**.

	![Ajouter un contrôleur API](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG)

4. Dans la boîte de dialogue **Ajouter un contrôleur**, nommez votre contrôleur « ContactsController ». Sélectionnez « Contact (ContactManager.Models) » en tant que **Classe de modèle**. Conservez la valeur par défaut pour la **classe du contexte des données**.

6. Cliquez sur **Ajouter**.

### Exécution locale de l'application

1. Appuyez sur Ctrl+F5 pour exécuter l’application.

	![Page d'index][intro001]

2. Entrez un contact, puis cliquez sur **Ajouter**. L'application revient à la page d'accueil et affiche le contact que vous venez d'entrer.

	![Page d'index avec éléments de liste des tâches][addwebapi004]

3. Dans le navigateur, ajoutez **/api/contacts** à l'URL.

	L’URL ressemblera alors à http://localhost:1234/api/contacts. L'API Web RESTful ajoutée revient aux contacts stockés. Firefox et Chrome affichent les données au format XML.

	![Page d'index avec éléments de liste des tâches][rxFFchrome]
	

	IE vous invite à ouvrir ou enregistrer les contacts.

	![Boîte de dialogue Enregistrer de l'API Web][addwebapi006]
	
	
	Vous pouvez ouvrir les contacts renvoyés dans le Bloc-notes ou un navigateur.
	
	Ce résultat peut être utilisé par une autre application, comme une page Web ou une application mobile.

	![Boîte de dialogue Enregistrer de l'API Web][addwebapi007]

	**Avertissement de sécurité** : pour l’instant, votre application n’est pas sécurisée et elle est vulnérable aux falsifications de requête intersites. Nous résoudrons ce problème plus tard dans ce didacticiel. Pour plus d'informations, consultez la page [Prévention des falsifications de requête intersites][prevent-csrf-attacks].
## Ajouter une protection XSRF

Une falsification de requête intersites (également connue sous le nom de XSRF ou CSRF) est une attaque contre des applications hébergées sur le Web durant lesquelles un site Web malveillant peut influencer l'interaction entre un navigateur client et un site Web approuvé par ce navigateur. Ces attaques sont rendues possibles par le fait que les navigateurs Web envoient automatiquement des jetons d'authentification avec chaque requête vers un site Web. L'exemple classique est le cookie d'authentification, comme le ticket d'authentification d'ASP.NET. Cependant, les sites Web utilisant un mécanisme d'authentification persistant (comme l'authentification Windows, Basic, etc.) peuvent être visés par ces attaques.

Une attaque XSRF est différente d'une attaque par hameçonnage (ou « phishing »). Les attaques par hameçonnage requièrent une interaction avec la victime. Dans ce genre d'attaque, un site Web malveillant va imiter un site Web cible et la victime est dupée pour fournir des informations sensibles à l'attaquant. Dans une attaque XSRF, il n'y a généralement pas d'interaction avec la victime. L’attaquant se repose plutôt sur le fait que le navigateur envoie automatiquement tous les cookies utiles au site Web de destination.

Pour plus d’informations, consultez la page [Projet de sécurité d’application web ouvert](https://www.owasp.org/index.php/Main_Page) (ou OWASP pour « Open Web Application Security Project ») (en anglais) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **ContactManager**, cliquez sur **Ajouter**, puis sur **Classe**.

2. Nommez le fichier *ValidateHttpAntiForgeryTokenAttribute.cs* et ajoutez le code suivant :

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Helpers;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using System.Web.Mvc;
        namespace ContactManager.Filters
        {
            public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
            {
                public override void OnAuthorization(HttpActionContext actionContext)
                {
                    HttpRequestMessage request = actionContext.ControllerContext.Request;
                    try
                    {
                        if (IsAjaxRequest(request))
                        {
                            ValidateRequestHeader(request);
                        }
                        else
                        {
                            AntiForgery.Validate();
                        }
                    }
                    catch (HttpAntiForgeryException e)
                    {
                        actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                    }
                }
                private bool IsAjaxRequest(HttpRequestMessage request)
                {
                    IEnumerable<string> xRequestedWithHeaders;
                    if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                    {
                        string headerValue = xRequestedWithHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(headerValue))
                        {
                            return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                        }
                    }
                    return false;
                }
                private void ValidateRequestHeader(HttpRequestMessage request)
                {
                    string cookieToken = String.Empty;
                    string formToken = String.Empty;
					IEnumerable<string> tokenHeaders;
                    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                    {
                        string tokenValue = tokenHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(tokenValue))
                        {
                            string[] tokens = tokenValue.Split(':');
                            if (tokens.Length == 2)
                            {
                                cookieToken = tokens[0].Trim();
                                formToken = tokens[1].Trim();
                            }
                        }
                    }
                    AntiForgery.Validate(cookieToken, formToken);
                }
            }
        }

1. Ajoutez l'instruction *using* suivante au contrôleur de contacts pour accéder à l'attribut **[ValidateHttpAntiForgeryToken]**.

	using ContactManager.Filters;

1. Ajoutez l’attribut **[ValidateHttpAntiForgeryToken]** aux méthodes Post du **ContactsController** pour le protéger des menaces XSRF. Vous l'ajouterez aux méthodes d'action « PutContact », « PostContact » et **DeleteContact**.

	[ValidateHttpAntiForgeryToken] public IHttpActionResult PutContact(int id, Contact contact) {

1. Mettez à jour la section *Scripts* du fichier *Views\\Home\\Index.cshtml* pour inclure le code d’obtention des jetons XSRF.

         @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                @functions{
                   public string TokenHeaderValue()
                   {
                      string cookieToken, formToken;
                      AntiForgery.GetTokens(null, out cookieToken, out formToken);
                      return cookieToken + ":" + formToken;                
                   }
                }

               function ContactsViewModel() {
                  var self = this;
                  self.contacts = ko.observableArray([]);
                  self.addContact = function () {

                     $.ajax({
                        type: "post",
                        url: "api/contacts",
                        data: $("#addContact").serialize(),
                        dataType: "json",
                        success: function (value) {
                           self.contacts.push(value);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
                     });

                  }
                  self.removeContact = function (contact) {
                     $.ajax({
                        type: "DELETE",
                        url: contact.Self,
                        success: function () {
                           self.contacts.remove(contact);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }

                     });
                  }

                  $.getJSON("api/contacts", function (data) {
                     self.contacts(data);
                  });
               }
               ko.applyBindings(new ContactsViewModel());
            </script>


## Publier la mise à jour de l’application dans Azure et Base de données SQL

Pour publier l'application, répétez la procédure suivie précédemment.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

	![Publier][rxP]

5. Cliquez sur l'onglet **Paramètres**.
	

1. Sous **ContactsManagerContext(ContactsManagerContext)**, cliquez sur l'icône **v** pour remplacer *Remote connection string* par la chaîne de connexion pour la base de données de contacts. Cliquez sur **ContactDB**.

	![Paramètres](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

7. Activez la case à cocher pour **Execute Code First Migrations (runs on application start)**.

1. Cliquez sur **Suivant**, puis sur **Aperçu**. Visual Studio affiche une liste des fichiers ajoutés ou mis à jour.

8. Cliquez sur **Publier**. Une fois le déploiement terminé, le navigateur ouvre la page d'accueil de l'application.

	![Page d'index sans contacts][intro001]

	Le processus de publication Visual Studio a configuré automatiquement la chaîne de connexion du fichier *Web.config* déployé pour qu'elle pointe vers la base de données SQL. Il a également configuré les migrations Code First pour mettre automatiquement à niveau la base de données vers la dernière version, dès le premier accès de l'application à la base de données après le déploiement.

	Ainsi, Code First a créé la base de données en exécutant le code de la classe **Initial** que vous avez créée précédemment. Cela s'est produit lors du premier accès de l'application à la base de données après le déploiement.

9. Entrez un contact comme vous l'avez fait lorsque vous avez exécuté l'application en local, pour vérifier que le déploiement de la base de données est correct.

Lorsque vous constatez que l'élément que vous entrez est enregistré et s'affiche sur la page du Gestionnaire de contacts, vous savez qu'il a été stocké dans la base de données.

![Page d'index avec contacts][addwebapi004]

L'application est à présent exécutée dans le cloud et utilise la base de données SQL Database pour stocker ses données. Lorsque vous avez fini de tester l'application dans Azure, supprimez-la. L'application est publique et ne dispose pas de mécanismes permettant d'en limiter l'accès.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Étapes suivantes

Une véritable application requiert une authentification et une autorisation. Vous devez utiliser la base de données d'appartenance pour cela. Le didacticiel [Déploiement d'une application ASP.NET MVC sécurisée avec une fonctionnalité d'appartenance, OAuth et une base de données SQL vers un site Web Windows Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) est basé sur ce didacticiel et vous montre comment déployer une application Web avec la base de données d'appartenance.

Une autre méthode pour stocker des données dans une application Azure consiste à utiliser le stockage Azure, qui permet de stocker des données non relationnelles sous la forme d'objets blob et de tables. Pour plus d'informations sur les API Web, ASP.NET MVC et Windows Azure, consultez les liens suivants.
 

* [Mise en route d’Entity Framework avec MVC][EFCodeFirstMVCTutorial] (en anglais)
* [Introduction à ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) (en anglais)
* [Votre première API Web ASP.NET](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) (en anglais)
* [Débogage de WAWS](web-sites-dotnet-troubleshoot-visual-studio.md)

Ce didacticiel et son exemple d'application ont été écrits par [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) avec l'aide de Tom Dykstra et Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

N'hésitez pas à nous transmettre vos commentaires sur ce qui vous a plu et ce qui pourrait être amélioré... pas seulement à propos de ce didacticiel, mais aussi en ce qui concerne les produits présentés ici. Vos commentaires nous aideront à orienter nos améliorations. Nous aimerions particulièrement savoir si l'automatisation du processus de configuration et de déploiement de la base de données d'appartenance vous intéresse.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Add Roles to the Membership Database]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[adddb]: #bkmk_addadatabase
[addcontroller]: #bkmk_addcontroller
[addwebapi]: #bkmk_addwebapi
[deploy2]: #bkmk_deploydatabaseupdate

<!-- links -->
[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[dbcontext-link]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx


<!-- images-->
[rxE]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxE.png
[rxP]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/
[rxb2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxb2.png
[rxz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz.png
[rxzz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxzz.png
[rxz2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
[rxz3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
[rxStyle]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
[rxz4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz4.png
[rxz44]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz44.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxPrevDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxOverwrite.png
[rxPWS]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPWS.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxAddApiController]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxAddApiController.png
[rxFFchrome]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
[intro001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
[setup009]: ../Media/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
[firsdeploy007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
[adddb001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-controller-dialog.png
[addcode004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-index-context.png
[addcode005]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
[addcode007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-bundleconfig-context.png
[addcode008]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
[addwebapi004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
[addwebapi006]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
[addwebapi007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
[Add XSRF Protection]: #xsrf
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[prevent-csrf-attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks
 

<!---HONumber=September15_HO1-->