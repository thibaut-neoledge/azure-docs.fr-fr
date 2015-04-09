<properties 
	pageTitle="Création d'une application web avec ASP.NET MVC et DocumentDB | Azure" 
	description="Découvrez comment utiliser DocumentDB avec .NET pour créer une application web de liste To Do. Vous allez stocker les données et y accéder à partir d'une application web ASP.NET MVC hébergée sur des sites web Azure." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/23/2015" 
	ms.author="ryancraw"/>

#<a name="_Toc395809351"></a>Création d'une application web avec ASP.NET MVC et DocumentDB

Pour mettre en évidence la façon dont vous pouvez exploiter efficacement Azure DocumentDB pour stocker et interroger les documents JSON, cet article fournit une procédure de bout en bout vous montrant comment créer une application web de liste todo à l'aide d'Azure DocumentDB. Ces dernières sont stockées en tant que documents JSON dans Azure DocumentDB.

![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image1.png)

Cette procédure pas à pas montre comment utiliser le service DocumentDB fourni par Azure pour stocker des données et y accéder à partir d'une application web ASP.NET MVC hébergée sur Azure.

> [AZURE.TIP] Ce didacticiel suppose que vous disposez d'une expérience préalable de l'utilisation d'ASP.NET MVC et des sites web Azure. Si vous débutez avec ASP.NET ou avec les [outils requis](#_Toc395637760), nous vous recommandons de télécharger l'ensemble du projet du didacticiel [todo](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/todo) à partir de [GitHub](https://github.com/Azure/azure-documentdb-net) et de le créer à l'aide des [instructions fournies à la fin de cet article](#GetProject). Une fois que vous l'avez créé, vous pouvez consulter l'article pour obtenir des informations sur le code dans le contexte du projet.

## <a name="_Toc395637760"></a>Conditions préalables

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

- un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](../../pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/) ou une version ultérieure, ou [Visual Studio Express], qui est la version gratuite.
- Kit de développement logiciel (SDK) Azure pour .NET 2.3 ou ultérieur, disponible via [Microsoft Web Platform Installer][]

Toutes les captures d'écran figurant dans cet article ont été prises avec Visual Studio 2013 Update 3) et le Kit de développement logiciel (SDK) Azure pour .NET 2.4. Si votre système est configuré avec différentes versions, il est probable que vos écrans et options ne correspondent pas totalement. Toutefois, si vous respectez la configuration requise citée plus haut, cette solution devrait fonctionner.

## <a name="_Toc395637761"></a>Étape 1 : Création d'un compte de base de données DocumentDB

