<properties linkid="develop-net-tutorials-website-with-mongodb-mongolab" urlDisplayName="Website with MongoDB on MongoLab" pageTitle="Create a Website that uses MongoDB on MongoLab (.NET)" metaKeywords="" description="Learn how to create an Azure website that stores data in MongoDB hosted by MongoLab." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create a C# ASP.NET Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="eric@mongolab.com" />

# Création d'une application C# ASP.NET sur Azure avec MongoDB à l'aide du module MongoLab

*Par Eric Sedor, MongoLab*

Bonjour, chers aventuriers ! Bienvenue dans MongoDB-as-a-Service. Ce didacticiel présente les procédures suivantes :

1.  [Configuration de la base de données][Configuration de la base de données] : le module [MongoLab][MongoLab] de l'Azure Store fournit une base de données MongoDB hébergée dans le cloud Azure et gérée par la plateforme de base de données cloud de MongoLab.
2.  [Création de l'application][Création de l'application] : il s'agit d'une simple application C# ASP.NET MVC visant à créer des notes.
3.  [Déploiement de l'application][Déploiement de l'application] : en combinant quelques astuces de configuration, nous apporterons un nouveau souffle à notre code.
4.  [Gestion de base de données][Gestion de base de données] : finalement, vous découvrirez le portail de gestion de base de données basé sur le Web où vous pouvez effectuer des recherches, visualiser et modifier les données facilement.

Au cours de ce didacticiel, n'hésitez pas à envoyer un e-mail à tout moment à l'adresse [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] si vous avez des questions.

## Démarrage rapide

Si vous possédez déjà une application Azure et un site Web avec lesquels vous souhaitez travailler ou si vous connaissez déjà l'Azure Store, cette section vous permettra de démarrer rapidement. Dans le cas contraire, consultez la section [Configuration de la base de données][Configuration de la base de données] ci-dessous.

1.  Ouvrez l'Azure Store.
	![Store][Store]

2.  Achetez le module MongoLab.
    ![MongoLab][1]

3.  Cliquez sur le module MongoLab dans la liste des modules complémentaires, puis cliquez sur **Connection Info**.
    ![ConnectionInfoButton][ConnectionInfoButton]
4.  Copiez le fichier MONGOLAB\_URI dans votre presse-papiers.<br />
	![ConnectionInfoScreen][ConnectionInfoScreen]<br />
    **Cet URI contient votre nom d’utilisateur et votre mot de passe. Considérez ces informations comme étant sensibles, ne les partagez pas.**
5.  Ajoutez la valeur à la liste Connection Strings dans le menu Configuration de votre application Azure Web :<br />
	![WebSiteConnectionStrings][WebSiteConnectionStrings]
6.  Dans **Name**, entrez MONGOLAB\_URI.
7.  Dans **Value**, collez la chaîne de connexion obtenue dans la section précédente.
8.  Sélectionnez **Custom** dans la liste déroulante Type (à la place de la valeur par défaut **SQLAzure**).
9.  Dans Visual Studio, installez le pilote Mongo C# en sélectionnant **Outils \> Gestionnaire de package de bibliothèques \> Console du Gestionnaire de package**. Dans la console PM, tapez **Install-Package mongocsharpdriver** et appuyez sur **Entrée**.
10. Configurez un hook dans votre code pour obtenir votre URI de connexion MongoLab à partir d'une variable d'environnement :

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

    Remarque : Azure ajoute le préfixe **CUSTOMCONNSTR\_** à la chaîne de connexion déclarée à l'origine, ce qui explique pourquoi le code indique **CUSTOMCONNSTR\_MONGOLAB\_URI.** au lieu de **MONGOLAB\_URI**.

Passons maintenant au didacticiel complet...

## <a name="provision"></a>Configuration de la base de données

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

## <a name="create"></a>Création de l'application

Dans cette section, vous allez créer un projet Visual Studio C# ASP.NET et découvrir l'utilisation du pilote C# MongoDB afin de créer une simple application Note. Vous souhaitez pouvoir visiter votre site Web, rédiger une note et consulter toutes les notes qui ont été écrites.

Vous effectuerez ce développement dans Visual Studio Express 2012 pour le Web.

### Création du projet

Votre exemple d'application utilisera un modèle Visual Studio pour démarrer. Assurez-vous d'utiliser .NET Framework 4.0.

1.  Sélectionnez **Fichier \> Nouveau projet**. La boîte de dialogue Nouveau projet s'affiche :<br />
    ![NewProject][NewProject]
