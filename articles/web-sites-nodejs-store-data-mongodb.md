<properties 
	pageTitle="Créer une application web Node.js sur Azure avec MongoDB dans une machine virtuelle" 
	description="Découvrez comment utiliser MongoDB pour stocker des données dans une application Node.js hébergée sur Azure."
	tags="azure-portal" 
	services="app-service\web, virtual-machines" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="04/23/2015" 
	ms.author="mwasson"/>


# Créer une application web Node.js sur Azure avec MongoDB dans une machine virtuelle

Ce didacticiel vous présente comment utiliser [MongoDB] sur une machine virtuelle Azure pour stocker des données et accéder à ces données depuis une application [Node] hébergée dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. [MongoDB] est une base de données NoSQL libre qui offre des performances élevées.

Vous apprendrez à effectuer les opérations suivantes :

* Configuration d'une machine virtuelle avec Ubuntu et MongoDB depuis le VM Depot.
* Accès à MongoDB depuis une application Node
* Utilisation des outils interplateforme pour Azure pour créer une application web dans Azure App Service

Dans ce didacticiel, vous allez concevoir une application Web simple pour la gestion des tâches qui vous permet de créer et de récupérer des tâches et de les marquer comme terminées. Les tâches sont stockées dans MongoDB.

> [AZURE.NOTE]Ce didacticiel utilise une instance MongoDB installée sur une machine virtuelle. Si vous préférez utiliser une instance MongoDB hébergée fournie par MongoLabs, consultez la page [Créer une application web Node.js sur Azure avec MongoDB à l’aide du module complémentaire MongoLab](store-mongolab-web-sites-nodejs-store-data-mongodb).
 
Les fichiers de projets de ce didacticiel sont stockés dans un répertoire appelé **tasklist** et l'application terminée ressemble à ce qui suit :

![Une page Web avec une liste de tâches vide][node-mongo-finished]

> [AZURE.NOTE]Plusieurs des étapes ci-dessous vous demandent d'utiliser la ligne de commande. Pour ces étapes, utilisez la ligne de commande de votre système d'exploitation, par exemple **Windows PowerShell** (Windows) ou **Bash** (Shell Unix). Sur les systèmes OS X, vous pouvez accéder à la ligne de commande via l'application Terminal.

##Conditions préalables

Les étapes de ce didacticiel font référence à Node.js. Vous devez donc disposer d'une version récente de [Node.js][node] dans votre environnement de développement.

De plus, [Git] doit être accessible depuis la ligne de commande de votre environnement de développement, car il est utilisé pour le déploiement de l'application sur le site web Azure.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

##Créer une machine virtuelle

<!--
After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

Bien qu’il soit possible de créer une machine virtuelle puis d’y installer MongoDB selon les [guides d’installation MongoDB][installguides], une machine virtuelle préinstallée avec MongoDB est disponible dans Azure Marketplace. Les étapes suivantes montrent comment utiliser un de ces nombreux modèles de machine virtuelle.

> [AZURE.NOTE]L'image de communauté utilisée par ce didacticiel stocke les données MongoDB sur le disque du système d'exploitation. Bien que cela soit suffisant pour les besoins du didacticiel, le stockage de données MongoDB sur un disque de données offrira de meilleures performances. Pour obtenir les étapes nécessaires à la création d'une nouvelle machine virtuelle, y compris un disque de données, et au stockage de données MongoDB sur le disque de données, consultez [Installer MongoDB sur Linux sur Azure][mongodbonazure].

1. Connectez-vous au [portail Azure][azureportal].

