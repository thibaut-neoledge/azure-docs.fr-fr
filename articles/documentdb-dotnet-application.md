<properties 
	pageTitle="Création d'une application web avec ASP.NET MVC et DocumentDB | Azure" 
	description="Découvrez comment utiliser DocumentDB pour créer une application web de liste de tâches. Vous allez stocker les données et y accédez à partir d'une application web de ASP.NET MVC hébergée sur Azure." 
	services="documentdb" 
	documentationCenter="" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="ryancraw"/>

<a name="_Toc395809351">Création d'une application web avec ASP.NET MVC et DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Vue d'ensemble</a>
==============================================================================================

<a name="_Toc395637759">Scénario</a>
------------------------------------

Ce document présente une procédure pas à pas complète détaillant la création d'une application web de liste de tâches à l'aide du service Azure DocumentDB, afin d'expliquer comment les clients peuvent tirer parti de ce dernier pour stocker et interroger des documents JSON.

Cette procédure pas à pas indique comment utiliser le service DocumentDB fourni par Azure pour stocker les données et y accéder à partir d'une application web ASP.NET MVC hébergée sur Azure. Elle suppose que vous avez déjà une expérience d'ASP.NET MVC et de Sites Web Azure.

Vous apprendrez à effectuer les opérations suivantes :

1\. Création et configuration d'un compte DocumentDB

2\. Création d'une application ASP.NET MVC

3\. Connexion à Azure DocumentDB pour une utilisation à partir de votre application web

4\. Déploiement de l'application web vers Sites Web Azure

Dans cette procédure pas à pas, vous allez concevoir une simple application web de gestion des tâches qui vous permet de créer, de récupérer et de terminer des tâches. Ces dernières sont stockées en tant que documents JSON dans Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

Git pour Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (ou la version gratuite [Visual Studio Express][])

Kit de développement logiciel (SDK) Azure pour .NET 2.3 ou ultérieur, disponible via [Microsoft Web Platform Installer][]

Toutes les captures d'écran figurant dans ce document ont été prises avec Visual Studio 2013 Update 3) et le Kit de développement logiciel (SDK) Azure pour .NET 2.4. Si votre système est configuré avec différentes versions, il est probable que vos écrans et options ne correspondent pas totalement. Toutefois, si vous respectez la configuration requise citée plus haut, cette solution devrait fonctionner.

<a name="_Toc395637761">Création d'un compte de base de données DocumentDB</a>
================================================================

Pour configurer un compte de base de données DocumentDB dans Azure, ouvrez le portail de gestion Azure, puis cliquez sur la vignette Galerie Azure sur la page d'accueil ou sur " + " dans le coin inférieur gauche de l'écran.

![Alt text](./media/documentdb-dotnet-application/image2.png)


Cette opération ouvre la Galerie Azure, dans laquelle vous pouvez sélectionner l'un des nombreux services Azure disponibles. Dans la Galerie, sélectionnez " Données, stockage et sauvegarde " dans la liste des catégories.

![Alt text](./media/documentdb-dotnet-application/image3.png)

Sélectionnez l'option pour Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image4.png)


Sélectionnez ensuite " Créer " en bas de l'écran.

![Alt text](./media/documentdb-dotnet-application/image5.png)

