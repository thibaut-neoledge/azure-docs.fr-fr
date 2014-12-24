<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Création d'une application web avec ASP.NET MVC et DocumentDB | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">Build a web application with ASP.NET MVC using DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Overview</a>
==============================================================================================

<a name="_Toc395637759">Scenario</a>
------------------------------------

To highlight how customers can efficiently leverage Azure DocumentDB to store and query JSON documents, this document provides an end-to-end walk-through of building a ToDo List web application using Azure Document DB.

This walk-through shows you how to use DocumentDB service provided by Azure to store and access data from an ASP.NET MVC web application hosted on Azure and presumes that you have some prior experience using ASP.NET MVC and Azure Websites.

You will learn:

1\. Creating and provisioning a DocumentDB Account

2\. Creating a ASP.NET MVC Application

3\. Connecting to and using Azure DocumentDB from your web application

4\. Deploying the Web Application to Azure Websites

By following this walk-through, you will build a simple web-based task-management application that allows creating, retrieving and completing of tasks. The tasks will be stored as JSON documents in Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Before following the instructions in this article, you should ensure that you have the following installed:

Git for Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (or [Visual Studio Express][] which is the free version)

Azure SDK for .NET version 2.3 or higher, available through [Microsoft Web Platform Installer][]

All the screen shots in this document have been taken using Visual Studio 2013 with Update 3 applied and Azure SDK for .NET version 2.4. If your system is configured with different versions it is possible that your screens and options won't match entirely, but if you meet the above prerequisites this solution should work.

<a name="_Toc395637761">Create a DocumentDB database account</a>
================================================================

To provision a DocumentDB database account in Azure, open the Azure Management Portal and either Click the Azure Gallery tile on the homepage or click "+" in the lower left hand corner of the screen.

![Alt text](./media/documentdb-dotnet-application/image2.png)


This will open the Azure Gallery, where you can select from the many available Azure services. In the Gallery, select "Data, storage and backup" from the list of categories.

![Alt text](./media/documentdb-dotnet-application/image3.png)

From here, select the option for Azure DocumentDB

![Alt text](./media/documentdb-dotnet-application/image4.png)


Then select "Create" from the bottom of the screen

![Alt text](./media/documentdb-dotnet-application/image5.png)

This will open up the "New DocumentDB" blade where you can specify the name, region, scale, resource group and other settings for your new account.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Once you're done supplying the values for your account, click "Create" and the provisioning process will begin creating your database account.
When the provisioning process is complete, you should see a notification appear in the notifications area of the portal and the tile on your start screen (if you selected to create one) will change to show the completed action.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Once provisioning is complete, clicking the DocumentDB tile from the start screen will bring up the main blade for this newly created DocumentDB account.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Using the "Keys" button, access your endpoint URL and the Primary Key, copy these to your clipboard and keep them handy as we will use these values in the web application we will use these values in the web application we will create next.

We will now walk through how to create a new ASP.NET MVC application from the ground-up. For your reference the complete solution can be downloaded [here].

<a name="_Toc395637762">Create a new ASP.NET MVC application</a>
================================================================