2.  Sélectionnez **Installé \> Modèles \> Visual C# \> Web**.
3.  Sélectionnez **.NET Framework 4** dans le menu déroulant des versions de .NET (*remarque : Framework 4.5 ne fonctionne pas à l'heure actuelle*).
    ![ProjectFramework][ProjectFramework]
4.  Sélectionnez **Application Web ASP.NET MVC 4**.
5.  Saisissez *mongoNotes* comme **Nom du projet**. Si vous choisissez un nom différent, vous devrez modifier le code fourni dans l'ensemble du didacticiel.
6.  Cliquez sur **OK**. La boîte de dialogue Modèle de projet s'affiche :<br />
    ![ProjectTemplate][ProjectTemplate]
7.  Sélectionnez **Application Internet** et cliquez sur **OK**. Le projet est créé.
8.  Sélectionnez **Outils \> Gestionnaire de package de bibliothèques \> Console du Gestionnaire de package**. Dans la console du Gestionnaire de package, saisissez **Install-Package mongocsharpdriver** et appuyez sur **Entrée**.
   
    ![PMConsole][PMConsole]
    Le pilote MongoDB C# est intégré au projet et la ligne suivante est automatiquement ajoutée au fichier *packages.config* :

        < package id="mongocsharpdriver" version="1.8" targetFramework="net40" / >

### Ajout d'un modèle de note

Commencez par réaliser un modèle pour Notes, avec simplement une date et du texte.

1.  Cliquez avec le bouton droit sur **Modèles** dans l'Explorateur de solutions et sélectionnez **Ajouter \> Classe**. Nommez cette nouvelle classe *Note.cs*.
2.  Remplacez le code généré automatiquement pour cette classe par les instructions suivantes :

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

1.  Cliquez avec le bouton droit sur le projet **mongoNotes** dans l'Explorateur de solutions et sélectionnez **Ajouter \> Nouveau dossier**. Nommez le dossier **DAL**.
2.  Cliquez avec le bouton droit sur **DAL** dans l'Explorateur de solutions et sélectionnez **Ajouter \> Classe**. Nommez cette nouvelle classe *Dal.cs*.
3.  Remplacez le code généré automatiquement pour cette classe par les instructions suivantes :

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
                MongoUrl url = new MongoUrl(connectionString);

                private string dbName = "myMongoApp";
                private string collectionName = "Notes";

                // Default constructor.        
                public Dal()
                {
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

4.  Notez le code suivant ci-dessus :

        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  

    Ici, vous accédez à une variable d'environnement que vous configurerez plus tard. Si vous disposez d'une instance Mongo locale pour le développement, vous pouvez définir cette valeur de façon temporaire sur « localhost ».

	Définissez également le nom de votre base de données. Plus précisément, définissez la valeur **dbName** sur le nom que vous avez saisi lorsque vous avez configuré le module MongoLab.

1.  Enfin, étudiez le code suivant dans **GetNotesCollection()** :

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);

    Vous n'avez aucune modification à apporter ici. Gardez seulement à l'esprit qu'il s'agit de la méthode d'obtention d'un objet MongoCollection permettant de réaliser des insertions, des mises à jour et des requêtes, comme celle-ci dans **GetAllNotes()** :

        collection.FindAll().ToList<Note>();

Pour plus d'informations sur l'utilisation optimale du pilote C# MongoDB, consultez le guide [Sharp Driver Quick Start][Sharp Driver Quick Start] sur le site mongo dB.org.

### Ajout d'une vue Créer

Maintenant, vous allez ajouter une vue pour la création d'une nouvelle note.

1.  Cliquez avec le bouton droit sur l'entrée **Vues \> Accueil** dans l'Explorateur de solutions et sélectionnez **Ajouter \> Vue**. Nommez cette nouvelle vue **Créer** et cliquez sur **Ajouter**.
2.  Remplacez le code généré automatiquement pour cette vue (**Create.cshtml**) par les instructions suivantes :

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

Ensuite, déplacez une simple disposition pour l'affichage et la création de notes sur votre site Web.

1.  Ouvrez **Index.cshtml** sous **Vues \> Accueil** et remplacez son contenu par les instructions suivantes :

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

1.  Ouvrez **HomeController.cs** sous **Contrôleurs** dans l'Explorateur de solutions et remplacez son contenu par les instructions suivantes :

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

## <a name="deploy"></a>Déploiement de l’application

Maintenant que l'application a été développée, il est temps de créer un site Web Azure pour l'héberger. Configurez le site Web et déployez le code. L'élément essentiel de cette section est l'utilisation de la chaîne de connexion MongoDB (URI). Vous allez configurer une variable d'environnement dans votre site Web avec cet URI, afin de conserver l'URI séparé du code. L'URI doit être traitée comme une information sensible car elle contient les identifiants de connexion à votre base de données.

### Création d'un site Web et obtention du fichier de paramètres de publication

La création d'un site Web dans Azure est très conviviale, notamment car Azure génère automatiquement un profil de publication pour Visual Studio.

1.  Dans le portail Azure, cliquez sur **New**.<br />
    ![New][New]
2.  Sélectionnez **Compute \> Web Site \> Quick Create**.<br />
    ![CreateSite][CreateSite]
3.  Saisissez un préfixe d'URL. Sélectionnez le nom de votre choix, tout en gardant à l'esprit qu'il doit être unique (« mongoNotes » risque fort de ne pas être disponible).
4.  Cliquez sur **Create Website**.
5.  Une fois le site Web créé, cliquez sur le nom du site Web dans la liste des sites Web. Le tableau de bord du site Web s'affiche.<br />
    ![WebSiteDashboard][WebSiteDashboard]
6.  Cliquez sur **Download publish profile** sous **quick glance**, puis enregistrez le fichier .PublishSettings dans un répertoire de votre choix.<br />
    ![DownloadPublishProfile][DownloadPublishProfile]

### Accédez à la chaîne de connexion MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

### Ajoutez la chaîne de connexion aux variables d'environnement du site Web

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

### Publication du site Web

1.  Dans Visual Studio, cliquez avec le bouton droit sur le projet **mongoNotes** dans l'Explorateur de solutions et sélectionnez **Publier**. La boîte de dialogue Publier s'affiche :<br />
    ![Publish][Publish]
2.  Cliquez sur **Importer** et sélectionnez le fichier .PublishSettings depuis le répertoire de téléchargement de votre choix. Ce fichier alimente automatiquement les valeurs dans la boîte de dialogue Publier.
3.  Cliquez sur **Valider la connexion** pour tester le fichier.
4.  Une fois la validation réussie, cliquez sur **Publier**. Une fois la publication terminée, un nouvel onglet du navigateur s'ouvre et le site Web s'affiche.
5.  Saisissez un texte de note, cliquez sur **Créer** et voyez le résultat !<br />
    ![HelloMongoAzure][HelloMongoAzure]

## <a name="manage"></a>Gestion de la base de données

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Félicitations ! Vous venez de lancer une application C# ASP.NET utilisant une base de données MongoDB hébergée sur MongoLab ! Maintenant que vous disposez d'une base de données MongoLab, vous pouvez contacter [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] pour toute question ou problème relatif à votre base de données ainsi que pour obtenir de l'aide concernant MongoDB ou le pilote C#. Bonne continuation !

  [Configuration de la base de données]: #provision
  [MongoLab]: http://mongolab.com
  [Création de l'application]: #create
  [Déploiement de l'application]: #deploy
  [Gestion de base de données]: #manage
  [Store]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-store.png
  [1]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/entry-mongolab.png
  [ConnectionInfoButton]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab_connectioninfo.png
  [WebSiteConnectionStrings]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-websiteconnectionstring.png
  [howto-provision-mongolab]: ../includes/howto-provision-mongolab.md
  [NewProject]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-newproject.png
  [ProjectFramework]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-dotNet-Framework4-mongolab.png
  [ProjectTemplate]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-projecttemplate.png
  [PMConsole]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-csharp-pmconsole.png
  [Sharp Driver Quick Start]: http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "Sharp Driver Quick start"
  [New]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-new.png
  [CreateSite]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-newwebsite.png
  [WebSiteDashboard]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-websitedashboard.png
  [DownloadPublishProfile]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-website-downloadpublishprofile.png
  [howto-get-connectioninfo-mongolab]: ../includes/howto-get-connectioninfo-mongolab.md
  [howto-save-connectioninfo-mongolab]: ../includes/howto-save-connectioninfo-mongolab.md
  [Publish]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-vspublish.png
  [HelloMongoAzure]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-sampleapp.png
  [howto-access-mongolab-ui]: ../includes/howto-access-mongolab-ui.md