Le panneau " Nouveau DocumentDB " s'ouvre. Vous pouvez y spécifier le nom, la région, l'échelle et le groupe de ressources, ainsi que d'autres paramètres de votre nouveau compte.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Une fois que vous avez terminé de fournir les valeurs de votre compte, cliquez sur " Créer ". Le processus de configuration entame ensuite la création de votre compte de base de données.
Une fois le processus de configuration terminé, une notification doit s'afficher dans la zone des notifications du portail et la vignette de votre écran d'accueil (si vous avez choisi d'en créer une) est modifiée pour refléter l'action terminée.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Une fois la configuration terminée, un clic sur la vignette DocumentDB de l'écran d'accueil affiche le panneau principal correspondant au compte DocumentDB nouvellement créé.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


À l'aide du bouton " Clés ", accédez à votre URL de point de terminaison et à la clé primaire. Copiez-les dans le Presse-papiers et gardez-les à portée de main. Nous les utiliserons dans l'application web que nous allons ensuite créer.

Voyons à présent comment créer une application ASP.NET MVC de A à Z. Pour information, vous pouvez télécharger la solution complète [ici].

<a name="_Toc395637762">Création d'une application ASP.NET MVC</a>
================================================================

Dans Visual Studio, cliquez sur Fichier - Nouveau projet, puis sélectionnez l'option permettant de créer une application web ASP.NET MVC.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Sélectionnez l'emplacement de création du projet, le nom du projet (nous utiliserons ici " todo "), puis cliquez sur Ok.

![Alt text](./media/documentdb-dotnet-application/image11.png)


Si vous envisagez d'héberger votre application dans Azure, activez la case à cocher " Héberger sur le cloud " située en bas à droite. Nous avons choisi un hébergement dans le cloud et d'exécuter l'application hébergée dans un site web Azure. Cette option préconfigure un site web Azure pour vous et facilite le déploiement de l'application opérationnelle finale. Si vous voulez utiliser un autre emplacement pour l'hébergement ou si vous ne voulez pas configurer Azure à l'avance, décochez la case " Héberger dans le cloud ".

Sélectionnez OK et laissez Visual Studio structurer le modèle ASP.NET MVC vide. Si vous voulez procéder à l'hébergement dans le cloud, un ou plusieurs écrans supplémentaires apparaîtront pour vous demander de vous connecter à votre compte Azure et d'indiquer plusieurs valeurs pour votre nouveau site web. Procédez comme indiqué et continuez. 

Ici, je n'ai pas choisi de " Serveur de base de données ", car nous n'allons pas utiliser de serveur de base de données SQL Azure. Nous allons créer un compte Azure DocumentDB ultérieurement, dans le portail de gestion. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Une fois que Visual Studio a fini de créer l'application MVC réutilisable, vous disposez d'une application ASP.NET vide que vous pouvez exécuter localement.

Si vous ne savez pas comment exécuter l'application localement, reportez-vous à l'application Hello World ASP.NET. Nous allons maintenant ajouter DocumentDB à ce projet et créer notre application.

</h1>
<a name="_Toc395637767">Ajout de DocumentDB à votre projet</a>
=============================================================

Les étapes précédentes ont permis de mettre en place la plupart des éléments nécessaires à cette solution. Venons-en maintenant au cœur du sujet et ajoutons Azure DocumentDB à notre application web.

### 

### <a name="_Toc395637764">Installation du package Nuget</a>

Le Kit de développement logiciel (SDK) .NET DocumentDB est packagé et distribué en tant que package NuGet. Pour accéder au gestionnaire de package NuGet dans Visual Studio, cliquez avec le bouton droit sur le projet et sélectionnez " Gérer les packages NuGet ".

![Alt text](./media/documentdb-dotnet-application/image21.png)

Recherchez " Azure DocumentDB " en ligne et installez le package. Cela va vous permettre de télécharger et d'installer le package DocumentDB ainsi que toutes les dépendances (telles que Newtonsoft.Json).

**REMARQUE :** comme le service est toujours en version préliminaire, le package NuGet est marqué comme " Version préliminaire ", vous devez donc activer l'option " Inclure la version préliminaire ", sinon le package ne s'affichera pas. 

Vous pouvez également utiliser la console de commande du package pour installer le package en entrant la commande suivante :

    Install-Package Microsoft.Azure.Documents.Client -Pre

Une fois installée, votre solution Visual Studio doit ressembler à ce qui suit, avec l'ajout de deux nouvelles références.

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Configuration de l'application ASP.NET MVC</a>
==================================================================

### 

### <a name="_Toc395637764">Ajout d'un modèle</a>

Commençons par créer le modèle (qui correspond au **M** dans MVC). Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le dossier  *Models*. Cliquez ensuite sur **Ajouter**, puis sur **Classe**.

![Alt text](./media/documentdb-dotnet-application/image12.png)

Attribuez le nom **Item** à la nouvelle classe, puis ajoutez le code suivant dans cette nouvelle classe.

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
		
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
		
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
		
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Toutes les données de DocumentDB sont transmises puis stockées au format JSON. Pour contrôler la méthode JSON.NET de sérialisation/désérialisation de vos objets, vous pouvez utiliser l'attribut JsonProperty, comme indiqué dans la classe Item que nous venons de créer. Vous n'êtes pas **obligé** de procéder ainsi, mais cela permet de s'assurer que les propriétés respectent les conventions d'attribution de noms JSON camelCase. 

En plus de contrôler le format du nom de la propriété lors de sa transmission à JSON, vous pouvez entièrement renommer vos propriétés .NET, comme ici avec la propriété Description. 

Si vous voulez, vous pouvez également utiliser les objets JsonConverter ici, pour contrôler complètement la gestion de la sérialisation.  

Pour que Visual Studio résolve l'attribut " JsonProperty " utilisé ici, vous devez ajouter l'instruction using suivante à la section using de votre fichier de classe.

    using Newtonsoft.Json;

### <a name="_Toc395637765">Ajout d'un contrôleur</a>

Intéressons-nous maintenant au **C** de MVC, qui correspond à la classe de contrôleur.
Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier  *Controllers*. Cliquez ensuite sur **Ajouter**, puis sur **Contrôleur**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


Dans la boîte de dialogue **Ajouter la structure**, cliquez sur **Contrôleur MVC 5 - vide.** Cliquez sur **Ajouter.**

![Alt text](./media/documentdb-dotnet-application/image15.png)

Nommez votre contrôleur **ItemController.**

Visual Studio va maintenant ajouter ItemController. L'Explorateur de solutions doit être semblable à ce qui suit.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Ajout de vues</a>

Pour terminer, créons une vue (**V** dans MVC).


#### Ajout d'une vue Item Index

Développez le dossier ***Views*** dans l'Explorateur de solutions et recherchez le dossier Item (vide) que Visual Studio a créé pour vous lorsque vous avez ajouté *ItemController* précédemment. Cliquez avec le bouton droit sur ***Item*** et sélectionnez Ajouter une nouvelle vue.

![Alt text](./media/documentdb-dotnet-application/image17.png)

Dans la boîte de dialogue " Ajouter une vue ". Nommez la vue ***Index***, utilisez le modèle ***List***, sélectionnez ***Item (todo.Models)*** (que nous avons créé précédemment en tant que classe), puis utilisez ***~Views/Shared/_Layout.cshtml*** dans la solution en tant que page de disposition.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Une fois que vous avez défini toutes ces valeurs, cliquez sur Ajouter et laissez Visual Studio créer la vue pour vous. Visual Studio crée alors une vue de modèle. Ensuite, il ouvre le fichier .cshtml créé. Nous pouvons fermer ce document dans Visual Studio. Nous y reviendrons ultérieurement.

#### Ajout d'une vue New Item

Comme ci-dessus, créons une vue pour la création de nouveaux éléments (voir ci-dessous).

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Ajout d'une vue Edit Item

<a name="_Toc395888515"></a>
============================

Pour terminer, ajoutons une dernière vue pour la modification d'un élément en suivant la procédure indiquée plus haut.

![Alt text](./media/documentdb-dotnet-application/image20.png)


À la fin de cette procédure, fermez les documents .cshtml dans Visual Studio. Nous retournerons à ces vues ultérieurement.

</h1>
<a name="_Toc395637769">Configuration de DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Liste des éléments non terminés</a>

Ouvrez **ItemController**, puis supprimez tout le code de la classe (mais pas la classe) que Visual Studio a ajouté. Nous allons le recréer entièrement à l'aide de DocumentDB.

Ajoutez l'extrait de code suivant dans la classe ItemController qui est à présent vide.

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

Ce code utilise également une classe " pseudo référentiel " nommée DocumentDBRepository, que nous devons créer. Il s'agit d'une classe Helper contenant l'intégralité du code spécifique de DocumentDB. Dans le cadre de ce didacticiel, nous n'allons pas implémenter une couche d'accès aux données complète avec une injection de dépendances à l'aide de modèles de fabrique ou de référentiel, comme nous le ferions lors de la création d'une véritable application. 
Nous allons simplement insérer l'ensemble de la logique d'accès aux données dans un projet unique dans un souci de simplicité.

Ajoutez une nouvelle classe à votre projet et nommez-la **DocumentDBRepository.**
Remplacez le code présent dans le fichier de classe par le code suivant.

    public static class DocumentDBRepository
    {
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
				
        private static Database database;
        private static Database Database
        {
			get
			{
				if (database == null)
				{
					database = ReadOrCreateDatabase();
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
					collection = ReadOrCreateCollection(Database.SelfLink);
				}
				
				return collection;
			}
        }
		
        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
					string endpoint = ConfigurationManager.AppSettings["endpoint"];
					string authKey = ConfigurationManager.AppSettings["authKey"];
					Uri endpointUri = new Uri(endpoint);
					client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }
    } 

