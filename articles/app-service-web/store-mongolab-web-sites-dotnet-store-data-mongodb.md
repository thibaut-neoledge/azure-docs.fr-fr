<properties 
	pageTitle="Création d'une application web .NET sur Azure avec MongoDB à l'aide du module complémentaire MongoLab" 
	description="Apprenez à créer une application web ASP.NET sur Azure App Service qui stocke des données dans MongoDB hébergé par MongoLab." 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="chrisckchang" 
	manager="partners@mongolab.com" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="chris@mongolab.com"/>



# Création d'une application web .NET sur Azure avec MongoDB à l'aide du module complémentaire MongoLab

  	<!-- The MongoLab workflow is not yet supported in the Preview Portal -->

<p><em>Par Eric Sedor, MongoLab</em></p>

Bonjour, chers aventuriers ! Bienvenue dans MongoDB-as-a-Service. Ce didacticiel présente les procédures suivantes :

1. [Configuration de la base de données][provision] : le module [MongoLab](http://mongolab.com) d'Azure Marketplace fournit une base de données MongoDB hébergée dans le cloud Azure et gérée par la plateforme de base de données cloud de MongoLab.
1. [Création de l'application][create] : il s'agit d'une simple application C# ASP.NET MVC visant à créer des notes.
1. [Déploiement de l'application][deploy] : en combinant quelques astuces de configuration, nous apporterons un nouveau souffle à notre code [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).
1. [Gestion de base de données][manage] : finalement, vous découvrirez le portail de gestion de base de données basé sur le Web où vous pouvez effectuer des recherches, visualiser et modifier les données facilement.

Au cours de ce didacticiel, n'hésitez pas à envoyer un e-mail à tout moment à l'adresse [support@mongolab.com](mailto:support@mongolab.com) si vous avez des questions.

## Démarrage rapide
Si vous disposez déjà d'une application web dans Azure App Service que vous voulez utiliser ou que vous connaissez un peu Azure Marketplace, utilisez cette section pour démarrer rapidement. Dans le cas contraire, consultez la section [Configuration de la base de données][provision] ci-dessous.
 
1. Ouvrez Azure Marketplace en cliquant sur **Nouveau** > **Markeplace**.  
	<!-- ![Store][button-store] -->

1. Achetez le module MongoLab.  
	![MongoLab][entry-mongolab]

1. Cliquez sur le module MongoLab dans la liste des modules complémentaires, puis cliquez sur **Connection Info**.  
	![ConnectionInfoButton][button-connectioninfo]

1. Copiez le MONGOLAB_URI dans votre presse-papiers.  
	![ÉcranInformationsDeConnexion][screen-connectioninfo]  
	**Cet URI contient votre nom d’utilisateur et votre mot de passe pour la base de données. Considérez ces informations comme étant sensibles, ne les partagez pas.**

1. Ajoutez la valeur à la liste Connection Strings dans le menu Configuration de votre application Azure Web :
	![ChaînesConnexionSiteWeb][focus-website-connectinfo]

1. Dans **Name**, entrez MONGOLAB_URI.

1. Dans **Value**, collez la chaîne de connexion obtenue dans la section précédente.

1. Sélectionnez **Custom** dans la liste déroulante Type (à la place de la valeur par défaut **SQLAzure**).

1. Dans Visual Studio, installez le pilote Mongo C# en sélectionnant **Outils > Gestionnaire de package de bibliothèques > Console du Gestionnaire de package**. Dans la console PM, tapez **Install-Package mongocsharpdriver** et appuyez sur **Entrée**.

1. Configurez un hook dans votre code pour obtenir votre URI de connexion MongoLab à partir d'une variable d'environnement :

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

> **Remarque :** Azure ajoute le préfixe **CUSTOMCONNSTR_** à la chaîne de connexion déclarée à l'origine, ce qui explique pourquoi le code indique **CUSTOMCONNSTR_MONGOLAB_URI.** au lieu de **MONGOLAB_URI**.

Passons maintenant au didacticiel complet...

<a name="provision"></a>
## Configuration de la base de données

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## Création de l'application

Dans cette section, vous allez créer un projet Visual Studio C# ASP.NET et découvrir l'utilisation du pilote C# MongoDB afin de créer une simple application Note. Vous voulez pouvoir visiter votre application web, rédiger une note et consulter toutes les notes qui ont été écrites.

Vous effectuerez ce développement dans Visual Studio Express 2013 pour le web.

### Création du projet
Votre exemple d'application utilisera un modèle Visual Studio pour démarrer. Assurez-vous d'utiliser .NET Framework 4.5.

1. Sélectionnez **Fichier > Nouveau projet**. La boîte de dialogue Nouveau projet s'affiche :  
	![NewProject][dialog-mongolab-csharp-newproject]

1. Sélectionnez **Installé > Modèles > Visual C# > Web**.

1. Sélectionnez **.NET Framework 4.5** dans le menu déroulant des versions de .NET.

1. Choisissez **Application MVC**.

1. Saisissez _mongoNotes_ comme **Nom du projet**. Si vous choisissez un nom différent, vous devrez modifier le code fourni dans l'ensemble du didacticiel.

1. Sélectionnez **Outils > Gestionnaire de package de bibliothèques > Console du Gestionnaire de package**. Dans la console PM, tapez **Install-Package mongocsharpdriver** et appuyez sur **Entrée**. 
	![PMConsole][focus-mongolab-csharp-pmconsole]
	Le pilote MongoDB C# est intégré au projet et la ligne suivante est automatiquement ajoutée au fichier _packages.config_ :

        < package id="mongocsharpdriver" version="1.9.2" targetFramework="net45" / >

### Ajout d'un modèle de note
Commencez par réaliser un modèle pour Notes, avec simplement une date et du texte.

1. Cliquez avec le bouton droit sur **Modèles** dans l'Explorateur de solutions et sélectionnez **Ajouter > Classe**. Nommez cette nouvelle classe *Note.cs*.

1. Remplacez le code généré automatiquement pour cette classe par les instructions suivantes :

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MongoDB.Bson.Serialization.Attributes;
        using MongoDB.Bson.Serialization.IdGenerators;
        using MongoDB.Bson;
                
        namespace mongoNotes.Models
        {
            public class Note
            {
                public Note()
                {
                    Date = DateTime.UtcNow;
                }
                
                private DateTime date;
        
                [BsonId(IdGenerator = typeof(CombGuidGenerator))]
                public Guid Id { get; set; }
        
                [BsonElement("Note")]
                public string Text { get; set; }
        
                [BsonElement("Date")]
                public DateTime Date {
                    get { return date.ToLocalTime(); }
                    set { date = value;}
                }
            }
        }

### Ajout d'une couche d'accès aux données
Il est important que vous définissiez un moyen d'accéder à MongoDB afin de récupérer et d'enregistrer les notes. Votre couche d'accès aux données utilisera le modèle Note et sera ultérieurement liée à votre HomeController.

1. Cliquez avec le bouton droit sur le projet **mongoNotes** dans l'Explorateur de solutions et sélectionnez **Ajouter > Nouveau dossier**. Nommez le dossier **DAL**.

1. Cliquez avec le bouton droit sur **DAL** dans l'Explorateur de solutions et sélectionnez **Ajouter > Classe**. Nommez cette nouvelle classe *Dal.cs*.

1. Remplacez le code généré automatiquement pour cette classe par les instructions suivantes :

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using mongoNotes.Models;
        using MongoDB.Driver;
        using System.Configuration;

        namespace mongoNotes
        {
            public class Dal : IDisposable
            {
                private MongoServer mongoServer = null;
                private bool disposed = false;
        
                private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
                MongoUrl url;
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
                    url = new MongoUrl(connectionString);
                }
           
                public List<Note> GetAllNotes()
                {
                    try
                    {
                        MongoCollection<Note> collection = GetNotesCollection();
                        return collection.FindAll().ToList<Note>();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<Note>();
                    }
                }
        
                // Creates a Note and inserts it into the collection in MongoDB.
                public void CreateNote(Note note)
                {
                    MongoCollection<Note> collection = getNotesCollectionForEdit();
                    try
                    {
                        collection.Insert(note);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private MongoCollection<Note> GetNotesCollection()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
                    return noteCollection;
                }
        
                private MongoCollection<Note> getNotesCollectionForEdit()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> notesCollection = database.GetCollection<Note>(collectionName);
                    return notesCollection;
                }
        
                # region IDisposable
        
                public void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
        
                protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                            if (mongoServer != null)
                            {
                                this.mongoServer.Disconnect();
                            }
                        }
                    }
        
                    this.disposed = true;
                }
        
                # endregion
            }
        }

