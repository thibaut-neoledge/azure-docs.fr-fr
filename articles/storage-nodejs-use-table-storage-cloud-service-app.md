<properties urlDisplayName="Web App with Storage" pageTitle="Application web avec stockage de tables (Node.js) | Microsoft Azure" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="Ce didacticiel ajoute les services Azure Storage et le module Azure au didacticiel Application web avec Express." metaCanonical="" services="cloud-services,storage" documentationCenter="nodejs" title="Node.js Web Application using Storage" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="mwasson" />






# Application Web Node.js utilisant le stockage

Dans ce didacticiel, vous allez enrichir l'application créée dans le didacticiel [Application web Node.js avec Express] à l'aide des bibliothèques clientes Azure pour Node.js afin qu'elle fonctionne avec les services de gestion de données. Vous allez étendre les fonctionnalités de votre application en vue de créer une application de liste de tâches Web que vous pouvez déployer sur Azure. La liste de tâches permet à un utilisateur d'extraire des tâches, d'en ajouter de nouvelles et de marquer celles qui sont terminées.

Les éléments de tâches sont stockés dans Azure Storage, qui offre le stockage de données non structurées à tolérance de panne et haute disponibilité. Azure Storage inclut plusieurs structures de données dans lesquelles vous pouvez stocker des données et y accéder. Vous pouvez également exploiter les services de stockage à partir des API incluses dans le Kit de développement logiciel (SDK) Azure pour Node.js ou via les API REST. Pour plus d'informations, consultez la page [Stockage et accessibilité des données dans Azure].

Ce didacticiel part du principe que vous avez effectué les didacticiels [Application web
Node.js] et [Node.js avec Express][Node.js Web Application using Express].

Vous apprendrez à effectuer les opérations suivantes :

-   utiliser le moteur de modèles Jade ;
-   utiliser les services de gestion de données Azure.

Voici une capture d'écran de l'application terminée :

![The completed web page in internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Définition des informations d'identification de stockage dans Web.Config

Pour accéder à Azure Storage, vous devez transmettre les informations d'identification de stockage. Pour ce faire, vous utilisez les paramètres d'application web.config. Ceux-ci sont transmis en tant que variables d'environnement à Node, qui sont alors lues par le Kit de développement logiciel (SDK) Azure.

<div class="dev-callout">
<strong>Remarque</strong>
<p>Les informations d'identification de stockage sont uniquement utilisées lors du déploiement de l'application sur Azure. Lorsqu'elle est exécutée dans l'émulateur, l'application utilise l'émulateur de stockage.</p>
</div>

Procédez comme suit pour extraire les informations d'identification de stockage et les ajouter aux paramètres web.config :

1.  Si ce n'est pas déjà le cas, ouvrez Azure PowerShell à partir du menu **Démarrer** en développant **Tous les programmes, Azure**, cliquez avec le bouton droit sur **Azure PowerShell**, puis sélectionnez  **Exécuter en tant qu'administrateur**.

2.  Remplacez les répertoires par le dossier contenant votre application. Par exemple, C:\\node\\tasklist\\WebRole1.

3.  Dans la fenêtre Azure Powershell, entrez la cmdlet suivante pour extraire les informations du compte de stockage :

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

	Cette action permet d'extraire la liste des comptes de stockage et les clés de compte associées à votre service hébergé.

	<div class="dev-callout">
	<strong>Remarque</strong>
	<p>Dans la mesure où le Kit de développement logiciel (SDK) Azure crée un compte de stockage lorsque vous déployez un service, un compte de stockage doit déjà exister puisque vous avez déployé votre application dans les guides précédents.</p>
	</div>

4.  Ouvrez le fichier **ServiceDefinition.csdef** contenant les paramètres d'environnement utilisés lorsque l'application est déployée vers Azure :

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Insérez le bloc suivant sous l'élément  **Environment** en remplaçant {STORAGE ACCOUNT} et {STORAGE ACCESS KEY} par le nom de compte et la clé primaire du compte de stockage que vous souhaitez utiliser pour le déploiement :

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

	![The web.cloud.config file contents](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Enregistrez le fichier et fermez le Bloc-notes.

###Installation de modules supplémentaires

2. Utilisez la commande suivante pour installer les modules [azure], [node-uuid], [nconf] et [async] localement et pour enregistrer une entrée pour eux dans le fichier **package.json** :

		PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

	The output of this command should appear similar to the following:

		node-uuid@1.4.1 node_modules\node-uuid

		nconf@0.6.9 node_modules\nconf
		├── ini@1.1.0
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##Utilisation du service de Table dans une application Node

Dans cette section, vous allez étendre l'application de base créée par la commande **express** en ajoutant un fichier **task.js** qui contient le modèle de vos tâches. Vous allez également modifier le fichier **app.js** existant et créer un fichier **tasklist.js** qui utilise le modèle.

### Création du modèle

1. Dans le répertoire **WebRole1**, créez un répertoire appelé **models**.

2. Dans le répertoire **models**, créez un fichier appelé **task.js**. Ce fichier contiendra le modèle des tâches créées par votre application.

3. Au début du fichier **task.js**, ajoutez le code suivant pour référencer les bibliothèques nécessaires :

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. Ensuite, vous allez ajouter du code pour définir et exporter l'objet Task. Cet objet est responsable de la connexion vers la table.

  		module.exports = Task;

		function Task(storageClient, tableName, partitionKey) {
		  this.storageClient = storageClient;
		  this.tableName = tableName;
		  this.partitionKey = partitionKey;
		  this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
		    if(error) {
		      throw error;
		    }
		  });
		};