#### 

Prenez le temps de résoudre tous les espaces de noms dans Visual Studio. Tous les espaces de noms seront faciles à résoudre dans Visual Studio si le package NuGet a été correctement installé. Les références aux méthodes ReadOrCreateDatabase et ReadOrCreateCollection ne seront pas résolues tant que nous ne les aurons pas ajoutées, ce que nous ferons après. 

Deux appels de méthode sont utilisés ici pour la lecture et la création de bases de données et de collections de documents DocumentDB.
Ajoutez donc les deux méthodes suivantes à cette classe.

    private static DocumentCollection ReadOrCreateCollection(string databaseLink)
    {
        var col = Client.CreateDocumentCollectionQuery(databaseLink)
                          .Where(c => c.Id == CollectionId)
                          .AsEnumerable()
                          .FirstOrDefault();

        if (col == null)
        {
            col = Client.CreateDocumentCollectionAsync(databaseLink, new DocumentCollection { Id = CollectionId }).Result;
        }
		
        return col;
    }

    private static Database ReadOrCreateDatabase()
    {
        var db = Client.CreateDatabaseQuery()
                        .Where(d => d.Id == DatabaseId)
                        .AsEnumerable()
                        .FirstOrDefault();
		
        if (db == null)
        {
            db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
        }
		
        return db;
    }