1. Notez le code suivant ci-dessus :
            
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  
Ici, vous accédez à une variable d'environnement que vous configurerez plus tard. Si vous disposez d'une instance Mongo locale pour le développement, vous pouvez définir cette valeur de façon temporaire sur « localhost ».
  
  Définissez également le nom de votre base de données. Plus précisément, définissez la valeur **dbName** sur le nom que vous avez saisi lorsque vous avez configuré le module MongoLab.

1. Enfin, étudiez le code suivant dans **GetNotesCollection()** :  

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
  Vous n'avez aucune modification à apporter ici. Gardez seulement à l'esprit qu'il s'agit de la méthode d'obtention d'un objet MongoCollection permettant de réaliser des insertions, des mises à jour et des requêtes, comme celle-ci dans **GetAllNotes()** :

        collection.FindAll().ToList<Note>();

Pour plus d'informations sur l'utilisation optimale du pilote C# MongoDB, consultez le guide [Sharp Driver QuickStart](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "Démarrage rapide du pilote CSharp") sur le site mongodb.org.

### Ajout d'une vue Créer
Maintenant, vous allez ajouter une vue pour la création d'une nouvelle note.

1. Cliquez avec le bouton droit sur l'entrée **Vues > Accueil** dans l'Explorateur de solutions et sélectionnez **Ajouter > Vue**. Nommez cette nouvelle vue **Créer** et cliquez sur **Ajouter**.

