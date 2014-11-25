<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Build a web application with ASP.NET MVC using DocumentDB | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="hawong" />

# <a name="_Toc395809351">Création d'une application web avec ASP.NET MVC et DocumentDB</a>

# <a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Vue d'ensemble</a>

## <a name="_Toc395637759">Scénario</a>

Pour démontrer la manière dont les clients peuvent tirer parti d'Azure DocumentDB pour
stocker et interroger des documents JSON, ce document propose une
procédure pas à pas complète détaillant la création d'une application web ToDo List à l'aide d'Azure Document
DB.

Cette procédure pas à pas indique comment utiliser le service DocumentDB fourni par
Azure pour stocker les données et y accéder à partir d'une application web ASP.NET MVC
hébergée sur Azure. Elle suppose que vous avez déjà une expérience d'
ASP.NET MVC et de Sites Web Azure.

Vous apprendrez à effectuer les opérations suivantes :

1. Création et configuration d'un compte DocumentDB

2. Création d'une application ASP.NET MVC

3. Connexion à Azure DocumentDB pour une utilisation à partir de votre application web

4. Déploiement de l'application web vers Sites Web Azure

Dans cette procédure pas à pas, vous allez concevoir une simple application web
de gestion des tâches qui vous permet de créer, de récupérer et de
terminer des tâches. Ces dernières sont stockées en tant que documents JSON dans Azure
DocumentDB.

![Alt text][Alt text]

# <a name="_Toc395637760">Configuration requise</a>

Avant de suivre les instructions de cet article, vérifiez que les éléments
suivants sont installés :

Git pour Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (ou la version gratuite [Visual Studio Express][Visual Studio Express])

Kit de développement logiciel (SDK) Azure pour .NET 2.3 ou ultérieur, disponible via
[Microsoft Web Platform Installer][Microsoft Web Platform Installer]

Toutes les captures d'écran figurant dans ce document ont été prises avec
Visual Studio 2013 Update 3) et le Kit de développement logiciel (SDK) Azure pour .NET
2.4. Si votre système est configuré avec différentes versions, il est probable
que vos écrans et options ne correspondent pas totalement. Toutefois, si vous respectez la
configuration requise citée plus haut, cette solution devrait fonctionner.

# <a name="_Toc395637761">Création d'un compte de base de données DocumentDB</a>

Pour configurer un compte de base de données DocumentDB dans Azure, ouvrez le portail de gestion
Azure, puis cliquez sur la vignette Galerie Azure sur la
page d'accueil ou sur « + » dans le coin inférieur gauche de l'écran.

![Alt text][1]

Cette opération ouvre la Galerie Azure, dans laquelle vous pouvez sélectionner
l'un des nombreux services Azure disponibles. Dans la Galerie, sélectionnez « Données, stockage et
sauvegarde » dans la liste des catégories.

![Alt text][2]

Sélectionnez l'option pour Azure DocumentDB.

![Alt text][3]

Sélectionnez ensuite « Créer » en bas de l'écran.

![Alt text][4]

Le volet « Nouveau DocumentDB » s'ouvre. Vous pouvez y spécifier
le nom, la région, l'échelle, le groupe de ressources et d'autres paramètres pour votre
nouveau compte.

![Alt text][5]

