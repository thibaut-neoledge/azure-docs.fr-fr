<properties 
	pageTitle="Création d'une application web Node.js avec DocumentDB | Azure" 
	description="Apprenez à utiliser Microsoft Azure DocumentDB pour stocker des données et y accéder à partir d'une application web Node.js Express hébergée sur Sites Web Azure." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="03/20/2015" 
	ms.author="ryancraw"/>

# <a name="_Toc395783175"></a>Création d'une application web Node.js avec DocumentDB

Ce didacticiel vous montre comment utiliser le service Azure DocumentDB pour stocker des données et accéder à ces dernières à partir d'une application Node.js Express hébergée sur Sites Web Azure.

Nous vous recommandons de commencer par visionner la vidéo suivante, dans laquelle Andrew Liu explique comment approvisionner un compte de base de données Azure DocumentDB et stocker des documents JSON dans votre application Node.js. 

> [AZURE.VIDEO azure-demo-getting-started-with-azure-documentdb-on-nodejs-in-linux]

Ensuite, revenez à cet article dans lequel vous découvrirez les réponses aux questions suivantes :

- Comment utiliser DocumentDB à l'aide du module npm de documentdb ?
- Comment déployer l'application web sur Sites Web Azure ?

Dans ce didacticiel, vous allez créer une application web simple de
gestion des tâches permettant de créer, de récupérer et de
terminer des tâches. Ces dernières sont stockées en tant que documents JSON dans Azure
DocumentDB.

![Screen shot of the My Todo List application created in this tutorial](./media/documentdb-nodejs-application/image1.png)

Vous n'avez pas le temps de terminer le didacticiel et vous souhaitez simplement obtenir la solution complète de GitHub ? Ce n'est pas un problème. Vous pouvez l'obtenir [ici](https://github.com/Azure/azure-documentdb-node/tree/master/tutorial/todo).

## <a name="_Toc395783176"></a>Conditions préalables

> [AZURE.TIP] Ce didacticiel suppose que vous avez déjà utilisé Node.js et Sites Web Azure.

Avant de suivre les instructions de cet article, vérifiez que
vous disposez des éléments suivants :

- Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](../../pricing/free-trial/).
- [Node.js][] version v0.10.29 ou ultérieure.
- [Générateur Express](http://www.expressjs.com/starter/generator.html) (installation possible via `npm install express-generator -g`)
- [Git][].

## <a name="_Toc395637761"></a>Étape 1 : Création d'un compte de base de données DocumentDB

Commençons par créer un compte DocumentDB. Si vous avez déjà un compte, vous pouvez passer à l'[étape 2 : Création d'une application Node.js](#_Toc395783178).

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>Étape 2 : Création d'une application Node.js

Nous allons maintenant créer un projet Node.js " Hello World " simple en utilisant l'infrastructure [Express](http://expressjs.com/).

1. Ouvrez votre terminal préféré.

2. Utilisez le générateur Express pour générer une nouvelle application appelée **todo**.

		express todo

3. Ouvrez votre nouveau répertoire **todo** et installez les dépendances.

		cd todo
		npm install

4. Exécutez votre nouvelle application.

		npm start

5. Vous pouvez afficher votre nouvelle application en accédant à l'adresse [http://localhost:3000](http://localhost:3000) dans votre navigateur.

	![Screenshot of the Hello World application in a browser window](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>Étape 3 : Installation de modules supplémentaires

Le fichier **package.json** est l'un des fichiers créés à la racine du
projet. Il contient une liste des modules supplémentaires qui sont
nécessaires pour votre application Node.js. Ensuite, lorsque vous déployez cette
application sur Sites Web Azure, ce fichier permet de déterminer
quels modules doivent être installés sur Azure pour prendre en charge votre application. Nous avons besoin d'installer deux autres packages pour ce didacticiel.

1. De retour dans le terminal, installez le module **async** via npm.

		npm install async --save

1. Installez le module **documentdb** via npm. Il s'agit du module où se produit toute la magie de DocumentDB.

		npm install documentdb --save

3. Une vérification rapide du fichier **package.json** de l'application doit faire apparaître les modules complémentaires. Ce fichier indiquera à Azure les packages à télécharger et à installer lors de l'exécution de votre application. Il doit ressembler à l'exemple ci-dessous.

	![Screenshot of the package.json tab](./media/documentdb-nodejs-application/image17.png)

       Ce code indique à Node (et à Azure ultérieurement) que votre application dépend de ces modules supplémentaires.

## <a name="_Toc395783180"></a>Étape 4 : Utilisation du service DocumentDB dans une application Node

Ceci concerne l'ensemble de l'installation et de la configuration initiales. Venons-en à présent à la raison de notre présence ici, à savoir écrire du code avec Azure DocumentDB.

### Création du modèle

1. Dans le répertoire de projet, créez un répertoire nommé **models**.
2. Dans le répertoire **models**, créez un fichier nommé **taskDao.js**. Ce fichier contiendra le modèle des tâches créées par votre application.
3. Dans le même répertoire **models**, créez un autre fichier nommé **docdbUtils.js**. Ce fichier contient du code utile et réutilisable que nous utiliserons dans notre application. 
4. Copiez le code suivant dans **docdbUtils.js**

		var DocumentDBClient = require('documentdb').DocumentClient;
			
		var DocDBUtils = {
		    getOrCreateDatabase: function (client, databaseId, callback) {
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: databaseId
		            }]
		        };
		
		        client.queryDatabases(querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                if (results.length === 0) {
		                    var databaseSpec = {
		                        id: databaseId
		                    };
		
		                    client.createDatabase(databaseSpec, function (err, created) {
		                        callback(null, created);
		                    });
		
		                } else {
		                    callback(null, results[0]);
		                }
		            }
		        });
		    },
		
		    getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: collectionId
		            }]
		        };
		        
		        client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {		
		                if (results.length === 0) {
		                    var collectionSpec = {
		                        id: collectionId
		                    };
		
		                    client.createCollection(databaseLink, collectionSpec, function (err, created) {
		                        callback(null, created);
		                    });
		
		                } else {
		                    callback(null, results[0]);
		                }
		            }
		        });
		    }
		};
				
		module.exports = DocDBUtils;
		