5. Ensuite, ajoutez le code suivant pour définir des méthodes supplémentaires sur l'objet Task permettant d'interagir avec les données stockées dans la table :

		Task.prototype = {
		  find: function(query, callback) {
		    self = this;
		    self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
		      if(error) {
		        callback(error);
		      } else {
		        callback(null, result.entries);
		      }
		    });
		  },

		  addItem: function(item, callback) {
		    self = this;
			// use entityGenerator to set types
			// NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
		    var itemDescriptor = {
		      PartitionKey: entityGen.String(self.partitionKey),
		      RowKey: entityGen.String(uuid()),
		      name: entityGen.String(item.name),
		      category: entityGen.String(item.category),
		      completed: entityGen.Boolean(false)
		    };

		    self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
		      if(error){  
		        callback(error);
		      }
		      callback(null);
		    });
		  },

		  updateItem: function(rKey, callback) {
		    self = this;
		    self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
		      if(error) {
		        callback(error);
		      }
		      entity.completed._ = true;
		      self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
		        if(error) {
		          callback(error);
		        }
		        callback(null);
		      });
		    });
		  }
		}

6. Enregistrez, puis fermez le fichier **task.js**.

###Création du contrôleur

1. Dans le répertoire **WebRole1/routes**, créez un fichier **tasklist.js** et ouvrez-le dans un éditeur de texte.

2. Ajoutez le code suivant dans  **tasklist.js**. Cela charge les modules azure et async, qui sont utilisés par **tasklist.js**. Cela définit également la fonction **TaskList**, à qui est transmise une instance de l'objet **Task** défini précédemment :

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

		function TaskList(task) {
		  this.task = task;
		}

2. Continuez à ajouter au fichier **tasklist.js** en ajoutant les méthodes utilisées par **showTasks**, **addTask** et **completeTasks** :

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = azure.TableQuery()
		      .where('completed eq ?', false);
		    self.task.find(query, function itemsFound(error, items) {
		      res.render('index',{title: 'My ToDo List ', tasks: items});
		    });
		  },

		  addTask: function(req,res) {
		    var self = this      
		    var item = req.body.item;
		    self.task.addItem(item, function itemAdded(error) {
		      if(error) {
		        throw error;
		      }
		      res.redirect('/');
		    });
		  },

		  completeTask: function(req,res) {
		    var self = this;
		    var completedTasks = Object.keys(req.body);
		    async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		      self.task.updateItem(completedTask, function itemsUpdated(error) {
		        if(error){
		          callback(error);
		        } else {
		          callback(null);
		        }
		      });
		    }, function goHome(error){
		      if(error) {
		        throw error;
		      } else {
		       res.redirect('/');
		      }
		    });
		  }
		}

3. Enregistrez le fichier  **tasklist.js**.

### Modification de app.js

1. Dans le répertoire **WebRole1**, ouvrez le fichier **app.js** dans un éditeur de texte. 

2. Au début du fichier, ajoutez ce qui suit pour charger le module azure, et définissez le nom de la table et la clé de partition :

		var azure = require('azure-storage');
		var tableName = 'tasks';
		var partitionKey = 'hometasks';

3. Dans le fichier app.js, faites défiler le contenu jusqu'à la ligne suivante :

		app.use('/', routes);
		app.use('/users', users);

	Remplacez les lignes ci-dessus par le code affiché ci-dessous. Cela initialise une instance de <strong>Task</strong> avec une connexion à votre compte de stockage. Cela est passé à la  <strong>TaskList</strong>, qui l'utilisera pour communiquer avec le service de Table :

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));
	
4. Enregistrez le fichier **app.js**.

###Modification de la vue d'index

1. Passez dans le répertoire **views** et ouvrez le fichier **index.jade** dans un éditeur de texte.

