<properties 
	pageTitle="Créer une application web Java avec DocumentDB" 
	description="Ce didacticiel vous montre comment utiliser le service Azure DocumentDB pour stocker les données et y accéder à partir d'une application Java hébergée sur Azure Websites." 
	services="documentdb" 
	documentationCenter="java" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.devlang="java" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="NA" 
	ms.workload="data-services" 
	ms.date="04/29/2015" 
	ms.author="andrl"/>

# Créer une application web Java avec DocumentDB #

Ce didacticiel vous montre comment utiliser le service [Microsoft Azure DocumentDB](https://portal.azure.com/#gallery/Microsoft.DocumentDB) pour stocker les données et y accéder à partir d'une application Java hébergée sur Azure Websites. Dans cette rubrique, vous allez apprendre à :

- Créer une application de base JSP dans Eclipse.
- Utiliser le service Azure DocumentDB avec le [Kit de développement logiciel (SDK) Java de DocumentDB](https://github.com/Azure/azure-documentdb-java).

Ce didacticiel vous montre comment créer une application de gestion des tâches basée sur le web qui vous permet de créer, de récupérer et de marquer des tâches comme terminées, comme illustré dans l'image suivante. Chacune des tâches dans la liste ToDo est stockée sous forme de documents JSON dans Azure DocumentDB.

![Application My ToDo List](./media/documentdb-java-application/image1.png)

> [AZURE.TIP]Ce didacticiel part du principe que vous avez déjà utilisé Java. Si vous débutez avec Java ou avec les [outils requis](#Prerequisites), nous vous recommandons de télécharger l'ensemble du projet [todo](https://github.com/Azure/azure-documentdb-java/tree/master/tutorial/todo) à partir de [GitHub](https://github.com/Azure/azure-documentdb-java) et de le générer à l'aide des [instructions fournies à la fin de cet article](#GetProject). Une fois que vous l'avez créé, vous pouvez consulter l'article pour obtenir des informations sur le code dans le contexte du projet.

##<a id="Prerequisites"></a>Configuration requise ##
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](../../pricing/free-trial/).
- [Kit de développement logiciel Java (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
- [Environnement de développement intégré (IDE) Eclipse pour développeurs Java EE.](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
- [Site web Azure avec un environnement d'exécution Java (Tomcat ou Jetty, par exemple) activé.](app-service-web/web-sites-java-get-started.md)

Si vous installez ces outils pour la première fois, coreservlets.com fournit un guide pas à pas du processus d'installation dans la section Démarrage rapide de son article [Didacticiel : installation de TomCat7 et son utilisation avec Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html).

##<a id="CreateDB"></a>Étape 1 : création d'un compte de base de données DocumentDB ##
Pour configurer un compte de base de données DocumentDB dans Azure :

1. Si vous ne disposez pas d'un compte de base de données, créez-en un en suivant les instructions de la page [Création d'un compte de base de données](documentdb-create-account.md). Si vous disposez déjà d'un compte, passez à l'étape 2.
2. À l'aide du panneau **Clés** indiqué dans l'illustration suivante, copiez l'**URI** et la **CLÉ PRIMAIRE** de votre point de terminaison dans votre Presse-papiers et conservez-les à portée de main, car nous utiliserons ces valeurs dans l'application web que nous allons créer.

![Capture d'écran du portail Azure, présentant un compte DocumentDB, avec le hub ACTIF et le bouton Clés mis en surbrillance dans le panneau du compte DocumentDB, et les valeurs d'URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés][1]


##<a id="CreateJSP"></a>Étape 2 : création de l'application JSP ##

Pour créer l'application JSP :

1. Tout d'abord, nous allons commencer par la création d'un projet Java. Démarrez Eclipse, puis cliquez sur **File** (Fichier), sur **New** (Nouveau), puis sur **Dynamic Web Project** (Projet web dynamique). Si **Dynamic Web Project** (Projet web dynamique) ne s'affiche pas dans les projets disponibles, procédez comme suit : cliquez sur **File** (Fichier), sur **New** (Nouveau), puis sur **Project** (Projet), développez **Web**, cliquez sur **Dynamic Web Project** (Projet web dynamique), puis sur **Next** (Suivant). 

	![](./media/documentdb-java-application/image10.png)

2. Entrez un nom de projet dans la zone **Project name** (Nom du projet) et sélectionnez **Target Runtime** (Runtime cible) dans le menu déroulant, vous pouvez aussi sélectionner une valeur (par exemple, Apache Tomcat v7.0), puis cliquez sur **Finish** (Terminer). La sélection d'un runtime cible vous permet d'exécuter votre projet localement via Eclipse.
3. Dans la vue de l'Explorateur de projets Eclipse, développez votre projet. Cliquez avec le bouton droit sur **WebContent**, cliquez sur **New**, puis sur **JSP File**.
4. Dans la boîte de dialogue **New JSP File** (Nouveau fichier JSP), nommez le fichier **index.jsp**. Gardez le nom du dossier parent **WebContent**, comme indiqué dans l'illustration ci-dessous, puis cliquez sur **Next** (Suivant).

	![](./media/documentdb-java-application/image11.png)

5. Pour les besoins de ce didacticiel, dans la boîte de dialogue **Select JSP Template** (Sélectionner le modèle JSP), sélectionnez **New JSP File (html)** (Nouveau fichier JSP (html)) et cliquez sur **Finish** (Terminer).

6. Quand le fichier index.jsp s'ouvre dans Eclipse, ajoutez du texte pour que **Hello World!** s'affiche dans l'élément <body> existant. Le contenu <body> mis à jour doit se présenter de la façon suivante :
    
	    <body>
	        <% out.println("Hello World!"); %>
	    </body>

8. Enregistrez le fichier index.jsp.
9. Si vous avez défini un runtime cible à l'étape 2, vous pouvez cliquer sur **Project** (Projet), puis sur **Run** (Exécuter) pour exécuter votre application JSP localement :

	![](./media/documentdb-java-application/image12.png)

##<a id="InstallSDK"></a>Étape 3 : installation du Kit de développement logiciel (SDK) Java de DocumentDB ##

Le moyen le plus simple d'extraire le Kit de développement logiciel (SDK) Java de DocumentDB et ses dépendances est d'utiliser [Apache Maven](http://maven.apache.org/).

Pour ce faire, vous devez convertir votre projet en projet Maven en procédant comme suit :

1. Cliquez avec le bouton droit sur votre projet dans l'Explorateur de projets, cliquez sur **Configure** (Configurer) et sur **Convert to Maven Project** (Convertir en projet Maven).
2. Dans la fenêtre **Create new POM** (Créer un nouveau POM), acceptez les valeurs par défaut et cliquez sur **Finish** (Terminer).
3. Dans l'**Explorateur de projets**, ouvrez le fichier pom.xml. 
4. Sous l'onglet **Dependencies** (Dépendances) du volet **Dependencies** (Dépendances), cliquez sur **Add** (Ajouter).
4. Dans la fenêtre **Select Dependency** (Sélectionner une dépendance), procédez comme suit :
 - Dans la zone **GroupId** (ID de groupe), entrez com.microsoft.azure.
 - Dans la zone **Artifact Id** (ID d'artefact), entrez azure-documentdb.
 - Dans la zone **Version**, entrez 1.0.0.

	![](./media/documentdb-java-application/image13.png)

	Ou ajoutez la dépendance XML pour GroupId et ArtifactId directement au fichier pom.xml via un éditeur de texte :

	    <dependency>
		    <groupId>com.microsoft.azure</groupId>
		    <artifactId>azure-documentdb</artifactId>
		    <version>1.0.0</version>
	    </dependency>

5. Cliquez sur **Ok** et Maven installe le Kit de développement logiciel (SDK) Java de DocumentDB.
6. Enregistrez le fichier pom.xml.

##<a id="UseService"></a>Étape 4 : utilisation du service DocumentDB dans une application Java ##

1. Tout d'abord, nous allons définir l'objet TodoItem :

	    @Data
	    @Builder
	    public class TodoItem {
		    private String category;
		    private boolean complete;
		    private String id;
		    private String name;
	    }

	Dans ce projet, nous utilisons [Project Lombok](http://projectlombok.org/) pour générer le constructeur, les accesseurs get, les méthodes setter et un générateur. Vous pouvez également écrire ce code manuellement ou faire en sorte que l'IDE le génère.

2. Pour appeler le service DocumentDB, vous devez instancier un nouveau **DocumentClient**. En général, il est préférable de réutiliser le **DocumentClient** plutôt que de construire un nouveau client à chaque demande. Nous pouvons réutiliser le client en l'encapsulant dans un **DocumentClientFactory**. C'est ici également que vous devez coller les valeurs d'URI et de CLÉ PRIMAIRE que vous avez enregistrées dans le Presse-papiers à l'[étape 1](#CreateDB). Remplacez [YOUR\\_ENDPOINT\\_HERE] par l'URI et [YOUR\\_KEY\\_HERE] par la CLÉ PRIMAIRE.

	    private static final String HOST = "[YOUR_ENDPOINT_HERE]";
	    private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
	
	    private static DocumentClient documentClient;
	
	    public static DocumentClient getDocumentClient() {
	        if (documentClient == null) {
	            documentClient = new DocumentClient(HOST, MASTER_KEY,
	                    ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
	        }
	
	        return documentClient;
	    }

3. Créons maintenant un objet d'accès aux données (DAO) pour conserver les éléments ToDo dans DocumentDB.

	Pour enregistrer les éléments ToDo dans une collection, le client doit connaître la base de données et la collection de stockage (référencées par les liens réflexifs). En général, il est préférable de mettre en cache la collection et la base de données lorsque cela est possible afin d'éviter les allers-retours supplémentaires vers la base de données.

	Le code suivant illustre comment récupérer notre base de données et notre collection si elle existe, ou en créer une si elle n'existe pas :

		public class DocDbDao implements TodoDao {
		    // The name of our database.
		    private static final String DATABASE_ID = "TodoDB";
		
		    // The name of our collection.
		    private static final String COLLECTION_ID = "TodoCollection";
		
		    // The DocumentDB Client
		    private static DocumentClient documentClient = DocumentClientFactory
		            .getDocumentClient();
		
		    // Cache for the database object, so we don't have to query for it to
		    // retrieve self links.
		    private static Database databaseCache;
		
		    // Cache for the collection object, so we don't have to query for it to
		    // retrieve self links.
		    private static DocumentCollection collectionCache;
		
		    private Database getTodoDatabase() {
		        if (databaseCache == null) {
		            // Get the database if it exists
		            List<Database> databaseList = documentClient
		                    .queryDatabases(
		                            "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
		                                    + "'", null).getQueryIterable().toList();
		
		            if (databaseList.size() > 0) {
		                // Cache the database object so we won't have to query for it
		                // later to retrieve the selfLink.
		                databaseCache = databaseList.get(0);
		            } else {
		                // Create the database if it doesn't exist.
		                try {
		                    Database databaseDefinition = new Database();
		                    databaseDefinition.setId(DATABASE_ID);
		
		                    databaseCache = documentClient.createDatabase(
		                            databaseDefinition, null).getResource();
		                } catch (DocumentClientException e) {
		                    // TODO: Something has gone terribly wrong - the app wasn't
		                    // able to query or create the collection.
		                    // Verify your connection, endpoint, and key.
		                    e.printStackTrace();
		                }
		            }
		        }
		
		        return databaseCache;
		    }
		
		    private DocumentCollection getTodoCollection() {
		        if (collectionCache == null) {
		            // Get the collection if it exists.
		            List<DocumentCollection> collectionList = documentClient
		                    .queryCollections(
		                            getTodoDatabase().getSelfLink(),
		                            "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
		                                    + "'", null).getQueryIterable().toList();
		
		            if (collectionList.size() > 0) {
		                // Cache the collection object so we won't have to query for it
		                // later to retrieve the selfLink.
		                collectionCache = collectionList.get(0);
		            } else {
		                // Create the collection if it doesn't exist.
		                try {
		                    DocumentCollection collectionDefinition = new DocumentCollection();
		                    collectionDefinition.setId(COLLECTION_ID);

		                    // Configure the new collection performance tier to S1.
		                    RequestOptions requestOptions = new RequestOptions();
		                    requestOptions.setOfferType("S1");
		
		                    collectionCache = documentClient.createCollection(
		                            getTodoDatabase().getSelfLink(),
		                            collectionDefinition, requestOptions).getResource();
		                } catch (DocumentClientException e) {
		                    // TODO: Something has gone terribly wrong - the app wasn't
		                    // able to query or create the collection.
		                    // Verify your connection, endpoint, and key.
		                    e.printStackTrace();
		                }
		            }
		        }
		
		        return collectionCache;
		    }
		}

4. L'étape suivante consiste à écrire du code pour conserver les TodoItems dans la collection. Dans cet exemple, nous utiliserons [Gson](https://code.google.com/p/google-gson/) pour sérialiser et désérialiser les objets POJO (Plain Old Java Object) TodoItem des documents JSON. [Jackson](http://jackson.codehaus.org/) ou votre propre sérialiseur personnalisé peuvent aussi servir à sérialiser les POJO.

	    // We'll use Gson for POJO <=> JSON serialization for this example.
	    private static Gson gson = new Gson();

	    @Override
	    public TodoItem createTodoItem(TodoItem todoItem) {
	        // Serialize the TodoItem as a JSON Document.
	        Document todoItemDocument = new Document(gson.toJson(todoItem));
	
	        // Annotate the document as a TodoItem for retrieval (so that we can
	        // store multiple entity types in the collection).
	        todoItemDocument.set("entityType", "todoItem");
	
	        try {
	            // Persist the document using the DocumentClient.
	            todoItemDocument = documentClient.createDocument(
	                    getTodoCollection().getSelfLink(), todoItemDocument, null,
	                    false).getResource();
	        } catch (DocumentClientException e) {
	            e.printStackTrace();
	            return null;
	        }
	
	        return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
	    }



5. De même que les collections et les bases de données DocumentDB, les documents sont référencés par des liens réflexifs. La fonction d'assistance suivante permet d'extraire les documents à l'aide d'un autre attribut (« id », par exemple) que le lien réflexif :

	    private Document getDocumentById(String id) {
	        // Retrieve the document using the DocumentClient.
	        List<Document> documentList = documentClient
	                .queryDocuments(getTodoCollection().getSelfLink(),
	                        "SELECT * FROM root r WHERE r.id='" + id + "'", null)
	                .getQueryIterable().toList();
	
	        if (documentList.size() > 0) {
	            return documentList.get(0);
	        } else {
	            return null;
	        }
	    }

6. Nous pouvons utiliser la méthode d'assistance de l'étape 5 pour récupérer un document JSON TodoItem par son id et le désérialiser ensuite en un POJO :

	    @Override
	    public TodoItem readTodoItem(String id) {
	        // Retrieve the document by id using our helper method.
	        Document todoItemDocument = getDocumentById(id);
	
	        if (todoItemDocument != null) {
	            // De-serialize the document in to a TodoItem.
	            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
	        } else {
	            return null;
	        }
	    }

7. Nous pouvons également utiliser le DocumentClient pour obtenir une collection ou liste de TodoItems à l'aide du langage SQL de DocumentDB :
	
	    @Override
	    public List<TodoItem> readTodoItems() {
	        List<TodoItem> todoItems = new ArrayList<TodoItem>();
	
	        // Retrieve the TodoItem documents
	        List<Document> documentList = documentClient
	                .queryDocuments(getTodoCollection().getSelfLink(),
	                        "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
	                        null).getQueryIterable().toList();
	
	        // De-serialize the documents in to TodoItems.
	        for (Document todoItemDocument : documentList) {
	            todoItems.add(gson.fromJson(todoItemDocument.toString(),
	                    TodoItem.class));
	        }
	
	        return todoItems;
	    }

8. Il existe plusieurs manières de mettre à jour un document avec le DocumentClient. Dans notre application de liste Todo, nous voulons pouvoir afficher ou masquer qu'un élément TodoItem est terminé. Cela est possible en mettant à jour l'attribut « complete » au sein du document :
	
	    @Override
	    public TodoItem updateTodoItem(String id, boolean isComplete) {
	        // Retrieve the document from the database
	        Document todoItemDocument = getDocumentById(id);
	
	        // You can update the document as a JSON document directly.
	        // For more complex operations - you could de-serialize the document in
	        // to a POJO, update the POJO, and then re-serialize the POJO back in to
	        // a document.
	        todoItemDocument.set("complete", isComplete);
	
	        try {
	            // Persist/replace the updated document.
	            todoItemDocument = documentClient.replaceDocument(todoItemDocument,
	                    null).getResource();
	        } catch (DocumentClientException e) {
	            e.printStackTrace();
	            return null;
	        }
	
	        return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
	    }

9. Enfin, nous souhaitons pouvoir supprimer un TodoItem de notre liste. Pour ce faire, nous pouvons utiliser la méthode d'assistance écrite précédemment afin de récupérer le lien réflexif et d'indiquer ensuite au client de le supprimer :
	
	    @Override
	    public boolean deleteTodoItem(String id) {
	        // DocumentDB refers to documents by self link rather than id.
	
	        // Query for the document to retrieve the self link.
	        Document todoItemDocument = getDocumentById(id);
	
	        try {
	            // Delete the document by self link.
	            documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
	        } catch (DocumentClientException e) {
	            e.printStackTrace();
	            return false;
	        }
	
	        return true;
	    }


##<a id="Wire"></a> Étape 5 : liaison du reste de l'application ##

Maintenant que nous avons terminé l'aspect amusant, il nous reste à créer une interface utilisateur rapide et à l'associer à notre objet d'accès aux données.

1. Tout d'abord, nous allons créer un contrôleur pour appeler notre objet d'accès aux données :

		public class TodoItemController {
		    public static TodoItemController getInstance() {
		        if (todoItemController == null) {
		            todoItemController = new TodoItemController(TodoDaoFactory.getDao());
		        }
		        return todoItemController;
		    }
		
		    private static TodoItemController todoItemController;
		
		    private final TodoDao todoDao;
		
		    TodoItemController(TodoDao todoDao) {
		        this.todoDao = todoDao;
		    }
		
		    public TodoItem createTodoItem(@NonNull String name,
		            @NonNull String category, boolean isComplete) {
		        TodoItem todoItem = TodoItem.builder().name(name).category(category)
		                .complete(isComplete).build();
		        return todoDao.createTodoItem(todoItem);
		    }
		
		    public boolean deleteTodoItem(@NonNull String id) {
		        return todoDao.deleteTodoItem(id);
		    }
		
		    public TodoItem getTodoItemById(@NonNull String id) {
		        return todoDao.readTodoItem(id);
		    }
		
		    public List<TodoItem> getTodoItems() {
		        return todoDao.readTodoItems();
		    }
		
		    public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
		        return todoDao.updateTodoItem(id, isComplete);
		    }
		}

	Dans une application plus complexe, le contrôleur peut héberger une logique métier complexe en plus de l'objet d'accès aux données.

2. Ensuite, nous allons créer un servlet pour acheminer les demandes HTTP vers le contrôleur :

		public class TodoServlet extends HttpServlet {
			// API Keys
			public static final String API_METHOD = "method";
		
			// API Methods
			public static final String CREATE_TODO_ITEM = "createTodoItem";
			public static final String GET_TODO_ITEMS = "getTodoItems";
			public static final String UPDATE_TODO_ITEM = "updateTodoItem";
		
			// API Parameters
			public static final String TODO_ITEM_ID = "todoItemId";
			public static final String TODO_ITEM_NAME = "todoItemName";
			public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
			public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
		
			public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
		
			private static final long serialVersionUID = 1L;
			private static final Gson gson = new Gson();
		
			@Override
			protected void doGet(HttpServletRequest request,
					HttpServletResponse response) throws ServletException, IOException {
		
				String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
		
				TodoItemController todoItemController = TodoItemController
						.getInstance();
		
				String id = request.getParameter(TODO_ITEM_ID);
				String name = request.getParameter(TODO_ITEM_NAME);
				String category = request.getParameter(TODO_ITEM_CATEGORY);
				boolean isComplete = StringUtils.equalsIgnoreCase("true",
						request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
		
				switch (request.getParameter(API_METHOD)) {
				case CREATE_TODO_ITEM:
					apiResponse = gson.toJson(todoItemController.createTodoItem(name,
							category, isComplete));
					break;
				case GET_TODO_ITEMS:
					apiResponse = gson.toJson(todoItemController.getTodoItems());
					break;
				case UPDATE_TODO_ITEM:
					apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
							isComplete));
					break;
				default:
					break;
				}
		
				response.getWriter().println(apiResponse);
			}
	
			@Override
			protected void doPost(HttpServletRequest request,
					HttpServletResponse response) throws ServletException, IOException {
				doGet(request, response);
			}
		}

3. Nous avons besoin d'une interface utilisateur web à afficher à l'utilisateur. Réécrivons le fichier index.jsp créé précédemment :

		<html>
		<head>
		  <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
		  <title>Azure DocumentDB Java Sample</title>
		
		  <!-- Bootstrap -->
		  <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
		
		  <style>
		    /* Add padding to body for fixed nav bar */
		    body {
		      padding-top: 50px;
		    }
		  </style>
		</head>
		<body>
		  <!-- Nav Bar -->
		  <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
		    <div class="container">
		      <div class="navbar-header">
		        <a class="navbar-brand" href="#">My Tasks</a>
		      </div>
		    </div>
		  </div>
		
		  <!-- Body -->
		  <div class="container">
		    <h1>My ToDo List</h1>
		
		    <hr/>
		
		    <!-- The ToDo List -->
		    <div class = "todoList">
		      <table class="table table-bordered table-striped" id="todoItems">
		        <thead>
		          <tr>
		            <th>Name</th>
		            <th>Category</th>
		            <th>Complete</th>
		          </tr>
		        </thead>
		        <tbody>
		        </tbody>
		      </table>
		
		      <!-- Update Button -->
		      <div class="todoUpdatePanel">
		        <form class="form-horizontal" role="form">
		          <button type="button" class="btn btn-primary">Update Tasks</button>
		        </form>
		      </div>
		
		    </div>
		
		    <hr/>
		
		    <!-- Item Input Form -->
		    <div class="todoForm">
		      <form class="form-horizontal" role="form">
		        <div class="form-group">
		          <label for="inputItemName" class="col-sm-2">Task Name</label>
		          <div class="col-sm-10">
		            <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
		          </div>
		        </div>
		
		        <div class="form-group">
		          <label for="inputItemCategory" class="col-sm-2">Task Category</label>
		          <div class="col-sm-10">
		            <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
		          </div>
		        </div>
		
		        <button type="button" class="btn btn-primary">Add Task</button>
		      </form>
		    </div>
		
		  </div>
		
		  <!-- Placed at the end of the document so the pages load faster -->
		  <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
		  <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
		  <script src="assets/todo.js"></script>
		</body>
		</html>

4. Et enfin, écrivons le code Javascript côté client pour lier l'interface utilisateur web et le servlet :
	
		var todoApp = {
		  /*
		   * API methods to call Java backend.
		   */
		  apiEndpoint: "api",
		
		  createTodoItem: function(name, category, isComplete) {
		    $.post(todoApp.apiEndpoint, {
		        "method": "createTodoItem",
		        "todoItemName": name,
		        "todoItemCategory": category,
		        "todoItemComplete": isComplete
		      },
		      function(data) {
		        var todoItem = data;
		        todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
		      },
		      "json");
		  },
		
		  getTodoItems: function() {
		    $.post(todoApp.apiEndpoint, {
		        "method": "getTodoItems"
		      },
		      function(data) {
		        var todoItemArr = data;
		        $.each(todoItemArr, function(index, value) {
		          todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
		        });
		      },
		      "json");
		  },
		
		  updateTodoItem: function(id, isComplete) {
		    $.post(todoApp.apiEndpoint, {
		        "method": "updateTodoItem",
		        "todoItemId": id,
		        "todoItemComplete": isComplete
		      },
		      function(data) {},
		      "json");
		  },
		
		  /*
		   * UI Methods
		   */
		  addTodoItemToTable: function(id, name, category, isComplete) {
		    var rowColor = isComplete ? "active" : "warning";
		
		    todoApp.ui_table().append($("<tr>")
		      .append($("<td>").text(name))
		      .append($("<td>").text(category))
		      .append($("<td>")
		        .append($("<input>")
		          .attr("type", "checkbox")
		          .attr("id", id)
		          .attr("checked", isComplete)
		          .attr("class", "isComplete")
		        ))
		      .addClass(rowColor)
		    );
		  },
		
		  /*
		   * UI Bindings
		   */
		  bindCreateButton: function() {
		    todoApp.ui_createButton().click(function() {
		      todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
		      todoApp.ui_createNameInput().val("");
		      todoApp.ui_createCategoryInput().val("");
		    });
		  },
		
		  bindUpdateButton: function() {
		    todoApp.ui_updateButton().click(function() {
		      // Disable button temporarily.
		      var myButton = $(this);
		      var originalText = myButton.text();
		      $(this).text("Updating...");
		      $(this).prop("disabled", true);
		
		      // Call api to update todo items.
		      $.each(todoApp.ui_updateId(), function(index, value) {
		        todoApp.updateTodoItem(value.name, value.value);
		        $(value).remove();
		      });
		
		      // Re-enable button.
		      setTimeout(function() {
		        myButton.prop("disabled", false);
		        myButton.text(originalText);
		      }, 500);
		    });
		  },
		
		  bindUpdateCheckboxes: function() {
		    todoApp.ui_table().on("click", ".isComplete", function(event) {
		      var checkboxElement = $(event.currentTarget);
		      var rowElement = $(event.currentTarget).parents('tr');
		      var id = checkboxElement.attr('id');
		      var isComplete = checkboxElement.is(':checked');
		
		      // Togle table row color
		      if (isComplete) {
		        rowElement.addClass("active");
		        rowElement.removeClass("warning");
		      } else {
		        rowElement.removeClass("active");
		        rowElement.addClass("warning");
		      }
		
		      // Update hidden inputs for update panel.
		      todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
		
		      todoApp.ui_updateForm().append($("<input>")
		        .attr("type", "hidden")
		        .attr("class", "updateComplete")
		        .attr("name", id)
		        .attr("value", isComplete));
		
		    });
		  },
		
		  /*
		   * UI Elements
		   */
		  ui_createNameInput: function() {
		    return $(".todoForm #inputItemName");
		  },
		
		  ui_createCategoryInput: function() {
		    return $(".todoForm #inputItemCategory");
		  },
		
		  ui_createButton: function() {
		    return $(".todoForm button");
		  },
		
		  ui_table: function() {
		    return $(".todoList table tbody");
		  },
		
		  ui_updateButton: function() {
		    return $(".todoUpdatePanel button");
		  },
		
		  ui_updateForm: function() {
		    return $(".todoUpdatePanel form");
		  },
		
		  ui_updateId: function() {
		    return $(".todoUpdatePanel .updateComplete");
		  },
		
		  /*
		   * Install the TodoApp
		   */
		  install: function() {
		    todoApp.bindCreateButton();
		    todoApp.bindUpdateButton();
		    todoApp.bindUpdateCheckboxes();
		
		    todoApp.getTodoItems();
		  }
		};
		
		$(document).ready(function() {
		  todoApp.install();
		});

5. Génial ! Maintenant, il ne nous reste qu'à tester l'application. Exécutez l'application localement et ajoutez des éléments Todo en renseignant le nom et la catégorie de l'élément, puis en cliquant sur **Add Task** (Ajouter une tâche).

6. Une fois que l'élément s'affiche, vous pouvez le mettre à jour s'il est terminé en activant/désactivant la case à cocher et en cliquant sur **Update Tasks** (Mettre à jour les tâches).

##<a id="Deploy"></a>Étape 6 : déploiement de votre application sur Azure Websites ##

Azure Websites permet de déployer facilement les applications Java en exportant votre application sous forme de fichier WAR et en la téléchargeant via le contrôle de code source (GIT, par exemple) ou FTP.

1. Pour exporter votre application en tant que fichier WAR, cliquez avec le bouton droit sur votre projet dans l'**Explorateur de projets**, cliquez sur **Export** (Exporter), puis sur **WAR File** (Fichier WAR). 
2. Dans la fenêtre **WAR Export** (Exportation WAR), procédez comme suit :
 - Dans la boîte de dialogue de projet web, entrez azure-documentdb-java-sample.
 - Dans la boîte de dialogue Destination, choisissez un emplacement d'enregistrement du fichier WAR.
 - Cliquez sur **Terminer**.

3. Maintenant que vous disposez d'un fichier WAR, vous pouvez le télécharger tout simplement dans le répertoire **webapps** de votre site web Azure. Pour des instructions sur le téléchargement du fichier, consultez [Ajout d'une application à votre site web Java sur Azure](app-service-web/web-sites-java-add-app.md).

	Une fois le fichier WAR téléchargé sur le répertoire webapps, l'environnement d'exécution détecte que vous l'avez ajouté et le télécharge automatiquement.
4. Pour afficher votre produit fini, accédez à http://YOUR_SITE_NAME.azurewebsites.net/azure-documentdb-java-sample/ et commencez à ajouter vos tâches.

##<a id="GetProject"></a>Obtenir le projet à partir de GitHub##

Tous les exemples de ce didacticiel sont inclus dans le projet [todo](https://github.com/Azure/azure-documentdb-java/tree/master/tutorial/todo) sur GitHub, qui fait partie du référentiel [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java). Pour importer le projet todo dans Eclipse, vérifiez d'abord que vous disposez des logiciels et ressources indiqués dans la section [Configuration requise](#Prerequisites), puis procédez comme suit :

1. Installez [Project Lombok](http://projectlombok.org/). Lombok est utilisé pour générer des constructeurs, des méthodes getter et des méthodes setter dans le projet. Une fois que vous avez téléchargé le fichier lombok.jar, double-cliquez dessus pour l'installer ou installez-le à partir de la ligne de commande. 
2. Si l'application Eclipse est ouverte, fermez-la et redémarrez-la pour charger Lombok.
3. Dans Eclipse, dans le menu **File** (Fichier), cliquez sur **Import** (Importer).
4. Dans la fenêtre **Import** (Importer), cliquez sur **Git**, sur **Projects from Git** (Projets provenant de Git), puis sur **Next** (Suivant). 
5. Dans l'écran **Select Repository Source** (Sélectionner une source de référentiel), cliquez sur **Clone URI** (URI du clone).
6. Dans l'écran **Source Git Repository** (Référentiel du Git source), dans la zone **URI**, entrez https://github.com/Azure/azure-documentdb-java.git, puis cliquez sur **Next** (Suivant).
7. Dans l'écran **Branch Selection** (Sélection d'une branche), vérifiez que **master** (principal) est sélectionné, puis cliquez sur **Next** (Suivant).
8. Dans l'écran **Local Destination** (Destination locale), cliquez sur **Browse** (Parcourir) pour sélectionner un dossier dans lequel copier le référentiel, puis cliquez sur **Next** (Suivant).
9. Dans l'écran **Select a wizard to use for importing projects** (Sélectionner un Assistant à utiliser pour l'importation de projets), vérifiez que **Import existing projects** (Importer des projets existants) est sélectionné, puis cliquez sur **Next** (Suivant).
10. Dans l'écran **Import Projects** (Importer des projets), désélectionnez le projet **DocumentDB**, puis cliquez sur **Finish** (Terminer). Le projet DocumentDB contient le Kit de développement logiciel (SDK) Java de DocumentDB, que nous allons ajouter en tant que dépendance à la place.
11. Dans l'**Explorateur de projets**, accédez à azure-documentdb-java-sample\\src\\com.microsoft.azure.documentdb.sample.dao\\DocumentClientFactory.java et remplacez les valeurs HOST et MASTER_KEY par l'URI et la CLÉ PRIMAIRE de votre compte DocumentDB, puis enregistrez le fichier. Pour plus d'informations, consultez l'[Étape 1. création d'un compte de base de données DocumentDB](#CreateDB).
12. Dans l'**Explorateur de projets**, cliquez avec le bouton droit sur **azure-documentdb-java-sample**, cliquez sur **Build Path** (Chemin de build), puis sur **Configure Build Path** (Configurer le chemin de build).
13. Dans l'écran **Java Build Path** (Chemin de build Java), dans le volet droit, sélectionnez l'onglet **Libraries** (Bibliothèques), puis cliquez sur **Add External JARs** (Ajouter des JAR externes). Accédez à l'emplacement du fichier lombok.jar et cliquez sur **Open** (Ouvrir), puis sur **OK**.
14. Utilisez l'étape 12 pour rouvrir la fenêtre **Properties** (Propriétés), puis, dans le volet de gauche, cliquez sur **Targeted Runtimes** (Runtime ciblés).
15. Dans l'écran **Targeted Runtimes** (Runtime ciblés), cliquez sur **New** (Nouveau), sélectionnez **Apache Tomcat v7.0**, puis cliquez sur **OK**.
16. Suivez l'étape 12 pour rouvrir la fenêtre **Properties** (Propriétés) et, dans le volet de gauche, cliquez sur **Project Facets** (Facettes du projet).
17. Dans l'écran **Project Facets** (Facettes du projet), sélectionnez **Dynamic Web Module** (Module web dynamique) et **Java**, puis cliquez sur **OK**.
18. Sous l'onglet **Servers** (Serveurs) en bas de l'écran, cliquez sur **Tomcat v7.0 Server at localhost** (Serveur Tomcat v7.0 dans localhost), puis cliquez sur **Add and Remove** (Ajouter et supprimer).
19. Dans la fenêtre **Add and Remove** (Ajouter et supprimer), déplacez **azure-dfocumentdb-java-sample** vers la zone **Configured** (Configuré), puis cliquez sur **Finish** (Terminer). 
20. Sous l'onglet **Server** (Serveur), cliquez avec le bouton droit sur **Tomcat v7.0 Server at localhost** (Serveur Tomcat v7.0 dans localhost), puis cliquez sur **Restart** (Redémarrer).
21. Dans un navigateur, accédez à http://localhost:8080/azure-documentdb-java-sample/ et commencez à ajouter des tâches à votre liste de tâches. Notez que si vous avez modifié les valeurs du port par défaut, définissez 8080 sur la valeur que vous avez sélectionnée.
22. Pour déployer votre projet sur un site web Azure, consultez l'[Étape 6. déploiement de votre application sur Azure Websites](#Deploy). 

[1]: ../includes/media/documentdb-keys/keys.png

<!--HONumber=52-->