3. Enregistrez, puis fermez le fichier **docdbUtils.js**.

4. Au début du fichier **taskDao.js**, ajoutez le code suivant pour faire référence au **DocumentDBClient** et au fichier **docdbUtils.js** créé précédemment :

        var DocumentDBClient = require('documentdb').DocumentClient;
		var docdbUtils = require('./docdbUtils');

4. Ensuite, vous allez ajouter du code pour définir et exporter l'objet Task. Il est responsable de l'initialisation de notre objet Task et de la configuration de la base de données et de la collection de documents que nous allons utiliser.

		function TaskDao(documentDBClient, databaseId, collectionId) {
		  this.client = documentDBClient;
		  this.databaseId = databaseId;
		  this.collectionId = collectionId;
		
		  this.database = null;
		  this.collection = null;
		}
		
		module.exports = TaskDao;

5. Ensuite, ajoutez le code suivant pour définir des méthodes supplémentaires sur l'objet Task permettant d'interagir avec les données stockées dans DocumentDB.

		TaskDao.prototype = {
		    init: function (callback) {
		        var self = this;
		
		        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
		            if (err) {
		                callback(err);

		            } else {
		                self.database = db;
		                docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
		                    if (err) {
		                        callback(err);
		
		                    } else {
		                        self.collection = coll;
		                    }
		                });
		            }
		        });
		    },
		
		    find: function (querySpec, callback) {
		        var self = this;
		
		        self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, results);
		            }
		        });
		    },
		
		    addItem: function (item, callback) {
		        var self = this;
		
		        item.date = Date.now();
		        item.completed = false;
		
		        self.client.createDocument(self.collection._self, item, function (err, doc) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, doc);
		            }
		        });
		    },
		
		    updateItem: function (itemId, callback) {
		        var self = this;
		
		        self.getItem(itemId, function (err, doc) {
		            if (err) {
		                callback(err);
		
		            } else {
		                doc.completed = true;
		
		                self.client.replaceDocument(doc._self, doc, function (err, replaced) {
		                    if (err) {
		                        callback(err);
		
		                    } else {
		                        callback(null, replaced);
		                    }
		                });
		            }
		        });
		    },
		
		    getItem: function (itemId, callback) {
		        var self = this;
		
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: itemId
		            }]
		        };
		
		        self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, results[0]);
		            }
		        });
		    }
		};

6. Enregistrez, puis fermez le fichier **taskDao.js**. 

### Création du contrôleur

1. Dans le répertoire **routes** de votre projet, créez un nouveau fichier nommé **tasklist.js**. 
2. Ajoutez le code suivant dans **tasklist.js**. Ce code charge les modules DocumentDBClient et async qui sont utilisés par **tasklist.js**. Cela a également permis de définir la fonction **TaskList**, à qui est transmise une instance de l'objet **Task** défini précédemment :

		var DocumentDBClient = require('documentdb').DocumentClient;
		var async = require('async');
		
		function TaskList(taskDao) {
		  this.taskDao = taskDao;
		}
		
		module.exports = TaskList;