Une fois que vous avez fourni les valeurs pour votre compte, cliquez sur « Créer » : le processus de configuration commence alors la création de votre compte de base de données.
Lorsque ce processus est terminé, une notification
apparaît dans la zone des notifications du portail et la vignette
sur l'écran d'accueil (si vous avez choisi d'en créer une)
affiche l'action terminée.

![Alt text][6]

Une fois la configuration terminée, un clic sur la vignette DocumentDB
dans l'écran d'accueil affiche le volet principal correspondant au
nouveau compte DocumentDB.

![Alt text][7]
![Alt text][8]

À l'aide du bouton « Clés », accédez à votre URL de point de terminaison et à la clé primaire.
Copiez-les dans le Presse-papiers et gardez-les à portée
de main. Nous les utiliserons dans l'application web que nous allons ensuite créer.

Voyons à présent comment créer une application ASP.NET MVC
de A à Z. Pour information, vous pouvez télécharger la solution
complète [ici].

# <a name="_Toc395637762">Création d'une application ASP.NET MVC</a>

Dans Visual Studio, cliquez sur Fichier – Nouveau projet, puis sélectionnez l'option permettant
de créer une application web ASP.NET MVC.

![Alt text][9]

Sélectionnez l'emplacement dans lequel créer le projet, puis cliquez sur Ok.

![Alt text][10]

Si vous envisagez d'héberger votre application dans Azure, activez la case à cocher « Héberger sur le cloud » située en bas à droite. Nous avons choisi un hébergement dans le cloud et d'exécuter l'application hébergée dans un site web Azure. Cette option
préconfigure un site web Azure pour vous et facilite
le déploiement de l'application opérationnelle finale.

Sélectionnez OK et laissez Visual Studio structurer le
modèle ASP.NET MVC vide. D'autres écrans peuvent s'afficher et vous demander de vous
connecter à votre compte Azure et d'indiquer certaines valeurs pour le nouveau
site web. Procédez comme indiqué et continuez.

Une fois que Visual Studio a fini de créer l'application MVC réutilisable,
vous disposez d'une application ASP.NET vide que vous pouvez exécuter localement.

Si vous ne savez pas comment exécuter l'application localement, reportez-vous à l'application Hello World ASP.NET. Nous allons maintenant ajouter DocumentDB à ce projet et créer notre application.

# <a name="_Toc395637763">Configuration de l'application ASP.NET MVC</a>

### 

### <a name="_Toc395637764">Ajout d'un modèle</a>

Commençons par créer le modèle (qui correspond au **M** dans MVC). Dans
l'Explorateur de solutions, cliquez avec le bouton droit sur le dossier *Models*. Cliquez ensuite sur **Ajouter**, puis sur
**Classe**

![Alt text][11]

Attribuez le nom **Item** à la nouvelle classe, puis ajoutez le code suivant dans cette nouvelle
classe.

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public int ID { get; set; }
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Toutes les données de DocumentDB sont transmises puis stockées au format JSON. Pour
contrôler la manière dont vos objets sont sérialisés/désérialisés par JSON.NET, vous
pouvez utiliser l'attribut JsonProperty comme ci-dessus, ce qui permet de vous assurer que les
noms de propriétés sont en minuscules, comme le spécifie la convention JSON.
Vous n'avez **pas** besoin de vous en occuper.

### <a name="_Toc395637765">Ajout d'un contrôleur</a>

Intéressons-nous maintenant au **C** de MVC, qui correspond à la
classe de contrôleur.
 Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Controllers*.
Cliquez ensuite sur **Ajouter**, puis sur **Contrôleur**.

![Alt text][12]

![Alt text][13]

Dans la boîte de dialogue **Ajouter la structure**, cliquez sur **Contrôleur MVC 5 - vide.**
Cliquez sur **Ajouter**.

![Alt text][14]

Nommez votre contrôleur **ItemController**.

Visual Studio va maintenant ajouter ItemController. L'Explorateur
de solutions doit être semblable à ce qui suit.

![Alt text][15]

### <a name="_Toc395637766">Ajout de vues</a>

Pour terminer, créons une vue (**V** dans MVC).

#### Ajout d'une vue Item Index

Développez le dossier ***Views*** dans l'Explorateur de solutions et recherchez le dossier
Item (vide) que Visual Studio a créé pour vous lorsque
vous avez ajouté *ItemController* précédemment. Cliquez avec le bouton droit sur ***Item***
 et sélectionnez Ajouter une nouvelle vue.

![Alt text][16]

Dans la boîte de dialogue « Ajouter une vue ». Nommez la vue ***Index***, utilisez le modèle
***List***, sélectionnez ***Item (Todo.Models)*** (que nous avons
créé précédemment en tant que classe), puis utilisez***\_Layout.cshtml***
dans la solution en tant que page de disposition.

![Alt text][17]

Une fois que vous avez défini toutes ces valeurs, cliquez sur Ajouter et laissez Visual Studio créer
la vue pour vous. Visual Studio crée alors une vue de modèle. Ensuite,
il ouvre le fichier cshtml créé. Nous pouvons fermer ce document
dans Visual Studio. Nous y reviendrons ultérieurement.

#### Ajout d'une vue New Item

Comme ci-dessus, créons une vue pour la création de nouveaux éléments
(voir ci-dessous).

![Alt text][18]

#### Ajout d'une vue Edit Item

# <a name="_Toc395888515"></a>

Pour terminer, ajoutons une dernière vue pour la modification d'un élément en suivant la procédure
indiquée plus haut.

![Alt text][19]

À la fin de cette procédure, fermez les documents cshtml dans Visual Studio. Nous
retournerons à ces vues ultérieurement.

</h1>
# <a name="_Toc395637767">Ajout de DocumentDB à votre projet</a>

Les étapes précédentes ont permis de mettre en place la plupart des éléments nécessaires à
cette solution. Venons-en maintenant au cœur du sujet et ajoutons Azure DocumentDB à notre application web.

</h1>
## <a name="_Toc395637768">Installation du package NuGet DocumentDB</a>

Le Kit de développement logiciel (SDK) .NET DocumentDB est packagé et distribué en tant que package NuGet.
Pour accéder au gestionnaire de package NuGet dans Visual Studio,
cliquez avec le bouton droit sur le projet et sélectionnez « Gérer les packages NuGet ».

![Alt text][20]

Recherchez « Azure DocumentDB » en ligne et installez le package. Cela va
vous permettre de télécharger et d'installer le package DocumentDB ainsi que toutes
les dépendantes (telles que Newtonsoft.Json).

Une fois installée, votre solution Visual Studio doit ressembler à ce qui suit,
avec l'ajout de deux nouvelles références.

![Alt text][21]

</h1>
## <a name="_Toc395637769">Configuration de DocumentDB</a>

### <a name="_Toc395637770">Liste des éléments non terminés</a>

Ouvrez **ItemController**, puis supprimez tout le code de la classe
(mais pas la classe) que Visual Studio a ajouté. Nous allons le recréer entièrement à l'aide de DocumentDB.

Ajoutez l'extrait de code suivant dans la classe
ItemController qui est à présent vide.

    public async Task<ActionResult> Index() 
    {
        var items = await repo.GetIncompleteItems();
        return View(items);
    }

Ce code utilise une classe de « pseudo référentiel » pour DocumentDB, qui est en fait
une classe d'assistance contenant tout le code propre à DocumentDB. Dans le
cadre de ce didacticiel, nous n'allons pas implémenter une
couche d'accès aux données complète avec une injection de dépendances à l'aide d'un modèle de référentiel,
comme nous le ferions lors de la création d'une véritable application. Nous
allons simplement insérer l'ensemble de la logique d'accès aux données
dans un projet unique dans un souci de simplicité.

Ajoutez une nouvelle classe à votre projet et nommez-la **DocumentDBRepository**.
Remplacez ensuite le code du fichier de classe par le code suivant.

    public static class DocumentDBRepository
    {
        public static async Task<List<Item>> GetIncompleteItems()
        {
            return await Task<List<Item>>.Run( () => 
                Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => !d.Completed)
                    .AsEnumerable()
                    .ToList<Item>());
        }
    } 