</h1>

#### 

Ceci permet de configurer la base de données, une entité DocumentCollection, mais aussi de créer un code pour la connexion à DocumentDB via l'entité DocumentClient. 

Nous allons lire certaines valeurs à partir de la configuration, ouvrez donc **web.config** et ajoutez les lignes suivantes, sous la
section <AppSettings\>.

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

À présent, nous allons ajouter du code pour effectuer le travail. 
Le premier objectif d'une application de liste de tâches est d'afficher les éléments incomplets. La méthode suivante le fait automatiquement pour vous. Veuillez donc la copier, puis la coller dans la classe de référentiel que nous utilisons.
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
La référence à *CreateDocumentQuery* sera résolue une fois que vous aurez ajouté manuellement l'instruction using suivante :

    using Microsoft.Azure.Documents.Linq;

À ce stade, votre solution doit pouvoir être générée sans erreur.

Si vous exécutiez l'application maintenant, vous pourriez accéder au contrôleur Home et à la vue Index associée. Bien qu'il s'agisse du comportement par défaut pour le projet de modèle MVC choisi au début, nous n'en voulons pas. Modifions le routage de cette application MVC pour changer ce comportement.

Ouvrez ***App\_Start\RouteConfig.cs*** et recherchez la ligne commençant par "defaults:", puis modifiez-la pour qu'elle ressemble à celle qui suit :

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Ce code indique maintenant à ASP.NET MVC que vous n'avez pas spécifié de valeur dans l'URL pour contrôler le comportement de routage qui, au lieu de " Home ", utilise " Item " comme contrôleur et " Index " comme vue.
Si vous exécutez l'application, celle-ci appellera **ItemController** et renverra les résultats de la méthode **GetIncompleteItems** à la vue Views\Item\Index. 