3. Continuez à modifier le fichier **tasklist.js** en ajoutant les méthodes utilisées pour afficher les tâches, ajouter les tâches et marquer les tâches comme terminées (**showTasks, addTask** et **completeTasks**) :
		
		TaskList.prototype = {
		    showTasks: function (req, res) {
		        var self = this;
		
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.completed=@completed',
		            parameters: [{
		                name: '@completed',
		                value: false
		            }]
		        };
		
		        self.taskDao.find(querySpec, function (err, items) {
		            if (err) {
		                throw (err);
		            }
		
		            res.render('index', {
		                title: 'My ToDo List ',
		                tasks: items
		            });
		        });
		    },
		
		    addTask: function (req, res) {
		        var self = this;
		        var item = req.body;
		
		        self.taskDao.addItem(item, function (err) {
		            if (err) {
		                throw (err);
		            }
		
		            res.redirect('/');
		        });
		    },
		
		    completeTask: function (req, res) {
		        var self = this;
		        var completedTasks = Object.keys(req.body);
		
		        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		            self.taskDao.updateItem(completedTask, function (err) {
		                if (err) {
		                    callback(err);
		                } else {
		                    callback(null);
		                }
		            });
		        }, function goHome(err) {
		            if (err) {
		                throw err;
		            } else {
		                res.redirect('/');
		            }
		        });
		    }
		};


4. Enregistrez, puis fermez le fichier **task.js**.
 
### Ajout de config.json

1. Dans le répertoire de projet, créez un nouveau fichier nommé **config.js**.
2. Ajoutez le code suivant à **config.json**. Définit les paramètres de configuration et les valeurs nécessaires à notre application.

		var config = {}
		
		config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
		config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
		config.databaseId = "ToDoList";
		config.collectionId = "Items";
		
		module.exports = config;

3. Dans le fichier **config.js**, modifiez les valeurs de HOST et AUTH_KEY avec les valeurs trouvées dans le volet Clés de votre compte DocumentDB dans le [portail Azure en version préliminaire ](http://portal.azure.com) :

4. Enregistrez, puis fermez le fichier **config.js**.
 
### Modification de app.js

1. Dans le répertoire du projet, ouvrez le fichier **app.js**. Ce fichier a été créé précédemment lors de la création de l'application web Express.
2. Ajoutez le code suivant au début de **app.js**
	
		var DocumentDBClient = require('documentdb').DocumentClient;
		var config = require('./config');
		var TaskList = require('./routes/tasklist');
		var TaskDao = require('./models/taskDao');

3. Ce code définit le fichier de configuration à utiliser et procède à la lecture des valeurs de ce fichier dans des variables que nous utiliserons prochainement.
4. Remplacez les deux lignes suivantes dans le fichier **app.js** :

		app.use('/', routes);
		app.use('/users', users); 

      par l'extrait de code suivant :

		var docDbClient = new DocumentDBClient(config.host, {
		    masterKey: config.authKey
		});
		var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
		var taskList = new TaskList(taskDao);
		taskDao.init();
		
		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));