#### 

Prenez le temps d'ajouter les instructions using appropriées pour résoudre les
références. Toutes les références seront faciles à résoudre dans Visual Studio
si le package NuGet a été correctement installé. La référence à
*CreateDocumentQuery* sera résolue une fois que vous aurez ajouté manuellement
l'instruction using suivante.

    using Microsoft.Azure.Documents.Linq; 

Nous devons encore configurer un peu plus notre classe de pseudo
référentiel pour que tout fonctionne correctement. Ajoutons le code
ci-dessous à la classe de référentiel.

        private static Database database;
        private static Database Database
        {
            get
            {
                if (database == null)
                {
                    ReadOrCreateDatabase().Wait();
                }
                
                return database;
            }
        }

        private static DocumentCollection collection;
        private static DocumentCollection Collection
        {
            get
            {
                if (collection == null)
                {
                    ReadOrCreateCollection(Database.SelfLink).Wait();
                }
                
                return collection;
            }
        }

        private static string databaseId;
        private static String DatabaseId
        {
            get
            {
                if (string.IsNullOrEmpty(databaseId))
                {
                    databaseId = ConfigurationManager.AppSettings["database"];
                }
            
                return databaseId;
            }
        }
      
        private static string collectionId;
        private static String CollectionId
        {
            get
            {
                if (string.IsNullOrEmpty(collectionId))
                {
                    collectionId = ConfigurationManager.AppSettings["collection"];
                }
            
                return collectionId;
            }
        }

        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
                    String endpoint = ConfigurationManager.GetSetting("endpoint")
                    string authKey = ConfigurationManager.GetSetting("authKey");
                    Uri endpointUri = new Uri(endpoint);
                    client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }

        private static async Task ReadOrCreateCollection(string databaseLink)
        {
            var collections = Client.CreateDocumentCollectionQuery(databaseLink)
                              .Where(col => col.Id == CollectionId).ToArray();
            
            if (collections.Any())
            {
                collection = collections.First();
            }
            else
            {
                collection = await Client.CreateDocumentCollectionAsync(databaseLink, 
                    new DocumentCollection { Id = CollectionId });
            }
        }
     
        private static async Task ReadOrCreateDatabase()
        {
            var databases = Client.CreateDatabaseQuery()
                            .Where(db => db.Id == DatabaseId).ToArray();
            
            if (databases.Any())
            {
                database = databases.First();
            }
            else
            {
                Database database = new Database { Id = DatabaseId };   
                database = await Client.CreateDatabaseAsync(database);
            }
        }