Si vous exécutez ce projet maintenant, vous devriez voir ce qui suit :    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">Ajout d'éléments</a>

Plaçons à présent quelques éléments dans notre base de données afin d'ajouter du contenu à la grille vide.

Nous avons déjà une vue Create et un bouton sur la vue Index qui dirige l'utilisateur vers la vue Create. Ajoutons du code au contrôleur et au référentiel pour rendre l'enregistrement persistant dans DocumentDB.

Ouvrez ***ItemController.cs*** et ajoutez l'extrait de code suivant qui indique à ASP.NET MVC quoi faire pour l'action Create. Dans ce cas, effectuez simplement le rendu de la vue Create.cshtml associée créée précédemment.

    public ActionResult Create()
    { 
	return View(); 
    }

Nous devons à présent ajouter du code à ce contrôleur qui acceptera la soumission à partir de la vue Create.

Ajoutons le bloc de code suivant qui indique à ASP.NET MVC quoi faire avec une opération POST de formulaire pour ce contrôleur.
	
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Create([Bind(Include = "Id,Name,Description,Completed")] Item item)  
    {
		if (ModelState.IsValid)  
		{  
		    await repo.CreateDocument(item);  
		    return RedirectToAction("Index");  
		}   
    	return View(item);   
    }

**Remarque relative à la sécurité** : L'attribut [ValidateAntiForgeryToken] est utilisé ici pour protéger cette application contre les falsifications de requête intersites. En plus de cela, vos vues doivent également fonctionner avec ce jeton anti-falsification. Pour plus d'informations sur le sujet et des exemples illustrant une implémentation adéquate, veuillez consulter [Prévention des falsifications de requête intersites][]. Le code source du lien de téléchargement situé à la fin de cet article permet de mettre en place l'implémentation complète.

**Remarque relative à la sécurité** : Nous utilisons également l'attribut [Bind] sur le paramètre de la méthode pour établir une protection contre les attaques par surcharge. Pour plus d'informations, veuillez consulter [Opérations CRUD de base dans ASP.NET MVC][]

Une fois ceci mis en place, le contrôleur Items Controller peut transmettre l'élément de façon sécurisée à partir du formulaire, à la méthode CreateDocument du référentiel de la classe. Ajoutez maintenant la méthode suivante à votre classe DocumentDBRepository.

    public static async Task<Document> CreateItemAsync(Item item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item);
    }

Cette méthode prend simplement un des objets qui lui est transmis et le rend persistant dans DocumentDB.
Le code qui permet d'ajouter de nouveaux éléments à la base de données est à présent complet.


### <a name="_Toc395637772">Modification des éléments</a>

Il est à présent possible de modifier les éléments de la base de données et de les marquer comme terminés à mesure que nous effectuons les tâches. Comme pour l'ajout, la vue pour la modification a déjà été créée. Nous devons donc tout simplement ajouter du code à notre contrôleur et (une nouvelle fois) à la classe DocumentDBRepository.

Ajoutez le code suivant à la classe ItemController.

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    {
        if (ModelState.IsValid)
        {
            await DocumentDBRepository.UpdateItemAsync(item);
            return RedirectToAction("Index");
        }

        return View(item);
    }
	
    public ActionResult Edit(string id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Item item = (Item)DocumentDBRepository.GetItem(id);
        if (item == null)
        {
            return HttpNotFound();
        }

        return View(item);
    }

La première méthode traite l'opération Http Get qui se produit lorsque l'utilisateur clique sur le lien Edit de la vue Index. Elle analyse un document à partir de DocumentDB et le transmet à la vue Edit.

La vue Edit renvoie ensuite une opération Http Post au IndexController. 
La deuxième méthode que nous avons ajoutée gère ceci en transmettant l'objet mis à jour à DocumentDB pour le rendre persistant dans la base de données.