6. Ces lignes définissent une nouvelle instance de notre objet **TaskDao**, avec une nouvelle connexion à DocumentDB (à l'aide des valeurs lues dans **config.js**), initialisent l'objet Task et lient les actions de formulaire à des méthodes dans notre contrôleur **TaskList**. 

7. Enfin, enregistrez et fermez le fichier **app.js**. Nous avons presque terminé.
 
## <a name="_Toc395783181"></a>Étape 5 : Création d'une interface utilisateur

Intéressons-nous à présent à la création de l'interface utilisateur pour permettre à un utilisateur d'interagir réellement avec notre application. L'application Express que nous avons créée utilise **Jade** comme moteur de vue. Pour plus d'informations sur Jade, consultez la page [http://jade-lang.com/](http://jade-lang.com/).

1. Le fichier **layout.jade** du répertoire **views** sert de modèle global aux autres fichiers**.jade**. Dans cette étape, vous allez le modifier pour utiliser [Twitter Bootstrap](https://github.com/twbs/bootstrap), un kit de ressources qui facilite la conception d'un site web bien présenté. 
2. Ouvrez le fichier **layout.jade** trouvé dans le dossier **views** et remplacez le contenu par le code suivant :
	
		doctype html
		html
		  head
		    title= title
		    link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		  body
		    nav.navbar.navbar-inverse.navbar-fixed-top
		      div.navbar-header
		        a.navbar-brand(href='#') My Tasks
		    block content
		    script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
		    script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')



	Ce code demande au moteur **Jade** de générer un rendu HTML pour notre application et crée un **bloc** intitulé **content** dans lequel nous pouvons fournir la mise en page de nos pages de contenu.
	Enregistrez et fermez ce fichier **layout.jade**.

4. Ouvrez maintenant le fichier **index.jade**, la vue qui sera utilisée par l'application, et remplacez le contenu du fichier par le code suivant :

		extends layout
		
		block content
		  h1 #{title}
		  br
		
		  form(action="/completetask", method="post")
		    table.table.table-striped.table-bordered
		      tr
		        td Name
		        td Category
		        td Date
		        td Complete
		      if (typeof tasks === "undefined")
		        tr
		          td
		      else
		        each task in tasks
		          tr
		            td #{task.name}
		            td #{task.category}
		            - var date  = new Date(task.date);
		            - var day   = date.getDate();
		            - var month = date.getMonth() + 1;
		            - var year  = date.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name:
		    input(name="name", type="textbox")
		    label Item Category:
		    input(name="category", type="textbox")
		    br
		    button.btn(type="submit") Add item

	Ce code étend la mise en page et fournit du contenu pour l'espace réservé **content** que nous avons vu plus haut dans le fichier **layout.jade**.
	
	Dans cette mise en page, nous avons créé deux fichiers HTML. 
	Le premier formulaire contient un tableau pour nos données et un bouton qui permet de mettre à jour des éléments en appelant la méthode **/completetask** de notre contrôleur.
	Le second formulaire contient deux champs d'entrée et un bouton qui permet de créer un élément en appelant la méthode **/addtask** de notre contrôleur.
	
	Ceci devrait être suffisant pour que notre application puisse fonctionner.

5. Ouvrez le fichier **style.css** dans le répertoire **public\stylesheets** et remplacez le code par le code suivant :

		body {
		  padding: 50px;
		  font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
		}
		a {
		  color: #00B7FF;
		}
		.well label {
		  display: block;
		}
		.well input {
		  margin-bottom: 5px;
		}
		.btn {
		  margin-top: 5px;
		  border: outset 1px #C8C8C8;
		}

	Enregistrez et fermez ce fichier **style.css**.

## <a name="_Toc395783181"></a>Étape 6 : Exécution locale de l'application

1. Pour tester l'application sur votre ordinateur local, exécutez `npm start` sur un terminal pour démarrer votre application et lancer un navigateur avec une page similaire à celle illustrée ci-dessous :

	![Screenshot of the MyTodo List application in a browser window](./media/documentdb-nodejs-application/image18.png)


2. Dans les champs Item Name (Nom de l'élément) et Item Category (Catégorie de l'élément), entrez
les informations appropriées, puis cliquez sur **Add Item (Ajouter un élément)**.

3. La page doit alors s'actualiser et afficher le nouvel élément dans la liste des tâches
.

	![Screenshot of the application with a new item in the ToDo list](./media/documentdb-nodejs-application/image19.png)

4. Pour terminer une tâche, cochez la case dans la colonne Complete (Terminé),
puis cliquez sur **Update Tasks (Mettre à jour les tâches)**.

## <a name="_Toc395783182"></a>Étape 7 : Déploiement de votre application vers Sites Web Azure

1. Si vous ne l'avez pas encore fait, activez un référentiel git pour votre site web Azure. Vous trouverez des instructions sur la façon de procéder [ici](web-sites-publish-source-control-git.md#step4).

2. Ajoutez votre site web Azure en tant que git distant.

		git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git

3. Procédez au déploiement par un envoi au git distant.

		git push azure master

4. En quelques secondes, Git achève la publication de votre application web
et lance un navigateur dans lequel vous pouvez voir votre réalisation
exécutée dans Azure !

## <a name="_Toc395637775"></a>Étapes suivantes

Félicitations ! Vous venez de créer votre première application web Node.js Express
avec Azure DocumentDB et de la publier dans Sites Web Azure.

Vous pouvez télécharger le code source de l'application de référence complète [ici](https://github.com/Azure/azure-documentdb-node/tree/master/tutorial/todo).

  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Portail de gestion Azure]: http://portal.azure.com

<!--HONumber=49-->