</h1>
#### 

Lorsque vous accédez pour la première fois à une méthode telle que GetIncompleteItems(), celle-ci appelle le
client, qui établit la connexion à Azure DocumentDB. Le premier
appel à GetIncompleteItems() tente également de lire ou de créer
Database et DocumentCollection pour cette application. Cette opération ne doit
être effectuée qu'une seule fois. Par la suite, vous pourrez mettre en cache les propriétés SelfLink pour
les deux ressources et les réutiliser dans l'application.

Lorsque vous voudrez vous connecter à DocumentDB plus tard au sein de la même application,
la même instance du client, de la base de données et de la collection sera réutilisée
plutôt que de créer une instance à chaque fois.

Ouvrez le fichier **web.config** et ajoutez les lignes suivantes sous la
section <appsettings\>.

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>

</h4>
À ce stade, votre solution doit pouvoir être générée sans erreur.

Si vous exécutiez l'application maintenant, vous pourriez accéder au contrôleur Home et
à la vue Index associée. Bien qu'il s'agisse du comportement par défaut pour le
projet de modèle MVC choisi au début, nous n'en voulons pas. Modifions le routage de cette application MVC pour changer ce comportement.

Ouvrez ***RouteConfig.cs*** sous le dossier App\_Start. Recherchez la ligne qui
commence par « defaults: » et modifiez-la comme suit.

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Ce code indique maintenant à ASP.NET MVC que vous n'avez pas spécifié de valeur dans l'URL pour contrôler le comportement de routage qui, au lieu de « Home », utilise « Item » comme contrôleur et « Index » comme vue.
Si vous exécutiez l'application dans son état actuel, celle-ci appellerait **ItemController** et renverrait les résultats de la méthode **GetIncompleteItems** à la vue Views\\Item\\Index.
Si vous exécutez ce projet maintenant, vous devriez voir ce qui suit.

![Alt text][22]

### <a name="_Toc395637771">Ajout d'éléments</a>

Plaçons à présent quelques éléments dans notre base de données afin d'ajouter du contenu
à la grille vide.

Nous avons déjà une vue Create et un bouton sur la vue Index
qui dirige l'utilisateur vers la vue Create. Ajoutons du code au
contrôleur et au référentiel pour rendre l'enregistrement persistant dans DocumentDB.