1. Remplacez le code généré automatiquement pour cette vue (**Create.cshtml**) par les instructions suivantes :

        @model mongoNotes.Models.Note
        
        <script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
        
        @using (Html.BeginForm("Create", "Home")) {
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>New Note</legend>
                <h3>New Note</h3>       
                <div class="editor-label">
                    @Html.LabelFor(model => model.Text)
                </div>
                <div class="editor-field">
                    @Html.EditorFor(model => model.Text)
                </div>
               <p>
                    <input type="submit" value="Create" />
               </p>
           </fieldset>
        }

### Modification du fichier index.cshtml
Ensuite, déplacez une simple disposition pour l'affichage et la création de notes sur votre site web.

1. Ouvrez **Index.cshtml** sous **Vues > Accueil** et remplacez son contenu par les instructions suivantes :  

        @model IEnumerable<mongoNotes.Models.Note>
        
        @{
            ViewBag.Title = "Notes";
        }
        
        <h2>My Notes</h2>

        <table border="1">
            <tr>
                <th>Date</th>
                <th>Note Text</th>      
            </tr>
        
        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Date)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Text)
                </td>       
            </tr>
        }
        
        </table>
        <div>  @Html.Partial("Create", new mongoNotes.Models.Note())</div>

### Modification du fichier HomeController.cs
Enfin, votre HomeController doit instancier votre couche d'accès aux données et l'appliquer à vos vues.

1. Ouvrez **HomeController.cs** sous **Contrôleurs** dans l'Explorateur de solutions et remplacez son contenu par les instructions suivantes :  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using System.Web.Mvc;
        using mongoNotes.Models;
        using System.Configuration;
        
        namespace mongoNotes.Controllers
        {
            public class HomeController : Controller, IDisposable
            {
                private Dal dal = new Dal();
                private bool disposed = false;
                //
                // GET: /Task/
        
                public ActionResult Index()
                {
                    return View(dal.GetAllNotes());
                }
        
                //
                // GET: /Task/Create
        
                public ActionResult Create()
                {
                    return View();
                }
        
                //
                // POST: /Task/Create
        
                [HttpPost]
                public ActionResult Create(Note note)
                {
                    try
                    {
                        dal.CreateNote(note);
                        return RedirectToAction("Index");
                    }
                    catch
                    {
                        return View();
                    }
                }
        
                public ActionResult About()
                {
                    return View();
                }
        
                # region IDisposable
        
                new protected void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
        
                new protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                            this.dal.Dispose();
                        }
                    }
        
                    this.disposed = true;
                }
        
                # endregion
        
            }
        }
    