In Visual Studio, click File - New Project and select the option to create a new ASP.NET MVC Web Application.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Select where you would like to create the project, what you want the project to be called (I'm using "todo") and click Ok.

![Alt text](./media/documentdb-dotnet-application/image11.png)


If you plan on hosting your application in Azure then select the box on the lower right to "Host in the cloud". We've selected to host in the cloud, and run the application hosted in an Azure Website. Selecting this option will pre-provision an Azure Website for you and make life a lot easier when it comes times to deploy the final working application. If you want to host this elsewhere or don't want to configure Azure upfront, then just un-check the "Host in the Cloud" option.

Select OK and let Visual Studio do its thing around scaffolding the empty ASP.NET MVC template. If you chose to host this in the Cloud you will see at least one additional screen asking you to login to your Azure account and provide some values for your new Website. Proceed to supply all these Azure values and continue. 

I haven't chosen a "Database server" here because we're not using an Azure SQL Database Server here, we're going to be creating a new Azure DocumentDB Account later on in the management portal. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Once Visual Studio has finished creating the boilerplate MVC application you have an empty ASP.NET application that you can run locally.

We'll skip running locally because I'm sure we've all seen the ASP.NET "Hello World" application. Let's go straight to adding DocumentDB to this project and building our application.

</h1>
<a name="_Toc395637767">Adding DocumentDB to your project</a>
=============================================================

That takes care of most of the ASP.NET MVC plumbing that we need for
this solution. Now let's get to the real purpose of this tutorial, adding Azure DocumentDB to our web application.

### 

### <a name="_Toc395637764">Installing the NuGet package</a>

The DocumentDB .NET SDK is packaged and distributed as a NuGet package. Using the NuGet package manager in Visual Studio (which you can get to by Right-Clicking on the Project and choosing "Manage NuGet Packages"

![Alt text](./media/documentdb-dotnet-application/image21.png)

Search for Online for "Azure DocumentDB" and install the package. This will download and install the DocumentDB package as well as all dependencies, like Newtonsoft.Json.

**NOTE:** While the service is still in preview, the NuGet package is marked as "Prerelease" so you need to include the option to "Include Prerelease", else you will not find the package. 

Alternatively you could use the Package Command Console to install the package by typing;

    Install-Package Microsoft.Azure.Documents.Client -Pre

Once installed your Visual Studio solution should resemble the following with two new references added;

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Setting up the ASP.NET MVC application</a>
==================================================================

### 

### <a name="_Toc395637764">Add a Model</a>

Let's begin by creating the **M** in MVC, the model. In Solution Explorer, right-click the *Models* folder and then click **Add**, then **Class**

![Alt text](./media/documentdb-dotnet-application/image12.png)

Name your new Class, **Item** and then add the following code in to this new Class

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

All data in DocumentDB is passed over the wire, and stored, as JSON. To control the way your objects are serialized/deserialized by JSON.NET you can use the JsonProperty attribute as demonstrated in the Item class we just created. You don't **have** to do this but i wanted to ensure that my properties followed the JSON camelCase naming conventions. 

Not only can you control the format of the property name when it goes in to JSON, but you can entirely rename your .NET properties like I did with the Description property. 

You can, if you like, use JsonConverter objects here as well to completely control how serialization is handled.  

In order to get Visual Studio to resolve the "JsonProperty" attribute used here you need to add the following using statement to the using section of your class file;

    using Newtonsoft.Json;

### <a name="_Toc395637765">Add a Controller</a>

That takes care of the M, now let's create the **C** in MVC, a controller class.
In **Solution Explorer**, right-click the *Controllers* folder and then click **Add**, then **Controller**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


In the **Add Scaffold** dialog box, click **MVC 5 Controller - Empty.** Click **Add.**

![Alt text](./media/documentdb-dotnet-application/image15.png)

Name your new Controller, **ItemController.**

Visual Studio will now add the ItemController your Solution Explorer should look like similar to below.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Add Views</a>

And finally, let's create the **V** in MVC, a view.


#### Add Item Index View

Expand the ***Views***  folder in Solution Explorer and location the (empty) Item folder which Visual Studio would've created for you when you added the *ItemController* earlier. Right click on ***Item*** and choose to Add a new View.

![Alt text](./media/documentdb-dotnet-application/image17.png)

In the "Add View" dialog. Call your view "***Index***", use the ***List*** Template, select the ***Item (todo.Models)*** which we created earlier as the class and finally use the ***~Views/Shared/_Layout.cshtml*** in the Solution as the Layout page.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Once all these values are set, click Add and let Visual Studio create your view for you. Visual Studio will create a template view. Once it is done, it will open the cshtml file created. We can close this document in Visual Studio as we will come back to it later.

#### Add New Item View

In a similar fashion to above, create a new View for creating new Items as per the example shown below;

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Add Edit Item View

<a name="_Toc395888515"></a>
============================

And finally, add one last View for editing an Item in the same way as before;

![Alt text](./media/documentdb-dotnet-application/image20.png)


Once this is done, close the cshtml documents in Visual Studio as we will return to these Views later.

</h1>
<a name="_Toc395637769">Wiring up DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Listing Incomplete Items</a>

Open the **ItemController** and remove all the code within the class (but leave the class) that Visual Studio added. We'll rebuild it piece by piece using DocumentDB.

Add the following code snippet within the now empty ItemController class;

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

This code also uses a "pseudo repository" class called DocumentDBRepository which we have yet to create. This is actually just a Helper class that contains all the DocumentDB specific code. For the purposes of this walk-through we aren't going to implement a full data access layer with dependency injection, and factories and repository patterns, as you would probably be doing if you were building a real world application. 
For the purposes of this walk-through we're just going to put all the data access logic directly in to one project to keep things simple and focus on the DocumentDB specific bits.

Add a new Class to your project and call it **DocumentDBRepository.**
Replace the code in the class file with the following;

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

Spend some time resolving all the namespaces in Visual Studio. All namespaces will be easy to resolve in Visual Studio as long as the NuGet package was installed successfully. The references to the ReadOrCreateDatabase and ReadOrCreateCollection methods will remain unresolved until we have added them, which comes next. 

There are two method calls used here for reading or creating DocumentDB Databases and Document Collections.
So add the following two methods to this class;

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

This takes care setting up the Database, a DocumentCollection, and creating some code to connect to DocumentDB through the DocumentClient. 

We're reading some values from configuration, so open the **web.config** and add the following lines under the
<AppSettings\> section;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

﻿À présent, nous allons ajouter du code pour effectuer le travail. 
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

Ouvrez ***App\_Start\RouteConfig.cs*** et recherchez la ligne qui commence par " defaults: " et modifiez-la comme suit.

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Ce code indique maintenant à ASP.NET MVC que vous n'avez pas spécifié de valeur dans l'URL pour contrôler le comportement de routage qui, au lieu de " Home ", utilise " Item " comme contrôleur et " Index " comme vue.
Si vous exécutiez l'application dans son état actuel, celle-ci appellerait **ItemController** et renverrait les résultats de la méthode **GetIncompleteItems** à la vue Views\Item\Index. 

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

**Remarque relative à la sécurité** : L'attribut [ValidateAntiForgeryToken] est utilisé ici pour protéger cette application contre les falsifications de requête intersite. En plus de cela, vos vues doivent également fonctionner avec ce jeton anti-falsification. Pour plus d'informations sur le sujet et des exemples illustrant une implémentation adéquate, veuillez consulter [Prévention des falsifications de requête intersites][]. Le code source du lien de téléchargement situé à la fin de cet article permet de mettre en place l'implémentation complète.

**Remarque relative à la sécurité** : Nous utilisons également l'attribut [Bind] sur le paramètre de la méthode pour établir une protection contre les attaques par surcharge. Pour plus d'informations, veuillez consulter [Opérations CRUD de base dans ASP.NET MVC][]

Une fois ceci mis en place, le contrôleur Items Controller peut   transmettre de façon l'élément de façon sécurisée à partir du formulaire, à la méthode CreateDocument du référentiel de la classe. Ajoutez maintenant la méthode suivante à votre classe DocumentDBRepository.

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

2\.Cliquez sur l'option Modifier située à côté d'un élément de la liste. Vous êtes alors redirigé vers la vue Edit où vous pouvez mettre à jour les propriétés de votre objet, notamment l'indicateur " completed ". L'élément est marqué comme terminé, puis supprimé de la liste des tâches non terminées.

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.Pour terminer une tâche, cochez la case et cliquez sur **Enregistrer.** Vous êtes redirigé vers la page de liste où l'élément ne figure plus.

</h3>
<a name="_Toc395637774">Déploiement de l'application dans Sites Web Azure</a>
================================================================

### 

Maintenant que l'application terminée fonctionne correctement avec DocumentDB, nous allons la déployer vers Sites Web Azure.

Si vous avez sélectionné " Héberger dans le cloud " lorsque nous avons créé le projet ASP.NETMVC vide, Visual Studio vous facilite la tâche en faisant presque tout le travail pour vous. Pour publier cette application, cliquez avec le bouton droit sur le projet dans l'Explorateur de solutions (en vérifiant que nous n'êtes plus en local), puis sélectionnez Publier.

![Alt text](./media/documentdb-dotnet-application/image28.png)

Tout doit être déjà configuré selon vos informations d'identification. En fait, le site web a déjà été créé dans Azure à l'URL de destination indiquée. Il vous reste juste à cliquer sur **Publier**.

![Alt text](./media/documentdb-dotnet-application/image29.png)

En quelques secondes, Visual Studio achève la publication de votre application web et lance un navigateur dans lequel vous pouvez voir votre réalisation exécutée dans Azure !

</h3>



<a name="_Toc395637775">Conclusion</a>
======================================

### 

Félicitations ! Vous venez de créer votre première application ASP.NET MVC à l'aide d'Azure DocumentDB et de la publier dans Sites Web Azure. Vous pouvez télécharger le code source de l'application de référence complète, incluant les fonctionnalités Details et Delete dont nous n'avons pas parlé, [ici][].


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
[Programme d'installation de la plate-forme web Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
[ici]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Prévention des falsifications de requête intersites]: http://go.microsoft.com/fwlink/?LinkID=517254
[Opérations CRUD de base dans ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