Commençons par créer un compte DocumentDB. Si vous possédez déjà un compte, vous pouvez passer à l'étape [Création d'une application ASP.NET MVC](#_Toc395637762)

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

<br/>
Voyons à présent comment créer une application ASP.NET MVC de A à Z. 

## <a name="_Toc395637762"></a>Étape 2 : Création d'une application ASP.NET MVC

Maintenant nous allons créer notre nouveau projet ASP.NET.

1. Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

   	La boîte de dialogue **Nouveau projet** apparaît.
2. Dans le volet **Types de projets**, développez **Modèles**, **Visual C#**, **Web**, puis sélectionnez **Application Web ASP.NET**.

  	![Screen shot of the New Project dialog box with the ASP.NET Web Application project type highlighted](./media/documentdb-dotnet-application/image10.png)

3. Dans la zone **Nom**, tapez le nom du projet. Ce didacticiel utilise le nom " todo ". 
4. Cliquez sur **Parcourir** pour accéder au dossier où vous souhaitez créer le projet, puis cliquez sur **OK**.

  	La boîte de dialogue **Nouveau projet ASP.NET** s'affiche.

  	![Screen shot of the New ASP.NET Project dialog box with the MVC template highlighted and the Host in the cloud box checked](./media/documentdb-dotnet-application/image11.png)

5. Dans le volet Modèles, sélectionnez **MVC**.
6. Si vous prévoyez d'héberger votre application dans Azure, sélectionnez **Hôte dans le cloud** dans le coin inférieur droit pour qu'Azure héberge l'application. Nous avons choisi un hébergement dans le cloud et d'exécuter l'application hébergée dans un site web Azure. Cette option préconfigure un site web Azure pour vous et facilite le déploiement de l'application opérationnelle finale. Si vous voulez utiliser un autre emplacement pour l'hébergement ou si vous ne voulez pas configurer Azure à l'avance, désactivez la case **Héberger dans le cloud**.
7. Cliquez sur **OK** et laissez Visual Studio structurer le modèle ASP.NET MVC vide. 
8. Si vous voulez procéder à l'hébergement dans le cloud, un ou plusieurs écrans supplémentaires apparaîtront pour vous demander de vous connecter à votre compte Azure et d'indiquer plusieurs valeurs pour votre nouveau site web. Fournissez toutes les valeurs supplémentaires et continuez. 

  	Je n'ai pas choisi de " Serveur de base de données ", car nous n'allons pas utiliser de serveur de base de données SQL Azure. Nous allons créer un compte Azure DocumentDB ultérieurement, dans le portail Azure en version préliminaire. 

  	![Screen shot of the Configure Microsoft Azure Website dialog box](./media/documentdb-dotnet-application/image11_1.png)

9. Une fois que Visual Studio a fini de créer l'application MVC réutilisable, vous disposez d'une application ASP.NET vide que vous pouvez exécuter localement.

Nous allons sauter l'exécution du projet localement, car je suis sûr que nous avons tous vu l'application " Hello World " ASP.NET. Nous allons maintenant ajouter DocumentDB à ce projet et créer notre application.

## <a name="_Toc395637767"></a>Étape 3 : ajout de DocumentDB à votre projet

Les étapes précédentes ont permis de mettre en place la plupart des éléments nécessaires
à cette solution. Venons-en maintenant au cœur du sujet et ajoutons Azure DocumentDB à notre application web.

1. Le Kit de développement logiciel (SDK) .NET DocumentDB est packagé et distribué en tant que package NuGet. Pour obtenir le package NuGet dans Visual Studio, utilisez le gestionnaire de package NuGet dans Visual Studio en cliquant avec le bouton droit sur le projet dans l'**Explorateur de solutions**, puis en cliquant sur **Gérer les packages NuGet**.

  	La boîte de dialogue **Gérer les packages NuGet** apparaît.

  	![Sreen shot of the right-click options for the project in Solution Explorer, with Manage NuGet Packages highlighted.](./media/documentdb-dotnet-application/image21.png)

2. Dans la zone **Rechercher en ligne**, tapez " Azure DocumentDB ". À partir des résultats, installez le package **Bibliothèques clientes de Microsoft Azure DocumentDB**. Cela va vous permettre de télécharger et d'installer le package DocumentDB ainsi que toutes les dépendances (telles que Newtonsoft.Json).

  	> [AZURE.NOTE] Comme le service est toujours en version préliminaire, le package NuGet est marqué comme " Version préliminaire ", vous devez donc activer l'option " Inclure la version préliminaire ", sinon le package ne s'affichera pas. 

  	Vous pouvez également utiliser la console de commande du package pour installer le package en entrant la commande suivante.

    	Install-Package Microsoft.Azure.Documents.Client -Pre

3. Une fois que le package est installé, votre solution Visual Studio doit ressembler à ce qui suit avec deux nouvelles références ajoutées, Microsoft.Azure.Documents.Client et Newtonsoft.Json.

  	![Sreen shot of the two references added to the project in Solution Explorer.](./media/documentdb-dotnet-application/image22.png)


##<a name="_Toc395637763"></a>Étape 4 : configuration de l'application ASP.NET MVC
 
Maintenant nous allons ajouter les modèles, les vues et les contrôleurs à cette application MVC :

- [Ajout d'un modèle](#_Toc395637764).
- [Ajout d'un contrôleur](#_Toc395637765).
- [Ajout de vues](#_Toc395637766).


### <a name="_Toc395637764"></a>Ajout d'un modèle

Commençons par créer le modèle (qui correspond au **M** dans MVC). 

1. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Modèles**, cliquez sur **Ajouter**, puis sur **Classe**.

  	La boîte de dialogue **Ajouter un nouvel élément** s'affiche.

2. Attribuez le nom **Item** à la nouvelle classe, puis ajoutez le code suivant au fichier Item.cs.

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

	Toutes les données de DocumentDB sont transmises puis stockées au format JSON. Pour contrôler la méthode JSON.NET de sérialisation/désérialisation de vos objets, vous pouvez utiliser l'attribut **JsonProperty**, comme indiqué dans la classe **Item** que nous venons de créer. Vous n'êtes pas **obligé** de procéder ainsi, mais cela permet de s'assurer que les propriétés respectent les conventions d'attribution de noms JSON camelCase. 

	En plus de contrôler le format du nom de la propriété lors de sa transmission à JSON, vous pouvez entièrement renommer vos propriétés .NET, comme ici avec la propriété **Description**. 

	Si vous voulez, vous pouvez également utiliser les objets **JsonConverter** ici, pour contrôler complètement la gestion de la sérialisation.  

3. Pour que Visual Studio résolve l'attribut **JsonProperty** utilisé ici, vous devez ajouter l'instruction using suivante à la section using de votre fichier de classe.

    	using Newtonsoft.Json;

### <a name="_Toc395637765"></a>Ajout d'un contrôleur

Maintenant que nous en avons terminé avec le **M** de MVC, intéressons-nous au **C**, qui correspond à la classe de contrôleur.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Contrôleurs**, puis cliquez sur **Ajouter** et sur **Contrôleur**.

    La boîte de dialogue **Ajouter une structure** s'affiche.

2. Sélectionnez **Classe de contrôleur MVC 5 - Vide** puis cliquez sur **Ajouter**.

	![Screen shot of the Add Scaffold dialog box with the MVC 5 Controller - Empty option highlighted](./media/documentdb-dotnet-application/image14.png)

3. Nommez votre contrôleur **ItemController.**

	![Screen shot of the Add Controller dialog box](./media/documentdb-dotnet-application/image15.png)

Une fois le fichier créé, votre solution Visual Studio doit ressembler à ce qui suit avec le nouveau fichier ItemController.cs dans l'**Explorateur de solutions**.

![Screen shot of Solution Explorer with the new ItemController.cs file and Item.cs file highlighted](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766"></a>Ajout de vues

Pour terminer, créons le **V** de MVC, les vues :

- [Ajout d'une vue Index de l'élément](#AddItemIndexView).
- [Ajout d'une vue Nouvel élément](#AddNewIndexView).
- [Ajout d'une vue Modifier l'élément](#_Toc395888515).


#### <a name="AddItemIndexView"></a>Ajout d'une vue Index de l'élément

1. Dans l'**Explorateur de solutions**, développez le dossier ***Vues***, cliquez avec le bouton droit sur le dossier vide **Élément** créé par Visual Studio pour vous lorsque vous avez ajouté **ItemController** précédemment, cliquez sur **Ajouter**, puis cliquez sur **Affichage**.

![Screen shot of Solution Explorer showing the Item folder with the Add View commands highlighted](./media/documentdb-dotnet-application/image17.png)

2. Dans la boîte de dialogue **Ajouter une vue**, procédez comme suit :
	- Dans la zone **Nom de la vue**, tapez ***Index***.
	- Dans la zone **Modèle**, sélectionnez ***Liste***.
	- Dans la zone **Classe de modèle**, sélectionnez ***Élément (todo.Models)***.
	- Dans la zone de la page de disposition, tapez ***~/Views/Shared/_Layout.cshtml***.
	- Cliquez sur **Ajouter**.
	
![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image18.png)

3. Une fois que vous avez défini toutes ces valeurs, cliquez sur **Ajouter** et laissez Visual Studio créer la vue pour vous. Visual Studio crée alors une vue de modèle. Ensuite, il ouvre le fichier .cshtml créé. Nous pouvons fermer ce fichier dans Visual Studio. Nous y reviendrons ultérieurement.

#### <a name="AddNewIndexView"></a>Ajout d'une vue Nouvel élément

De la même façon que nous avons créé une vue **Index de l'élément**, nous allons maintenant créer une nouvelle vue pour la création de nouveaux **Éléments**.

Dans la boîte de dialogue **Ajouter une vue**, procédez comme suit :

- Dans la zone **Nom de la vue**, tapez ***Create***.
- Dans la zone **Modèle**, sélectionnez ***Create***.
- Dans la zone **Classe de modèle**, sélectionnez ***Élément (todo.Models)***.
- Dans la zone de la page de disposition, tapez ***~/Views/Shared/_Layout.cshtml***.
- Cliquez sur **Ajouter**.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image19.png)

#### <a name="_Toc395888515"></a>Ajout d'une vue Modifier l'élément

Pour terminer, ajoutons une dernière vue pour la modification d'un **Élément** en suivant la procédure indiquée plus haut.


Dans la boîte de dialogue **Ajouter une vue**, procédez comme suit :

- Dans la zone **Nom de la vue**, tapez ***Edit***.
- Dans la zone **Modèle**, sélectionnez ***Edit***.
- Dans la zone **Classe de modèle**, sélectionnez ***Élément (todo.Models)***.
- Sélectionnez **Créer en tant que vue partielle**.
- Cliquez sur **Ajouter**.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image20.png)

À la fin de cette procédure, fermez les documents .cshtml dans Visual Studio. Nous retournerons à ces vues ultérieurement.

## <a name="_Toc395637769"></a>Étape 5 : Configuration de DocumentDB

Dans cette section, nous allons ajouter du code pour gérer les éléments suivants :

- [Liste des éléments incomplets](#_Toc395637770).
- [Ajout d'éléments](#_Toc395637771).
- [Modification d'éléments](#_Toc395637772).

### <a name="_Toc395637770"></a>Liste des éléments incomplets

1. Ouvrez **ItemController**, puis supprimez tout le code de la classe (mais pas la classe) que Visual Studio a ajouté. Nous allons le recréer entièrement à l'aide de DocumentDB.

2. Ajoutez l'extrait de code suivant dans la classe **ItemController** qui est à présent vide.

    	public ActionResult Index()
    	{
			var items = DocumentDBRepository<Item>.GetItems(d => !d.Completed);
			return View(items);
    	}

	Ce code utilise également une classe " pseudo référentiel " nommée **DocumentDBRepository**, que nous devons créer. Il s'agit d'une classe Helper contenant l'intégralité du code spécifique de DocumentDB. Dans le cadre de ce didacticiel, nous n'allons pas implémenter une couche d'accès aux données complète avec une injection de dépendances à l'aide de modèles de fabrique ou de référentiel, comme nous le ferions lors de la création d'une véritable application. 
	Nous allons simplement insérer l'ensemble de la logique d'accès aux données dans un projet unique dans un souci de simplicité.

3. Ajoutez une nouvelle classe à votre projet et nommez-la **DocumentDBRepository**. 
4. Remplacez le code présent dans la classe **DocumentDBRepository** par le code suivant.

    	public static class DocumentDBRepository<T>
    	{
	       using Microsoft.Azure.Documents; 
	       using Microsoft.Azure.Documents.Client; 
	       using Microsoft.Azure.Documents.Linq; 
		
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

5. Prenez le temps de résoudre tous les espaces de noms dans Visual Studio, ce qui doit inclure l'ajout des directives d'utilisation suivantes au fichier DocumentDBRepository.cs.

		using Microsoft.Azure.Documents;
		using Microsoft.Azure.Documents.Client;
		using Microsoft.Azure.Documents.Linq;
		using System.Configuration;

 	Tous les espaces de noms seront faciles à résoudre dans Visual Studio si le package NuGet a été correctement installé. Les références aux méthodes **ReadOrCreateDatabase** et **ReadOrCreateCollection** ne seront pas résolues tant que nous ne les aurons pas ajoutées, ce que nous ferons après.
 
6. Deux appels de méthode sont utilisés ici pour la lecture et la création de bases de données et de collections de documents DocumentDB. Ajoutez donc les deux méthodes suivantes à la classe **DocumentDBRepository**.

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

	Ce code permet de configurer la base de données, une entité [**DocumentCollection**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.documentcollection.aspx), mais aussi de créer le code pour la connexion à DocumentDB via l'entité [**DocumentClient**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx). 

7. Nous allons lire certaines valeurs à partir de la configuration. Pour cela, ouvrez le fichier **Web.config** et ajoutez les lignes suivantes sous la section `<AppSettings>`.
	
    	<add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    	<add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    	<add key="database" value="ToDoList"/>
    	<add key="collection" value="Items"/>
	
8. À présent, mettez à jour ces valeurs à l'aide du panneau Clés du portail de gestion Azure. Utilisez la valeur de l'**URI** du panneau Clés en tant que valeur de la clé du point de terminaison et utilisez la valeur de la **CLÉ PRIMAIRE** du panneau Clés en tant que valeur de la clé authKey.
	
	À présent, nous allons ajouter du code pour effectuer le travail. 	

9. La première chose que nous souhaitons pouvoir faire avec une application de liste todo est d'afficher les éléments non terminés.  La méthode ci-dessous effectue automatiquement cette opération pour vous. Copiez et collez donc la n'importe où dans la classe **DocumentDBRepository**.

	    public static IEnumerable<T> GetItems(Expression<Func<T, bool>> predicate) 
	    {
			return Client.CreateDocumentQuery<T>(Collection.DocumentsLink) 
				.Where(predicate) 
				.AsEnumerable(); 
		} 
	    
	À ce stade, votre solution doit pouvoir être générée sans erreur.

	Si vous exécutiez l'application maintenant, vous pourriez accéder au **HomeController** et à la vue **Index** associée. Bien qu'il s'agisse du comportement par défaut pour le projet de modèle MVC choisi au début, nous n'en voulons pas. Modifions le routage de cette application MVC pour changer ce comportement.

11. Ouvrez ***App\_Start\RouteConfig.cs***. Recherchez la ligne commençant par "defaults:", puis modifiez-la pour qu'elle ressemble à celle qui suit :

    	defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

	Ce code indique maintenant à ASP.NET MVC que vous n'avez pas spécifié de valeur dans l'URL pour contrôler le comportement de routage qui, au lieu de **Home**, utilise **Item** comme contrôleur et **Index** comme vue.
	Si vous exécutez l'application, celle-ci appellera **ItemController** et renverra les résultats de la méthode **GetIncompleteItems** à la vue **Views**\\**Item**\\**Index**. 

12. Si vous créez et exécutez ce projet maintenant, vous devriez voir ce qui suit :    

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>Ajout d'éléments

Plaçons à présent quelques éléments dans notre base de données afin d'ajouter du contenu à la grille vide.

Nous avons déjà une vue **Create** et un bouton sur la vue **Index** qui dirige l'utilisateur vers la vue **Create**. Ajoutons du code au contrôleur et au référentiel pour rendre l'enregistrement persistant dans DocumentDB.

1. Ouvrez le fichier ItemController.cs et ajoutez l'extrait de code suivant qui permet à ASP.NET MVC de savoir que faire pour l'action **Create**. Dans ce cas, restituez simplement la vue Create.cshtml associée créée précédemment.

    	public ActionResult Create()
    	{ 
			return View(); 
   		}

	Nous devons à présent ajouter du code à ce contrôleur qui acceptera la soumission à partir de la vue **Create**.

2. Ajoutons le bloc de code suivant qui indique à ASP.NET MVC quoi faire avec une opération POST de formulaire pour ce contrôleur.
	
    	[HttpPost]
    	[ValidateAntiForgeryToken]
   	 	public async Task<ActionResult> Create([Bind(Include = 	"Id,Name,Description,Completed")] Item item)  
  	  	{
			if (ModelState.IsValid)  
			{  
			    await DocumentDBRepository<Item>.CreateItemAsync(item);
			    return RedirectToAction("Index");  
			}   
			return View(item);   
		}

	**Remarque relative à la sécurité** : L'attribut **ValidateAntiForgeryToken** est utilisé ici pour protéger cette application contre les falsifications de requête intersites. En plus d'ajouter cet attribut, vous devez vérifier que vos vues fonctionnent avec ce jeton anti-falsification. Pour plus d'informations sur le sujet et des exemples illustrant une implémentation adéquate, consultez la rubrique [Prévention des falsifications de requête intersites][]. Le code source fourni sur [GitHub][] comporte l'implémentation complète.

	**Remarque relative à la sécurité** : Nous utilisons également l'attribut **Bind** sur le paramètre de la méthode pour établir une protection contre les attaques par surcharge. Pour plus d'informations, consultez la rubrique [Opérations CRUD de base dans ASP.NET MVC][]

3. Maintenant que la méthode **Create** est en place, **ItemController** transmet l'objet **Item** du formulaire à la méthode **CreateDocument**. Ajoutez à présent la méthode suivante à votre classe **DocumentDBRepository**.

    	public static async Task<Document> CreateItemAsync(T item)
   	 	{
   	   		return await Client.CreateDocumentAsync(Collection.SelfLink, item);
   		}

	Cette méthode prend simplement un des objets qui lui est transmis et le rend persistant dans DocumentDB.

Le code qui permet d'ajouter de nouveaux éléments à la base de données est à présent complet.


### <a name="_Toc395637772"></a>Modification d'éléments

Il est à présent possible de modifier les **éléments** de la base de données et de les marquer comme terminés. La vue correspondant à la modification a déjà été ajoutée au projet, nous devons donc simplement ajouter à nouveau du code à notre contrôleur et à la classe **DocumentDBRepository**.

1. Ajoutez le code suivant à la classe **ItemController**.

    	[HttpPost]
   		[ValidateAntiForgeryToken]
    	public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    	{
     	   if (ModelState.IsValid)
    	    {
    	        await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
    	        return RedirectToAction("Index");
    	    }

  	      return View(item);
 	   	}
		
		public ActionResult Edit(string id)
		{
		    if (string.IsNullOrEmpty(id))
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
					 
		    Item item = (Item)DocumentDBRepository<Item>.GetItem(d => d.Id == id);
		 	
		    if (item == null)
		    {
		        return HttpNotFound();
		    }
		 	
		    return View(item);
		}
		
	
	La première méthode traite l'opération Http GET qui se produit lorsque l'utilisateur clique sur le lien **Edit** de la vue **Index**. Elle extrait un [**Document**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) à partir de DocumentDB et le transmet à la vue **Edit**.

	La vue **Edit** renvoie ensuite une opération Http POST au **IndexController**. 
	
	La deuxième méthode que nous avons ajoutée gère en transmettant l'objet mis à jour à DocumentDB pour le rendre persistant dans la base de données.

2. Ajoutez le code suivant au fichier ItemController.cs à l'aide de la directive.

		using todo.Models;
		using System.Threading.Tasks;
		using System.Net;

3. Ajoutez le code suivant à la classe **DocumentDBRepository**.

    	public static T GetItem(Expression<Func<T, bool>> predicate)
    	{
        	return Client.CreateDocumentQuery<T>(Collection.DocumentsLink)
                    	.Where(predicate)
                    	.AsEnumerable()
                    	.FirstOrDefault();
    	}	
		
		public static Document GetDocument(string id) 
		{ 
			return Client.CreateDocumentQuery(Collection.DocumentsLink) 
				.Where(d => d.Id == id) 
				.AsEnumerable() 
				.FirstOrDefault(); 
		} 

    	public static async Task<Document> UpdateItemAsync(string id, T item)
    	{
        	Document doc = GetDocument(id);	
        	return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    	}

	La première de ces méthodes analyse un **élément** à partir de DocumentDB et le transmet à nouveau à **ItemController**, puis à la vue **Edit**.
	
	La deuxième méthode que nous venons d'ajouter remplace le **document** dans DocumentDB par la version du **document** transmise à partir de **ItemController**.

4. Ajoutez le code suivant au fichier DocumentDBRepository.cs à l'aide de la directive.

		using System.Threading.Tasks;

	Nous avons à présent terminé la configuration des éléments nécessaires à l'exécution de notre application, à savoir répertorier les **éléments** non terminés, ajouter des **éléments**, puis modifier des **éléments**.

## <a name="_Toc395637773"></a>Étape 6 : Exécution locale de l'application

Pour tester l'application sur votre machine locale, procédez comme suit :

1. Appuyez sur F5 dans Visual Studio pour générer l'application en mode débogage. Cette opération doit générer l'application et lancer un navigateur avec la page de grille vide que nous avons vue auparavant :

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image24.png)

	Si vous rencontrez des erreurs à ce stade, vous pouvez comparer votre code au didacticiel todo sur [GitHub][].

2. Cliquez sur le lien **Créer nouveau** et ajoutez des valeurs pour les champs **Nom** et **Description**. N'activez pas la case à cocher **Terminé**, sans quoi le nouvel **élément** sera ajouté avec l'état terminé et n'apparaîtra pas dans la liste initiale.

	![Screen shot of the Create view](./media/documentdb-dotnet-application/image25.png)

3. Cliquez sur **Créer** et vous êtes redirigé vers la vue **Index** et votre **élément** apparaît dans la liste.

	![Screen shot of the Index view](./media/documentdb-dotnet-application/image26.png)

	Vous pouvez ajouter d'autres **éléments** à votre liste todo.

3. Cliquez sur l'option **Modifier** située à côté d'un **élément** de la liste. Vous êtes alors redirigé vers la vue **Edit** où vous pouvez mettre à jour les propriétés de votre objet, notamment l'indicateur **Completed**. Si vous marquez l'indicateur **Completed** et cliquez sur **Enregistrer**, l'**élément** est supprimé de la liste des tâches incomplètes.

	![Screen shot of the Index view with the Completed box checked](./media/documentdb-dotnet-application/image27.png)

4. Une fois que vous avez testé l'application, appuyez sur Ctrl+F5 pour arrêter le débogage de l'application. Vous êtes prêt à déployer.

##<a name="_Toc395637774"></a>Étape 7 : déploiement de l'application vers des sites web Azure

Maintenant que l'application terminée fonctionne correctement avec DocumentDB, nous allons la déployer vers des sites web Azure. Si vous avez sélectionné **Héberger dans le cloud** lorsque vous avez créé le projet ASP.NET MVC vide, Visual Studio vous facilite la tâche en faisant presque tout le travail pour vous. 

Pour publier cette application, cliquez avec le bouton droit sur le projet dans l'**Explorateur de solutions**, puis cliquez sur **Publier**.

![Screen shot of the Publish option in Solution Explorer](./media/documentdb-dotnet-application/image28.png)

Tout doit être déjà configuré selon vos informations d'identification. En fait, le site web a déjà été créé dans Azure à l'**URL de destination** indiquée. Il vous reste juste à cliquer sur **Publier**.

![Screen shot of the Publish Web dialog box](./media/documentdb-dotnet-application/image29.png)

En quelques secondes, Visual Studio achève la publication de votre application web et lance un navigateur dans lequel vous pouvez voir votre réalisation exécutée dans Azure !

##<a name="_Toc395637775"></a>Étapes suivantes

Félicitations ! Vous venez de créer votre première application ASP.NET MVC à l'aide d'Azure DocumentDB et de la publier sur les sites web Azure. Le code source de l'application terminée, y compris les fonctionnalités de détail et de suppression qui n'étaient pas incluses dans ce didacticiel, peuvent être téléchargés ou clonés à partir de [GitHub][]. Si vous êtes intéressé par l'ajout de ce code à votre application, copiez-le et ajoutez-le à cette dernière.

Pour ajouter des fonctionnalités supplémentaires à votre application, passez en revue les API disponibles dans la [bibliothèque de documents DB .NET](http://msdn.microsoft.com/library/azure/dn783362.aspx) et n'hésitez pas à contribuer à la bibliothèque DocumentDB .NET sur [GitHub][]. 

##<a id="GetProject"></a>Obtention de la solution à partir de GitHub

Si vous voulez gagner du temps et souhaitez simplement générer la solution todo complète sans ajouter le code vous-même, vous avez de la chance. La solution complète est disponible sur GitHub et vous pouvez la générer et la déployer en quelques minutes à l'aide des instructions suivantes.

1. Vérifiez que vous avez installé tous les [logiciels prérequis](#_Toc395637760), qui incluent Visual Studio et le Kit de développement logiciel (SDK) Azure pour .NET version 2.3 ou ultérieure.

2. Clonez le référentiel azure-documentdb-net à l'aide de Git pour Windows ([http://www.git-scm.com/](http://www.git-scm.com/)), ou téléchargez le fichier zip depuis [GitHub](https://github.com/Azure/azure-documentdb-net/).

2. À partir de Visual Studio, ouvrez le fichier todo.sln à partir du répertoire azure-documentdb-net/tutorials/todo directory.

3. Pour restaurer les références au Kit de développement logiciel (SDK) .NET de DocumentDB dans Visual Studio 2013, cliquez avec le bouton droit sur la solution todo dans l'**Explorateur de solutions**, puis cliquez sur **Activer la restauration des packages NuGet**, qui permet de restaurer les références. 

4. Récupérez les valeurs de l'**URI**, de la **CLÉ PRIMAIRE** ou de la **CLÉ SECONDAIRE** dans le panneau **Clés** de votre compte DocumentDB dans le [portail Azure en version préliminaire](https://portal.azure.com/). 

	
	Si vous n'avez pas de compte, consultez la rubrique [Création d'un compte de base de données](documentdb-create-account.md) pour en créer un.

	![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the Keys button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade](../includes/media/documentdb-keys/keys.png)

5. Dans le fichier Web.config, mettez à jour les valeurs par défaut pour les clés **endpoint** et **authKey**.

    	<add key="endpoint" value="~enter URI for your DocumentDB Account, from Azure Management Portal~" /> 
		<add key="authKey" value="~enter either Primary or Secondary key for your DocumentDB Account, from Azure Management Portal~" /> 

	- Copiez la valeur de l'**URI** du panneau Clés et collez-la dans la valeur de la propriété **endpoint**. 
	- Copiez la valeur **CLÉ PRIMAIRE** ou **CLÉ SECONDAIRE** depuis le panneau **Clés** et collez-la dans la valeur de la propriété **authKey**.
	


7. Vous pouvez maintenant [exécuter l'application localement](#_Toc395637773), puis [déployer l'application sur des sites web Azure](#_Toc395637774).


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[GitHub]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Prévention des falsifications de requête intersites]: http://go.microsoft.com/fwlink/?LinkID=517254
[Opérations CRUD de base dans ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=49-->