2. Remplacez le contenu du fichier **index.jade** par le code qui suit. Ce code définit la vue pour l'affichage des tâches existantes, ainsi qu'un formulaire pour l'ajout de nouvelles tâches et pour marquer les tâches existantes comme terminées.

		extends layout

		block content
		  h1= title
		  br
		
		  form(action="/completetask", method="post")
		    table.table.table-striped.table-bordered
		      tr
		        td Name
		        td Category
		        td Date
		        td Complete
		      if tasks != []
		        tr
		          td 
		      else
		        each task in tasks
		          tr
		            td #{task.name._}
		            td #{task.category._}
		            - var day   = task.Timestamp._.getDate();
		            - var month = task.Timestamp._.getMonth() + 1;
		            - var year  = task.Timestamp._.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name: 
		    input(name="item[name]", type="textbox")
		    label Item Category: 
		    input(name="item[category]", type="textbox")
		    br
		    button.btn(type="submit") Add item

3. Fermez et enregistrez le fichier **index.jade**.

###Modification de la disposition générale

Le fichier **layout.jade** du répertoire  **views** sert de modèle global aux autres fichiers**.jade**. Lors de cette étape, vous allez le modifier pour utiliser [Twitter Bootstrap](https://github.com/twbs/bootstrap), qui est une boîte à outils qui simplifie la conception d'un site web visuellement attrayant.

1. Téléchargez les fichiers du [Twitter Bootstrap], puis procédez à l'extraction(http://getbootstrap.com/). Copiez le fichier **bootstrap.min.css** du dossier  **bootstrap\\dist\\css** vers le répertoire **public\\stylesheets** de votre application de liste de tâches.

2. Dans le dossier **views**, ouvrez  **layout.jade** dans votre éditeur de texte et remplacez son contenu par le code suivant :

		doctype html
		html
		  head
		    title= title
		    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		  body.app
		    nav.navbar.navbar-default
		      div.navbar-header
		        a.navbar-brand(href='/') My Tasks
		    block content

3. Enregistrez le fichier **layout.jade**.

### Exécution de l'application dans l'émulateur

Utilisez la commande suivante pour lancer l'application dans l'émulateur.

	PS C:\node\tasklist\WebRole1> start-azureemulator -launch

Le navigateur s'ouvre et affiche la page suivante :

![A web paged titled My Task List with a table containing tasks and fields to add a new task.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

Utilisez le formulaire pour ajouter des éléments ou supprimez des éléments en les marquant comme terminés.

## Publication de l'application dans Azure


Dans la fenêtre Windows PowerShell, appelez la cmdlet suivante pour redéployer votre service hébergé vers Azure.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Remplacez **myuniquename** par un nom unique pour cette application. Remplacez **datacentername** par le nom d'un centre de données Azure, tel que **Ouest des États-Unis**.

Une fois le déploiement terminé, une réponse similaire à celle présentée ci-dessous doit s'afficher :

	PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
	WARNING: Publishing tasklist to Windows Azure. This may take several minutes...
	WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
	WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
	WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
	WARNING: 2:19:01 PM - Connecting...
	WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
	WARNING: 2:19:40 PM - Upgrading...
	WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
	WARNING: 2:22:48 PM - Initializing...
	WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
	WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Comme auparavant, dans la mesure où vous avez spécifié l'option **-launch**, le navigateur s'ouvre et affiche votre application qui s'exécute dans Azure une fois la publication effectuée.

![Fenêtre de navigateur affichant la page My Task List. L'URL indique que la page est désormais hébergée sur Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Arrêt et suppression de votre application

Après avoir déployé votre application, vous pouvez la désactiver afin d'éviter des coûts ou de générer et de déployer d'autres applications au cours de la période d'évaluation gratuite.

Azure facture les instances de rôle Web par heure de serveur consommée. Une fois votre application déployée, elle consomme du temps de serveur, même si les instances ne sont pas exécutées et sont arrêtées.

La procédure suivante présente l'arrêt et la suppression de l'application.

1.  Dans la fenêtre Windows PowerShell, arrêtez le déploiement du service créé dans la section précédente à l'aide de la cmdlet suivante :

        PS C:\node\tasklist\WebRole1> Stop-AzureService

	L'arrêt du service peut prendre plusieurs minutes. Une fois le service arrêté, vous recevez un message confirmant l'arrêt du service.

3.  Pour supprimer le service, utilisez la cmdlet suivante :

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

	À l'invite, tapez **Y** pour supprimer le service.

	La suppression du service peut prendre plusieurs minutes. Une fois le service supprimé, vous recevez un message confirmant la suppression du service.

  [Application Web Node.js utilisant Express]: http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/web-app-with-express/
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
  [Application Web Node.js]: http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/getting-started/
 

<!--HONumber=35.1-->