Ouvrez ***ItemController.cs*** et ajoutez l'extrait de code suivant
qui indique à ASP.NET MVC quoi faire pour l'action Create. Dans ce
cas, effectuez simplement le rendu de la vue Create.cshtml associée créée précédemment.

    public ActionResult Create()
    { 
          return View(); 
    }

Nous devons à présent ajouter du code à ce contrôleur qui acceptera la
soumission à partir de la vue Create.

Ajoutons le bloc de code suivant qui indique à ASP.NET MVC quoi faire
avec une opération POST de formulaire pour ce contrôleur.

    [HttpPost]  
    public async Task<ActionResult> Create(Item item)  
    {
        if (ModelState.IsValid)  
        {  
            await repo.CreateDocument(item);  
            return RedirectToAction("Index");  
        }   
        return View(item);   
    }

Le contrôleur Items Controller transmet à présent l'élément, à partir du formulaire, à la
méthode CreateDocument de notre classe de pseudo référentiel. Ajoutons maintenant la
méthode suivante à la classe DocumentDBRepository.

    public static async Task<Document> CreateDocument(dynamic item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item); 
    }

Cette méthode prend simplement un des objets qui lui est transmis et le rend persistant dans
DocumentDB.

Le code qui permet d'ajouter de nouveaux éléments à la base de données est à présent complet.

### <a name="_Toc395637772">Modification des éléments</a>

Il est à présent possible de modifier
les éléments de la base de données et de les marquer comme terminés à mesure que nous effectuons les
tâches. Comme pour l'ajout, la vue pour la modification a déjà été créée. Nous devons donc
tout simplement ajouter du code à notre contrôleur et (une nouvelle fois) à la classe
DocumentDBRepository.

Ajoutez le code suivant à la classe ItemController.

    public async Task<ActionResult> Edit(string id)    
    {  
        if (id == null)  
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);   
        }

        Item item = await repo.GetDocument(id);
        if (item == null)
        {       
            return HttpNotFound();
        }
        
        return View(item);
    }

    [HttpPost] 
    public async Task<ActionResult> Edit(Item item)  
    {
        if (ModelState.IsValid)
        {
             await repo.UpdateDocument(item);
             return RedirectToAction("Index");
        }
        
        return View(item); 
    }

La première méthode traite l'opération Http Get qui se produit lorsque l'utilisateur
clique sur le lien Edit de la vue Index. Elle analyse un
document à partir de DocumentDB et le transmet à la vue Edit.

### 

La vue Edit renvoie ensuite une opération Http Post au IndexController.
La deuxième méthode que nous avons ajoutée gère ceci en transmettant l'objet mis à jour
à DocumentDB pour le rendre persistant dans la base de données.

Ajoutez le code suivant à la classe DocumentDBRepository.

    public static async Task<Item> GetDocument(string id)
    {
        return await Task<Item>.Run(() =>
            Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => d.ID == id)
                .AsEnumerable()
                .FirstOrDefault());
    }

    public static async Task<Document> UpdateDocument(Item item)
    {
        var doc = Client.CreateDocumentQuery<Document>(Collection.DocumentsLink)
                    .Where(d => d.Id == item.ID)
                    .AsEnumerable().FirstOrDefault();
        
        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

La première de ces deux méthodes analyse un élément à partir de DocumentDB et le
transmet à nouveau à ItemController, puis à la vue Edit.

La deuxième méthode remplace le document dans
DocumentDB par la version transmise à partir de
ItemController.

Nous avons à présent terminé la configuration des éléments nécessaires à l'exécution de notre application, à savoir répertorier
les éléments non terminés, ajouter des éléments, puis les modifier.

</h3>
# <a name="_Toc395637773">Exécution locale de l'application</a>

### 

Pour tester l'application sur votre ordinateur local, appuyez sur F5 dans Visual Studio
qui doit générer l'application et ouvrir un navigateur avec la grille vide
que nous avons vue précédemment :

![Alt text][23]

1\. Utilisez les champs fournis pour Item, Item Name et Category pour entrer
les informations requises, puis cliquez sur le lien **Créer un nouveau** et indiquez quelques
valeurs. N'activez pas la case à cocher Terminé, sans quoi le nouvel élément sera
ajouté ave l'état terminé et n'apparaîtra pas dans la liste initiale.

![Alt text][24]

Si vous cliquez sur Créer, vous êtes redirigé vers la page Index qui affiche
en principe votre élément dans la liste.

![Alt text][25]

Vous pouvez ajouter d'autres éléments à votre liste.

2\. Cliquez sur l'option Modifier située à côté d'un élément de la liste. Vous êtes alors redirigé vers
la vue Edit où vous pouvez mettre à jour les propriétés de votre objet,
notamment l'indicateur « completed ». L'élément est marqué comme
terminé, puis supprimé de la liste des tâches non terminées.

![Alt text][26]

3\. Pour terminer une tâche, cochez la case et cliquez sur
**Enregistrer**. Vous êtes redirigé vers la page de liste où
l'élément ne figure plus.

</h3>
# <a name="_Toc395637774">Déploiement de l'application dans Sites Web Azure</a>

### 

Maintenant que l'application terminée fonctionne correctement avec
DocumentDB, nous allons la déployer vers Sites Web Azure.

Si vous avez sélectionné « Héberger dans le cloud » lorsque nous avons créé le projet ASP.NET
MVC vide, Visual Studio vous facilite la tâche en faisant presque tout le
travail pour vous. Pour publier cette application, cliquez avec le bouton droit
sur le projet dans l'Explorateur de solutions (en vérifiant que nous n'êtes plus
en local), puis sélectionnez Publier.