<a name="deploy"></a>
## Déploiement de l’application

Maintenant que l'application a été développée, il est temps de créer une application web dans Azure App Service pour l'héberger. Configurez cette application web et déployez le code. L'élément essentiel de cette section est l'utilisation de la chaîne de connexion MongoDB (URI). Vous allez configurer une variable d'environnement dans votre application web avec cet URI, afin de séparer l'URI du code. L'URI doit être traitée comme une information sensible car elle contient les identifiants de connexion à votre base de données.

### Création d'une application web et obtention du fichier de paramètres de publication
La création d'une application web dans Azure App Service est très conviviale, notamment car Azure génère automatiquement un profil de publication pour Visual Studio.

1. Dans le portail Azure, cliquez sur **New**.
	![Nouveau][button-new]

1. Sélectionnez **Calculer > Application web > Création rapide**.
	<!-- ![CreateWebApp][screen-mongolab-newwebsite] -->

1. Saisissez un préfixe d'URL. Sélectionnez le nom de votre choix, tout en gardant à l'esprit qu'il doit être unique (« mongoNotes » risque fort de ne pas être disponible).

1. Cliquez sur **Créer une application web**.

1. Quand la création de l'application web est terminée, cliquez sur son nom dans la liste des applications web. Le tableau de bord de l'application web s'affiche.
	![WebAppDashboard][screen-mongolab-websitedashboard]

1. Cliquez sur **Télécharger le profil de publication** sous **aperçu rapide**, puis enregistrez le fichier .PublishSettings dans un répertoire de votre choix.
	![DownloadPublishProfile][button-website-downloadpublishprofile]

Vous pouvez aussi configurer une application web directement à partir de Visual Studio. Quand vous liez votre compte Azure à Visual Studio, suivez les invites pour configurer une application web à partir de cet emplacement. Une fois terminé, il vous suffit de cliquer avec le bouton droit sur le nom du projet dans l'Explorateur de solutions pour le déployer dans Azure. Vous devrez quand même configurer la chaîne de connexion MongoLab, comme détaillé dans la procédure ci-dessous.

### Accédez à la chaîne de connexion MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

### Ajoutez la chaîne de connexion aux variables d'environnement de l'application web

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

### Publication de l'application web
1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **mongoNotes** dans l'Explorateur de solutions et sélectionnez **Publier**. La boîte de dialogue Publier s'affiche :  
	<!-- ![Publish][dialog-mongolab-vspublish] -->

1. Cliquez sur **Importer** et sélectionnez le fichier .PublishSettings depuis le répertoire de téléchargement de votre choix. Ce fichier alimente automatiquement les valeurs dans la boîte de dialogue Publier.

1. Cliquez sur **Valider la connexion** pour tester le fichier.

1. Une fois la validation réussie, cliquez sur **Publier**. Une fois la publication terminée, un nouvel onglet du navigateur s'ouvre et l'application web s'affiche.

1. Saisissez un texte de note, cliquez sur **Créer** et voyez le résultat !
	![HelloMongoAzure][screen-mongolab-sampleapp]

<a name="manage"></a>
## Gestion de la base de données

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

Félicitations ! Vous venez de lancer une application C# ASP.NET utilisant une base de données MongoDB hébergée sur MongoLab ! Maintenant que vous disposez d'une base de données MongoLab, vous pouvez contacter [support@mongolab.com](mailto:support@mongolab.com) pour toute question ou problème relatif à votre base de données ainsi que pour obtenir de l'aide concernant MongoDB ou le pilote C# lui-même. Bonne continuation !

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[screen-mongolab-sampleapp]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-sampleapp.png
[dialog-mongolab-vspublish]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-vspublish.png
[button-website-downloadpublishprofile]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-website-downloadpublishprofile.png
[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-new.png
[dialog-mongolab-csharp-newproject]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-newproject.png
[dialog-mongolab-csharp-projecttemplate]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-projecttemplate.png
[focus-mongolab-csharp-pmconsole]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-csharp-pmconsole.png
[button-store]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage

 

<!----HONumber=62-->