Ajoutez le code suivant à la classe DocumentDBRepository.

    public static Item GetItem(string id)
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => d.Id == id)
                    .AsEnumerable()
                    .FirstOrDefault();
    }
	
    public static async Task<Document> UpdateItemAsync(Item item)
    {
        Document doc = Client.CreateDocumentQuery(Collection.DocumentsLink)
                            .Where(d => d.Id == item.Id)
                            .AsEnumerable()
                            .FirstOrDefault();

        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

La première de ces deux méthodes analyse un élément à partir de DocumentDB et le transmet à nouveau à ItemController, puis à la vue Edit.

La deuxième méthode remplace le document dans DocumentDB par la version transmise à partir de ItemController.

Nous avons à présent terminé la configuration des éléments nécessaires à l'exécution de notre application, à savoir répertorier les éléments non terminés, ajouter des éléments, puis les modifier.

</h3>
<a name="_Toc395637773">Exécution locale de l'application</a>
=========================================================

### 

Pour tester l'application sur votre ordinateur local, appuyez sur F5 dans Visual Studio qui doit générer l'application et ouvrir un navigateur avec la grille vide que nous avons vue précédemment :

![Alt text](./media/documentdb-dotnet-application/image24.png)

1\.Utilisez les champs fournis pour Item, Item Name et Category pour entrer les informations requises, puis cliquez sur le lien **Créer un nouveau** et indiquez quelques valeurs. N'activez pas la case à cocher Terminé, sans quoi le nouvel élément sera ajouté avec l'état terminé et n'apparaîtra pas dans la liste initiale.

![Alt text](./media/documentdb-dotnet-application/image25.png)

Si vous cliquez sur Créer, vous êtes redirigé vers la page Index qui affiche en principe votre élément dans la liste.

![Alt text](./media/documentdb-dotnet-application/image26.png)

Vous pouvez ajouter d'autres éléments à votre liste.

2\.Cliquez sur l'option " Modifier " située à côté d'un élément de la liste. Vous êtes alors redirigé vers la vue Edit où vous pouvez mettre à jour les propriétés de votre objet, notamment l'indicateur " completed ". L'élément est marqué comme terminé, puis supprimé de la liste des tâches non terminées.

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.Pour terminer une tâche, cochez la case et cliquez sur **Enregistrer.** Vous êtes redirigé vers la page de liste où l'élément ne figure plus.

</h3>
<a name="_Toc395637774">Déploiement de l'application dans Sites Web Azure</a>
================================================================

### 

Maintenant que l'application terminée fonctionne correctement avec DocumentDB, nous allons la déployer vers Sites Web Azure.

Si vous avez sélectionné " Héberger dans le cloud " lorsque nous avons créé le projet ASP.NETMVC vide, Visual Studio vous facilite la tâche en faisant presque tout le travail pour vous. Pour publier cette application, cliquez avec le bouton droit sur le projet dans l'Explorateur de solutions (en vérifiant que nous n'êtes plus en local), puis sélectionnez Publier.

![Alt text](./media/documentdb-dotnet-application/image28.png)

Tout doit être déjà configuré selon vos informations d'identification. En fait, le site web a déjà été créé dans Azure à l'" URL de destination " indiquée. Il vous reste juste à cliquer sur **Publier**.

![Alt text](./media/documentdb-dotnet-application/image29.png)

En quelques secondes, Visual Studio achève la publication de votre application web et lance un navigateur dans lequel vous pouvez voir votre réalisation exécutée dans Azure !

</h3>



<a name="_Toc395637775">Conclusion</a>
======================================

### 

Félicitations ! Vous venez de créer votre première application ASP.NET MVC à l'aide d'Azure DocumentDB et de la publier dans Sites Web Azure. Vous pouvez télécharger le code source de l'application de référence complète, incluant les fonctionnalités Details et Delete dont nous n'avons pas parlé, [ici][].


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/fr-fr/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[ici]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Prévention des falsifications de requête intersites]: http://go.microsoft.com/fwlink/?LinkID=517254
[Opérations CRUD de base dans ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=27-->

<!--HONumber=46--> 