![Alt text][27]

Tout doit être déjà configuré selon vos informations d'identification.
En fait, le site web a déjà été créé dans Azure à
l'URL de destination indiquée. Il vous reste juste à cliquer sur **Publier**

![Alt text][28]

En quelques secondes, Visual Studio achève la publication de votre
application web et lance un navigateur dans lequel vous pouvez voir
votre réalisation exécutée dans Azure !

</h3>
# <a name="_Toc395637775">Conclusion</a>

### 

Félicitations ! Vous venez de créer votre première application ASP.NET MVC à l'aide d'Azure DocumentDB et de la publier dans Sites Web Azure. Vous pouvez télécharger le code source de l'application de référence complète ici.

[ici] (http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409)

  [Alt text]: ./media/documentdb-dotnet-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/fr-fr/products/visual-studio-express-vs.aspx
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [1]: ./media/documentdb-dotnet-application/image2.png
  [2]: ./media/documentdb-dotnet-application/image3.png
  [3]: ./media/documentdb-dotnet-application/image4.png
  [4]: ./media/documentdb-dotnet-application/image5.png
  [5]: ./media/documentdb-dotnet-application/image6.png
  [6]: ./media/documentdb-dotnet-application/image7.png
  [7]: ./media/documentdb-dotnet-application/image8.png
  [8]: ./media/documentdb-dotnet-application/image9.png
  [9]: ./media/documentdb-dotnet-application/image10.png
  [10]: ./media/documentdb-dotnet-application/image11.png
  [11]: ./media/documentdb-dotnet-application/image12.png
  [12]: ./media/documentdb-dotnet-application/image13.png
  [13]: ./media/documentdb-dotnet-application/image14.png
  [14]: ./media/documentdb-dotnet-application/image15.png
  [15]: ./media/documentdb-dotnet-application/image16.png
  [16]: ./media/documentdb-dotnet-application/image17.png
  [17]: ./media/documentdb-dotnet-application/image18.png
  [18]: ./media/documentdb-dotnet-application/image19.png
  [19]: ./media/documentdb-dotnet-application/image20.png
  [20]: ./media/documentdb-dotnet-application/image21.png
  [21]: ./media/documentdb-dotnet-application/image22.png
  [22]: ./media/documentdb-dotnet-application/image23.png
  [23]: ./media/documentdb-dotnet-application/image24.png
  [24]: ./media/documentdb-dotnet-application/image25.png
  [25]: ./media/documentdb-dotnet-application/image26.png
  [26]: ./media/documentdb-dotnet-application/image27.png
  [27]: ./media/documentdb-dotnet-application/image28.png
  [28]: ./media/documentdb-dotnet-application/image29.png