3. Cliquez sur **Nouveau** > **Données et stockage** > **Marketplace**.

	![capture d'écran de la sélection de VM Depot][selectdepo]

2. Dans la zone de recherche en haut, tapez « mongodb », puis sélectionnez **MongoDB v2.2.3 on Hardened Ubuntu 12.04 LTS**. Cliquez sur **Créer** pour continuer.

	![capture d'écran de MongoDB v2.2.3 sur l'image Hardened Ubuntu][selectedimage]

	Cliquez sur la flèche située en bas pour passer à l'écran suivant.

7. Renseignez les champs **Nom d’hôte**, **Nom d’utilisateur**, **Mot de passe** et **Groupe de ressources** pour la machine virtuelle. Puis cliquez sur **Configuration facultative**.

	![capture d'écran de la configuration de la machine virtuelle][vmconfig]

8. Création de points de terminaison supplémentaires pour la machine virtuelle. Comme nous allons accéder à MongoDB sur cette machine virtuelle, ajoutez un nouveau point de terminaison à l'aide des informations suivantes :

	* Nom : MongoDB
	* Protocole : TCP
	* Port public : 27017
	* Port privé : 27017

	Pour exposer le portail Web de MongoDB, ajoutez un autre point de terminaison à l'aide des informations suivantes :

	* Nom : MongoDBWeb
	* Protocole : TCP
	* Port public : 28017
	* Port privé : 28017
	
	![capture d'écran de la configuration du point de terminaison][vmendpoint]

9. Cliquez sur **OK** deux fois, puis sur **Créer** pour créer la machine virtuelle.

	La machine virtuelle créée s’affiche dans votre tableau d’accueil. Cliquez dessus pour ouvrir son panneau. Vous devez pouvoir ouvrir un navigateur web et accéder à **http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/** pour vérifier que MongoDB est en cours d’exécution. En bas de la page, un journal contient des informations sur le service, qui ressemblent à celles-ci :

		Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

	Si le journal contient des erreurs, consultez la [documentation MongoDB][mongodocs] pour obtenir des instructions sur la résolution des problèmes.


##Installation des modules et création de la structure

Dans cette section, vous allez créer une nouvelle application Node dans votre environnement de développement et utiliser npm pour ajouter des packages de module. Pour l'application de liste de tâches, vous allez utiliser les modules [Express] et [Mongoose]. Le module Express contient une infrastructure de modèle de contrôleur d'affichage pour Node, alors que Mongoose est un pilote pour la communication avec MongoDB.

###Installation express et création de la structure

1. Dans la ligne de commande, accédez au répertoire **tasklist**. Si le répertoire **tasklist** n'existe pas, créez-le.

	> [AZURE.NOTE]Ce didacticiel fait référence au dossier **tasklist**. Le chemin complet de ce dossier n'est pas mentionné, en raison des différences entre les systèmes d'exploitation. Créez ce dossier dans un emplacement facilement accessible dans votre système de fichiers local, par exemple **\~/node/tasklist** ou **c:\\node\\tasklist**

2. Entrez la commande suivante pour installer la commande express.

	npm install express-generator -g
 
	> [AZURE.NOTE]Lorsque vous utilisez le paramètre ’-g’ sur certains systèmes d’exploitation, vous pouvez recevoir une erreur de type ___Error: EPERM, chmod ’/usr/local/bin/express’___ et une demande d’exécution du compte en tant qu’administrateur. Dans ce cas, utilisez la commande `sudo` pour exécuter npm avec des privilèges plus élevés.

    Le résultat de cette commande doit ressembler à ceci :

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)                                                                         
 
	> [AZURE.NOTE]Avec le paramètre '-g', le module express est installé dans sa globalité. Ceci nous permet d’accéder à la commande ___express___ pour générer la structure de l’application web sans avoir à saisir d’informations supplémentaires dans le chemin d’accès.

4. Pour créer la structure qui sera utilisée pour cette application, utilisez la commande **express** :

    express

	Le résultat de cette commande doit ressembler à ceci :

		   create : .
		   create : ./package.json
		   create : ./app.js
		   create : ./public
		   create : ./public/images
		   create : ./routes
		   create : ./routes/index.js
		   create : ./routes/users.js
		   create : ./public/stylesheets
		   create : ./public/stylesheets/style.css
		   create : ./views
		   create : ./views/index.jade
		   create : ./views/layout.jade
		   create : ./views/error.jade
		   create : ./public/javascripts
		   create : ./bin
		   create : ./bin/www
		
		   install dependencies:
		     $ cd . && npm install
		
		   run the app:
		     $ DEBUG=my-application ./bin/www

	Une fois la commande terminée, vous devez disposer de plusieurs nouveaux répertoires et fichiers dans le répertoire **tasklist**.

3. Copiez le fichier **tasklist/bin/www** dans un fichier nommé **server.js** dans le dossier **tasklist**. App Service Web Apps s’attend à ce que le point d’entrée d’une application Node.js soit **server.js** ou **app.js**. Comme **app.js** existe déjà mais qu'il ne s'agit pas d'un point d'entrée, nous devons utiliser **server.js**.

4. Modifiez le fichier **server.js** pour supprimer l'un des caractères « . » de la ligne suivante.

		var app = require('../app');

	La ligne modifiée devrait ressembler à ce qui suit.

		var app = require('./app');

	Cela est nécessaire car **server.js** (anciennement **bin/www**,) se trouve maintenant dans le même dossier que le fichier **app.js** nécessaire.

###Installation de modules supplémentaires

Le fichier **package.json** est un des fichiers créés par la commande **express**. Il contient une liste de modules supplémentaires qui sont nécessaires pour les applications Express. Ensuite, quand vous déployez cette application dans App Service Web Apps, ce fichier permet de déterminer les modules à installer sur Azure pour prendre en charge votre application.
	
1. Dans le dossier **tasklist**, utilisez la commande suivante pour installer les modules décrits dans le fichier **package.json** :

        npm install

    Le résultat de cette commande doit ressembler à ceci :

		debug@0.7.4 node_modules\debug
		
		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0
		
		morgan@1.0.0 node_modules\morgan
		└── bytes@0.2.1
		
		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.4 (bytes@0.3.0)
		└── type-is@1.1.0 (mime@1.2.11)
		
		express@4.0.0 node_modules\express
		├── methods@0.1.0
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── fresh@0.2.2
		├── range-parser@1.0.0
		├── buffer-crc32@0.2.1
		├── qs@0.6.6
		├── cookie@0.1.0
		├── path-to-regexp@0.1.2
		├── send@0.2.0 (mime@1.2.11)
		├── type-is@1.0.0 (mime@1.2.11)
		├── accepts@1.0.0 (negotiator@0.3.0, mime@1.2.11)
		└── serve-static@1.0.1 (send@0.1.4)
		
		jade@1.3.1 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.0 (uglify-js@2.4.13)
		├── with@3.0.0 (uglify-js@2.4.13)
		└── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)                                                                

	Ceci installe tous les modules utilisés par défaut par les applications Express.

2. Ensuite, entrez la commande suivante pour installer le module Mongoose en local et pour enregistrer une entrée lui correspondant dans le fichier **package.json** :

		npm install mongoose --save

	Le résultat de cette commande doit ressembler à ceci :

		mongoose@3.8.8 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.5
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.5.3
		└── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)         

    > [AZURE.NOTE]Vous pouvez ignorer tout message concernant l'installation de l'analyseur C++ bson.

##Utilisation de MongoDB dans une application Node

Dans cette section, vous allez étendre l'application de base créée par la commande **express** en ajoutant un fichier **task.js** qui contient le modèle de vos tâches. Vous allez également modifier le fichier **app.js** existant et créer un fichier de contrôleur **tasklist.js** pour utiliser le modèle.

### Création du modèle

1. Dans le répertoire **tasklist**, créez un répertoire appelé **models**.

2. Dans le répertoire **models**, créez un fichier appelé **task.js**. Ce fichier contiendra le modèle des tâches créées par votre application.

3. Au début du fichier **task.js**, ajoutez le code suivant pour référencer les bibliothèques nécessaires :

        var mongoose = require('mongoose'),
	        Schema = mongoose.Schema;

4. Ensuite, vous allez ajouter du code pour définir et exporter le modèle. Ce modèle sera utilisé pour assurer les interactions avec la base de données MongoDB.

        var TaskSchema = new Schema({
	        itemName      : String,
	        itemCategory  : String,
	        itemCompleted : { type: Boolean, default: false },
	        itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Enregistrez, puis fermez le fichier **task.js**.

###Création du contrôleur

1. Dans le répertoire **tasklist/routes**, créez un nouveau fichier **tasklist.js** et ouvrez-le dans un éditeur de texte.

2. Ajoutez le code suivant à **tasklist.js**. Ce code charge le module mongoose et le modèle de tâches défini dans **task.js**. La fonction TaskList est utilisée pour créer la connexion au serveur MongoDB basée sur la valeur **connection** :

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

2. Continuez à modifier le fichier **tasklist.js** en ajoutant les méthodes utilisées pour afficher les tâches (**showTasks**), ajouter les tâches (**addTask**) et marquer les tâches comme terminées (**completeTasks**) :

		TaskList.prototype = {
  		  showTasks: function(req, res) {
      	    task.find({itemCompleted: false}, function foundTasks(err, items) {
      		  res.render('index',{title: 'My ToDo List ', tasks: items})
    		});
  		  },

  		  addTask: function(req,res) {
    		var item = req.body.item;
    		newTask = new task();
    		newTask.itemName = item.name;
    		newTask.itemCategory = item.category;
    		newTask.save(function savedTask(err){
      		  if(err) {
      		    throw err;
      		  }
    	    });
    	  	res.redirect('/');
  		  },
  

  		  completeTask: function(req,res) {
    		var completedTasks = req.body;
    		for(taskId in completedTasks) {
      		  if(completedTasks[taskId]=='true') {
        		var conditions = { _id: taskId };
        		var updates = { itemCompleted: completedTasks[taskId] };
        		task.update(conditions, updates, function updatedTask(err) {
          		  if(err) {
          		    throw err;
          		  }
        		});
      		  }
    		}
    		res.redirect('/');
  		  }
		}

3. Enregistrez le fichier **tasklist.js**.

### Modification de app.js

1. Dans le répertoire **tasklist**, ouvrez le fichier **app.js** dans un éditeur de texte. Ce fichier a été créé ultérieurement à l'aide de la commande **express**.

2. Ajoutez le code qui suit au début du fichier **app.js**. Il initialise **TaskList** avec la chaîne de connexion du serveur MongoDB :

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	Notez la deuxième ligne. Vous accédez à une variable d'environnement que vous configurerez ultérieurement qui contient les informations de connexion de votre instance Mongo. Si vous disposez d'une instance Mongo locale pour le développement, vous pouvez définir cette valeur de façon temporaire sur « localhost » au lieu de process.env.MONGODB_URI.

3. Recherchez les lignes suivantes :

		app.use('/', routes);
		app.use('/users', users);

	Remplacez les deux lignes ci-dessus par ce qui suit :

    	app.get('/', taskList.showTasks.bind(taskList));
    	app.post('/addtask', taskList.addTask.bind(taskList));
    	app.post('/completetask', taskList.completeTask.bind(taskList));

	Ce code ajoute les fonctions définies dans **tasklist.js** en tant que routes.

4. Enregistrez le fichier **app.js**.

###Modification de la vue d'index

1. Passez dans le répertoire **views** et ouvrez le fichier **index.jade** dans un éditeur de texte.

2. Remplacez le contenu du fichier **index.jade** par le code qui suit. Ce code définit la vue pour l'affichage des tâches existantes, ainsi qu'un formulaire pour l'ajout de nouvelles tâches et pour marquer les tâches existantes comme terminées.

		h1= title
		form(action="/completetask", method="post")
		  table(border="1")
		    tr
		      td Name
		      td Category
		      td Date
		      td Complete
		    each task in tasks
		      tr
		        td #{task.itemName}
		        td #{task.itemCategory}
		        - var day   = task.itemDate.getDate();
		        - var month = task.itemDate.getMonth() + 1;
		        - var year  = task.itemDate.getFullYear();
		        td #{month + "/" + day + "/" + year}
		        td
		          input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
		  input(type="submit", value="Update tasks")
		hr
		form(action="/addtask", method="post")
		  table(border="1") 
		    tr
		      td Item Name: 
		      td 
		        input(name="item[name]", type="textbox")
		    tr
		      td Item Category: 
		      td 
		        input(name="item[category]", type="textbox")
		  input(type="submit", value="Add item")

3. Fermez et enregistrez le fichier **index.jade**.

<!-- ##Run your application locally

To test the application on your local machine, perform the following steps:

1. From the command-line, change directories to the **tasklist** directory.

2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.

	On a Windows system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	On an OS X or Linux-based system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.

2. Use the following command to launch the application locally:

        node app.js

3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. The page should update to display the item in the ToDo List table.

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.

7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

##Déploiement de votre application dans Azure

Les étapes de cette section utilisent les outils en ligne de commande Azure pour créer une application web dans Azure App Service et déployer votre application à l’aide de Git. Pour effectuer ces étapes, vous devez disposer d'un abonnement Azure.

> [AZURE.NOTE]Ces étapes peuvent également être effectuées sur le portail Azure. Pour déployer une application Node.js à l’aide du portail Azure, consultez la page [Créer et déployer une application web Node.js dans Azure App Service](web-sites-nodejs-develop-deploy-mac.md).

> [AZURE.NOTE]S’il s’agit de votre première application web App Service, vous devez utiliser le portail Azure pour la déployer.

###Installation de l'interface de ligne de commande interplateforme Azure

L'interface de ligne de commande interplateforme Azure (xplat-cli) vous permet d'effectuer des opérations de gestion sur les services Azure. Si vous n'avez pas déjà installé et configuré xplat-cli dans votre environnement de développement, consultez les instructions dans [Installation de l'interface de ligne de commande interplateforme Azure][xplatcli].

###Créer une application web App Service

1. Dans la ligne de commande, accédez au répertoire **tasklist**.

2. Utilisez la commande suivante pour créer une application web App Service. Remplacez « myuniquewebappname » par le nom unique de votre application web. Cette valeur est utilisée comme une partie de l’URL de l’application web obtenue.

		azure site create myuniqueappname --git
		
	Vous devez indiquer le centre de données qui héberge votre application web. Sélectionnez un centre de données géographiquement proche de votre emplacement.
	
	Le paramètre `--git` crée un référentiel Git localement dans le dossier **tasklist** s’il n’existe pas. Il crée également une télécommande [Git remote] appelée « azure », qui sera utilisée pour publier l'application sur Azure. Il crée un fichier [iisnode.yml], qui contient les paramètres utilisés par Azure pour héberger les applications Node. Enfin, il crée également un fichier .gitignore afin d'exclure le dossier node-modules de la publication sur .git.
	
	> [AZURE.NOTE]Si cette commande est exécutée depuis un répertoire qui contient déjà un référentiel Git, le répertoire n'est pas réinitialisé.
	
	> [AZURE.NOTE]Si le paramètre --git est omis, mais que le répertoire contient un référentiel Git, la commande distante « azure » est quand même créée.
	
	Une fois cette commande terminée, le résultat doit ressembler à ce qui suit. Notez que la ligne commençant par **Created website at** contient l’URL de l’application web App Service.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

	> [AZURE.NOTE> S’il s’agit de la première application web App Service dans votre abonnement, vous devez utiliser le portail pour la créer. Pour plus d’informations, consultez la page [Créer et déployer une application web Node.js dans Azure App Service](web-sites-nodejs-develop-deploy-mac.md).

###Définir la variable d'environnement MONGODB_URI

L'application s'attend à ce que la chaîne de connexion de l'instance MongoDB soit disponible dans la variable d'environnement MONGODB_URI. Pour définir cette valeur pour l’application web, utilisez la commande suivante :

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Ceci crée un paramètre d’application pour l’application web, qui sera intégré dans la variable d’environnement MONGODB_URI lue par l’application web. Remplacez la valeur de « mymongodb.cloudapp.net » par le nom de la machine virtuelle sur laquelle MongoDB a été installé.

###Publication de l'application

1. Dans la fenêtre Terminal, passez dans le répertoire **tasklist** si vous ne vous y trouvez pas déjà.

2. Utilisez les commandes suivantes pour ajouter, puis valider les fichiers dans le référentiel :

		git add .
		git commit -m "adding files"

3. Quand vous transférez les dernières modifications du référentiel Git à l’application web App Service, vous devez spécifier que la branche cible est **master**, car elle est utilisée pour le contenu de l’application web.

		git push azure master
	
	Vous devez voir des informations similaires à ce qui suit. À mesure que le déploiement s'effectue, Azure télécharge tous les modules npm.

		Counting objects: 17, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (13/13), done.
		Writing objects: 100% (17/17), 3.21 KiB, done.
		Total 17 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id 'ef276f3042'.
		remote: Preparing files for deployment.
		remote: Running NPM.
		...
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
 		 * [new branch]      master -> master
 
4. Une fois l’opération push terminée, accédez à l’application web à l’aide de la commande `azure site browse` pour voir votre application.

##Étapes suivantes

Bien que les étapes de cet article décrivent l'utilisation de MongoDB pour stocker des informations, vous pouvez aussi utiliser Azure Table Service. Pour plus d’informations, consultez la page [Application Web Node.js avec le service de Table Azure].

Pour savoir comment utiliser une instance hébergée de MongoDB fournie par MongoLabs, consultez la page [Création d’une application Node.js sur Azure avec MongoDB à l’aide du module complémentaire MongoLabs](store-mongolab-web-sites-nodejs-store-data-mongodb.md).

Pour plus d'informations sur la sécurisation de MongoDB, consultez la page [Sécurité de MongoDB][mongosecurity].

##Ressources supplémentaires

[Outil en ligne de commande Azure pour Mac et Linux][Build and deploy a Node.js web app in Azure App Service] [Déploiement continu à l’aide de GIT dans Azure App Service]

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure command-line tool for Mac and Linux]: virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Build and deploy a Node.js web app in Azure App Service]: web-sites-nodejs-develop-deploy-mac.md
[Déploiement continu à l’aide de GIT dans Azure App Service]: web-sites-publish-source-control.md
[Application Web Node.js avec le service de Table Azure]: storage-nodejs-use-table-storage-web-site.md
[node-mongo-finished]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_add_item.png
[node-mongo-list-items]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://portal.azure.com
[mongodocs]: http://docs.mongodb.org/manual/
[xplatcli]: xplat-cli.md

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
<!--HONumber=54-->